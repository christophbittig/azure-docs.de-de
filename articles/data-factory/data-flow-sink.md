---
title: Senkentransformation in einem Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie im Zuordnungsdatenfluss eine Senkentransformation konfigurieren.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/10/2021
ms.openlocfilehash: afdc363c53790f1710ee274d5430416e415059fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059946"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Senkentransformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Nachdem Sie die Datentransformation abgeschlossen haben, schreiben Sie sie mithilfe der Senkentransformation in einen Zielspeicher. Jeder Datenfluss erfordert mindestens eine Senkentransformation. Sie können aber so viele Senken wie erforderlich hinzufügen, um Ihren Transformationsfluss zu realisieren. Um in weitere Senken zu schreiben, erstellen Sie über neue Verzweigungen und bedingte Teilungen neue Datenströme.

Jede Senkentransformation ist genau einem Datasetobjekt oder einem verknüpften Dienst zugeordnet. Die Form und Position der Daten, in die geschrieben werden soll, richtet sich nach der Senkentransformation.

## <a name="inline-datasets"></a>Inlinedatasets

Beim Erstellen einer Senkentransformation legen Sie fest, ob Ihre Senkeninformationen innerhalb eines Datasetobjekts oder innerhalb der Senkentransformation definiert sind. Die meisten Formate sind nur in einem von beiden verfügbar. Weitere Informationen zur Verwendung eines bestimmten Connectors finden Sie im Dokument zu diesem Connector.

Wenn ein Format sowohl für Inline- als auch in einem Datasetobjekt unterstützt wird, ergeben sich Vorteile für beide. Datasetobjekte sind wiederverwendbare Entitäten, die in anderen Datenflüssen und Aktivitäten wie Kopiervorgängen genutzt werden können. Diese wiederverwendbaren Entitäten sind besonders nützlich, wenn Sie ein verstärktes Schema verwenden. Datasets befinden sich nicht in Spark. Gelegentlich müssen Sie bestimmte Einstellungen oder Schemaprojektionen in der Senkentransformation überschreiben.

Inlinedatasets werden empfohlen, wenn flexible Schemas, einmalig genutzte Senkeninstanzen oder parametrisierte Senken verwendet werden. Wenn Ihre Senke stark parametrisiert ist, können Sie mit Inline-Datasets kein „Dummy“-Objekt erstellen. Inline-Datasets befinden sich in Spark, und ihre Eigenschaften sind für den Datenfluss nativ.

Um ein Inline-Dataset zu verwenden, wählen Sie das gewünschte Format im Selektor **Senkentyp** aus. Anstatt ein Senkendataset auszuwählen, wählen Sie den verknüpften Dienst aus, mit dem Sie eine Verbindung herstellen möchten.

:::image type="content" source="media/data-flow/inline-selector.png" alt-text="Screenshot mit hervorgehobener Auswahl von „Inline“.":::

## <a name="workspace-db-synapse-workspaces-only"></a>Workspace DB (nur Synapse-Arbeitsbereiche)

Bei Verwendung von Datenflüssen in Azure Synapse-Arbeitsbereichen verfügen Sie über eine zusätzliche Option, um Ihre Daten direkt in einen Datenbanktyp umzuwandeln, der in Ihrem Synapse-Arbeitsbereich vorhanden ist. Es ist dann nicht mehr erforderlich, für diese Datenbanken verknüpfte Dienste oder Datasets hinzuzufügen.

> [!NOTE]
> Der Workspace DB-Connector von Azure Synapse befindet sich derzeit in der öffentlichen Vorschauphase und funktioniert nur bei Spark Lake-Datenbanken.

:::image type="content" source="media/data-flow/syms-sink.png" alt-text="Screenshot: Hervorgehobene Auswahl von „Workspace DB“":::

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Unterstützte Senkentypen

Der Zuordnungsdatenfluss folgt einem Ansatz zum Extrahieren, Laden und Transformieren (ELT) und funktioniert mit *Stagingdatasets* in Azure. Derzeit können die folgenden Datasets in einer Quelltransformation verwendet werden.

| Connector | Format | Dataset/Inline |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL-API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Model](format-common-data-model.md#sink-properties)<br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure-Datenbank für PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL-Datenbank](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/✓ |
| [Verwaltete Azure SQL-Datenbank-Instanz](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |
| [SQL Server](connector-sql-server.md) | | ✓/✓ |

Die für diese Connectors spezifischen Einstellungen befinden sich auf der Registerkarte **Einstellungen**. Informationen und Beispiele zu Datenflussskripts zu diesen Einstellungen finden Sie in der Connectordokumentation.

Der Dienst hat Zugriff auf mehr als [90 native Connectors](connector-overview.md). Um Daten aus Ihrem Datenfluss in diese anderen Quellen zu schreiben, verwenden Sie die Kopieraktivität zum Laden der Daten aus einer unterstützten Senke.

## <a name="sink-settings"></a>Senkeneinstellungen

Nachdem Sie eine Senke hinzugefügt haben, konfigurieren Sie sie auf der Registerkarte **Senke**. Hier können Sie das Dataset auswählen oder erstellen, in das die Senke schreibt. Entwicklungswerte für Datasetparameter können in [Debugeinstellungen](concepts-data-flow-debug-mode.md) konfiguriert werden. (Der Debugmodus muss aktiviert sein.)

Im folgenden Video werden verschiedene Senkenoptionen für Dateitypen mit Texttrennzeichen beschrieben.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

:::image type="content" source="media/data-flow/sink-settings.png" alt-text="Screenshot mit Hervorhebung von „Senkeneinstellungen“.":::

**Schemaabweichung**: [Schemaabweichung](concepts-data-flow-schema-drift.md) ist die Fähigkeit des Diensts, flexible Schemas in Ihren Datenflüssen nativ zu verarbeiten, ohne Spaltenänderungen explizit definieren zu müssen. Aktivieren Sie **Schemaabweichung zulassen**, wenn Sie zusätzliche Spalten zusätzlich zur Definition im Datenschema der Senke schreiben möchten.

**Schema überprüfen:** Wenn „Schema überprüfen“ ausgewählt ist, führt der Datenfluss zu einem Fehler, wenn eine der Spalten in der Senkenprojektion im Senkenspeicher nicht gefunden wird oder wenn die Datentypen nicht übereinstimmen. Verwenden Sie diese Einstellung, um eine Übereinstimmung des Senkenschemas mit dem Vertrag Ihrer definierten Projektion zu erzwingen. Dies ist in Szenarios mit Datenbanksenken nützlich, um anzugeben, dass die Spaltennamen oder -typen geändert wurden.


## <a name="cache-sink"></a>Cachesenke

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

Von einer *Cachesenke* wird dann gesprochen, wenn Daten von einem Datenfluss in den Spark-Cache statt in einen Datenspeicher geschrieben werden. Bei Zuordnungsdatenflüssen kann mithilfe einer *Cachesuche* mehrmals innerhalb desselben Datenflusses auf diese Daten verwiesen werden. Dies ist nützlich, wenn Sie auf Daten als Teil eines Ausdrucks verweisen möchten, die Spalten jedoch nicht explizit damit verknüpfen möchten. Gängige Beispiele, in denen eine Cachesenke hilfreich sein kann, ist das Suchen eines Höchstwerts in einem Datenspeicher und das Abgleichen von Fehlercodes mit einer Fehlermeldungsdatenbank. 

Um in eine Cachesenke zu schreiben, fügen Sie eine Senkentransformation hinzu, und wählen Sie **Cache** als Senkentyp aus. Im Gegensatz zu anderen Senkentypen müssen Sie kein Dataset oder keinen verknüpften Dienst auswählen, da Sie nicht in einen externen Speicher schreiben. 

:::image type="content" source="media/data-flow/select-cache-sink.png" alt-text="Auswählen der Cachesenke":::

In den Senkeneinstellungen können Sie optional die Schlüsselspalten der Cachesenke angeben. Diese werden bei Verwendung der Funktion `lookup()` in einer Cachesuche als Übereinstimmungsbedingungen verwendet. Wenn Sie Schlüsselspalten angeben, können Sie die Funktion `outputs()` nicht in einer Cachesuche verwenden. Weitere Informationen zur Syntax für die Cachesuche finden Sie unter [zwischengespeicherten Suchen](concepts-data-flow-expression-builder.md#cached-lookup).

:::image type="content" source="media/data-flow/cache-sink-key-columns.png" alt-text="Schlüsselspalten der Cachesenke":::

Wenn Sie beispielsweise eine einzelne Schlüsselspalte `column1` in einer Cachesenke namens `cacheExample` angeben, wird beim Aufruf von `cacheExample#lookup()` mit einem Parameter festgelegt, mit welcher Zeile in der Cachesenke der Vergleich durchgeführt werden soll. Die Funktion gibt eine einzelne komplexe Spalte mit Unterspalten für jede zugeordnete Spalte aus.

> [!NOTE]
> Eine Cachesenke muss sich in einem vollständig unabhängigen Datenstrom von allen Transformationen befinden, die über eine Cachesuche darauf verweisen. Eine Cachesenke muss außerdem als erste Senke geschrieben werden. 

**Schreiben in die Aktivitätsausgabe**: Die zwischengespeicherte Senke kann optional Ihre Ausgabedaten in die Eingabe der nächsten Pipelineaktivität schreiben. Dadurch können Sie Daten schnell und einfach aus Ihrer Datenflussaktivität übergeben, ohne sie in einem Datenspeicher dauerhaft speichern zu müssen.

## <a name="field-mapping"></a>Feldzuordnung

Ähnlich wie bei einer Auswahltransformation können Sie auf der Registerkarte **Zuordnung** der Senke festlegen, welche eingehenden Spalten geschrieben werden. Standardmäßig werden alle Eingabespalten – auch abweichende Spalten – zugeordnet. Dieses Verhalten wird als *automatische Zuordnung* bezeichnet.

Wenn Sie die automatische Zuordnung deaktivieren, können Sie feste spaltenbasierte Zuordnungen oder regelbasierte Zuordnungen hinzuzufügen. Bei regelbasierten Zuordnungen können Sie Ausdrücke mit Musterabgleich schreiben. Bei der festen Zuordnung werden logische und physische Spaltennamen zugeordnet. Weitere Informationen zur regelbasierten Zuordnung finden Sie unter [Spaltenmuster im Zuordnungsdatenfluss](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Benutzerdefinierte Senkenreihenfolge

Standardmäßig werden Daten ohne festgeschriebene Reihenfolge in mehrere Senken geschrieben. Die Ausführungs-Engine schreibt im Verlauf der Transformationslogik Daten parallel. Dabei kann bei jeder Ausführung die Senkenreihenfolge variieren. Um die genaue Reihenfolge der Senken anzugeben, aktivieren Sie auf der Registerkarte **Allgemein** des Datenflusses **Benutzerdefinierte Senkenreihenfolge**. Falls aktiviert, erfolgt in den Senken das Schreiben sequenziell in aufsteigender Reihenfolge.

:::image type="content" source="media/data-flow/custom-sink-ordering.png" alt-text="Screenshot mit Hervorhebung von „Benutzerdefinierte Senkenreihenfolge“":::

> [!NOTE]
> Bei Verwendung von [zwischengespeicherten Suchen](./concepts-data-flow-expression-builder.md#cached-lookup) müssen Sie sicherstellen, dass die zwischengespeicherten Senken in der Senkenreihenfolge auf 1 festgelegt sind, wobei es sich um den niedrigsten (oder ersten) Rang handelt.

:::image type="content" source="media/data-flow/cache-2.png" alt-text="Benutzerdefinierte Senkenreihenfolge":::

### <a name="sink-groups"></a>Senkengruppen

Sie können Senken gruppieren, indem Sie für eine Reihe von Senken die gleiche Auftragsnummer anwenden. Der Dienst behandelt diese Senken als Gruppen, die parallel ausgeführt werden können. Optionen für die parallele Ausführung werden in der Pipeline-Datenflussaktivität angezeigt.

## <a name="error-row-handling"></a>Fehlerzeilenbehandlung

Beim Schreiben in Datenbanken können aufgrund von Einschränkungen durch das Ziel Fehler bei bestimmten Datenzeilen auftreten. Standardmäßig scheitert eine Datenflussausführung beim ersten erhaltenen Fehler. In bestimmten Connectors können Sie eine Option **Bei Fehler fortsetzen** auswählen, die einen Abschluss des Datenflusses auch dann ermöglicht, wenn einzelne Zeilen Fehler aufweisen. Diese Funktion ist derzeit nur in Azure SQL-Datenbank und Azure Synapse verfügbar. Weitere Informationen finden Sie unter [Fehlerzeilenbehandlung in Azure SQL-Datenbank](connector-azure-sql-database.md#error-row-handling).

Es folgt ein Videotutorial zur automatischen Verwendung der Fehlerzeilenbehandlung für Datenbanken in Ihrer Senkentransformation.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Datenvorschau in Senken

Beim Abrufen einer Datenvorschau im Debugmodus werden keine Daten in die Senke geschrieben. Es wird eine Momentaufnahme der Daten zurückgegeben, aber nichts in das Ziel geschrieben. Um das Schreiben von Daten in die Senke zu überprüfen, führen Sie einen Pipelinedebugvorgang in der Pipelinecanvas aus.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="example"></a>Beispiel

Es folgt ein Beispiel einer Senkentransformation und des zugehörigen Datenflussskripts:

```
sink(input(
        movie as integer,
        title as string,
        genres as string,
        year as integer,
        Rating as integer
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:false,
    updateable:true,
    upsertable:false,
    keys:['movie'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    saveOrder: 1,
    errorHandlingOption: 'stopOnFirstError') ~> sink1
```

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun Ihren Datenfluss erstellt haben, fügen Sie [Ihrer Pipeline eine Aktivität zur Ausführung eines Datenflusses](concepts-data-flow-overview.md) hinzu.
