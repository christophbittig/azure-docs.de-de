---
title: Erweitern von Azure IoT Central mit benutzerdefinierten Analysen | Microsoft-Dokumentation
description: Als Lösungsentwickler konfigurieren Sie eine IoT Central-Anwendung zur Ausführung von benutzerdefinierten Analysen und Visualisierungen. Diese Lösung verwendet Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5f046920ce711ac6b1e6b9a461d71d9d49b2ddc7
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597663"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Erweitern von Azure IoT Central mit benutzerdefinierten Analysen mithilfe von Azure Databricks

Diese Schrittanleitung zeigt, wie Sie Ihre IoT Central-Anwendung um benutzerdefinierte Analysen und Visualisierungen erweitern. Im Beispiel wird ein [Azure Databricks](/azure/azure-databricks/)-Arbeitsbereich verwendet, um den IoT Central-Telemetriedatenstrom zu analysieren und Visualisierungen wie [Boxplots](https://wikipedia.org/wiki/Box_plot) zu generieren.  

Diese Schrittanleitung zeigt, wie Sie IoT Central mithilfe der integrierten [Analysetools](./howto-create-custom-analytics.md) über die bisherige Funktionalität hinaus erweitern.

In dieser Schrittanleitung erfahren Sie Folgendes:

* Streamen von Telemetriedaten aus einer IoT Central-Anwendung mithilfe des *kontinuierlichen Datenexports*
* Erstellen Sie eine Azure Databricks-Umgebung zum Analysieren und Darstellen von Gerätetelemetrie.

## <a name="prerequisites"></a>Voraussetzungen

Um die in dieser Anleitung aufgeführten Schritte ausführen zu können, benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

### <a name="iot-central-application"></a>IoT Central-Anwendung

Erstellen Sie über die Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral) eine IoT Central-Anwendung mit folgenden Einstellungen:

| Einstellung | value |
| ------- | ----- |
| Tarif | Standard |
| Anwendungsvorlage | In-Store-Analyse – Bedingungsüberwachung |
| Anwendungsname | Standardwert übernehmen oder eigenen Namen angeben |
| URL | Standardwert übernehmen oder eigenes eindeutiges URL-Präfix angeben |
| Verzeichnis | Ihr Azure Active Directory-Mandant |
| Azure-Abonnement | Ihr Azure-Abonnement |
| Region | Ihre nächstgelegene Region |

Bei den Beispielen und Screenshots in diesem Artikel wird die Region **USA** verwendet. Wählen Sie einen Standort in Ihrer Nähe, und stellen Sie sicher, dass Sie alle Ressourcen in derselben Region erstellen.

Diese Anwendungsvorlage enthält zwei simulierte Thermostatgeräte, die Telemetriedaten senden.

### <a name="resource-group"></a>Resource group

Verwenden Sie das [Azure-Portal zum Erstellen einer Ressourcengruppe](https://portal.azure.com/#create/Microsoft.ResourceGroup) namens **IoTCentralAnalysis**. Sie soll die anderen von Ihnen erstellten Ressourcen enthalten. Erstellen Sie Ihre Azure-Ressourcen am gleichen Standort wie Ihre IoT Central-Anwendung.

### <a name="event-hubs-namespace"></a>Event Hubs-Namespace

Verwenden Sie das [Azure-Portal zum Erstellen eines Event Hubs-Namespace](https://portal.azure.com/#create/Microsoft.EventHub) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| Name    | Namen Ihres Namespace auswählen |
| Tarif | Basic |
| Subscription | Ihr Abonnement |
| Resource group | IoTCentralAnalysis |
| Standort | East US |
| Durchsatzeinheiten | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks-Arbeitsbereich

Verwenden Sie das [Azure-Portal zum Erstellen eines Azure Databricks-Diensts](https://portal.azure.com/#create/Microsoft.Databricks) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| Arbeitsbereichname    | Wählen Sie den Namen Ihres Arbeitsbereichs |
| Subscription | Ihr Abonnement |
| Resource group | IoTCentralAnalysis |
| Standort | East US |
| Preisstufe | Standard |

Wenn Sie die erforderlichen Ressourcen erstellt haben, sieht Ihre Ressourcengruppe **IoTCentralAnalysis** wie im folgenden Screenshot aus:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="Abbildung: Ressourcengruppe „IoTCentralAnalysis“":::

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Sie können eine IoT Central-Anwendung konfigurieren, um Telemetriedaten kontinuierlich an einen Event Hub zu exportieren. In diesem Abschnitt erstellen Sie einen Event Hub, um Telemetriedaten aus Ihrer IoT Central-Anwendung zu empfangen. Der Event Hub übermittelt die Telemetriedaten zur Verarbeitung an Ihren Stream Analytics-Auftrag.

1. Navigieren Sie im Azure-Portal zu Ihrem Event Hubs-Namespace, und klicken Sie auf **+ Event Hub**.
1. Geben Sie dem Event Hub den Namen **centralexport**.
1. Wählen Sie in der Liste der Event Hubs in Ihrem Namespace **centralexport** aus. Wählen Sie dann **SAS-Richtlinien** aus.
1. Wählen Sie **+ Hinzufügen**. Erstellen Sie eine Richtlinie namens **SendListen** mit den Ansprüchen **Send** und **Listen**.
1. Wenn die Richtlinie fertig ist, wählen Sie sie in der Liste aus, und kopieren Sie den Wert von **Verbindungszeichenfolge – Primärschlüssel**.
1. Notieren Sie sich diese Verbindungszeichenfolge. Sie werden Sie später verwenden, wenn Sie Ihr Databricks-Notebook zum Lesen aus dem Event Hub konfigurieren.

Ihr Event Hubs-Namespace sieht wie im folgenden Screenshot aus:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Abbildung: Azure Event Hubs-Namespace":::

## <a name="configure-export-in-iot-central"></a>Konfigurieren des Exports nach IoT Central

In diesem Abschnitt konfigurieren Sie die Anwendung so, dass sie Telemetriedaten von ihren simulierten Geräten an Ihren Event-Hub sendet.

Navigieren Sie auf der [Azure IoT Zentraler Anwendungsmanager](https://aka.ms/iotcentral)-Website zu der zuvor erstellten IoT Zentralen Anwendung. Um den Export zu konfigurieren, erstellen Sie zunächst ein Ziel:

1. Navigieren Sie zur **Datenexport**-Seite, und wählen Sie dann **Ziele** aus.
1. Wählen Sie **+Neues Ziel** aus.
1. Verwenden Sie die Werte in der folgenden Tabelle, um ein Ziel zu erstellen:

    | Einstellung | Wert |
    | ----- | ----- |
    | Zielname | Telemetrie-Ereignis-Hub |
    | Zieltyp | Azure Event Hubs |
    | Verbindungszeichenfolge | Die Event-Hub-Verbindungszeichenkette die Sie sich zuvor notiert haben |

    Der **Event Hub** wird als **Zentral-Export** angezeigt.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Screenshot, der das Ziel des Datenexports anzeigt.":::

1. Wählen Sie **Speichern** aus.

So erstellen Sie die Exportdefinition:

1. Navigieren Sie zur Seite **Datenexport**, und wählen Sie **+ Neuer Export** aus.

1. Verwenden Sie die in der nachfolgenden Tabelle aufgeführten Werte, um den Export zu konfigurieren:

    | Einstellung | Wert |
    | ------- | ----- |
    | Export-Name | Ereignis-Hub-Export |
    | Enabled | Andererseits |
    | Typ der zu exportierenden Daten | Telemetrie |
    | Destinations | Wählen Sie **+ Ziel** aus, und wählen Sie dann **Telemetrie-Ereignis-Hub** aus |

1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Screenshot, dass die Datenexport-Definition anzeigt.":::

Warten Sie, bis der Exportstatus auf der **Datenexportseite** **Gesund** anzeigt, bevor Sie fortfahren.

## <a name="configure-databricks-workspace"></a>Konfigurieren des Databricks-Arbeitsbereichs

Navigieren Sie im Azure-Portal zu Ihrem Azure Databricks-Dienst, und wählen Sie **Arbeitsbereich starten** aus. In Ihrem Browser wird eine neue Registerkarte geöffnet, die Sie an Ihrem Arbeitsbereich anmeldet.

### <a name="create-a-cluster"></a>Erstellen eines Clusters

Wählen Sie auf der Seite **Azure Databricks** unter der Liste der allgemeinen Aufgaben **Neuer Cluster** aus.

Verwenden Sie die Informationen in der folgenden Tabelle zum Erstellen Ihres Clusters:

| Einstellung | Wert |
| ------- | ----- |
| Clustername | centralanalysis |
| Clustermodus | Standard |
| Databricks-Runtimeversion | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Python-Version | 3 |
| Automatische Skalierung aktivieren | Nein |
| Terminate after __ minutes of inactivity (Nach __ Minuten Inaktivität beenden) | 30 |
| Workertyp | Standard_DS3_v2 |
| Worker | 1 |
| Treibertyp | Entspricht dem Worker |

Das Erstellen eines Clusters kann mehrere Minuten dauern. Warten Sie, bis die Clustererstellung abgeschlossen ist, bevor Sie den Vorgang fortsetzen.

### <a name="install-libraries"></a>Installieren von Bibliotheken

Warten Sie auf der Seite **Cluster**, bis der Clusterstatus **Wird ausgeführt** lautet.

Die folgenden Schritte zeigen, wie Sie die für Ihr Beispiel benötigte Bibliothek in den Cluster importieren:

1. Warten Sie auf der Seite **Cluster**, bis der Status des interaktiven Clusters **centralanalysis** gleich **Wird ausgeführt** lautet.

1. Wählen Sie den Cluster und dann die Registerkarte **Bibliotheken** aus.

1. Wählen Sie auf der Registerkarte **Bibliotheken** die Option **Neue installieren** aus.

1. Wählen Sie auf der Seite **Bibliothek installieren** als Bibliotheksquelle **Maven** aus.

1. Geben Sie im Textfeld **Koordinaten** den folgenden Wert ein: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Wählen Sie **Installieren**, um die Bibliothek auf dem Cluster zu installieren.

1. Der Bibliotheksstatus lautet jetzt **Installiert**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Screenshot: Installierte Bibliothek":::

### <a name="import-a-databricks-notebook"></a>Importieren eines Databricks-Notebooks

Mit den folgenden Schritten importieren Sie ein Databricks-Notebook, das den Python-Code zum Analysieren und Visualisieren Ihrer IoT Central-Telemetrie enthält:

1. Navigieren Sie in Ihrer Databricks-Umgebung zur Seite **Arbeitsbereich**. Wählen Sie das Dropdownfeld neben Ihrem Kontonamen und dann **Importieren** aus.

1. Wählen Sie, dass Sie aus einer URL importieren möchten, und geben Sie folgende Adresse ein: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Wählen Sie zum Importieren des Notebooks **Importieren** aus.

1. Wählen Sie den **Arbeitsbereich** zum Anzeigen des importierten Notebooks aus:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Screenshot: Importiertes Notebook":::

5. Bearbeiten Sie den Code in der ersten Python-Zelle, um die zuvor gespeicherte Event Hubs-Verbindungszeichenfolge hinzuzufügen:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Ausführen der Analyse

Zum Ausführen der Analyse müssen Sie das Notebook an den Cluster anfügen:

1. Wählen Sie **Getrennt** und dann den Cluster **centralanalysis** aus.
1. Wenn der Cluster nicht ausgeführt wird, starten Sie ihn.
1. Wählen Sie zum Starten des Notebooks die Schaltfläche „Ausführen“ aus.

Möglicherweise wird in der letzten Zelle ein Fehler angezeigt. Wenn dies zutrifft, überprüfen Sie, ob die vorherigen Zellen ausgeführt werden, warten Sie eine Minute, damit einige Daten in den Speicher geschrieben werden, und führen Sie dann die letzte Zelle erneut aus.

### <a name="view-smoothed-data"></a>Anzeigen von geglätteten Daten

Scrollen Sie im Notebook nach unten bis Zelle 14, um einen Plot vom gleitenden Durchschnitt der Luftfeuchtigkeit nach Gerätetyp anzuzeigen. Dieser Plot wird beim Empfang der Streaming-Telemetrie fortlaufend aktualisiert:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Screenshot: Geglätteter Telemetrieplot":::

Sie können die Größe des Diagramms im Notebook ändern.

### <a name="view-box-plots"></a>Anzeigen von Boxplots

Scrollen Sie im Notebook nach unten bis Zelle 20, um die [Boxplots](https://en.wikipedia.org/wiki/Box_plot) anzuzeigen. Weil die Boxplots auf statischen Daten basieren, müssen Sie für deren Aktualisierung die Zelle erneut ausführen:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Screenshot: Boxplots":::

Sie können die Größe der Plots im Notebook ändern.

## <a name="tidy-up"></a>Aufräumen

Um nach dieser Vorgehensweise aufzuräumen und unnötige Kosten zu vermeiden, löschen Sie die Ressourcengruppe **IoTCentralAnalysis** im Azure-Portal.

Sie können die IoT Central-Anwendung in der Anwendung selbst auf der Seite **Verwaltung** löschen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schrittanleitung wurde Folgendes vermittelt:

* Streamen von Telemetriedaten aus einer IoT Central-Anwendung mithilfe des *kontinuierlichen Datenexports*
* Erstellen einer Azure Databricks-Umgebung zum Analysieren und Darstellen von Telemetriedaten

Sie wissen jetzt, wie benutzerdefinierte Analysen erstellt werden. Als Nächstes sollten Sie den Artikel [Verwenden der Azure IoT Central-Geräte-Bridge, um andere IoT-Clouds mit IoT Central zu verbinden](howto-build-iotc-device-bridge.md) lesen.
