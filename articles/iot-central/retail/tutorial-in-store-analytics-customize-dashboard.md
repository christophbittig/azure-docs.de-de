---
title: 'Tutorial: Anpassen des Dashboards in Azure IoT Central'
description: Dieses Tutorial zeigt, wie Sie das Dashboard in einer IoT Central-Anwendung anpassen und Geräte verwalten.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 08/24/2021
ms.openlocfilehash: 0f0eae49b3f108d1bb2e812fd8b466da243293c8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428559"
---
# <a name="tutorial-customize-the-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial: Anpassen des Dashboards und Verwalten von Geräten in Azure IoT Central

In diesem Tutorial erfahren Sie, wie Sie das Dashboard in Ihrer Azure IoT Central-Anwendung für die In-Store-Analyse anpassen können. Anwendungsbediener können das angepasste Dashboard verwenden, um die Anwendung auszuführen und die angeschlossenen Geräte zu verwalten.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]

> * Anpassen von Bildkacheln auf dem Dashboard
> * Anordnen von Kacheln zum Ändern des Layouts
> * Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen
> * Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails
> * Hinzufügen von Befehlskacheln zum Ausführen von Befehlen

## <a name="prerequisites"></a>Voraussetzungen

Der Ersteller muss das Tutorial absolvieren, um die Azure IoT Central-Anwendung für die In-Store-Analyse zu erstellen und Geräte hinzuzufügen:

* [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (erforderlich)

## <a name="change-the-dashboard-name"></a>Ändern des Dashboardnamens

Zum Anpassen des Dashboards müssen Sie das Standarddashboard in Ihrer Anwendung bearbeiten. Sie können auch neue Dashboards erstellen. Der erste Schritt beim Anpassen des Dashboards in Ihrer Anwendung besteht darin, den Namen zu ändern.

1. Navigieren Sie zur Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral).

1. Öffnen Sie die Anwendung für die Überwachung von Bedingungen, die Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) erstellt haben.

1. Wählen Sie **Dashboardeinstellungen** aus, geben Sie einen **Namen** für Ihr Dashboard ein, und wählen Sie **Speichern** aus. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png" alt-text="Azure IoT Central-Dashboard: Bearbeiten.":::

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/new-dashboard.png" alt-text="Azure IoT Central: neues Dashboard.":::


## <a name="customize-image-tiles-on-the-dashboard"></a>Anpassen von Bildkacheln auf dem Dashboard

Ein Azure IoT Central-Anwendungsdashboard besteht aus einer oder mehreren Kacheln. Eine Kachel ist ein rechteckiger Container zum Anzeigen von Inhalt auf einem Dashboard. Sie können den Kacheln verschiedene Arten von Inhalten zuordnen, und Sie können Kacheln ziehen, ablegen und ihre Größe ändern, um das Layout des Dashboards anzupassen. Es gibt verschiedene Arten von Kacheln für die Anzeige von Inhalten. Bildkacheln enthalten Bilder, und Sie können eine URL hinzufügen, die es Benutzern ermöglicht, auf das Bild zu klicken. Bezeichnungskacheln enthalten nur Text. Markdownkacheln enthalten formatierten Inhalt und ermöglichen es Ihnen, ein Bild, eine URL, einen Titel sowie Markdowncode festzulegen, der als HTML gerendert wird. Telemetrie-, Eigenschaften- und Befehlskacheln zeigen gerätespezifische Daten an. 

In diesem Abschnitt erfahren Sie, wie Sie Bildkacheln auf dem Dashboard anpassen.

Um die Bildkachel anzupassen, die ein Markenbild auf dem Dashboard anzeigt, gehen Sie folgendermaßen vor:

1. Klicken Sie auf der Symbolleiste des Dashboards auf **Bearbeiten**. 

1. Wählen Sie auf der Bildkachel, auf der das Northwind-Markenbild angezeigt wird, die Option **Konfigurieren** aus. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png" alt-text="Azure IoT Central-Dashboard: Bearbeiten des Markenbilds.":::

1. Ändern Sie den **Titel**. Der Titel wird angezeigt, wenn ein Benutzer auf das Bild zeigt.

1. Klicken Sie auf **Bild**. Ein Dialogfeld wird geöffnet, in dem Sie ein benutzerdefiniertes Bild hochladen können. 

1. Optional können Sie eine URL für das Bild angeben.

1. Wählen Sie **Update** (Aktualisieren) aus.

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png" alt-text="Azure IoT Central-Dashboard: Speichern des Markenbilds.":::

1. Optional können Sie auch auf der Kachel namens **Dokumentation** die Option **Konfigurieren** auswählen und eine URL zu unterstützenden Inhalten angeben. 

So passen Sie die Bildkachel an, die einen Plan der Sensorzonen im Laden anzeigt:

1. Wählen Sie auf der Bildkachel, auf der der standardmäßige Zonenplan des Ladens angezeigt wird, die Option **Konfigurieren** aus. 

1. Klicken Sie auf **Bild**, und verwenden Sie das Dialogfeld, um ein benutzerdefiniertes Bild des Ladenzonenplans im Laden hochzuladen. 

1. Wählen Sie **Update** aus.

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png" alt-text="Azure IoT Central-Dashboard: Speichern des Ladenplans.":::

    Der Beispielplan für das Contoso-Ladengeschäft zeigt vier Zonen: zwei Kassenzonen, eine Zone für Waschmittel und Körperpflegeprodukte und eine Zone für Lebensmittel und Getränke. In diesem Tutorial ordnen Sie diesen Zonen Sensoren zu, um Telemetriedaten bereitzustellen.

## <a name="arrange-tiles-to-modify-the-layout"></a>Anordnen von Kacheln zum Ändern des Layouts

Ein wichtiger Schritt beim Anpassen eines Dashboards besteht darin, die Kacheln so anzuordnen, dass sie eine nützliche Ansicht bieten. Anwendungsbediener verwenden das Dashboard, um Telemetriedaten zu Geräten zu visualisieren, Geräte zu verwalten und Bedingungen in einem Ladengeschäft zu überwachen. Azure IoT Central erleichtert Anwendungserstellern die Arbeit beim Erstellen eines Dashboards. Mit dem Bearbeitungsmodus für Dashboards können Sie Kacheln schnell hinzufügen, verschieben, löschen und ihre Größe ändern. Die Anwendungsvorlage **In-store analytics - checkout** vereinfacht die Erstellung eines Dashboards ebenfalls. Sie bietet ein funktionierendes Dashboardlayout mit verbundenen Sensoren und Kacheln, die Zähler für Warteschlangen an der Kasse sowie Umgebungsbedingungen anzeigen.

In diesem Abschnitt ordnen Sie das Dashboard in der Anwendungsvorlage **In-store analytics - checkout** neu an, um ein benutzerdefiniertes Layout zu erstellen.

So entfernen Sie Kacheln, die Sie in Ihrer Anwendung nicht verwenden möchten:

1. Klicken Sie auf der Symbolleiste des Dashboards auf **Bearbeiten**. 

1. Wählen Sie die **Auslassungszeichen** und **Löschen** aus, um die folgenden Kacheln zu entfernen: **Back to all zones**, **Visit store dashboard**, **Occupancy**, **Warm-up checkout zone**, **Cool-down checkout zone**, **Occupancy sensor settings**, **Thermostat sensor settings**, **Environment conditions** und alle drei zu **Checkout 3** gehörenden Kacheln. Das Dashboard für das Ladengeschäft von Contoso verwendet diese Kacheln nicht. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png" alt-text="Azure IoT Central: Löschen von Kacheln.":::


1. Wählen Sie **Speichern** aus. Das Entfernen nicht verwendeter Kacheln gibt Platz auf der Bearbeitungsseite frei und vereinfacht die Dashboardansicht für Bediener.

Nachdem Sie nicht verwendete Kacheln entfernt haben, ordnen Sie die verbleibenden Kacheln neu an, um eine übersichtliches Layout zu erstellen. Das neue Layout bietet Platz für Kacheln, die Sie in einem späteren Schritt hinzufügen.

So ordnen Sie die verbleibenden Kacheln neu an:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie die Kachel **Occupancy firmware** aus, und ziehen Sie sie rechts neben die Akkukachel **Occupancy**.

1. Wählen Sie die Kachel **Thermostat firmware** aus, und ziehen Sie sie rechts neben die Akkukachel **Thermostat**.

1. Wählen Sie **Speichern** aus.

1. Sehen Sie sich die Layoutänderungen an. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png" alt-text="Azure IoT Central: Akkukacheln für Firmware.":::

## <a name="add-telemetry-tiles-to-display-conditions"></a>Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen

Nachdem Sie das Dashboardlayout angepasst haben, können Sie Kacheln zur Anzeige von Telemetrie hinzufügen. Um eine Telemetriekachel zu erstellen, wählen Sie eine Gerätevorlage und eine Geräteinstanz aus, und wählen Sie dann die gerätespezifische Telemetrie aus, die auf der Kachel angezeigt werden soll. Die Anwendungsvorlage **In-store analytics - checkout** enthält verschiedene Telemetriekacheln auf dem Dashboard. Die vier Kacheln in den beiden Kassenzonen zeigen Telemetriedaten des simulierten Sensors für die Zonenauslastung an. Die Kachel **People traffic** zeigt Zähler in den beiden Kassenzonen an. 

In diesem Abschnitt fügen Sie zwei weitere Telemetriekacheln hinzu, um Umgebungstelemetrie der RuuviTag-Sensoren anzuzeigen, die Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) hinzugefügt haben. 

So fügen Sie Kacheln zum Anzeigen von Umgebungsdaten aus den RuuviTag-Sensoren hinzu:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `RuuviTag` aus. 

1. Wählen Sie als **Geräteinstanz** einen der beiden RuuviTag-Sensoren aus. Klicken Sie im Contoso-Ladengeschäft auf `Zone 1 Ruuvi`, um eine Telemetriekachel für Zone 1 zu erstellen. 

1. Wählen Sie in der Liste **Telemetrie** die Einträge `Relative humidity` und `temperature` aus. Dies sind die Telemetrieelemente, die für jede Zone auf der Kachel Daten anzeigen.

1. Wählen Sie **Kombinieren** aus. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png" alt-text="Azure IoT Central: Hinzufügen von RuuviTag-Kachel 1.":::

    Es wird eine neue Kachel hinzugefügt, die kombinierte Telemetriedaten für Luftfeuchtigkeit und Temperatur für den ausgewählten Sensor anzeigt. 

1. Klicken Sie auf der neuen Kachel für den RuuviTag-Sensor auf **Konfigurieren**. 

1. Ändern Sie den **Titel** zu *Umgebung Zone 1*. 

1. Klicken Sie auf **Konfiguration aktualisieren**.

1. Wiederholen Sie diese Schritte, um eine Kachel für die zweite Sensorinstanz zu erstellen. Legen Sie den **Titel** auf *Umgebung Zone 2* fest, und wählen Sie dann **Konfiguration aktualisieren** aus.

1. Ziehen Sie die Kachel mit dem Titel **Umgebung Zone 2** unter die Kachel **Thermostat firmware**. 

1. Ziehen Sie die Kachel mit dem Titel **Umgebung Zone 1** unter die Kachel **People traffic**. 

1. Wählen Sie **Speichern** aus. Das Dashboard zeigt die Zonentelemetrie auf den beiden neuen Kacheln an.

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png" alt-text="Azure IoT Central: Alle RuuviTag-Kacheln.":::

So bearbeiten Sie die Kachel **People traffic** so, dass nur Telemetriedaten für zwei Kassenzonen angezeigt werden:

1. Wählen Sie **Bearbeiten** aus. 

1. Klicken Sie auf der Kachel **People traffic** auf **Konfigurieren**.

1. Wählen Sie unter **Telemetrie** die Einträge **Zähler 1**, **Zähler 2** und **Zähler 3** aus. 

1. Klicken Sie auf **Konfiguration aktualisieren**. Dadurch wird die vorhandene Konfiguration auf der Kachel gelöscht. 

1. Klicken Sie auf der Kachel **People traffic** erneut auf **Konfigurieren**.

1. Wählen Sie unter **Telemetrie** die Einträge **Zähler 1** und **Zähler 2** aus. 

1. Klicken Sie auf **Konfiguration aktualisieren**. 

1. Wählen Sie **Speichern** aus.  Das aktualisierte Dashboard zeigt nur für Ihre beiden Kassenzonen Zähler an, die auf dem simulierten Sensor für die Zonenauslastung basieren.

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png" alt-text="Azure IoT Central: „People traffic“, zwei Kassenschlangen.":::

## <a name="add-property-tiles-to-display-device-details"></a>Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails

Anwendungsbediener verwenden das Dashboard zum Verwalten von Geräten und zur Statusüberwachung. Fügen Sie für jedes RuuviTag eine Kachel hinzu, um Bedienern die Anzeige der Softwareversion zu ermöglichen. 

So fügen Sie eine Eigenschaftenkachel für jedes RuuviTag hinzu:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `RuuviTag` aus. 

1. Wählen Sie als **Geräteinstanz** einen der beiden RuuviTag-Sensoren aus. Klicken Sie im Contoso-Ladengeschäft auf `Zone 1 Ruuvi`, um eine Telemetriekachel für Zone 1 zu erstellen. 

1. Klicken Sie auf **Eigenschaften > Softwareversion**.

1. Wählen Sie **Kombinieren** aus. 

1. Klicken Sie auf der neu erstellten Kachel namens **Softwareversion** auf **Konfigurieren**. 

1. Ändern Sie den **Titel** zu *Softwareversion von Ruuvi 1*.

1. Klicken Sie auf **Konfiguration aktualisieren**. 

1. Ziehen Sie die Kachel namens **Softwareversion von Ruuvi 1** unter die Kachel **Umgebung Zone 1**.

1. Wiederholen Sie diese Schritte, um eine Eigenschaftenkachel für die Softwareversion für das zweite RuuviTag zu erstellen. 

1. Wählen Sie **Speichern** aus.  

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png" alt-text="Azure IoT Central: RuuviTag-Eigenschaftenkacheln.":::

## <a name="add-command-tiles-to-run-commands"></a>Hinzufügen von Befehlskacheln zum Ausführen von Befehlen

Anwendungsbediener verwenden das Dashboard auch zum Verwalten von Geräten durch Ausführen von Befehlen. Sie können Befehlskacheln zum Dashboard hinzufügen, mit denen vordefinierte Befehle auf einem Gerät ausgeführt werden. In diesem Abschnitt fügen Sie eine Befehlskachel hinzu, um es Bedienern zu ermöglichen, das Rigado-Gateway neu zu starten. 

So fügen Sie eine Befehlskachel für einen Neustart des Gateways hinzu:

1. Wählen Sie **Bearbeiten** aus. 

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `C500` aus. Dies ist die Vorlage für das Rigado C500-Gateway. 

1. Wählen Sie in **Geräteinstanz** die Gatewayinstanz aus.

1. Wählen Sie **Befehl > Neustart** aus, und ziehen Sie diesen Eintrag auf das Dashboard neben den Ladenplan. 

1. Wählen Sie **Speichern** aus. 

1. Zeigen Sie Ihr vollständiges Contoso-Dashboard an. 

    :::image type="content" source="media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png" alt-text="Azure IoT Central: Abgeschlossene Dashboardanpassung.":::

1. Optional können Sie die Kachel **Neustart** auswählen, um den Neustartbefehl auf Ihrem Gateway auszuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Ändern des Dashboardnamens
* Anpassen von Bildkacheln auf dem Dashboard
* Anordnen von Kacheln zum Ändern des Layouts
* Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen
* Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails
* Hinzufügen von Befehlskacheln zum Ausführen von Befehlen

Nachdem Sie das Dashboard in Ihrer Azure IoT Central-Anwendung für die In-Store-Analyse angepasst haben, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Exportieren von Daten und Visualisieren von Erkenntnissen](./tutorial-in-store-analytics-export-data-visualize-insights.md)
