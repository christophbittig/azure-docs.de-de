---
title: Authentifizierung und Autorisierung für Azure Static Web Apps
description: Es wird beschrieben, wie Sie verschiedene Autorisierungsanbieter verwenden, um Ihre statische App zu schützen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: cshoe
ms.openlocfilehash: e38cc40407f636f8bfd53a9196ecaf9c431d34db
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729822"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Authentifizierung und Autorisierung für Azure Static Web Apps

Azure Static Web Apps bietet eine optimierte Authentifizierungserfahrung. Standardmäßig haben Sie Zugriff auf eine Reihe von vorkonfigurierten Anbietern oder die Option zum [Registrieren eines benutzerdefinierten Anbieters](./authentication-custom.md).

- Jeder Benutzer kann sich bei einem aktivierten Anbieter authentifizieren.
- Nach der Anmeldung gehören Benutzer standardmäßig den Rollen `anonymous` und `authenticated` an.
- Autorisierte Benutzer erhalten Zugriff auf eingeschränkte [Routen](configuration.md#routes) durch Regeln, die in der [Datei „staticwebapp.config.json“](./configuration.md) definiert sind.
- Benutzern werden mithilfe des integrierten [Einladungssystems](#invitations) benutzerdefinierte Rollen zugewiesen.
- Benutzern können bei der Anmeldung durch eine API-Funktion programmgesteuert benutzerdefinierte Rollen zugewiesen werden.
- Alle Authentifizierungsanbieter sind standardmäßig aktiviert.
  - [Blockieren Sie den Zugriff](#block-an-authorization-provider) mit einer benutzerdefinierten Routenregel, um einen Authentifizierungsanbieter auszuschließen.
- Vorkonfigurierte Anbieter umfassen:
  - Azure Active Directory
  - GitHub
  - Twitter

Die Themen Authentifizierung und Autorisierung überlappen sich erheblich mit Routingkonzepten, die im [Anwendungskonfigurationshandbuch](configuration.md#routes) beschrieben werden.

## <a name="roles"></a>Rollen

Jeder Benutzer, der auf eine statische Web-App zugreift, gehört mindestens einer Rolle an. Benutzer können zwei integrierten Rollen angehören:

- **anonymous**: Alle Benutzer gehören automatisch der Rolle _anonymous_ an.
- **authenticated**: Alle Benutzer, die angemeldet sind, gehören der Rolle _authenticated_ an.

Zusätzlich zu den integrierten Rollen können Sie Benutzern benutzerdefinierte Rollen zuweisen und in der Datei _staticwebapp.config.json_ darauf verweisen.

## <a name="role-management"></a>Rollenverwaltung

# <a name="invitations"></a>[Einladungen](#tab/invitations)

### <a name="add-a-user-to-a-role"></a>Hinzufügen eines Benutzers zu einer Rolle

Um einen Benutzer zu einer Rolle hinzuzufügen, generieren Sie Einladungen, mit denen Sie Benutzer bestimmten Rollen zuordnen können. Rollen werden in der Datei _staticwebapp.config.json_ definiert und verwaltet.

> [!NOTE]
> Sie können [einen benutzerdefinierten Azure Active Directory-Anbieter registrieren](./authentication-custom.md), um das Ausstellen von Einladungen für die Gruppenverwaltung zu vermeiden.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Erstellen einer Einladung

Einladungen gelten spezifisch für einzelne Autorisierungsanbieter. Aus diesem Grund sollten Sie die Anforderungen Ihrer App beachten, wenn Sie die zu unterstützenden Anbieter auswählen. Einige Anbieter legen die E-Mail-Adresse eines Benutzers offen, während andere nur den Benutzernamen für die Website angeben.

<a name="provider-user-details" id="provider-user-details"></a>

| Autorisierungsanbieter | Offenlegung von Benutzerdaten |
| ---------------------- | ---------------- |
| Azure Active Directory | E-Mail-Adresse    |
| GitHub                 | username         |
| Twitter                | username         |

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einer Static Web Apps-Ressource.
1. Klicken Sie unter _Einstellungen_ auf **Rollenverwaltung**.
1. Klicken Sie auf die Schaltfläche **Einladen**.
1. Wählen Sie in der Liste mit den Optionen den Eintrag _Autorisierungsanbieter_ aus.
1. Fügen Sie im Feld _Invitee details_ (Details zum Eingeladenen) den Benutzernamen oder die E-Mail-Adresse des Empfängers hinzu.
   - Geben Sie bei GitHub und Twitter den Benutzernamen ein. Geben Sie für alle anderen Anbieter die E-Mail-Adresse des Empfängers ein.
1. Wählen Sie in der Dropdownliste _Domäne_ die Domäne Ihrer statischen Website aus.
   - Die von Ihnen ausgewählte Domäne ist die Domäne, die in der Einladung angezeigt wird. Falls Sie über eine benutzerdefinierte Domäne verfügen, die Ihrer Website zugeordnet ist, möchten Sie wahrscheinlich diese benutzerdefinierte Domäne auswählen.
1. Fügen Sie im Feld _Rolle_ eine Liste mit Rollennamen (durch Kommas getrennt) ein.
1. Geben Sie die Anzahl von Stunden für die maximale Gültigkeitsdauer der Einladung ein.
   - Die Obergrenze beträgt 168 Stunden (7 Tage).
1. Klicken Sie auf die Schaltfläche **Generieren**.
1. Kopieren Sie den Link aus dem Feld _Einladungslink_.
1. Senden Sie eine E-Mail mit dem Einladungslink an die Person, der Sie Zugriff auf Ihre App erteilen möchten.

Wenn der Benutzer in der Einladung auf den Link klickt, wird er aufgefordert, sich mit seinem entsprechenden Konto anzumelden. Nach der erfolgreichen Anmeldung wird der Benutzer den ausgewählten Rollen zugeordnet.

> [!CAUTION]
> Stellen Sie sicher, dass Ihre Routenregeln nicht in Konflikt mit Ihren ausgewählten Authentifizierungsanbietern stehen. Wenn ein Anbieter durch eine Routenregel blockiert wird, können Benutzer keine Einladungen akzeptieren.

### <a name="update-role-assignments"></a>Aktualisieren von Rollenzuweisungen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einer Static Web Apps-Ressource.
1. Klicken Sie unter _Einstellungen_ auf **Rollenverwaltung**.
1. Klicken Sie in der Liste auf den Benutzer.
1. Bearbeiten Sie die Liste mit den Rollen im Feld _Rolle_.
1. Klicken Sie auf die Schaltfläche **Aktualisieren**.

### <a name="remove-user"></a>Entfernen von Benutzern

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einer Static Web Apps-Ressource.
1. Klicken Sie unter _Einstellungen_ auf **Rollenverwaltung**.
1. Suchen Sie in der Liste nach dem Benutzer.
1. Aktivieren Sie das Kontrollkästchen in der Zeile des Benutzers.
1. Klicken Sie auf die Schaltfläche **Löschen** .

Beachten Sie beim Entfernen eines Benutzers Folgendes:

1. Wenn ein Benutzer entfernt wird, werden seine Berechtigungen ungültig.
1. Die weltweite Verteilung kann einige Minuten dauern.
1. Wenn der Benutzer wieder der App hinzugefügt wird, ändert sich die [`userId`](user-information.md).

# <a name="function-preview"></a>[Funktion (Vorschau)](#tab/function)

Anstatt des integrierten Einladungssystems können Sie eine serverlose Funktion verwenden, um Benutzern bei der Anmeldung programmgesteuert Rollen zuzuweisen.

Um benutzerdefinierte Rollen in einer Funktion zuzuweisen, können Sie eine API-Funktion definieren, die nach jeder erfolgreichen Authentifizierung eines Benutzers bei einem Identitätsanbieter automatisch aufgerufen wird. Der Funktion werden die Benutzerinformationen vom Anbieter übergeben. Sie muss eine Liste benutzerdefinierter Rollen zurückgeben, die dem Benutzer zugewiesen werden.

Beispiele für die Verwendung dieser Funktion sind:

- Abfragen einer Datenbank, um zu bestimmen, welche Rollen einem Benutzer zugewiesen werden sollen
- Aufrufen der [Microsoft Graph-API](https://developer.microsoft.com/graph), um die Rollen eines Benutzers basierend auf seiner Active Directory-Gruppenmitgliedschaft zu bestimmen
- Bestimmen der Rollen eines Benutzers basierend auf Ansprüchen, die vom Identitätsanbieter zurückgegeben werden

> [!NOTE]
> Die Möglichkeit, Rollen über eine Funktion zuzuweisen, ist nur verfügbar, wenn die [benutzerdefinierte Authentifizierung](authentication-custom.md) konfiguriert ist.
>
> Wenn dieses Feature aktiviert ist, werden alle über das integrierte Einladungssystem zugewiesenen Rollen ignoriert.

### <a name="configure-a-function-for-assigning-roles"></a>Konfigurieren einer Funktion zum Zuweisen von Rollen

Um Azure Static Web Apps für die Verwendung einer API-Funktion als Funktion für die Zuweisung von Rollen zu konfigurieren, fügen Sie dem Abschnitt `auth` der [Konfigurationsdatei](configuration.md) Ihrer App eine `rolesSource`-Eigenschaft hinzu. Der Wert der `rolesSource`-Eigenschaft ist der Pfad zur API-Funktion.

```json
{
  "auth": {
    "rolesSource": "/api/GetRoles",
    "identityProviders": {
      // ...
    }
  }
}
```

> [!NOTE]
> Nach der Konfiguration kann von externen HTTP-Anforderungen nicht mehr auf die Rollenzuweisungsfunktion zugegriffen werden.

### <a name="create-a-function-for-assigning-roles"></a>Erstellen einer Funktion zum Zuweisen von Rollen

Nachdem Sie die `rolesSource`-Eigenschaft in der Konfiguration Ihrer App definiert haben, fügen Sie in Ihrer statischen Web-App unter dem angegebenen Pfad eine [API-Funktion](apis.md) hinzu. Sie können eine „Verwaltete Funktion“-App oder eine BYOF-App (Bring Your Own Function) verwenden.

Jedes Mal, wenn sich ein Benutzer erfolgreich bei einem Identitätsanbieter authentifiziert, wird die angegebene Funktion aufgerufen. Der Funktion wird ein JSON-Objekt im Anforderungstext übergeben, das die Informationen des Benutzers vom Anbieter enthält. Bei einigen Identitätsanbietern enthalten die Benutzerinformationen auch ein `accessToken`-Objekt, das die Funktion verwenden kann, um API-Aufrufe mit der Benutzeridentität vorzunehmen.

Dies ist eine Beispielnutzlast aus Azure Active Directory:

```json
{
  "identityProvider": "aad",
  "userId": "72137ad3-ae00-42b5-8d54-aacb38576d76",
  "userDetails": "ellen@contoso.com",
  "claims": [
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname",
          "val": "Contoso"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname",
          "val": "Ellen"
      },
      {
          "typ": "name",
          "val": "Ellen Contoso"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/objectidentifier",
          "val": "7da753ff-1c8e-4b5e-affe-d89e5a57fe2f"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
          "val": "72137ad3-ae00-42b5-8d54-aacb38576d76"
      },
      {
          "typ": "http://schemas.microsoft.com/identity/claims/tenantid",
          "val": "3856f5f5-4bae-464a-9044-b72dc2dcde26"
      },
      {
          "typ": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "val": "ellen@contoso.com"
      },
      {
          "typ": "ver",
          "val": "1.0"
      }
  ],
  "accessToken": "eyJ0eXAiOiJKV..."
}
```

Die Funktion kann anhand der Benutzerinformationen bestimmen, welche Rollen dem Benutzer zugewiesen werden sollen. Sie muss eine HTTP 200-Antwort mit einem JSON-Text zurückgeben, der eine Liste benutzerdefinierter Rollennamen enthält, die dem Benutzer zugewiesen werden sollen.

Um dem Benutzer z. B. die Rollen `Reader` und `Contributor` zuzuweisen, geben Sie die folgende Antwort zurück:

```json
{
  "roles": [
    "Reader",
    "Contributor"
  ]
}
```

Wenn Sie dem Benutzer keine zusätzlichen Rollen zuweisen möchten, geben Sie ein leeres `roles`-Array zurück.

Weitere Informationen finden Sie unter [Tutorial: Zuweisen von benutzerdefinierten Rollen mit einer Funktion und Microsoft Graph](assign-roles-microsoft-graph.md).

---

## <a name="remove-personal-identifying-information"></a>Entfernen von personenbezogenen Informationen

Wenn Sie als Endbenutzer für eine Anwendung Ihre Einwilligung erteilen, hat die Anwendung je nach Identitätsanbieter Zugriff auf Ihre E-Mail-Adresse oder Ihren Benutzernamen. Nachdem diese Informationen bereitgestellt wurden, trifft der Besitzer der Anwendung die Entscheidung, wie personenbezogene Informationen verwaltet werden.

Endbenutzer müssen sich an die Administratoren der einzelnen Web-Apps wenden, um diese Informationen für ihre Systeme zu widerrufen.

Senden Sie über die folgende URL eine Anfrage, um personenbezogene Informationen von der Azure Static Web Apps-Plattform entfernen zu lassen und zu verhindern, dass diese Informationen über die Plattform bei zukünftigen Anforderungen bereitgestellt werden:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Senden Sie unter der folgenden URL eine Anfrage, um für einzelne Apps zu verhindern, dass die Plattform diese Informationen bei zukünftigen Anforderungen bereitstellt:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Hinweis: Wenn Sie Azure Active Directory verwenden, geben Sie `aad` als Wert für den `<AUTHENTICATION_PROVIDER_NAME>`-Platzhalter an.

## <a name="system-folder"></a>Systemordner

Azure Static Web Apps nutzt den Systemordner `/.auth`, um Zugriff auf autorisierungsbezogene APIs zu gewähren. Anstatt die Routen im Ordner `/.auth` direkt für Endbenutzer offenzulegen, können Sie erwägen, [Routingregeln](configuration.md#routes) für die Erstellung von benutzerfreundlichen URLs festzulegen.

## <a name="login"></a>Anmeldename

Verwenden Sie die folgende Tabelle, um die anbieterspezifische Route zu ermitteln.

| Autorisierungsanbieter | Anmelderoute             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| GitHub                 | `/.auth/login/github`   |
| Twitter                | `/.auth/login/twitter`  |

Wenn Sie sich beispielsweise mit GitHub anmelden möchten, können Sie einen Link wie den folgenden Codeausschnitt einfügen:

```html
<a href="/.auth/login/github">Login</a>
```

Falls Sie mehr als einen Anbieter unterstützen möchten, müssen Sie auf Ihrer Website dafür jeweils einen anbieterspezifischen Link bereitstellen.

Sie können eine [Routenregel](./configuration.md#routes) verwenden, um einen Standardanbieter einer benutzerfreundlichen Route zuzuordnen, z. B. _/login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Umleitung nach der Anmeldung

Wenn ein Benutzer nach der Anmeldung zu einer bestimmten Seite zurückgeleitet werden soll, geben Sie eine vollqualifizierte URL im `post_login_redirect_uri`-Abfragezeichenfolgenparameter an.

Beispiel:

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Logout

Mit der Route `/.auth/logout` werden Benutzer von der Website abgemeldet. Sie können einen Link zu Ihrer Sitenavigation hinzufügen, um dem Benutzer das Abmelden zu ermöglichen. Dies ist im folgenden Beispiel dargestellt.

```html
<a href="/.auth/logout">Log out</a>
```

Sie können eine [Routenregel](./configuration.md#routes) verwenden, um eine benutzerfreundliche Route wie _/logout_ zuzuordnen.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Umleitung nach der Abmeldung

Wenn ein Benutzer nach der Abmeldung zu einer bestimmten Seite zurückgeleitet werden soll, geben Sie eine URL im `post_logout_redirect_uri`-Abfragezeichenfolgenparameter an.

## <a name="block-an-authorization-provider"></a>Blockieren eines Autorisierungsanbieters

Unter Umständen möchten Sie für Ihr App die Verwendung eines Autorisierungsanbieters verhindern. Es kann beispielsweise sein, dass für Ihre App standardmäßig nur [Anbieter genutzt werden sollen, die E-Mail-Adressen offenlegen](#provider-user-details).

Zum Blockieren eines Anbieters können Sie [Routenregeln](configuration.md#routes) erstellen, um einen 404-Fehler für Anforderungen zurückzugeben, die an die blockierte anbieterspezifische Route gesendet werden. Fügen Sie beispielsweise die folgende Routenregel hinzu, um Twitter als Anbieter auszuschließen.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": 404
}
```

## <a name="restrictions"></a>Beschränkungen

Allgemeine Einschränkungen und Grenzwerte finden Sie im [Artikel zu Kontingenten](quotas.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zugreifen auf Daten der Benutzerauthentifizierung und -autorisierung](user-information.md)

<sup>1</sup> Externe Zertifizierung ausstehend.
