---
title: Verwenden von Parametern und Ausdrücken in Azure Data Factory
description: Dieser Artikel zur Vorgehensweise enthält Informationen zu Ausdrücken und Funktionen, die Sie bei der Erstellung von Data Factory-Entitäten verwenden können.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 41689a842bff067fb36dd97f980c10b71e533f8d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446860"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Verwenden von Parametern, Ausdrücken und Funktionen in Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuelle Version](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Dokument konzentrieren wir uns in erster Linie auf das Erlernen grundlegender Konzepte mit verschiedenen Beispielen, um die Möglichkeit zum Erstellen von parametrisierten Datenpipelines in Azure Data Factory zu untersuchen. Parametrisierung und dynamische Ausdrücke sind solche bedeutenden Ergänzungen von ADF, da sie sehr viel Zeit sparen können und eine viel flexiblere Lösung zum Extrahieren, Transformieren und Laden (Extract, Transform, Load, ETL) oder Extrahieren, Laden und Transformieren (Extract, Load, Transform, ELT) ermöglichen, wodurch die Kosten für die Wartung der Lösung drastisch gesenkt werden und die Implementierung neuer Features in vorhandene Pipelines beschleunigt wird. Diese Vorteile basieren darauf, dass die Parametrisierung die Hartcodierung minimiert und die Anzahl der wiederverwendbaren Objekte und Prozesse in einer Lösung erhöht.

## <a name="azure-data-factory-ui-and-parameters"></a>Benutzeroberfläche und Parameter von Azure Data Factory

Wenn Sie noch nicht mit der Verwendung von Azure Data Factory-Parametern in der ADF-Benutzeroberfläche vertraut sind, nutzen Sie die visuelle Erläuterung in den Artikeln zur [Data Factory-Benutzeroberfläche für verknüpfte Dienste mit Parametern](./parameterize-linked-services.md#ui-experience) und [Data Factory-Benutzeroberfläche für metadatengesteuerte Pipelines mit Parametern](./how-to-use-trigger-parameterization.md#data-factory-ui).

## <a name="parameter-and-expression-concepts"></a>Parameter- und Ausdruckskonzepte 

Sie können mit Parametern externe Werte an Pipelines, Datasets, verknüpfte Dienste und Datenflüsse übergeben. Nachdem der Parameter an die Ressource übergeben wurde, kann er nicht mehr geändert werden. Indem Sie Ressourcen parametrisieren, können Sie sie jedes Mal mit unterschiedlichen Werten wiederverwenden. Parameter können einzeln oder als Teil von Ausdrücken verwendet werden. Bei den JSON-Werten in der Definition kann es sich um Literalwerte oder um Ausdrücke handeln, die zur Laufzeit ausgewertet werden.

Beispiel:  
  
```json
"name": "value"
```

 oder  
  
```json
"name": "@pipeline().parameters.password"
```

Ausdrücke können an beliebiger Stelle in einem JSON-Zeichenfolgenwert verwendet werden und ergeben immer einen anderen JSON-Wert. Hier ist *password* ein Pipelineparameter im Ausdruck. Wenn ein JSON-Wert ein Ausdruck ist, wird der Text des Ausdrucks durch Entfernen des \@-Zeichens extrahiert. Falls Sie ein Zeichenfolgenliteral benötigen, das mit \@ beginnt, muss es wie folgt mit einem Escapezeichen versehen werden: \@\@. Die folgenden Beispiele veranschaulichen die Auswertung von Ausdrücken.  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"parameters"|Die Zeichenfolge „parameters“ wird zurückgegeben.|  
|"parameters[1]"|Die Zeichenfolge „parameters[1]“ wird zurückgegeben.|  
|"\@\@"|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (ein Zeichen).|  
|" \@"|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (zwei Zeichen).|  
  
 Mit der *Zeichenfolgeninterpolation* können Ausdrücke auch innerhalb von Zeichenfolgen verwendet werden. Dabei werden die Ausdrücke in `@{ ... }` eingeschlossen. Beispiel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Bei der Verwendung einer Zeichenfolgeninterpolation ist das Ergebnis immer eine Zeichenfolge. Angenommen, ich habe `myNumber` als `42` und `myString` als `foo` definiert:  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@{pipeline().parameters.myString}"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@pipeline().parameters.myNumber"| Gibt `42` als *Zahl* zurück.|  
|"\@{pipeline().parameters.myNumber}"| Gibt `42` als *Zeichenfolge* zurück.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: @{pipeline().parameters.myNumber}` zurück.|  

## <a name="examples-of-using-parameters-in-expressions"></a>Beispiele für die Verwendung von Parametern in Ausdrücken 

### <a name="complex-expression-example"></a>Beispiel für komplexen Ausdruck
Das folgende Beispiel zeigt einen komplexen Ausdruck, der auf ein tiefes Unterfeld der Aktivitätsausgabe verweist. Wenn Sie auf einen Pipelineparameter verweisen möchten, der als ein Unterfeld ausgewertet wird, verwenden Sie die Syntax „[]“ statt des Punktoperators „(.)“ (wie bei „subfield1“ und „subfield2“).

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor für dynamische Inhalte

Der Editor für dynamische Inhalte versieht automatisch Zeichen in ihrem Inhalt mit Escapezeichen, wenn die Bearbeitung abgeschlossen ist. Der folgende Inhalt im Inhalts-Editor ist z. B. eine Zeichenfolgeninterpolation mit zwei Ausdrucksfunktionen. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Der Editor für dynamische Inhalte konvertiert oben Inhalt in einen Ausdruck `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"`. Das Ergebnis dieses Ausdrucks ist eine Zeichenfolge im JSON-Format, die unten gezeigt wird.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Ein Dataset mit Parametern

Im folgenden Beispiel nutzt „BlobDataset“ einen Parameter namens **path**. Der Wert wird unter Verwendung des folgenden Ausdrucks zum Festlegen eines Werts für die Eigenschaft **folderPath** verwendet: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Eine Pipeline mit Parametern

Im folgenden Beispiel nutzt die Pipeline die Parameter **inputPath** und **outputPath**. Der Parameter **path** für das parametrisierte Blobdataset wird mithilfe der Werte dieser Parameter festgelegt. Die hier verwendete Syntax lautet `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Aufrufen von Funktionen innerhalb von Ausdrücken 

Sie können Funktionen innerhalb von Ausdrücken aufrufen. Die folgenden Abschnitte enthalten Informationen zu den Funktionen, die in einem Ausdruck verwendet werden können.  

### <a name="string-functions"></a>Zeichenfolgenfunktionen  

Für die Arbeit mit Zeichenfolgen können Sie folgende Zeichenfolgenfunktionen und auch einige [Sammlungsfunktionen](#collection-functions) verwenden.
Zeichenfolgenfunktionen können nur in Zeichenfolgen verwendet werden.

| Zeichenfolgenfunktion | Aufgabe |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Kombiniert mindestens zwei Zeichenfolgen miteinander und gibt die kombinierte Zeichenfolge zurück. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Überprüft, ob eine Zeichenfolge mit der angegebenen Teilzeichenfolge endet. |
| [guid](control-flow-expression-language-functions.md#guid) | Generiert einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als Zeichenfolge. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Gibt die Anfangsposition für eine Teilzeichenfolge zurück. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Gibt die Anfangsposition des letzten Vorkommens einer Teilzeichenfolge zurück. |
| [replace](control-flow-expression-language-functions.md#replace) | Ersetzt eine Teilzeichenfolge durch die angegebene Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |
| [split](control-flow-expression-language-functions.md#split) | Gibt ein Array mit Teilzeichenfolgen, die durch Trennzeichen getrennt sind, aus einer größeren Zeichenfolge basierend auf einem angegebenen Trennzeichen in der ursprünglichen Zeichenfolge zurück. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge beginnt. |
| [substring](control-flow-expression-language-functions.md#substring) | Gibt Zeichen aus einer Zeichenfolge zurück, beginnend mit der angegebenen Position. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Gibt eine Zeichenfolge in Kleinbuchstaben zurück. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Gibt eine Zeichenfolge in Großbuchstaben zurück. |
| [trim](control-flow-expression-language-functions.md#trim) | Entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |

### <a name="collection-functions"></a>Auflistungsfunktionen

Für die Arbeit mit Sammlungen (in der Regel Arrays, Zeichenfolgen und manchmal auch Bibliotheken) können Sie folgende Sammlungsfunktionen verwenden.

| Sammlungsfunktion | Aufgabe |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Überprüft, ob eine Sammlung ein bestimmtes Element enthält. |
| [empty](control-flow-expression-language-functions.md#empty) | Überprüft, ob eine Sammlung leer ist. |
| [first](control-flow-expression-language-functions.md#first) | Gibt das erste Element aus einer Sammlung zurück. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Gibt eine Sammlung zurück, die *nur* die gängigen Elemente aus den angegebenen Sammlungen enthält. |
| [join](control-flow-expression-language-functions.md#join) | Gibt eine Zeichenfolge zurück, die *alle* Elemente aus einem Array enthält, getrennt durch das angegebene Zeichen. |
| [last](control-flow-expression-language-functions.md#last) | Gibt das letzte Element aus einer Sammlung zurück. |
| [length](control-flow-expression-language-functions.md#length) | Gibt die Anzahl der Elemente in einer Zeichenfolge oder einem Array zurück. |
| [skip](control-flow-expression-language-functions.md#skip) | Entfernt Elemente vom Anfang einer Sammlung und gibt *alle anderen* Elemente zurück. |
| [take](control-flow-expression-language-functions.md#take) | Gibt Elemente vom Anfang einer Sammlung zurück. |
| [union](control-flow-expression-language-functions.md#union) | Gibt eine Sammlung zurück, die *sämtliche* Elemente aus den angegebenen Sammlungen enthält. | 

### <a name="logical-functions"></a>Logische Funktionen  

Diese Funktionen können innerhalb von Bedingungen zur Auswertung einer beliebigen Art von Logik verwendet werden.  
  
| Logische Vergleichsfunktion | Aufgabe |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Überprüft, ob für sämtliche Ausdrücke der Wert „TRUE“ festgelegt ist. |
| [equals](control-flow-expression-language-functions.md#equals) | Überprüft, ob beide Werte identisch sind. |
| [greater](control-flow-expression-language-functions.md#greater) | Überprüft, ob der erste Wert größer als der zweite ist. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Überprüft, ob der erste Wert größer als oder gleich dem zweiten ist. |
| [if](control-flow-expression-language-functions.md#if) | Überprüft, ob ein Ausdruck gleich „true“ oder „false“ ist. Gibt abhängig vom Ergebnis einen angegebenen Wert zurück. |
| [less](control-flow-expression-language-functions.md#less) | Überprüft, ob der erste Wert kleiner als der zweite ist. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Überprüft, ob der erste Wert kleiner als oder gleich dem zweiten ist. |
| [not](control-flow-expression-language-functions.md#not) | Überprüft, ob ein Ausdruck gleich „false“ ist. |
| [or](control-flow-expression-language-functions.md#or) | Überprüft, ob mindestens ein Ausdruck gleich „true“ ist. |
  
### <a name="conversion-functions"></a>Konvertierungsfunktionen  

 Diese Funktionen dienen zum Konvertieren zwischen den einzelnen nativen Typen in der Sprache:  
-   Zeichenfolge
-   integer
-   float
-   boolean
-   Arrays
-   Wörterbücher

| Konvertierungsfunktion | Aufgabe |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Gibt ein Array aus einer einzelnen angegebenen Eingabe zurück. Für mehrere Eingaben siehe [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Gibt die base64-codierte Version für eine Zeichenfolge zurück. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Gibt die Binärversion für eine base64-codierte Zeichenfolge zurück. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [binary](control-flow-expression-language-functions.md#binary) | Gibt die Binärversion für einen Eingabewert zurück. |
| [bool](control-flow-expression-language-functions.md#bool) | Gibt die Version des booleschen Werts für einen Eingabewert zurück. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Gibt den ersten Wert ungleich NULL aus mindestens einem Parameter zurück. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Gibt ein Array aus mehreren Eingaben zurück. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Gibt den Daten-URI für einen Eingabewert zurück. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Gibt die Binärversion für einen Daten-URI zurück. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Gibt die Zeichenfolgenversion für einen Daten-URI zurück. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Gibt die Binärversion für einen Daten-URI zurück. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Gibt eine Zeichenfolge zurück, in der Escapezeichen durch decodierte Versionen ersetzt sind. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Gibt eine Zeichenfolge zurück, die URL-unsichere Zeichen durch Escapezeichen ersetzt. |
| [float](control-flow-expression-language-functions.md#float) | Gibt eine Gleitkommazahl für einen Eingabewert zurück. |
| [int](control-flow-expression-language-functions.md#int) | Gibt die Ganzzahlversion für eine Zeichenfolge zurück. |
| [json](control-flow-expression-language-functions.md#json) | Gibt den JSON-Typwert oder das JSON-Objekt (JSON = JavaScript Object Notation) für eine Zeichenfolge oder XML zurück. |
| [string](control-flow-expression-language-functions.md#string) | Gibt die Zeichenfolgenversion für einen Eingabewert zurück. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Gibt die URI-codierte Version für einen Eingabewert zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Gibt die Binärversion für eine URI-codierte Zeichenfolge zurück. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Gibt die Zeichenfolgenversion für eine URI-codierte Zeichenfolge zurück. |
| [xml](control-flow-expression-language-functions.md#xml) | Gibt die XML-Version für eine Zeichenfolge zurück. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Überprüft die XML auf Knoten oder Werte, die mit einem XPath-Ausdruck (XML Path Language) übereinstimmen, und gibt die übereinstimmenden Knoten oder Werte zurück. |

### <a name="math-functions"></a>Mathematische Funktionen  
 Diese Funktionen können für folgende Arten von Zahlen verwendet werden: **ganze Zahlen** und **Gleitkommazahlen**.  

| Mathematische Funktion | Aufgabe |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Gibt das Ergebnis der Addition zweier Zahlen zurück. |
| [div](control-flow-expression-language-functions.md#div) | Gibt das Ergebnis der Division zweier Zahlen zurück. |
| [max](control-flow-expression-language-functions.md#max) | Gibt den höchsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [min](control-flow-expression-language-functions.md#min) | Gibt den niedrigsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [mod](control-flow-expression-language-functions.md#mod) | Gibt den Restwert aus der Division zweier Zahlen zurück. |
| [mul](control-flow-expression-language-functions.md#mul) | Gibt das Produkt aus der Multiplikation zweier Zahlen zurück. |
| [rand](control-flow-expression-language-functions.md#rand) | Gibt eine beliebige ganze Zahl aus einem angegebenen Bereich zurück. |
| [range](control-flow-expression-language-functions.md#range) | Gibt ein Array mit ganzen Zahlen zurück, das mit einer angegebenen ganzen Zahl beginnt. |
| [sub](control-flow-expression-language-functions.md#sub) | Gibt das Ergebnis aus der Subtraktion der zweiten Zahl von der ersten Zahl zurück. |
  
### <a name="date-functions"></a>Datumsfunktionen  

| Datums- oder Zeitfunktion | Aufgabe |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Fügt eine Anzahl von Tagen zu einem Zeitstempel hinzu. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Fügt eine Anzahl von Stunden zu einem Zeitstempel hinzu. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Fügt eine Anzahl von Minuten zu einem Zeitstempel hinzu. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Fügt eine Anzahl von Sekunden zu einem Zeitstempel hinzu. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Fügt eine Anzahl von Zeiteinheiten zu einem Zeitstempel hinzu. Siehe auch [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Konvertiert einen Zeitstempel von der UTC-Zeitzone (UTC = Universal Time, Coordinated) in die Zielzeitzone. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Konvertiert einen Zeitstempel von der Quellzeitzone in die Zielzeitzone. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konvertiert einen Zeitstempel von der Quellzeitzone in die UTC-Zeitzone (UTC = Universal Time, Coordinated). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Gibt den Tag der Monatskomponente eines Zeitstempels zurück. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Gibt den Tag der Wochenkomponente eines Zeitstempels zurück. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Gibt den Tag der Jahreskomponente eines Zeitstempels zurück. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Gibt den Zeitstempel als Zeichenfolge in optionalem Format zurück. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück. Siehe auch [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Gibt den aktuellen Zeitstempel abzüglich der angegebenen Zeiteinheiten zurück. Siehe auch [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Gibt den Beginn des Tages für einen Zeitstempel zurück. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Gibt den Beginn der Stunde für einen Zeitstempel zurück. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Gibt den Beginn des Monats für einen Zeitstempel zurück. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtrahiert eine Anzahl von Zeiteinheiten von einem Zeitstempel. Siehe auch [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Gibt den Eigenschaftswert `ticks` für einen angegebenen Zeitstempel zurück. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Gibt den aktuellen Zeitstempel als Zeichenfolge zurück. |

## <a name="detailed-examples-for-practice"></a>Ausführliche Beispiele für die Praxis

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Azure Data Factory-Kopierpipeline mit Parametern im Detail 

In diesem [Tutorial zur Übergabe von Azure Data Factory-Kopierpipeline-Parametern](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) erfahren Sie, wie Sie Parameter zwischen einer Pipeline und einer Aktivität sowie zwischen den Aktivitäten übergeben können.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Zuordnungsdatenfluss-Pipeline mit Parametern im Detail 

Ein ausführliches Beispiel zum Verwenden von Parametern im Datenfluss finden Sie unter [Parametrisieren von Zuordnungsdatenflüssen](./parameters-data-flow.md).

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Metadatengesteuerte Pipeline mit Parametern im Detail

Unter [Verweisen auf Triggermetadaten in Pipelineausführungen](./how-to-use-trigger-parameterization.md) finden Sie weitere Informationen zur Verwendung von Parametern zum Entwerfen metadatengesteuerter Pipelines. Dies ist ein beliebter Anwendungsfall für Parameter.


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Systemvariablen, die Sie in Ausdrücken verwenden können, finden Sie unter [Systemvariablen](control-flow-system-variables.md).
