---
title: Authentifizierung und Autorisierung für Azure Static Web Apps
description: Es wird beschrieben, wie Sie verschiedene Autorisierungsanbieter verwenden, um Ihre statische App zu schützen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 1ba278b80d75e64b4394bfeb1b98cd0ec2d805a8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469956"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Authentifizierung und Autorisierung für Azure Static Web Apps

Azure Static Web Apps bietet eine optimierte Authentifizierungserfahrung. Standardmäßig haben Sie Zugriff auf eine Reihe von vorkonfigurierten Anbietern oder die Option zum [Registrieren eines benutzerdefinierten Anbieters](./authentication-custom.md).

- Jeder Benutzer kann sich bei einem aktivierten Anbieter authentifizieren.
- Nach der Anmeldung gehören Benutzer standardmäßig den Rollen `anonymous` und `authenticated` an.
- Autorisierte Benutzer erhalten Zugriff auf eingeschränkte [Routen](configuration.md#routes) durch Regeln, die in der [Datei „staticwebapp.config.json“](./configuration.md) definiert sind.
- Benutzer treten benutzerdefinierten Rollen über anbieterspezifische [Einladungen](#invitations) oder über eine [benutzerdefinierte Azure Active Directory-Anbieterregistrierung](./authentication-custom.md) bei.
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

Zusätzlich zu den integrierten Rollen können Sie neue Rollen erstellen, sie mithilfe von Einladungen Benutzern zuweisen und in der Datei _staticwebapp.config.json_ darauf verweisen.

## <a name="role-management"></a>Rollenverwaltung

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
1. Klicken Sie auf die Schaltfläche **Löschen**.

Beachten Sie beim Entfernen eines Benutzers Folgendes:

1. Wenn ein Benutzer entfernt wird, werden seine Berechtigungen ungültig.
1. Die weltweite Verteilung kann einige Minuten dauern.
1. Wenn der Benutzer wieder der App hinzugefügt wird, ändert sich die [`userId`](user-information.md).

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
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Beschränkungen

Allgemeine Einschränkungen und Grenzwerte finden Sie im [Artikel zu Kontingenten](quotas.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zugreifen auf Daten der Benutzerauthentifizierung und -autorisierung](user-information.md)

<sup>1</sup> Externe Zertifizierung ausstehend.
