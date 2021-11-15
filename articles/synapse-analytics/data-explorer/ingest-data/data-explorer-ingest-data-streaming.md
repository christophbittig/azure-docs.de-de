---
title: Konfigurieren der Streamingerfassung in Ihrem Azure Synapse Data Explorer-Pool
description: Hier erfahren Sie, wie Sie Ihren Azure Synapse Data Explorer-Pool konfigurieren und mit dem Laden von Daten mit der Streamingerfassung beginnen.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d7fdc238adda294a570b7656d62368a5d4feae9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479748"
---
# <a name="configure-streaming-ingestion-on-your-azure-synapse-data-explorer-pool-preview"></a>Konfigurieren der Streamingerfassung in Ihrem Azure Synapse Data Explorer-Pool (Vorschau)

Die Streamingerfassung ist nützlich zum Laden von Daten, wenn eine geringe Wartezeit zwischen Erfassung und Abfrage erforderlich ist. Die Streamingerfassung kann in folgenden Szenarien verwendet werden:

- Eine Wartezeit von weniger als einer Sekunde ist erforderlich.
- Optimierung der operativen Verarbeitung vieler Tabellen, bei denen der Datenstrom für die einzelnen Tabellen jeweils relativ klein (wenige Datensätze pro Sekunde), das Gesamtvolumen der Datenerfassung aber hoch ist (mehrere tausend Datensätze pro Sekunde).

Wenn der Datenstrom für die einzelnen Tabellen groß ist (über 4 GB pro Stunde), sollten Sie ggf. die [Batcherfassung](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) nutzen.

Unter [Übersicht über die Datenerfassung in Azure Data Explorer](data-explorer-ingest-data-overview.md) erfahren Sie mehr über verschiedene Erfassungsmethoden.

## <a name="choose-the-appropriate-streaming-ingestion-type"></a>Auswählen des geeigneten Streamingerfassungstyps

Zwei Streamingerfassungstypen werden unterstützt:

| Erfassungstyp | BESCHREIBUNG |
| -- | -- |
| **Event Hub** oder **IoT Hub** | Hubs werden als Datenquellen für das Tabellenstreaming konfiguriert.<br />Informationen zu ihrer Einrichtung finden Sie unter [**Event Hub**](data-explorer-ingest-event-hub-overview.md). <!--  or [**IoT Hub**](ingest-data-iot-hub.md) data ingestion methods --> |
| **Benutzerdefinierte Erfassung** | Für die benutzerdefinierte Erfassung muss eine Anwendung geschrieben werden, die eine der [Clientbibliotheken](/azure/data-explorer/kusto/api/client-libraries?context=/azure/synapse-analytics/context/context) von Azure Synapse Data Explorer verwendet.<br />Konfigurieren Sie die benutzerdefinierte Erfassung anhand der Informationen in diesem Thema. Unter Umständen finden Sie auch die [C#-Beispielanwendung für die Streamingerfassung](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) hilfreich. |

Die folgende Tabelle unterstützt Sie beim Auswählen eines für Ihre Umgebung geeigneten Erfassungstyps:

|Kriterium|Event Hub/IoT Hub|Benutzerdefinierte Erfassung|
|---------|---------|---------|
|Datenverzögerung zwischen der Initiierung der Erfassung und der Verfügbarkeit der Daten für Abfragen | Längere Verzögerung | Kürzere Verzögerung |
|Zusätzlicher Entwicklungsaufwand | Schnelle und einfache Einrichtung, kein zusätzlicher Entwicklungsaufwand | Hoher zusätzlicher Entwicklungsaufwand zum Erstellen einer Anwendung, um die Daten zu erfassen, Fehler zu behandeln und die Datenkonsistenz sicherzustellen. |

> [!NOTE]
> Das Erfassen von Daten aus einem Event Hub in Data Explorer-Pools funktioniert nicht, wenn Ihr Synapse-Arbeitsbereich ein verwaltetes virtuelles Netzwerk mit aktiviertem Datenexfiltrationsschutz verwendet.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Abrufen der Abfrage- und Datenerfassungsendpunkte
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="performance-and-operational-considerations"></a>Überlegungen zu Leistung und Betrieb

Die wichtigsten Aspekte, die sich auf die Streamingerfassung auswirken können:

- **Computespezifikation:** Die Leistung und Kapazität der Streamingerfassung wird bei größeren Data Explorer-Pools skaliert. Die Anzahl der gleichzeitigen Erfassungsanforderungen ist auf sechs pro Kern beschränkt. Für 16 Kernworkloadtypen wie „Für Compute optimiert“ (Groß) und „Datenspeicheroptimiert“ (Groß) beträgt die maximal unterstützte Last beispielsweise 96 gleichzeitige Erfassungsanforderungen. Für zwei Kernworkloadtypen wie „Für Compute optimiert“ (Sehr klein) beträgt die maximal unterstützte Last beispielsweise 12 gleichzeitige Erfassungsanforderungen.
- **Datengrößenbeschränkung:** Die Datengröße für Anforderungen zur Streamingerfassung ist auf 4 MB beschränkt.
- **Schemaaktualisierungen:** Schemaaktualisierungen wie die Erstellung und Änderung von Tabellen und Erfassungszuordnungen können für den Streamingerfassungsdienst bis zu fünf Minuten dauern. Weitere Informationen finden Sie unter [Streamingerfassung und Schemaänderungen](/azure/data-explorer/kusto/management/data-ingestion/streaming-ingestion-schema-changes?context=/azure/synapse-analytics/context/context).
- **SSD-Kapazität:** Wenn die Streamingerfassung in einem Data Explorer-Pool aktiviert wird, wird ein Teil des lokalen SSD-Datenträgers der Data Explorer-Poolcompuer für Streamingerfassungsdaten genutzt, wodurch sich der verfügbare Speicherplatz für den aktiven Cache verringert. Dies gilt auch, wenn gar keine Daten per Streaming erfasst werden.

## <a name="enable-streaming-ingestion-on-your-data-explorer-pool"></a>Aktivieren der Streamingerfassung für Ihren Data Explorer-Pool

Bevor Sie die Streamingerfassung verwenden können, müssen Sie die Funktion für den Data Explorer-Pool aktivieren und eine [Streamingerfassungsrichtlinie](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) definieren. Sie können die Funktion beim [Erstellen des Data Explorer-Pools](#enable-streaming-ingestion-while-creating-a-new-data-explorer-pool) aktivieren oder [einem vorhandenen Data Explorer-Pool hinzufügen](#enable-streaming-ingestion-on-an-existing-data-explorer-pool).

> [!WARNING]
> Überprüfen Sie vor dem Aktivieren der Streamingerfassung die [Einschränkungen](#limitations).

### <a name="enable-streaming-ingestion-while-creating-a-new-data-explorer-pool"></a>Aktivieren der Streamingerfassung beim Erstellen eines neuen Data Explorer-Pools

Sie können die Streamingerfassung beim Erstellen eines neuen Data Explorer-Pools mithilfe von Azure Synapse Studio oder des Azure-Portals aktivieren.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

Wählen Sie beim Erstellen eines Data Explorer-Pools anhand der Schritte unter [Schnellstart: Erstellen eines Data Explorer-Pools mit Synapse Studio (Vorschau)](../data-explorer-create-pool-studio.md#create-a-new-data-explorer-pool) auf der Registerkarte **Zusätzliche Einstellungen** die Optionen **Streamingerfassung** > **Aktiviert** aus.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Aktivieren der Streamingerfassung beim Erstellen eines Data Explorer-Pools in Azure Synapse Data Explorer":::

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie beim Erstellen eines Data Explorer-Pools anhand der Schritte unter [Schnellstart: Erstellen eines Data Explorer-Pools im Azure-Portal (Vorschauversion)](../data-explorer-create-pool-portal.md#create-a-new-data-explorer-pool) auf der Registerkarte **Zusätzliche Einstellungen** die Optionen **Streamingerfassung** > **Aktiviert** aus.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-portal.png" alt-text="Aktivieren der Streamingerfassung beim Erstellen eines Data Explorer-Pools in Azure Synapse Data Explorer":::

---

### <a name="enable-streaming-ingestion-on-an-existing-data-explorer-pool"></a>Aktivieren der Streamingerfassung für einen vorhandenen Data Explorer-Pool

Wenn Sie über einen vorhandenen Data Explorer-Pool verfügen, können Sie die Streamingerfassung mithilfe des Azure-Portals aktivieren.

1. Navigieren Sie im Azure-Portal zu Ihrem Data Explorer-Pool.
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus.
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

### <a name="create-a-target-table-and-define-the-policy"></a>Erstellen einer Zieltabelle und Definieren der Richtlinie

Erstellen Sie eine Tabelle, um die Streamingerfassungsdaten zu empfangen, und definieren Sie die zugehörige Richtlinie mithilfe von Azure Synapse Studio oder des Azure-Portals.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

1. Wählen Sie in Synapse Studio im linken Bereich **Entwickeln** aus.
1. Wählen Sie unter **KQL-Skripts** die Option **&plus;** (Neue Ressource hinzufügen) > **KQL-Skript** aus. Im rechten Bereich können Sie Ihr Skript benennen.
1. Wählen Sie im Menü **Verbinden mit** den Eintrag *contosodataexplorer* aus.
1. Wählen Sie im Menü **Datenbank verwenden** die Option *TestDatenbank* aus.
1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Kopieren Sie einen der folgenden Befehle in den **Abfragebereich**, und wählen Sie **Ausführen** aus: Dadurch wird die [Streamingerfassungsrichtlinie](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) für die von Ihnen erstellte Tabelle oder für die Datenbank definiert, die diese Tabelle enthält.

    > [!TIP]
    > Eine Richtlinie, die auf Datenbankebene definiert ist, gilt für alle vorhandenen und zukünftigen Tabellen in der Datenbank.

    - Verwenden Sie Folgendes, um die Richtlinie für die von Ihnen erstellte Tabelle zu definieren:

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Verwenden Sie Folgendes, um die Richtlinie für die Datenbank zu definieren, die die von Ihnen erstellte Tabelle enthält:

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im Azure Synapse Analytics-Portal zu Ihrem Data Explorer-Pool.
1. Wählen Sie **Abfrage**.
1. Kopieren Sie den folgenden Befehl in den **Abfragebereich**, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen, die die Daten über Streamingerfassung erhalten soll:

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. Kopieren Sie einen der folgenden Befehle in den **Abfragebereich**, und wählen Sie **Ausführen** aus: Dadurch wird die [Streamingerfassungsrichtlinie](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) für die von Ihnen erstellte Tabelle oder für die Datenbank definiert, die diese Tabelle enthält.

    > [!TIP]
    > Eine Richtlinie, die auf Datenbankebene definiert ist, gilt für alle vorhandenen und zukünftigen Tabellen in der Datenbank.

    - Verwenden Sie Folgendes, um die Richtlinie für die von Ihnen erstellte Tabelle zu definieren:

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - Verwenden Sie Folgendes, um die Richtlinie für die Datenbank zu definieren, die die von Ihnen erstellte Tabelle enthält:

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

---

## <a name="create-a-streaming-ingestion-application-to-ingest-data-to-your-data-explorer-pool"></a>Erstellen einer Anwendung für die Streamingerfassung zum Erfassen von Daten in Ihrem Data Explorer-Pool

Erstellen Sie Ihre Anwendung zum Erfassen von Daten in Ihrem Data Explorer-Pool unter Verwendung der von Ihnen bevorzugten Sprache. Verwenden Sie für die Variable *poolPath* den Abfrageendpunkt, den Sie sich unter [Voraussetzungen](#prerequisites) notiert haben.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Kusto.Data;
using Kusto.Ingest;
using System.IO;
using Kusto.Data.Common;

namespace StreamingIngestion
{
    class Program
    {
        static void Main(string[] args)
        {
            string poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
            string appId = "<appId>";
            string appKey = "<appKey>";
            string appTenant = "<appTenant>";
            string dbName = "<dbName>";
            string tableName = "<tableName>";

            // Create Kusto connection string with App Authentication
            var csb =
                new KustoConnectionStringBuilder(poolPath)
                    .WithAadApplicationKeyAuthentication(
                        applicationClientId: appId,
                        applicationKey: appKey,
                        authority: appTenant
                    );

            // Create a disposable client that will execute the ingestion
            using (IKustoIngestClient client = KustoIngestFactory.CreateStreamingIngestClient(csb))
            {
                // Initialize client properties
                var ingestionProperties =
                    new KustoIngestionProperties(
                        databaseName: dbName,
                        tableName: tableName
                    );

                // Ingest from a compressed file
                var fileStream = File.Open("MyFile.gz", FileMode.Open);
                // Create source options
                var sourceOptions = new StreamSourceOptions()
                {
                    CompressionType = DataSourceCompressionType.GZip,
                };
                // Ingest from stream
                var status = client.IngestFromStreamAsync(fileStream, ingestionProperties, sourceOptions).GetAwaiter().GetResult();
            }
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
from Azure Synapse Analytics.kusto.data import KustoConnectionStringBuilder

from Azure Synapse Analytics.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient
)

poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
appId = "<appId>"
appKey = "<appKey>"
appTenant = "<appTenant>"
dbName = "<dbName>"
tableName = "<tableName>"

csb = KustoConnectionStringBuilder.with_aad_application_key_authentication(
    poolPath,
    appId,
    appKey,
    appTenant
)
client = KustoStreamingIngestClient(csb)

ingestionProperties = IngestionProperties(
    database=dbName,
    table=tableName,
    data_format=DataFormat.CSV
)

# Ingest from file
# Automatically detects gz format
client.ingest_from_file("MyFile.gz", ingestion_properties=ingestionProperties) 
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Load modules using ES6 import statements:
import { DataFormat, IngestionProperties, StreamingIngestClient } from "azure-kusto-ingest";
import { KustoConnectionStringBuilder } from "azure-kusto-data";

// For earlier version, load modules using require statements:
// const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
// const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
// const {DataFormat} = require("azure-kusto-ingest").IngestionPropertiesEnums;
// const StreamingIngestClient = require("azure-kusto-ingest").StreamingIngestClient;

const poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
const appId = "<appId>";
const appKey = "<appKey>";
const appTenant = "<appTenant>";
const dbName = "<dbName>";
const tableName = "<tableName>";
const mappingName = "<mappingName>"; // Required for JSON formatted files

const ingestionProperties = new IngestionProperties({
    database: dbName, // Your database
    table: tableName, // Your table
    format: DataFormat.JSON,
    ingestionMappingReference: mappingName
});

// Initialize client with engine endpoint
const client = new StreamingIngestClient(
    KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
        poolPath,
        appId,
        appKey,
        appTenant
    ),
    ingestionProperties
);

// Automatically detects gz format
await client.ingestFromFile("MyFile.gz", ingestionProperties);
```

### <a name="go"></a>[Go](#tab/go)

```go
import (
    "context"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto/ingest"
    "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
)

func ingest() {
    poolPath := "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
    appId := "<appId>"
    appKey := "<appKey>"
    appTenant := "<appTenant>"
    dbName := "<dbName>"
    tableName := "<tableName>"
    mappingName := "<mappingName>" // Optional, can be nil

    // Creates a Kusto Authorizer using your client identity, secret, and tenant identity.
    // You may also uses other forms of authorization, see GoDoc > Authorization type.
    // auth package is: "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
    authorizer := kusto.Authorization{
        Config: auth.NewClientCredentialsConfig(appId, appKey, appTenant),
    }

    // Create a client
    client, err := kusto.New(poolPath, authorizer)
    if err != nil {
        panic("add error handling")
    }

    // Create an ingestion instance
    // Pass the client, the name of the database, and the name of table you wish to ingest into.
    in, err := ingest.New(client, dbName, tableName)
    if err != nil {
        panic("add error handling")
    }

    // Go currently only supports streaming from a byte array with a maximum size of 4 MB.
    jsonEncodedData := []byte("{\"a\":  1, \"b\":  10}\n{\"a\":  2, \"b\":  20}")

    // Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
    if err := in.Stream(context.Background(), jsonEncodedData, ingest.JSON, mappingName); err != nil {
        panic("add error handling")
    }
}
```

### <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.Azure Synapse Analytics.kusto.data.auth.ConnectionStringBuilder;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClient;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClientFactory;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestionProperties;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.result.OperationStatus;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.CompressionType;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.StreamSourceInfo;
import java.io.FileInputStream;
import java.io.InputStream;

public class FileIngestion {
    public static void main(String[] args) throws Exception {
        String poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
        String appId = "<appId>";
        String appKey = "<appKey>";
        String appTenant = "<appTenant>";
        String dbName = "<dbName>";
        String tableName = "<tableName>";

        // Build connection string and initialize
        ConnectionStringBuilder csb =
            ConnectionStringBuilder.createWithAadApplicationCredentials(
                poolPath,
                appId,
                appKey,
                appTenant
            );

        // Initialize client and its properties
        IngestClient client = IngestClientFactory.createClient(csb);
        IngestionProperties ingestionProperties =
            new IngestionProperties(
                dbName,
                tableName
            );

        // Ingest from a compressed file
        // Create Source info
        InputStream zipInputStream = new FileInputStream("MyFile.gz");
        StreamSourceInfo zipStreamSourceInfo = new StreamSourceInfo(zipInputStream);
        // If the data is compressed
        zipStreamSourceInfo.setCompressionType(CompressionType.gz);
        // Ingest from stream
        OperationStatus status = client.ingestFromStream(zipStreamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
    }
}
```

---

## <a name="disable-streaming-ingestion-on-your-data-explorer-pool"></a>Deaktivieren der Streamingerfassung für Ihren Data Explorer-Pool

> [!WARNING]
> Die Deaktivierung der Streamingerfassung kann mehrere Stunden dauern.

Bevor Sie die Streamingerfassung für Ihren Data Explorer-Pool deaktivieren, löschen Sie die [Streamingerfassungsrichtlinie](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) aus allen relevanten Tabellen und Datenbanken. Durch das Entfernen der Streamingerfassungsrichtlinie wird eine Neuanordnung der Daten in Ihrem Data Explorer-Pool ausgelöst. Die Streamingerfassungsdaten werden aus dem anfänglichen Speicher in den permanenten Speicher im Spaltenspeicher (Erweiterungen oder Shards) verschoben. Dieser Vorgang kann abhängig von der Datenmenge im anfänglichen Speicher zwischen einigen Sekunden und wenigen Stunden dauern.

### <a name="drop-the-streaming-ingestion-policy"></a>Löschen der Streamingerfassungsrichtlinie

Sie können die Streamingerfassungsrichtlinie mithilfe von Azure Synapse Studio oder des Azure-Portals löschen.

#### <a name="studio"></a>[Studio](#tab/azure-studio)

1. Wählen Sie in Synapse Studio im linken Bereich **Entwickeln** aus.
1. Wählen Sie unter **KQL-Skripts** die Option **&plus;** (Neue Ressource hinzufügen) > **KQL-Skript** aus. Im rechten Bereich können Sie Ihr Skript benennen.
1. Wählen Sie im Menü **Verbinden mit** den Eintrag *contosodataexplorer* aus.
1. Wählen Sie im Menü **Datenbank verwenden** die Option *TestDatenbank* aus.
1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen.

    ```kusto
    .delete table TestTable policy streamingingestion
    ```

1. Navigieren Sie im Azure-Portal zu Ihrem Data Explorer-Pool.
1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus.
1. Wählen Sie im Bereich **Konfigurationen** die Option **Ein** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im Azure-Portal zu Ihrem Data Explorer-Pool, und wählen Sie **Abfragen** aus.
1. Kopieren Sie zum Löschen der Streamingerfassungsrichtlinie aus der Tabelle den folgenden Befehl in den **Abfragebereich**, und wählen Sie **Ausführen** aus:

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus.
1. Wählen Sie im Bereich **Konfigurationen** die Option **Off** aus, um die **Streamingerfassung** zu aktivieren.
1. Wählen Sie **Speichern** aus.

---

## <a name="limitations"></a>Einschränkungen

- [Datenbankcursor](/azure/data-explorer/kusto/management/databasecursor?context=/azure/synapse-analytics/context/context) werden für eine Datenbank nicht unterstützt, wenn für diese selbst oder für eine Ihrer Tabellen die [Richtlinie für die Streamingerfassung](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) definiert und aktiviert wurde.
- [Datenzuordnungen](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) müssen [vorab erstellt](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) werden, damit sie bei der Streamingerfassung verwendet werden können. Individuelle Anforderungen zur Streamingerfassung bieten keine Inlinedatenzuordnungen.
- [Erweiterungstags](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) können für die Streamingerfassungsdaten nicht festgelegt werden.
- [Aktualisierungsrichtlinie:](/azure/data-explorer/kusto/management/updatepolicy?context=/azure/synapse-analytics/context/context) Die Aktualisierungsrichtlinie kann nur auf die neu erfassten Daten in der Quelltabelle und nicht auf andere Daten oder Tabellen in der Datenbank verweisen.
- Wenn die Streamingerfassung für eine der Tabellen der Datenbank verwendet wird, kann diese Datenbank nicht als Leader für Follower-Datenbanken oder als Datenanbieter für die Azure Synapse Analytics-Datenfreigabe verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Analysieren mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
