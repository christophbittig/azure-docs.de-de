---
title: 'Tutorial: Registrieren einer Single-Page-Webanwendung'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie eine Single-Page-Webanwendung (Single-Page Application, SPA) in Azure Active Directory B2C mithilfe des Azure-Portals registrieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 82454e41ea9895c8006ac179a7c731a57a0c5c3b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581644"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Tutorial: Registrieren einer Single-Page-Webanwendung (SPA) in Azure Active Directory B2C

Bevor Ihre [Anwendungen](application-types.md) mit Azure Active Directory B2C (Azure AD B2C) interagieren können, müssen sie in einem von Ihnen verwalteten Mandanten registriert werden. In diesem Tutorial erfahren Sie, wie Sie eine Single-Page-Webanwendung mithilfe des Azure-Portals registrieren.

## <a name="overview-of-authentication-options"></a>Übersicht über die Authentifizierungsoptionen

Viele moderne Webanwendungen werden als clientseitige Single-Page-Webanwendungen („SPAs“) erstellt. Entwickler schreiben diese mithilfe von JavaScript oder eines SPA-Frameworks wie Angular, Vue oder React. Diese Anwendungen werden in einem Webbrowser ausgeführt und weisen andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen auf.

Azure AD B2C bietet **zwei** Optionen, um Single-Page-Webanwendungen das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder -Web-APIs zu ermöglichen:

### <a name="authorization-code-flow-with-pkce"></a>Autorisierungscodefluss (mit PKCE)
- [OAuth 2.0-Autorisierungscodefluss (mit PKCE)](./authorization-code-flow.md). Der Autorisierungscodefluss ermöglicht es der Anwendung, einen Autorisierungscode für **ID-Token** auszutauschen, um den authentifizierten Benutzer darzustellen, sowie **Zugriffstoken** auszutauschen, die zum Aufrufen geschützter APIs erforderlich sind. Darüber hinaus werden **Aktualisierungstoken** zurückgegeben, die langfristigen Zugriff auf Ressourcen im Auftrag von Benutzern bereitstellen, ohne dass eine Interaktion mit diesen Benutzern erforderlich ist. 

Dies ist die **empfohlene** Vorgehensweise. Aktualisierungstoken mit begrenzter Lebensdauer tragen zur Anpassung Ihrer Anwendung an die [in Bezug auf die Einschränkungen von Cookies geltenden Datenschutzbestimmungen moderner Browser](../active-directory/develop/reference-third-party-cookies-spas.md) (z. B. an Safari ITP) bei.

Um diesen Flow nutzen zu können, kann Ihre Anwendung eine Authentifizierungsbibliothek verwenden, von der sie unterstützt wird, z. B. [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser). 

![Autorisierungsfluss für Single-Page-Webanwendungen](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Impliziter Gewährungsflow
- [Impliziter OAuth 2.0-Fluss](implicit-flow-single-page-application.md). Einige Bibliotheken, z. B. [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), unterstützen nur den Flow für die implizite Genehmigung. Der Fluss für die implizite Genehmigung ermöglicht der Anwendung das Abrufen von **ID-** und **Zugriffstoken**. Im Gegensatz zum Autorisierungscodefluss gibt der Fluss für die implizite Genehmigung kein **Aktualisierungstoken** zurück. 

![Impliziter Fluss für Single-Page-Webanwendungen](./media/tutorial-single-page-app/spa-app.svg)

Dieser Authentifizierungsfluss umfasst keine Anwendungsszenarios, die plattformübergreifende JavaScript-Frameworks verwenden, z. B. Electron und React-Native. Diese Szenarien erfordern weitere Funktionen für die Interaktion mit den nativen Plattformen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie Ihren eigenen [Azure AD B2C-Mandanten](tutorial-create-tenant.md) noch nicht erstellt haben, erstellen Sie jetzt einen. Sie können einen vorhandenen Azure AD B2C-Mandanten verwenden.

## <a name="register-the-spa-application"></a>Registrieren der SPA

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *spaapp1*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus.
1. Wählen Sie unter **Umleitungs-URI** die Option **Single-Page-Anwendung (SPA)** aus, und geben Sie `https://jwt.ms` in das URL-Textfeld ein.

    Die Umleitungs-URI ist der Endpunkt, an den der Benutzer vom Autorisierungsserver (in diesem Fall Azure AD B2C) nach Abschluss seiner Interaktion mit dem Benutzer gesendet wird und an den bei erfolgreicher Autorisierung ein Zugriffstoken oder Autorisierungscode gesendet wird. In einer Produktionsanwendung handelt es sich in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, etwa um `https://contoso.com/auth-response`. Für Testzwecke wie in diesem Tutorial können Sie ihn auf `https://jwt.ms` festlegen. Dabei handelt es sich um eine Microsoft-Webanwendung, die den decodierten Inhalt eines Tokens anzeigt (der Inhalt des Tokens verlässt niemals Ihren Browser). Während der App-Entwicklung können Sie den Endpunkt hinzufügen, an dem die Anwendung lokal lauscht, etwa `http://localhost:5000`. Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern.

    Für Umleitungs-URIs gelten die folgenden Einschränkungen:

    * Die Antwort-URL muss mit dem Schema `https` beginnen, es sei denn, Sie verwenden `localhost`.
    * Bei der Antwort-URL muss die Groß-/Kleinschreibung beachtet werden. Die Groß-/Kleinschreibung muss der Groß-/Kleinschreibung des URL-Pfads Ihrer ausgeführten Anwendung entsprechen. Wenn Ihre Anwendung z. B. als Teil des Pfads `.../abc/response-oidc` enthält, geben Sie in der Antwort-URL nicht `.../ABC/response-oidc` an. Weil der Webbrowser bei Pfaden die Groß-/Kleinschreibung beachtet, werden Cookies, die `.../abc/response-oidc` zugeordnet sind, möglicherweise ausgeschlossen, wenn eine Umleitung an die anders geschriebene (nicht übereinstimmende) URL `.../ABC/response-oidc` erfolgt.

1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.


## <a name="enable-the-implicit-flow"></a>Aktivieren des impliziten Flows
Bei Verwendung des impliziten Flows müssen Sie den Flow für die implizite Genehmigung in der App-Registrierung aktivieren.

1. Wählen Sie im linken Menü unter **Verwalten** die Option **Authentifizierung** aus.
1. Aktivieren Sie unter **Implizite Genehmigung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Wählen Sie **Speichern** aus.

## <a name="migrate-from-the-implicit-flow"></a>Migrieren vom impliziten Flow

Wenn Sie über eine vorhandene Anwendung verfügen, die den impliziten Flow verwendet, empfiehlt es sich, zur Verwendung des Autorisierungscodeflows zu migrieren. Verwenden Sie dazu ein Framework, das diesen unterstützt, etwa [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Wenn alle von einer App-Registrierung dargestellten Single-Page-Webanwendungen in Ihrer Produktionsumgebung den Autorisierungscodeflow verwenden, deaktivieren Sie die Einstellungen für den Flow für implizite Genehmigung. 

1. Wählen Sie im linken Menü unter **Verwalten** die Option **Authentifizierung** aus.
1. Deaktivieren Sie unter **Implizite Genehmigung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Klicken Sie auf **Speichern**.

Anwendungen, die den impliziten Flow verwenden, funktionieren weiterhin, wenn der implizite Flow aktiviert bleibt.

* * *

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes erfahren Sie, wie Sie Benutzerflows erstellen, damit sich die Benutzer registrieren, anmelden und ihre Profile verwalten können.

> [!div class="nextstepaction"]
> [Erstellen von Benutzerflows in Azure Active Directory B2C >](tutorial-create-user-flows.md)
