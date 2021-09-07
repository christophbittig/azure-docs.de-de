---
title: Benutzerdefinierte Authentifizierung in Azure Static Web Apps
description: Erfahren Sie, wie Sie benutzerdefinierte Authentifizierung für Azure Static Web Apps konfigurieren.
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: b09d1f6d6cdd5838f4c43e7cb05f63d8efd3e7f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354744"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Benutzerdefinierte Authentifizierung in Azure Static Web Apps

Azure Static Web Apps bietet [verwaltete Authentifizierung](authentication-authorization.md), die von Azure verwaltete Anbieterregistrierungen verwendet. Um mehr Flexibilität bei der Registrierung zu ermöglichen, können Sie die Standardwerte mit einer benutzerdefinierten Registrierung überschreiben.

- Mit der benutzerdefinierten Authentifizierung können Sie auch [benutzerdefinierte Anbieter konfigurieren](#configure-a-custom-openid-connect-provider), die [OpenID Connect](https://openid.net/connect/) unterstützen. Diese Konfiguration ermöglicht die Registrierung mehrerer externer Anbieter.

- Wenn Sie benutzerdefinierte Registrierungen verwenden, werden alle vorkonfigurierten Anbieter deaktiviert.

- Insbesondere bei AAD-Registrierungen (Azure Active Directory) haben Sie die Möglichkeit, einen Mandanten bereitzustellen, mit dem Sie den [Einladungsflow](./authentication-authorization.md#role-management) für die Gruppenverwaltung umgehen können.

> [!NOTE]
> Die benutzerdefinierte Authentifizierung ist nur im Azure Static Web Apps-Standardplan verfügbar.

## <a name="override-pre-configured-provider"></a>Überschreiben vorkonfigurierter Anbieter

Die Einstellungen, die zum Überschreiben eines Anbieters verwendet werden, werden im Abschnitt `auth` der [Konfigurationsdatei](configuration.md) konfiguriert.

Um zu vermeiden, dass Geheimnisse in die Quellcodeverwaltung aufgenommen werden, sucht die Konfiguration in den [Anwendungseinstellungen](application-settings.md) nach einem übereinstimmenden Namen in der Konfigurationsdatei. Sie können ihre Geheimnisse auch in [Azure Key Vault](./key-vault-secrets.md) speichern.

### <a name="configuration"></a>Konfiguration

Zum Einrichten der benutzerdefinierten Authentifizierung müssen Sie auf einige Geheimnisse verweisen, die als [Anwendungseinstellungen](./application-settings.md) gespeichert sind. 

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Azure Active Directory-Anbieter sind in zwei verschiedenen Versionen verfügbar. Version 1 definiert `userDetailsClaim` explizit, wodurch in den Nutzdaten Benutzerinformationen zurückgegeben werden können. Im Gegensatz dazu gibt Version 2 standardmäßig Benutzerinformationen zurück und wird durch `v2.0` in der `openIdIssuer`-URL festgelegt.

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `AAD_CLIENT_ID` | Die Anwendungs-ID (Client) für Ihre Azure AD-App-Registrierung. |
| `AAD_CLIENT_SECRET` | Der geheime Clientschlüssel für die Azure AD-App-Registrierung. |

#### <a name="azure-active-directory-version-1"></a>Azure Active Directory-Version 1

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

#### <a name="azure-active-directory-version-2"></a>Azure Active Directory-Version 2

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

Weitere Informationen zum Konfigurieren von Azure Active Directory finden Sie in der [Dokumentation zur App Service-Authentifizierung/-Autorisierung](../app-service/configure-authentication-provider-aad.md).

> [!NOTE]
> Während der Konfigurationsabschnitt für Azure Active Directory `azureActiveDirectory` lautet, verwendet die Plattform den Alias `aad` in den URLs für Anmeldung, Abmeldung und zur Bereinigung von Benutzerinformationen. Weitere Informationen finden Sie im Abschnitt [Authentifizierung und Autorisierung](authentication-authorization.md).

# <a name="apple"></a>[Apple](#tab/apple)

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `APPLE_CLIENT_ID` | Die ID des Apple-Clients. |
| `APPLE_CLIENT_SECRET` | Der geheime Apple-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter zu konfigurieren.

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

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `FACEBOOK_APP_ID` | Die ID der Facebook-Anwendung. |
| `FACEBOOK_APP_SECRET` | Das Facebook-Anwendungsgeheimnis. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter zu konfigurieren.

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


Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `GITHUB_CLIENT_ID` | Die ID des GitHub-Clients. |
| `GITHUB_CLIENT_SECRET` | Der geheime GitHub-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter zu konfigurieren.

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


Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Die ID des Google-Clients. |
| `GOOGLE_CLIENT_SECRET` | Der geheime Google-Clientschlüssel. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter zu konfigurieren.

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

Um die Registrierung zu erstellen, müssen Sie zunächst die folgenden Anwendungseinstellungen vornehmen:

| Einstellungsname | Wert |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Der Twitter-Consumerschlüssel. |
| `TWITTER_CONSUMER_SECRET` | Das Twitter-Consumergeheimnis. |

Verwenden Sie als Nächstes das folgende Beispiel, um den Anbieter zu konfigurieren.

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

---

## <a name="configure-a-custom-openid-connect-provider"></a>Konfigurieren eines benutzerdefinierten OpenID Connect-Anbieters

In diesem Abschnitt erfahren Sie, wie Sie Azure Static Web Apps für die Verwendung eines benutzerdefinierten Authentifizierungsanbieters konfigurieren, der die [OpenID Connect-Spezifikation (OIDC)](https://openid.net/connect/) einhält. Die folgenden Schritte sind erforderlich, um einen benutzerdefinierten OIDC-Anbieter zu verwenden.

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

### <a name="login-logout-and-purging-user-details"></a>Anmelden, Abmelden und Bereinigen von Benutzerdetails

Um einen benutzerdefinierten OIDC-Anbieter zu verwenden, verwenden Sie die folgenden URL-Muster.

| Aktion             | Muster                                  |
| ------------------ | ---------------------------------------- |
| Anmelden              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Bereinigen von Benutzerdetails | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Wenn Sie Azure Active Directory verwenden, geben Sie `aad` als Wert für den `<AUTHENTICATION_PROVIDER_NAME>`-Platzhalter an.

### <a name="authentication-callbacks"></a>Authentifizierungsrückrufe

Benutzerdefinierte OIDC-Anbieter benötigen zur Ausführung von Anmeldungs- oder Abmeldungsanforderungen eine Umleitungs-URL. Die folgenden Endpunkte sind als Umleitungsziele verfügbar.

| Typ   | URL-Muster                                                 |
| ------ | ----------------------------------------------------------- |
| Anmelden  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Wenn Sie Azure Active Directory verwenden, geben Sie `aad` als Wert für den `<AUTHENTICATION_PROVIDER_NAME>`-Platzhalter an.

> [!Note]
> Diese URLs werden von Azure Static Web Apps bereitgestellt, um die Antwort vom Authentifizierungsanbieter zu empfangen. Sie müssen keine Seiten auf diesen Routen erstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen von Authentifizierungsgeheimnissen in Azure Key Vault](./key-vault-secrets.md)
