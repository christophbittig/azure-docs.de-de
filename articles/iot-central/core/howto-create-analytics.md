---
title: Analysieren von Gerätedaten in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Analysieren Sie Gerätedaten in Ihrer Azure IoT Central-Anwendung.
author: dominicbetts
ms.author: dobett
ms.date: 08/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ad16a831b22097e98bd02b606e3a6596cf4e48f5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810939"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Analysieren von Gerätedaten mithilfe von Analytics

Azure IoT Central bietet umfassende Analysefunktionen, mit denen Sie historische Trends analysieren und Telemetriedaten von Ihren Geräten korrelieren können. Zum Starten dieses Vorgangs wählen Sie im linken Bereich **Analysen** aus.

## <a name="understand-the-analytics-ui"></a>Grundlegendes zur Benutzeroberfläche für Analysen

Die Benutzeroberfläche für Analysen enthält drei Hauptkomponenten:

- **Datenkonfigurationsbereich:** Wählen Sie im Konfigurationsbereich zuerst die Gerätegruppe aus, bei der Sie die Daten analysieren möchten. Wählen Sie als Nächstes die Telemetriedaten, die Sie analysieren möchten, und dann die Aggregationsmethode für die einzelnen Telemetriedaten aus. Mit dem Steuerelement **Gruppieren nach** können Sie die Daten gruppieren, indem Sie die Geräteeigenschaften als Dimensionen verwenden.

    > [!TIP]
    > Wenn Ihr Gerät Organisationen verwendet, hängen die angezeigten Gerätegruppen von Ihrer Organisationsmitgliedschaft ab.

- **Zeitsteuerelement:** Mithilfe des Zeitsteuerelements können Sie die Dauer auswählen, für die Sie die Daten analysieren möchten. Sie können eines der Enden des Schiebereglers ziehen, um den gewünschten Zeitraum auszuwählen. Das Zeitsteuerelement enthält auch den Schieberegler **Intervallgröße**, über den der Bucket oder die Intervallgröße zum Aggregieren der Daten gesteuert wird.

- **Diagrammsteuerelement:** Das Diagrammsteuerelement visualisiert die Daten als Liniendiagramm. Durch Interaktion mit der Diagrammlegende können Sie die Sichtbarkeit bestimmter Linien umschalten.

  :::image type="content" source="media/howto-create-analytics/analytics-ui.png" alt-text="Screenshot: Die drei Bereiche der Benutzeroberfläche für Analysen":::

## <a name="query-your-data"></a>Abfragen Ihrer Daten

Wählen Sie zuerst eine **Gerätegruppe** und dann die Telemetriedaten aus, die Sie analysieren möchten. Wählen Sie anschließend **Analysieren** aus, um mit der Visualisierung Ihrer Daten zu beginnen:

- **Gerätegruppe:** Eine [Gerätegruppe](tutorial-use-device-groups.md) ist eine benutzerdefinierte Gruppe Ihrer Geräte. Beispiele: **Alle Kühlschränke in Oakland** oder **Alle Windturbinen der Version 2.0**.

- **Telemetrie:** Wählen Sie die Telemetriedaten aus, die Sie analysieren und untersuchen möchten. Sie können mehrere Telemetrietypen zum gemeinsamen Analysieren auswählen. Die Standardaggregationsmethode für den Datentyp „Numeric“ (Numerisch) ist auf **Average** (Durchschnitt) und für den Datentyp „String“ (Zeichenfolge) auf **Count** (Anzahl) festgelegt. Die Aggregationsmethoden für den Datentyp „Numeric“ sind **Average**, **Maximum**, **Minimum**, **Count** und **Sum** (Summe). **Count** ist die einzige Aggregationsmethode für Zeichenfolgen.

    > [!NOTE]
    > Historische Datenpunkte werden nur angezeigt, wenn die Bedingungen der Abfrage erfüllt sind. Beispiel: Gestern wurde ein Gerät von **Vorlage1** auf **Vorlage2** aktualisiert. Wenn Sie heute Gerätegruppen mit Geräten des Typs **Vorlage1** abfragen, werden Gerätedaten von gestern und davor angezeigt. Wenn Sie Gerätegruppen mit Geräten des Typs **Vorlage2** abfragen, werden das Gerät und die Daten ab dem Zeitpunkt der Aktualisierung angezeigt.

- **Gruppieren nach**: Mit dem Steuerelement **Gruppieren nach** können Sie die Daten gruppieren, indem Sie die Geräteeigenschaften als Dimensionen verwenden. Gerätetelemetrie und -eigenschaften werden mit Cloudeigenschaften kombiniert, wenn das Gerät Daten sendet. Wenn die Cloud- oder Geräteeigenschaft aktualisiert wird, werden die Telemetriedaten – nach verschiedenen Werten gruppiert – im Diagramm angezeigt.

    > [!TIP]
    > Wenn Sie die Daten für jedes Gerät getrennt anzeigen möchten, wählen Sie im Steuerelement **Gruppieren nach** die Option **Geräte-ID** aus.

## <a name="interact-with-your-data"></a>Interagieren Sie mit Ihren Daten

Nachdem Sie Ihre Daten abgefragt haben, können Sie sie im Liniendiagramm visualisieren. Sie können Telemetriedaten ein- oder ausblenden, die Dauer ändern oder die Daten in einem Raster anzeigen.

- **Editor-Bereich für Zeiten:** Standardmäßig werden Daten ab dem vorherigen Tag angezeigt. Sie können eines der Enden des Schiebereglers ziehen, um die Zeitdauer zu ändern. Außerdem können Sie mithilfe des Kalendersteuerelements einen der vordefinierten Zeitrahmen oder einen benutzerdefinierten Zeitbereich auswählen. Das Zeitsteuerelement enthält auch den Schieberegler **Intervallgröße**, über den die Intervallgröße zum Aggregieren der Daten gesteuert wird.

  :::image type="content" source="media/howto-create-analytics/time-editor-panel.png" alt-text="Screenshot: Der Editor-Bereich für Zeiten":::

  - **Schieberegler für den inneren Datumsbereich:** Verwenden Sie die beiden Endpunktsteuerelemente zum Hervorheben des gewünschten Zeitraums. Der innere Datumsbereich wird durch den Schieberegler für den äußeren Datumsbereich begrenzt.
  
  - **Schieberegler für den äußeren Datumsbereich**: Verwenden Sie die Endpunktsteuerelemente zum Auswählen des äußeren Datumsbereichs, der dann für das Steuerelement für den inneren Datumsbereich zur Verfügung steht.

  - **Schaltflächen zum Erhöhen/Verringern des Datumsbereichs**: Sie können Ihren Zeitraum erhöhen oder verringern, indem Sie eine der Schaltflächen für das gewünschte Intervall auswählen.

  - **Schieberegler für die Intervallgröße**: Mit dem Schieberegler für die Intervallgröße können Sie die Intervalle in einer Zeitspanne vergrößern und verkleinern. Dieses Steuerelement ermöglicht Ihnen eine präzisere Steuerung der Bewegung zwischen großen Zeitabschnitten. Sie können damit präzise, hochauflösende Ansichten Ihrer Daten anzeigen – sogar im Bereich von Millisekunden. Der Standardstartpunkt des Schiebereglers bietet Ihnen eine optimale Ansicht der Daten aus Ihrer Auswahl. In dieser Ansicht werden Auflösung, Abfragegeschwindigkeit und Granularität ausgeglichen.
  
  - **Datumsbereichsauswahl**: Mit diesem Steuerelement können Sie den gewünschten Datums- und Zeitbereich auswählen. Sie können das Steuerelement auch verwenden, um zwischen verschiedenen Zeitzonen zu wechseln. Wählen Sie **Speichern** aus, nachdem Sie die gewünschten Änderungen für Ihren aktuellen Arbeitsbereich vorgenommen haben.

  > [!TIP]
  > Die Intervallgröße wird – basierend auf dem ausgewählten Zeitraum – dynamisch bestimmt. Kleinere Zeiträume ermöglichen Ihnen eine Datenaggregation in sehr präzisen Intervallen von bis zu wenigen Sekunden.

- **Diagrammlegende:** Die Diagrammlegende zeigt die ausgewählten Telemetriedaten im Diagramm. Zeigen Sie mit dem Mauszeiger auf ein Element in der Legende, um es im Diagramm zu fokussieren. Wenn Sie **Gruppieren nach** verwenden, werden die Telemetriedaten nach den Werten der ausgewählten Dimension gruppiert. Sie können die Sichtbarkeit jedes Telemetrietyps umschalten oder auf den Gruppennamen klicken, um die Gruppensichtbarkeit umzuschalten.  

- **Steuerelement „y-Achsenformat“:** Der Modus „y-Achse“ durchläuft die verfügbaren Ansichtsoptionen für die y-Achse. Dieses Steuerelement steht nur zur Verfügung, wenn Sie mehrere Telemetrietypen visualisieren. Es gibt die folgenden drei Modi:

  - **Gestapelt:** Für jeden Telemetrietyp werden Diagramme als Stapel dargestellt, und jedes Diagramm hat eine eigene y-Achse. Dies ist der Standardmodus.
  - **Shared (Geteilt):** Für jeden Telemetrietyp wird ein Diagramm auf derselben y-Achse gezeichnet.
  - **Überlappung:** In diesem Modus können Sie mehrere Linien auf derselben y-Achse stapeln, wobei sich die Achsendaten je nach ausgewählter Linie ändern.

  :::image type="content" source="media/howto-create-analytics/y-axis-control.png" alt-text="Ein Screenshot mit hervorgehobenem y-Achsen-Steuerelement":::

- **Zoomsteuerelement:** Mit dem Zoomsteuerelement können Sie einen Drillvorgang in Ihre Daten durchführen. Wenn Sie einen Zeitraum finden, auf den Sie sich in Ihrem Ergebnissatz konzentrieren möchten, markieren Sie diesen Bereich mit dem Mauszeiger. Klicken Sie dann mit der rechten Maustaste auf den ausgewählten Bereich, und wählen Sie **Zoomen** aus.

  :::image type="content" source="media/howto-create-analytics/zoom.png" alt-text="Screenshot: Die Verwendung des Zoomsteuerelements":::

Wählen Sie die Auslassungspunkte aus, um weitere Diagrammsteuerelemente anzuzeigen:

- **Raster anzeigen:** Zeigen Sie Ihre Ergebnisse in einem Tabellenformat an, bei dem Sie den Wert für jeden einzelnen Datenpunkt sehen können.

- **Als CSV herunterladen:** Exportieren Sie Ihre Ergebnisse als eine durch Trennzeichen getrennte Datei (CSV-Datei). Die CSV-Datei enthält Daten für jedes Gerät. Die Ergebnisse werden auf der Grundlage des angegebenen Intervalls und Zeitrahmens exportiert.

- **Marker setzen:** Mithilfe des Steuerelements **Marker setzen** können Sie bestimmte Datenpunkte auf dem Diagramm verankern. Das ist hilfreich, wenn Sie versuchen, Daten für mehrere Linien über verschiedene Zeiträume hinweg zu vergleichen.

  :::image type="content" source="media/howto-create-analytics/additional-chart-controls.png" alt-text="Screenshot: Zugriff auf die zusätzlichen Diagrammsteuerelemente":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Ihre Daten mit den integrierten Analysefunktionen visualisieren können, sollten Sie sich im nächsten Schritt über das [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports](howto-export-data.md) informieren.
