---
title: 'Schnellstart: Aufrufen von Microsoft Graph über eine Node.js-Konsolen-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Node.js-Konsolenanwendung unter Verwendung der App-eigenen Identität ein Zugriffstoken abrufen und eine API aufrufen kann, die durch einen Microsoft Identity Platform-Endpunkt geschützt ist.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 9a75435ca75e5d6638315f3c96716c091048694f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502066"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Node.js-Konsolen-App unter Verwendung der App-Identität

Das Codebeispiel, das in dieser Schnellstartanleitung heruntergeladen und ausgeführt wird, zeigt, wie eine Node.js-Konsolenanwendung unter Verwendung der App-Identität ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht, wie ein unbeaufsichtigter Auftrag oder Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann.

In dieser Schnellstartanleitung wird die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) mit der [Gewährung von Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) verwendet.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrieren und Herunterladen der Beispielanwendung
>
> Führen Sie die folgenden Schritte aus:
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Schritt 1: Registrieren der Anwendung
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `msal-node-cli`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
> 1. Wählen Sie unter **Geheimer Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, geben Sie einen Namen ein, und wählen Sie dann **Hinzufügen** aus. Notieren Sie den Wert für den geheimen Schlüssel an einem sicheren Ort, damit Sie ihn in einem späteren Schritt verwenden können.
> 1. Wählen Sie unter **Verwalten** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Wählen Sie **Microsoft Graph**.
> 1. Wählen Sie **Anwendungsberechtigungen**.
> 1. Wählen Sie unter dem Knoten **Benutzer** die Option **User.Read.All** und dann **Berechtigungen hinzufügen** aus.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Herunterladen und Konfigurieren der Beispiel-App
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Schritt 1: Konfigurieren der Anwendung im Azure-Portal
> Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Schritt 2: Herunterladen des Node.js-Beispielprojekts

> [!div renderon="docs"]
> [Laden Sie das Codebeispiel herunter](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Schritt 3: Konfigurieren des Node.js-Beispielprojekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise unter *C:\Azure-Samples*).
> 1. Bearbeiten Sie *.env*, und ersetzen Sie die Felder `TENANT_ID`, `CLIENT_ID` und `CLIENT_SECRET` durch den folgenden Ausschnitt:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Hierbei gilt:
>    - `Enter_the_Application_Id_Here`: Hierbei handelt es sich um die **Anwendungs-ID (Client)** der zuvor von Ihnen registrierten Anwendung. Diese ID befindet sich im Bereich **Übersicht** der App-Registrierung im Azure-Portal.
>    - `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z. B. „contoso.microsoft.com“).  Diese Werte befinden sich im Bereich **Übersicht** der App-Registrierung im Azure-Portal.
>    - `Enter_the_Client_Secret_Here`: Ersetzen Sie diesen Wert durch den zuvor erstellten geheimen Clientschlüssel. Generieren Sie im Azure-Portal mithilfe von **Zertifikate und Geheimnisse** in den App-Registrierungseinstellungen einen neuen Schlüssel.
>
> > [!WARNING]
> > Ein Klartextgeheimnis im Quellcode stellt ein höheres Sicherheitsrisiko dar. In diesem Artikel wird nur der Einfachheit halber ein geheimer Clientschlüssel in Klartext verwendet. Verwenden Sie in vertraulichen Clientanwendungen [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md) anstelle von geheimen Clientschlüsseln. Das gilt insbesondere bei Apps, die Sie in der Produktion bereitstellen möchten.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Schritt 4: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, wird der Fehler *HTTP 403 – Verboten* angezeigt: `Insufficient privileges to complete the operation`. Dieser Fehler tritt auf, da für eine *nur für die App geltende Berechtigung* eine **Administratoreinwilligung** erforderlich ist. Ein globaler Administrator Ihres Verzeichnisses muss der Anwendung also seine Einwilligung geben. Wählen Sie je nach Ihrer Rolle eine der unten angegebenen Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

> [!div renderon="docs"]
> Gehen Sie wie folgt vor, wenn Sie ein globaler Mandantenadministrator sind: Navigieren Sie im Azure-Portal in der Anwendungsregistrierung zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für „{Mandantenname}“ erteilen** aus. (Hierbei steht „{Mandantenname}“ für den Namen Ihres Verzeichnisses.)

> [!div renderon="portal" class="sxs-lookup"]
> Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für <Mandantennamen_hier_eingeben> erteilen**.
> > [!div id="apipermissionspage"]
> > [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, müssen Sie einen globalen Administrator bitten, die **Administratoreinwilligung** für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Hierbei gilt:
>> * `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>> * `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Schritt 5: Ausführen der Anwendung

Navigieren Sie über eine Eingabeaufforderung oder Konsole zum Stammordner des Beispiels (in dem sich `package.json` befindet). Sie müssen einmalig die Abhängigkeiten dieses Beispiels installieren:

```console
npm install
```

Führen Sie dann die Anwendung über die Eingabeaufforderung oder über die Konsole aus:

```console
node . --op getUsers
```

Die Konsolenausgabe sollte ein JSON-Fragment enthalten, das eine Liste der Benutzer in Ihrem Azure AD-Verzeichnis darstellt.

## <a name="about-the-code"></a>Informationen zum Code

Im Anschluss werden einige wichtige Aspekte der Beispielanwendung erläutert.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. In dieser Schnellstartanleitung werden Token nicht auf der Grundlage von delegierten Berechtigungen, sondern wie beschrieben auf der Grundlage von Anwendungsberechtigungen (unter Verwendung der anwendungseigenen Identität) angefordert. Der hier verwendete Authentifizierungsablauf wird als [OAuth 2.0-Clientanmeldeinformationsflow](v2-oauth2-client-creds-grant-flow.md) bezeichnet. Weitere Informationen zur Verwendung von MSAL Node mit Daemon-Apps finden Sie unter [Szenario: Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md).

 MSAL Node kann mithilfe des folgenden npm-Befehls installiert werden:

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```javascript
const msal = require('@azure/msal-node');
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Hierbei gilt: |BESCHREIBUNG |
> |---------|---------|
> | `clientId` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
> | `authority`    | Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei „{tenant}“ der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|
> | `clientSecret` | Der geheime Clientschlüssel, der für die Anwendung im Azure-Portal erstellt wird. |

Weitere Informationen finden Sie in der [Referenzdokumentation für `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md).

### <a name="requesting-tokens"></a>Anfordern von Token

Verwenden Sie die `acquireTokenByClientCredential`-Methode, um ein Token mit der Identität einer App anzufordern:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `tokenRequest` | Enthält die angeforderten Bereiche. Für vertrauliche Clients sollte ein Format wie `{Application ID URI}/.default` verwendet werden. Hiermit wird angegeben, dass die angeforderten Bereiche diejenigen sind, die im App-Objekt, das im Azure-Portal festgelegt ist, statisch definiert sind (für Microsoft Graph wird für `{Application ID URI}` auf `https://graph.microsoft.com` verwiesen). Für benutzerdefinierte Web-APIs wird `{Application ID URI}` in der Anwendungsregistrierung im Azure-Portal unter dem Abschnitt **Eine API verfügbar machen** definiert. |
> | `tokenResponse` | Die Antwort enthält ein Zugriffstoken für die angeforderten Bereiche. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Entwicklung von Daemon-/Konsolen-Apps mit MSAL Node finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Daemon-App zum Aufrufen von Web-APIs](tutorial-v2-nodejs-console.md)
