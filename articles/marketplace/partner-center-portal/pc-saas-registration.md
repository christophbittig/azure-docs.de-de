---
title: Registrieren einer SaaS-Anwendung – Azure Marketplace
description: Erfahren Sie, wie Sie über das Azure-Portal eine SaaS-Anwendung registrieren und ein Azure Active Directory-Sicherheitstoken erhalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 78a66070c6bcf03f4a279163106048d87fe27f23
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061380"
---
# <a name="register-a-saas-application"></a>Registrieren einer SaaS-Anwendung

In diesem Artikel erfahren Sie, wie Sie eine SaaS-Anwendung über das [Azure-Portal](https://portal.azure.com/) von Microsoft registrieren und das Zugriffstoken des Herausgebers (Azure Active Directory-Zugriffstoken) abrufen. Dieses Token wird vom Herausgeber verwendet, um die SaaS-Anwendung durch Aufrufen der SaaS-Fulfillment-APIs zu authentifizieren.  Von den Fulfillment-APIs werden die OAuth 2.0-Clientanmeldeinformationen für den Gewährungsflow auf Azure Active Directory-Endpunkten (v1.0) verwendet, um eine Dienst-zu-Dienst-Zugriffstokenanforderung durchzuführen.

Von Azure Marketplace werden keinerlei Vorgaben hinsichtlich der Authentifizierungsmethode gemacht, die von Ihrem SaaS-Dienst für Endbenutzer verwendet wird. Der folgende Flow ist nur zum Authentifizieren des SaaS-Diensts in Azure Marketplace erforderlich.

Weitere Informationen zu Azure Active Directory (AD) finden Sie unter [Was ist Authentifizierung?](../../active-directory/develop/authentication-vs-authorization.md).

## <a name="register-an-azure-ad-secured-app"></a>Registrieren einer über Azure AD geschützten App

Jede Anwendung muss zunächst in einem Azure AD-Mandanten registriert werden, um die Funktionen von Azure AD nutzen zu können. Im Rahmen dieses Registrierungsvorgangs müssen einige Details zu Ihrer Anwendung an Azure AD weitergegeben werden. Führen Sie die folgenden Schritte aus, um mit dem Azure-Portal eine neue Anwendung zu registrieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wenn Sie mit Ihrem Konto Zugriff auf mehrere Mandanten haben, wählen Sie Ihr Konto rechts oben aus. Legen Sie dann die Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
3. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory**, dann **App-Registrierungen** und anschließend **Registrierung einer neuen Anwendung**  aus.

    ![SaaS: AD-App-Registrierungen](./media/saas-offer-app-registration-v1.png)

4. Geben Sie auf der Seite „Erstellen“ die Registrierungsinformationen\' für Ihre Anwendung ein:
    -   **Name**: Geben Sie einen aussagekräftigen Anwendungsnamen ein.
    -   **Anwendungstyp**:  
        
        Wählen Sie die Option **Web-App/API** für [Clientanwendungen](../../active-directory/develop/developer-glossary.md#client-application) und [Ressourcen-/API-Anwendungen](../../active-directory/develop/developer-glossary.md#resource-server) aus, die auf einem sicheren Server installiert sind. Diese Einstellung wird für vertrauliche OAuth-[Webclients](../../active-directory/develop/developer-glossary.md#web-client) und für öffentliche [Clients auf Basis von Benutzer-Agents](../../active-directory/develop/developer-glossary.md#user-agent-based-client) verwendet.
        Außerdem kann dieselbe Anwendung sowohl einen Client als auch eine Ressource/API verfügbar machen.

        Spezifische Beispiele für Webanwendungen finden Sie in den Schnellstart-Einrichtungsanleitungen, die im Abschnitt [Erste Schritte](../../active-directory/develop/quickstart-create-new-tenant.md) des [Azure AD-Entwicklerleitfadens](../../active-directory/develop/index.yml) zur Verfügung stehen.

5. Wenn Sie so weit sind, klicken Sie auf **Registrieren**.  Ihrer neuen Anwendung wird von Azure AD eine eindeutige *Anwendungs-ID* zugewiesen. Es empfiehlt sich, eine einzelne App zu registrieren, die nur auf die API zugreift (als einzelner Mandant).

6. Navigieren Sie zum Erstellen eines geheimen Clientschlüssels zur Seite **Zertifikate und Geheimnisse**, und wählen Sie **+Neuer geheimer Clientschlüssel** aus.  Kopieren Sie den Geheimniswert für die Verwendung in Ihrem Code.

Die **Azure AD-App-ID** ist Ihrer Herausgeber-ID zugeordnet. Achten Sie daher darauf, dass in allen Ihren Angeboten die gleiche *App-ID* verwendet wird.

>[!Note]
>Wenn der Herausgeber über zwei oder mehr unterschiedliche Konten im Partner Center verfügt, können die Azure AD-App-Registrierungsdetails nur in einem Konto verwendet werden. Das Verwenden desselben Mandanten-ID/App-ID-Paars für ein Angebot unter einem anderen Herausgeberkonto wird nicht unterstützt.

## <a name="how-to-get-the-publishers-authorization-token"></a>Abrufen des Autorisierungstokens des Herausgebers

Nachdem Sie Ihre Anwendung registriert haben, können Sie das Autorisierungstoken des Herausgebers (Azure AD-Zugriffstoken, über den Azure AD V1-Endpunkt) programmgesteuert anfordern. Der Herausgeber muss dieses Token beim Aufrufen der verschiedenen SaaS-Fulfillment-APIs verwenden. Dieses Token ist nur eine Stunde lang gültig.

Weitere Informationen zu diesen Token finden Sie unter [Azure Active Directory-Zugriffstoken](../../active-directory/develop/access-tokens.md).  Im folgenden Flow wird das V1-Endpunkttoken verwendet.

### <a name="get-the-token-with-an-http-post"></a>Abrufen des Tokens mit „HTTP POST“

#### <a name="http-method"></a>HTTP-Methode

Post<br>

##### <a name="request-url"></a>*Anforderungs-URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI-Parameter*

|  Parametername    |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  Die Mandanten-ID der registrierten AAD-Anwendung. |

##### <a name="request-header"></a>*Anforderungsheader*

|  Headername       |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  Der Anforderung zugeordneter Inhaltstyp. Standardwert: `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Anforderungstext*

|  Eigenschaftenname     |  Erforderlich         |  BESCHREIBUNG |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  Gewährungstyp. Verwenden Sie `"client_credentials"`. |
|  `client_id`       |  True      |  Der Azure AD-App zugeordneter Client-/App-Bezeichner. |
|  `client_secret`   |  True      |  Der Azure AD-App zugeordnetes Geheimnis. |
|  `resource`        |  True      |  Zielressource, für die das Token angefordert wird. Verwenden Sie `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`, da die Marketplace-SaaS-API in diesem Fall immer die Zielressource ist. |

##### <a name="response"></a>*Antwort*

|  Name     |  type         |  BESCHREIBUNG |
|  ------   |  ---------------  | ------------ |
|  200 – OK   |  TokenResponse    |  Anforderung erfolgreich. |

##### <a name="tokenresponse"></a>*TokenResponse*

Beispiel für eine Antwort:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `access_token` | Dieses Element ist das Zugriffstoken (`<access_token>`), das Sie beim Aufrufen aller SaaS-Fulfillment- und Marketplace-Messungs-APIs als den Autorisierungsparameter übergeben. Beim Aufrufen einer geschützten REST-API wird das Token als „Bearertoken“ in das `Authorization`-Anforderungsheader-Feld eingebettet, damit der Aufrufer von der API authentifiziert werden kann. |
| `expires_in` | Der Zeitraum in Sekunden, wie lange das Zugriffstoken ab dem Zeitpunkt der Ausstellung gültig ist, bevor es abläuft. Der Ausstellungszeitpunkt ist im Anspruch `iat` des Tokens zu finden. |
| `expires_on` | Der Zeitpunkt, zu dem das Zugriffstoken abläuft. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `exp` des Tokens) dargestellt. |
| `not_before` | Der Zeitpunkt, ab dem das Zugriffstoken wirksam ist und akzeptiert werden kann. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `nbf` des Tokens) dargestellt. |
| `resource` | Die Ressource, für die das Zugriffstoken angefordert wurde (Übereinstimmung mit dem Abfragezeichenfolgenparameter `resource` der Anforderung). |
| `token_type` | Der Typ des Tokens. In diesem Fall ein „Bearerzugriffstoken“, sodass die Ressource Zugriff auf den Bearer dieses Tokens gewähren kann. |

## <a name="next-steps"></a>Nächste Schritte

In der über Azure AD geschützten App können jetzt die [SaaS-Fulfillment-Abonnement-APIs der Version 2](pc-saas-fulfillment-subscription-api.md) und die [SaaS-Fulfillment-Vorgangs-APIs der Version 2](pc-saas-fulfillment-operations-api.md) verwendet werden.
