---
title: Migrieren vorhandener Daten zu einem Tabellen-API-Konto in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie lokale Daten oder Clouddaten zu einem Azure-Tabellen-API-Konto in Azure Cosmos DB migrieren bzw. in ein solches Konto importieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: c47b92d416bd24554c6e4cecc1aadb81c331616e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061266"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrieren Ihrer Daten zu einem Azure Cosmos DB-Konto für die Tabellen-API
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

In diesem Tutorial erfahren Sie, wie Sie Daten für die Verwendung mit der [Table-API](introduction.md) von Azure Cosmos DB importieren. Wenn Sie Daten in Azure Table Storage gespeichert haben, können Sie entweder das Datenmigrationstool oder AzCopy verwenden, um Ihre Daten für die Azure Cosmos DB-Tabellen-API zu importieren. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Importieren von Daten mit dem Datenmigrationstool
> * Importieren von Daten mit AzCopy

## <a name="prerequisites"></a>Voraussetzungen

* **Erhöhung des Durchsatzes:** Die Dauer der Datenmigration richtet sich nach dem Durchsatzwert, den Sie für einen einzelnen Container oder eine Gruppe von Containern einrichten. Achten Sie darauf, dass Sie den Durchsatz für größere Datenmigrationen erhöhen. Nachdem die Migration abgeschlossen ist, können Sie den Durchsatz wieder verringern, um Kosten zu sparen.

* **Erstellen von Azure Cosmos DB-Ressourcen:** Bevor Sie mit der Migration der Daten beginnen, erstellen Sie vorab alle Ihre Tabellen im Azure-Portal. Wenn Sie als Migrationsziel ein Azure Cosmos DB-Konto mit Durchsatz auf Datenbankebene verwenden, geben Sie beim Erstellen der Azure Cosmos DB-Tabellen einen Partitionsschlüssel an.

## <a name="data-migration-tool"></a>Datenmigrationstool

Sie können das Befehlszeilen-Datenmigrationstool (dt.exe) in Azure Cosmos DB verwenden, um Ihre vorhandenen Azure Table Storage-Daten in ein Tabellen-API-Konto zu importieren. 

So migrieren Sie Tabellendaten:

1. Laden Sie das Migrationstool von [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) herunter.
2. Führen Sie `dt.exe` mit den Befehlszeilenargumenten für Ihr Szenario aus. `dt.exe` akzeptiert einen Befehl im folgenden Format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Für diesen Befehl werden die folgenden Optionen unterstützt:

* **/ErrorLog:** Optional. Name der CSV-Datei zum Umleiten bei Datenübertragungsfehlern.
* **/OverwriteErrorLog:** Optional. Überschreiben der Fehlerprotokolldatei.
* **/ProgressUpdateInterval:** Optional. Standardwert ist `00:00:01`. Das Zeitintervall für die Aktualisierung der Statusanzeige bei der Datenübertragung.
* **/ErrorDetails:** Optional. Standardwert ist `None`. Gibt an, dass für die folgenden Fehler ausführliche Fehlerinformationen angezeigt werden sollen: `None`, `Critical` oder `All`.
* **/EnableCosmosTableLog:** Optional. Leiten Sie das Protokoll an ein Azure Cosmos DB-Tabellenkonto weiter. Wenn diese Option festgelegt ist, wird standardmäßig die Verbindungszeichenfolge des Zielkontos verwendet, sofern nicht auch `/CosmosTableLogConnectionString` angegeben ist. Dies ist hilfreich, wenn mehrere Instanzen des Tools gleichzeitig ausgeführt werden.
* **/CosmosTableLogConnectionString:** Optional. Die Verbindungszeichenfolge zum Weiterleiten des Protokolls an ein Azure Cosmos DB-Remotetabellenkonto.

### <a name="command-line-source-settings"></a>Befehlszeilen-Quelleinstellungen

Verwenden Sie die folgenden Quelloptionen, wenn Sie Azure Table Storage als Quelle für die Migration festlegen.

* **/s:AzureTable:** Lesen von Daten aus Table Storage.
* **/s.ConnectionString:** Verbindungszeichenfolge für den Tabellenendpunkt. Dies können Sie über das Azure-Portal abrufen.
* **/s.LocationMode:** Optional. Standardwert ist `PrimaryOnly`. Gibt an, welcher Standortmodus beim Herstellen einer Verbindung mit Table Storage verwendet werden soll: `PrimaryOnly`, `PrimaryThenSecondary`, `SecondaryOnly`, `SecondaryThenPrimary`.
* **/s.Table:**  Name der Azure-Tabelle.
* **/s.InternalFields:** Legen Sie diese Option für die Tabellenmigration auf `All` fest, da `RowKey` und `PartitionKey` für den Import benötigt werden.
* **/s.Filter:** Optional. Anzuwendende Filterzeichenfolge.
* **/s.Projection:** Optional. Liste der auszuwählenden Spalten,

Öffnen Sie das Azure-Portal, wenn Sie die Quell-Verbindungszeichenfolge beim Importieren aus Table Storage abrufen möchten. Wählen Sie **Speicherkonten** > **Konto** > **Zugriffsschlüssel** aus, und kopieren Sie die **Verbindungszeichenfolge**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot: Optionen „Speicherkonten“ > „Konto“ > „Zugriffsschlüssel“ mit hervorgehobener Schaltfläche „Kopieren“.":::

### <a name="command-line-target-settings"></a>Befehlszeilen-Zieleinstellungen

Verwenden Sie die folgenden Zieloptionen, wenn Sie die Tabellen-API von Azure Cosmos DB als Ziel für die Migration festlegen.

* **/t:T:TableAPIBulk:** Lädt Daten in Batches in die Azure Cosmos DB Tabellen-API hoch.
* **/t.ConnectionString:**  Verbindungszeichenfolge für den Tabellenendpunkt.
* **/t.TableName:** Gibt den Namen der Tabelle an, in die geschrieben werden soll.
* **/t.Overwrite:** Optional. Standardwert ist `false`. Gibt an, ob vorhandene Werte überschrieben werden sollen.
* **/t.MaxInputBufferSize:** Optional. Standardwert ist `1GB`. Schätzwert für die zu puffernden Eingabebytes, bevor Daten in die Senke geleert werden.
* **/t.Throughput:** Optional. Standardwerte des Diensts, wenn nichts angegeben ist. Gibt den Durchsatz an, der für die Tabelle konfiguriert werden soll.
* **/t.MaxBatchSize:** Optional. Standardwert ist `2MB`. Gibt die Batchgröße in Byte an.

### <a name="sample-command-source-is-table-storage"></a>Beispielbefehl: Table Storage ist Quelle

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus Table Storage in die Tabellen-API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmos.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrieren von Daten mithilfe von AzCopy

Sie können auch das Befehlszeilenprogramm AzCopy verwenden, um Daten von Table Storage zur Azure Cosmos DB Tabellen-API zu migrieren. Um AzCopy zu verwenden, müssen Sie Ihre Daten vorher so exportieren, wie in [Exportieren von Daten aus Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) beschrieben. Anschließend importieren Sie die Daten mit dem folgenden Befehl in die Azure Cosmos DB-Tabellen-API. Sie können sie auch in den [Azure-Tabellenspeicher](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) importieren.

Für den Fall, dass Sie in Azure Cosmos DB importieren, können Sie das folgende Beispiel lesen. Beachten Sie das der `/Dest`-Wert `cosmosdb`, nicht `core` verwendet.

Beispiel für den import-Befehl:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Abfragen von Tabellendaten mithilfe der Tabellen-API von Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Abfragen von Daten](tutorial-query-table.md)




