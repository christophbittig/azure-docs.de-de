---
title: 'Tutorial: Hinzufügen der Authentifizierung zu einer Web-App in Azure App Service | Azure'
description: In diesem Tutorial erfahren Sie, wie Sie die Authentifizierung und Autorisierung für eine Web-App aktivieren, die in Azure App Service ausgeführt wird. Außerdem erfahren Sie, wie Sie den Web-App-Zugriff auf Benutzer in Ihrer Organisation beschränken.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 09/14/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a14d63d171c3e6bdbcbe0c706f5f614eae7e853b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628608"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Tutorial: Hinzufügen der Authentifizierung zu Ihrer Web-App in Azure App Service

Hier erfahren Sie, wie Sie die Authentifizierung für Ihre in Azure App Service ausgeführte Web-App aktivieren und den Zugriff auf die Benutzer in Ihrer Organisation beschränken.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagramm: Benutzeranmeldung" border="false":::

App Service verfügt über integrierte Authentifizierungs- und Autorisierungsunterstützung, sodass Sie für die Benutzeranmeldung und den Zugriff auf Daten in Ihrer Web-App nur wenig oder gar keinen Code schreiben müssen. Die Verwendung des App Service-Moduls für die Authentifizierung/Autorisierung ist zwar nicht erforderlich, sie vereinfacht jedoch die Authentifizierung und Autorisierung für Ihre App. In diesem Artikel erfahren Sie, wie Sie Ihre Web-App mit dem App Service-Modul für die Authentifizierung/Autorisierung schützen, indem Sie Azure Active Directory (Azure AD) als Identitätsanbieter verwenden.

Das Modul für die Authentifizierung/Autorisierung wird über das Azure-Portal und die App-Einstellungen aktiviert und konfiguriert. Weder SDKs noch bestimmte Sprachen oder Änderungen am Anwendungscode sind erforderlich. Es werden verschiedene Identitätsanbieter unterstützt, darunter Azure AD, Microsoft-Konto, Facebook, Google und Twitter. Wenn das Modul für die Authentifizierung/Autorisierung aktiviert ist, wird es von allen eingehenden HTTP-Anforderungen durchlaufen, bevor diese vom App-Code verarbeitet werden. Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](overview-authentication-authorization.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Konfigurieren der Authentifizierung für die Web-App
> * Beschränken des Web-App-Zugriffs auf Benutzer in Ihrer Organisation

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Erstellen und Veröffentlichen einer Web-App in App Service

Für dieses Tutorial benötigen Sie eine in App Service bereitgestellte Web-App. Sie können eine vorhandene Web-App verwenden oder die [ASP.NET Core-Schnellstartanleitung](quickstart-dotnetcore.md) befolgen, um eine neue Web-App für App Service zu erstellen und zu veröffentlichen.

Unabhängig davon, ob Sie eine vorhandene Web-App verwenden oder eine neue erstellen, sollten Sie sich den Namen der Web-App und den Namen der Ressourcengruppe notieren, in der die Web-App bereitgestellt wurde. Sie benötigen diese Namen in diesem Tutorial. 

## <a name="configure-authentication-and-authorization"></a>Konfigurieren der Authentifizierung und Autorisierung

Sie verfügen nun über eine Web-App, die in App Service ausgeführt wird. Als Nächstes aktivieren Sie die Authentifizierung und Autorisierung für die Web-App. Als Identitätsanbieter wird Azure AD verwendet. Weitere Informationen finden Sie unter [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](configure-authentication-provider-aad.md).

Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) die Option **Ressourcengruppen** aus, oder suchen Sie auf einer beliebigen Seite nach **Ressourcengruppen**, und wählen Sie diese Option aus.

Navigieren Sie in **Ressourcengruppen** zu Ihrer Ressourcengruppe, und wählen Sie sie aus. Wählen Sie unter **Übersicht** die Verwaltungsseite Ihrer App aus.

:::image type="content" alt-text="Screenshot: Auswählen der Verwaltungsseite Ihrer App" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Wählen Sie im linken Menü der APP **Authentifizierung** aus, und klicken Sie dann auf **Identitäts Anbieter hinzufügen**.

Wählen Sie auf der Seite **Identitäts Anbieter hinzufügen die Option** **Microsoft** als **Identitäts Anbieter** aus, um Microsoft-und Azure AD Identitäten anzumelden.

Wählen Sie für **App** Registration App Registration  >  **Type** die Option **Create New App Registration** aus.

Wählen Sie für die **App-Registrierung**  >  **unterstützte Konto Typen** die Option **Aktueller Mandant-einzelner** Mandant aus.

Belassen Sie im Abschnitt "Einstellungen für die **App Service Authentifizierung** " die **Einstellung Authentifizierung** **erforderlich**, und **nicht authentifizierte Anforderungen** auf **HTTP 302-Umleitung finden: empfohlen für Websites**.

Klicken Sie am unteren Rand der Seite **Identitäts Anbieter hinzufügen** auf **Hinzufügen**, um die Authentifizierung für Ihre Web-App zu aktivieren.

:::image type="content" alt-text="Screenshot: Konfigurieren einer Authentifizierung." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Sie verfügen nun über eine App, die per App Service-Authentifizierung und -Autorisierung geschützt ist.

> [!NOTE]
> Wenn Sie Konten von anderen Mandanten zulassen möchten, ändern Sie die ‚Aussteller-URL‘ in ‚ https://login.microsoftonline.com/common/v2.0 ‘, indem Sie auf dem Blatt ‚Authentifizierung‘ Ihren ‚Identitätsanbieter‘ bearbeiten.
>

## <a name="verify-limited-access-to-the-web-app"></a>Überprüfen des beschränkten Zugriffs auf die Web-App

Im Zuge der Aktivierung des App Service-Moduls für die Authentifizierung/Autorisierung wurde in Ihrem Azure AD-Mandanten eine App-Registrierung erstellt. Die App-Registrierung hat den gleichen Anzeigenamen wie Ihre Web-App. Wählen Sie zum Überprüfen der Einstellungen im Portalmenü die Option **Azure Active Directory** und anschließend **App-Registrierungen** aus. Wählen Sie die App-Registrierung aus, die erstellt wurde. Überprüfen Sie in der Übersicht, ob **Unterstützte Kontotypen** auf **Nur meine Organisation** festgelegt ist.

:::image type="content" alt-text="Screenshot: Überprüfen des Zugriffs" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Starten Sie einen Browser im Inkognito- oder InPrivate-Modus, und navigieren Sie zu `https://<app-name>.azurewebsites.net`, um zu überprüfen, ob der Zugriff auf Ihre App auf die Benutzer Ihrer Organisation beschränkt ist. Sie sollten zu einer geschützten Anmeldeseite weitergeleitet werden. Ist dies der Fall, haben nicht authentifizierte Benutzer keinen Zugriff auf die Website. Melden Sie sich als Benutzer Ihrer Organisation an, um Zugriff auf die Website zu erhalten. Sie können auch einen neuen Browser starten und versuchen, sich mit einem persönlichen Konto anzumelden, um sich zu vergewissern, dass externe Benutzer keinen Zugriff haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben und die Web-App und die zugehörigen Ressourcen nicht mehr benötigen, sollten Sie [die von Ihnen erstellten Ressourcen bereinigen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Konfigurieren der Authentifizierung für die Web-App
> * Beschränken des Web-App-Zugriffs auf Benutzer in Ihrer Organisation

> [!div class="nextstepaction"]
> [App Service: Zugreifen auf den Speicher](scenario-secure-app-access-storage.md)
