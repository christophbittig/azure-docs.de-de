---
title: Erstellen und Verwalten von Azure IoT Central-Dashboards | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anwendungs- und persönliche Dashboards in Azure IoT Central erstellen und verwalten.
author: dominicbetts
ms.author: dobett
ms.date: 08/19/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bcd9b0f4aa1b4a7f89479270ebce65fba9968e56
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810780"
---
# <a name="create-and-manage-dashboards"></a>Erstellen und Verwalten von Dashboards

Das Standard-*Organisationsdashboard* ist die Seite, die geladen wird, wenn Sie zum ersten Mal zu Ihrer Anwendung navigieren. Als Administrator können Sie zusätzliche Organisationsdashboards erstellen, die einer bestimmten Organisation zugeordnet werden. Ein Organisationsdashboard ist nur für Benutzer sichtbar, die auf die Organisation zugreifen können, der das Dashboard zugeordnet wurde. Nur Benutzer in einer Rolle mit [Organisationsdashboardberechtigungen](howto-manage-users-roles.md#customizing-the-app) können Organisationsdashboards erstellen, bearbeiten und löschen.

> [!TIP]
> In den Dashboardeinstellungen können Sie sehen, welcher Organisation ein Dashboard zugeordnet wurde.

Alle Benutzer können eigene, *persönliche Dashboards* erstellen, und sie können zwischen Organisationsdashboards und persönlichen Dashboards wechseln.

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Wenn Ihre Rolle die entsprechenden Berechtigungen enthält, können Sie das Standarddashboard anpassen. Wählen Sie zum Erstellen eines neuen Dashboards oben links auf der Seite die Option **+ Neues Dashboard** aus:

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="Screenshot: Schaltfläche „Neues Dashboard“.":::

Geben Sie ihrem Dashboard im Bereich **Dashboard erstellen** einen Namen, und wählen Sie entweder **Organisation** oder **Persönlich** als Dashboardtyp aus. Wenn Sie ein Organisationsdashboard erstellen, wählen Sie die [Organisation](howto-create-organizations.md) aus, der das Dashboard zugeordnet wird. Ein Organisationsdashboard und die zugehörigen Kacheln zeigen nur die Geräte, die für die Organisation und deren Unterorganisationen sichtbar sind.

Nachdem Sie das Dashboard erstellt haben, wählen Sie die Elemente aus der Bibliothek aus, die Sie dem Dashboard hinzufügen möchten. Die Bibliothek enthält die Kacheln und Dashboardprimitive, mit denen Sie das Dashboard anpassen:

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="Screenshot: Dashboardbibliothek":::

Als Administrator können Sie ein persönliches Dashboard oder ein Organisationsdashboard erstellen. Benutzern werden die Organisationsdashboards angezeigt, die der Organisation zugeordnet sind, der sie zugewiesen wurden. Alle Benutzer können persönliche Dashboards erstellen, die nur ihnen angezeigt werden.

Geben Sie einen Titel ein, und wählen Sie den Typ des Dashboards aus, das Sie erstellen möchten. [Fügen Sie Kacheln hinzu](#add-tiles), um Ihr Dashboard anzupassen.

> [!TIP]
> Sie benötigen mindestens eine Gerätevorlage in Ihrer Anwendung, um Kacheln hinzufügen zu können, die Geräteinformationen anzeigen.

## <a name="manage-dashboards"></a>Dashboards verwalten

Sie können mehrere persönliche Dashboards haben und dazwischen wechseln oder eines der Organisationsdashboards auswählen:

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="Screenshot: Wechseln zwischen Dashboards":::

Sie können Ihre persönlichen Dashboards bearbeiten und die nicht mehr benötigten löschen. Wenn Sie die richtigen [Berechtigungen](howto-manage-users-roles.md#customizing-the-app) haben, können Sie auch Organisationsdashboards bearbeiten oder löschen.

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="Screenshot: Löschvorgang von Dashboards.":::

Wenn Sie ein Dashboard umbenennen oder die Organisation anzeigen möchten, der es zugewiesen ist, wählen Sie **Dashboardeinstellungen** aus:

:::image type="content" source="media/howto-manage-dashboards/rename-dashboards.png" alt-text="Screenshot: Umbenennen eines Dashboards":::

## <a name="add-tiles"></a>Hinzufügen von Kacheln

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Wählen Sie **Bearbeiten** aus, um das aktuelle Dashboard anzupassen. Wählen Sie **Neues Dashboard** aus, um ein persönliches Dashboard oder ein Organisationsdashboard hinzuzufügen:

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="Screenshot: Dashboard für Anwendungen, die auf der Vorlage „Benutzerdefinierte Anwendung“ basieren.":::

Nachdem Sie auf **Bearbeiten** oder **Neues Dashboard** geklickt haben, befindet sich das Dashboard im *Bearbeitungsmodus*. Sie können die Tools im Panel **Dashboard bearbeiten** verwenden, um dem Dashboard Kacheln hinzuzufügen. Sie können Kacheln auf dem Dashboard selbst anpassen und entfernen. So fügen Sie beispielsweise eine Kachel vom Typ Liniendiagramm hinzu, um Telemetriewerte im zeitlichen Verlauf zu verfolgen, die von einem oder mehreren Geräten gemeldet werden:

1. Wählen Sie **Mit einem Steuerelement beginnen**, **Liniendiagramm** und dann **Kachel hinzufügen** aus, oder ziehen Sie die Kachel einfach auf das Canvas-Panel.
 
1. Um die Kachel zu konfigurieren, wählen Sie das zugehörige **Zahnradsymbol** aus. Geben Sie einen **Titel** ein, und wählen Sie eine **Gerätegruppe** aus. Wählen Sie in der Liste **Geräte** die Geräte aus, die auf der Kachel angezeigt werden sollen.

   :::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="Screenshot: Hinzufügen einer Kachel zu einem Dashboard.":::

1. Nachdem Sie alle Geräte ausgewählt haben, die auf der Kachel angezeigt werden sollen, wählen Sie **Aktualisieren** aus.

1. Nachdem Sie das Hinzufügen und Anpassen der Kacheln auf dem Dashboard abgeschlossen haben, klicken Sie auf **Speichern**. Dadurch wird der Bearbeitungsmodus beendet.

## <a name="customize-tiles"></a>Anpassen von Kacheln

Wenn Sie eine Kachel bearbeiten möchten, müssen Sie sich im Bearbeitungsmodus befinden. Die verschiedenen [Kacheltypen](#tile-types) haben unterschiedliche Anpassungsoptionen:

* Über das **Linealsymbol** auf einer Kachel können Sie die Visualisierung ändern. Zu den Visualisierungen gehören Linien-, Balken- und Kreisdiagramme, letzter bekannter Wert, KPI (Key Performance Indicator), Wärmebilder und Karten.

* Über das **quadratische** Symbol können Sie die Größe der Kachel ändern.

* Das **Zahnradsymbol** ermöglicht die Konfiguration der Visualisierung. Für ein Liniendiagramm können Sie beispielsweise die Legende und Achsen anzeigen und den Zeitbereich auswählen, der dargestellt werden soll.

## <a name="tile-types"></a>Kacheltypen

Die Tabelle beschreibt die Typen von Kacheln, die Sie einem Dashboard hinzufügen können:

| Kachel             | BESCHREIBUNG |
| ---------------- | ----------- |
| Markdown         | Kacheln mit Unterstützung für Markdown sind Kacheln, auf die geklickt werden kann und in denen Überschriften und Beschreibungstext in Markdown angezeigt werden. Die URL kann ein relativer Link zu einer anderen Seite der Anwendung sein, oder ein absoluter Link zu einer externen Website.|
| Image            | Auf Bildkacheln, auf denen ein benutzerdefiniertes Bild angezeigt wird, kann geklickt werden. Die URL kann ein relativer Link zu einer anderen Seite der Anwendung sein, oder ein absoluter Link zu einer externen Website.|
| Bezeichnung            | Auf Kacheln mit einer Beschriftung auf einem Dashboard wird benutzerdefinierter Text angezeigt. Sie können die Größe des Texts festlegen. Auf einer Kachel mit Beschriftung können Sie dem Dashboard relevante Informationen wie Beschreibungen, Kontaktinformationen oder Hilfe hinzufügen.|
| Anzahl            | Diese Kachel zeigt die Anzahl der Geräte in einer Gerätegruppe an.|
| Karte (Telemetrie)              | Kartenkacheln zeigen den Standort eines oder mehrerer Geräte auf einer Karte an. Sie können bis zu 100 Punkte im Standortverlauf eines Geräts anzeigen. Sie können beispielsweise ein Routenmuster der Orte anzeigen, an denen sich ein Gerät in der vergangenen Woche befand.|
| Karte (Eigenschaft)              | Kartenkacheln zeigen den Standort eines oder mehrerer Geräte auf einer Karte an.|
| KPI              |  KPI-Kacheln zeigen aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Damit können Sie beispielsweise die Maximaltemperatur und den Druck eines oder mehrerer Geräte in der letzten Stunde anzeigen.|
| Liniendiagramm       | Liniendiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Sie können beispielsweise ein Liniendiagramm anzeigen, das die durchschnittliche Temperatur und den durchschnittlichen Druck eines oder mehrere Geräte in der letzten Stunde anzeigt.|
| Balkendiagramm        | Balkendiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Sie können beispielsweise ein Balkendiagramm anzeigen, das die durchschnittliche Temperatur und den durchschnittlichen Druck eines oder mehrerer Geräte in der letzten Stunde anzeigt.|
| Kreisdiagramm        | Kreisdiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an.|
| Heat Map         | Auf Wärmebildkacheln werden Informationen zu einem oder mehreren Geräten farblich dargestellt.|
| Letzter bekannter Wert | Kacheln mit letzten bekannten Werten zeigen die aktuellen Telemetriewerte für ein oder mehrere Geräte an. Auf dieser Kachel können Sie beispielsweise die aktuellen Messungen von Temperatur, Druck und Feuchtigkeit für ein oder mehrere Gerät anzeigen. |
| Ereignisverlauf    | Kacheln mit dem Ereignisverlauf zeigen die Ereignisse für ein Gerät über einen bestimmten Zeitraum hinweg an. Auf dieser Kachel können Sie beispielsweise alle Öffnungs- und Schließereignisse der Ventile für ein oder mehrere Geräte während der letzten Stunde anzeigen.|
| Eigenschaft         |  Auf Eigenschaftenkacheln werden die aktuellen Werte für Eigenschaften und Cloudeigenschaften eines oder mehrerer Geräts angezeigt. Sie können diese Kachel z. B. verwenden, um Geräteeigenschaften wie den Hersteller oder die Firmwareversion eines Geräts anzuzeigen. |
| Statusübersicht         |  Statusübersichtskacheln stellen Änderungen für mindestens ein Gerät über einen bestimmten Zeitraum hinweg dar. Mithilfe dieser Kachel können Sie beispielsweise Eigenschaften wie Temperaturänderungen für ein Gerät anzeigen. |
| Ereignisdiagramm         |  Ereignisdiagrammkacheln zeigen Telemetrieereignisse für mindestens ein Gerät über einen bestimmten Zeitraum hinweg an. Mithilfe dieser Kachel können Sie beispielsweise Eigenschaften wie Temperaturänderungen für ein Gerät anzeigen. |
| Statusverlauf         |  Der Zustandsverlauf listet Statusänderungen für Zustandstelemetrie auf und zeigt diese an.|
| Externer Inhalt         |  Mithilfe von Kacheln mit externem Inhalt können Sie Inhalte aus einer externen Quelle laden. |

Aktuell können Sie Kacheln, die mehrere Geräte unterstützen, bis zu zehn Geräte hinzufügen.

### <a name="customize-visualizations"></a>Anpassen von Visualisierungen

Liniendiagramme zeigen standardmäßig Daten über einen Zeitraum hinweg. Der ausgewählte Zeitbereich wird in 50 Partitionen gleicher Größe unterteilt. Die Gerätedaten werden dann pro Partition aggregiert, um 50 Datenpunkte über den ausgewählten Zeitbereich zu erhalten. Wenn Sie Rohdaten anzeigen möchten, können Sie Ihre Auswahl so ändern, dass die letzten 100 Werte angezeigt werden. Wenn Sie den Zeitbereich ändern oder eine Visualisierung für Rohdaten auswählen möchten, verwenden Sie die Dropdownliste **Anzeigebereich** des **Panel Diagramm konfigurieren**:

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="Screenshot: Dropdownliste „Anzeigebereich“.":::

Klicken Sie bei Kacheln, die aggregierte Werte anzeigen, auf das **Zahnradsymbol** neben dem Telemetrietyp im Bereich **Diagramm konfigurieren**, um die Aggregation auszuwählen. Zur Verfügung stehen die Aggregationen für den Durchschnitt, die Summe, das Maximum, das Minimum oder die Anzahl.

Bei Liniendiagrammen, Balkendiagrammen und Kreisdiagrammen können Sie die Farbe der verschiedenen Telemetriewerte anpassen. Klicken Sie hierzu auf das **Farbpalettensymbol** neben den Telemetriedaten, die Sie anpassen möchten:

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="Screenshot: Schaltfläche „Farbpalette“.":::

Bei Kacheln, die Zeichenfolgeneigenschaften oder Telemetriewerte zeigen, können Sie auswählen, wie der Text angezeigt werden soll. Wenn das Gerät beispielsweise eine URL in einer Zeichenfolgeneigenschaft speichert, können Sie sie als Link anzeigen, auf den geklickt werden kann. Wenn die URL auf ein Bild verweist, können Sie das Bild in einer Kachel mit dem letzten bekannten Wert oder einer Eigenschaftenkachel rendern. Wenn Sie ändern möchten, wie eine Zeichenfolge angezeigt wird, klicken Sie in der Kachelkonfiguration auf das **Zahnradsymbol** neben dem Telemetrietyp oder der Eigenschaft in der Kachelkonfiguration.

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="Screenshot: Ändern der Anzeige einer Zeichenfolge auf einer Kachel.":::

Für die numerischen Kacheln „KPI“, „Letzter bekannter Wert“ und für Eigenschaftenkacheln können Sie mithilfe einer bedingten Formatierung die Farbe der jeweiligen Kachel basierend auf ihrem Wert anpassen. Um eine bedingte Formatierung anzuwenden, wählen Sie die Kachel **Konfigurieren** und dann neben dem von Ihnen anzupassenden Wert das Symbol **Bedingte Formatierung** aus:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="Screenshot: Schaltfläche „Bedingte Formatierung“.":::

Fügen Sie anschließend Ihre Regeln für bedingte Formatierung hinzu:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="Screenshot: Regeln für die bedingte Formatierung für den verfügbaren Arbeitsspeicher Es gibt Regeln für kleiner als, größer als und größer als oder gleich.":::

Der folgende Screenshot zeigt die Auswirkung von Regeln für bedingte Formatierung:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="Screenshot: violette Hintergrundfarbe auf der verfügbaren Speicherkachel.":::

### <a name="tile-formatting"></a>Kachelformatierung

Dieses Feature ist auf den Kacheln KPI, LKV und Eigenschaften verfügbar. Mit diesem Feature können Sie den Schriftgrad anpassen, die dezimale Genauigkeit auswählen, numerische Werte abkürzen (z. B. kann 1.700 als 1,7 Tsd. abgekürzt werden) oder Zeichenfolgenwerte in den dazugehörigen Kacheln einschließen.

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="Screenshot: Dialogfeld für die Kachelformatierung.":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie persönliche Dashboards erstellen und verwalten, können Sie als Nächstes [erfahren, wie Sie Ihre Anwendungseinstellungen verwalten](howto-manage-preferences.md).
