---
title: Konzepte zur inkrementellen Anreicherung (Vorschau)
titleSuffix: Azure Cognitive Search
description: Sie können Zwischeninhalte und inkrementelle Änderungen aus der KI-Anreicherungspipeline in Azure Storage zwischenspeichern, um Investitionen in vorhandene verarbeitete Dokumente zu erhalten. Dieses Feature ist zurzeit als öffentliche Preview verfügbar.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 7b3d0fc85afbff58641edea332576f921c96b672
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727784"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Inkrementelle Anreicherung und Zwischenspeicherung in Azure Cognitive Search

> [!IMPORTANT] 
> Dieses Feature befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

*Inkrementelle Anreicherung* ist ein Features, das sich auf [Skillsets](cognitive-search-working-with-skillsets.md) richtet. Es nutzt Azure Storage, um die von einer Anreicherungspipeline ausgegebene Prozessausgabe für die Wiederverwendung in künftigen Indexerläufen zu speichern. Nach Möglichkeit verwendet der Indexer jede zwischengespeicherte Ausgabe, die noch gültig ist. 

Die inkrementelle Anreicherung sorgt nicht nur dafür, dass ihre monetären Investitionen in die Verarbeitung (insbesondere OCR und Bildverarbeitung) geschützt werden, sondern schafft auch ein effizienteres System. 

Ein Workflow, der inkrementelle Anreicherung verwendet, besteht aus den folgenden Schritten:

1. [Erstellen oder Bestimmen eines Azure-Speicherkontos](../storage/common/storage-account-create.md) zum Aufnehmen des Zwischenspeichers.
1. [Aktivieren von inkrementeller Anreicherung](search-howto-incremental-index.md) im Indexer.
1. [Erstellen eines Indexers](/rest/api/searchservice/create-indexer) – sowie eines [Skillsets](/rest/api/searchservice/create-skillset) – zum Aufrufen der Pipeline. Während der Verarbeitung werden für jedes Dokument Phasen der Anreicherung für jedes Dokument zur zukünftigen Verwendung in Blob-Speicher gespeichert.
1. Testen Sie Ihren Code, und verwenden Sie nach ggf. erforderlichen Änderungen [Skillset aktualisieren](/rest/api/searchservice/update-skillset), um eine Definition zu ändern.
1. [Führen Sie den Indexer aus](/rest/api/searchservice/run-indexer), um die Pipeline aufzurufen, wobei zwischengespeicherte Ausgaben für eine schnellere und kostengünstigere Verarbeitung abgerufen werden.

Weitere Informationen zu Schritten und Überlegungen beim Arbeiten mit einem vorhandenen Indexer finden Sie unter [Einrichten der inkrementellen Anreicherung](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Indexercache

Die inkrementelle Anreicherung ergänzt die Anreicherungspipeline um einen Zwischenspeicher. Der Indexer speichert die Ergebnisse der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking) sowie die Ausgaben der einzelnen Qualifikationen für jedes Dokument zwischen. Wenn ein Skillset aktualisiert wird, werden lediglich die geänderten Qualifikationen (oder die Downstreamqualifikationen) erneut ausgeführt. Die aktualisierten Ergebnisse werden in den Zwischenspeicher geschrieben, und das Dokument wird im Suchindex oder im Wissensspeicher aktualisiert.

Der Zwischenspeicher wird physisch in einem Blobcontainer in Ihrem Azure Storage-Konto gespeichert. Der Cache verwendet auch Table Storage für die interne Aufzeichnung der Verarbeitung von Updates. Alle Indizes in einem Suchdienst können für den Indexercache das gleiche Speicherkonto verwenden. Jedem Indexer wird ein eindeutiger und unveränderlicher Cachebezeichner zum Container zugewiesen, der ihn verwendet.

> [!NOTE]
> Der Indexercache erfordert ein Speicherkonto vom Typ „Universell“. Weitere Informationen finden Sie im Thema zu den [verschiedenen Typen von Speicherkonten](../storage/common/storage-account-overview.md#types-of-storage-accounts).

## <a name="cache-configuration"></a>Cachekonfiguration

Um die inkrementelle Anreicherung nutzen zu können, muss die `cache`-Eigenschaft für den Indexer festgelegt werden. Das folgende Beispiel veranschaulicht einen Indexer, bei dem die Zwischenspeicherung aktiviert ist. Bestimmte Teile dieser Konfiguration werden in den folgenden Abschnitten beschrieben. Weitere Informationen finden Sie unter [Einrichten der inkrementellen Anreicherung](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Wenn Sie diese Eigenschaft für einen vorhandenen Indexer festlegen, müssen Sie sie zurücksetzen und den Indexer erneut ausführen, was dazu führt, dass alle Dokumente in der Datenquelle erneut verarbeitet werden. Dieser Schritt ist erforderlich, um alle Dokumente zu eliminieren, die durch frühere Versionen des Skillsets angereichert wurden. 

## <a name="cache-management"></a>Cacheverwaltung

Der Lebenszyklus des Caches wird vom Indexer verwaltet. Wird die `cache`-Eigenschaft im Indexer auf „Null“ festgelegt oder die Verbindungszeichenfolge geändert, wird der vorhandene Cache gelöscht. Der Cachelebenszyklus ist auch an den Lebenszyklus des Indexers gebunden. Wird ein Indexer gelöscht, wird auch der zugehörige Cache gelöscht.

Die inkrementelle Anreicherung ist zwar so konzipiert, dass Änderungen erkannt werden und darauf reagiert wird, ohne dass Sie selbst eingreifen müssen. Es gibt aber Parameter, die Sie verwenden können, um Standardverhalten zu überschreiben:

+ Priorisieren neuer Dokumente
+ Umgeben von Skillsetüberprüfungen
+ Umgeben von Datenquellenüberprüfungen
+ Erzwingen der Skillsetauswertung

### <a name="prioritize-new-documents"></a>Priorisieren neuer Dokumente

Legen Sie die Eigenschaft `enableReprocessing` fest, um die Verarbeitung eingehender Dokumente, die bereits im Cache vorhanden sind, zu steuern. Ist diese Option auf `true` festgelegt (Standard), werden Dokumente, die bereits im Cache vorhanden sind, bei der erneuten Ausführung des Indexers erneut verarbeitet, vorausgesetzt, dass sich Ihre Qualifikationsaktualisierung auf dieses Dokument auswirkt. 

Ist diese Option auf `false` festgelegt, werden bestehende Dokumente nicht erneut verarbeitet, wodurch neuen, eingehenden Inhalten effektiv eine höhere Priorität eingeräumt wird als bestehenden Inhalten. Sie sollten `enableReprocessing` nur temporär auf `false` festlegen. Um die Konsistenz im Korpus sicherzustellen, sollte `enableReprocessing` in den meisten Fällen auf `true` festgelegt werden, um sicherzustellen, dass alle Dokumente, sowohl neue als auch vorhandene, gemäß der aktuellen Skillsetdefinition gültig sind.

### <a name="bypass-skillset-evaluation"></a>Umgehen der Skillsetauswertung

Das Ändern eines Skillsets und das erneute Verarbeiten dieses Skillsets gehen in der Regel Hand in Hand. Einige Änderungen an einem Skillset sollten jedoch nicht zur erneuten Verarbeitung führen (z. B. das Bereitstellen einer benutzerdefinierten Qualifikation an einem neuen Ort oder mit einem neuen Zugriffsschlüssel). Höchstwahrscheinlich handelt es sich hierbei um periphere Änderungen, die keine echte Auswirkung auf des Inhalt des Skillsets haben. 

Wenn Sie wissen, dass eine Änderung des Skillsets tatsächlich oberflächlich ist, sollten Sie die Skillsetauswertung außer Kraft setzen, indem Sie den Parameter `disableCacheReprocessingChangeDetection` auf `true` festlegen:

1. Rufen Sie „Update Skillset“ auf, und ändern Sie die Skillsetdefinition.
1. Hängen Sie den Parameter `disableCacheReprocessingChangeDetection=true` an die Anforderung an.
1. Übermitteln Sie die Änderung.

Indem Sie diesen Parameter festlegen, wird sichergestellt, dass nur Aktualisierungen der Skillsetdefinition committet werden. Die Auswirkungen der Änderung auf den vorhandenen Korpus werden dagegen nicht ausgewertet.

Das folgende Beispiel zeigt eine Update Skillset-Anforderung mit dem Parameter:

```http
PUT https://[search service].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Umgehen von Datenquellen-Validierungsüberprüfungen

Die meisten Änderungen an einer Datenquellendefinition führen dazu, dass der Cache ungültig wird. In Szenarien, bei denen eine Änderung den Cache nicht ungültig machen sollte (wie z. B. das Ändern einer Verbindungszeichenfolge oder das Rotieren des Schlüssels im Speicherkonto) fügen Sie jedoch den Parameter`ignoreResetRequirement` an die Datenquellenaktualisierung an. Wird dieser Parameter auf `true` festgelegt, kann der Commit durchlaufen werden, ohne eine Bedingung „Zurücksetzen“ auszulösen, die dazu führen würde, dass alle Objekte von Grund auf neu erstellt und gefüllt werden.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Erzwingen der Skillsetauswertung

Der Zweck des Caches ist es, unnötige Verarbeitung zu vermeiden, aber angenommen, Sie nehmen eine Änderung an einer Qualifikation vor, die der Indexer nicht erkennt (etwa eine Änderung an Elementen in externem Code, wie z. B. einer benutzerdefinierten Qualifikation).

In diesem Fall können Sie [Reset Skills](/rest/api/searchservice/preview-api/reset-skills) verwenden, um die erneute Verarbeitung einer bestimmten Qualifikation zu erzwingen, einschließlich aller Downstreamqualifikationen, die von der Ausgabe dieser Qualifikation abhängig sind. Diese API akzeptiert eine POST-Anforderung mit einer Liste von Qualifikationen, die ungültig gemacht und zur erneuten Verarbeitung markiert werden sollten. Führen Sie nach „Reset Skills“ den Indexer aus, um die Pipeline aufzurufen.

### <a name="reset-documents"></a>Zurücksetzen von Dokumenten

Das [Zurücksetzen eines Indexers](/rest/api/searchservice/reset-indexer) führt dazu, dass alle Dokumente im Suchkorpus erneut verarbeitet werden. In Szenarien, in denen nur wenige Dokumente erneut verarbeitet werden müssen und die Datenquelle nicht aktualisiert werden kann, nutzen Sie das [Zurücksetzen von Dokumenten (Vorschau)](/rest/api/searchservice/preview-api/reset-documents), um die erneute Verarbeitung bestimmter Dokumente zu erzwingen. Wenn ein Dokument zurückgesetzt wird, macht der Indexer den Cache für dieses Dokument ungültig, und das Dokument wird erneut verarbeitet, indem es aus der Datenquelle gelesen wird. Weitere Informationen finden Sie unter [Ausführen oder Zurücksetzen von Indexern, Skills und Dokumenten](search-howto-run-reset-indexers.md).

Um bestimmte Dokumente zurückzusetzen, enthält die Anforderungspayload eine Liste von Dokumentschlüsseln, die aus dem Index gelesen werden. Je nachdem, wie Sie die API aufrufen, wird die Anforderung entweder angefügt, überschrieben oder in die Warteschlange der Schlüsselliste gestellt:

+ Wenn Sie die API mehrmals mit unterschiedlichen Schlüsseln aufrufen, werden die neuen Schlüssel der Liste der zurückgesetzten Dokumentschlüssel angehängt. 

+ Wenn Sie die API mit auf TRUE festgelegtem querystring-Parameter `overwrite` aufrufen, wird die aktuelle Liste der zurückzusetzenden Dokumentschlüssel mit den Nutzdaten der Anforderung überschrieben.

+ Das Aufrufen der API führt nur dazu, dass die Dokumentschlüssel der Verarbeitungswarteschlange des Indexers hinzugefügt werden. Wenn der Indexer das nächste Mal aufgerufen wird (entweder geplant oder bei Bedarf), priorisiert er die Verarbeitung der Zurücksetzung der Dokumentschlüssel gegenüber anderen Änderungen in der Datenquelle.

Das folgende Beispiel veranschaulicht eine Zurücksetzungsanforderung für Dokumente:

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "documentKeys" : [
        "key1",
        "key2",
        "key3"
    ]
}
```

## <a name="change-detection"></a>Änderungserkennung

Sobald Sie einen Cache aktivieren, wertet der Indexer Änderungen in Ihrer Pipelinekomposition aus, um zu bestimmen, welche Inhalte wiederverwendet werden können und welche neu verarbeitet werden müssen. In diesem Abschnitt werden Änderungen aufgezählt, die den Cache vollständig ungültig machen, gefolgt von Änderungen, die eine inkrementelle Verarbeitung auslösen. 

### <a name="changes-that-invalidate-the-cache"></a>Änderungen, die den Cache ungültig machen

Durch eine solche Änderung wird der gesamte Cache ungültig. Ein Beispiel für eine solche Änderung wäre die Aktualisierung Ihrer Datenquelle. Im Anschluss folgt eine vollständige Liste der Änderungen, die dazu führen, dass Ihr Cache ungültig wird:

* Änderung des Datenquellentyps
* Änderung des Datenquellencontainers
* Anmeldeinformationen für die Datenquelle
* Richtlinie zur Erkennung von Änderungen für die Datenquelle
* Richtlinie zur Erkennung von Löschungen für die Datenquelle
* Indexerfeldzuordnungen
* Indexerparameter
    * Analysemodus
    * Ausgeschlossene Dateierweiterungen
    * Indizierter Dateierweiterungen
    * Indexspeicher-Metadaten (nur für übergroße Dokumente)
    * Textheader mit Trennzeichen
    * Trennzeichen für Text mit Trennzeichen
    * Dokumentstamm
    * Bildaktion (Änderung an der Art der Bildextraktion)

### <a name="changes-that-trigger-incremental-processing"></a>Änderungen, die eine inkrementelle Verarbeitung auslösen

Bei der inkrementellen Verarbeitung wird Ihre Skillsetdefinition ausgewertet, und es wird ermittelt, welche Qualifikationen erneut ausgeführt werden sollen, wobei die betroffenen Teile der Dokumentstruktur selektiv aktualisiert werden. Hier finden Sie die vollständige Liste der Änderungen, die zu einer inkrementellen Anreicherung führen:

* Anderer Qualifikationstyp im Skillset Aktualisierung des OData-Typs der Qualifikation
* Aktualisierung qualifikationsspezifischer Parameter wie URL, Standardwerte oder andere Parameter
* Änderung der Qualifikationsausgaben (Rückgabe zusätzlicher oder anderer Ausgaben durch die Qualifikation)
* Qualifikationsaktualisierungen, durch die sich eine andere Herkunft ergibt (Änderung der Qualifikationsverkettung, also Qualifikationseingaben)
* Upstream-Qualifikationsinvalidierung (im Falle der Aktualisierung einer Qualifikation, die eine Eingabe für diese Qualifikation bereitstellt)
* Aktualisierung des Projektionsspeicherorts für den Wissensspeicher, wodurch Dokumente neu projiziert werden müssen
* Änderung der Wissensspeicherprojektionen, wodurch Dokumente neu projiziert werden müssen
* Änderung der Ausgabefeldzuordnungen für einen Indexer, wodurch Dokumente erneut im Index projiziert werden müssen

## <a name="api-reference"></a>API-Referenz

Die REST-API-Version `2020-06-30-Preview` bietet eine inkrementelle Anreicherung durch zusätzliche Eigenschaften für Indexer. Skillsets und Datenquellen können die allgemein verfügbare Version verwenden. Ergänzende Informationen zur Referenzdokumentation mit Einzelheiten zum Aufrufen der APIs finden Sie unter [Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md).

+ [Create Indexer (api-version=2020-06-30-Preview)](/rest/api/searchservice/create-indexer) 

+ [Update Indexer (api-version=2020-06-30-Preview)](/rest/api/searchservice/update-indexer) 

+ [Update Skillset (api-version=2020-06-30)](/rest/api/searchservice/update-skillset) (Neuer URI-Parameter in der Anforderung)

+ [Reset Skills (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Datenbankindexer (Azure SQL, Cosmos DB). Einige Indexer rufen Daten über Abfragen ab. Für Abfragen, die Daten abrufen, unterstützt [Update Data Source](/rest/api/searchservice/update-data-source) einen neuen Parameter in einer Anforderung: **ignoreResetRequirement**, der auf `true` festgelegt werden sollte, wenn Ihre Aktualisierungsaktion den Cache nicht ungültig machen soll. 

  Setzen Sie **ignoreResetRequirement** mit Bedacht ein, da diese Option zu unerwünschten Inkonsistenzen in Ihren Daten führen kann, die nicht ohne Weiteres erkennbar sind.

## <a name="next-steps"></a>Nächste Schritte

Inkrementelle Anreicherung ist ein leistungsstarkes Feature, das die Änderungsverfolgung auf Skillsets und KI-Anreicherung ausdehnt. Die inkrementelle Anreicherung ermöglicht die Wiederverwendung vorhandener, verarbeiteter Inhalte beim Durchlaufen des Skillsetdesigns.

Aktivieren Sie im nächsten Schritt die Zwischenspeicherung in einem vorhandenen Indexer, oder fügen Sie den Cache beim Definieren eines neuen Indexers hinzu.

> [!div class="nextstepaction"]
> [Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md)