---
title: Problembehandlung für den Azure Cosmos DB-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit den Connectors für Azure Cosmos DB und Azure Cosmos DB (SQL-API) in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390545"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Azure Cosmos DB-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit den Connectors für Azure Cosmos DB und Azure Cosmos DB (SQL-API) in Azure Data Factory und Azure Synapse.

## <a name="error-message-request-size-is-too-large"></a>Fehlermeldung: Anforderung ist zu groß.

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB mit einer Standardgröße für Schreibbatches kopieren, erhalten Sie den folgenden Fehler: `Request size is too large.`

- **Ursache:** Azure Cosmos DB schränkt die Größe einer einzelnen Anforderung auf 2 MB ein. Die Formel lautet: *Anforderungsgröße = Einzeldokumentgröße \* Schreibbatchgröße*. Wenn Ihr Dokument zu groß ist, ist beim Standardverhalten auch die Anforderung zu groß. Sie können die Batchgröße für Schreibvorgänge optimieren.

- **Lösung:** Reduzieren Sie in der Senke der Kopieraktivität den Wert für die *Schreibbatchgröße* (Standardwert ist „10.000“).

## <a name="error-message-unique-index-constraint-violation"></a>Fehlermeldung: Einschränkungsverletzung für eindeutigen Index

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB kopieren, erhalten Sie den folgenden Fehler:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Ursache:** Es gibt zwei mögliche Ursachen:

    - Ursache 1: Wenn Sie **Einfügen** als Schreibverhalten verwenden, bedeutet dieser Fehler, dass Ihre Quelldaten Zeilen oder Objekte mit der gleichen ID enthalten.
    - Ursache 2: Wenn Sie **Upsert** als Schreibverhalten verwenden und einen anderen eindeutigen Schlüssel für den Container festlegen, bedeutet dieser Fehler, dass Ihre Quelldaten über Zeilen oder Objekte mit unterschiedlichen IDs verfügen, aber für den definierten eindeutigen Schlüssel den gleichen Wert aufweisen.

- **Lösung:** 

    - Legen Sie für Ursache 1 die Option **Upsert** als Schreibverhalten fest.
    - Stellen Sie für Ursache 2 sicher, dass jedes Dokument über einen anderen Wert für den definierten eindeutigen Schlüssel verfügt.

## <a name="error-message-request-rate-is-large"></a>Fehlermeldung: Anforderungsrate ist hoch

- **Symptome:** Wenn Sie Daten in Azure Cosmos DB kopieren, erhalten Sie den folgenden Fehler:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Ursache:** Die Anzahl der verwendeten Anforderungseinheiten (Request Units, RUs) ist größer als die verfügbaren RUs, die in Azure Cosmos DB konfiguriert sind. Informationen dazu, wie Azure Cosmos DB RUs berechnet, finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Lösung:** Versuchen Sie es mit einer der beiden folgenden Lösungen:

    - Legen Sie die Anzahl der *Container-RUs* in Azure Cosmos DB auf einen größeren Wert fest. Mit dieser Lösung wird die Leistung der Kopieraktivität verbessert, aber es fallen Mehrkosten in Azure Cosmos DB an. 
    - Legen Sie *writeBatchSize* auf einen niedrigeren Wert (z. B. auf 1.000) fest, und verringern Sie den Wert von *parallelCopies* (z. B. auf 1). Mit dieser Lösung wird die Leistung der Kopierausführung verringert, aber es fallen keine Mehrkosten in Azure Cosmos DB an.

## <a name="columns-missing-in-column-mapping"></a>Fehlenden Spalte in Spaltenzuordnung

- **Symptome:** Beim Importieren eines Schemas für Azure Cosmos DB für die Spaltenzuordnung fehlen einige Spalten. 

- **Ursache**: Azure Data Factory- und Synapse-Pipelines leiten das Schema von den ersten 10 Azure Cosmos DB-Dokumenten ab. Wenn einige Dokumentspalten oder -eigenschaften keine Werte enthalten, wird das Schema nicht erkannt und folglich nicht angezeigt.

- **Lösung:** Sie können die Abfrage wie im folgenden Code gezeigt optimieren, um zu erzwingen, dass die Spaltenwerte im Resultset mit leeren Werten angezeigt werden. Nehmen Sie an, dass die Spalte *impossible* in den ersten zehn Dokumenten fehlt. Alternativ können Sie die Spalte auch manuell für die Zuordnung hinzufügen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fehlermeldung: GuidRepresentation für den Reader ist CSharpLegacy

- **Symptome:** Wenn Sie Daten aus Azure Cosmos DB MongoAPI oder MongoDB mit dem UUID-Feld (Universally Unique Identifier) kopieren, erhalten Sie den folgenden Fehler:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Ursache:** Es gibt zwei Möglichkeiten, die UUID in BSON (Binary JSON) darzustellen: UuidStardard und UuidLegacy. Standardmäßig wird UuidLegacy zum Lesen von Daten verwendet. Sie erhalten einen Fehler, wenn für Ihre UUID-Daten in MongoDB UuidStandard festgelegt ist.

- **Lösung:** Fügen Sie der MongoDB-Verbindungszeichenfolge die Option *uuidRepresentation=standard* hinzu. Weitere Informationen finden Sie im Artikel zur [MongoDB-Verbindungszeichenfolge](connector-mongodb.md#linked-service-properties).

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>Fehlercode: CosmosDbSqlApiOperationFailed

- **Meldung**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Ursache:** Beim CosmosDbSqlApi-Vorgang ist ein Problem aufgetreten.  Dies gilt insbesondere für den Cosmos DB-Connector (SQL-API).

- **Empfehlung**:  Um die Fehlerdetails zu überprüfen, lesen Sie das [Azure Cosmos DB-Hilfedokument](../cosmos-db/troubleshoot-dot-net-sdk.md). Um weitere Unterstützung zu erhalten, nehmen Sie Kontakt mit dem Azure Cosmos DB-Team auf.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
