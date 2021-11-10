---
title: Indizieren von Daten aus Azure Data Lake Storage Gen2
titleSuffix: Azure Cognitive Search
description: Richten Sie einen Azure Data Lake Storage Gen2-Indexer ein, um die Indizierung von Inhalten und Metadaten für die Volltextsuche in Azure Cognitive Search zu automatisieren.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 59b3d06af79d1b2c6f12f7dbb843ae31f203cab6
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894144"
---
# <a name="index-data-from-azure-data-lake-storage-gen2"></a>Indizieren von Daten aus Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie einen Azure Data Lake Storage Gen2-Indexer zum Extrahieren von Inhalten konfigurieren und dafür sorgen, dass er in Azure Cognitive Search durchsucht werden kann. Dieser Workflow erstellt einen Suchindex für Azure Cognitive Search und lädt ihn mit vorhandenem Inhalt, der aus Azure Data Lake Storage Gen2 extrahiert wurde.

Azure Data Lake Storage Gen2 ist über Azure Storage verfügbar. Beim Einrichten eines Azure-Speicherkontos haben Sie die Möglichkeit, einen [hierarchischen Namespace](../storage/blobs/data-lake-storage-namespace.md) zu aktivieren. Auf diese Weise kann die Sammlung von Inhalten in einem Konto in einer Hierarchie von Verzeichnissen und geschachtelten Unterverzeichnissen organisiert werden. Durch das Aktivieren eines hierarchischen Namespace aktivieren Sie [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

In den Beispielen in diesem Artikel werden das Portal und REST-APIs verwendet. Beispiele in C# finden Sie unter [Index Data Lake Gen2 using Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) auf GitHub.

## <a name="supported-access-tiers"></a>Unterstützte Zugriffsebenen

Die Data Lake Storage Gen2-[Zugriffsebenen](../storage/blobs/access-tiers-overview.md) umfassen die heiße, kalte und Archivebene. Indexer können nur auf die heiße und kalte Ebene zugreifen.

## <a name="access-control"></a>Zugriffssteuerung

Data Lake Storage Gen2 implementiert ein [Zugriffssteuerungsmodell](../storage/blobs/data-lake-storage-access-control.md), das sowohl die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) in Azure als auch POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) unterstützt. Zugriffssteuerungslisten werden in Azure Cognitive Search-Szenarien teilweise unterstützt:

+ Die Unterstützung der Zugriffssteuerung ist für den Indexerzugriff auf Inhalte in Data Lake Storage Gen2 aktiviert. Für einen Suchdienst mit einem System oder einer benutzerseitig zugewiesenen verwalteten Identität können Sie Rollenzuweisungen definieren, die den Indexerzugriff auf bestimmte Dateien und Ordner in Azure Storage kontrollieren.

+ Unterstützung von Berechtigungen auf Dokumentebene auf einem Index ist nicht verfügbar. Wenn bei Ihren Zugriffssteuerungen die Zugriffsebene je nach Benutzer variieren, können diese Berechtigungen nicht in einen Suchindex in Ihrem Suchdienst übertragen werden. Alle Benutzer haben dieselbe Zugriffsebene für alle durchsuchbaren und abrufbaren Inhalte im Index.

Wenn die Verwaltung der Zugriffssteuerung für jedes Dokument im Index wichtig ist, kann der Anwendungsentwickler [Sicherheitsfilter](./search-security-trimming-for-azure-search.md) implementieren.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Unterstützte Dokumentformate

Der Blobindexer von Azure Cognitive Search kann Text aus den folgenden Dokumentformaten extrahieren:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="indexing-through-the-azure-portal"></a>Indizierung über das Azure-Portal

Der Azure-Portal unterstützt das Importieren von Daten aus Azure Data Lake Storage Gen2. Navigieren Sie zum Importieren von Daten aus Data Lake Storage Gen2 zu Ihrer Azure Cognitive Search-Dienstseite im Azure-Portal, wählen Sie **Daten importieren** aus, wählen Sie **Azure Data Lake Storage Gen2** aus, und folgen Sie dann dem „Daten importieren“-Ablauf, um Ihre Datenquelle, Ihr Skillset, Ihren Index und Ihren Indexer zu erstellen.

## <a name="indexing-with-the-rest-api"></a>Indizierung mit der REST-API

Der Data Lake Storage Gen2-Indexer wird von der REST-API unterstützt. Befolgen Sie die nachstehenden Anweisungen, um eine Datenquelle, einen Index und einen Indexer zu erstellen.

### <a name="step-1---create-the-data-source"></a>Schritt 1 – Erstellen der Datenquelle

Die Datenquellendefinition gibt den Datenquellentyp sowie andere Eigenschaften für die Authentifizierung und die Verbindung mit dem zu indizierenden Inhalt an.

Die Definition einer Data Lake Storage Gen2-Datenquelle sieht ähnlich wie im folgenden Beispiel aus:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]
    {
        "name" : "adlsgen2-datasource",
        "type" : "adlsgen2",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

Die Eigenschaft `"credentials"` kann eine Verbindungszeichenfolge sein, wie im obigen Beispiel gezeigt, oder einer der alternativen Ansätze, die im nächsten Abschnitt beschrieben werden. Die Eigenschaft `"container"` gibt den Speicherort des Inhalts in Azure Storage an. Mit `"query"` wird ein Unterordner im Container angegeben. Weitere Informationen zu Datenquellendefinitionen finden Sie unter [Erstellen einer Datenquelle (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

#### <a name="credentials"></a>Anmeldeinformationen

Sie haben folgende Möglichkeiten zum Angeben der Anmeldeinformationen für den Container:

**Verbindungszeichenfolge für verwaltete Identitäten**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Diese Verbindungszeichenfolge erfordert keinen Kontoschlüssel, Sie müssen jedoch die Anweisungen zum [Einrichten einer Verbindung mit einem Azure Storage-Konto mithilfe einer verwalteten Identität](search-howto-managed-identities-storage.md) befolgen.

**Verbindungszeichenfolge für den Vollzugriff auf ein Speicherkonto**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Sie können die Verbindungszeichenfolge über das Azure-Portal abrufen, indem Sie auf dem Blatt des Speicherkontos zu „Einstellungen“ > „Schlüssel“ (für klassische Speicherkonten) oder zu „Einstellungen“ > „Zugriffsschlüssel“ (für Azure Resource Manager-Speicherkonten) navigieren.

**SAS-Verbindungszeichenfolge (Shared Access Signature) für ein Speicherkonto**: `{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

Die SAS muss über Listen- und Leseberechtigungen für Container und Objekte (in diesem Fall Blobs) verfügen.

**Shared Access Signature des Containers**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

Die SAS muss über Listen- und Leseberechtigungen für den Container verfügen. Weitere Informationen zu Shared Access Signatures (SAS) finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Bei Verwendung von SAS-Anmeldeinformationen müssen Sie die Anmeldedaten für die Datenquellen in regelmäßigen Abständen mit erneuerten Signaturen aktualisieren, um den Ablauf zu verhindern. Wenn SAS-Anmeldeinformationen ablaufen, zeigt der Indexer eine Fehlermeldung wie „Die in der Verbindungszeichenfolge angegebenen Anmeldeinformationen sind ungültig oder abgelaufen“ an.  

### <a name="step-2---create-an-index"></a>Schritt 2 – Erstellen eines Index

Mit dem Index werden die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben. Bei allen Indexern müssen Sie eine Suchindexdefinition als Ziel angeben. Im folgenden Beispiel wird [Create Index (REST-API)](/rest/api/searchservice/create-index) verwendet. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-target-index",
        "fields": [
          { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
          { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
        ]
    }
```

Indexdefinitionen erfordern ein Feld in der Sammlung `"fields"`, das als Dokumentschlüssel fungiert. Indexdefinitionen sollten auch Felder für Inhalte und Metadaten enthalten.

Bei Blobinhalten ist üblicherweise das Feld **`content`** vorhanden. Es enthält den aus Blobdaten extrahierten Text. Ihre Definition dieses Felds kann ähnlich wie die obige aussehen. Sie sind nicht verpflichtet, diesen Namen zu verwenden, aber Sie können dadurch die Vorteile impliziter Zuordnungen von Feldern nutzen. Der Blobindexer kann Blobinhalte an das Inhaltsfeld „Edm.String“ im Index übermitteln, wobei keine Zuordnung der Felder erforderlich ist.

Sie könnten auch Felder für beliebige Blobmetadaten hinzufügen, die Sie im Index wünschen. Der Indexer kann Eigenschaften von benutzerdefinierten Metadaten, [Standardmetadaten](#indexing-blob-metadata) und [inhaltsspezifischen Metadaten](search-blob-metadata-properties.md) lesen. Weitere Informationen zu Indizes finden Sie unter [Erstellen eines Index](search-what-is-an-index.md).

### <a name="step-3---configure-and-run-the-indexer"></a>Schritt 3 – Konfigurieren und Ausführen des Indexers

Nachdem Sie den Index und die Datenquelle erstellt haben, können Sie den [Indexer](/rest/api/searchservice/create-indexer) erstellen:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "adlsgen2-indexer",
      "dataSourceName" : "adlsgen2-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { 
        "interval" : "PT2H"
      }
    }
```

Dieser Indexer wird zunächst umgehend und danach [planmäßig](search-howto-schedule-indexers.md) alle zwei Stunden ausgeführt (das Planungsintervall ist auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.

<a name="DocumentKeys"></a>

## <a name="defining-document-keys-and-field-mappings"></a>Definieren von Dokumentschlüsseln und Feldzuordnungen

In einem Suchindex wird jedes Dokument anhand des Dokumentschlüssels eindeutig identifiziert. Das gewählte Feld muss den Typ `Edm.String` haben. Für Blobinhalte eignen sich am besten die Metadateneigenschaften des Blobs als Dokumentschlüssel.

+ **`metadata_storage_name`** : Diese Eigenschaft kommt in Frage, aber nur, wenn die Namen in allen Containern und Ordnern, die Sie indizieren, eindeutig sind. Unabhängig vom Speicherort des Blobs ist das Endergebnis, dass der Dokumentschlüssel (Name) im Suchindex eindeutig sein muss, nachdem alle Inhalte indiziert wurden. 

  Ein weiteres mögliches Problem mit dem Speichernamen ist, dass er Zeichen enthalten kann, die für Dokumentschlüssel ungültig sind, z. B. Bindestriche. Ungültige Zeichen können Sie mit der [Feldzuordnungsfunktion](search-indexer-field-mappings.md#base64EncodeFunction) von `base64Encode` behandeln. Wenn Sie dies tun, denken Sie daran, auch Dokumentschlüssel zu codieren, wenn Sie sie in API-Aufrufen wie z. B. zum [Nachschlagen von Dokumenten (REST)](/rest/api/searchservice/lookup-document) übergeben. In .NET können Sie zum Codieren von Zeichen die [UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode)-Methode verwenden.

+ **`metadata_storage_path`** : Die Verwendung des vollständigen Pfads stellt Eindeutigkeit sicher, aber der Pfad enthält definitiv `/`-Zeichen, die in einem [Dokumentschlüssel unzulässig sind](/rest/api/searchservice/naming-rules). Wie zuvor können Sie die `base64Encode`-[Funktion](search-indexer-field-mappings.md#base64EncodeFunction) verwenden, um Zeichen zu codieren.

+ Eine dritte Möglichkeit besteht darin, den Blobs eine benutzerdefinierte Metadateneigenschaft hinzuzufügen. Für diese Option ist es erforderlich, dass diese Metadateneigenschaft im Prozess des Hochladens von Blobs allen Blobs hinzugefügt wird. Da der Schlüssel eine erforderliche Eigenschaft ist, schlägt die Indizierung aller Blobs fehl, denen ein Wert fehlt.

> [!IMPORTANT]
> Wenn keine ausdrückliche Zuordnung für das Schlüsselfeld im Index vorhanden ist, wird in der kognitiven Azure-Suche automatisch `metadata_storage_path` als Schlüssel verwendet, und die Schlüsselwerte werden Base64-codiert (zweite Option oben).
>
> Wenn Sie eine benutzerdefinierte Metadateneigenschaft als Schlüssel verwenden, vermeiden Sie es, Änderungen an dieser Eigenschaft vorzunehmen. Indexer fügen doppelte Dokumente für denselben Blob hinzu, wenn sich die Schlüsseleigenschaft ändert.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt `metadata_storage_name` als Dokumentschlüssel. Angenommen, der Index hat ein Schlüsselfeld mit dem Namen `key` und ein weiteres Feld mit dem Namen `fileSize` zum Speichern der Dokumentgröße. [Feldzuordnungen](search-indexer-field-mappings.md) in der Indexerdefinition legen Feldzuordnungen fest. `metadata_storage_name` verfügt über die [`base64Encode`Feldzuordnungsfunktion](search-indexer-field-mappings.md#base64EncodeFunction) zur Behandlung nicht unterstützter Zeichen.

```http
    PUT https://[service name].search.windows.net/indexers/adlsgen2-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]
    
    {
      "dataSourceName" : "adlsgen2-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

### <a name="how-to-make-an-encoded-field-searchable"></a>Gestalten eines codierten Felds als „durchsuchbar“

Es gibt Fälle, in denen Sie eine codierte Version eines Felds wie `metadata_storage_path` als Schlüssel verwenden müssen, dieses Feld aber auch (ohne Codierung) im Suchindex durchsuchbar sein muss. Um beide Anwendungsfälle zu unterstützen, können Sie `metadata_storage_path` zwei Feldern zuordnen: einem für den Schlüssel (verschlüsselt) und einem zweiten für ein Pfadfeld, von dem wir annehmen können, dass es im Indexschema als „durchsuchbar“ gekennzeichnet ist. Das folgende Beispiel zeigt zwei Feldzuordnungen für `metadata_storage_path`.

```http
    PUT https://[service name].search.windows.net/indexers/adlsgen2-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]
    
    {
      "dataSourceName" : " adlsgen2-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Indizieren von Inhalten und Metadaten

Data Lake Storage Gen2 Blobs enthalten Inhalte und Metadaten. Sie können mithilfe des Konfigurationsparameters `dataToExtract` steuern, welche Teile der Blobs indiziert werden. Die folgenden Werte sind möglich:

+ `contentAndMetadata` ‒ Gibt an, dass alle Metadaten und Textinhalte, die aus dem Blob extrahiert wurden, indiziert werden Dies ist der Standardwert.

+ `storageMetadata` ‒ Gibt an, dass nur die [standardmäßigen Blob-Eigenschaften und benutzerspezifischen Metadaten](../storage/blobs/storage-blob-container-properties-metadata.md) indiziert werden

+ `allMetadata`: gibt an, dass standardmäßige Blobeigenschaften und alle [Metadaten für gefundene Inhaltstypen](search-blob-metadata-properties.md) aus dem Blobinhalt extrahiert und indiziert werden.

Verwenden Sie z.B. Folgendes, um nur die Speichermetadaten zu indizieren:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

## <a name="indexing-blob-content"></a>Indizieren von Blobinhalten

Blobs mit strukturiertem Inhalt wie JSON oder CSV werden standardmäßig als einzelnes Textsegment indiziert. Wenn die JSON- oder CSV-Dokumente jedoch eine interne Struktur (Trennzeichen) haben, können Sie Analysemodi so zuweisen, dass für jede Zeile oder jedes Element einzelne Suchdokumente generiert werden. Weitere Informationen finden Sie unter [Indizieren von JSON-Blobs](search-howto-index-json-blobs.md) und [Indizieren von CSV-Blobs](search-howto-index-csv-blobs.md).

Ein Verbunddokument oder eingebettetes Dokument (z. B. ein ZIP-Archiv, ein Word-Dokument mit eingebetteter Outlook-E-Mail mit Anlagen oder eine MSG-Datei mit Anlagen) wird ebenfalls als einzelnes Dokument indiziert. So werden beispielsweise alle Bilder, die aus den Anlagen einer MSG-Datei extrahiert wurden, im Feld „normalized_images“ zurückgegeben.

Der Textinhalt des Dokuments wird in ein Zeichenfolgefeld mit dem Namen `content` extrahiert.

  > [!NOTE]
  > Azure Cognitive Search beschränkt die Menge des extrahierten Texts abhängig vom Tarif. Aktuell gelten diese [Grenzwerte](search-limits-quotas-capacity.md#indexer-limits): 32.000 Zeichen für den Free-Tarif, 64.000 Zeichen für den Tarif „Basic“, 4 Mio. Zeichen für den Tarif „Standard“, 8 Mio. Zeichen für den Tarif „Standard S2“ und 16 Mio. Zeichen für den Tarif „Standard S3“. Für gekürzte Dokumente wird eine Warnung in die Statusantwort des Indexers einbezogen.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indizieren der Metadaten von Blobs

Indexer können auch Blobmetadaten indizieren. Zunächst können beliebige vom Benutzer angegebene Metadateneigenschaften wortgetreu extrahiert werden. Um die Werte zu erhalten, müssen Sie im Suchindex ein Feld des Typs `Edm.String` definieren, das den gleichen Namen hat wie der Metadatenschlüssel des Blobs. Wenn ein Blob beispielsweise über den Metadatenschlüssel `Sensitivity` mit dem Wert `High` verfügt, müssen Sie ein Feld mit dem Namen `Sensitivity` in Ihrem Suchindex definieren, das dann mit dem Wert `High` aufgefüllt wird.

Zweitens können die standardmäßigen Metadateneigenschaften von Blobs in die nachstehend aufgeführten Felder extrahiert werden. Der Blobindexer erstellt automatisch interne Feldzuordnungen für diese Metadateneigenschaften von Blobs. Sie müssen die in der Indexdefinition gewünschten Felder nach wie vor hinzufügen, aber Sie können das Erstellen von Feldzuordnungen im Indexer weglassen.

  + **metadata_storage_name** (`Edm.String`): der Dateiname des Blobs. Für ein Blob mit dem Namen „/my-container/my-folder/subfolder/resume.pdf“ lautet der Wert dieses Felds beispielsweise `resume.pdf`.

  + **metadata_storage_path** (`Edm.String`): der vollständigen URI des Blobs, einschließlich Speicherkonto. Beispiel: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** (`Edm.String`): Inhaltstyp, der mit dem Code angegeben wird, den Sie zum Hochladen des Blobs verwendet haben. Beispiel: `application/octet-stream`.

  + **metadata_storage_last_modified** (`Edm.DateTimeOffset`): Zeitstempel der letzten Änderung des Blobs. In der kognitiven Azure-Suche wird dieser Zeitstempel zum Identifizieren geänderter Blobs verwendet, um die erneute Indizierung aller Elemente nach der ersten Indizierung zu vermeiden.

  + **metadata_storage_size** (`Edm.Int64`): Blobgröße in Bytes.

  + **metadata_storage_content_md5** (`Edm.String`): MD5-Hash des Blobinhalts, sofern vorhanden.

  + **metadata_storage_sas_token** (`Edm.String`): ein temporäres SAS Token, das von [benutzerdefinierten Qualifikationen](cognitive-search-custom-skill-interface.md) verwendet werden kann, um Zugriff auf den Blob zu erhalten. Dieses Token sollte nicht zur späteren Verwendung gespeichert werden, da es ablaufen kann.

Schließlich können auch alle Metadateneigenschaften, die für das Dokumentformat der zu indizierenden Blobs spezifisch sind, im Indexschema dargestellt werden. Weitere Informationen zu inhaltsspezifischen Metadaten finden Sie unter [Eigenschaften von Inhaltsmetadaten](search-blob-metadata-properties.md).

Wichtig ist der Hinweis, dass Sie nicht für alle der oben genannten Eigenschaften Felder in Ihrem Suchindex definieren müssen. Erfassen Sie lediglich die Eigenschaften, die Sie für Ihre Anwendung benötigen.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Steuern der zu indizierenden Blobs

Sie können steuern, welche Blobs indiziert und welche übersprungen werden, indem Sie Rollenzuweisungen, den Dateityp des Blobs oder Eigenschaften für den Blob selbst festlegen, sodass der Indexer sie überspringt.

### <a name="use-access-controls-and-role-assignments"></a>Verwenden von Zugriffssteuerungen und Rollenzuweisungen

Indexer, die auf einem System oder einer benutzerseitig zugewiesenen verwalteten Identität ausgeführt werden, können entweder einer Leser- oder Storage-Blobdatenleser-Rolle angehören, die Leseberechtigungen für bestimmte Dateien und Ordner gewährt.

### <a name="include-specific-file-extensions"></a>Einschließen bestimmter Dateierweiterungen

Verwenden Sie `indexedFileNameExtensions`, um eine durch Trennzeichen getrennte Liste der zu indizierenden Dateierweiterungen anzugeben (mit einem vorangestellten Punkt). Um beispielsweise nur die PDF- und DOCX-Blobs zu indizieren, gehen Sie folgendermaßen vor:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Ausschließen bestimmter Dateierweiterungen

Verwenden Sie `excludedFileNameExtensions`, um eine durch Trennzeichen getrennte Liste der zu überspringenden Dateierweiterungen anzugeben (wiederum mit einem vorangestellten Punkt). Um beispielsweise alle Blobs mit Ausnahme von Blobs mit den Erweiterungen PNG und JPEG zu indizieren, gehen Sie folgendermaßen vor:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Wenn die beiden Parameter `indexedFileNameExtensions` und `excludedFileNameExtensions` vorhanden sind, untersucht der Indexer zuerst `indexedFileNameExtensions` und danach `excludedFileNameExtensions`. Wenn die gleiche Dateierweiterung in beiden Listen vorhanden ist, wird sie von der Indizierung ausgeschlossen.

### <a name="add-skip-metadata-the-blob"></a>Hinzufügen von Metadaten zum Überspringen des Blobs

Die Konfigurationsparameter des Indexers gelten für alle Blobs im Container oder Ordner. Mitunter möchten Sie möglicherweise steuern, wie *einzelne Blobs* indiziert werden. Dazu können Sie die folgenden Metadateneigenschaften und -werte zu Blobs in Blog Storage hinzufügen. Wenn der Indexer auf diese Eigenschaften stößt, überspringt er den Blob oder dessen Inhalt im Indizierungslauf.

| Eigenschaftenname | Eigenschaftswert | Erklärung |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Weist den Blobindexer an, das Blob vollständig zu überspringen. Weder die Metadaten- noch die Inhaltsextraktion werden versucht. Dies ist nützlich, wenn ein bestimmtes Blob wiederholt fehlschlägt und den Indizierungsprozess unterbricht. |
| `AzureSearch_SkipContent` |`"true"` |Dies entspricht der [oben](#PartsOfBlobToIndex) beschriebenen Einstellung `"dataToExtract" : "allMetadata"` zu einem bestimmten Blob. |

## <a name="index-large-datasets"></a>Indizieren großer Datasets

Das Indizieren von Blobs kann sehr zeitaufwändig sein. In Fällen, in denen Millionen von Blobs indiziert werden müssen, können Sie die Indizierung durch Partitionieren der Daten und Verwenden mehrerer Indexer zur [parallelen](search-howto-large-index.md#parallel-indexing) Datenverarbeitung beschleunigen. Sie können es folgendermaßen einrichten:

1. Partitionieren Sie Ihre Daten in mehrere Blobcontainer oder virtuelle Ordner.

1. Richten Sie mehrere Datenquellen ein, eine pro Container oder Ordner. Verwenden Sie den Parameter `query`, um auf einen Blob-Ordner zu verweisen:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

1. Erstellen Sie einen entsprechenden Indexer für jede Datenquelle. Alle Indexer müssen auf den gleichen Zielsuchindex zeigen.  

Eine Sucheinheit in Ihrem Dienst kann einen Indexer zu jeder angegebenen Uhrzeit ausführen. Das Erstellen von mehreren Indexern – wie oben beschrieben – ist nur nützlich, wenn sie tatsächlich parallel ausgeführt werden.

Um mehrere Indexer parallel auszuführen, skalieren Sie Ihren Suchdienst auf, indem Sie eine bestimmte Anzahl von Partitionen und Replikate erstellen. Wenn Ihr Suchdienst beispielsweise über 6 Sucheinheiten verfügt (z.B. 2 Partitionen x 3 Replikate), dann können 6 Indexers simultan ausgeführt werden. Dies führt zu einem sechsfachen Anstieg des Indizierungsdurchsatzes. Weitere Informationen zu Skalierung und Kapazitätsplanung finden Sie unter [Anpassen der Kapazität eines Azure Cognitive Search-Diensts](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Behandlung von Fehlern

Zu häufigen Fehlern bei der Indizierung gehören nicht unterstützte Inhaltstypen, fehlende Inhalte oder übergroße Blobs.

Der Blobindexer wird standardmäßig beendet, sobald ein Blob mit einem nicht unterstützten Inhaltstyp (z.B. ein Bild) gefunden wird. Sie könnten mithilfe des Parameters `excludedFileNameExtensions` bestimmte Inhaltstypen überspringen. Möglicherweise möchten Sie jedoch, dass die Indizierung auch dann fortgesetzt wird, wenn Fehler auftreten, und dann später einzelne Dokumente debuggen. Weitere Informationen zu Indexerfehlern finden Sie unter [Beheben von häufigen Problemen bei Suchindexern in Azure Cognitive Search](search-indexer-troubleshooting.md) und [Beheben von häufigen Fehlern und Warnungen bei Suchindexern in Azure Cognitive Search](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Reagieren auf Fehler

Es gibt vier Indexereigenschaften, die die Reaktion des Indexers beim Auftreten von Fehlern steuern. Die folgenden Beispiele zeigen, wie Sie diese Eigenschaften in der Indexerdefinition festlegen. Wenn bereits ein Indexer vorhanden ist, können Sie diese Eigenschaften hinzufügen, indem Sie die Definition im Portal bearbeiten.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` und `"maxFailedItemsPerBatch"`

Setzen Sie die Indizierung fort, wenn an einem beliebigen Punkt der Verarbeitung Fehler auftreten, entweder bei der Analyse von Blobs oder beim Hinzufügen von Dokumenten zu einem Index. Legen Sie diese Eigenschaften auf die Anzahl zulässiger Fehler fest. Der Wert `-1` ermöglicht die Verarbeitung unabhängig davon, wie viele Fehler auftreten. Andernfalls ist der Wert eine positive ganze Zahl.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` und `"failOnUnprocessableDocument"` 

Für einige Blobs kann in Azure Cognitive Search der Inhaltstyp nicht bestimmt oder ein Dokument mit eigentlich unterstütztem Inhaltstyp nicht verarbeitet werden. Um diese Fehlerbedingungen zu ignorieren, legen Sie Konfigurationsparameter auf `false` fest:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Lockern der Einschränkungen für Indexer

Sie können auch [Blobkonfigurationseigenschaften](/rest/api/searchservice/create-indexer#blob-configuration-parameters) festlegen, die effektiv bestimmen, ob eine Fehlerbedingung vorliegt. Die folgende Eigenschaft kann Einschränkungen lockern und Fehler unterdrücken, die andernfalls auftreten würden.

+ `"indexStorageMetadataOnlyForOversizedDocuments"`, um Speichermetadaten für Blobinhalt, der zu groß zum Verarbeiten ist, trotzdem zu indizieren. Zu große Blobs werden standardmäßig als Fehler behandelt. Grenzwerte für die Blobgröße finden Sie unter [Dienstgrenzwerte](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Weitere Informationen

+ [Beispiel für C#: Index Data Lake Gen2 using Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md)
+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Erstellen eines Indexers](search-howto-create-indexers.md)
+ [Übersicht über die KI-Anreicherung über Blobs](search-blob-ai-integration.md)
+ [Durchsuchen von Azure Blob Storage-Inhalten](search-blob-storage-integration.md)