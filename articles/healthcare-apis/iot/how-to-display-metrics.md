---
title: Anzeigen der Protokollierung von IoT-Connectormetriken – Azure Healthcare-APIs
description: In diesem Artikel wird erläutert, wie Sie IoT-Connectormetriken anzeigen.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 88e5310a53bfd5fed79ae0a9a6efc06d91fdb0cf
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933949"
---
# <a name="how-to-display-iot-connector-metrics"></a>Anzeigen von IoT-Connectormetriken

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie IoT-Connectormetriken im Azure-Portal anzeigen. 

## <a name="display-metrics"></a>Anzeigen von Metriken

1. Wählen Sie in Ihrem Azure Healthcare-APIs-Arbeitsbereich **ioT connectors (IoT-Connectors)** aus. 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="Wählen Sie die Schaltfläche IoT-Connectors aus." lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. Wählen Sie den IoT-Connector aus, für den Sie die Metriken anzeigen möchten.

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="Wählen Sie IoT-Connector aus, für den Sie Metriken anzeigen möchten." lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. Wählen Sie auf der Seite des IoT-Connectors die Option **Metriken** aus.

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="Wählen Sie die Schaltfläche Metriken." lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. Auf der Seite Metriken können Sie die Metriken erstellen, die Sie für Ihren IoT-Connector anzeigen möchten. In diesem Beispiel wählen wir die folgende Auswahl aus:

    * **Bereich** = IoT-Connectorname (**Standard**)
    * **Metriknamespace** = Standardmetriken (**Standard**) 
    * **Metrik** = IoT-Connectormetriken, die Sie anzeigen möchten. In diesem Beispiel wählen wir **Number of Incoming Messages (Anzahl eingehender Nachrichten) aus.**
    * **Aggregation** = Wie möchten Sie die Metriken anzeigen? In diesem Beispiel wählen wir **Count (Anzahl)** aus. 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="Wählen Sie anzuzeigende Metriken aus." lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. Wir sehen nun die IoT-Connectormetriken für **die Anzahl eingehender Nachrichten,** die auf dem Azure-Portal angezeigt werden.

    > [!TIP]
    > Sie können zusätzliche Metriken hinzufügen, indem Sie die Schaltfläche **Metrik hinzufügen** auswählen und Ihre Auswahl treffen.

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="Wählen Sie die Schaltfläche Metrik hinzufügen aus, um weitere Metriken hinzuzufügen." lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > Wenn Sie die Seite Metriken verlassen, gehen die Metrikeneinstellungen verloren und müssen neu erstellt werden. Wenn Sie Ihre IoT-Connectormetriken für die zukünftige Anzeige speichern möchten, können Sie sie als Kachel an ein Azure-Dashboard anheften.

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>Kachel "Metriken anheften" auf Azure-Portal Dashboard

1. Um die Kachel Metriken an ein Azure-Portal Dashboard anzuheften, wählen Sie die Schaltfläche **An Dashboard anheften** aus:

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="Wählen Sie die Schaltfläche An Dashboard anheften aus." lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. Wählen Sie das Dashboard aus, auf dem Sie IoT-Connectormetriken anzeigen möchten. In diesem Beispiel verwenden wir ein privates Dashboard mit dem Namen `IoT connector Metrics` . Wählen Sie **Anheften** aus, um dem Dashboard die Kachel Metriken hinzuzufügen.

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="Wählen Sie Dashboard und Schaltfläche Anheften aus, um den Anheftvorgang für das Dashboard abzuschließen." lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. Sie erhalten eine Bestätigung, dass die Kachel Metriken dem Dashboard erfolgreich hinzugefügt wurde.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="Die Kachel &quot;Metriken&quot; wurde erfolgreich an das Dashboard angeheftet." lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. Nachdem Sie eine erfolgreiche Bestätigung erhalten haben, wählen Sie **Dashboard** aus.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="Wählen Sie die Schaltfläche Dashboard aus." lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. Wählen Sie das Dashboard aus, an das Sie die Kachel Metriken angeheftet haben. In diesem Beispiel ist das Dashboard `IoT connector Metrics` . Auf dem Dashboard wird die Kachel Metriken des IoT-Connectors angezeigt, die Sie in den vorherigen Schritten erstellt haben.

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="Dashboard mit angehefteter Kachel &quot;Metriken&quot; des IoT-Connectors." lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

> [!TIP]
> Informationen zum Beheben häufiger Fehler und Probleme finden Sie im Leitfaden zur [Problembehandlung](./iot-troubleshoot-guide.md) für IoT-Connectors.

## <a name="conclusion"></a>Zusammenfassung 

Der Zugriff auf Metriken ist für die Überwachung und Problembehandlung von entscheidender Bedeutung.  Der IoT-Connector unterstützt Sie bei diesen Aktionen über Metriken. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die häufig gestellten Fragen zum IoT-Connector an.

>[!div class="nextstepaction"]
>[Häufig gestellte Fragen zum IoT-Connector](iot-connector-faqs.md)

(FHIR&#174;) ist eine registrierte Marke von HL7 und wird mit der Berechtigung von HL7 verwendet.
