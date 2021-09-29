---
title: Kennenlernen der Benutzeroberfläche von Azure IoT Central | Microsoft-Dokumentation
description: Machen Sie sich mit den wichtigsten Bereichen der Benutzeroberfläche von Azure IoT Central vertraut, die Sie zum Erstellen, Verwalten und Nutzen Ihrer IoT-Lösung verwenden.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 187db7eefb019584835bd7f67fdb8d6abd91f45c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083902"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Kennenlernen der Benutzeroberfläche von Azure IoT Central

In diesem Artikel wird die Benutzeroberfläche von Azure IoT Central vorgestellt. Über die Benutzeroberfläche können Sie eine Azure IoT Central-Anwendung und die damit verbundenen Geräte erstellen, verwalten und verwenden.

## <a name="iot-central-homepage"></a>IoT Central-Startseite

Auf der [IoT Central-Startseite](https://apps.azureiotcentral.com/) finden Sie weitere Informationen zu den aktuellen News und Features von IoT Central, zum Erstellen neuer Anwendungen und zum Anzeigen und Starten Ihrer vorhandenen Anwendung.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central-Startseite":::

### <a name="create-an-application"></a>Erstellen einer Anwendung

Im Abschnitt für die **Erstellung** können Sie die Liste mit den branchenrelevanten IoT Central-Vorlagen durchsuchen oder mithilfe einer Vorlage für eine benutzerdefinierte App von Grund auf neu beginnen.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central-Seite für die Erstellung":::

Weitere Informationen finden Sie in der Schnellstartanleitung [Erstellen einer Azure IoT Central-Anwendung](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Starten Ihrer Anwendung

Sie starten Ihre IoT Central-Anwendung, indem Sie zu der URL navigieren, die Sie während der App-Erstellung ausgewählt haben. Darüber hinaus können Sie im [App-Manager von IoT Central](https://apps.azureiotcentral.com/myapps) eine Liste mit allen Anwendungen anzeigen, auf die Sie Zugriff haben.

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="App-Manager von IoT Central":::

## <a name="navigate-your-application"></a>Navigieren in der Anwendung

In der IoT-Anwendung nutzen Sie den Bereich auf der linken Seite, um auf die verschiedenen Bereiche zuzugreifen. Sie können den linken Bereich erweitern oder reduzieren, indem Sie oben im Bereich das Symbol mit den drei Linien auswählen:

> [!NOTE]
> Welche Elemente im linken Bereich angezeigt werden, hängt von ihrer Benutzerrolle ab. Lesen Sie die weiteren Informationen zum [Verwalten von Benutzern und Rollen](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="Navigationsbereich auf der linken Seite":::

  :::column-end:::
  :::column span="2":::
     Unter **Dashboard** werden alle Anwendungsdashboards und persönlichen Dashboards angezeigt. 
     
     Unter **Geräte** können Sie all Ihre Geräte verwalten.

     Unter **Gerätegruppen** können Sie Sammlungen mit Geräten anzeigen und erstellen, die per Abfrage angegeben werden. Gerätegruppen werden durch die Anwendung zum Durchführen von Massenvorgängen verwendet.

     Unter **Regeln** können Sie Regeln zur Überwachung Ihrer Geräte erstellen und bearbeiten. Regeln werden anhand der Gerätedaten ausgewertet und dienen zum Auslösen von anpassbaren Aktionen.

     Unter **Analysen** stehen umfassende Analysefunktionen zur Verfügung, mit denen Sie historische Trends analysieren und verschiedene Telemetriedaten von Ihren Geräten korrelieren können.

     Unter **Aufträge** können Sie Ihre Geräte im großen Stil verwalten, indem Sie Massenvorgänge durchführen.

     Die Option **Gerätevorlagen** ermöglicht die Erstellung und Verwaltung der Merkmale von Geräten, für die eine Verbindung mit Ihrer Anwendung hergestellt wird.

     Unter **Datenexport** können Sie den fortlaufenden Export in externe Dienste konfigurieren, z. B. Speicher und Warteschlangen.

     Die Option **Verwaltung** ermöglicht Ihnen das Verwalten der Einstellungen, Anpassung, Abrechnung, Benutzer und Rollen Ihrer Anwendung.

     Unter **Meine Apps** können Sie zurück zum IoT Central-App-Manager wechseln.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Suche, Hilfe, Design und Support

Das obere Menü wird auf jeder Seite angezeigt:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central-Symbolleiste":::

* Wenn Sie nach Geräten suchen möchten, geben Sie einen Wert für die **Suche** ein.
* Wählen Sie zum Ändern der Sprache der Benutzeroberfläche oder des Designs das Symbol **Einstellungen** aus. Informieren Sie sich über das [Verwalten Ihrer Anwendungseinstellungen](howto-manage-preferences.md).
* Wenn Sie Hilfe oder Unterstützung benötigen, klicken Sie auf das Dropdownmenü **Hilfe**, um eine Liste mit Ressourcen anzuzeigen. Sie können [Informationen zu Ihrer Anwendung](howto-faq.yml#how-do-i-get-information-about-my-application-) über den Link **Informationen zu Ihrer App** abrufen.
* Wählen Sie das Symbol **Konto** aus, um sich von der Anwendung abzumelden.

Sie können zwischen einem hellen und einem dunklen Benutzeroberflächendesign wählen:

> [!NOTE]
> Die Option zum Wählen zwischen hellen und dunklen Designs ist nicht verfügbar, wenn Ihr Administrator ein benutzerdefiniertes Design für die Anwendung konfiguriert hat.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Screenshot: „Design auswählen“ in IoT Central":::

### <a name="dashboard"></a>Dashboard

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Screenshot: IoT Central-Dashboard":::

* Nach der Anmeldung bei Ihrer IoT Central-Anwendung wird als Erstes die Seite **Dashboard** angezeigt. Sie können mehrere Anwendungsdashboards erstellen und anpassen. Informieren Sie sich über das [Hinzufügen von Kacheln zu Ihrem Dashboard](howto-manage-dashboards.md).

* Persönliche Dashboards können auch erstellt werden, um die für Sie wichtigen Aspekte zu überwachen. Weitere Informationen finden Sie in der Anleitung zum [Erstellen und Verwalten mehrerer Dashboards](howto-manage-dashboards.md).

### <a name="devices"></a>Geräte

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Screenshot: Seite „Geräte“":::

Auf dieser Seite werden die Geräte in Ihrer IoT Central-Anwendung gruppiert nach _Gerätevorlage_ angezeigt.

* Eine Gerätevorlage definiert einen Gerätetyp, der eine Verbindung mit Ihrer Anwendung herstellen kann.
* Bei einem Gerät handelt es sich um ein echtes oder simuliertes Gerät in Ihrer Anwendung.

### <a name="device-groups"></a>Gerätegruppen

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Seite „Gerätegruppe“":::

Auf dieser Seite können Sie Gerätegruppen in Ihrer IoT Central-Anwendung erstellen und anzeigen. Mithilfe von Gerätegruppen können Sie Massenvorgänge in Ihrer Anwendung durchführen oder Daten analysieren. Weitere Informationen finden Sie im Artikel [Verwenden von Gerätegruppen in Ihrer Azure IoT Central-Anwendung](tutorial-use-device-groups.md).

### <a name="rules"></a>Regeln
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Screenshot: Seite „Regeln“":::

Auf dieser Seite können Sie Regeln basierend auf Gerätedaten anzeigen und erstellen. Bei Auslösung einer Regel können mehrere Aktionen ausgelöst werden, z. B. das Senden einer E-Mail oder das Aufrufen eines Webhooks. Weitere Informationen hierzu finden Sie im Tutorial zum [Konfigurieren von Regeln](tutorial-create-telemetry-rules.md).

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Screenshot: Seite „Analyse“":::

Analysen bieten umfassende Analysefunktionen, mit denen Sie historische Trends analysieren und verschiedene Telemetriedaten von Ihren Geräten korrelieren können. Weitere Informationen finden Sie im Artikel zum Thema [Erstellen einer Analyse für Ihre Azure IoT Central-Anwendung](howto-create-analytics.md).

### <a name="jobs"></a>Aufträge

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Seite „Aufträge“":::

Auf dieser Seite können Sie Aufträge anzeigen und erstellen, die für Massenvorgänge für die Geräteverwaltung auf Ihren Geräten verwendet werden können. Sie können Geräteeigenschaften und Einstellungen aktualisieren und Befehle für Gerätegruppen ausführen. Weitere Informationen finden Sie im Artikel [Ausführen eines Auftrags](howto-manage-devices-in-bulk.md).

### <a name="device-templates"></a>Gerätevorlagen

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Screenshot „Gerätevorlagen“":::

Auf der Seite „Gerätevorlagen“ können Sie Gerätevorlagen in der Anwendung anzeigen und erstellen. Weitere Informationen finden Sie im Tutorial [Define a new device type in your Azure IoT Central application](howto-set-up-template.md) (Definieren eines neuen Gerätetyps in Ihrer Azure IoT Central-Anwendung).

### <a name="data-export"></a>Datenexport

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Datenexport":::

Per Datenexport können Sie Datenströme zu externen Systemen einrichten. Weitere Informationen finden Sie im Artikel [Exportieren von Daten in Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Verwaltung

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Screenshot: IoT-Verwaltung":::

Auf der Seite „Verwaltung“ können Sie Ihre IoT Central-Anwendung konfigurieren und anpassen. Sie können den Anwendungsnamen, die URL und das Design ändern, Benutzer und Rollen verwalten, API-Token erstellen und Ihre Anwendung exportieren. Weitere Informationen finden Sie im Artikel [How to administer your application](howto-administer.md) (Verwalten Ihrer Anwendung).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über Azure IoT Central verschafft und sich mit dem Layout der Benutzeroberfläche vertraut gemacht haben, empfehlen wir, mit der Schnellstartanleitung zum [Erstellen einer Azure IoT Central-Anwendung](quick-deploy-iot-central.md) fortzufahren.
