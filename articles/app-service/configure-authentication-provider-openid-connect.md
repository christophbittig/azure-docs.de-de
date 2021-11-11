---
title: Konfigurieren eines OpenID Connect-Anbieters (Vorschau)
description: Erfahren Sie, wie Sie einen OpenID Connect-Anbieter als Identitätsanbieter für Ihre App Services- oder Azure Functions-App konfigurieren.
ms.topic: article
ms.date: 10/20/2021
ms.reviewer: mahender
ms.openlocfilehash: 0536d28ec91ada939a1ee0b3b7ecf6bf5e621a7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236498"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Konfigurieren Ihrer App Service- oder Azure Functions-App für die Anmeldung über einen OpenID Connect-Anbieter (Vorschau)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Artikel wird gezeigt, wie Sie Azure App Service oder Azure Functions für die Verwendung eines benutzerdefinierten Authentifizierungsanbieters konfigurieren, der der [OpenID Connect-Spezifikation](https://openid.net/connect/) entspricht. Bei OpenID Connect (OIDC) handelt es sich um einen von vielen Identitätsanbietern (IdPs) verwendeten Industriestandard. Sie müssen die Spezifikation nicht im Detail verstehen, um Ihre App für die Verwendung eines entsprechenden IdP zu konfigurieren.

Ihre App kann für die Verwendung eines oder mehrerer OIDC-Anbieter konfiguriert werden. Jedem Anbieter muss in der Konfiguration ein eindeutiger alphanumerischer Name zugewiesen werden, und nur einer kann als Standardziel für die Umleitung dienen.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrieren Ihrer Anwendung beim Identitätsanbieter

Neben Ihrer Anwendung müssen gemäß Anbieter auch die zugehörigen Details registriert werden. Einer dieser Schritte umfasst die Angabe eines Umleitungs-URI. Dieser Umleitungs-URI hat die Form `<app-url>/.auth/login/<provider-name>/callback`. Jeder Identitätsanbieter sollte weitere Anweisungen zum Ausführen dieser Schritte bereitstellen.

> [!NOTE]
> Für einige Anbieter sind möglicherweise zusätzliche Schritte für die Konfiguration und die Verwendung der von ihnen bereitgestellten Werte erforderlich. Apple stellt beispielsweise einen privaten Schlüssel bereit, der nicht selbst als OIDC-Clientgeheimnis verwendet wird. Stattdessen müssen Sie damit ein JWT erstellen, das Sie als Geheimnis in der App-Konfiguration bereitstellen. Weitere Informationen hierzu finden Sie im Abschnitt „Creating the Client Secret“ (Erstellen des Clientgeheimnisses) in der [Dokumentation zu „Mit Apple anmelden“](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens).
>

Sie müssen eine **Client-ID** und ein **Clientgeheimnis** für Ihre Anwendung erfassen.

> [!IMPORTANT]
> Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.
>

Außerdem benötigen Sie die OpenID Connect-Metadaten für den Anbieter. Diese werden häufig über ein [Konfigurationsmetadatendokument](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) zur Verfügung gestellt, bei dem es sich um die Aussteller-URL des Anbieters mit dem Suffix `/.well-known/openid-configuration` handelt. Erfassen Sie diese Konfigurations-URL.

Wenn Sie kein Konfigurationsmetadatendokument verwenden können, müssen Sie die folgenden Werte separat erfassen:

- Die Aussteller-URL (manchmal als `issuer` angezeigt)
- Den [OAuth 2.0-Autorisierungsendpunkt](https://tools.ietf.org/html/rfc6749#section-3.1) (manchmal als `authorization_endpoint` angezeigt)
- Den [OAuth 2.0-Tokenendpunkt](https://tools.ietf.org/html/rfc6749#section-3.2) (manchmal als `token_endpoint` angezeigt)
- Die URL des Dokuments mit dem [OAuth 2.0 JSON Web Key Set](https://tools.ietf.org/html/rfc8414#section-2) (manchmal als `jwks_uri` angezeigt)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Hinzufügen von Anbieterinformationen zu Ihrer Anwendung

1. Melden Sie sich beim [Azure-Portal] an, und navigieren Sie zu Ihrer App.
1. Wählen Sie **Authentifizierung** im Menü auf der linken Seite. Klicken Sie auf **Identitätsanbieter hinzufügen**.
1. Wählen Sie im Dropdownfeld „Identitätsanbieter“ **OpenID Connect** aus.
1. Geben Sie den eindeutigen alphanumerischen Namen an, den Sie zuvor für den **OpenID-Anbieternamen** ausgewählt haben.
1. Wenn Sie über die URL für das **Metadatendokument** vom Identitätsanbieter verfügen, geben Sie diesen Wert als **Metadaten-URL** an. Wählen Sie andernfalls die Option **Endpunkte separat bereitstellen** aus, und legen Sie jede vom Identitätsanbieter erhaltene URL in das entsprechende Feld ein.
1. Geben Sie die zuvor erfasste **Client-ID** und den **Geheimen Clientschlüssel** in den entsprechenden Feldern an.
1. Geben Sie einen Anwendungseinstellungsnamen für Ihren geheimen Clientschlüssel an. Ihr geheimer Clientschlüssel wird als App-Einstellung gespeichert, um sicherzustellen, dass Geheimnisse auf sichere Weise gespeichert werden. Sie können diese Einstellung später aktualisieren, um [Key Vault Verweise](./app-service-key-vault-references.md) zu verwenden, wenn Sie den geheimen Schlüssel in Azure Key Vault verwalten möchten.
1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um die Einrichtung des Identitätsanbieters fertig zu stellen. 

## <a name="next-steps"></a><a name="related-content"> </a>Nächste Schritte

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
