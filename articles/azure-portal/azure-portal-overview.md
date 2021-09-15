---
title: Azure-Portal – Übersicht
description: Das Azure-Portal ist eine grafische Benutzeroberfläche, über die Sie Azure-Dienste verwalten können. Erfahren Sie, wie Sie im Azure-Portal navigieren und Ressourcen suchen können.
keywords: Portal
ms.date: 08/30/2021
ms.topic: overview
ms.openlocfilehash: 32ca1f0032ab5ced2157bb73ccf5c3f40df86707
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224459"
---
# <a name="azure-portal-overview"></a>Azure-Portal – Übersicht

In diesem Artikel zur Vorstellung des Azure-Portals werden Sie mit den Elementen auf der Portalseite und seiner Verwaltungsumgebung vertraut gemacht.

## <a name="what-is-the-azure-portal"></a>Was ist das Azure-Portal?

Das Azure-Portal ist eine webbasierte, zentrale Konsole, die eine Alternative zu Befehlszeilentools darstellt. Im Azure-Portal können Sie Ihr Azure-Abonnement auf einer grafischen Benutzeroberfläche verwalten. Sie können alle Komponenten erstellen, verwalten und überwachen – von einfachen Web-Apps bis hin zu komplexen Cloudbereitstellungen. Erstellen Sie benutzerdefinierte Dashboards für eine übersichtliche Ansicht der Ressourcen. Konfigurieren Sie Optionen für die Barrierefreiheit für eine optimale Benutzererfahrung.

Das Azure-Portal ist auf Resilienz und fortlaufende Verfügbarkeit ausgelegt. Es ist in jedem Azure-Rechenzentrum vorhanden. Durch diese Konfiguration ist das Azure-Portal hinsichtlich der Ausfälle einzelner Rechenzentren resilient und vermeidet Netzwerkverzögerungen, da es sich in der Nähe der Benutzer befindet. Das Azure-Portal wird ständig aktualisiert und erfordert bei Wartungsarbeiten keine Downtime.

## <a name="azure-portal-menu"></a>Azure-Portalmenü

Sie können den [Standardmodus für das Portalmenü auswählen](set-preferences.md#set-menu-behavior). Es kann angedockt werden oder als Flyoutbereich dienen.

Wenn sich das Portalmenü im Flyoutmodus befindet, wird es ausgeblendet, bis Sie es benötigen. Wählen Sie das Menüsymbol aus, um das Menü zu öffnen oder zu schließen.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png" alt-text="Screenshot: Azure-Portalmenü im Flyoutmodus.":::

Wenn Sie für das Portalmenü den angedockten Modus auswählen, wird es immer angezeigt. Sie können das Menü reduzieren, um den Arbeitsbereich zu vergrößern.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png" alt-text="Screenshot: Azure-Portalmenü im angedockten Modus.":::

## <a name="azure-home"></a>Azure-Startseite

Als neuer Abonnent von Azure-Diensten sehen Sie nach der [Anmeldung beim Portal](https://portal.azure.com) als Erstes die **Azure-Startseite**. Auf dieser Seite finden Sie Ressourcen, die Ihnen helfen, das Beste aus Ihrem Azure-Abonnement herauszuholen. Sie finden hier Links zu kostenlosen Onlinekursen, Dokumentation, den wichtigsten Diensten und nützlichen Websites, um auf dem Laufenden zu bleiben und Veränderungen für Ihr Unternehmen erfolgreich umzusetzen. Für einen schnellen und einfachen Zugriff auf die aktuellen Aufgaben zeigen wir Ihnen auch eine Liste Ihrer zuletzt verwendeten Ressourcen.

Sie können die Startseite nicht anpassen, aber Sie können wählen, ob Sie die **Startseite** oder das **Dashboard** als Standardansicht sehen möchten. Bei der ersten Anmeldung wird oben auf der Seite eine Eingabeaufforderung zum Speichern Ihrer Einstellung angezeigt. Sie können [die Auswahl der Startseite jederzeit in den **Portaleinstellungen** ändern](set-preferences.md#startup-page).

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png" alt-text="Screenshot: Optionen der Startseite im Azure-Portal.":::

## <a name="azure-dashboard"></a>Azure-Dashboard

Dashboards bieten einen fokussierten Überblick über die Ressourcen in Ihrem Abonnement, die für Sie am wichtigsten sind. Wir haben Ihnen ein Standarddashboard zur Verfügung gestellt, um Ihnen den Einstieg zu erleichtern. Sie können dieses Dashboard so anpassen, dass die von Ihnen häufig genutzten Ressourcen in einer zentralen Ansicht dargestellt werden. Alle Änderungen, die Sie an der Standardansicht vornehmen, wirken sich nur auf Ihre Arbeitsumgebung aus. Sie können jedoch zusätzliche Dashboards für den eigenen Gebrauch erstellen oder Ihre benutzerdefinierten Dashboards veröffentlichen und diese für andere Benutzer in Ihrer Organisation freigeben. Weitere Informationen finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).

Wie oben erwähnt, können Sie [das Dashboard als Startseite festlegen](set-preferences.md#startup-page), wenn Sie ihr zuletzt verwendetes [Dashboard](azure-portal-dashboards.md) anzeigen möchten, wenn Sie sich beim Azure-Portal anmelden.

## <a name="getting-around-the-portal"></a>Navigation im Portal

Es ist hilfreich, das grundlegende Layout des Portals zu kennen und die Interaktion mit ihm zu verstehen. An dieser Stelle stellen wir Ihnen die Komponenten der Benutzeroberfläche und einige der Begriffe in unseren Anweisungen vor. Eine ausführlichere Tour durch das Portal finden Sie in der Lerneinheit [Navigation im Portal](/learn/modules/tour-azure-portal/3-navigate-the-portal) des Kurses.

Das Azure-Portalmenü und die Kopfzeile sind stets vorhandene globale Elemente. Diese unveränderlichen Elemente sind die „Shell“ für die Benutzeroberfläche, die mit allen Diensten und Funktionen verknüpft ist, und die Kopfzeile bietet Zugriff auf globale Steuerelemente. Die Konfigurationsseite (auch „Blatt“ genannt) für eine Ressource kann auch ein Ressourcenmenü aufweisen, das Ihnen hilft, zwischen Features zu wechseln.

In der nachstehenden Abbildung sind die Grundelemente des Azure-Portals gekennzeichnet, die in der folgenden Tabelle beschrieben werden. In diesem Beispiel liegt der aktuelle Fokus auf einem virtuellen Computer, aber die gleichen Elemente gelten unabhängig davon, mit welcher Art von Ressource oder Dienst Sie arbeiten.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-callouts.png" alt-text="Screenshot der Vollbildansicht des Portals mit Schlüsseln für Benutzeroberflächenelemente.":::

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png" alt-text="Screenshot des erweiterten Portalmenüs mit Schlüsseln für Benutzeroberflächenelemente.":::

|Schlüssel|BESCHREIBUNG
|:---:|---|
|1|Kopfzeile. Wird oben auf jeder Portalseite angezeigt und enthält globale Elemente.|
|2|Globale Suche. Über die Suchleiste können Sie schnell eine bestimmte Ressource, einen Dienst oder Dokumentation finden.|
|3|Globale Steuerelemente. Werden ebenso wie alle globalen Elemente stets im Portal angezeigt und umfassen: Cloud Shell, Abonnementfilter, Benachrichtigungen, Portaleinstellungen, Hilfe und Support und Feedbackoption.|
|4|Ihr Konto. Hier können Sie Informationen zu Ihrem Konto einsehen, Verzeichnisse wechseln, sich abmelden oder mit einem anderen Konto anmelden.|
|5|Azure-Portalmenü. Dieses globale Element kann Ihnen helfen, zwischen Diensten zu navigieren. Manchmal auch als Randleiste bezeichnet. (Die Elemente 9 und 10 in dieser Liste werden in diesem Menü angezeigt.)|
|6|Ressourcenmenü. Viele Dienste weisen ein Ressourcenmenü auf, das Ihnen bei der Verwaltung des Diensts hilft. Dieses Element wird möglicherweise auch als „linker Bereich“ bezeichnet. Hier sehen Sie Befehle, die in Ihrem aktuellen Fokus kontextabhängig sind.|
|7|Befehlsleiste. Der Kontext dieser Steuerelemente ist von Ihrem aktuellen Fokus abhängig.|
|8|Arbeitsbereich. Zeigt die Details der Ressource, die gegenwärtig im Fokus ist.|
|9|Breadcrumb. Über die Breadcrumb-Links kehren Sie in Ihrem Workflow eine Ebene zurück.|
|10|Hauptsteuerelement zum Erstellen einer neuen Ressource im aktuellen Abonnement. Erweitern oder öffnen Sie das Azure-Portalmenü, um **+ Ressource erstellen** anzuzeigen. Sie finden diese Option auch auf der **Startseite**. Sie können dann in Azure Marketplace den zu erstellenden Ressourcentyp suchen und auswählen.|
|11|Ihre Favoritenliste im Azure-Portalmenü. Weitere Informationen zum Anpassen dieser Liste finden Sie unter [Hinzufügen, Entfernen und Sortieren von Favoriten](../azure-portal/azure-portal-add-remove-sort-favorites.md).|

## <a name="get-started-with-services"></a>Erste Schritte mit Diensten

Wenn Sie ein neuer Abonnent sind, müssen Sie erst eine Ressource erstellen, ehe es etwas zu verwalten gibt. Wählen Sie **+ Ressource erstellen** aus, um die in Azure Marketplace verfügbaren Dienste anzuzeigen. Hier finden Sie Hunderte Anwendungen und Dienste von zahlreichen Anbietern, die alle für die Ausführung in Azure zertifiziert sind.

Wir haben Ihre [Favoriten](../azure-portal/azure-portal-add-remove-sort-favorites.md) auf der Randleiste vorab mit Links zu häufig genutzten Diensten versehen.  Klicken Sie zum Anzeigen aller verfügbaren Dienste auf der Randleiste auf **Alle Dienste**.

> [!TIP]
> Der schnellste Weg, eine Ressource, einen Dienst oder Dokumentation zu finden, ist die *Suche* in der globalen Kopfzeile.

Sehen Sie sich dieses Video an, um zu erfahren, wie die globale Suche im Azure-Portal verwendet wird.

> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Verwenden der globalen Suche im Azure-Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie unter [Unterstützte Browser und Geräte](../azure-portal/azure-portal-supported-browsers-devices.md), wo Sie das Azure-Portal aufrufen können.
* Bleiben Sie mit der [mobilen Azure-App](https://azure.microsoft.com/features/azure-portal/mobile-app/) unterwegs auf dem Laufenden.
* Das Onboarding und Einrichten Ihrer Cloudumgebung führen Sie mit dem [Azure-Schnellstartcenter](../azure-portal/azure-portal-quickstart-center.md) durch.