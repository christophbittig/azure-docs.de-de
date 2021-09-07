---
title: Erstellen und Verwalten von Azure IoT Central-Dashboards | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anwendungs- und persönliche Dashboards erstellen und verwalten.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6c14cc00843a998990c89e7892a793d10cfdbbdf
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650913"
---
# <a name="create-and-manage-dashboards"></a>Erstellen und Verwalten von Dashboards

Das *Standardanwendungsdashboard* ist die Seite, die geladen wird, wenn Sie zum ersten Mal zu Ihrer Anwendung navigieren. Als Administrator können Sie bis zu 10 Anwendungsdashboards erstellen, die für alle Anwendungsbenutzer sichtbar sind. Nur Administratoren können Dashboards auf Anwendungsebene erstellen, bearbeiten und löschen.

Alle Benutzer können eigene, *persönliche Dashboards* erstellen, und sie können zwischen Anwendungsdashboards und persönlichen Dashboards wechseln.

## <a name="create-dashboard"></a>Erstellen von Dashboards

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Sie können das Standardanwendungsdashboard durch ein persönliches Dashboard oder – als Administrator – durch ein anderes Dashboard auf Anwendungsebene ersetzen. Wählen Sie dazu oben links auf der Seite **+ Neues Dashboard** aus:

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="Screenshot: Dashboard für Anwendungen, die auf der Vorlage „Benutzerdefinierte Anwendung“ basieren":::

Wählen Sie **+ Neues Dashboard** aus, um den Dashboard-Editor zu öffnen. Im Editor können Sie Ihrem Dashboard einen Namen geben und Elemente aus der Bibliothek auswählen. Die Bibliothek enthält die Kacheln und Dashboardprimitive, mit denen Sie das Dashboard anpassen können:

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="Screenshot: Dashboardbibliothek":::

Als Administrator können Sie ein persönliches Dashboard oder ein Anwendungsdashboard erstellen. Alle Anwendungsbenutzer können die Anwendungsdashboards sehen, die der Administrator erstellt. Alle Benutzer können persönliche Dashboards erstellen, die nur ihnen angezeigt werden.

Geben Sie einen Titel ein, und wählen Sie den Typ des Dashboards aus, das Sie erstellen möchten. [Fügen Sie Kacheln hinzu](#add-tiles), um Ihr Dashboard anzupassen.

> [!TIP]
> Sie benötigen mindestens eine Gerätevorlage in Ihrer Anwendung, um Kacheln hinzufügen zu können, die Geräteinformationen anzeigen.

## <a name="manage-dashboards"></a>Dashboards verwalten

Sie können über mehrere persönliche Dashboards verfügen und zwischen diesen wechseln oder eines der Standardanwendungsdashboards auswählen:

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="Screenshot: Wechseln zwischen Dashboards":::

Sie können Ihre persönlichen Dashboards bearbeiten und die nicht mehr benötigten löschen. Wenn Sie ein **Administrator** sind, können Sie auch Dashboards auf Anwendungsebene bearbeiten oder löschen.

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="Screenshot: Löschen von Dashboards":::

## <a name="add-tiles"></a>Hinzufügen von Kacheln

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Wählen Sie **Bearbeiten** aus, um das aktuelle Dashboard anzupassen. Wählen Sie **Neu** aus, um ein persönliches Dashboard oder ein Anwendungsdashboard hinzuzufügen:

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="Dashboard für Anwendungen, die auf der Vorlage „Benutzerdefinierte Anwendung“ basieren":::

Nachdem Sie auf **Bearbeiten** oder **Neu** geklickt haben, befindet sich das Dashboard im *Bearbeitungsmodus*. Sie können die Tools im Bereich **Dashboard bearbeiten** verwenden, um dem Dashboard Kacheln hinzuzufügen und Kacheln auf dem Dashboard selbst anzupassen und von dort zu entfernen. So fügen Sie beispielsweise eine Kachel vom Typ **Liniendiagramm** hinzu, um Telemetriewerte im zeitlichen Verlauf zu verfolgen, die von einem oder mehreren Geräten gemeldet werden:

1. Wählen Sie **Mit Visual beginnen** und dann **Liniendiagramm** aus, und klicken Sie anschließend auf **Kachel hinzufügen**, oder verschieben Sie die Kachel per Drag & Drop auf die Canvas.
 
1. Um die Kachel zu konfigurieren, wählen Sie das zugehörige Zahnradsymbol aus. Geben Sie einen **Titel** ein, wählen Sie eine **Gerätegruppe** und dann Ihre Geräte in der Dropdownliste **Geräte** aus, damit sie auf der Kachel angezeigt werden.

:::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="Hinzufügen einer Kachel mit Temperaturtelemetriedaten zum Dashboard":::

Wenn Sie alle Werte ausgewählt haben, die auf der Kachel angezeigt werden sollen, klicken Sie auf **Aktualisieren**.

Wenn Sie das Hinzufügen und Anpassen von Kacheln zu Ihrem Dashboard abgeschlossen haben, klicken Sie auf **Speichern**, damit die Änderungen am Dashboard gespeichert werden. Dadurch verlassen Sie den Bearbeitungsmodus.

## <a name="customize-tiles"></a>Anpassen von Kacheln

Wenn Sie eine Kachel bearbeiten möchten, müssen Sie sich im Bearbeitungsmodus befinden. Die verfügbaren Anpassungsoptionen hängen vom [Kacheltyp](#tile-types) ab:

* Über das Linealsymbol auf einer Kachel können Sie die Visualisierung ändern. Zu den Visualisierungen gehören Linien-, Balken- und Kreisdiagramme, letzter bekannter Wert, KPI (Key Performance Indicator), Wärmebilder und Karten.

* Über das quadratische Symbol können Sie die Größe der Kachel ändern.

* Das Zahnradsymbol ermöglicht die Konfiguration der Visualisierung. Für ein Liniendiagramm können Sie beispielsweise die Legende und Achsen anzeigen und den Zeitbereich auswählen, der dargestellt werden soll.

## <a name="tile-types"></a>Kacheltypen

Die folgende Tabelle beschreibt die verschiedenen Typen von Kacheln, die Sie einem Dashboard hinzufügen können:

| Kachel             | BESCHREIBUNG |
| ---------------- | ----------- |
| Markdown         | Kacheln mit Unterstützung für Markdown sind Kacheln, auf die geklickt werden kann und in denen Überschriften und Beschreibungstext mithilfe von Markdown angezeigt werden. Die URL kann ein relativer Link zu einer anderen Seite der Anwendung sein, oder ein absoluter Link zu einer externen Website.|
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
| Ereignisverlauf    | Auf einer Kachel mit dem Ereignisverlauf werden die Ereignisse für ein Gerät in einem Zeitraum gezeigt. Auf dieser Kachel können Sie beispielsweise alle Öffnungs- und Schließereignisse der Ventile für ein oder mehrere Geräte während der letzten Stunde anzeigen.|
| Eigenschaft         |  Auf Eigenschaftenkacheln wird der aktuelle Wert für Eigenschaften und Cloudeigenschaften eines oder mehrerer Geräts angezeigt. Sie können diese Kachel z. B. verwenden, um Geräteeigenschaften wie den Hersteller oder die Firmwareversion eines Geräts anzuzeigen. |
| Statusübersicht         |  Zustandsdiagramme stellen Änderungen für mindestens ein Gerät über einen festgelegten Zeitbereich dar. Mithilfe dieser Kachel können Sie beispielsweise Geräteeigenschaften wie Temperaturänderungen für ein Gerät anzeigen. |
| Ereignisdiagramm         |  Ereignisdiagramme zeigen Telemetrieereignisse für mindestens ein Gerät über einen festgelegten Zeitbereich an. Mithilfe dieser Kachel können Sie beispielsweise Eigenschaften wie Temperaturänderungen für ein Gerät anzeigen. |
| Der Statusverlauf         |  Der Zustandsverlauf listet Statusänderungen für Zustandstelemetrie auf und zeigt diese an.|
| Externer Inhalt         |  Mithilfe der Kachel „Externer Inhalt“ können Sie externe Inhalte aus einer externen Quelle laden. |

Aktuell können Sie Kacheln, die mehrere Geräte unterstützen, bis zu zehn Geräte hinzufügen.

### <a name="customizing-visualizations"></a>Anpassen von Visualisierungen

Liniendiagramme zeigen standardmäßig Daten über einen Zeitraum hinweg. Der gewählte Zeitraum wird in 50 Buckets gleicher Größe aufgeteilt, und die Gerätedaten werden dann pro Bucket aggregiert, sodass für den ausgewählten Zeitbereich 50 Datenpunkte entstehen. Wenn Sie Rohdaten anzeigen möchten, können Sie Ihre Auswahl so ändern, dass die letzten 100 Werte angezeigt werden. Wenn Sie den Zeitbereich ändern oder eine Visualisierung für Rohdaten auswählen möchten, verwenden Sie das Dropdown „Anzeigebereich“ des Panel **Diagramm konfigurieren**.

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="Ändern des Anzeigebereichs eines Liniendiagramms":::

Klicken Sie bei Kacheln, die aggregierte Werte anzeigen, auf das Zahnradsymbol neben dem Telemetrietyp im Bereich **Diagramm konfigurieren**, um die Aggregation auszuwählen. Zur Verfügung stehen die Aggregationen für den Durchschnitt, die Summe, das Maximum, das Minimum und die Anzahl.

Bei Liniendiagrammen, Balkendiagrammen und Kreisdiagrammen können Sie die Farbe der verschiedenen Telemetriewerte anpassen. Klicken Sie hierzu auf das Farbpalettensymbol neben den Telemetriedaten, die Sie anpassen möchten:

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="Ändern der Farbe eines Telemetriewerts":::

Bei Kacheln, die Zeichenfolgeneigenschaften oder Telemetriewerte zeigen, können Sie auswählen, wie der Text angezeigt werden soll. Wenn das Gerät beispielsweise eine URL in einer Zeichenfolgeneigenschaft speichert, können Sie sie als Link anzeigen, auf den geklickt werden kann. Wenn die URL auf ein Bild verweist, können Sie das Bild in einer Kachel mit dem letzten bekannten Wert oder einer Eigenschaftenkachel rendern. Wenn Sie ändern möchten, wie eine Zeichenfolge angezeigt wird, klicken Sie in der Kachelkonfiguration auf das Zahnradsymbol neben dem Telemetrietyp oder der Eigenschaft:

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="Ändern der Anzeige einer Zeichenfolge auf einer Kachel":::

Für die numerischen Kacheln „KPI“, „Letzter bekannter Wert“ und für Eigenschaftenkacheln können Sie mithilfe einer bedingten Formatierung die Farbe der jeweiligen Kachel basierend auf ihrem aktuellen Wert anpassen. Um eine bedingte Formatierung anzuwenden, wählen Sie die Kachel **Konfigurieren** und dann neben dem anzupassenden Wert das Symbol **Bedingte Formatierung** aus:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="Screenshot der Option „Konfiguration“ für eine Kachel und des Symbols für die bedingte Formatierung":::

Fügen Sie Ihre Regeln für bedingte Formatierung hinzu:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="Screenshot von Regeln für bedingte Formatierung für den durchschnittlichen Fluss. Es gibt drei Regeln: „Kleiner als 20“ wird grün dargestellt, „Kleiner als 50“ gelb und „Größer als 50“ rot.":::

Der folgende Screenshot zeigt die Auswirkung der Regel für bedingte Formatierung:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="Screenshot der Kachel für durchschnittlichen Wasserfluss mit roter Hintergrundfarbe. Die Zahl auf der Kachel lautet 50,54.":::

### <a name="tile-formatting"></a>Kachelformatierung

Mit diesem Feature, das in KPI, LKV und auf Eigenschaftenkacheln verfügbar ist, können Benutzer den Schriftgrad anpassen, die dezimale Genauigkeit auswählen, numerische Werte abkürzen (z. B. kann 1.700 als 1,7 Tsd. abgekürzt werden) oder Zeichenfolgenwerte in den dazugehörigen Kacheln einschließen.

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="Kachelformat":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie persönliche Dashboards erstellen und verwalten, können Sie als Nächstes [erfahren, wie Sie Ihre Anwendungseinstellungen verwalten](howto-manage-preferences.md).
