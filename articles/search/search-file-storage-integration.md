---
title: Indizieren von Daten aus Azure Files (Vorschau)
titleSuffix: Azure Cognitive Search
description: Richten Sie einen Azure Files-Indexer ein, um die Indizierung von Dateifreigaben in Azure Cognitive Search zu automatisieren.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 37f33bb17b48f482f27a449871acc7256713c100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157553"
---
# <a name="index-data-from-azure-files"></a>Indizieren von Daten aus Azure Files

> [!IMPORTANT] 
> Azure Files befindet sich derzeit in der öffentlichen Vorschau und unterliegt [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Verwenden Sie eine [Vorschau-REST-API (2021-04-30-preview oder höher)](search-api-preview.md), um Ihre Inhalte zu indizieren. Die Portalunterstützung ist momentan eingeschränkt, und das .NET SDK wird nicht unterstützt.

In diesem Artikel wird der grundlegende Workflow für die Extraktion von Inhalten und Metadaten aus Azure-Dateifreigaben sowie das Senden derselben an einen Suchindex in Azure Cognitive Search erläutert. Der resultierende Index kann über die Volltextsuche abgefragt werden.

> [!NOTE]
> Sie sind bereits mit dem Workflow und der Struktur vertraut? Dann ist das [Konfigurieren eines Dateiindexers](#configure) Ihr nächster Schritt.

## <a name="functionality"></a>Funktionalität

Ein Indexer in Azure Cognitive Search ist ein Crawler, der durchsuchbare Daten und Metadaten aus einer Datenquelle extrahiert. Der Azure Files-Indexer wird eine Verbindung mit Ihrer Azure-Dateifreigabe und den Indexdateien herstellen. Der Indexer bietet die folgenden Funktionen:

+ Indizieren von Inhalten aus einer Azure-Dateifreigabe.
+ Der Indexer unterstützt die inkrementelle Indizierung, d. h., er ermittelt die Inhalte in der Azure-Dateifreigabe, die geändert wurden, und indiziert bei zukünftigen Indizierungsläufen nur die aktualisierten Inhalte. Wenn beispielsweise ursprünglich fünf PDF-Dateien vom Indexer indiziert wurden, indiziert er bei der erneuten Ausführung nur die eine PDF-Datei, die aktualisiert wurde.
+ Text und normalisierte Bilder werden standardmäßig aus den indizierten Dateien extrahiert. Optional kann der Pipeline ein Skillset zur weiteren Inhaltsanreicherung hinzugefügt werden. Weitere Informationen zu Skillsets finden Sie im Artikel [Skillsetkonzepte in Azure Cognitive Search](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Unterstützte Dokumentformate

Der Azure Files-Indexer von Azure Cognitive Search kann Text aus den folgenden Dokumentformaten extrahieren:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="required-resources"></a>Erforderliche Ressourcen

Sie benötigen sowohl Azure Cognitive Search als auch [Azure Files](https://azure.microsoft.com/services/storage/files/). Innerhalb von Azure Files benötigen Sie eine Dateifreigabe, die den Quellinhalt bereitstellt.

> [!NOTE]
> Um eine Dateifreigabe zu indizieren, muss sie den Zugriff über die REST-API [der Dateidatenebene unterstützen](/rest/api/storageservices/file-service-rest-api). [NFS-Freigaben](../storage/files/files-nfs-protocol.md#support-for-azure-storage-features) unterstützen die REST-API der Dateidatenebene nicht und können nicht mit Azure Cognitive Search-Indexern verwendet werden. [SMB-Freigaben](../storage/files/files-smb-protocol.md) unterstützen die REST-API der Dateidatenebene und können mit Azure Cognitive Search-Indexern verwendet werden.

<a name="configure"></a>

## <a name="configuring-a-file-indexer"></a>Konfigurieren eines Dateiindexers

Azure-Dateiindexer verwenden viele Konfigurationsoptionen, die denen für [Azure-Blobindexer](search-howto-indexing-azure-blob-storage.md) ähneln. So unterstützen Azure File-Indexer beispielsweise das [Erstellen mehrerer Suchdokumente aus einer einzelnen Datei](search-howto-index-one-to-many-blobs.md), [Nur-Text-Dateien](search-howto-index-plaintext-blobs.md), [JSON-Dateien](search-howto-index-json-blobs.md) und [verschlüsselte Dateien](search-howto-index-encrypted-blobs.md). Außerdem gelten viele derselben [Konfigurationsoptionen](search-howto-indexing-azure-blob-storage.md). Wichtige Unterschiede sind unten hervorgehoben.

## <a name="data-source-definitions"></a>Datenquellendefinitionen

Der wichtigste Unterschied zwischen einem Dateiindexer und allen anderen Indexern besteht in der dem Indexer zugewiesenen Datenquellendefinition. Die Datenquellendefinition gibt den Datenquellentyp ("type": "azurefile") sowie andere Eigenschaften für die Authentifizierung und die Verbindung mit dem zu indizierenden Inhalt an.

Die Definition einer Dateidatenquelle sieht ähnlich wie im folgenden Beispiel aus:

```http
{
    "name" : "my-file-datasource",
    "type" : "azurefile",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-file", "query" : "<optional-directory-name>" }
}
```

Die Eigenschaft `"credentials"` kann eine Verbindungszeichenfolge sein, wie im obigen Beispiel gezeigt, oder einer der alternativen Ansätze, die im nächsten Abschnitt beschrieben werden. Die `"container"`-Eigenschaft gibt die Dateifreigabe in Azure Storage an, und mit `"query"` wird ein Unterordner in der Freigabe angegeben. Weitere Informationen zu Datenquellendefinitionen finden Sie unter [Erstellen einer Datenquelle (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Anmeldeinformationen

Sie können die Anmeldeinformationen für die Dateifreigabe mit einer der folgenden Methoden angeben:

**Verbindungszeichenfolge für verwaltete Identitäten**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Diese Verbindungszeichenfolge erfordert keinen Kontoschlüssel, Sie müssen jedoch die Anweisungen zum [Einrichten einer Verbindung mit einem Azure Storage-Konto mithilfe einer verwalteten Identität](search-howto-managed-identities-storage.md) befolgen.

**Verbindungszeichenfolge für den Vollzugriff auf ein Speicherkonto**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Sie können die Verbindungszeichenfolge über das Azure-Portal abrufen, indem Sie auf dem Blatt des Speicherkontos zu „Einstellungen“ > „Schlüssel“ (für klassische Speicherkonten) bzw. zu „Sicherheit + Netzwerkbetrieb“ > „Zugriffsschlüssel“ (für Azure Resource Manager-Speicherkonten) navigieren.

**SAS-Verbindungszeichenfolge (Shared Access Signature) für ein Speicherkonto**: `{ "connectionString" : "BlobEndpoint=https://<your account>.file.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&sp=rl&sr=s;" }`

Die SAS muss über Berechtigungen zum Auflisten und Lesen für die Dateifreigaben verfügen.

**Shared Access Signature des Containers**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.file.core.windows.net/<share name>?sv=2016-05-31&sr=s&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

Die SAS muss über Berechtigungen zum Auflisten und Lesen für die Dateifreigabe verfügen. Weitere Informationen zu Shared Access Signatures (SAS) finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Bei Verwendung von SAS-Anmeldeinformationen müssen Sie die Anmeldedaten für die Datenquellen in regelmäßigen Abständen mit erneuerten Signaturen aktualisieren, um den Ablauf zu verhindern. Wenn SAS-Anmeldeinformationen ablaufen, zeigt der Indexer eine Fehlermeldung wie „Die in der Verbindungszeichenfolge angegebenen Anmeldeinformationen sind ungültig oder abgelaufen“ an.

## <a name="indexing-file-metadata"></a>Indizieren von Dateimetadaten

Das Indizieren von benutzerdefinierten Metadaten sowie von Systemeigenschaften für jede Datei ist ein häufig auftretendes Szenario, in dem Sie Dateien jedes Inhaltstyps unkompliziert durchsehen können. Auf diese Weise werden die Informationen für alle Dateien unabhängig vom Dokumenttyp indiziert und in einem Index in Ihrem Suchdienst gespeichert. Mit dem neuen Index können Sie dann den gesamten Dateispeicherinhalt sortieren und filtern sowie Facetten bilden.

Sie können die standardmäßigen Metadateneigenschaften von Dateien in die nachstehend aufgeführten Felder extrahieren. Der Dateiindexer erstellt automatisch interne Feldzuordnungen für diese Metadateneigenschaften von Dateien. Sie müssen die in der Indexdefinition gewünschten Felder nach wie vor hinzufügen, aber Sie können das Erstellen von Feldzuordnungen im Indexer weglassen.

+ **metadata_storage_name** (`Edm.String`): der Dateiname. Für eine Datei mit dem Namen „/my-share/my-folder/subfolder/resume.pdf“ lautet der Wert dieses Felds beispielsweise `resume.pdf`.
+ **metadata_storage_path** (`Edm.String`): der vollständigen URI der Datei, einschließlich Speicherkonto. Zum Beispiel, `https://myaccount.file.core.windows.net/my-share/my-folder/subfolder/resume.pdf`
+ **metadata_storage_content_type** (`Edm.String`): Inhaltstyp, der mit dem Code angegeben wird, den Sie zum Hochladen der Datei verwendet haben. Beispiel: `application/octet-stream`.
+ **metadata_storage_last_modified** (`Edm.DateTimeOffset`): Zeitstempel der letzten Änderung der Datei. In Azure Cognitive Search wird dieser Zeitstempel zum Identifizieren geänderter Dateien verwendet, um die erneute Indizierung aller Elemente nach der ersten Indizierung zu vermeiden.
+ **metadata_storage_size** (`Edm.Int64`): Dateigröße in Bytes.
+ **metadata_storage_content_md5** (`Edm.String`): MD5-Hash des Dateiinhalts, sofern vorhanden.
+ **metadata_storage_sas_token** (`Edm.String`): ein temporäres SAS-Token, das von [benutzerdefinierten Skills](cognitive-search-custom-skill-interface.md) verwendet werden kann, um Zugriff auf die Datei zu erhalten. Dieses Token sollte nicht zur späteren Verwendung gespeichert werden, da es ablaufen kann.

## <a name="index-by-file-type"></a>Indizieren nach Dateityp

Sie können steuern, welche Dokumente indiziert und welche übersprungen werden.

### <a name="include-documents-having-specific-file-extensions"></a>Einschließen von Dokumenten mit bestimmten Dateierweiterungen

Sie können nur die Dokumente mit den Dateinamenerweiterungen indizieren, die Sie mit dem Konfigurationsparameter `indexedFileNameExtensions` des Indexers angeben. Der Wert ist eine Zeichenfolge mit einer durch Trennzeichen getrennte Liste von Dateierweiterungen (mit einem vorangestellten Punkt). So indizieren Sie beispielsweise nur die PDF- und DOCX-Dokumente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Ausschließen von Dokumenten mit bestimmten Dateierweiterungen

Mit dem Konfigurationsparameter `excludedFileNameExtensions` können Sie Dokumente mit bestimmten Dateinamenerweiterungen von der Indizierung ausschließen. Der Wert ist eine Zeichenfolge mit einer durch Trennzeichen getrennte Liste von Dateierweiterungen (mit einem vorangestellten Punkt). So indizieren Sie beispielsweise alle Dokumente mit Ausnahme von Dokumenten mit den Erweiterungen PNG und JPEG:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Wenn sowohl der `indexedFileNameExtensions`- als auch der `excludedFileNameExtensions`-Parameter vorhanden ist, wird in der kognitiven Azure-Suche zunächst `indexedFileNameExtensions` und danach `excludedFileNameExtensions` überprüft. Das heißt, wenn die gleiche Dateierweiterung in beiden Listen vorhanden ist, wird sie von der Indizierung ausgeschlossen.

<a name="deleted-files"></a>

## <a name="detecting-deleted-files"></a>Erkennen gelöschter Dateien

Nachdem ein erster Suchindex erstellt wurde, sollen nachfolgende Indexeraufräge vielleicht nur neue und geänderte Dokumente aufnehmen. Wenn Sie einen Zeitplan für die Triggerindizierung verwenden, wird die Änderungserkennung bei Suchinhalten aus Azure File Storage automatisch durchgeführt. Standardmäßig werden wie vom `LastModified`-Zeitstempel der Datei angegeben nur die geänderten Dateien vom Dienst neu indiziert. Im Gegensatz zu anderen von Suchindexern unterstützten Datenquellen verfügen Dateien immer über einen Zeitstempel, wodurch die Notwendigkeit entfällt, eine Richtlinie für die Änderungserkennung manuell einzurichten.

Die Änderungserkennung ist eine Standardeinstellung, die Erkennung von Löschungen hingegen nicht. Wenn Sie gelöschte Dateien erkennen möchten, stellen Sie sicher, dass Sie einen Ansatz für „vorläufiges Löschen“ verwenden. Wenn Sie die Dateien direkt löschen, werden die entsprechenden Dokumente nicht aus dem Suchindex entfernt.

## <a name="soft-delete-using-custom-metadata"></a>Vorläufiges Löschen mithilfe benutzerdefinierter Metadaten

Diese Methode verwendet die Metadaten einer Datei, um zu bestimmen, ob ein Suchdokument aus dem Index entfernt werden soll. Diese Methode erfordert zwei separate Aktionen: Zunächst wird das Suchdokument aus dem Index und dann die Datei in Azure Storage gelöscht.

Sowohl bei der File Storage als auch bei Cognitive Search gibt es Schritte, die zu befolgen sind, aber es bestehen keine weiteren Featureabhängigkeiten. Diese Funktion wird in allgemein verfügbaren APIs unterstützt.

1. Fügen Sie der Datei ein Schlüssel-Wert-Paar für die Metadaten hinzu, um in Azure Cognitive Search anzugeben, dass die Datei logisch gelöscht ist.

1. Konfigurieren Sie für die Datenquelle eine Richtlinie zur Erkennung von Spalten für vorläufiges Löschen. Bei der folgenden Richtlinie wird eine Datei beispielsweise als gelöscht angesehen, wenn ihre Metadateneigenschaft `IsDeleted` den Wert `true` aufweist:

    ```http
    PUT https://[service name].search.windows.net/datasources/file-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "file-datasource",
        "type" : "azurefile",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-share", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Nachdem der Indexer das Blob verarbeitet und das Dokument aus dem Index gelöscht hat, können Sie das Blob in Azure Blob Storage löschen.

### <a name="reindexing-undeleted-files-using-custom-metadata"></a>Erneutes Indizieren wiederhergestellter Dateien (mithilfe von benutzerdefinierten Metadaten)

Nachdem ein Indexer eine gelöschte Datei verarbeitet und das entsprechende Suchdokument aus dem Index gelöscht hat, wird die Datei nicht erneut überprüft, wenn Sie sie später wiederherstellen und der `LastModified`-Zeitstempel der Datei älter als die letzte Indexerausführung ist.

Wenn Sie dieses Dokument erneut indizieren möchten, ändern Sie `"softDeleteMarkerValue" : "false"` für die Datei, und führen Sie den Indexer erneut aus.

## <a name="see-also"></a>Weitere Informationen

+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Was ist Azure Files?](../storage/files/storage-files-introduction.md)
