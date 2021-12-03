---
title: Anmelden mit der Gewährung der Kennwortanmeldeinformationen des Ressourcenbesitzers | Azure
titleSuffix: Microsoft identity platform
description: Support für nicht browserbasierte Authentifizierungsflows mit der Gewährung der Kennwortanmeldeinformationen des Ressourcenbesitzers (Resource Owner Password Credential, ROPC)
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bbfb5923228b3f581981ad23d933e047cee7212a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435977"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity Platform und OAuth 2.0-Kennwortanmeldeinformationen des Ressourcenbesitzers

Die Microsoft Identity Platform unterstützt die [Gewährung für OAuth 2.0-Kennwortanmeldeinformationen des Ressourcenbesitzers (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3). So kann eine Anwendung Benutzer anmelden, indem sie ihr Kennwort direkt verarbeitet.  In diesem Artikel wird beschrieben, wie Sie direkt mit dem Protokoll in Ihrer Anwendung programmieren.  Es wird stattdessen empfohlen, ggf. die unterstützten Microsoft Authentication Libraries (MSAL) zu verwenden, um [Token zu erhalten und gesicherte Web-APIs aufzurufen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Sehen Sie sich auch die [Beispiel-Apps an, die MSAL verwenden](sample-v2-code.md).

> [!WARNING]
> Microsoft empfiehlt, dass Sie _nicht_ den ROPC-Flow verwenden. In den meisten Szenarien sind sicherere Alternativen verfügbar und werden daher empfohlen. Dieser Flow erfordert ein sehr hohes Maß an Vertrauen in die Anwendung und birgt Risiken, die in anderen Flows nicht vorhanden sind. Verwenden Sie diesen Flow nur, wenn kein anderer Flow verfügbar ist, der mehr Sicherheit bietet.


> [!IMPORTANT]
>
> * Die Microsoft Identity Platform unterstützt nur ROPC bei Azure AD-Mandanten – nicht für persönliche Konten. Das bedeutet, Sie müssen einen mandantenspezifischen Endpunkt (`https://login.microsoftonline.com/{TenantId_or_Name}`) oder den Endpunkt `organizations` verwenden.
> * Persönliche Konten, die zu einem Mandanten von Azure AD eingeladen werden, können ROPC nicht verwenden.
> * Konten ohne Kennwörter können sich nicht mit ROPC anmelden. Dies bedeutet, dass Features wie SMS-Anmeldung, FIDO und die Authenticator-App bei diesem Flow nicht funktionieren. Verwenden Sie einen anderen Flow als ROPC, wenn diese Features für Ihre App oder Benutzer erforderlich sind.
> * Wenn Benutzer die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) verwenden müssen, um sich bei der Anwendung anzumelden, werden Sie stattdessen blockiert.
> * ROPC wird in Szenarien mit [Hybrididentitätsverbund](../hybrid/whatis-fed.md) (also beispielsweise bei Verwendung von Azure AD und AD FS für die Authentifizierung lokaler Konten) nicht unterstützt. Wenn Benutzer vollständig auf eine Seite eines lokalen Identitätsanbieters weitergeleitet werden, kann Azure AD den Benutzernamen und das Kennwort nicht anhand dieses Identitätsanbieters testen. Die [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) wird mit ROPC allerdings unterstützt.
> * Eine Ausnahme für ein Szenario mit Hybrididentitätsverbund wäre: Durch Festlegen von „AllowCloudPasswordValidation“ auf „TRUE“ für die Richtlinie zur Startbereichsermittlung kann der ROPC-Flow für Verbundbenutzer verwendet werden, wenn das lokale Kennwort mit der Cloud synchronisiert wird. Weitere Informationen finden Sie unter [Aktivieren der direkten ROPC-Authentifizierung von Verbundbenutzern für Legacyanwendungen](../manage-apps/home-realm-discovery-policy.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications).

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>Protokolldiagramm

Dieses Diagramm zeigt den ROPC-Flow:

![Diagramm mit ROPC-Flow (Resource Owner Password Credential)](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Autorisierungsanforderung)

Der ROPC-Flow ist eine einzelne Anforderung: Er sendet die Client-ID und die Anmeldeinformationen des Benutzers an den ausstellenden Verteilungspunkt und empfängt im Gegenzug ein Token. Zuvor muss der Client die E-Mail-Adresse des Benutzers und das Kennwort anfordern. Unmittelbar nach einer erfolgreichen Anforderung muss der Client die Anmeldeinformationen des Benutzers sicher aus dem Arbeitsspeicher freigeben. Er darf sie niemals speichern.

```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, bei dem Sie den Benutzer anmelden möchten. Kann als GUID oder als Anzeigename bereitgestellt werden. Dieser Parameter kann nicht auf `common` oder `consumers`, sondern nur auf `organizations` festgelegt werden. |
| `client_id` | Erforderlich | Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `grant_type` | Erforderlich | Muss auf `password` festgelegt sein. |
| `username` | Erforderlich | Die E-Mail-Adresse des Benutzers. |
| `password` | Erforderlich | Das Kennwort des Benutzers. |
| `scope` | Empfohlen | Eine durch Leerzeichen getrennte Liste von [Bereichen](v2-permissions-and-consent.md) oder Berechtigungen, die die App benötigt. In einem interaktiven Flow müssen der Administrator oder der Benutzer diesen Bereichen vorab zustimmen. |
| `client_secret`| Manchmal erforderlich | Wenn es sich bei Ihrer App um einen öffentlichen Client handelt, kann `client_secret` oder `client_assertion` nicht eingeschlossen werden.  Wenn es sich bei der App um einen vertraulichen Client handelt, muss es eingeschlossen werden.|
| `client_assertion` | Manchmal erforderlich | Eine andere Form von `client_secret`, die unter Verwendung eines Zertifikats generiert wird.  Ausführlichere Informationen finden Sie unter [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md). |

> [!WARNING]
> Im Rahmen der Nichtempfehlung dieses Flows zur Nutzung unterstützen die offiziellen SDKs diesen Flow nicht bei vertraulichen Clients, die ein Geheimnis oder eine Assertion verwenden. Möglicherweise stellen Sie fest, dass das SDK, das Sie verwenden möchten, Ihnen nicht erlaubt, ein Geheimnis hinzuzufügen, während Sie ROPC verwenden. 

### <a name="successful-authentication-response"></a>Erfolgreiche Authentifizierungsantwort

Das folgende Beispiel stellt eine erfolgreiche Tokenantwort dar:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | BESCHREIBUNG |
| --------- | ------ | ----------- |
| `token_type` | String | Immer auf `Bearer` festgelegt. |
| `scope` | Durch Leerzeichen getrennte Zeichenfolgen | Wenn ein Zugriffstoken zurückgegeben wurde, führt dieser Parameter die Bereiche auf, für die das Zugriffstoken gültig ist. |
| `expires_in`| INT | Die Anzahl von Sekunden, die das enthaltene Zugriffstoken gültig ist. |
| `access_token`| Nicht transparente Zeichenfolge | Ausgestellt für die [Bereiche](v2-permissions-and-consent.md), die angefordert wurden. |
| `id_token` | JWT | Ausgestellt, wenn der ursprüngliche `scope`-Parameter den `openid`-Bereich enthalten hat. |
| `refresh_token` | Nicht transparente Zeichenfolge | Ausgestellt, wenn der ursprüngliche `scope`-Parameter `offline_access` enthalten hat. |

Sie können mit Aktualisierungstoken neue Zugriffstoken und Aktualisierungstoken abrufen. Verwenden Sie den in der [Dokumentation für den OAuth-Code](v2-oauth2-auth-code-flow.md#refresh-the-access-token) beschriebenen Flow.

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>Fehlerantwort

Wenn der Benutzer nicht den richtigen Benutzernamen bzw. das richtige Kennwort angegeben hat oder der Client nicht über die erforderliche Berechtigung verfügt, schlägt die Authentifizierung fehl.

| Fehler | BESCHREIBUNG | Clientaktion |
|------ | ----------- | -------------|
| `invalid_grant` | Fehler bei der Authentifizierung | Die Anmeldeinformationen waren falsch oder dem Client fehlt die Berechtigung für die angeforderten Bereiche. Wenn die Bereiche nicht gewährt werden, wird ein Fehler vom Typ `consent_required` zurückgegeben. In diesem Fall sollte der Client den Benutzer über eine Webansicht oder einen Browser zu einer interaktiven Eingabeaufforderung weiterleiten. |
| `invalid_request` | Anforderung war nicht ordnungsgemäß konstruiert | Der Gewährungstyp wird in den Authentifizierungskontexten `/common` oder `/consumers` nicht unterstützt.  Verwenden Sie stattdessen `/organizations` oder eine Mandanten-ID. |

## <a name="learn-more"></a>Weitere Informationen

Ein Beispiel für die Verwendung von ROPC finden Sie im Codebeispiel [.NET Core-Konsolenanwendung](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) auf GitHub.
