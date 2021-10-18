---
title: Konfigurieren von Cache und inkrementeller Anreicherung (Vorschau)
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie das Zwischenspeichern, und bewahren Sie den Status von angereicherten Inhalten für die kontrollierte Verarbeitung in einem kognitiven Skillset. Dieses Feature ist zurzeit als öffentliche Preview verfügbar.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 5ea2c908cce37e19023e27b0e3e4cc76f778b7f0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620163"
---
# <a name="configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung in Azure Cognitive Search

> [!IMPORTANT] 
> Dieses Feature befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

In diesem Artikel wird erklärt, wie Sie eine Zwischenspeicherung zu einer Anreicherungspipeline hinzufügen, sodass Sie Schritte inkrementell ändern können, ohne jedes Mal eine Neuerstellung durchführen zu müssen. Standardmäßig ist ein Skillset zustandslos, und eine Änderung eines beliebigen Teils seiner Komposition erfordert eine vollständige erneute Ausführung des Indexers. Mit der inkrementellen Anreicherung kann der Indexer ermitteln, welche Teile der Dokumentstruktur auf der Grundlage der in den Skillset- oder Indexerdefinitionen erkannten Änderungen aktualisiert werden müssen. Die vorhandene verarbeitete Ausgabe wird nach Möglichkeit beibehalten und wiederverwendet. 

Zwischengespeicherte Inhalte werden mithilfe der von Ihnen bereitgestellten Kontoinformationen in Azure Storage platziert. Der Container namens `ms-az-search-indexercache-<alpha-numerc-string>` wird erstellt, wenn Sie den Indexer ausführen. Er sollte als eine interne Komponente angesehen werden, die von Ihrem Suchdienst verwaltet wird und nicht geändert werden darf.

Wenn Sie mit dem Einrichten von Indexern nicht vertraut sind, beginnen Sie mit [Übersicht über Indexer](search-indexer-overview.md), und fahren Sie dann mit [Skillsets](cognitive-search-working-with-skillsets.md) fort, um sich über Anreicherungspipelines zu informieren. Weitere Hintergrundinformationen zu Schlüsselkonzepten finden Sie unter [Inkrementelle Anreicherung](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Aktivieren der Zwischenspeicherung für einen vorhandenen Indexer

Wenn Sie über einen Indexer mit einem bereits vorhandenen Skillset verfügen, führen Sie die Schritte in diesem Abschnitt aus, um die Zwischenspeicherung hinzuzufügen. Sie müssen den Indexer einmalig vollständig zurücksetzen und erneut ausführen, bevor die inkrementelle Verarbeitung wirksam werden kann.

### <a name="step-1-get-the-indexer-definition"></a>Schritt 1: Abrufen der Indexerdefinition

Beginnen Sie mit einem gültigen, vorhandenen Indexer, der über diese Komponenten verfügt: Datenquelle, Skillset, Index. Der Indexer sollte ausführbar sein.

Erstellen Sie mithilfe eines API-Clients eine [GET Indexer-Anforderung](/rest/api/searchservice/get-indexer), um die aktuelle Konfiguration des Indexers abzurufen. Wenn Sie die Vorschauversion der API verwenden, um den Indexer zu erhalten, wird den Definitionen eine `cache`-Eigenschaft hinzugefügt, die auf NULL festgelegt ist.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Kopieren Sie die Indexerdefinition aus der Antwort.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Schritt 2: Ändern der Cacheeigenschaft in der Indexerdefinition

Der Standardwert der `cache`-Eigenschaft ist NULL. Verwenden Sie einen API-Client, um die Cachekonfiguration festzulegen (das Portal unterstützt dieses partikuläre Update nicht). 

Ändern Sie das Cacheobjekt so, dass es die folgenden erforderlichen und optionalen Eigenschaften umfasst: 

+ `storageConnectionString` ist erforderlich und muss auf eine Azure Storage-Verbindungszeichenfolge festgelegt werden. 
+ Die boolesche Eigenschaft `enableReprocessing` ist optional (standardmäßig `true`) und gibt an, dass die inkrementelle Anreicherung aktiviert ist. Sie können bei Bedarf `false` festlegen, um die inkrementelle Verarbeitung anzuhalten, während andere ressourcenintensive Vorgänge, wie z. B. das Indizieren neuer Dokumente, ausgeführt werden, und sie später wieder auf `true` zurückzusetzen.

```json
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

> [!NOTE]
> Der Indexercache erfordert ein Speicherkonto vom Typ „Universell v2“. Weitere Informationen finden Sie im Thema zu den [verschiedenen Typen von Speicherkonten](../storage/common/storage-account-overview.md#types-of-storage-accounts).

### <a name="step-3-reset-the-indexer"></a>Schritt 3: Zurücksetzen des Indexers

Ein Zurücksetzen des Indexers ist beim Einrichten der inkrementellen Anreicherung für vorhandene Indexer erforderlich, um sicherzustellen, dass sich alle Dokumente in einem konsistenten Zustand befinden. Sie können das Portal oder einen API-Client und die [REST-API „Indexer zurücksetzen“](/rest/api/searchservice/reset-indexer) für diese Aufgabe verwenden.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Schritt 4: Speichern der aktualisierten Definition

[Aktualisieren Sie den Indexer](/rest/api/searchservice/preview-api/update-indexer) mit einer PUT-Anforderung. Der Anforderungstext sollte die aktualisierte Indexerdefinition mit der Cacheeigenschaft enthalten. Wenn der Fehler 400 angezeigt wird, überprüfen Sie die Indexerdefinition, um sicherzustellen, dass alle Anforderungen erfüllt sind (Datenquelle, Skillset, Index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
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

Nachdem der Indexer ausgeführt wurde, finden Sie den Cache in Azure Blob Storage. Containernamen weisen folgendes Format auf: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Ein Zurücksetzen und erneutes Ausführen des Indexers führt zu einer vollständigen Neuerstellung, sodass der Inhalt zwischengespeichert werden kann. Alle kognitiven Anreicherungen werden für alle Dokumente erneut ausgeführt.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Schritt 6: Ändern eines Skillsets und Bestätigen der inkrementellen Anreicherung

Zum Ändern eines Skillsets können das Portal oder die API verwenden. Wenn Sie z. B. die Textübersetzung verwenden, reicht eine einfache Inlineänderung von `en` in `es` oder eine andere Sprache als Proof of Concept-Test für die inkrementelle Anreicherung aus.

Führen Sie den Indexer erneut aus. Es werden nur die Teile einer angereicherten Dokumentstruktur aktualisiert. Wenn Sie den [Portal-Schnellstart](cognitive-search-quickstart-blob.md) als Proof of Concept verwendet haben und die Qualifikation „Textübersetzung“ in „Ja“ geändert haben, werden Sie feststellen, dass statt der ursprünglichen 14 nur 8 Dokumente aktualisiert werden. Bilddateien, die vom Übersetzungsprozess nicht betroffen sind, werden aus dem Zwischenspeicher wieder verwendet.

## <a name="enable-caching-on-new-indexers"></a>Aktivieren der Zwischenspeicherung für neue Indexer

Um die inkrementelle Anreicherung für einen neuen Indexer einzurichten, müssen Sie lediglich die `cache`-Eigenschaft in die Nutzlast der Indexerdefinition einschließen, wenn Sie [Create Indexer (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) aufrufen. 


```json
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

## <a name="checking-for-cached-output"></a>Überprüfen auf eine zwischengespeicherte Ausgabe

Der Zwischenspeicher wird vom Indexer erstellt, verwendet und verwaltet, und sein Inhalt wird nicht in einem für den Benutzer lesbaren Format dargestellt. Die beste Möglichkeit, um zu bestimmen, ob der Zwischenspeicher verwendet wird, besteht darin, den Indexer auszuführen und die Vorher-Nachher-Metriken für Ausführungszeit und Dokumentanzahl zu vergleichen. 

Angenommen, Sie verfügen über ein Skillset, das mit der Bildanalyse und der optischen Zeichenerkennung (OCR) der gescannten Dokumente beginnt, gefolgt von einer Downstreamanalyse des resultierenden Texts. Wenn Sie eine Downstreamtextqualifikation ändern, kann der Indexer alle zuvor verarbeiteten Bild- und OCR-Inhalte aus dem Zwischenspeicher abrufen und nur die durch Ihre Änderungen angegebenen textbezogene Änderungen aktualisieren und verarbeiten. Es ist zu erwarten, dass als Dokumentanzahl weniger Dokumente (jetzt z. B. 8/8 statt 14/14 bei der ursprünglichen Ausführung), kürzere Ausführungszeiten und weniger Gebühren auf der Rechnung angezeigt werden.

## <a name="working-with-the-cache"></a>Arbeiten mit dem Zwischenspeicher

Sobald der Zwischenspeicher betriebsbereit ist, wird er beim Aufrufen von [Run Indexer](/rest/api/searchservice/run-indexer) durch den Indexer überprüft, um zu ermitteln, welche Teile der vorhandenen Ausgabe verwendet werden können. 

In der folgenden Tabelle wird zusammengefasst, wie sich verschiedene APIs auf den Zwischenspeicher beziehen:

| API           | Auswirkung auf den Zwischenspeicher     |
|---------------|------------------|
| [Create Indexer (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) | Erstellt bei der ersten Verwendung einen Indexer und führt ihn aus. Sofern in der Indexerdefinition angegeben, wird auch ein Zwischenspeicher erstellt. |
| [Run Indexer](/rest/api/searchservice/run-indexer) | Führt bei Bedarf eine Anreicherungspipeline aus. Diese API liest aus dem Zwischenspeicher, sofern vorhanden, oder erstellt einen Zwischenspeicher, wenn Sie das Zwischenspeichern zu einer aktualisierten Indexerdefinition hinzugefügt haben. Wenn Sie einen Indexer ausführen, für den das Zwischenspeichern aktiviert ist, lässt der Indexer Schritte aus, wenn zwischengespeicherte Ausgaben verwendet werden können. Sie können die allgemein verfügbare API-Version oder die Vorschauversion dieser API verwenden.|
| [Reset Indexer](/rest/api/searchservice/reset-indexer)| Bereinigt den Indexer von inkrementellen Indizierungsinformationen. Die nächste Indexerausführung (entweder bedarfsgesteuert oder geplant) ist eine vollständige Neuverarbeitung von Grund auf, einschließlich der erneuten Ausführung aller Qualifikationen und der Neuerstellung des Zwischenspeichers. Dies entspricht funktionell dem Löschen und Neuerstellen des Indexers. Sie können die allgemein verfügbare API-Version oder die Vorschauversion dieser API verwenden.|
| [Reset Skills](/rest/api/searchservice/preview-api/reset-skills) | Gibt an, welche Qualifikationen bei der nächsten Indexerausführung erneut ausgeführt werden sollen, auch wenn keine Qualifikationen geändert wurden. Der Zwischenspeicher wird entsprechend aktualisiert. Ausgaben, wie z. B. ein Wissensspeicher oder ein Suchindex, werden mit wiederverwendbaren Daten aus dem Zwischenspeicher und neuen Inhalten gemäß der aktualisierten Qualifikation aktualisiert. |

Weitere Informationen zum Steuern, was mit dem Zwischenspeicher passiert, finden Sie unter [Cacheverwaltung](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Nächste Schritte

Die inkrementelle Anreicherung gilt für Indexer, die Skillsets enthalten. Im nächsten Schritt schauen Sie sich Informationen zu Konzepten und Komposition in der Dokumentation zu Skillsets an. 

Nachdem Sie den Zwischenspeicher aktiviert haben, möchten Sie außerdem vielleicht mehr über die Parameter und APIs erfahren, die sich auf die Zwischenspeicherung auswirken, beispielsweise, wie bestimmtes Verhalten außer Kraft gesetzt oder erzwungen wird. Weitere Informationen finden Sie unter den folgenden Links.

+ [Skillsets: Konzepte und Komposition](cognitive-search-working-with-skillsets.md)
+ [Erstellen eines Skillsets](cognitive-search-defining-skillset.md)
+ [Einführung in die inkrementelle Anreicherung und das Zwischenspeichern](cognitive-search-incremental-indexing-conceptual.md)