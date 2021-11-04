---
title: Benutzerdefinierte Authentifizierung in Azure Static Web Apps
description: Erfahren Sie, wie Sie benutzerdefinierte Authentifizierung für Azure Static Web Apps konfigurieren.
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 8b7fe1fc5c291f02abff88cb3e5ace2607cf32f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428138"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Benutzerdefinierte Authentifizierung in Azure Static Web Apps

Azure Static Web Apps bietet [verwaltete Authentifizierung](authentication-authorization.md), die von Azure verwaltete Anbieterregistrierungen verwendet. Um mehr Flexibilität bei der Registrierung zu ermöglichen, können Sie die Standardwerte mit einer benutzerdefinierten Registrierung überschreiben.

- Mit der benutzerdefinierten Authentifizierung können Sie auch [benutzerdefinierte Anbieter konfigurieren](./authentication-custom.md?tabs=openid-connect#configure-a-custom-identity-provider), die [OpenID Connect](https://openid.net/connect/) unterstützen. Diese Konfiguration ermöglicht die Registrierung mehrerer externer Anbieter.

- Wenn Sie benutzerdefinierte Registrierungen verwenden, werden alle vorkonfigurierten Anbieter deaktiviert.

> [!NOTE]
> Die benutzerdefinierte Authentifizierung ist nur im Azure Static Web Apps-Standardplan verfügbar.

## <a name="configure-a-custom-identity-provider"></a>Konfigurieren eines benutzerdefinierten Identitätsanbieters

Benutzerdefinierte Identitätsanbieter werden im Abschnitt `auth` der [Konfigurationsdatei](configuration.md) konfiguriert.

Um zu vermeiden, dass Geheimnisse in die Quellcodeverwaltung aufgenommen werden, sucht die Konfiguration in den [Anwendungseinstellungen](application-settings.md#configure-application-settings) nach einem übereinstimmenden Namen in der Konfigurationsdatei. Sie können ihre Geheimnisse auch in [Azure Key Vault](./key-vault-secrets.md) speichern.

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md#configure-application-settings) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `AAD_CLIENT_ID` | Die Anwendungs-ID (Client) für Ihre Azure AD-App-Registrierung. |
| `AAD_CLIENT_SECRET` | Der geheime Clientschlüssel für die Azure AD-App-Registrierung. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

Azure Active Directory-Anbieter sind in zwei verschiedenen Versionen verfügbar. Version 1 definiert `userDetailsClaim` explizit, wodurch in den Nutzdaten Benutzerinformationen zurückgegeben werden können. Im Gegensatz dazu gibt Version 2 standardmäßig Benutzerinformationen zurück und wird durch `v2.0` in der `openIdIssuer`-URL festgelegt.

### <a name="azure-active-directory-version-1"></a>Azure Active Directory-Version 1

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Stellen Sie sicher, `<TENANT_ID>` durch die ID Ihres Azure Active Directory-Mandanten zu ersetzen.

### <a name="azure-active-directory-version-2"></a>Azure Active Directory-Version 2

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>/v2.0",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Stellen Sie sicher, `<TENANT_ID>` durch die ID Ihres Azure Active Directory-Mandanten zu ersetzen.

Weitere Informationen zum Konfigurieren von Azure Active Directory finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-aad.md#-option-2-use-an-existing-registration-created-separately) zur Verwendung einer vorhandenen Registrierung.

> [!NOTE]
> Während der Konfigurationsabschnitt für Azure Active Directory `azureActiveDirectory` lautet, verwendet die Plattform den Alias `aad` in den URLs für Anmeldung, Abmeldung und zur Bereinigung von Benutzerinformationen. Weitere Informationen finden Sie im Abschnitt [Authentifizierung und Autorisierung](authentication-authorization.md).

# <a name="apple"></a>[Apple](#tab/apple)

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `APPLE_CLIENT_ID` | Die ID des Apple-Clients. |
| `APPLE_CLIENT_SECRET` | Der geheime Apple-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "APPLE_CLIENT_ID",
          "clientSecretSettingName": "APPLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Weitere Informationen zum Konfigurieren von Apple als Authentifizierungsanbieter finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-apple.md).

# <a name="facebook"></a>[Facebook](#tab/facebook)

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `FACEBOOK_APP_ID` | Die ID der Facebook-Anwendung. |
| `FACEBOOK_APP_SECRET` | Das Facebook-Anwendungsgeheimnis. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "FACEBOOK_APP_ID",
          "appSecretSettingName": "FACEBOOK_APP_SECRET"
        }
      }
    }
  }
}
```

Weitere Informationen zum Konfigurieren von Facebook als Authentifizierungsanbieter finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung.](../app-service/configure-authentication-provider-facebook.md)

# <a name="github"></a>[GitHub](#tab/github)


Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `GITHUB_CLIENT_ID` | Die ID des GitHub-Clients. |
| `GITHUB_CLIENT_SECRET` | Der geheime GitHub-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "GITHUB_CLIENT_ID",
          "clientSecretSettingName": "GITHUB_CLIENT_SECRET"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)


Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Die ID des Google-Clients. |
| `GOOGLE_CLIENT_SECRET` | Der geheime Google-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "GOOGLE_CLIENT_ID",
          "clientSecretSettingName": "GOOGLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Weitere Informationen zum Konfigurieren von Google als Authentifizierungsanbieter finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-google.md).

# <a name="twitter"></a>[Twitter](#tab/twitter)

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden [Anwendungseinstellungen](application-settings.md) vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Der Twitter-Consumerschlüssel. |
| `TWITTER_CONSUMER_SECRET` | Das Twitter-Consumergeheimnis. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter in der [Konfigurationsdatei](configuration.md) zu konfigurieren.

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "TWITTER_CONSUMER_KEY",
          "consumerSecretSettingName": "TWITTER_CONSUMER_SECRET"
        }
      }
    }
  }
}
```

Weitere Informationen zum Konfigurieren von Twitter als Authentifizierungsanbieter finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-twitter.md).

# <a name="openid-connect"></a>[OpenID Connect](#tab/openid-connect)

Sie können Azure Static Web Apps für die Verwendung eines benutzerdefinierten Authentifizierungsanbieters konfigurieren, der die [OpenID Connect-Spezifikation (OIDC)](https://openid.net/connect/) einhält. Die folgenden Schritte sind erforderlich, um einen benutzerdefinierten OIDC-Anbieter zu verwenden.

- Ein oder mehrere OIDC-Anbieter sind zulässig.
- Jeder Anbieter muss über einen eindeutigen Namen in der Konfiguration verfügen.
- Nur ein Anbieter kann als Standardumleitungsziel dienen.

### <a name="register-your-application-with-the-identity-provider"></a>Registrieren Ihrer Anwendung beim Identitätsanbieter

Sie müssen die Details Ihrer Anwendung bei einem Identitätsanbieter registrieren. Fragen Sie den Anbieter nach den Schritten, die zum Generieren einer **Client-ID** und eines **geheimen Clientschlüssels** für Ihre Anwendung erforderlich sind.

Nachdem die Anwendung beim Identitätsanbieter registriert wurde, erstellen Sie die folgenden Anwendungsgeheimnisse in den [Anwendungseinstellungen](application-settings.md) der statischen Web-App:

| Einstellungsname | Wert |
| --- | --- |
| `MY_PROVIDER_CLIENT_ID` | Die Client-ID, die vom Authentifizierungsanbieter für Ihre statische Web-App generiert wurde. |
| `MY_PROVIDER_CLIENT_SECRET` | Der geheime Clientschlüssel, der vom Authentifizierungsanbieter für Ihre statische Web-App generiert wurde. |

Wenn Sie zusätzliche Anbieter registrieren, benötigt jeder Anbieter eine zugeordnete Client-ID und einen Speicher für geheime Clientschlüssel in den Anwendungseinstellungen.

> [!IMPORTANT]
> Anwendungsgeheimnisse sind vertrauliche Sicherheitsanmeldeinformationen. Teilen Sie dieses Geheimnis mit niemandem, verteilen Sie es in keiner Clientanwendung, und checken Sie es nicht in die Quellcodeverwaltung ein.

Sobald Sie über die Anmeldeinformationen für die Registrierung verfügen, erstellen Sie mithilfe der folgenden Schritte eine benutzerdefinierte Registrierung.

1. Sie benötigen die OpenID Connect-Metadaten für den Anbieter. Diese Informationen werden häufig über ein [Konfigurationsmetadatendokument](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) zur Verfügung gestellt, bei dem es sich um die _Aussteller-URL_ des Anbieters mit dem Suffix `/.well-known/openid-configuration` handelt. Erfassen Sie diese Konfigurations-URL.

1. Fügen Sie der [Konfigurationsdatei](configuration.md) einen `auth`-Abschnitt mit einem Konfigurationsblock für die OIDC-Anbieter und Ihre Anbieterdefinition hinzu.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "MY_PROVIDER_CLIENT_ID",
               "clientCredential": {
                 "clientSecretSettingName": "MY_PROVIDER_CLIENT_SECRET"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  - Der Anbietername, in diesem Beispiel `myProvider`, ist der eindeutige Bezeichner, der von Azure Static Web Apps verwendet wird.
  - Stellen Sie sicher, `<PROVIDER_ISSUER_URL>` durch den Pfad zur _Aussteller-URL_ des Anbieters zu ersetzen.
  - Mit dem `login`-Objekt können Sie Werte für benutzerdefinierte Bereiche, Anmeldeparameter oder benutzerdefinierte Ansprüche angeben.

---

## <a name="authentication-callbacks"></a>Authentifizierungsrückrufe

Identitätsanbieter benötigen zur Ausführung von Anmeldungs- oder Abmeldungsanforderungen eine Umleitungs-URL. Die meisten Anbieter erfordern, dass Sie die Rückruf-URLs einer Zulassungsliste hinzufügen. Die folgenden Endpunkte sind als Umleitungsziele verfügbar.

| Typ   | URL-Muster                                                 |
| ------ | ----------------------------------------------------------- |
| Anmelden  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Wenn Sie Azure Active Directory verwenden, geben Sie `aad` als Wert für den `<PROVIDER_NAME_IN_CONFIG>`-Platzhalter an.

> [!Note]
> Diese URLs werden von Azure Static Web Apps bereitgestellt, um die Antwort vom Authentifizierungsanbieter zu empfangen. Sie müssen keine Seiten auf diesen Routen erstellen.

## <a name="login-logout-and-purging-user-details"></a>Anmelden, Abmelden und Bereinigen von Benutzerdetails

Um einen benutzerdefinierten Identitätsanbieter zu verwenden, verwenden Sie die folgenden URL-Muster.

| Aktion             | Muster                                  |
| ------------------ | ---------------------------------------- |
| Anmelden              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Bereinigen von Benutzerdetails | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Wenn Sie Azure Active Directory verwenden, geben Sie `aad` als Wert für den `<PROVIDER_NAME_IN_CONFIG>`-Platzhalter an.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen von Authentifizierungsgeheimnissen in Azure Key Vault](./key-vault-secrets.md)
