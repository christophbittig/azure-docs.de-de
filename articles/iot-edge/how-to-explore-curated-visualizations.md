---
title: 'Erkunden kuratierter Visualisierungen: Azure IoT Edge'
description: Hier erfahren Sie, wie Sie integrierte IoT Edge-Metriken mithilfe von Azure-Arbeitsmappen visualisieren und erkunden.
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7be7260e993f9cc95b542406767d6794f18836f8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350095"
---
# <a name="explore-curated-visualizations-preview"></a>Erkunden kuratierter Visualisierungen (Vorschau)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Die vom IoT Edge-Gerät gesammelten Metriken können mithilfe von Azure Monitor-Arbeitsmappen direkt im Azure-Portal visualisiert und erkundet werden. Kuratierte Überwachungsarbeitsmappen für IoT Edge-Geräte werden in Form von öffentlichen Vorlagen bereitgestellt:

* Navigieren Sie für mit IoT Hub verbundene Geräte über das Blatt **IoT Hub** im Azure-Portal zur Seite **Arbeitsmappen** im Abschnitt **Überwachung**.
* Navigieren Sie für mit IoT Central verbundene Geräte über das Blatt **IoT Central** im Azure-Portal zur Seite **Arbeitsmappen** im Abschnitt **Überwachung**.

Die kuratierten Arbeitsmappen nutzen [integrierte Metriken](how-to-access-built-in-metrics.md) aus der IoT Edge-Runtime. Für diese Ansichten ist keinerlei Metrikinstrumentierung aus den Workloadmodulen erforderlich.

## <a name="access-curated-workbooks"></a>Zugreifen auf kuratierte Arbeitsmappen

Bei den Azure Monitor-Arbeitsmappen für IoT handelt es sich um eine Reihe von Vorlagen, die Sie entweder direkt zur Visualisierung Ihrer Metriken verwenden oder an Ihre Lösung anpassen können.

Gehen Sie wie folgt vor, um auf die kuratierten Arbeitsmappen zuzugreifen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub- oder IoT Central-Anwendung.

1. Wählen Sie im Menüabschnitt **Überwachung** die Option **Arbeitsmappen** aus.

1. Wählen Sie in der Liste mit den öffentlichen Vorlagen die Arbeitsmappe aus, die Sie erkunden möchten:

  * **IoT Edge-Bestandsansicht (Vorschau)**: Überwachen Sie Ihren Gerätebestand, und zeigen Sie Detailinformationen zu bestimmten Geräten an, um eine Integritätsmomentaufnahme zu erhalten.
  * **IoT Edge-Gerätedetails (Vorschau)**: Visualisieren Sie Gerätedetails zu Messaging, Modulen und Hostkomponenten eines IoT Edge-Geräts.
  * **IoT Edge-Integritätsmomentaufnahme (Vorschau)**: Zeigen Sie die Integrität eines Geräts basierend auf sechs gängigen Leistungsmetriken an. Um auf die Arbeitsmappe für Integritätsmomentaufnahmen zuzugreifen, beginnen Sie in der Arbeitsmappe mit der Bestandsansicht, und wählen Sie das Gerät aus, das Sie anzeigen möchten. Von der Arbeitsmappe mit der Bestandsansicht werden einige erforderliche Parameter an die Ansicht für Integritätsmomentaufnahmen übergeben.

Sie können die Arbeitsmappen selbst erkunden oder sich anhand der folgenden Abschnitte einen Überblick über die Arten von Daten und Visualisierungen der einzelnen Arbeitsmappen verschaffen.

## <a name="iot-edge-fleet-view-workbook"></a>Arbeitsmappe „IoT Edge-Bestandsansicht (Vorschau)“

Die Arbeitsmappe mit der Bestandsansicht enthält zwei Ansichten:

* In der Ansicht **Geräte** wird eine Übersicht über aktive Geräte angezeigt.
* In der Ansicht **Warnungen** werden Warnungen angezeigt, die auf der Grundlage [vorkonfigurierter Warnungsregeln](how-to-create-alerts.md) generiert wurden.

Verwenden Sie die Registerkarten am oberen Rand der Arbeitsmappe, um zwischen den Ansichten zu wechseln.

# <a name="devices"></a>[Geräte](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="Abschnitt „Geräte“ der Arbeitsmappe mit der Bestandsansicht" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

Bei der Ansicht **Geräte** handelt es sich um eine Übersicht über aktive Geräte, die Metriken senden. In dieser Ansicht werden Geräte aufgeführt, die der aktuellen IoT Hub- oder IoT Central-Anwendung zugeordnet sind.

Auf der rechten Seite befindet sich die Geräteliste mit zusammengesetzten Säulen für gesendete Nachrichten (lokal und Upstream). Sie können die Liste nach Gerätename filtern und auf den als Link dargestellten Gerätenamen klicken, um ausführliche Metriken für das Gerät anzuzeigen.

Auf der linken Seite gibt die Hive-Zellenvisualisierung Aufschluss darüber, welche Geräte fehlerfrei oder fehlerhaft sind. Außerdem sehen Sie hier, wann zuletzt Metriken von dem Gerät gesendet wurden. Geräte, von denen seit mehr als 30 Minuten keine Metriken gesendet wurden, werden blau dargestellt. Klicken Sie in der Hive-Zelle auf den Gerätenamen, um die zugehörige Integritätsmomentaufnahme anzuzeigen. Bei der Bestimmung der Integrität werden nur die letzten drei Messungen des Geräts berücksichtigt. Vorübergehende Spitzen bei den gemeldeten Metriken sind auf die ausschließliche Verwendung aktueller Daten zurückzuführen.

# <a name="alerts"></a>[Warnungen](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="Abschnitt „Warnungen“ der Arbeitsmappe mit der Bestandsansicht" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

Die Ansicht **Warnungen** enthält die Warnungen, die auf der Grundlage [vorab erstellter Warnungsregeln](how-to-create-alerts.md) generiert wurden. In dieser Ansicht werden Warnungen von mehreren IoT Hub- oder IoT Central-Anwendungen angezeigt.

Auf der linken Seite befindet sich eine Liste mit Warnungsschweregraden und der jeweiligen Anzahl. Auf der rechten Seite befindet sich eine Karte mit der Gesamtanzahl von Warnungen pro Region.

Klicken Sie auf eine Zeile mit einem Schweregrad, um Warnungsdetails anzuzeigen. Über den Link **Warnungsregel** gelangen Sie zum Warnungskontext und über den Link **Gerät** zur Arbeitsmappe mit den ausführlichen Metriken. Wenn die Arbeitsmappe mit den Gerätedetails über diese Ansicht geöffnet wird, wird sie automatisch an den Zeitbereich angepasst, in dem die Warnung ausgelöst wurde.

---

## <a name="iot-edge-device-details-workbook"></a>Arbeitsmappe „IoT Edge-Gerätedetails (Vorschau)“

Die Arbeitsmappe mit den Gerätedetails enthält drei Ansichten:

* In der Ansicht **Messaging** werden die Nachrichtenrouten für das Gerät visualisiert. Außerdem finden Sie hier Informationen zur allgemeinen Integrität des Messagingsystems.
* Die Ansicht **Module** gibt Aufschluss über die Leistung der einzelnen Module eines Geräts.
* Die Ansicht **Host** enthält Informationen zum Hostgerät – einschließlich Versionsinformationen für Hostkomponenten und Ressourcenverwendung.

Verwenden Sie die Registerkarten am oberen Rand der Arbeitsmappe, um zwischen den Ansichten zu wechseln.

Die Arbeitsmappe mit den Gerätedetails ist auch in die auf dem IoT Edge-Portal basierende Problembehandlung integriert, sodass Sie von Ihrem Gerät eingehende **Liveprotokolle** anzeigen können. Zu dieser Umgebung gelangen Sie, indem Sie über der Arbeitsmappe auf die Schaltfläche **Troubleshoot \<device name> live** (Liveproblembehandlung für <Gerätename>) klicken.

# <a name="messaging"></a>[Nachrichten](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="Abschnitt „Messaging“ der Arbeitsmappe mit den Gerätedetails" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

Die Ansicht **Messaging** enthält drei Unterabschnitte: Routingdetails, einen Routinggraphen und Informationen zur Messagingintegrität. Durch Ziehen und Loslassen innerhalb eines beliebigen Zeitdiagramms können Sie den globalen Zeitbereich an den ausgewählten Bereich anpassen.

Der Abschnitt **Routing** zeigt den Nachrichtenfluss zwischen sendenden und empfangenden Modulen. Hier finden Sie Informationen wie Nachrichtenanzahl, Rate und Anzahl verbundener Clients. Klicken Sie auf einen Sender oder Empfänger, um weitere Detailinformationen anzuzeigen. Wenn Sie auf einen Sender klicken, werden das Trenddiagramm für die Wartezeit des Senders sowie die Anzahl gesendeter Nachrichten angezeigt. Wenn Sie auf einen Empfänger klicken, werden der Trend für die Warteschlangenlänge des Empfängers und die Anzahl empfangener Nachrichten angezeigt.

Der Abschnitt **Graph** enthält eine visuelle Darstellung des Nachrichtenflusses zwischen Modulen. Der Graph kann durch Ziehen und Zoomen angepasst werden.

Im Abschnitt **Integrität** werden verschiedene Metriken im Zusammenhang mit der allgemeinen Integrität des Messagingsubsystems angezeigt. Sie können nach und nach weitere Details anzeigen, falls Fehler festgestellt werden.

# <a name="modules"></a>[Module](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="Abschnitt „Module“ der Arbeitsmappe mit den Gerätedetails" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

Die Ansicht **Module** enthält Metriken, die vom edgeAgent-Modul gesammelt wurden. Von diesem Modul wird der Status aller ausgeführten Module des Geräts gemeldet. Die Informationen umfassen unter anderem Folgendes:

* Modulverfügbarkeit
* Modulspezifische CPU- und Arbeitsspeicherauslastung
* Modulübergreifende CPU- und Arbeitsspeicherauslastung
* Anzahl von Modulneustarts und Zeitachse für Modulneustart

# <a name="host"></a>[Host](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="Abschnitt „Host“ der Arbeitsmappe mit den Gerätedetails" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

Die Ansicht **Host** enthält Metriken aus dem edgeAgent-Modul. Die Informationen umfassen unter anderem Folgendes:

* Informationen zur Version der Hostkomponente
* Betriebszeit
* CPU-, Arbeitsspeicher- und Speicherplatzauslastung auf Hostebene

# <a name="live-logs"></a>[Liveprotokolle](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="Auf Liveprotokolle kann über die Arbeitsmappe mit den Gerätedetails zugegriffen werden." lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

Diese Arbeitsmappe ist direkt in die portalbasierte Problembehandlung integriert. Klicken Sie auf die Schaltfläche für die Liveproblembehandlung, um zum Bildschirm für die Problembehandlung zu gelangen. Hier können Sie komfortabel und nach Bedarf vom Gerät gepullte Modulprotokolle anzeigen. Der Zeitbereich wird automatisch auf den Zeitbereich der Arbeitsmappe festgelegt, sodass Sie sich sofort im passenden Zeitkontext befinden. Darüber hinaus kann über diese Benutzeroberfläche jedes Modul neu gestartet werden.

---

## <a name="iot-edge-health-snapshot-workbook"></a>Arbeitsmappe „IoT Edge-Integritätsmomentaufnahme (Vorschau)“

Auf die Arbeitsmappe für Integritätsmomentaufnahmen können Sie über die Arbeitsmappe mit der Bestandsansicht zugreifen. Von der Arbeitsmappe mit der Bestandsansicht werden einige Parameter übergeben, die zum Initialisieren der Ansicht für Integritätsmomentaufnahmen erforderlich sind. Wählen Sie in der Hive-Zelle einen Gerätenamen aus, um die Integritätsmomentaufnahme des entsprechenden Geräts anzuzeigen.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="Zur Arbeitsmappe für Integritätsmomentaufnahmen gelangen Sie, indem Sie in der Arbeitsmappe mit der Bestandsansicht ein Gerät auswählen." lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::

Die Integritätsmomentaufnahme umfasst standardmäßig sechs Signale:

* Upstreamnachrichten
* Lokale Nachrichten
* Warteschlangenlänge
* Datenträgerauslastung
* CPU-Auslastung auf Hostebene
* Arbeitsspeicherauslastung auf Hostebene

Diese Signale werden mit konfigurierbaren Schwellenwerten abgeglichen, um zu ermitteln, ob ein Gerät fehlerfrei ist. Die Arbeitsmappe kann bearbeitet werden, um Schwellenwerte anzupassen oder neue Signale hinzuzufügen. Weitere Informationen zu Arbeitsmappenanpassungen finden Sie im nächsten Abschnitt.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="Anzeigen der Arbeitsmappe für Integritätsmomentaufnahmen" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>Anpassen von Arbeitsmappen

[Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md) sind hochgradig anpassbar. Sie können die öffentlichen Vorlagen bearbeiten, um sie an Ihre Anforderungen anzupassen. Alle Visualisierungen werden durch ressourcenbezogene [KQL](/azure/data-explorer/kusto/query/)-Abfragen für die Tabelle [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) gesteuert. Im folgenden Beispiel werden die Integritätsschwellenwerte bearbeitet.

Wechseln Sie zur Anpassung einer Arbeitsmappe zunächst in den Bearbeitungsmodus. Wählen Sie auf der Menüleiste der Arbeitsmappe die Schaltfläche **Bearbeiten** aus.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="Starten des Bearbeitungsmodus einer Arbeitsmappe" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::

In kuratierten Arbeitsmappen wird ausgiebig Gebrauch von Arbeitsmappengruppen gemacht. Unter Umständen müssen Sie in mehreren geschachtelten Gruppen auf **Bearbeiten** klicken, um eine Visualisierungsabfrage anzeigen zu können.

Speichern Sie Ihre Änderungen als neue Arbeitsmappe. Sie können die Arbeitsmappe für Ihr Team [freigeben](../azure-monitor/visualize/workbooks-access-control.md) oder sie im Rahmen der Ressourcenbereitstellungen in Ihrer Organisation [programmgesteuert bereitstellen](../azure-monitor/visualize/workbooks-automate.md).

So können Sie beispielsweise die Schwellenwerte für die Fehlerfreiheit oder Fehlerhaftigkeit eines Geräts ändern. Hierzu können Sie in der Vorlage für die Arbeitsmappe mit der Bestandsansicht einen Drilldown bis zum Abfrageelement **device-health-graph** ausführen. Dieses Element enthält alle Metrikschwellenwerte, die von der Arbeitsmappe für ein Gerät herangezogen werden.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="Öffnen des Bearbeitungsmodus für geschachtelte Komponenten, bis das Abfrageelement erreicht ist" lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::

## <a name="next-steps"></a>Nächste Schritte

Passen Sie Ihre Überwachungslösung mit [Warnungsregeln](how-to-create-alerts.md) und [Metriken aus benutzerdefinierten Modulen](how-to-add-custom-metrics.md) an.