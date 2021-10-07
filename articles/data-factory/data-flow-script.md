---
title: Zuordnungsdatenflussskript
description: Übersicht über die CodeBehind-Sprache für das Datenflussskript von Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/22/2021
ms.openlocfilehash: 73fe862475b866e625d4bf2bdce3c044b6dcc87b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061578"
---
# <a name="data-flow-script-dfs"></a>Datenflussskript (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Beim Datenflussskript (DFS) handelt es sich um die zugrunde liegenden Metadaten (ähnlich wie eine Programmiersprache), die zur Ausführung der in einem Zuordnungsdatenfluss enthaltenen Transformationen dienen. Jede Transformation wird durch eine Reihe von Eigenschaften dargestellt, die die erforderlichen Informationen zur ordnungsgemäßen Ausführung des Auftrags liefern. Durch Klicken auf die Schaltfläche „Skript“ im oberen Menüband der Browser-Benutzeroberfläche kann das Skript aus ADF heraus angezeigt und bearbeitet werden.

:::image type="content" source="media/data-flow/scriptbutton.png" alt-text="Schaltfläche „Skript“":::

So weist beispielsweise `allowSchemaDrift: true,` in einer Quelltransformation den Dienst an, alle Spalten aus dem Quelldataset in den Datenfluss einzubeziehen – sogar, wenn sie in der Schemaprojektion nicht enthalten sind.

## <a name="use-cases"></a>Anwendungsfälle
Das DFS wird von der Benutzeroberfläche automatisch erstellt. Wenn Sie das Skript anzeigen und anpassen möchten, können Sie auf die Schaltfläche „Skript“ klicken. Sie können Skripts auch außerhalb der ADF-Benutzeroberfläche generieren und dann an das PowerShell-Cmdlet übergeben. Beim Debuggen komplexer Datenflüsse finden Sie es vielleicht einfacher, den Script-CodeBehind zu überprüfen, statt die Darstellung der Diagramm-Benutzeroberfläche für Ihre Datenflüsse überprüfen zu müssen.

Hier sind ein paar Beispiele für Anwendungsfälle:
- Programmgesteuerte Erstellung vieler Datenflüsse, die ziemlich ähnlich sind, d.h., „Ausmerzen“ von Datenflüssen.
- Komplexe Ausdrücke, die in der Benutzeroberfläche schwierig zu verwalten sind oder zu Problemen bei der Überprüfung führen.
- Debuggen von verschiedenen Fehlern, die während der Ausführung zurückgegeben werden, und ein besseres Verständnis dafür.

Wenn Sie ein Datenflussskript zur Verwendung von PowerShell oder einer API erstellen, müssen Sie den formatierten Text in eine einzige Zeile reduzieren. Sie können Tabstopps und Zeilenumbrüche als Escapezeichen beibehalten. Der Text muss aber so formatiert werden, dass er in eine JSON-Eigenschaft passt. In der Benutzeroberfläche des Skript-Editors gibt es ganz unten eine Schaltfläche, über die das Skript als eine einzige Zeile formatiert wird.

:::image type="content" source="media/data-flow/copybutton.png" alt-text="Schaltfläche „Kopieren“":::

## <a name="how-to-add-transforms"></a>Hinzufügen von Transformationen
Zum Hinzufügen von Transformationen sind drei grundlegende Schritte erforderlich: Hinzufügen der Haupttransformationsdaten, Umleiten des Eingabestreams und Umleiten des Ausgabestreams. Dies lässt sich am einfachsten in einem Beispiel zeigen.
Angenommen, Sie beginnen mit einer einfachen Quelle zum Senken des Datenflusses wie hier:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Wenn Sie beschließen, eine abgeleitete Transformation hinzuzufügen, müssen Sie zuerst den Haupttransformationstext erstellen, der einen einfachen Ausdruck zum Hinzufügen der neuen Großbuchstabenspalte `upperCaseTitle` enthält:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Anschließend fügen Sie dem vorhandenen DFS die Transformation hinzu:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Dann leiten Sie den eingehenden Stream um, indem Sie identifizieren, nach welcher Transformation die neue Transformation erfolgen soll (in diesem Fall, `source1`), und den Namen des Streams in die neue Transformation kopieren:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Und schließlich identifizieren Sie die Transformation, die nach dieser neuen Transformation erfolgen soll, und ersetzen deren Eingabestream (in diesem Fall, `sink1`) durch den Namen des Ausgabestreams für die neue Transformation:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS-Grundlagen
Der DFS besteht aus einer Reihe von verbundenen Transformationen, einschließlich Quellen, Senken und verschiedenen anderen, die neue Spalten hinzufügen, Daten filtern, Daten verknüpfen und vieles mehr erledigen können. Normalerweise startet das Skript mit einer oder mehreren Quellen, gefolgt von vielen Transformationen, und es endet mit einer oder mehreren Senken.

Alle Quellen haben denselben Standardaufbau:
```
source(
  source properties
) ~> source_name
```

Beispielsweise würde eine einfache Quelle mit drei Spalten („movieId“, „title“, „genres“) so aussehen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alle anderen Transformationen als Quellen haben denselben Standardaufbau:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Beispielsweise würde eine einfache abgeleitete Transformation, die eine Spalte („title“) mit einer Version in Großbuchstaben überschreibt, so aussehen:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Und eine Senke ohne Schema würde so aussehen:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Skriptausschnitte

Skriptausschnitte sind zur Freigabe geeigneter Code von Datenflussskripts, den Sie Datenflüsse übergreifend verwenden können. Im folgenden Video wird erläutert, wie Sie Skriptausschnitte verwenden und mit dem Datenflussskript Teile des Skripts kopieren und hinter Ihren Datenflussdiagrammen einfügen können:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Aggregierte Zusammenfassungsstatistiken
Fügen Sie dem Datenfluss „SummaryStats“ eine aggregierte Transformation hinzu, und fügen Sie anschließend den folgenden Code für die Aggregatfunktion in das Skript ein. Ersetzen Sie dabei die vorhandenen SummaryStats. Dadurch wird ein generisches Muster für Zusammenfassungsstatistiken zum Datenprofil bereitgestellt.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Sie können auch das unten aufgeführte Beispiel verwenden, um die Anzahl der eindeutigen und die Anzahl der unterschiedlichen Zeilen in Ihren Daten zu ermitteln. Das folgende Beispiel kann in einen Datenfluss mit der Aggregattransformation ValueDistAgg eingefügt werden. In diesem Beispiel wird eine Spalte mit dem Namen „title“ verwendet. Achten Sie darauf, „title“ durch die Zeichenfolgenspalte in Ihren Daten zu ersetzen, die Sie verwenden möchten, um Werteanzahlen zu erhalten.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Einschließen aller Spalten in ein Aggregat
Dabei handelt es sich um ein allgemeines Aggregatmuster, das veranschaulicht, wie Sie beim Erstellen von Aggregaten die verbleibenden Spalten in den Ausgabemetadaten beibehalten können. In diesem Fall verwenden Sie die ```first()```-Funktion, um den ersten Wert in jeder Spalte auszuwählen, deren Name nicht „movie“ ist. Für die Verwendung erstellen Sie eine Aggregattransformation namens „DistinctRows“ und fügen diese dann über dem vorhandenen Aggregatskript „DistinctRows“ in Ihr Skript ein.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Erstellen von Zeilenhash-Fingerabdrücken 
Verwenden Sie diesen Code in Ihrem Datenflussskript, um eine neue abgeleitete Spalte namens ```DWhash``` zu erstellen, die einen ```sha1```-Hash von drei Spalten erzeugt.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Sie können das Skript unten auch zum Generieren eines Zeilenhashs mithilfe aller im Stream vorhandenen Spalten verwenden, ohne jede einzelne Spalte benennen zu müssen:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg equivalent
Dieser Code verhält sich wie die T-SQL-Funktion ```string_agg()``` und aggregiert Zeichenfolgenwerte in ein Array. Dieses Array können Sie dann in eine Zeichenfolge zur Verwendung bei SQL-Zielen umwandeln.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Anzahl von Updates, Upserts, Einfügungen, Löschungen
Bei Verwendung einer Zeilenänderungstransformation können Sie die Anzahl von Updates, Upserts, Einfügungen und Löschvorgängen zählen, die sich aus den Richtlinien für Zeilenänderungen ergeben. Fügen Sie nach der Zeilenänderung eine Transformation für das Aggregieren hinzu, und fügen Sie das folgende Datenflussskript in die Aggregatdefinition für diese Anzahl ein:

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Eindeutige Zeile mit allen Spalten
Mit diesem Codeausschnitt wird dem Datenfluss eine neue Transformation für das Aggregieren hinzugefügt. Dabei werden alle eingehenden Spalten angenommen, ein Hash wird generiert, der für die Gruppierung zum Entfernen von Duplikaten verwendet wird, und anschließend wird das erste Vorkommen der einzelnen Duplikate als Ausgabe bereitgestellt. Sie müssen die Spalten nicht explizit benennen, da sie automatisch aus dem eingehenden Datenstrom generiert werden.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Überprüfung auf NULL in allen Spalten
Dies ist ein Codeausschnitt, den Sie in Ihren Datenfluss einfügen können, um alle Ihre Spalten generisch auf NULL-Werte zu überprüfen. Dieses Verfahren verwendet eine Schemaabweichung, um alle Spalten in allen Zeilen zu durchsuchen, sowie das bedingte Teilen (Conditional Split), um die Zeilen mit NULL-Werten von den Zeilen ohne NULL-Werten zu trennen. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Schemadrift bei automatischer Zuordnung mit select-Funktion
Wenn Sie ein vorhandenes Datenbankschema aus einem unbekannten oder dynamischen Satz eingehender Spalten laden möchten, müssen Sie die Spalten auf der rechten Seite in der Senkentransformation zuordnen. Dies ist nur erforderlich, wenn Sie eine vorhandene Tabelle laden. Fügen Sie diesen Codeausschnitt vor der Senke ein, um eine select-Funktion zu erstellen, die Ihre Spalten automatisch zuordnet. Behalten Sie für die Senke die automatische Zuordnung bei.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Beibehalten von Spaltendatentypen
Fügen Sie dieses Skript in einer Definition für abgeleitete Spalten hinzu, um die Spaltennamen und Datentypen aus dem Datenfluss mithilfe einer Senke in einem permanenten Speicher zu speichern.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Abwärtsgerichtetes Ausfüllen
Hier erfahren Sie, wie Sie das häufige Problem des abwärtsgerichteten Ausfüllens bei Datasets implementieren, wenn Sie NULL-Werte durch den Wert aus dem vorherigen Nicht-NULL-Wert in der Sequenz ersetzen möchten. Beachten Sie, dass sich dieser Vorgang negativ auf die Leistung auswirken kann, da Sie ein synthetisches Fenster für das gesamte Dataset mit dem Kategoriewert „dummy“ erstellen müssen. Außerdem müssen Sie nach einem Wert sortieren, um die richtige Datensequenz zum Ermitteln des vorherigen Nicht-NULL-Werts zu erstellen. Mit dem folgenden Codeausschnitt wird die synthetische Kategorie als „dummy“ erstellt und nach einem Ersatzschlüssel sortiert. Sie können den Ersatzschlüssel entfernen und Ihren eigenen datenspezifischen Sortierschlüssel verwenden. Bei diesem Codeausschnitt wird davon ausgegangen, dass Sie bereits eine Quelltransformation namens ```source1``` hinzugefügt haben.

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Gleitender Durchschnitt
Der gleitende Durchschnitt kann sehr einfach mithilfe einer Fenstertransformation in Datenflüssen implementiert werden. Das Beispiel unten erstellt einen gleitenden Durchschnitt über 15 Tage der Aktienpreise für Microsoft.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

### <a name="distinct-count-of-all-column-values"></a>Distinct count of all column values (Eindeutige Anzahl aller Spaltenwerte)
Sie können dieses Skript verwenden, um Schlüsselspalten zu identifizieren und die Kardinalität aller Spalten in Ihrem Stream mit einem einzelnen Skriptausschnitt anzeigen. Fügen Sie dieses Skript ihrem Datenfluss als Aggregattransformation hinzu, und es stellt automatisch eine eindeutige Anzahl aller Spalten zur Verfügung.

```
aggregate(each(match(true()), $$ = countDistinct($$))) ~> KeyPattern
```

### <a name="compare-previous-or-next-row-values"></a>Vergleich mit Werten in der Zeile davor oder danach
Dieser Beispielausschnitt zeigt, wie die Fenstertransformation verwendet werden kann, um Spaltenwerte aus dem aktuellen Zeilenkontext mit Spaltenwerten aus Zeilen vor und nach der aktuellen Zeile zu vergleichen. In diesem Beispiel wird eine abgeleitete Spalte verwendet, um einen Dummywert zu generieren und so eine Fensterpartition für das gesamte Dataset zu ermöglichen. Mit einer Ersatzschlüsseltransformation wird jeder Zeile ein eindeutiger Schlüsselwert zugewiesen. Wenn Sie dieses Muster auf Ihre Datentransformationen anwenden, können Sie den Ersatzschlüssel entfernen, wenn es sich um eine Spalte handelt, nach der Sie sortieren möchten. Wenn Sie über Spalten verfügen, die zum Partitionieren Ihrer Daten verwendet werden, können Sie die abgeleitete Spalte entfernen.

```
source1 keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey1
SurrogateKey1 derive(dummy = 1) ~> DerivedColumn1
DerivedColumn1 window(over(dummy),
    asc(sk, true),
    prevAndCurr = lag(title,1)+'-'+last(title),
        nextAndCurr = lead(title,1)+'-'+last(title)) ~> leadAndLag
```

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie Datenflüsse, und lesen Sie zunächst den [Artikel „data flows overview“](concepts-data-flow-overview.md) (Übersicht über Datenflüsse).
