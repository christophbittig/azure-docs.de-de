---
title: Indizieren von Daten aus Azure Table Storage
titleSuffix: Azure Cognitive Search
description: Richten Sie einen Suchindexer ein, um in Azure Table Storage gespeicherte Daten für die Volltextsuche in Azure Cognitive Search zu indizieren.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 8469716c5cc6b3b7c70d5eeb5868f81ae6b40198
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982958"
---
# <a name="index-data-from-azure-table-storage"></a>Indizieren von Daten aus Azure Table Storage

In diesem Artikel erfahren Sie, wie Sie einen Azure-Tabellenindexer zum Extrahieren von Inhalten konfigurieren und dafür sorgen, dass er in Azure Cognitive Search durchsucht werden kann. Dieser Workflow erstellt einen Suchindex für Azure Cognitive Search und lädt ihn mit vorhandenem Inhalt, der aus Azure Table Storage extrahiert wurde.

Sie können einen Azure Table Storage-Indexer mithilfe eines der folgenden Clients einrichten:

* [Azure portal](https://ms.portal.azure.com)
* [REST-API](/rest/api/searchservice/Indexer-operations) für die kognitive Azure-Suche
* [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) für die kognitive Azure-Suche

In diesem Artikel werden die REST-APIs verwendet. 

## <a name="configure-an-indexer"></a>Konfigurieren eines Indexers

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Unter [Erstellen einer Datenquelle](/rest/api/searchservice/create-data-source) wird angegeben, welche Daten indiziert werden müssen. Zudem werden die Anmeldeinformationen für den Zugriff auf die Daten sowie die Richtlinien zur Aktivierung von Azure Cognitive Search festgelegt, um Änderungen an den Daten effizient identifizieren zu können.

Für die Tabellenindizierung muss die Datenquelle über die folgenden Eigenschaften verfügen:

- **name** ist der eindeutige Name der Datenquelle im Suchdienst.
- **type** muss `azuretable` lauten.
- Der **credentials**-Parameter enthält die Speicherkonto-Verbindungszeichenfolge. Näheres erfahren Sie im Abschnitt [Angeben von Anmeldeinformationen](#Credentials).
- **container** legt den Tabellennamen und eine optionale Abfrage fest.
    - Geben Sie den Tabellennamen mit dem Parameter `name` an.
    - Geben Sie optional eine Abfrage mit dem Parameter `query` an. 

> [!IMPORTANT] 
> Verwenden Sie nach Möglichkeit einen Filter für PartitionKey, um eine bessere Leistung zu erzielen. Alle anderen Abfragen führen einen vollständigen Tabellenscan durch, was bei großen Tabellen eine schlechte Leistung zur Folge hat. Lesen Sie den Abschnitt [Leistungsüberlegungen](#Performance).

Senden Sie die folgende Anforderung zum Erstellen einer Datenquelle:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "table-datasource",
    "type" : "azuretable",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
}   
```

<a name="Credentials"></a>

#### <a name="ways-to-specify-credentials"></a>Möglichkeiten zum Angeben von Anmeldeinformationen ####

Sie können die Anmeldeinformationen für die Tabelle mit einer der folgenden Methoden angeben: 

- **Verbindungszeichenfolge für verwaltete Identitäten:** `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` Diese Verbindungszeichenfolge erfordert keinen Kontoschlüssel, Sie müssen jedoch die Anweisungen zum [Einrichten einer Verbindung mit einem Azure Storage-Konto mithilfe einer verwalteten Identität](search-howto-managed-identities-storage.md) befolgen.
- **Verbindungszeichenfolge für den Vollzugriff auf ein Speicherkonto**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Sie können die Verbindungszeichenfolge über das Azure-Portal abrufen, indem Sie auf dem **Blatt des Speicherkontos** zu  > **Einstellungen** > **Schlüssel** (für klassische Speicherkonten) oder zu **Einstellungen** > **Zugriffsschlüssel** (für Speicherkonten von Azure Resource Manager) wechseln.
- **Verbindungszeichenfolge für die Shared Access Signature eines Speicherkontos**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Die Shared Access Signature muss über Listen- und Leseberechtigungen für Container (in diesem Fall Tabellen) und Objekte (Tabellenzeilen) verfügen.
-  **Shared Access Signature für eine Tabelle**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` Die Shared Access Signature sollte über Abfrageberechtigungen (Lesen) für die Tabelle verfügen.

Weitere Informationen zu Shared Access Signatures (SAS) finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Bei Verwendung von SAS-Anmeldeinformationen müssen Sie die Anmeldedaten für die Datenquellen in regelmäßigen Abständen mit erneuerten Signaturen aktualisieren, um den Ablauf zu verhindern. Wenn SAS-Anmeldeinformationen ablaufen, zeigt der Indexer eine Fehlermeldung wie „Die in der Verbindungszeichenfolge angegebenen Anmeldeinformationen sind ungültig oder abgelaufen“ an.  

### <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index

Unter [Erstellen eines Index](/rest/api/searchservice/create-index) werden die Felder in einem Dokument, die Attribute und andere Konstrukte für die Suchoberfläche aufgeführt.

Senden Sie die folgende Anforderung zum Erstellen eines Index:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
        "name" : "my-target-index",
        "fields": [
        { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
}
```

### <a name="step-3-create-an-indexer"></a>Schritt 3: Erstellen eines Indexers

Unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer) wird eine Datenquelle mit einem Zielsuchindex verbunden. Außerdem wird ein Zeitplan zur Automatisierung der Datenaktualisierung bereitgestellt. 

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "table-indexer",
    "dataSourceName" : "table-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```

Dieser Indexer wird alle zwei Stunden ausgeführt. (Das Zeitplanintervall ist auf „PT2H“ festgelegt.) Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie einen Indexer bei Bedarf jederzeit ausführen. Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="handle-field-name-discrepancies"></a>Behandeln von Abweichungen in Feldnamen

Die Feldnamen in Ihrem vorhandenen Index unterscheiden sich manchmal von den Eigenschaftennamen in Ihrer Tabelle. Sie können Feldzuordnungen verwenden, um die Eigenschaftennamen der Tabelle den Feldnamen in Ihrem Suchindex zuzuordnen. Weitere Informationen zu Feldzuordnungen finden Sie unter [Durch Indexerfeldzuordnungen der kognitiven Azure-Suche werden die Unterschiede zwischen Datenquellen und Suchindizes überbrückt](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Behandeln von Dokumentschlüsseln

In der kognitiven Azure-Suche wird ein Dokument mit dem Dokumentschlüssel eindeutig identifiziert. Jeder Suchindex muss über genau ein Schlüsselfeld vom Typ `Edm.String`verfügen. Das Schlüsselfeld ist für jedes Dokument erforderlich, das dem Index hinzugefügt wird. (Es ist gleichzeitig das einzige erforderliche Feld.)

Da Tabellenzeilen über einen Verbundschlüssel verfügen, generiert die kognitive Azure-Suche das synthetische Feld `Key`, bei dem es sich um eine Verkettung von Partitionsschlüssel- und Zeilenschlüsselwerten handelt. Wenn der Partitionsschlüssel einer Zeile beispielsweise `PK1` lautet und der Zeilenschlüssel den Wert `RK1` hat, hat das `Key`-Feld den Wert `PK1RK1`.

> [!NOTE]
> Der Wert von `Key` kann unter Umständen Zeichen enthalten, die in Dokumentschlüsseln ungültig sind, z.B. Bindestriche. Ungültige Zeichen können Sie mit der -`base64Encode` [Feldzuordnungsfunktion](search-indexer-field-mappings.md#base64EncodeFunction) behandeln. Verwenden Sie in diesem Fall auch die URL-sichere Base64-Codierung beim Übergeben von Dokumentschlüsseln in API-Aufrufen (z.B. Suche).
>

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementelle Indizierung und Erkennung von Löschungen

Wenn Sie für einen Tabellenindexer die Ausführung nach einem Zeitplan einrichten, werden nur neue oder aktualisierte Zeilen neu indiziert. Dies wird durch den `Timestamp`-Wert einer Zeile bestimmt. Sie müssen eine Erkennungsrichtlinie für Änderungen angeben. Die inkrementelle Indizierung wird automatisch aktiviert.

Um anzugeben, dass bestimmte Dokumente aus dem Index entfernt werden müssen, können Sie eine Strategie für vorläufiges löschen verwenden. Anstatt eine Zeile zu löschen, fügen Sie eine Eigenschaft hinzu, um anzugeben, dass sie gelöscht ist, und richten Sie eine Richtlinie zur Erkennung einer vorläufigen Löschung für die Datenquelle ein. Bei der folgenden Richtlinie wird eine Zeile beispielsweise als gelöscht angesehen, wenn sie über die `IsDeleted`-Eigenschaft mit dem Wert `"true"` verfügt:

```http
PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-table-datasource",
    "type" : "azuretable",
    "credentials" : { "connectionString" : "<your storage connection string>" },
    "container" : { "name" : "table name", "query" : "<query>" },
    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
}   
```

<a name="Performance"></a>

## <a name="performance-considerations"></a>Überlegungen zur Leistung

In der kognitiven Azure-Suche wird standardmäßig folgender Abfragefilter verwendet: `Timestamp >= HighWaterMarkValue`. Da Azure-Tabellen keinen sekundären Index im `Timestamp`-Feld besitzen, erfordert dieser Abfragetyp einen vollständigen Tabellenscan und ist daher bei großen Tabellen langsam.

Hier lernen Sie zwei Möglichkeiten zum Verbessern der Tabellenindizierungsleistung kennen. Beide Vorgehensweisen basieren auf der Verwendung von Tabellenpartitionen: 

- Wenn Ihre Daten auf natürliche Weise in mehrere Bereiche partitioniert werden können, erstellen Sie eine Datenquelle und einen entsprechenden Indexer für jeden Partitionsbereich. Jeder Indexer muss jetzt nur einen bestimmten Partitionsbereich verarbeiten, was die Abfrageleistung verbessert. Wenn die zu indizierenden Daten auf eine kleine Anzahl fester Partitionen verteilt sind, ist dies noch besser – jeder Indexer führt dann nur einen Partitionsscan durch. Um beispielsweise eine Datenquelle für die Verarbeitung eines Partitionsbereichs mit Schlüsseln von `000` bis `100` zu erstellen, verwenden Sie etwa folgende Abfrage: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Wenn Ihre Daten nach der Zeit partitioniert sind (wenn Sie beispielsweise jeden Tag oder jede Woche eine neue Partition erstellen), ziehen Sie folgenden Ansatz in Betracht: 
    - Verwenden Sie eine Abfrage dieser Form: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Überwachen Sie den Indexerverlauf mit der [API zum Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status), und aktualisieren Sie regelmäßig die `<TimeStamp>`-Bedingung der Abfrage basierend auf dem aktuellen Markierungswert für die Obergrenze. 
    - Wenn Sie eine vollständige Neuindizierung auslösen müssen, müssen Sie mit diesem Ansatz die Datenquellenabfrage zusätzlich zum Indexer zurücksetzen. 

## <a name="see-also"></a>Weitere Informationen

+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Erstellen eines Indexers](search-howto-create-indexers.md)