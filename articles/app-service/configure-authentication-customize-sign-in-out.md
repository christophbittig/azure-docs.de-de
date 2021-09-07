---
title: Anpassen von An- und Abmeldungen
description: Verwenden Sie die integrierte Authentifizierung und Autorisierung in App Service, und passen Sie gleichzeitig das An- und Abmeldeverhalten an.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: bdb100afa821aa08fb831aac53b1eb80cdda043e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429542"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>Anpassen der An- und Abmeldung in der Azure App Service-Authentifizierung

In diesem Artikel wird erläutert, wie Sie Benutzeran- und -abmeldungen anpassen, während Sie die integrierte [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) nutzen. 

## <a name="use-multiple-sign-in-providers"></a>Verwenden mehrerer Anmeldungsanbieter

Die Portalkonfiguration bietet keine einfache Möglichkeit, Ihren Benutzern mehrere Anmeldungsanbieter (z.B. sowohl Facebook als auch Twitter) bereitzustellen. Allerdings lässt sich diese Funktionalität Ihrer App problemlos hinzufügen. Dazu sind folgende Schritte erforderlich:

Zunächst konfigurieren Sie im Azure-Portal auf der Seite **Authentifizierung/Autorisierung** alle Identitätsanbieter, die Sie aktivieren möchten.

Wählen Sie für **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Anonyme Anforderungen zulassen (keine Aktion)** aus.

Fügen Sie auf der Anmeldeseite, der Navigationsleiste oder an einer anderen Stelle in Ihrer App einen Anmeldelink für alle Anbieter hinzu, die Sie aktiviert haben (`/.auth/login/<provider>`). Beispiel:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Wenn der Benutzer auf einen der Links klickt, wird die entsprechende Anmeldeseite geöffnet, um den Benutzer anzumelden.

Um den Benutzer nach der Anmeldung auf eine benutzerdefinierte URL umzuleiten, verwenden Sie den `post_login_redirect_uri`-Abfragezeichenfolgen-Parameter (nicht zu verwechseln mit der Umleitungs-URI in der Konfiguration Ihres Identitätsanbieters). Zur Umleitung des Benutzers auf `/Home/Index` nach der Anmeldung verwenden Sie zum Beispiel den folgenden HTML-Code:

```html
<a href="/.auth/login/<provider>?post_login_redirect_uri=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>Clientgesteuerte Anmeldung

Bei einer clientgesteuerten Anmeldung meldet die Anwendung den Benutzer mithilfe eines anbieterspezifischen SDK beim Identitätsanbieter an. Der Anwendungscode übermittelt dann das resultierende Authentifizierungstoken mittels einer HTTP POST-Anforderung zur Überprüfung an den App Service (siehe [Authentifizierungsflow](overview-authentication-authorization.md#authentication-flow)). Die [Azure Mobile Apps SDKs](https://github.com/Azure/azure-mobile-apps) verwenden diesen Anmeldeflow. Diese Überprüfung allein gewährt Ihnen noch keinen Zugriff auf die gewünschten App-Ressourcen. Bei erfolgreicher Überprüfung erhalten Sie jedoch ein Sitzungstoken, das Sie für den Zugriff auf App-Ressourcen verwenden können.

Um das Anbietertoken zu überprüfen, muss die App Service-App zunächst mit dem gewünschten Anbieter konfiguriert werden. Zur Laufzeit, nachdem Sie das Authentifizierungstoken von Ihrem Anbieter abgerufen haben, posten Sie das Token zur Überprüfung unter `/.auth/login/<provider>`. Beispiel:

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Das Tokenformat kann je nach Anbieter leicht variieren. Details finden Sie in der folgenden Tabelle:

| Anbieterwert | Im Anforderungstext erforderlich | Kommentare |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | Die Eigenschaften `id_token`, `refresh_token` und `expires_in` sind optional. |
| `microsoftaccount` | `{"access_token":"<access_token>"}` oder `{"authentication_token": "<token>"`| `authentication_token` wird gegenüber `access_token` bevorzugt. Die `expires_in`-Eigenschaft ist optional. <br/> Fordern Sie beim Anfordern des Tokens von Livediensten immer den Bereich `wl.basic` an. |
| `google` | `{"id_token":"<id_token>"}` | Die `authorization_code`-Eigenschaft ist optional. Wenn Sie einen `authorization_code`-Wert bereitstellen, werden dem Tokenspeicher ein Zugriffstoken und ein Aktualisierungstoken hinzugefügt. Wenn `authorization_code` angegeben wird, kann er optional auch zusammen mit der Eigenschaft `redirect_uri` verwendet werden. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Verwenden Sie ein gültiges [Benutzerzugriffstoken](https://developers.facebook.com/docs/facebook-login/access-tokens) aus Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Wenn das Anbietertoken erfolgreich überprüft wird, gibt die API im Antworttext ein `authenticationToken` zurück. Dies ist Ihr Sitzungstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Sobald Sie dieses Sitzungstoken erhalten haben, können Sie auf geschützte App-Ressourcen zugreifen, indem Sie Ihren HTTP-Anforderungen den Header `X-ZUMO-AUTH` hinzufügen. Beispiel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Abmelden von einer Sitzung

Benutzer können eine Abmeldung initiieren, indem Sie eine `GET`-Anforderung an den Endpunkt `/.auth/logout` der App senden. Die `GET`-Anforderung bewirkt Folgendes:

- Löscht Authentifizierungscookies aus der aktuellen Sitzung
- Löscht die Token des aktuellen Benutzers aus dem Tokenspeicher
- Führt für Azure Active Directory und Google eine serverseitige Abmeldung für den Identitätsanbieter aus

Hier ist ein einfacher Abmeldungslink auf einer Webseite:

```html
<a href="/.auth/logout">Sign out</a>
```

Standardmäßig wird bei einer erfolgreichen Abmeldung der Client an die URL `/.auth/logout/done` weitergeleitet. Sie können die Weiterleitungsseite nach der Abmeldung ändern, indem Sie den Abfrageparameter `post_logout_redirect_uri` hinzufügen. Beispiel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Es wird empfohlen, den Wert von `post_logout_redirect_uri` zu [codieren](https://wikipedia.org/wiki/Percent-encoding).

Wenn Sie vollqualifizierte URLs verwenden, muss die URL in derselben Domäne gehostet oder als zulässige externe Weiterleitungs-URL für Ihre App konfiguriert werden. Im folgenden Beispiel erfolgt einer Weiterleitung an die URL `https://myexternalurl.com`, die nicht in derselben Domäne gehostet wird:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Führen Sie den nachstehenden Befehl in der [Azure Cloud Shell](../cloud-shell/quickstart.md) aus:

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Beibehalten von URL-Fragmenten

Nachdem sich Benutzer bei Ihrer App anmelden, möchten sie in der Regel zum selben Abschnitt aus derselben Seite weitergeleitet werden, z.B. `/wiki/Main_Page#SectionZ`. Da [URL-Fragmente](https://wikipedia.org/wiki/Fragment_identifier) (etwa `#SectionZ`) jedoch nie an den Server gesendet werden, werden sie standardmäßig nach Abschluss der OAuth-Anmeldung und der Weiterleitung zurück zu Ihrer App nicht beibehalten. Benutzer erhalten dadurch ein suboptimales Erlebnis, wenn sie wieder zum gewünschten Anker navigieren müssen. Diese Einschränkung betrifft alle serverseitigen Authentifizierungslösungen.

Bei der App Service-Authentifizierung können Sie die URL-Fragmente während der OAuth-Anmeldung beibehalten. Legen Sie dazu die App-Einstellung `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` auf `true` fest. Sie können dies im [Azure-Portal](https://portal.azure.com) erledigen oder einfach den folgenden Befehl in der [Azure Cloud Shell](../cloud-shell/quickstart.md) ausführen:

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="limit-the-domain-of-sign-in-accounts"></a>Beschränken der Domäne von Anmeldekonten

Die Anmeldung über mehrere Domänen ist sowohl bei Microsoft-Konten als auch bei Azure Active Directory möglich. Microsoft-Konten lassen beispielsweise _outlook.com_-, _live.com_- und _hotmail.com_-Konten zu. Azure AD lässt eine beliebige Anzahl von benutzerdefinierten Domänen für die Anmeldekonten zu. Möglicherweise möchten Sie aber, dass Ihre Benutzer möglichst schnell direkt zu Ihrer eigenen Azure AD-Anmeldeseite (etwa `contoso.com`) gelangen. Gehen Sie folgendermaßen vor, um den Domänennamen der Anmeldekonten vorzuschlagen.

Navigieren Sie in [https://resources.azure.com](https://resources.azure.com) zu **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings**. 

Klicken Sie auf **Bearbeiten**, ändern Sie die folgende Eigenschaft, und klicken Sie dann auf **Put**. Achten Sie darauf, _\<domain\_name>_ durch die gewünschte Domäne zu ersetzen.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Diese Einstellung fügt den `domain_hint`-Abfragezeichenfolgen-Parameter an die Umleitungs-URL der Anmeldung an. 

> [!IMPORTANT]
> Der Client kann den `domain_hint`-Parameter nach dem Empfang der Umleitungs-URL entfernen und sich dann mit einer anderen Domäne anmelden. Diese Funktion ist zwar komfortabel, unter Sicherheitsaspekten aber bedenklich.
>

## <a name="authorize-or-deny-users"></a>Autorisieren oder Ablehnen von Benutzern

Während App Service den einfachsten Autorisierungsfall erledigt (also nicht authentifizierte Anforderungen ablehnt), erfordert Ihre App möglicherweise ein differenzierteres Autorisierungsverhalten, beispielsweise Einschränken des Zugriffs, sodass nur eine bestimmte Benutzergruppe Zugriff hat. Für bestimmte Fälle müssen Sie benutzerdefinierten Anwendungscode schreiben, um den Zugriff für einen angemeldeten Benutzer zuzulassen oder abzulehnen. In anderen Fällen kann App Service oder Ihr Identitätsanbieter möglicherweise unterstützen, ohne dass Codeänderungen erforderlich sind.

- [Serverebene](#server-level-windows-apps-only)
- [Identitätsanbieterebene](#identity-provider-level)
- [Anwendungsebene](#application-level)

### <a name="server-level-windows-apps-only"></a>Serverebene (nur Windows-Apps)

Für jede Windows-App können Sie das Autorisierungsverhalten des IIS-Webservers definieren, indem Sie die Datei *Web.config* bearbeiten. Linux-Apps verwenden IIS nicht und können nicht über *Web.config* konfiguriert werden.

1. Navigieren Sie zu `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. Navigieren Sie im Browser-Explorer Ihrer App Service-Dateien zu *site/wwwroot*. Wenn keine *Web.config*-Datei vorhanden ist, erstellen Sie diese, indem Sie **+**  > **Neue Datei** auswählen. 

1. Wählen Sie das Freihandwerkzeug für *Web.config.* aus, um die Datei zu bearbeiten. Fügen Sie den folgenden Konfigurationscode hinzu, und klicken Sie auf **Speichern**. Wenn *Web.config* bereits vorhanden ist, fügen Sie einfach das `<authorization>`-Element mit allen zugehörigen Werten hinzu. Fügen Sie die Konten, die Sie zulassen möchten, im `<allow>`-Element hinzu.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Identitätsanbieterebene

Der Identitätsanbieter kann eine bestimmte fertige Autorisierung bereitstellen. Beispiel:

- Für [Azure App Service](configure-authentication-provider-aad.md) können Sie das [Verwalten von Zugriff auf Unternehmensebene](../active-directory/manage-apps/what-is-access-management.md) direkt in Azure AD vornehmen. Anweisungen hierzu finden Sie unter [Aufheben des Zugriffs eines Benutzers auf eine Anwendung](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Für [Google](configure-authentication-provider-google.md) können Google-API-Projekte, die zu einer [Organisation](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) gehören, so konfiguriert werden, dass sie Zugriff nur für Benutzer aus Ihrer Organisation zulassen (weitere Informationen finden Sie auf der [Unterstützungsseite **Setting up OAuth 2.0** von Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Anwendungsschicht

Wenn keine der anderen Ebenen die erforderliche Autorisierung bereitstellt, oder wenn Ihre Plattform oder Ihr Identitätsanbieter nicht unterstützt wird, müssen Sie benutzerdefinierten Code schreiben, um Benutzer anhand der [Benutzeransprüche](configure-authentication-user-identities.md) zu autorisieren.

## <a name="more-resources"></a>Weitere Ressourcen

- [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
- [Umgebungsvariablen und App-Einstellungen für Authentifizierung](reference-app-settings.md#authentication--authorization)
