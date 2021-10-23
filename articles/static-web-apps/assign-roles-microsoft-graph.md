---
title: 'Tutorial: Zuweisen von Azure Static Web Apps-Rollen mit Microsoft Graph'
description: Hier erfahren Sie, wie Sie mithilfe einer serverlosen Funktion benutzerdefinierte Benutzerrollen basierend auf der Active Directory-Gruppenmitgliedschaft zuweisen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: Autorisierung statischer Web-Apps, Zuweisen von Benutzerrollen, benutzerdefinierte Rollen
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858827"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>Tutorial: Zuweisen von benutzerdefinierten Rollen mit einer Funktion und Microsoft Graph

In diesem Artikel wird veranschaulicht, wie Sie mithilfe einer Funktion [Microsoft Graph](https://developer.microsoft.com/graph) abfragen und einem Benutzer basierend auf seiner Active Directory-Gruppenmitgliedschaft benutzerdefinierte Rollen zuweisen.

In diesem Tutorial lernen Sie Folgendes:

- Bereitstellen einer statischen Web-App
- Erstellen einer Azure Active Directory-App-Registrierung
- Einrichten der benutzerdefinierten Authentifizierung mit Azure Active Directory
- Konfigurieren einer [serverlosen Funktion](authentication-authorization.md?tabs=function#role-management), mit der die Active Directory-Mitgliedschaft des Benutzers abgefragt und eine Liste mit benutzerdefinierten Rollen zurückgegeben wird

> [!NOTE]
> In diesem Tutorial müssen Sie eine [Funktion zum Zuweisen von Rollen verwenden](authentication-authorization.md?tabs=function#role-management). Die funktionsbasierte Rollenverwaltung befindet sich derzeit in der Vorschauphase.

## <a name="prerequisites"></a>Voraussetzungen

- **Aktives Azure-Konto:** Sollten Sie über kein Konto verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- Sie müssen über ausreichende Berechtigungen für die Erstellung einer Azure Active Directory-Anwendung verfügen.

## <a name="create-a-github-repository"></a>Erstellen eines GitHub-Repositorys

1. Navigieren Sie zum folgenden Speicherort, um ein neues Repository zu erstellen:
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. Geben Sie Ihrem Repository den Namen **my-custom-roles-app**.

1. Wählen Sie **Create repository from template** (Repository aus Vorlage erstellen) aus.

## <a name="deploy-the-static-web-app-to-azure"></a>Bereitstellen der statischen Web-App in Azure

1. Navigieren Sie in einem Browserfenster zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit Ihrem Azure-Konto an.

1. Wählen Sie in der linken oberen Ecke **Ressource erstellen** aus.

1. Geben Sie im Suchfeld den Suchbegriff **static web apps** ein.

1. Wählen Sie **Static Web Apps** aus.

1. Klicken Sie auf **Erstellen**.

1. Konfigurieren Sie Ihre Azure Static Web Apps-Instanz mit den folgenden Informationen:

    | **Eingabe** | **Wert** | **Hinweise** |
    | ---------- | ---------- | ---------- |
    | _Abonnement_ | Auswählen des Azure-Abonnements | |
    | _Ressourcengruppe_ | Erstellen Sie eine neue Ressourcengruppe mit dem Namen **my-custom-roles-app-group** | |
    | _Name_ | **my-custom-roles-app**  | |
    | _Hostingplan_ | **Standard** | Zum Anpassen der Authentifizierung und zum Zuweisen von Rollen mithilfe einer Funktion ist der Standard-Plan erforderlich. |
    | _Region_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. | |
    | _Bereitstellungsdetails_ | Wählen Sie **GitHub** als Quelle aus. | |

1. Klicken Sie auf die Schaltfläche **Mit GitHub anmelden**, und führen Sie die Authentifizierung mit GitHub durch.

1. Wählen Sie den Namen der _Organisation_ aus, in der Sie das Repository erstellt haben.

1. Wählen Sie in der Dropdownliste _Repository_ den Eintrag **my-custom-roles-app** aus.

1. Wählen Sie in der Dropdownliste _Branch_ den Eintrag **Hauptbranch** aus.

1. Fügen Sie im Abschnitt _Builddetails_ Konfigurationsdetails für diese App hinzu.

    | **Eingabe** | **Wert** | **Hinweise** |
    | ---------- | ---------- | ---------- |
    | _Buildvoreinstellungen_ | **Benutzerdefiniert** | |
    | _App-Speicherort_ | **frontend** | Ordner im Repository, das die App enthält |
    | _API-Speicherort_ | **api** | Ordner im Repository mit der API |
    | _Ausgabespeicherort_ | | Diese App hat keine Buildausgabe. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie dann **Erstellen** aus, um die statische Web-App zu erstellen und die erste Bereitstellung zu initiieren.

1. Wählen Sie **Zu Ressource wechseln** aus, um Ihre neue statische Web-App anzuzeigen.

1. Suchen Sie im Abschnitt „Übersicht“ nach der **URL** Ihrer Anwendung. Kopieren Sie diesen Wert in einen Text-Editor, da Sie diese URL zum Einrichten der Active Directory-Authentifizierung und zum Testen der App benötigen.

## <a name="create-an-azure-active-directory-application"></a>Erstellen einer Azure Active Directory-Anwendung

1. Navigieren Sie im Azure-Portal zu *Azure Active Directory*.

1. Wählen Sie auf der Menüleiste **App-Registrierungen** aus.

1. Wählen Sie **+ Neue Registrierung** aus, um die Seite *Eine Anwendung registrieren* zu öffnen.

1. Geben Sie einen Namen für die Anwendung ein, beispielsweise **MyStaticWebApp**.

1. Wählen Sie für *Unterstützte Kontotypen* die Option **Nur Konten in diesem Organisationsverzeichnis** aus.

1. Wählen Sie unter *Umleitungs-URIs* die Option **Web** aus, und geben Sie den [Authentifizierungsrückruf](authentication-custom.md#authentication-callbacks) der Azure Active Directory-Anmeldung für Ihre statische Web-App ein. Beispiel: `<YOUR_SITE_URL>/.auth/login/aad/callback`.

    Ersetzen Sie `<YOUR_SITE_URL>` durch die URL Ihrer statischen Web-App.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="Erstellen einer App-Registrierung":::

1. Wählen Sie **Registrieren**.

1. Nachdem die App-Registrierung erstellt wurde, kopieren Sie im Abschnitt *Zusammenfassung* die **Anwendungs-ID (Client)** und die **Verzeichnis-ID (Mandant)** in einen Text-Editor. Sie benötigen diese Werte, um die Active Directory-Authentifizierung in Ihrer statischen Web-App zu konfigurieren.

### <a name="enable-id-tokens"></a>Aktivieren von ID-Token

1. Wählen Sie auf der Menüleiste die Option *Authentifizierung* aus.

1. Wählen Sie im Abschnitt *Implizite Genehmigung und Hybridflows* die Option **ID-Token (werden für implizite und Hybridflows verwendet)** aus.

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="Aktivieren von ID-Token":::
    
    Diese Konfiguration wird von Static Web Apps zum Authentifizieren von Benutzern benötigt.

1. Wählen Sie **Speichern** aus.

### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

1. Wählen Sie auf der Menüleiste die Option *Zertifikate und Geheimnisse* aus.

1. Wählen Sie im Abschnitt *Geheime Clientschlüssel* die Option **+ Neuer geheimer Clientschlüssel** aus.

1. Geben Sie einen Namen für den geheimen Clientschlüssel ein. beispielsweise **MyStaticWebApp**.

1. Übernehmen Sie für das Feld *Gültig bis* den Standardwert _6 Monate_.

    > [!NOTE]
    > Sie müssen das Geheimnis vor dem Ablaufdatum rotieren, indem Sie ein neues Geheimnis generieren und Ihre App mit seinem Wert aktualisieren.

1. Wählen Sie **Hinzufügen**.

1. Notieren Sie sich den **Wert** des von Ihnen erstellten geheimen Clientschlüssels. Sie benötigen diesen Wert, um die Active Directory-Authentifizierung in Ihrer statischen Web-App zu konfigurieren.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="Erstellen eines Clientgeheimnisses":::

## <a name="configure-active-directory-authentication"></a>Konfigurieren der Active Directory-Authentifizierung

1. Öffnen Sie in einem Browser das GitHub-Repository, das die von Ihnen bereitgestellte statische Web-App enthält. Navigieren Sie zur Konfigurationsdatei der App unter *frontend/staticwebapp.config.json*. Sie enthält den folgenden Abschnitt:

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > Wenn Sie ein Zugriffstoken für Microsoft Graph abrufen möchten, muss das Feld `loginParameters` mit `resource=https://graph.microsoft.com` konfiguriert sein.

1. Wählen Sie die Schaltfläche **Bearbeiten** aus, um die Datei zu aktualisieren.

1. Aktualisieren Sie den Wert *openIdIssuer* von `https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>`, indem Sie `<YOUR_AAD_TENANT_ID>` durch die Verzeichnis-ID (Mandant) Ihrer Azure Active Directory-Instanz ersetzen.

1. Wählen Sie **Direkten Commit zum Hauptbranch ausführen** und dann **Commit für Änderungen ausführen** aus.

1. Eine GitHub Actions-Ausführung wird ausgelöst, um die statische Web-App zu aktualisieren.

1. Navigieren Sie im Azure-Portal zu Ihrer Ressource für die statische Web-App.

1. Wählen Sie auf der Menüleiste die Option **Konfiguration** aus.

1. Fügen Sie im Abschnitt *Anwendungseinstellungen* die folgenden Einstellungen hinzu:

    | Name | Wert |
    |------|-------|
    | `AAD_CLIENT_ID` | *ID Ihrer Active Directory-Anwendung (Client)* |
    | `AAD_CLIENT_SECRET` | *Wert des geheimen Clientschlüssels Ihrer Active Directory-Anwendung* |

1. Wählen Sie **Speichern** aus.

## <a name="verify-custom-roles"></a>Überprüfen benutzerdefinierter Rollen

Die Beispielanwendung enthält eine serverlose Funktion (*api/GetRoles/index.js*), mit der Microsoft Graph abgefragt wird, um zu ermitteln, ob sich ein Benutzer in einer vordefinierten Gruppe befindet. Basierend auf den Gruppenmitgliedschaften des Benutzers weist die Funktion dem Benutzer benutzerdefinierte Rollen zu. Die Anwendung ist so konfiguriert, dass bestimmte Routen basierend auf diesen benutzerdefinierten Rollen eingeschränkt werden.

1. Navigieren Sie in Ihrem GitHub-Repository unter *api/GetRoles/index.js* zur Funktion *GetRoles*. Am oberen Rand befindet sich ein `roleGroupMappings`-Objekt, das Azure Active Directory-Gruppen benutzerdefinierte Benutzerrollen zuordnet.

1. Klicken Sie auf die Schaltfläche **Bearbeiten**.

1. Aktualisieren Sie das Objekt mit Gruppen-IDs aus Ihrem Azure Active Directory-Mandanten.

    Wenn Sie beispielsweise Gruppen mit den IDs `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` und `b6059db5-9cef-4b27-9434-bb793aa31805` besitzen, würden Sie das Objekt wie folgt aktualisieren:

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    Die Funktion *GetRoles* wird immer dann aufgerufen, wenn ein Benutzer erfolgreich mit Azure Active Directory authentifiziert wurde. Die Funktion verwendet das Zugriffstoken des Benutzers, um die Active Directory-Gruppenmitgliedschaft von Microsoft Graph abzufragen. Wenn der Benutzer Mitglied einer Gruppe ist, die im Objekt `roleGroupMappings` definiert ist, werden die entsprechenden benutzerdefinierten Rollen von der Funktion zurückgegeben.
    
    Wenn ein Benutzer Mitglied der Active Directory-Gruppe mit der ID `b6059db5-9cef-4b27-9434-bb793aa31805` ist, wird ihm im obigen Beispiel die Rolle `reader` zugewiesen.

1. Wählen Sie **Direkten Commit zum Hauptbranch ausführen** und dann **Commit für Änderungen ausführen** aus.

1. Eine GitHub Actions-Ausführung wird ausgelöst, um die statische Web-App zu aktualisieren.

1. Wenn die Bereitstellung abgeschlossen ist, können Sie Ihre Änderungen überprüfen, indem Sie zur URL der App navigieren.

1. Melden Sie sich mithilfe von Azure Active Directory bei Ihrer statischen Web-App an.

1. Wenn Sie angemeldet sind, zeigt die Beispiel-App eine Liste der Rollen an, die Ihnen basierend auf der Active Directory-Gruppenmitgliedschaft Ihrer Identität zugewiesen sind. Abhängig von diesen Rollen ist der Zugriff auf einige Routen in der App zulässig oder unzulässig.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.

1. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.

1. Wählen Sie den Namen der Ressourcengruppe aus, die Sie in diesem Tutorial verwendet haben.

1. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Authentifizierung und Autorisierung](./authentication-authorization.md)
