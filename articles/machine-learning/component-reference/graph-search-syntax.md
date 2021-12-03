---
title: Graph-Suchabfragesyntax
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe der Suchabfragesyntax im Azure Machine Learning-Designer nach Knoten im Pipelinegraph suchen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566432"
---
# <a name="graph-search-query-syntax"></a>Graph-Suchabfragesyntax

In diesem Artikel erfahren Sie mehr über die Graph-Suchfunktion in Azure Machine Learning. 

Mit der Graph-Suche können Sie schnell durch einen Knoten navigieren, wenn Sie eine Pipeline debuggen oder erstellen. Sie können das Schlüsselwort oder die Abfrage entweder in das Eingabefeld auf der Symbolleiste oder im linken Bereich unter der Registerkarte für die Suche eingeben, um die Suche auszulösen. Alle übereinstimmenden Ergebnisse werden auf der Canvas gelb hervorgehoben. Wenn Sie im linken Bereich ein Ergebnis auswählen, wird der Knoten auf der Canvas rot hervorgehoben.

![Screenshot: Beispiel für die Graph-Suche](media/search/graph-search-0322.png)

Die Graph-Suche unterstützt Volltext-Schlüsselwortsuche nach Knotennamen und Kommentaren. Sie können auch nach Knoteneigenschaften wie „runStatus“, „duration“ oder „computeTarget“ filtern. Die Schlüsselwortsuche basiert auf Lucene-Abfragen. Eine vollständige Suchabfrage sieht wie folgt aus:  

**[[Lucene-Abfrage] | [Filterabfrage]]** 

Sie können die Lucene-Abfrage oder die Filterabfrage verwenden. Um beide Abfragen zu verwenden, verwenden Sie das Trennzeichen **|** . Die Syntax der Filterabfrage ist strenger als für Lucene-Abfragen. Wenn die Kundeneingabe mit beiden Abfragen analysiert werden kann, wird daher die Filterabfrage angewendet.

`data OR model | compute in {cpucluster}` dient beispielsweise dazu, nach Knoten zu suchen, deren Name oder Kommentar `data` oder `model` enthält und bei denen „compute“ auf „cpucluster“ festgelegt ist.
 

## <a name="lucene-query"></a>Lucene-Abfrage

Die Graph-Suche verwendet einfache Lucene-Abfragen als Volltextsuchsyntax für den Knoten „name“ und „comment“. Folgende Lucene-Operatoren werden unterstützt:

 
- UND/ODER
- Platzhalterübereinstimmung mit **?** und **\*** -Operatoren.

### <a name="examples"></a>Beispiele

- Einfache Suche: `JSON Data`

- AND/OR: `JSON AND Validation`

- Mehrere AND-/OR-Operatoren: `(JSON AND Validation) OR (TSV AND Training)`

 
- Platzhalterübereinstimmung: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Eine Lucene-Abfrage darf nicht mit dem Zeichen „*“ beginnen.

##  <a name="filter-query"></a>Filterabfrage

 
Filterabfragen verwenden das folgende Muster:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Die folgenden Knoteneigenschaften können als Schlüssel verwendet werden:

- runStatus
- compute
- duration
- reuse
- Veröffentlichen
- tags

Folgende Operatoren können verwendet werden:

- Größer als oder gleich: `>=`
- Kleiner als oder gleich: `<=`
- Größer als: `>`
- Kleiner als: `<`
- Gleich: `==`
- Enthält: `=`
- NotEqual: `!=`
- In: `in`

 
 

### <a name="example"></a>Beispiel

- duration > 100;
- status in {Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>Technische Hinweise

- Die Beziehung zwischen mehreren Filtern ist „AND“.
- Wenn `>=,  >,  <, or  <=` ausgewählt wird, werden Werte automatisch in den Typ „number“ konvertiert. Andernfalls werden Zeichenfolgentypen für Vergleiche verwendet.
- Bei allen Werten vom Zeichenfolgentyp wird Groß-/Kleinschreibung in Vergleichen nicht berücksichtigt.
- Der Operator „In“ erwartet eine Sammlung als Wert, die Sammlungssyntax ist `{name1, name2, name3}`.
- Leerzeichen zwischen Schlüsselwörtern werden ignoriert.