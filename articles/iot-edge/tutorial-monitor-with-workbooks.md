---
title: 'Tutorial: Azure Monitor-Arbeitsmappen für IoT Edge'
description: Hier erfahren Sie, wie Sie IoT Edge-Module und -Geräte mithilfe von Azure Monitor-Arbeitsmappen für IoT überwachen.
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/13/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6ba79aa63700a35e79d49febad8510e283b35441
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207276"
---
# <a name="tutorial-monitor-iot-edge-devices"></a>Tutorial: Überwachen von IoT Edge-Geräten

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Verwenden Sie Azure Monitor-Arbeitsmappen, um die Integrität und Leistung Ihrer Azure IoT Edge-Bereitstellungen zu überwachen.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Verstehen, welche Metriken von IoT Edge-Geräten geteilt werden und wie sie vom Metriksammlermodul behandelt werden
> * Bereitstellen des Metriksammlermoduls auf einem IoT Edge-Gerät
> * Anzeigen zusammengestellter Visualisierungen der vom Gerät gesammelten Metriken

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein IoT Edge-Gerät, auf dem das Modul „Simulated Temperature Sensor“ (simulierter Temperatursensor) bereitgestellt ist. Falls Sie noch kein Gerät haben, führen Sie die Schritte unter [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](quickstart-linux.md) aus, um ein Gerät mithilfe einer VM zu erstellen.

## <a name="understand-iot-edge-metrics"></a>Grundlegendes zu IoT Edge-Metriken

Jedes IoT Edge-Gerät basiert auf zwei Modulen – den *Runtimemodulen*, die den Lebenszyklus und die Kommunikation aller anderen Module auf einem Gerät verwalten. Diese Module werden als **IoT Edge-Agent** und **IoT Edge-Hub** bezeichnet. Weitere Informationen zu diesen Modulen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Beide Runtimemodule erstellen Metriken, mit denen Sie die Leistung eines IoT Edge Geräts oder seiner einzelnen Module remote überwachen können. Der IoT Edge-Agent meldet Informationen zum Zustand einzelner Module und des Hostgeräts. Er erstellt damit Metriken wie die Dauer der ordnungsgemäßen Ausführung eines Moduls oder den genutzten Arbeitsspeicher und Prozentsatz der CPU-Auslastung auf dem Gerät. Der IoT Edge-Hub meldet Informationen zur Kommunikation auf dem Gerät. Er erstellt damit Metriken wie die Gesamtanzahl gesendeter und empfangener Nachrichten oder die zum Auflösen einer direkten Methode benötigte Zeit. Die vollständige Liste der verfügbaren Metriken finden Sie unter [Zugreifen auf integrierte Metriken](how-to-access-built-in-metrics.md).

Diese Metriken werden von beiden Modulen automatisch verfügbar gemacht, sodass Sie eigene Lösungen erstellen können, um auf die Metriken zuzugreifen und Berichte dazu zu erstellen. Für Benutzer, die keine benutzerdefinierte Lösung haben oder erstellen möchten, stellt Microsoft zur Vereinfachung das [Modul „azureiotedge-metrics-collector“](https://hub.docker.com/_/microsoft-azureiotedge-metrics-collector) bereit, das diesen Prozess für sie ausführt. Das Metriksammlermodul sammelt Metriken von den beiden Runtimemodulen und allen anderen Modulen, die Sie überwachen möchten, und transportiert sie vom Gerät.

Das Metriksammlermodul sendet Ihre Metriken mit einer von zwei Methoden an die Cloud. Die erste Option, die wir in diesem Tutorial verwenden, besteht darin, die Metriken direkt an Log Analytics zu senden. Bei der zweiten Option, die nur empfohlen wird, wenn Ihre Netzwerkrichtlinien es erfordern, senden Sie die Metriken über IoT Hub und richten dann eine Route zum Übergeben der Metriknachrichten an Log Analytics ein. In beiden Fällen sind die Metriken, sobald sie sich in Ihrem Log Analytics-Arbeitsbereich befinden, zur Anzeige mithilfe von Azure Monitor-Arbeitsmappen verfügbar.

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Ein Log Analytics-Arbeitsbereich ist zum Sammeln der Metrikdaten erforderlich. Er bietet eine Abfragesprache und ist in Azure Monitor integriert, um Ihnen das Überwachen Ihrer Geräte zu ermöglichen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie nach **Log Analytics-Arbeitsbereiche**, und wählen Sie diese Option aus.

1. Wählen Sie **Erstellen** aus, und folgen Sie dann den Eingabeaufforderungen, um einen neuen Arbeitsbereich zu erstellen.

1. Wählen Sie nach der Erstellung des Arbeitsbereichs die Option **Zu Ressource wechseln** aus.

1. Wählen Sie im Hauptmenü unter **Einstellungen** die Option **Agent-Verwaltung** aus.

1. Kopieren Sie die Werte von **Arbeitsbereichs-ID** und **Primärschlüssel**. Diese beiden Werte verwenden Sie später im Tutorial, um das Metriksammlermodul zum Senden der Metriken an diesen Arbeitsbereich zu konfigurieren.

## <a name="retrieve-your-iot-hub-resource-id"></a>Abrufen Ihrer IoT Hub-Ressourcen-ID

Beim Konfigurieren des Metriksammlermoduls geben Sie die Azure Resource Manager-Ressourcen-ID für Ihren IoT-Hub an. Rufen Sie diese ID jetzt ab.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub.

1. Wählen Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Eigenschaften** aus.

1. Kopieren Sie den Wert der **Ressourcen-ID**. Er muss das Format `/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Devices/IoTHubs/<iot_hub_name>` aufweisen.

## <a name="deploy-the-metrics-collector-module"></a>Bereitstellen des Metriksammlermoduls

Stellen Sie das Metriksammlermodul auf jedem Gerät bereit, das Sie überwachen möchten. Es wird wie jedes andere Modul auf dem Gerät ausgeführt und überwacht die zugewiesenen Endpunkte auf Metriken, die gesammelt und an die Cloud gesendet werden sollen.

Führen Sie die folgenden Schritte aus, um das Metriksammlermodul bereitzustellen und zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub-Instanz.

1. Wählen Sie im Menü im linken Bereich unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Wählen Sie die Geräte-ID des Zielgeräts in der Liste der IoT Edge-Geräte aus, um die Seite mit den Gerätedetails zu öffnen.

1. Wählen Sie in der oberen Menüleiste **Module festlegen** aus, um die Seite für die Modulbereitstellung (umfasst drei Schritte) zu öffnen.

1. Im ersten Schritt der Bereitstellung von Modulen über das Portal deklarieren Sie die **Module**, die auf einem Gerät vorhanden sein sollen. Wenn Sie das Gerät verwenden, das Sie im Schnellstart erstellt haben, sollte das Modul **SimulatedTemperatureSensor** bereits angezeigt werden. Falls nicht, fügen Sie es jetzt hinzu:

   1. Wählen Sie **Hinzufügen** und dann in der Dropdownliste die Option **Marketplace-Modul** aus.

   1. Suchen Sie nach dem Modul **SimulatedTemperatureSensor**, und wählen Sie es aus.

1. Fügen Sie das Metriksammlermodul hinzu, und konfigurieren Sie es:

   1. Wählen Sie **Hinzufügen** und dann in der Dropdownliste die Option **Marketplace-Modul** aus.
   1. Suchen Sie nach dem Metriksammlermodul **IoT Edge Metrics Collector**, und wählen Sie es aus.
   1. Wählen Sie das Metriksammlermodul in der Liste der Module aus, um die Seite mit den Konfigurationsdetails zu öffnen.
   1. Navigieren Sie zur Registerkarte **Umgebungsvariablen**.
   1. Ändern Sie die folgenden Werte:

      | Name | Wert |
      | ---- | ----- |
      | **ResourceId** | Die Ressourcen-ID Ihres IoT-Hubs, die Sie weiter oben abgerufen haben. |
      | **UploadTarget** | `AzureMonitor` |
      | **LogAnalyticsWorkspaceId** | Die ID Ihres Log Analytics-Arbeitsbereichs, die Sie weiter oben abgerufen haben. |
      | **LogAnalyticsSharedKey** | Der Schlüssel Ihres Log Analytics-Arbeitsbereichs, den Sie weiter oben abgerufen haben. |

   1. Löschen Sie die Umgebungsvariable **OtherConfig**. Diese Variable ist ein Platzhalter für zusätzliche Konfigurationsoptionen, die Sie vielleicht später hinzufügen möchten. Für dieses Tutorial ist sie nicht erforderlich.
   1. Wählen Sie **Aktualisieren** aus, um Ihre Änderungen zu speichern.

1. Wählen Sie **Weiter: Routen** aus, um mit dem zweiten Schritt der Bereitstellung von Modulen fortzufahren.

1. Das Portal fügt automatisch eine Route für den Metriksammler hinzu. Diese Route würden Sie verwenden, wenn Sie das Metriksammlermodul zum Senden der Metriken über IoT Hub konfigurieren. Da wir die Metriken in diesem Tutorial jedoch direkt an Log Analytics senden, benötigen wir sie nicht. Löschen Sie **FromMetricsCollectorToUpstream** aus der Route.

1. Wählen Sie **Überprüfen und erstellen** aus, um mit dem letzten Schritt der Bereitstellung von Modulen fortzufahren.

1. Wählen Sie **Erstellen** aus, um die Bereitstellung abzuschließen.

Nach Abschluss der Modulbereitstellung kehren Sie zur Seite mit den Gerätedetails zurück, auf der Sie nun vier Module unter **In Bereitstellung angegeben** sehen. Es kann etwas dauern, bis alle vier Module als **Vom Gerät gemeldet** angezeigt werden (dies bedeutet, dass sie erfolgreich gestartet wurden und ihren Status an IoT Hub gemeldet haben). Aktualisieren Sie die Seite, um den aktuellen Status anzuzeigen.

## <a name="monitor-device-health"></a>Überwachen der Integrität des Geräts

Es kann bis zu 15 Minuten dauern, bis die Arbeitsmappen für die Geräteüberwachung angezeigt werden können. Sobald Sie das Metriksammlermodul bereitgestellt haben, beginnt es, Metriknachrichten an Log Analytics zu senden, wo sie dann in einer Tabelle organisiert werden. Die erfassten Metriken werden über die von Ihnen angegebene IoT Hub-Ressourcen-ID mit dem zugehörigen Hub verknüpft. Dadurch können die zusammengestellten IoT Edge-Arbeitsmappen Metriken abrufen, indem sie die Metriktabelle anhand der Ressourcen-ID abfragen.

In Azure Monitor sind drei Standardarbeitsmappenvorlagen für IoT verfügbar:

* Die Arbeitsmappe **IoT Edge-Bestandsansicht** zeigt eine Übersicht der aktiven Geräte, sodass Sie fehlerhafte Geräte identifizieren und einen Drilldown in die Leistung einzelner Geräte ausführen können. Von Ihren Warnungsregeln generierte Warnungen werden auch in dieser Arbeitsmappe angezeigt.
* In der Arbeitsmappe **IoT Edge-Gerätedetails** sind Visualisierungen für drei Kategorien verfügbar: „Messaging“, „Module“ und „Host“. In der Ansicht „Messaging“ werden die Nachrichtenrouten für ein Gerät visualisiert und Informationen zur allgemeinen Integrität des Messagingsystems angezeigt. Die Ansicht „Module“ gibt Aufschluss über die Leistung der einzelnen Module auf einem Gerät. Die Ansicht „Host“ enthält Informationen zum Hostgerät, einschließlich Versionsinformationen für Hostkomponenten und Ressourcenverwendung.
* Die Arbeitsmappe **IoT Edge-Integritätsmomentaufnahme** misst Gerätesignale anhand konfigurierbarer Schwellenwerte, um festzustellen, ob ein Gerät fehlerfrei ist. Auf diese Arbeitsmappe kann nur in der Arbeitsmappe mit der Bestandsansicht zugegriffen werden, die die erforderlichen Parameter zum Initialisieren der Integritätsmomentaufnahme eines bestimmten Geräts übergibt.

### <a name="explore-the-fleet-view-and-health-snapshot-workbooks"></a>Untersuchen der Arbeitsmappen mit der Bestandsansicht und Integritätsmomentaufnahme

In der Arbeitsmappe mit der Bestandsansicht werden all Ihre Geräte angezeigt, und Sie können bestimmte Geräte auswählen, um die zugehörigen Integritätsmomentaufnahmen anzuzeigen. Gehen Sie wie folgt vor, um die Visualisierungen in einer Arbeitsmappe zu untersuchen:

1. Kehren Sie zu Ihrer IoT Hub-Seite im Azure-Portal zurück.

1. Scrollen Sie im Hauptmenü nach unten zum Abschnitt **Überwachung**, und wählen Sie **Arbeitsmappen** aus.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/workbooks-gallery.png" alt-text="Auswählen von „Arbeitsmappen“ zum Öffnen des Azure Monitor-Arbeitsmappenkatalogs":::

1. Wählen Sie die Arbeitsmappe **IoT Edge-Bestandsansicht** aus.

1. Ihr Gerät, auf dem das Metriksammlermodul ausgeführt wird, sollte angezeigt werden. Das Gerät wird entweder als **fehlerfrei** oder als **fehlerhaft** aufgeführt.

1. Wählen Sie den Namen des Geräts aus, um die **IoT Edge-Integritätsmomentaufnahme** zu öffnen und spezifische Details zur Integrität des Geräts anzuzeigen.

1. In allen Zeitdiagrammen können Sie den Zeitbereich ändern, indem Sie die Pfeilsymbole unter der X-Achse verwenden oder auf das Diagramm klicken und den Cursor ziehen.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/health-snapshot-custom-time-range.png" alt-text="Ändern des Zeitbereichs von Diagrammen durch Ziehen und Klicken oder Verwenden der Pfeilsymbole":::

1. Schließen Sie die Arbeitsmappe mit der Integritätsmomentaufnahme. Wählen Sie in der Arbeitsmappe mit der Bestandsansicht die Option **Arbeitsmappen** aus, um zum Arbeitsmappenkatalog zurückzukehren.

### <a name="explore-the-device-details-workbook"></a>Untersuchen der Arbeitsmappe mit den Gerätedetails

Die Arbeitsmappe mit den Gerätedetails zeigt detaillierte Informationen zur Leistung für ein einzelnes Gerät. Gehen Sie wie folgt vor, um die Visualisierungen in einer Arbeitsmappe zu untersuchen:

1. Wählen Sie im Arbeitsmappenkatalog die Arbeitsmappe **IoT Edge-Gerätedetails** aus.

1. Die erste Seite, die Sie in der Arbeitsmappe mit den Gerätedetails sehen, ist die Ansicht **Messaging** mit der Registerkarte **Routing**.

   Auf der linken Seite werden in einer Tabelle die Routen auf dem Gerät nach Endpunkt organisiert angezeigt. Für unser Gerät sehen wir, dass der Endpunkt **upstream** (dieser spezielle Begriff wird für die Weiterleitung an IoT Hub verwendet) Nachrichten von der Ausgabe **temperatureOutput** des Moduls „SimulatedTemperatureSensor“ empfängt.

   Im Graphen auf der rechten Seite wird die Anzahl verbundener Clients im Zeitverlauf nachverfolgt. Sie können auf den Graphen klicken und den Zeitbereich durch Ziehen ändern.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-routing.png" alt-text="Auswählen der Ansicht „Messaging“ zum Anzeigen des Status der Kommunikation auf dem Gerät":::

1. Wählen Sie die Registerkarte **Graph** aus, um eine andere Visualisierung der Routen anzuzeigen. Auf der Seite „Graph“ können Sie die verschiedenen Endpunkte per Drag & Drop verschieben, um den Graphen neu anzuordnen. Dieses Feature ist hilfreich, wenn Sie viele Routen visualisieren möchten.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-graph.png" alt-text="Auswählen der Ansicht „Graph“ zum Anzeigen eines interaktiven Graphen der Geräterouten":::

1. Auf der Registerkarte **Integrität** werden Probleme beim Messaging angezeigt, z. B. gelöschte Nachrichten oder Clients, deren Verbindung getrennt wurde.

1. Wählen Sie die Ansicht **Module** aus, um den Status aller auf dem Gerät bereitgestellten Module anzuzeigen. Sie können jedes Modul einzeln auswählen, um Details zur CPU- und Arbeitsspeichernutzung anzuzeigen.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-modules-availability.png" alt-text="Auswählen der Ansicht „Module“ zum Anzeigen des Status der einzelnen bereitgestellten Module auf dem Gerät":::

1. Wählen Sie die Ansicht **Host** aus, um Informationen zum Hostgerät anzuzeigen, einschließlich des Betriebssystems, der IoT Edge-Daemonversion und Ressourcennutzung.

## <a name="view-module-logs"></a>Anzeigen von Modulprotokollen

Nachdem Sie die Metriken für ein Gerät angezeigt haben, möchten Sie sich einzelne Module vielleicht genauer ansehen. IoT Edge bietet mit einem Feature für Livemodulprotokolle Unterstützung bei der Problembehandlung im Azure-Portal.

1. Wählen Sie in der Arbeitsmappe mit den Gerätedetails die Option **Problembehandlung live** aus.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-troubleshoot-live.png" alt-text="Auswählen der Schaltfläche „Problembehandlung live“ oben rechts in der Arbeitsmappe mit den Gerätedetails":::

1. Die Problembehandlungsseite wird mit den **edgeAgent**-Protokollen Ihres IoT Edge Geräts geöffnet. Wenn Sie in der Arbeitsmappe mit den Gerätedetails einen bestimmten Zeitbereich ausgewählt haben, wird diese Einstellung auf der Problembehandlungsseite übernommen.

1. Über das Dropdownmenü können Sie zu den Protokollen anderer Module wechseln, die auf dem Gerät ausgeführt werden. Klicken Sie auf die Schaltfläche **Neu starten**, um ein Modul neu zu starten.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/troubleshoot-device.png" alt-text="Verwenden des Dropdownmenüs zum Anzeigen der Protokolle anderer Module und der Schaltfläche „Neu starten“ zum Neustarten von Modulen":::

Sie können auch über die Detailseite eines IoT Edge-Geräts auf die Problembehandlungsseite zugreifen. Weitere Informationen finden Sie unter [Beheben von Problemen bei IoT Edge-Geräten über das Azure-Portal](troubleshoot-in-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Lassen Sie das Metriksammlermodul für die weiteren Tutorials auf Ihren Geräten, und kehren Sie zu diesen Arbeitsmappen zurück, um zu überprüfen, wie sich die Informationen ändern, wenn Sie komplexere Module und ein komplexeres Routing hinzufügen.

Fahren Sie mit dem nächsten Tutorial fort, in dem Sie Ihre Entwicklerumgebung einrichten, um mit der Bereitstellung benutzerdefinierter Module auf Ihren Geräten zu beginnen.

> [!div class="nextstepaction"]
> [Entwickeln von IoT Edge-Modulen mit Linux-Containern](tutorial-develop-for-linux.md)
