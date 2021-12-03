---
title: Erfassen von Daten aus Event Grid in Azure Synapse Data Explorer (Vorschau)
description: Hier erfahren Sie, wie Sie Daten aus Event Grid in Azure Synapse Data Explorer erfassen (laden).
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 2c472ae5950bc035612987c35e76291dc5c23f49
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485665"
---
# <a name="ingest-blobs-into-azure-synapse-data-explorer-by-subscribing-to-event-grid-notifications-preview"></a>Erfassen von Blobs in Azure Synapse Data Explorer durch Abonnieren von Event Grid-Benachrichtigungen (Vorschau)

<!-- > [!div class="op_single_selector"]
> * [One-click](one-click-ingestion-new-table.md)
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager template](data-connection-event-grid-resource-manager.md) -->

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

In diesem Artikel erfahren Sie, wie Blobs aus Ihrem Speicherkonto unter Verwendung einer Event Grid-Datenverbindung in Azure Synapse Data Explorer erfasst werden. Sie erstellen eine Event Grid-Datenverbindung, über die ein [Azure Event Grid](/azure/event-grid/overview)-Abonnement festgelegt wird. Das Event Grid-Abonnement leitet Ereignisse aus Ihrem Speicherkonto über einen Azure Event Hub an Data Explorer weiter. Anschließend wird ein Beispiel für den Datenfluss im gesamten System gezeigt.

Allgemeine Informationen zur Erfassung in Data Explorer aus Event Grid finden Sie unter [Herstellen einer Verbindung mit Event Grid](data-explorer-ingest-event-grid-overview.md).<!-- To create resources manually in the Azure portal, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md). -->

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Erstellen einer Zieltabelle, an die Event Hubs Daten senden
    1. Wählen Sie in Synapse Studio im linken Bereich **Entwickeln** aus.
    1. Wählen Sie unter **KQL-Skripts** die Option **&plus;** (Neue Ressource hinzufügen) > **KQL-Skript** aus. Im rechten Bereich können Sie Ihr Skript benennen.
    1. Wählen Sie im Menü **Verbinden mit** den Eintrag *contosodataexplorer* aus.
    1. Wählen Sie im Menü **Datenbank verwenden** die Option *TestDatabase* aus.
    1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
        ```

        > [!TIP]
        > Vergewissern Sie sich, dass die Tabelle erstellt wurde. Wählen Sie im linken Bereich **Daten** aus, wählen Sie das Menü mit weiteren Optionen *contosodataexplorer* aus, und wählen Sie dann **Aktualisieren** aus. Erweitern Sie unter *contosodataexplorer* den Eintrag **Tabellen**, und stellen Sie sicher, dass die Tabelle *TestTable* in der Liste angezeigt wird.

    1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.

        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
        ```

* Erstellen eines [Speicherkontos](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)
* Das Abonnement von Event Grid-Benachrichtigungen kann in Azure Storage-Konten für `BlobStorage`, `StorageV2` oder [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) festgelegt werden.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-an-event-grid-data-connection"></a>Erstellen einer Event Grid-Datenverbindung

Verbinden Sie nun das Speicherkonto mit Data Explorer, sodass die in den Speicher fließenden Daten in die Testtabelle gestreamt werden. Diese Verbindung kann im Azure-Portal unter „Data Explorer“ erstellt werden.

1. Wählen Sie unter dem von Ihnen erstellten Data Explorer-Pool **Datenbanken** > **TestDatabase** aus.

    :::image type="content" source="../media/ingest-data-event-grid/select-test-database.png" alt-text="Auswählen der Testdatenbank":::

1. Wählen Sie **Datenverbindungen** und dann **Datenverbindung hinzufügen** aus.

    :::image type="content" source="../media/ingest-data-event-grid/event-hub-connection.png" alt-text="Auswählen von Datenerfassung und „Datenverbindung hinzufügen“":::

#### <a name="data-connection---basics-tab"></a>Datenverbindung: Registerkarte „Grundlagen“

1. Wählen Sie den Verbindungstyp aus: **Blobspeicher**.

1. Füllen Sie das Formular mit den folgenden Informationen aus:

    :::image type="content" source="../media/ingest-data-event-grid/data-connection-basics.png" alt-text="Ausfüllen des Event Grid-Formulars mit allgemeinen Verbindungsinformationen":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
    |---|---|---|
    | Name der Datenverbindung | *test-grid-connection* | Der Name der Verbindung, die Sie in Data Explorer erstellen möchten.|
    | Speicherkontoabonnement | Ihre Abonnement-ID | Die Abonnement-ID, unter der sich Ihr Speicherkonto befindet.|
    | Speicherkonto | *gridteststorage1* | Der Name des zuvor erstellten Speicherkontos.|
    | Ereignistyp | *Blob erstellt* oder *Blob umbenannt* | Der Typ des Ereignisses, das die Erfassung auslöst *Blob umbenannt* wird nur für ADLSv2-Speicher unterstützt. Folgende Typen werden unterstützt: „Microsoft.Storage.BlobCreated“ oder „Microsoft.Storage.BlobRenamed“. |
    | Ressourcenerstellung | *Automatisch* | Legen Sie fest, ob mit Data Explorer ein Event Grid-Abonnement, Event Hub-Namespace und Event Hub für Sie erstellt werden soll. <!-- To create resources manually, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md) -->|

1. Wählen Sie **Filtereinstellungen** aus, wenn Sie bestimmte Themen nachverfolgen möchten. Legen Sie die Filter für die Benachrichtigungen wie folgt fest:
    * Das Feld **Präfix** ist das *Literalpräfix* des Themas. Da das angewendete Muster *startswith* ist, kann es für mehrere Container, Ordner oder Blobs gelten. Platzhalter sind nicht zulässig.
        * Zum Definieren eines Filters für den Blobcontainer *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container prefix]`* .
        * Zum Definieren eines Filters für ein Blobpräfix (oder einen Ordner in Azure Data Lake Gen2) *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * Das Feld **Suffix** ist das *Literalsuffix* des Blobs. Platzhalter sind nicht zulässig.
    * Mit dem Feld **Groß-/Kleinschreibung beachten** wird angegeben, ob bei den Präfix- und Suffixfiltern nach Groß-/Kleinschreibung unterschieden wird.
    * Weitere Informationen zum Filtern von Ereignissen finden Sie unter [Blob Storage-Ereignisse](/azure/storage/blobs/storage-blob-event-overview#filtering-events).

    :::image type="content" source="../media/ingest-data-event-grid/filter-settings.png" alt-text="Event Grid-Filtereinstellungen":::

1. Klicken Sie auf **Weiter: Erfassungseigenschaften**.

> [!NOTE]
> Es wird empfohlen die Datenverbindung so zu aktualisieren, dass verwaltete Identitäten verwendet werden, um auf das Speicherkonto zuzugreifen, sobald die Option für Ihren Cluster verfügbar ist.

#### <a name="data-connection---ingest-properties-tab"></a>Datenverbindung: Registerkarte „Erfassungseigenschaften“

1. Füllen Sie das Formular mit den folgenden Informationen aus. Für Tabellen- und Zuordnungsnamen wird die Groß-/Kleinschreibung beachtet:

   :::image type="content" source="../media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="Überprüfen und Erstellen von Erfassungseigenschaften für Tabellen und Zuordnungen":::

    Erfassungseigenschaften:

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Tabellenname | *TestTable* | Die Tabelle, die Sie unter **TestDatabase** erstellt haben. |
    | Datenformat | *JSON* | Die unterstützten Formate sind Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW und W3CLOG. Die unterstützten Komprimierungsoptionen sind Zip und GZip. |
    | Zuordnung | *TestMapping* | Die Zuordnung, die Sie in **TestDatabase** erstellt haben, um eingehende JSON-Daten den Spaltennamen und Datentypen von **TestTable** zuzuordnen.|
    | Erweiterte Einstellungen | *Die Daten haben Überschriften.* | Ignoriert Überschriften. Unterstützt für Dateien vom Typ „*SV“.|

   > [!NOTE]
   > Sie müssen nicht alle **Standardroutingeinstellungen** angeben. Es ist auch zulässig, nur einen Teil der Einstellungen anzugeben.
1. Klicken Sie auf **Weiter: Überprüfen und erstellen**.

#### <a name="data-connection---review--create-tab"></a>Datenverbindung: Registerkarte „Überprüfen und erstellen“

1. Überprüfen Sie die Ressourcen, die automatisch für Sie erstellt wurden, und wählen Sie **Erstellen** aus.

    :::image type="content" source="../media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Überprüfen und Erstellen einer Datenverbindung für Event Grid":::

### <a name="deployment"></a>Bereitstellung

Warten Sie, bis die Bereitstellung abgeschlossen wurde. Wenn bei Ihrer Bereitstellung ein Fehler aufgetreten ist, können Sie neben der fehlerhaften Phase auf **Vorgangsdetails** klicken, um weitere Informationen zur Fehlerursache anzuzeigen. Sie können auch **Erneut bereitstellen** auswählen, um die Bereitstellung der Ressourcen zu wiederholen. Sie können die Parameter vor der Bereitstellung ändern.

:::image type="content" source="../media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Bereitstellen von Event Grid-Ressourcen":::

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Nachdem nun eine Verbindung zwischen Data Explorer und dem Speicherkonto besteht, können Sie Beispieldaten erstellen.

### <a name="upload-blob-to-the-storage-container"></a>Hochladen eines Blobs in den Speichercontainer

Wir verwenden ein kleines Shellskript, das einige grundlegenden Azure CLI-Befehle für die Interaktion mit Azure Storage-Ressourcen aufruft. Das Skript bewirkt Folgendes:

1. Erstellen eines Blobcontainers in Ihrem Speicherkonto
1. Hochladen einer vorhandenen Datei (als Blob) in diesen Container
1. Auflisten der Blobs im Container

Das Skript kann per [Azure Cloud Shell](/azure/cloud-shell/overview) direkt im Portal ausgeführt werden.

Speichern Sie die Daten in einer Datei, und laden Sie sie mit diesem Skript hoch:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
    #!/bin/bash
    ### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Für eine optimale Leistung bei der Erfassung muss die *unkomprimierte* Größe der für die Erfassung übertragenen komprimierten Blobs übermittelt werden. Da Event Grid-Benachrichtigungen nur grundlegende Details enthalten, müssen die Größeninformationen explizit übermittelt werden. Die Informationen zur unkomprimierten Größe können durch Festlegen der `rawSizeBytes`-Eigenschaft in den Blobmetadaten angegeben werden, wobei die Größenangabe der *unkomprimierten* Daten in Byte erfolgt.

### <a name="rename-blob"></a>Umbenennen eines Blobs

Wenn Sie Daten aus ADLSv2-Speicher erfassen und *Blob umbenannt* als Ereignistyp für die Datenverbindung definiert haben, ist der Auslöser für die Bloberfassung die Blobumbenennung. Navigieren Sie zum Umbenennen eines Blobs im Azure-Portal zum Blob, klicken Sie mit der rechten Maustaste auf das Blob, und wählen Sie **Umbenennen** aus:

   :::image type="content" source="../media/ingest-data-event-grid/rename-blob-in-the-portal.png" alt-text="Umbenennen eines Blobs im Azure-Portal":::

### <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können die [Datenerfassungseigenschaften](data-explorer-ingest-event-grid-overview.md#ingestion-properties) für die Bloberfassung über die Blobmetadaten angeben.

> [!NOTE]
> Data Explorer löscht die Blobs nach der Erfassung nicht.
> Behalten Sie die Blobs für drei bis fünf Tage bei.
> Informationen zum Löschen von Blobs finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal).

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

> [!NOTE]
> Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist.
> Die Richtlinie ist standardmäßig auf fünf Minuten festgelegt.
> Sie kann bei Bedarf zu einem späteren Zeitpunkt geändert werden. In diesem Artikel ist mit einer Wartezeit von wenigen Minuten zu rechnen.

1. Im Azure-Portal sehen Sie unter Ihrer Event Grid-Instanz den Anstieg der Aktivität während der App-Ausführung.

    :::image type="content" source="../media/ingest-data-event-grid/event-grid-graph.png" alt-text="Aktivitätsgraph für Event Grid":::

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```kusto
    TestTable
    | count
    ```

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```kusto
    TestTable
    ```

    Das Resultset sollte wie auf der folgenden Abbildung aussehen:

    :::image type="content" source="../media/ingest-data-event-grid/table-result.png" alt-text="Nachrichten-Resultset für Event Grid":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Event Grid-Instanz nicht erneut verwenden möchten, sollten Sie die automatisch erstellten Komponenten „Event Grid-Abonnement“, „Event Hub-Namespace“ und „Event Hub“ bereinigen, um das Anfallen von Kosten zu vermeiden.

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Alle Ressourcen** aus.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Auswählen aller Ressourcen für die Event Grid-Bereinigung":::

1. Suchen Sie nach Ihrem Event Hub-Namespace, und wählen Sie **Löschen** aus, um ihn zu löschen:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-find-event-hub-namespace-delete.png" alt-text="Bereinigen des Event Hub-Namespace":::

1. Bestätigen Sie im Formular „Ressourcen löschen“ den Löschvorgang, um den Event Hub-Namespace und die Event Hub-Ressourcen zu löschen.

1. Wechseln Sie zum Speicherkonto. Wählen Sie im linken Menü die Option **Ereignisse** aus:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Auswählen zu bereinigender Ereignisse für Event Grid":::

1. Wählen Sie unterhalb des Graphs Ihr Event Grid-Abonnement aus, und wählen Sie dann **Löschen** aus, um es zu löschen:

    :::image type="content" source="../media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Löschen des Event Grid-Abonnements":::

1. Zum Löschen Ihrer Event Grid-Datenverbindung wechseln Sie zu Ihrem Data Explorer-Cluster. Wählen Sie im Menü auf der linken Seite **Datenbanken** aus.

1. Wählen Sie Ihre Datenbank **TestDatabase** aus:

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Auswählen der Datenbank zum Bereinigen von Ressourcen":::

1. Wählen Sie im Menü auf der linken Seite die Option **Datenerfassung** aus.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Auswählen von „Datenerfassung“ zum Bereinigen von Ressourcen":::

1. Wählen Sie Ihre Datenverbindung *test-grid-connection* aus, und wählen Sie dann **Löschen** aus, um sie zu löschen.

## <a name="next-steps"></a>Nächste Schritte

- [Analysieren mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
