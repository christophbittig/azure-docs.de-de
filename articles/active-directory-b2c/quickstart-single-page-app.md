---
title: 'Schnellstart: Einrichten der Anmeldung für eine Single-Page-App (SPA)'
titleSuffix: Azure AD B2C
description: In dieser Schnellstartanleitung führen Sie eine Single-Page-Anwendung aus, bei der die Kontoanmeldung über Azure Active Directory B2C erfolgt.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a4ab049f739ff1ec940234cb6559c88547a47b20
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036681"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Schnellstart: Einrichten der Anmeldung für eine Single-Page-App mit Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ermöglicht die Cloudidentitätsverwaltung zum Schützen Ihrer Anwendung, Ihres Unternehmens und Ihrer Kunden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke und bei Unternehmenskonten authentifizieren. 

In dieser Schnellstartanleitung verwenden Sie eine Single-Page-Anwendung, um sich unter Verwendung eines sozialen Netzwerks als Identitätsanbieter anzumelden und eine per Azure AD B2C geschützte Web-API aufzurufen.

<!--[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] -->

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Ein Social Media-Konto von Facebook, Google oder Microsoft
- Codebeispiel aus GitHub: [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa):

    Sie können [das ZIP-Archiv herunterladen](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) oder das Repository klonen.

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

1. Starten Sie den Server, indem Sie die folgenden Befehle über die Node.js-Eingabeaufforderung ausführen:

    ```console
    npm install
    npm update
    npm start
    ```

    Der von *server.js* gestartete Server zeigt den Port an, an dem gelauscht wird:

    ```console
    Listening on port 6420...
    ```

1. Navigieren Sie zur URL der Anwendung. Beispiel: `http://localhost:6420`.

    ![Beispiel-App für Singe-Page-Anwendung, die im Browser angezeigt wird](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Anmelden mit Ihrem Konto

1. Wählen Sie **Anmelden** aus, um die User Journey zu starten.
1. Azure AD B2C stellt für ein fiktives Unternehmen namens „Fabrikam“ eine Anmeldeseite für die Beispielwebanwendung bereit. Um sich mit einem Social Media-Konto als Identitätsanbieter zu registrieren, wählen Sie die Schaltfläche des Identitätsanbieters aus, den Sie verwenden möchten.

    ![Anmelde-oder Registrierungsseite mit Schaltflächen für die Auswahl von Identitätsanbietern](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Sie authentifizieren sich mit den Anmeldeinformationen Ihres Social Media-Kontos und autorisieren die Anwendung dazu, Informationen aus Ihrem Social Media-Konto zu lesen. Wenn Sie der Anwendung Zugriff auf diese gewähren, kann die Anwendung Profilinformationen aus dem Social Media-Konto abrufen, z.B. Ihren Namen und Ihre Stadt.

1. Schließen Sie den Anmeldevorgang für den Identitätsanbieter ab.

## <a name="access-a-protected-api-resource"></a>Zugreifen auf eine geschützte API-Ressource

Wählen Sie **API aufrufen** aus, damit die Web-API Ihren Anzeigenamen als JSON-Objekt zurückgibt.

![Beispielanwendung im Browser mit der Antwort der Web-API](./media/quickstart-single-page-app/call-api-spa.png)

Die Single-Page-Beispielanwendung enthält ein Zugriffstoken in der Anforderung, die an die geschützte Web-API-Ressource gesendet wird.

<!-- ## Clean up resources

You can use your Azure AD B2C tenant if you plan to try other Azure AD B2C quickstarts or tutorials. When no longer needed, you can [delete your Azure AD B2C tenant](faq.yml#how-do-i-delete-my-azure-ad-b2c-tenant-).-->

## <a name="next-steps"></a>Nächste Schritte

<!---In this quickstart, you used a sample single-page application to:

- Sign in with a social identity provider
- Create an Azure AD B2C user account (created automatically at sign-in)
- Call a web API protected by Azure AD B2C -->

- Erste Schritte beim Erstellen eines eigenen [Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
