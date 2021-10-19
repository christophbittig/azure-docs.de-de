---
title: FHIR-Server in Azure mit Postman – Azure API for FHIR
description: In diesem Tutorial werden die Schritte beschrieben, die erforderlich sind, um Postman für den Zugriff auf einen FHIR-Server zu verwenden. Postman ist beim Debuggen von Anwendungen nützlich, die auf APIs zugreifen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 1080b8fb47ebcab07737d26464302a9e18465315
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785307"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Zugreifen auf Azure API for FHIR mit Postman

Eine Clientanwendung kann auf die Azure API for FHIR über eine [REST-API](https://www.hl7.org/fhir/http.html) zugreifen. Um Anforderungen zu senden, Antworten anzuzeigen und Ihre Anwendung während der Erstellung zu debuggen, verwenden Sie ein API-Testtool Ihrer Wahl. In diesem Tutorial werden die Schritte zum Zugreifen auf den FHIR-Server mit [Postman](https://www.getpostman.com/)beschrieben. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein FHIR-Endpunkt in Azure. 

  Zum Bereitstellen der Azure API for FHIR (ein verwalteter Dienst) können Sie die [Azure-Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)oder [Azure CLI](fhir-paas-cli-quickstart.md)verwenden.

- Eine registrierte [vertrauliche Clientanwendung](register-confidential-azure-ad-client-app.md) für den Zugriff auf den FHIR-Dienst.
- Sie haben der vertraulichen Clientanwendung und Ihrem Benutzerkonto ,z.B. "FHIR-Datenmitwirkender" Berechtigungen für den Zugriff auf den FHIR-Dienst erteilt. Weitere Informationen finden Sie unter [Konfigurieren von Azure RBAC für FHIR.](./configure-azure-rbac.md)
- Postman ist installiert. 
    
  Weitere Informationen zu Postman finden Sie unter [Erste Schritte mit Postman.](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR-Server und Authentifizierungsdetails

Für die Verwendung von Postman sind die folgenden Authentifizierungsparameter erforderlich:

- Ihre FHIR-Server-URL, z. B. `https://MYACCOUNT.azurehealthcareapis.com`

- Der Identitätsanbieter `Authority` für Ihren FHIR-Server, z. B. `https://login.microsoftonline.com/{TENANT-ID}`.

- Der konfigurierte `audience` , der in der Regel die URL des FHIR-Servers ist, z. `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` B. oder `https://azurehealthcareapis.com` .

- Die `client_id` Anwendungs-ID oder die Anwendungs-ID der [vertraulichen Clientanwendung,](register-confidential-azure-ad-client-app.md) die für den Zugriff auf den FHIR-Dienst verwendet wird.

- Der `client_secret` geheime Anwendungsschlüssel oder der geheime Schlüssel der vertraulichen Clientanwendung.

Zum Schluss sollten Sie überprüfen, ob `https://www.getpostman.com/oauth2/callback` eine registrierte Antwort-URL für Ihre Clientanwendung ist.

## <a name="connect-to-fhir-server"></a>Verbinden mit dem FHIR-Server

Öffnen Sie Postman, und wählen Sie dann **GET** aus, um eine Anforderung an zu `https://fhir-server-url/metadata` stellen.

![Metadaten für Funktionsbestätigung in Postman](media/tutorial-postman/postman-metadata.png)

Die Metadaten-URL für Azure API for FHIR lautet `https://MYACCOUNT.azurehealthcareapis.com/metadata`. 

In diesem Beispiel lautet die FHIR-Server-URL `https://fhirdocsmsft.azurewebsites.net` , und die Funktions-Anweisung des Servers ist unter `https://fhirdocsmsft.azurewebsites.net/metadata` verfügbar. Auf diesen Endpunkt kann ohne Authentifizierung zugegriffen werden.

Wenn Sie versuchen, auf eingeschränkte Ressourcen zuzugreifen, wird eine Antwort mit dem Fehler bei der Authentifizierung angezeigt.

![Fehler bei der Authentifizierung](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Abrufen eines Zugriffstokens
Wählen Sie **Get New Access Token** (Neues Zugriffstoken abrufen) aus.

Um ein gültiges Zugriffstoken abzurufen, wählen Sie **Autorisierung** und dann im Dropdownmenü **TYPE** die Option **OAuth 2.0** aus.

![Festlegen von OAuth 2.0](media/tutorial-postman/postman-select-oauth-two.png)

Wählen Sie **Get New Access Token** (Neues Zugriffstoken abrufen) aus.

![Anfordern eines neuen Zugriffstokens](media/tutorial-postman/postman-request-token.png)

Geben Sie im Dialogfeld **Neues Zugriffstoken abrufen** die folgenden Details ein:

| Feld                 | Beispielwert                                                                                                   | Kommentar                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Tokenname            | MYTOKEN                                                                                                         | Ein von Ihnen gewählter Name          |
| Gewährungstyp            | Autorisierungscode                                                                                              |                            |
| Rückruf-URL          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| Authentifizierungs-URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` ist `https://MYACCOUNT.azurehealthcareapis.com` für Azure API for FHIR |
| Zugriffstoken-URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| Client-ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | Anwendungs-ID             |
| Geheimer Clientschlüssel         | `XXXXXXXX`                                                                                                      | Geheimer Clientschlüssel          |
| Bereich | `<Leave Blank>` | Der Bereich wird nicht verwendet. daher kann sie leer gelassen werden.  
| State                 | `1234`     | [Der Zustand](https://learning.postman.com/docs/sending-requests/authorization/) ist ein nicht transparenter Wert, um websiteübergreifende Anforderungsfälschungen zu verhindern. Sie ist optional und kann einen beliebigen Wert wie "1234" annehmen.                           |
| Clientauthentifizierung | Clientanmeldeinformationen im Text senden                                                                                 |                 

Wählen Sie **Anforderungstoken** aus, um den Azure Active Directory Authentifizierungsablauf zu durchlaufen, und ein Token wird an Postman zurückgegeben. Wenn ein Authentifizierungsfehler auftritt, finden Sie weitere Informationen in der Postman-Konsole. **Hinweis:** Wählen Sie im Menüband **Ansicht** und dann **Postman-Konsole anzeigen** aus. Die Tastenkombination zur Postman-Konsole lautet **ALT+STRG+C.**

Scrollen Sie nach unten, um den zurückgegebenen Tokenbildschirm anzuzeigen, und wählen Sie dann **Token verwenden** aus.

![Token verwenden](media/tutorial-postman/postman-use-token.png)

Im Feld **Zugriffstoken** können Sie das neu aufgefüllte Token anzeigen. Wenn Sie **Senden** auswählen, um die Ressourcensuche zu `Patient` wiederholen, wird ein **Status** `200 OK` zurückgegeben. Ein zurückgegebener Status `200 OK` gibt eine erfolgreiche HTTP-Anforderung an.

![200 – OK](media/tutorial-postman/postman-two-hundred-ok.png)

Im Beispiel für die *Patientensuche* sind keine Patienten in der Datenbank vorhanden, sodass das Suchergebnis leer ist.

Sie können das Zugriffstoken mit einem Tool wie [jwt.ms](https://jwt.ms)überprüfen. Unten sehen Sie ein Beispiel für den Inhalt.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Bei der Problembehandlung empfiehlt es sich, zuerst zu überprüfen, ob Sie die richtige Zielgruppe (`aud`-Anspruch) haben. Wenn Ihr Token vom richtigen Aussteller `iss` (Anspruch) stammt und über die richtige Zielgruppe `aud` (Anspruch) verfügt, Sie aber weiterhin nicht auf die FHIR-API zugreifen können, hat der Benutzer oder Dienstprinzipal `oid` (Anspruch) wahrscheinlich keinen Zugriff auf die FHIR-Datenebene. Es wird empfohlen, die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](configure-azure-rbac.md) zu verwenden, um Benutzern Rollen auf Datenebene zuzuweisen. Wenn Sie einen externen, sekundären Azure Active Directory-Mandanten für Ihre Datenebene verwenden, müssen Sie [lokale RBAC für FHIR-Zuweisungen konfigurieren.](configure-local-rbac.md)

Es ist auch möglich, [mithilfe](get-healthcare-apis-access-token-cli.md)des Azure CLI ein Token für die Azure API for FHIR abzurufen. Wenn Sie ein Token verwenden, das mit dem Azure CLI abgerufen wurde, sollten Sie den Autorisierungstyp *Bearertoken* verwenden. Fügen Sie das Token direkt ein.

## <a name="inserting-a-patient"></a>Einfügen eines Patienten

Mit einem gültigen Zugriffstoken können Sie jetzt einen neuen Patienten einfügen. Ändern Sie in Postman die -Methode, indem Sie **Post** auswählen, und fügen Sie dann das folgende JSON-Dokument im Text der Anforderung hinzu.

[!code-json[](../samples/sample-patient.json)]

Wählen Sie **Senden** aus, um zu ermitteln, ob der Patient erfolgreich erstellt wurde.

![Screenshot: Erfolgreiche Erstellung des Patienten](media/tutorial-postman/postman-patient-created.png)

Wenn Sie die Patientensuche wiederholen, sollte nun die Patientenakte angezeigt werden.

![Patient erstellt](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie über Postman auf die Azure API for FHIR zugegriffen. Weitere Informationen zu den Azure API for FHIR Features finden Sie unter
 
>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)
