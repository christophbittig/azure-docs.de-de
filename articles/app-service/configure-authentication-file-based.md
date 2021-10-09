---
title: Dateibasierte Konfiguration von AuthN/AuthZ
description: Konfigurieren der Authentifizierung und Autorisierung in App Service mithilfe einer Konfigurationsdatei, um bestimmte Vorschaufunktionen zu aktivieren.
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 2875c529e644b46d08251ae57ee9f424492a2433
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657213"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Dateibasierte Konfiguration in Azure App Service-Authentifizierung

Mit der [App Service-Authentifizierung](overview-authentication-authorization.md) lassen sich die Authentifizierungseinstellungen mit einer Datei konfigurieren. Möglicherweise müssen Sie die dateibasierte Konfiguration verwenden, um bestimmte Vorschaufunktionen der App Service Authentifizierung/-Autorisierung zu verwenden, bevor sie über [Azure Resource Manager](../azure-resource-manager/management/overview.md)-APIs verfügbar gemacht werden.

> [!IMPORTANT]
> Denken Sie daran, dass die Nutzlast Ihrer App – und damit auch diese Datei – möglicherweise zwischen Umgebungen verschoben wird, z. B. bei [Slots](./deploy-staging-slots.md). Es ist sehr wahrscheinlich, dass an jeden Slot eine andere App-Registrierung angeheftet werden muss. In diesen Fällen sollten Sie weiterhin die Standardkonfigurationsmethode anstelle der Konfigurationsdatei verwenden.

## <a name="enabling-file-based-configuration"></a>Aktivieren der dateibasierten Konfiguration

1. Erstellen Sie im Stammverzeichnis Ihres Projekts (bereitgestellt in D:\home\site\wwwroot in Ihrer Web-App bzw. Funktions-App) eine neue JSON-Datei für Ihre Konfiguration. Geben Sie die gewünschten Konfigurationsdetails entsprechend der [Referenz zur dateibasierten Konfiguration](#configuration-file-reference) an. Wenn Sie eine vorhandene Azure Resource Manager-Konfiguration ändern, stellen Sie sicher, dass Sie die in der `authsettings`-Sammlung aufgezeichneten Eigenschaften in die Konfigurationsdatei übersetzen.

2. Ändern Sie die vorhandene Konfiguration, die in den [Azure Resource Manager](../azure-resource-manager/management/overview.md)-APIs unter `Microsoft.Web/sites/<siteName>/config/authsettingsV2` aufgezeichnet ist. Hierfür können Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) oder ein Tool wie den [Azure-Ressourcen-Explorer](https://resources.azure.com/) verwenden. In der Sammlung authsettingsV2 müssen Sie zwei Eigenschaften festlegen (und können andere entfernen):

    1. Legen Sie `platform.enabled` auf TRUE fest.
    2. Legen Sie `platform.configFilePath` auf den Namen der Zertifikatdatei fest (z. B. „auth.json“).

> [!NOTE]
> Das Format für `platform.configFilePath` variiert je nach Plattform. Unter Windows werden sowohl relative als auch absolute Pfade unterstützt. Relative Pfade werden empfohlen. Unter Linux werden derzeit nur absolute Pfade unterstützt. Daher sollte der Wert der Einstellung „/home/site/wwwroot/auth.json“ oder ähnlich lauten.

Nachdem Sie dieses Konfigurationsupdate vorgenommen haben, wird der Inhalt der Datei verwendet, um das Verhalten der App Service-Authentifizierung bzw. -Autorisierung für diesen Standort zu definieren. Wenn Sie zur Azure Resource Manager-Konfiguration zurückkehren möchten, können Sie dies erreichen, indem Sie die Festlegung von `platform.configFilePath` auf „Null“ wieder entfernen.

## <a name="configuration-file-reference"></a>Verweis von der Konfigurationsdatei

Alle Geheimnisse, auf die von Ihrer Konfigurationsdatei verwiesen wird, müssen als [Anwendungseinstellungen](./configure-common.md#configure-app-settings) gespeichert werden. Sie können die Einstellungen ganz nach Wunsch benennen. Stellen Sie lediglich sicher, dass die Verweise von der Konfigurationsdatei dieselben Schlüssel verwenden.

Im Folgenden finden Sie mögliche Konfigurationsoptionen in der Datei:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>Weitere Ressourcen

- [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
- [Umgebungsvariablen und App-Einstellungen für Authentifizierung](reference-app-settings.md#authentication--authorization)
