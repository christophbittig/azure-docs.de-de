---
title: Konfigurieren eines OpenID Connect-Anbieters (Vorschau)
description: Erfahren Sie, wie Sie einen OpenID Connect-Anbieter als Identitätsanbieter für Ihre App Services- oder Azure Functions-App konfigurieren.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: 84a8ce0017b9aac6e2a1c1eb75ef78dff89721a9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355255"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Konfigurieren Ihrer App Service- oder Azure Functions-App für die Anmeldung über einen OpenID Connect-Anbieter (Vorschau)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Artikel wird gezeigt, wie Sie Azure App Service oder Azure Functions für die Verwendung eines benutzerdefinierten Authentifizierungsanbieters konfigurieren, der der [OpenID Connect-Spezifikation](https://openid.net/connect/) entspricht. Bei OpenID Connect (OIDC) handelt es sich um einen von vielen Identitätsanbietern (IdPs) verwendeten Industriestandard. Sie müssen die Spezifikation nicht im Detail verstehen, um Ihre App für die Verwendung eines entsprechenden IdP zu konfigurieren.

Ihre App kann für die Verwendung eines oder mehrerer OIDC-Anbieter konfiguriert werden. Jedem Anbieter muss in der Konfiguration ein eindeutiger Name zugewiesen werden, und nur einer kann als Standardziel für die Umleitung dienen.

> [!CAUTION]
> Durch das Aktivieren eines OpenID Connect-Anbieters wird die Verwaltung des App Service-Authentifizierungs-/-Autorisierungsfeatures für Ihre Anwendung durch einige Clients wie das Azure-Portal, die Azure CLI und Azure PowerShell deaktiviert. Das Feature basiert auf einer neuen API-Oberfläche, die während der Vorschau noch nicht in allen Verwaltungsumgebungen implementiert ist.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrieren Ihrer Anwendung beim Identitätsanbieter

Neben Ihrer Anwendung müssen gemäß Anbieter auch die zugehörigen Details registriert werden. Weitere Informationen finden Sie in den Anweisungen für den jeweiligen Anbieter. Sie müssen eine **Client-ID** und ein **Clientgeheimnis** für Ihre Anwendung erfassen.

> [!IMPORTANT]
> Der App-Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.
>

> [!NOTE]
> Für einige Anbieter sind möglicherweise zusätzliche Schritte für die Konfiguration und die Verwendung der von ihnen bereitgestellten Werte erforderlich. Apple stellt beispielsweise einen privaten Schlüssel bereit, der nicht selbst als OIDC-Clientgeheimnis verwendet wird. Stattdessen müssen Sie damit ein JWT erstellen, das Sie als Geheimnis in der App-Konfiguration bereitstellen. Weitere Informationen hierzu finden Sie im Abschnitt „Creating the Client Secret“ (Erstellen des Clientgeheimnisses) in der [Dokumentation zu „Mit Apple anmelden“](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens).
>

Fügen Sie das Clientgeheimnis als [Anwendungseinstellung](./configure-common.md#configure-app-settings) für die App hinzu, und verwenden Sie dabei einen Einstellungsnamen Ihrer Wahl. Notieren Sie sich diesen Namen zur späteren Verwendung.

Außerdem benötigen Sie die OpenID Connect-Metadaten für den Anbieter. Diese werden häufig über ein [Konfigurationsmetadatendokument](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) zur Verfügung gestellt, bei dem es sich um die Aussteller-URL des Anbieters mit dem Suffix `/.well-known/openid-configuration` handelt. Erfassen Sie diese Konfigurations-URL.

Wenn Sie kein Konfigurationsmetadatendokument verwenden können, müssen Sie die folgenden Werte separat erfassen:

- Die Aussteller-URL (manchmal als `issuer` angezeigt)
- Den [OAuth 2.0-Autorisierungsendpunkt](https://tools.ietf.org/html/rfc6749#section-3.1) (manchmal als `authorization_endpoint` angezeigt)
- Den [OAuth 2.0-Tokenendpunkt](https://tools.ietf.org/html/rfc6749#section-3.2) (manchmal als `token_endpoint` angezeigt)
- Die URL des Dokuments mit dem [OAuth 2.0 JSON Web Key Set](https://tools.ietf.org/html/rfc8414#section-2) (manchmal als `jwks_uri` angezeigt)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Hinzufügen von Anbieterinformationen zu Ihrer Anwendung

> [!NOTE]
> Die erforderliche Konfiguration ist in einem neuen API-Format verfügbar, das derzeit nur durch eine [dateibasierte Konfiguration (Vorschau)](configure-authentication-file-based.md)unterstützt wird. Führen Sie die unten aufgeführten Schritte mithilfe einer solchen Datei aus.

Dieser Abschnitt führt Sie durch die Aktualisierung der Konfiguration für Ihren neuen IdP. Im Folgenden ist eine Beispielkonfiguration aufgeführt.

1. Fügen Sie innerhalb des Objekts `identityProviders` ein Objekt `openIdConnectProviders` hinzu, sofern noch keines vorhanden ist.
1. Fügen Sie innerhalb des Objekts `openIdConnectProviders` einen Schlüssel für den neuen Anbieter hinzu. Dies ist ein Anzeigename, der in der übrigen Konfiguration für Verweise auf den Anbieter verwendet wird. Wenn beispielsweise alle Anforderungen bei diesem Anbieter authentifiziert werden sollen, legen Sie `globalValidation.unauthenticatedClientAction` auf „RedirectToLoginPage“ fest, und stellen Sie `redirectToProvider` auf diesen Anzeigenamen ein.
1. Weisen Sie dem Schlüssel ein Objekt mit einem darin enthaltenen Objekt `registration` und optional ein Objekt `login` zu:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scopes": [],
             "loginParameterNames": [],
       }
    }
    ```

1. Legen Sie im Registrierungsobjekt die `clientId` auf die von Ihnen erfasste Client-ID fest, geben Sie für `clientCredential.secretSettingName` den Namen der Anwendungseinstellung an, in der Sie das Clientgeheimnis gespeichert haben, und erstellen Sie ein Objekt `openIdConnectConfiguration`:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. Geben Sie im Objekt `openIdConnectConfiguration` die OpenID Connect-Metadaten an, die Sie zuvor erfasst haben. Hierfür gibt es je nach den von Ihnen erfassten Informationen zwei Möglichkeiten:

    - Legen Sie die Eigenschaft `wellKnownOpenIdConfiguration` auf die zuvor erfasste Konfigurationsmetadaten-URL fest.
    - Legen Sie alternativ dazu die vier erfassten Einzelwerte wie folgt fest:
        - Legen Sie `issuer` auf die Aussteller-URL fest.
        - Legen Sie `authorizationEndpoint` auf den Autorisierungsendpunkt fest.
        - Legen Sie `tokenEndpoint` auf den Tokenendpunkt fest.
        - Legen Sie `certificationUri` auf die URL des JWKS-Dokuments (JSON Web Key Set) fest.

    Diese beiden Optionen schließen sich gegenseitig aus.

Nachdem diese Konfiguration festgelegt wurde, können Sie Ihren OpenID Connect-Anbieter für die Authentifizierung in Ihrer App verwenden.

Ein Beispiel für eine Konfiguration könnte wie folgt aussehen (bei Verwendung von „Mit Apple anmelden“ als Beispiel, bei dem die Einstellung APPLE_GENERATED_CLIENT_SECRET gemäß [Apple-Dokumentation](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens) auf ein generiertes JWT verweist):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scopes": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Nächste Schritte

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
