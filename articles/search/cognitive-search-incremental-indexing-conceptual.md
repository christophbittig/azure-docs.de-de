---
title: Konzepte zur inkrementellen Anreicherung (Vorschau)
titleSuffix: Azure Cognitive Search
description: Sie können Zwischeninhalte und inkrementelle Änderungen aus der KI-Anreicherungspipeline in Azure Storage zwischenspeichern, um Investitionen in vorhandene verarbeitete Dokumente zu erhalten. Dieses Feature ist zurzeit als öffentliche Preview verfügbar.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/17/2021
ms.openlocfilehash: cb04bbea71588ea8d9fa1f1c4734b10e3d0b6792
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179201"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Inkrementelle Anreicherung und Zwischenspeicherung in Azure Cognitive Search

> [!IMPORTANT] 
> Dieses Feature befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

*Inkrementelle Anreicherung* bezieht sich auf die Verwendung von zwischengespeicherten Anreicherungen während der [Ausführung von Skillsets](cognitive-search-working-with-skillsets.md), sodass nur für neue und geänderte Skills und Dokumente KI-Verarbeitung erfolgt. Der Cache enthält die Ausgaben aus der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking) sowie die Ausgaben der einzelnen Skills für jedes Dokument. Da Bildextraktion und KI-Verarbeitung [abrechenbare Ereignisse](search-sku-manage-costs.md#billable-events) sind, reduziert die Aktivierung des Caches die Kosten und verbessert die Verarbeitungszeit der KI-Anreicherung. 

Wenn Sie die Zwischenspeicherung aktivieren, wertet der Indexer Ihre Updates aus, um zu bestimmen, ob vorhandene Anreicherungen aus dem Cache gezogen werden können. Bild- und Textinhalte aus der Phase der Dokumententschlüsselung sowie Skillausgaben, die sich upstream oder orthogonal zu Ihren Bearbeitungen befinden, sind wahrscheinlich wiederverwendbar.

Nach dem Ausführen der inkrementellen Anreicherungen, wie durch das Skillsetupdate angegeben, werden aktualisierte Ergebnisse in den Cache und auch in den Suchindex oder Wissensspeicher zurückgeschrieben.

## <a name="cache-configuration"></a>Cachekonfiguration

Der Cache wird physisch in einem Blobcontainer in Ihrem Azure Storage-Konto gespeichert, jeweils einer je Indexer. Jedem Indexer wird ein eindeutiger und unveränderlicher Cachebezeichner zugewiesen, der dem Container entspricht, der ihn verwendet.

Der Cache wird erstellt, wenn Sie die Eigenschaft „cache“ angeben und den Indexer ausführen. Nur angereicherte Inhalte können zwischengespeichert werden. Wenn Ihr Indexer nicht über ein angefügtes Skillset verfügt, wird die Zwischenspeicherung nicht angewendet. 

Das folgende Beispiel veranschaulicht einen Indexer, bei dem die Zwischenspeicherung aktiviert ist. Vollständige Anweisungen finden Sie unter [Aktivieren der Anreicherungszwischenspeicherung](search-howto-incremental-index.md). Beachten Sie, dass Sie beim Hinzufügen der Eigenschaft „cache“ die Vorschauversion der API (2020-06-30-Preview oder höher) für die Anforderung verwenden.

```json
POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    {
        "name": "myIndexerName",
        "targetIndexName": "myIndex",
        "dataSourceName": "myDatasource",
        "skillsetName": "mySkillset",
        "cache" : {
            "storageConnectionString" : "<Your storage account connection string>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

## <a name="cache-management"></a>Cacheverwaltung

Der Lebenszyklus des Caches wird vom Indexer verwaltet. Wird ein Indexer gelöscht, wird auch sein Cache gelöscht. Wird die Eigenschaft „cache“ im Indexer auf „Null“ festgelegt oder die Verbindungszeichenfolge geändert, wird der vorhandene Cache gelöscht. 

Die inkrementelle Anreicherung ist zwar so konzipiert, dass Änderungen erkannt werden und darauf reagiert wird, ohne dass Sie selbst eingreifen müssen. Es gibt aber Parameter, die Sie verwenden können, um bestimmte Verhaltensweisen aufzurufen:

+ [Priorisieren neuer Dokumente](#Prioritize-new-documents)
+ [Umgeben von Skillsetüberprüfungen](#Bypass-skillset-checks)
+ [Umgeben von Datenquellenüberprüfungen](#Bypass-data-source-check)
+ [Erzwingen der Skillsetauswertung](#Force-skillset-evaluation)

<a name="Prioritize-new-documents"></a>

### <a name="prioritize-new-documents"></a>Priorisieren neuer Dokumente

Die Eigenschaft „cache“ enthält einen enableReprocessing-Parameter. Er wird verwendet, um die Verarbeitung eingehender Dokumente, die bereits im Cache vorhanden sind, zu steuern. Ist diese Option auf „true“ festgelegt (Standard), werden Dokumente, die bereits im Cache vorhanden sind, bei der erneuten Ausführung des Indexers erneut verarbeitet, vorausgesetzt, dass sich Ihre Skillaktualisierung auf dieses Dokument auswirkt. 

Ist diese Option auf „false“ festgelegt, werden bestehende Dokumente nicht erneut verarbeitet, wodurch neuen, eingehenden Inhalten effektiv eine höhere Priorität eingeräumt wird als bestehenden Inhalten. Sie sollten „enableReprocessing“ nur vorübergehend auf „false“ festlegen. Indem „enableReprocessing“ meistens auf „true“ festgelegt ist, wird sichergestellt, dass alle Dokumente, sowohl neue als auch vorhandene, nach der aktuellen Skillsetdefinition gültig sind.

<a name="Bypass-skillset-checks"></a>

### <a name="bypass-skillset-evaluation"></a>Umgehen der Skillsetauswertung

Das Ändern eines Skills und das erneute Verarbeiten dieses Skills gehen in der Regel Hand in Hand. Einige Änderungen an einem Skill sollten jedoch nicht zur erneuten Verarbeitung führen (z. B. das Bereitstellen eines benutzerdefinierten Skills an einem neuen Ort oder mit einem neuen Zugriffsschlüssel). Höchstwahrscheinlich handelt es sich hierbei um periphere Änderungen, die keine echte Auswirkung auf den Inhalt der Skillausgabe haben. 

Wenn Sie wissen, dass eine Änderung des Skills tatsächlich oberflächlich ist, sollten Sie die Skillauswertung außer Kraft setzen, indem Sie den Parameter „disableCacheReprocessingChangeDetection“ auf „true“ festlegen:

1. Rufen Sie [Skillset aktualisieren](/rest/api/searchservice/update-skillset) auf, und ändern Sie die Skillsetdefinition.
1. Fügen Sie den Parameter „disableCacheReprocessingChangeDetection=true“ an die Anforderung an.
1. Übermitteln Sie die Änderung.

Indem Sie diesen Parameter festlegen, wird sichergestellt, dass nur Aktualisierungen der Skillsetdefinition committet werden. Die Auswirkungen der Änderung auf den vorhandenen Cache werden dagegen nicht ausgewertet. Verwenden Sie eine Vorschauversion der API (2020-06-30-Preview oder höher).

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview
    {
        "disableCacheReprocessingChangeDetection" : true
    }
```

<a name="Bypass-data-source-check"></a>

### <a name="bypass-data-source-validation-checks"></a>Umgehen von Datenquellen-Validierungsüberprüfungen

Die meisten Änderungen an einer Datenquellendefinition führen dazu, dass der Cache ungültig wird. In Szenarien, bei denen eine Änderung den Cache nicht ungültig machen sollte (wie z. B. das Ändern einer Verbindungszeichenfolge oder das Rotieren des Schlüssels im Speicherkonto) fügen Sie jedoch den Parameter“ignoreResetRequirement“ an die Datenquellenaktualisierung an. Wird dieser Parameter auf „true“ festgelegt, kann der Commit durchlaufen werden, ohne eine Bedingung „Zurücksetzen“ auszulösen, die dazu führen würde, dass alle Objekte von Grund auf neu erstellt und gefüllt werden.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview
    {
        "ignoreResetRequirement" : true
    }
```

<a name="Force-skillset-evaluation"></a>

### <a name="force-skillset-evaluation"></a>Erzwingen der Skillsetauswertung

Der Zweck des Caches ist es, unnötige Verarbeitung zu vermeiden, aber angenommen, Sie nehmen eine Änderung an einer Qualifikation vor, die der Indexer nicht erkennt (etwa eine Änderung an Elementen in externem Code, wie z. B. einer benutzerdefinierten Qualifikation).

In diesem Fall können Sie [Reset Skills](/rest/api/searchservice/preview-api/reset-skills) verwenden, um die erneute Verarbeitung einer bestimmten Qualifikation zu erzwingen, einschließlich aller Downstreamqualifikationen, die von der Ausgabe dieser Qualifikation abhängig sind. Diese API akzeptiert eine POST-Anforderung mit einer Liste von Qualifikationen, die ungültig gemacht und zur erneuten Verarbeitung markiert werden sollten. Führen Sie nach dem Zurücksetzen von Skills eine [Run Indexer](/rest/api/searchservice/run-indexer)-Anforderung aus, um die Pipelineverarbeitung aufzurufen.

## <a name="re-cache-specific-documents"></a>Erneutes Zwischenspeichern bestimmter Dokumente

Das [Zurücksetzen eines Indexers](/rest/api/searchservice/reset-indexer) führt dazu, dass alle Dokumente im Suchkorpus erneut verarbeitet werden. In Szenarien, in denen nur wenige Dokumente erneut verarbeitet werden müssen, nutzen Sie das [Zurücksetzen von Dokumenten (Vorschau)](/rest/api/searchservice/preview-api/reset-documents), um die erneute Verarbeitung bestimmter Dokumente zu erzwingen. Wenn ein Dokument zurückgesetzt wird, macht der Indexer den Cache für dieses Dokument ungültig, das dann erneut verarbeitet wird, indem es aus der Datenquelle gelesen wird. Weitere Informationen finden Sie unter [Ausführen oder Zurücksetzen von Indexern, Skills und Dokumenten](search-howto-run-reset-indexers.md).

Um bestimmte Dokumente zurückzusetzen, stellt die Anforderung eine Liste von Dokumentschlüsseln bereit, die aus dem Suchindex gelesen werden. Wenn der Schlüssel einem Feld in der externen Datenquelle zugeordnet ist, sollte der Wert, den Sie bereitstellen, derjenige sein, der im Suchindex verwendet wird.

Je nachdem, wie Sie die API aufrufen, wird die Anforderung entweder angefügt, überschrieben oder in die Warteschlange der Schlüsselliste gestellt:

+ Wenn Sie die API mehrmals mit unterschiedlichen Schlüsseln aufrufen, werden die neuen Schlüssel der Liste der zurückgesetzten Dokumentschlüssel angehängt. 

+ Wenn Sie die API mit auf „true“ festgelegtem Abfragezeichenfolgen-Parameter „overwrite“ aufrufen, wird die aktuelle Liste der zurückzusetzenden Dokumentschlüssel mit den Nutzdaten der Anforderung überschrieben.

+ Das Aufrufen der API führt nur dazu, dass die Dokumentschlüssel der Verarbeitungswarteschlange des Indexers hinzugefügt werden. Wenn der Indexer das nächste Mal aufgerufen wird (entweder geplant oder bei Bedarf), priorisiert er die Verarbeitung der Zurücksetzung der Dokumentschlüssel gegenüber anderen Änderungen in der Datenquelle.

Das folgende Beispiel veranschaulicht eine Zurücksetzungsanforderung für Dokumente:

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
    {
        "documentKeys" : [
            "key1",
            "key2",
            "key3"
        ]
    }
```

## <a name="changes-that-invalidate-the-cache"></a>Änderungen, die den Cache ungültig machen

Sobald Sie einen Cache aktivieren, wertet der Indexer Änderungen in Ihrer Pipelinekomposition aus, um zu bestimmen, welche Inhalte wiederverwendet werden können und welche neu verarbeitet werden müssen. In diesem Abschnitt werden Änderungen aufgezählt, die den Cache vollständig ungültig machen, gefolgt von Änderungen, die eine inkrementelle Verarbeitung auslösen. 

Durch eine solche Änderung wird der gesamte Cache ungültig. Ein Beispiel für eine solche Änderung wäre die Aktualisierung Ihrer Datenquelle. Dies ist die vollständige Liste der Änderungen an einem beliebigen Teil der Indexerpipeline, die Ihren Cache ungültig machen würden:

+ Ändern des Datenquellentyps
+ Ändern des Datenquellencontainers
+ Ändern der Anmeldeinformationen der Datenquelle
+ Ändern der Richtlinie zur Erkennung von Änderungen für die Datenquelle
+ Ändern der Richtlinie zur Erkennung von Löschungen für die Datenquelle
+ Ändern von Indexerfeldzuordnungen
+ Ändern von Indexerparametern:
  + Analysemodus
  + Ausgeschlossene Dateierweiterungen
  + Indizierter Dateierweiterungen
  + Indexspeicher-Metadaten (nur für übergroße Dokumente)
  + Textheader mit Trennzeichen
  + Trennzeichen für Text mit Trennzeichen
  + Dokumentstamm
  + Bildaktion (Änderung an der Art der Bildextraktion)

## <a name="changes-that-trigger-incremental-processing"></a>Änderungen, die eine inkrementelle Verarbeitung auslösen

Bei der inkrementellen Verarbeitung wird Ihre Skillsetdefinition ausgewertet, und es wird ermittelt, welche Qualifikationen erneut ausgeführt werden sollen, wobei die betroffenen Teile der Dokumentstruktur selektiv aktualisiert werden. Hier finden Sie die vollständige Liste der Änderungen, die zu einer inkrementellen Anreicherung führen:

+ Ändern des Skilltyps (der OData-Typ des Skills wird aktualisiert)
+ Skillspezifische Parameter wie URL, Standardwerte oder andere Parameter werden aktualisiert
+ Skillausgabeänderungen, der Skill gibt zusätzliche oder andere Ausgaben zurück
+ Skilleingabeänderungen, durch die sich eine andere Herkunft ergibt, Änderung der Skillverkettung
+ Upstream-Qualifikationsinvalidierung (im Falle der Aktualisierung einer Qualifikation, die eine Eingabe für diese Qualifikation bereitstellt)
+ Aktualisierungen des Speicherorts für Wissensspeicherprojektionen, wodurch Dokumente neu projiziert werden müssen
+ Änderungen an den Wissensspeicherprojektionen, wodurch Dokumente neu projiziert werden müssen
+ Die Ausgabefeldzuordnungen für einen Indexer wurden geändert, wodurch Dokumente im Index neu projiziert werden müssen

## <a name="apis-used-for-caching"></a>Für die Zwischenspeicherung verwendete APIs

Die REST-API-Version `2020-06-30-Preview` (oder höher) bietet eine inkrementelle Anreicherung durch zusätzliche Eigenschaften für Indexer. Skillsets und Datenquellen können die allgemein verfügbare Version verwenden. Ergänzende Informationen zur Referenzdokumentation mit Einzelheiten zur Reihenfolge der Vorgänge finden Sie unter [Konfigurieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md).

+ [Indexer erstellen oder aktualisieren (api-version=2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) 

+ [Update Skillset (api-version=2020-06-30)](/rest/api/searchservice/update-skillset) (Neuer URI-Parameter in der Anforderung)

+ [Reset Skills (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ [Datenquelle aktualisieren](/rest/api/searchservice/update-data-source) stellt beim Aufruf mit einer Vorschauversion der API einen neuen Parameter mit dem Namen „ignoreResetRequirement“ bereit, der auf „true“ festgelegt werden sollte, wenn der Cache durch Ihre Updateaktion nicht ungültig gemacht werden soll. Setzen Sie „ignoreResetRequirement“ mit Bedacht ein, da diese Option zu unerwünschten Inkonsistenzen in Ihren Daten führen kann, die nicht ohne Weiteres erkennbar sind.

## <a name="next-steps"></a>Nächste Schritte

Inkrementelle Anreicherung ist ein leistungsstarkes Feature, das die Änderungsverfolgung auf Skillsets und KI-Anreicherung ausdehnt. Die inkrementelle Anreicherung ermöglicht die Wiederverwendung vorhandener, verarbeiteter Inhalte beim Durchlaufen des Skillsetdesigns. Aktivieren Sie im nächsten Schritt das Zwischenspeichern für Ihre Indexer.

> [!div class="nextstepaction"]
> [Aktivieren der Zwischenspeicherung für die inkrementelle Anreicherung](search-howto-incremental-index.md)