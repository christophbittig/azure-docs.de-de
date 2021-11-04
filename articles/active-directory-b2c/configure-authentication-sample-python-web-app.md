---
title: Konfigurieren der Authentifizierung in einer Python-Beispiel-Webanwendung mithilfe von Azure Active Directory B2C
description: Dieser Artikel beschreibt, wie man Azure Active Directory B2C verwendet, um Benutzer in einer Python-Webanwendung anzumelden und zu registrieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1466e62e50cac9d24616c9662f73ad23f3f6eea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007915"
---
# <a name="configure-authentication-in-a-sample-python-web-app-by-using-azure-ad-b2c"></a>Konfigurieren Sie die Authentifizierung in einer Python-Webanwendung mit Azure AD B2C

In diesem Artikel wird anhand einer Python-Beispielwebanwendung veranschaulicht, wie Sie Ihren Webanwendungen die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können OIDC verwenden, um Benutzer sicher bei einer Anwendung anzumelden. Dieses Webanwendungsbeispiel verwendet die [Microsoft Authentifizierungs-Bibliothek (MSAL) für Python](https://github.com/AzureAD/microsoft-authentication-library-for-python). Die MSAL für Python vereinfacht das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu Python-Webanwendungen. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer navigieren zur Web-App und wählen **Anmelden** aus. 
1. Die App löst eine Authentifizierungsanforderung aus und leitet Benutzer an Azure AD B2C um.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md), [setzen das Passwort zurück](add-password-reset-policy.md), oder melden sich mit einem [Social Account](add-identity-provider.md) an.
1. Nachdem die Benutzer sich erfolgreicher angemeldet haben, gibt Azure AD B2C ein ID-Token an die App zurück.
1. Die App tauscht den Autorisierungscode gegen ein ID-Token aus, validiert das ID-Token, liest die Forderungen und gibt dann eine sichere Seite an die Benutzer zurück.


### <a name="sign-out"></a>Abmeldung

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem Folgendes ausgeführt wird: 

* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Python](https://nodejs.org/en/download/) 2.7+ oder 3+ 

## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>Schritt 2: Registrieren einer Webanwendung

Damit sich Ihre Anwendung mit Azure AD B2C anmelden kann, müssen Sie Ihre App im Azure AD B2C-Verzeichnis registrieren. Durch das Registrieren Ihrer App wird eine Vertrauensstellung zwischen der App und Azure AD B2C eingerichtet.  

Bei der App-Registrierung geben Sie den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den die Benutzer von Azure AD B2C umgeleitet werden, nachdem sie sich mit Azure AD B2C authentifiziert haben. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre App eindeutig identifiziert. Nachdem Ihre App registriert wurde, verwendet Azure AD B2C sowohl die Anwendungs-ID als auch den Umleitungs-URI, um Authentifizierungsanforderungen zu erstellen. 

### <a name="step-21-register-the-app"></a>Schritt 2.1: Registrieren der App

Gehen Sie zum Erstellen der Web-App-Registrierung folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein, (z. B. *webapp1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie `http://localhost:5000/getAToken` in das URL-Feld ein.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen **Administratoreinwilligung für OpenID- und Offlinezugriffsberechtigungen erteilen**.
1. Wählen Sie **Registrieren**.
1. Wählen Sie **Übersicht**.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der Webanwendung verwenden.

    ![Screenshot der Web-App-Übersichtsseite zur Erfassung Ihrer Web-App-ID.](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-create-a-web-app-client-secret"></a>Schritt 2.2: Erstellen eines Web-App-Client-Geheimnisses

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>Schritt 3: Herunterladen des Web-App-Beispiels

[Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip), oder klonen Sie die Beispielwebanwendung von GitHub. 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

Extrahieren Sie die Beispieldatei in einen Ordner, bei dem die Gesamtlänge des Pfads 260 oder weniger Zeichen oder umfasst.

## <a name="step-4-configure-the-sample-web-app"></a>Schritt 4: Konfigurieren der Beispiel-Web-App

Gehen Sie im Stammverzeichnis des Projekts wie folgt vor:

1. Benennen Sie die Datei *app_config.py* in *app_config.py.OLD* um.
1. Benennen Sie die Datei *app_config_b2c.py* in *app_config.py* um. 

Öffnen der Datei *app_config.py*. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen: 

|Schlüssel  |Wert  |
|---------|---------|
|`b2c_tenant`| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `contoso`).|
|`CLIENT_ID`| Die Web-API-Anwendungs-ID aus [Schritt 2.1](#step-21-register-the-app).|
|`CLIENT_SECRET`| Ersetzen des Clientschlüssel, den Sie in [Schritt 2.2](#step-22-create-a-web-app-client-secret) erstellt haben. Um die Sicherheit zu erhöhen, sollten Sie stattdessen eine Umgebungsvariable verwenden, wie in den Kommentaren empfohlen. |
|`*_user_flow`|Die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
| | |

Die endgültige Konfigurationsdatei sollte wie der folgende Python Code aussehen:

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!IMPORTANT]
> Wie in den Kommentaren zu den Codeschnipseln erwähnt, empfehlen wir, dass Sie *in Ihrem Anwendungscode keine Geheimnisse im Klartext* speichern. Die fest kodierte Variable wird im Codebeispiel *nur der Einfachheit halber verwendet*. Erwägen Sie die Verwendung einer Umgebungsvariablen oder eines Geheimspeichers, z. B. eines Azure Key Vault.


## <a name="step-5-run-the-sample-web-app"></a>Schritt 5: Ausführen der Beispiel-Web-App

1. Wechseln Sie in Ihrer Konsole oder Ihrem Terminal in das Verzeichnis, in dem sich das Beispiel befindet. Beispiel:

    ```console
    cd ms-identity-python-webapp
    ```
1. Installieren Sie die erforderlichen Pakete von PyPi und führen Sie die Webanwendung auf Ihrem lokalen Rechner aus, indem Sie die folgenden Befehle ausführen:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    Im Konsolenfenster wird die Portnummer der lokal ausgeführten Anwendung angezeigt:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on `http://localhost:5000/` (Press CTRL+C to quit)
    ```

 
1. Um die auf Ihrem lokalen Rechner laufende Webanwendung anzuzeigen, gehen Sie zu `http://localhost:5000`. 

1. Wählen Sie **Anmelden** aus.

    ![Der Screenshot zeigt die Anmeldung mit Azure AD B2C.](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab.

1. Nach erfolgreicher Authentifizierung sehen Sie Ihren Anzeigenamen, wie hier gezeigt: 

    ![Screenshot mit der Anzeige des Namens des Web-App-Tokens.](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="step-6-call-to-a-web-api"></a>Schritt 6: Aufrufen einer Web-API

Damit sich Ihre App mit Azure AD B2C anmelden und eine Web-API aufrufen kann, müssen Sie im Azure AD B2C-Verzeichnis zwei Anwendungen registrieren.  

- Die **Webanwendung** (Python) Registrierung, die Sie schon in [Schritt 2](#step-2-register-a-web-application) erstellt haben. Durch die Webanwendungsregistrierung kann sich Ihre Anwendung bei Azure AD B2C registrieren.  Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre App eindeutig identifiziert. Beispiel: **App-ID: 1**.

- Durch die **Web-API**-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Bei der App-Registrierung wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert (zum Beispiel *App-ID: 2*). Erteilen Sie Ihrer App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2).  

Die App-Registrierungen und die Anwendungsarchitektur werden in den folgenden Diagrammen beschrieben:

![Diagramm zur Beschreibung einer Web-App mit Web-API, Registrierungen und Token.](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="step-61-register-the-web-api-app"></a>Schritt 6.1: Registrierung der Web-API-Anwendung

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-62-configure-scopes"></a>Schritt 6.2: Konfigurieren von Geltungsbereichen

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-63-grant-the-web-app-permissions"></a>Schritt 6.3: Erteilen von Berechtigungen für die Webanwendung

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="step-64-configure-your-web-api"></a>Schritt 6.4: Konfigurieren Sie Ihre Web-API

Dieses Beispiel erwirbt ein Zugriffstoken mit den entsprechenden Bereichen, das die Webanwendung für eine Web-API verwenden kann. Um eine Web-API vom Code aus aufzurufen, verwenden Sie eine bestehende Web-API oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Authentifizierung in Ihrer eigenen Web-API mithilfe von Azure AD B2C](enable-authentication-web-api.md) aktivieren.

### <a name="step-65-configure-the-sample-app-with-the-web-api"></a>Schritt 6.5: Konfigurieren Sie die Beispielanwendung mit der Web-API

Öffnen der Datei *app_config.py*. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen: 

|Key  |Wert  |
|---------|---------|
|`ENDPOINT`| Der URI Ihrer Web-API (z. B. `https://localhost:44332/hello`).|
|`SCOPE`| Die von Ihnen erstellten Web-API [Scopes](#step-62-configure-scopes).|
| | |

Die endgültige Konfigurationsdatei sollte wie der folgende Python Code aussehen:

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="step-66-run-the-sample-app"></a>Schritt 6.6: Ausführen der Beispielanwendung

1. Wechseln Sie in Ihrer Konsole oder Ihrem Terminal in das Verzeichnis, in dem sich das Beispiel befindet. 
1. Beenden Sie die App. und führen Sie es erneut aus.
1. Wählen Sie **Aufruf von Microsoft Graph-API**.

    ![Screenshot, der zeigt, wie eine Web-API aufgerufen wird.](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="step-7-deploy-your-application"></a>Schritt 7: Bereitstellen der Anwendung 

In einer Produktionsanwendung handelt es sich bei dem Umleitungs-URI der App-Registrierung in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, z. B. `https://contoso.com/getAToken`. 

Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern. Für Umleitungs-URIs gelten die folgenden Einschränkungen:

* Die Antwort-URL muss mit dem Schema `https` beginnen.
* Bei der Antwort-URL muss die Groß-/Kleinschreibung beachtet werden. Die Groß-/Kleinschreibung muss der Groß-/Kleinschreibung des URL-Pfads Ihrer ausgeführten Anwendung entsprechen. 

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [Authentifizierungsoptionen in einer Python-Webanwendung mithilfe von Azure AD B2C](enable-authentication-python-web-app-options.md) konfigurieren.
