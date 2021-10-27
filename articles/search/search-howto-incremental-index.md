---
title: Aktivieren der Zwischenspeicherung für die inkrementelle Anreicherung (Vorschau)
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie das Zwischenspeichern angereicherter Inhalte für eine potenzielle Wiederverwendung, wenn Sie Downstreamskills und -projektionen in einer KI-Anreicherungspipeline ändern.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: b905517a2558eb6bc01b4ba218f0ecde7e830ebf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138588"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Aktivieren der Zwischenspeicherung für die inkrementelle Anreicherung in Azure Cognitive Search

> [!IMPORTANT] 
> Dieses Feature befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

In diesem Artikel wird erklärt, wie Sie eine Zwischenspeicherung zu einer Anreicherungspipeline hinzufügen, sodass Sie Schritte zur Downstreamanreicherung ändern können, ohne jedes Mal eine komplette Neuerstellung durchführen zu müssen. Standardmäßig ist ein Skillset zustandslos, und eine Änderung eines beliebigen Teils seiner Komposition erfordert eine vollständige erneute Ausführung des Indexers. Mit der [**inkrementellen Anreicherung**](cognitive-search-incremental-indexing-conceptual.md) kann der Indexer ermitteln, welche Teile der Dokumentstruktur auf der Grundlage der in den Skillset- oder Indexerdefinitionen erkannten Änderungen aktualisiert werden müssen. Die vorhandene verarbeitete Ausgabe wird nach Möglichkeit beibehalten und wiederverwendet. 

Zwischengespeicherte Inhalte werden mithilfe der von Ihnen bereitgestellten Kontoinformationen in Azure Storage platziert. Der Container namens `ms-az-search-indexercache-<alpha-numerc-string>` wird erstellt, wenn Sie den Indexer ausführen. Er sollte als eine interne Komponente angesehen werden, die von Ihrem Suchdienst verwaltet wird und nicht geändert werden darf.

Wenn Sie mit dem Einrichten von Indexern nicht vertraut sind, beginnen Sie mit [Übersicht über Indexer](search-indexer-overview.md), und fahren Sie dann mit [Skillsets](cognitive-search-working-with-skillsets.md) fort, um sich über Anreicherungspipelines zu informieren. Weitere Hintergrundinformationen zu Schlüsselkonzepten finden Sie unter [Inkrementelle Anreicherung](cognitive-search-incremental-indexing-conceptual.md).

## <a name="prerequisites"></a>Voraussetzungen

Azure Storage wird verwendet, um zwischengespeicherte Anreicherungen zu speichern. Das Speicherkonto muss vom Typ [Universell v2](../storage/common/storage-account-overview.md#types-of-storage-accounts) sein.

Zum Aktivieren des Caches für einen Indexer benötigen Sie Vorschau-APIs oder Azure SDKs der Betaversion. Das Portal bietet derzeit keine Option zum Zwischenspeichern der Anreicherung.

## <a name="enable-on-new-indexers"></a>Für neue Indexer aktivieren

Fügen Sie bei neuen Indexern die Eigenschaft „cache“ in der Nutzlast der Indexerdefinition hinzu, wenn Sie [Indexer erstellen oder aktualisieren (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) aufrufen. Sie können auch die vorherige Vorschauversion der API (2020-06-30-Preview) verwenden.

```https
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
        }
    }
```

## <a name="enable-on-existing-indexers"></a>Für vorhandene Indexer aktivieren

Verwenden Sie für vorhandene Indexer, die bereits über ein Skillset verfügen, die folgenden Schritte, um die Zwischenspeicherung hinzuzufügen. Setzen Sie den Indexer einmalig vollständig zurück, und führen Sie den Indexer erneut aus, um den Cache zu laden.

### <a name="step-1-get-the-indexer-definition"></a>Schritt 1: Abrufen der Indexerdefinition

Beginnen Sie mit einem gültigen, Arbeitsindexer, der über diese Komponenten verfügt: Datenquelle, Skillset, Index. Senden Sie mit einem API-Client eine [GET Indexer](/rest/api/searchservice/get-indexer)-Anforderung, um den Indexer abzurufen. Wenn Sie die Vorschauversion der API verwenden, um den Indexer zu erhalten, wird den Definitionen automatisch eine „cache“-Eigenschaft hinzugefügt, die auf NULL festgelegt ist.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>Schritt 2: Festlegen der Cacheeigenschaft

Ändern Sie in der Indexdefinition „cache“, um die folgenden erforderlichen und optionalen Eigenschaften einzuschließen:

+ (Erforderlich) `storageConnectionString` muss auf eine Azure Storage-Verbindungszeichenfolge festgelegt werden.
+ (Optional) Die boolesche Eigenschaft `enableReprocessing` (standardmäßig `true`) ist optional und gibt an, dass die inkrementelle Anreicherung aktiviert ist. Legen Sie die Eigenschaft auf `false` fest, wenn Sie die inkrementelle Verarbeitung anhalten möchten, während andere ressourcenintensive Vorgänge, wie z. B. das Indizieren neuer Dokumente, ausgeführt werden, und setzen Sie die später wieder auf `true` zurück.

```http
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

### <a name="step-3-reset-the-indexer"></a>Schritt 3: Zurücksetzen des Indexers

[Indexer zurücksetzen](/rest/api/searchservice/reset-indexer) ist beim Einrichten der inkrementellen Anreicherung für vorhandene Indexer erforderlich, um sicherzustellen, dass sich alle Dokumente in einem konsistenten Zustand befinden. Sie können das Portal oder einen API-Client für diese Aufgabe verwenden.

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>Schritt 4: Speichern des Indexers

[Aktualisieren Sie den Indexer (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) mit einer PUT-Anforderung, wobei der Text der Anforderung „cache“ enthält.

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
    {
        "name" : "<YOUR-INDEXER-NAME>",
        ...
        "cache": {
            "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        }
    }
```

Wenn Sie jetzt eine weitere GET-Anforderung für den Indexer ausgeben, enthält die Antwort vom Dienst eine Eigenschaft `ID` im Cacheobjekt. Die alphanumerische Zeichenfolge wird an den Namen des Containers angehängt, der alle zwischengespeicherten Ergebnisse und den Zwischenzustand der einzelnen Dokumente enthält, die von diesem Indexer verarbeitet werden. Die ID wird verwendet, um den Cache in Blobspeicher eindeutig zu benennen.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Schritt 5: Ausführen des Indexers

Sie können den Indexer über das Portal oder die API ausführen. Wählen Sie im Portal den Indexer aus der Liste der Indexer aus, und klicken Sie auf **Ausführen**. Ein Vorteil bei der Verwendung des Portals besteht darin, dass Sie den Indexerstatus überwachen, die Dauer des Auftrags notieren und die Anzahl der verarbeiteten Dokumente ermitteln können. Portalseiten werden alle paar Minuten aktualisiert.

Alternativ können Sie REST verwenden, um den [Indexer auszuführen](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

> [!NOTE]
> Ein Zurücksetzen und erneutes Ausführen des Indexers führt zu einer vollständigen Neuerstellung, sodass der Inhalt zwischengespeichert werden kann. Alle kognitiven Anreicherungen werden für alle Dokumente erneut ausgeführt. Die Wiederverwendung angereicherter Inhalte aus dem Cache beginnt nach dem Laden des Caches.
>

## <a name="check-for-cached-output"></a>Überprüfen auf eine zwischengespeicherte Ausgabe

Suchen Sie den Cache in Azure Storage unter Blobcontainer. Der Containername lautet `ms-az-search-indexercache-<some-alphanumeric-string>`.

Ein Cache wird erstellt und von einem Indexer verwendet. Sein Inhalt ist nicht für Menschen lesbar.

Um zu überprüfen, ob der Cache betriebsbereit ist, ändern Sie ein Skillset, führen Sie den Indexer aus, und vergleichen Sie dann die Vorher-/Nachher-Metriken auf Ausführungszeit und Dokumentanzahl. 

Skillsets, die Bildanalyse und optische Zeichenerkennung (Optical Character Recognition, OCR) gescannter Dokumente enthalten, sind gute Testfälle. Wenn Sie einen Downstreamtext-Skill oder einen beliebigen nicht bildbezogenen Skill ändern, kann der Indexer alle zuvor verarbeiteten Bild- und OCR-Inhalte aus dem Cache abrufen wobei nur die durch Ihre Änderungen angegebenen textbezogene Änderungen aktualisiert und verarbeitet werden.  Es ist zu erwarten, dass als Dokumentanzahl der Indexerausführung weniger Dokumente, kürzere Ausführungszeiten und weniger Gebühren auf der Rechnung angezeigt werden. 

Der in den [cog-search-demo-Tutorials](cognitive-search-tutorial-blob.md) verwendete [Dateisatz](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) ist ein nützlicher Testfall, da er 14 Dateien in verschiedenen Formaten (JPG, PNG, HTML, DOCX, PPTX und anderen Typen) enthält. Ändern Sie `en` in `es` oder eine andere Sprache im Skill „Textübersetzung“, um Proof-of-Concept-Tests der inkrementellen Anreicherung durchzuführen.

## <a name="common-errors"></a>Häufige Fehler

Der folgende Fehler tritt auf, wenn Sie vergessen, in der Anforderung eine Vorschauversion der API anzugeben:

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

Ein „400 Ungültige Anforderung“-Fehler tritt auch auf, wenn eine Indexeranforderung fehlt. In der Fehlermeldung werden alle fehlenden Abhängigkeiten angegeben.

## <a name="next-steps"></a>Nächste Schritte

Die inkrementelle Anreicherung gilt für Indexer, die Skillsets enthalten, und stellt wiederverwendbare Inhalte sowohl für Indizes als auch für Wissensspeicher bereit. Unter den folgenden Links finden Sie weitere Informationen zur Zwischenspeicherung und zu Skillsets.

+ [Inkrementelle Anreicherung (Lebenszyklus und Verwaltung)](cognitive-search-incremental-indexing-conceptual.md)
+ [Skillsets: Konzepte und Komposition](cognitive-search-working-with-skillsets.md)
+ [Erstellen eines Skillsets](cognitive-search-defining-skillset.md)
+ [Tutorial: Verwenden von REST und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob.md)