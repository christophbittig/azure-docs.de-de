---
title: Ausführen oder Zurücksetzen von Indexern
titleSuffix: Azure Cognitive Search
description: Führen Sie Indexer vollständig aus, oder setzen Sie einen Indexer, Qualifikationen oder einzelne Dokumente zurück, um einen Suchindex oder einen Wissensspeicher ganz oder teilweise zu aktualisieren.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: e29c511a59d8b446b497a8fd4ff393c9a9c683e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449296"
---
# <a name="run-or-reset-indexers-skills-or-documents"></a>Ausführen oder Zurücksetzen von Indexern, Qualifikationen oder Dokumenten

Indexer können auf drei Arten aufgerufen werden: bei Bedarf, nach einem Zeitplan oder beim [Erstellen des Indexers](/rest/api/searchservice/create-indexer). Nach der ersten Ausführung verfolgt ein Indexer, welche Suchdokumente über eine interne obere Grenze indiziert wurden. Der Marker wird nie in der API verfügbar gemacht, aber intern weiß der Indexer, wo die Indizierung angehalten wurde, sodass er bei der nächsten Ausführung an der Stelle fortfahren kann, wo er aufgehört hat.

Sie können die obere Grenze löschen, indem Sie den Indexer zurücksetzen, wenn Sie die Verarbeitung von Grund auf neu durchführen möchten. Rücksetz-APIs sind in der Objekthierarchie auf abnehmenden Ebenen verfügbar:

+ Der gesamte Suchkorpus (siehe [Zurücksetzen eines Indexers](#reset-indexers))
+ Ein bestimmtes Dokument oder eine Liste von Dokumenten (siehe [Zurücksetzen von Dokumenten (Vorschau)](#reset-docs))
+ Eine bestimmte Qualifikation oder eine Anreicherung in einem Dokument (verwenden Sie [Zurücksetzen von Qualifikationen - Vorschau](#reset-skills))

Die Rücksetz-APIs werden verwendet, um zwischengespeicherte (in Szenarien zur [KI-Anreicherung](cognitive-search-concept-intro.md) anwendbare) Inhalte zu aktualisieren oder um die obere Grenze zu löschen und den Index neu zu erstellen.

Durch Zurücksetzen, gefolgt von der Ausführung, können vorhandene Dokumente und neue Dokumente erneut verarbeitet werden, doch werden keine verwaisten Suchdokumente im Suchindex entfernt, die in vorherigen Ausführungen erstellt wurden. Weitere Informationen zum Löschen finden Sie unter [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (Azure Cognitive Search-REST-API)](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-run-indexers"></a>Ausführen von Indexern

Mit dem [Erstellen eines Indexers](/rest/api/searchservice/create-indexer) wird der Indexer erstellt und ausgeführt, es sei denn, Sie erstellen ihn in einem deaktivierten Zustand ("disabled": true). Die erste Ausführung dauert etwas länger, da sie auch die Objekterstellung abdeckt.

Beim [Ausführen des Indexers](/rest/api/searchservice/run-indexer) wird nur das erkannt und verarbeitet, was erforderlich ist, um den Suchindex mit der Datenquelle zu synchronisieren. Blobspeicher verfügt über eine integrierte Änderungserkennung. Andere Datenquellen, z. B. Azure SQL oder Cosmos DB, müssen für die Änderungserkennung konfiguriert werden, damit der Indexer sich beim Lesen auf die neuen und aktualisierten Zeilen beschränken kann.

Sie können einen Indexer ausführen, indem Sie einen der folgenden Ansätze verwenden:

+ Azure-Portal mithilfe des Befehls **Ausführen** auf der Indexerseite
+ [Ausführen des Indexers (REST)](/rest/api/searchservice/run-indexer)
+ [RunIndexers-Methode](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) im Azure .NET SDK (oder Verwenden der entsprechenden RunIndexer-Methode in einem anderen SDK)

## <a name="indexer-execution"></a>Indexer-Ausführung

Die Indexerausführung unterliegt den folgenden Einschränkungen:

+ Die maximale Anzahl von Indexeraufträgen beträgt 1 pro Replikat.

  Wenn der Indexer bereits an der Kapazitätsgrenze ausgeführt wird, erhalten Sie diese Benachrichtigung: „Fehler beim Ausführen des Indexers '\<indexer-name\>', Fehler: „Ein anderer Indexeraufruf wird derzeit ausgeführt; parallele Aufrufe sind nicht zulässig.“

+ Die maximale Ausführungszeit beträgt 2 Stunden, wenn ein Skillset verwendet wird, und 24 Stunden ohne. 

  Wenn Sie [einen großen Datensatz indexieren](search-howto-large-index.md), können Sie die Verarbeitung ausdehnen, indem Sie einen Zeitplan für den Indexer erstellen. Im Free-Tarif gelten niedrigere Ausführungszeitlimits. Die vollständige Liste finden Sie unter [Indexergrenzwerte](search-limits-quotas-capacity.md#indexer-limits).

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Zurücksetzen eines Indexers

Das Zurücksetzen eines Indexers ist allumfassend. Innerhalb des Suchindexes wird jedes Suchdokument, das ursprünglich durch den Indexer aufgefüllt wurde, für die vollständige Verarbeitung markiert. Alle in der zugrunde liegenden Quelle gefundenen neuen Dokumente werden dem Index als Suchdokumente hinzugefügt. Wenn der Indexer für die Verwendung eines Skillsets und für das [Zwischenspeichern](search-howto-incremental-index.md) konfiguriert ist, wird das Skillset erneut ausgeführt und der Cache wird aktualisiert.

Sie können einen Indexer mit einem dieser Ansätze zurücksetzen, gefolgt von einer Indexerausführung mit einer der oben beschriebenen Methoden.

+ Azure-Portal: mit Befehl **Zurücksetzen** auf der Indexerseite
+ [Zurücksetzen des Indexers (REST)](/rest/api/searchservice/reset-indexer)
+ [ResetIndexers-Methode](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) im Azure .NET SDK (oder Verwenden der entsprechenden RunIndexer-Methode in einem anderen SDK)

Ein Rücksetzflag wird nach Abschluss der Ausführung gelöscht. Jede reguläre Änderungserkennungslogik, die für die Datenquelle verwendet wird, wird bei der nächsten Ausführung fortgesetzt, wobei alle anderen neuen oder aktualisierten Werte im restlichen Dataset übernommen werden.

> [!NOTE]
> Eine Rücksetzungsanforderung bestimmt, was neu verarbeitet wird (Indexer, Fertigkeiten oder Dokument), wirkt sich jedoch anderweitig nicht auf das Laufzeitverhalten des Indexers aus. Wenn der Indexer über Laufzeitparameter, Feldzuordnungen, Zwischenspeichern, Batchoptionen usw. verfügt, sind diese Einstellungen wirksam, wenn ein Indexer nach dem Zurücksetzen ausgeführt wird.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Zurücksetzen von Fertigkeiten (Vorschau)

Für Indexer, die über Skillsets verfügen, können Sie bestimmte Fertigkeiten zurücksetzen, um die Verarbeitung dieser Fertigkeit und aller nachgelagerten Fertigkeiten zu erzwingen, die von ihrer Ausgabe abhängen. [Zwischengespeicherte Anreicherungen](search-howto-incremental-index.md) werden ebenfalls aktualisiert. Durch das Zurücksetzen von Fertigkeiten werden die zwischengespeicherten Fertigkeitenergebnisse ungültig, was nützlich ist, wenn eine neue Version einer Fertigkeit bereitgestellt wird und der Indexer diese Fertigkeit für alle Dokumente erneut ausführen soll. 

Das [Zurücksetzen von Qualifikationen](/rest/api/searchservice/preview-api/reset-skills) ist über REST **`api-version=2020-06-30-Preview`** oder höher verfügbar.

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Sie können individuelle Fertigkeiten angeben, wie im obigen Beispiel gezeigt, aber wenn eine dieser Fertigkeiten eine Ausgabe aus nicht aufgelisteten Fertigkeiten (2 bis 4) erfordert, werden nicht aufgelistete Fertigkeiten ausgeführt, sofern der Cache die erforderlichen Informationen nicht bereitstellen kann. Damit dies zutrifft, dürfen zwischengespeicherte Anreicherungen für Fertigkeiten 2 bis 4 keine Abhängigkeit von 1 (für die Zurücksetzung aufgelistet) aufweisen.

Wenn keine Fertigkeiten angegeben werden, wird das gesamte Skillset ausgeführt, und wenn das Zwischenspeichern aktiviert ist, wird der Cache ebenfalls aktualisiert.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Zurücksetzen von Dokumenten (Vorschau)

Die [API zum Zurücksetzen von Dokumenten](/rest/api/searchservice/preview-api/reset-documents) akzeptiert eine Liste von Dokumentschlüsseln, damit Sie bestimmte Dokumente aktualisieren können. Wenn angegeben, sind die Rücksetzungsparameter unabhängig von anderen Änderungen in den zugrunde liegenden Daten allein dafür ausschlaggebend, was verarbeitet wird. Wenn z. B. seit der letzten Indexerausführung 20 Blobs hinzugefügt oder aktualisiert wurden, Sie aber nur ein Dokument zurücksetzen, wird nur dieses eine Dokument verarbeitet.

Auf Dokumentbasis werden alle Felder in diesem Suchdokument mit Werten aus der Datenquelle aktualisiert. Sie können nicht auswählen, welche Felder aktualisiert werden sollen. 

Wenn das Dokument durch ein Skillset angereichert wird und zwischengespeicherte Daten enthält, wird das Skillset nur für die angegebenen Dokumente aufgerufen, und die zwischengespeicherten Daten werden für die neu verarbeiteten Dokumente aktualisiert.

Wenn Sie diese API zum ersten Mal testen, können Sie mithilfe der folgenden APIs die Verhaltensweisen validieren und testen:

+ [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status) mit API-Version **`api-version=2020-06-30-Preview`** oder höher, um den Status der Zurücksetzung und den Ausführungsstatus zu überprüfen. Informationen über die Rücksetzungsanforderung finden Sie am Ende der Statusantwort.
+ [Zurücksetzen der Dokumente](/rest/api/searchservice/preview-api/reset-documents) mit der API-Version **`api-version=2020-06-30-Preview`** oder höher, um anzugeben, welche Dokumente verarbeitet werden sollen.
+ [Ausführen des Indexers](/rest/api/searchservice/run-indexer), um den Indexer (beliebige API-Version) auszuführen.
+ [Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents), um nach aktualisierten Werten zu suchen, und außerdem, um Dokumentschlüssel zurückzugeben, wenn Sie sich über den Wert nicht sicher sind. Verwenden Sie `"select": "<field names>"`, wenn Sie einschränken möchten, welche Felder in der Antwort angezeigt werden.

### <a name="formulate-and-send-the-reset-request"></a>Formulieren und Senden der Rücksetzungsanforderung

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Die in der Anforderung bereitgestellten Dokumentschlüssel sind Werte aus dem Suchindex, die sich von den entsprechenden Feldern in der Datenquelle unterscheiden können. Wenn Sie sich über den Schlüsselwert nicht sicher sind, [senden Sie eine Abfrage](search-query-create.md), um den Wert zurückzugeben. Sie können `select` verwenden, um nur das Dokumentschlüsselfeld zurückzugeben.

Für Blobs, die in mehreren Suchdokumenten analysiert werden (wenn Sie z. B. [jsonLines oder jsonArrays](search-howto-index-json-blobs.md) bzw. [delimitedText](search-howto-index-csv-blobs.md) als Analysemodus verwendet haben), wird der Dokumentschlüssel vom Indexer generiert und ist Ihnen möglicherweise unbekannt. In diesem Fall ist eine Abfrage nach dem Dokumentschlüssel wichtig, um den korrekten Wert bereitzustellen.

Wenn Sie die API mehrmals mit unterschiedlichen Schlüsseln aufrufen, werden die neuen Schlüssel der Liste der zurückgesetzten Dokumentschlüssel angehängt. Wenn Sie die API mit auf TRUE festgelegtem **`overwrite`** -Parameter aufrufen, wird die aktuelle Liste der zurückzusetzenden Dokumentschlüssel mit den Nutzdaten der Anforderung überschrieben:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Überprüfen des Rücksetzungsstatus

Verwenden Sie zum Überprüfen des Status einer Zurücksetzung und zum Anzeigen der Dokumentschlüssel, die sich zur Verarbeitung gerade in der Warteschlange befinden, [Abrufen des Indexer-Status](/rest/api/searchservice/get-indexer-status) mit **`api-version=06-30-2020-Preview`** oder höher. Die Vorschau-API gibt den **`currentState`** -Abschnitt zurück, den Sie am Ende der Antwort von „Abrufen des Indexerstatus“ finden.

Der „Modus“ **`indexingAllDocs`** ist für das Zurücksetzen von Fertigkeiten verfügbar (da für die Felder, die durch die KI-Anreicherung aufgefüllt werden, potenziell alle Dokumente betroffen sind).

Für das Zurücksetzen von Dokumenten wird der Modus auf **`indexingResetDocs`** festgelegt. Der Indexer behält diesen Status so lange bei, bis alle Dokumentschlüssel im Aufruf zum Zurücksetzen der Dokumente verarbeitet sind, und während der Vorgang läuft, werden keine anderen Indexeraufträge ausgeführt. Um alle Dokumente in der Dokumentschlüsselliste zu finden, muss jedes Dokument zum Suchen und Abgleichen mit dem Schlüssel geöffnet werden. Dies kann bei großen Datasets einige Zeit in Anspruch nehmen. Wenn ein Blobcontainer Hunderte von Blobs enthält und sich die zurückzusetzenden Dokumente am Ende befinden, findet der Indexer die entsprechenden Blobs erst, wenn alle anderen zuerst geprüft wurden.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Nach der erneuten Verarbeitung der Dokumente kehrt der Indexer in den **`indexingAllDocs`** -Modus zurück und verarbeitet alle anderen neuen oder aktualisierten Dokumente bei der nächsten Ausführung.

## <a name="next-steps"></a>Nächste Schritte

Rücksetz-APIs werden verwendet, um über den Umfang der nächsten Indexerausführung zu informieren. Zur eigentlichen Verarbeitung müssen Sie eine bedarfsgesteuerte Indexerausführung aufrufen oder einen geplanten Auftrag zum Abschließen der Arbeit zulassen. Nachdem die Ausführung abgeschlossen ist, kehrt der Indexer unabhängig davon, ob es sich um eine Verarbeitung nach Zeitplan oder eine bedarfsgesteuerte Verarbeitung handelt, zur normalen Verarbeitung zurück.

Nachdem Sie Indexeraufträge zurückgesetzt und erneut ausgeführt haben, können Sie den Status vom Suchdienst aus überwachen oder ausführliche Informationen über die Diagnoseprotokollierung abrufen.

+ [Indexervorgänge (REST)](/rest/api/searchservice/indexer-operations)
+ [Überwachen des Status von Suchindexern](search-howto-monitor-indexers.md)
+ [Sammeln und Analysieren von Protokolldaten](search-monitor-logs.md)
+ [Planen eines Indexers](search-howto-schedule-indexers.md)