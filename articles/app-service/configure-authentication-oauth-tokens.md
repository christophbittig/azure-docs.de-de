---
title: OAuth-Token in AuthN/AuthZ
description: Erfahren Sie, wie Sie Token abrufen und aktualisieren sowie Sitzungen erweitern, wenn Sie die integrierte Authentifizierung und Autorisierung in App Service verwenden.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f158f9f76820635a65737b75f3c016ff67a3a92a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352080"
---
# <a name="work-with-oauth-tokens-in-azure-app-service-authentication"></a>Arbeiten mit OAuth-Token bei der Azure App Service-Authentifizierung

In diesem Artikel wird erläutert, wie Sie OAuth-Token einsetzen können, während Sie die integrierte [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) nutzen. 

## <a name="retrieve-tokens-in-app-code"></a>Abrufen von Token in App-Code

Die anbieterspezifischen Token werden aus dem Servercode in den Anforderungsheader eingefügt, sodass Sie problemlos darauf zugreifen können. Die folgende Tabelle zeigt mögliche Namen von Tokenheadern:

| Anbieter | Headernamen |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

> [!NOTE]
> Verschiedene Sprachenframeworks zeigen diese Header im App-Code möglicherweise in verschiedenen Formaten an (klein geschrieben oder mit großen Anfangsbuchstaben).

Senden Sie von Ihrem Clientcode (z. B. einer mobilen App oder JavaScript im Browser) eine HTTP-`GET`-Anforderung an `/.auth/me` (der [Tokenspeicher](overview-authentication-authorization.md#token-store) muss aktiviert sein). Der zurückgegebene JSON-Code enthält die anbieterspezifischen-Token.

> [!NOTE]
> Zugriffstoken sind für den Zugriff auf Anbieterressourcen vorgesehen, daher sind sie nur vorhanden, wenn Sie den Anbieter mit einem geheimen Clientschlüssel konfigurieren. Informationen zum Abrufen von Aktualisierungstoken finden Sie unter „Zugriffstoken für die Aktualisierung“.

## <a name="refresh-auth-tokens"></a>Aktualisieren von Authentifizierungstoken

Wenn das Zugriffstoken Ihres Anbieters (nicht das [Sitzungstoken](#extend-session-token-expiration-grace-period)) abgelaufen ist, müssen Sie den Benutzer erneut authentifizieren, bevor Sie dieses Token erneut verwenden. Sie können den Tokenablauf vermeiden, indem Sie einen `GET`-Aufruf an den `/.auth/refresh`-Endpunkt Ihrer Anwendung durchführen. Bei einem Aufruf aktualisiert App Service automatisch die Zugriffstoken im [Tokenspeicher](overview-authentication-authorization.md#token-store) für den authentifizierten Benutzer. Bei nachfolgenden Anforderungen von Token durch Ihren App-Code werden die aktualisierten Token abgerufen. Damit die Tokenaktualisierung funktioniert, muss der Tokenspeicher jedoch [Aktualisierungstoken](https://auth0.com/learn/refresh-tokens/) für Ihren Anbieter enthalten. Die jeweilige Methode zum Abrufen von Aktualisierungstoken ist von den einzelnen Anbietern dokumentiert, die folgende Liste stellt jedoch eine kurze Zusammenfassung dar:

- **Google**: Fügen Sie einen Abfragezeichenfolgen-Parameter vom Typ `access_type=offline` an Ihren `/.auth/login/google`-API-Aufruf an. Bei Verwendung des Mobile Apps SDK können Sie den Parameter einer der `LogicAsync`-Überladungen hinzufügen (siehe [Google-Aktualisierungstoken](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Stellt keine Aktualisierungstoken bereit. Langlebige Token laufen nach 60 ab (siehe [Verlängern von Zugriffsschlüsseln für Seiten](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Zugriffstoken laufen nicht ab (siehe [Häufig gestellte Fragen zu OAuth für Twitter](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft**: Führen Sie in [https://resources.azure.com](https://resources.azure.com) folgende Schritte aus:
    1. Wählen Sie am oberen Seitenrand die Option **Lesen/Schreiben** aus.
    2. Navigieren Sie im linken Browser zu **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettingsV2**.
    3. Klicken Sie auf **Bearbeiten**.
    4. Ändern Sie die folgende Eigenschaft.

        ```json
        "identityProviders": {
          "azureActiveDirectory": {
            "login": {
              "loginParameters": ["scope=openid profile email offline_access"]
            }
          }
        }
        ```

    5. Klicken Sie auf **Put**.
    
    > [!NOTE]
    > Der Bereich, der Ihnen ein Aktualisierungstoken bietet, ist [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access). Die Vorgehensweise dazu finden Sie in [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md). Die anderen Bereiche werden standardmäßig von App Service angefordert. Informationen zu diesen Standardbereich finden Sie unter [OpenID Connect-Bereiche](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes).

Sobald Ihr Anbieter konfiguriert ist, können Sie im Tokenspeicher [das Aktualisierungstoken und die Ablaufzeit für das Zugriffstoken suchen](#retrieve-tokens-in-app-code). 

Um das Zugriffstoken jederzeit zu aktualisieren, rufen Sie einfach `/.auth/refresh` in einer beliebigen Sprache auf. Im folgenden Codeausschnitt wird jQuery verwendet, um Ihre Zugriffstoken aus einem JavaScript-Client zu aktualisieren.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Wenn ein Benutzer die Ihrer App gewährten Berechtigungen widerruft, schlägt Ihr Aufruf von `/.auth/me` möglicherweise mit der Antwort `403 Forbidden` fehl. Überprüfen Sie Ihre Anwendungsprotokolle auf Details, um Fehler zu diagnostizieren.

## <a name="extend-session-token-expiration-grace-period"></a>Verlängern der Toleranzperiode für das Sitzungstoken

Die authentifizierte Sitzung läuft nach acht Stunden ab. Nachdem eine authentifizierte Sitzung abgelaufen ist, gilt standardmäßig eine Toleranzperiode von 72 Stunden. Innerhalb dieser Toleranzperiode sind Sie berechtigt, das Sitzungstoken mit App Service zu aktualisieren, ohne den Benutzer erneut zu authentifizieren. Rufen Sie einfach `/.auth/refresh` auf, wenn Ihr Sitzungstoken ungültig wird. Sie müssen den Tokenablauf nicht selbst nachverfolgen. Nach Ablauf der 72-stündigen Toleranzperiode muss sich der Benutzer neu anmelden, um ein gültiges Sitzungstoken zu erhalten.

Wenn 72 Stunden für Sie nicht ausreichend sind, können Sie dieses Ablauffenster erweitern. Die Erweiterung des Ablauffensters über einen langen Zeitraum kann sich erheblich auf die Sicherheit auswirken (z.B. wenn ein Authentifizierungstoken kompromittiert oder gestohlen wird). Daher wird empfohlen, den Standardwert von 72 Stunden beizubehalten oder den Ablaufzeitraum auf den kleinsten Wert festzulegen.

Zum Erweitern des standardmäßigen Ablauffensters führen Sie den folgenden Befehl in [Cloud Shell](../cloud-shell/overview.md) aus.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Die Toleranzperiode gilt nur für die authentifizierte App Service-Sitzung, nicht jedoch für die Token von den Identitätsanbietern. Es gibt keine Toleranzperiode für die abgelaufenen Anbietertoken. 
>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
