---
title: '„Split Data“: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die „Split Data“-Komponente (Daten aufteilen) in Azure Machine Learning verwenden, um ein Dataset in zwei verschiedene Sätze aufzuteilen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d7d11ea059128c8d2623ae3b9613acb57c97e1c1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549506"
---
# <a name="split-data-component"></a>„Split Data“-Komponente

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie die „Split Data“-Komponente, um ein Dataset in zwei unterschiedliche Sätze aufzuteilen.

Diese Komponente ist hilfreich, wenn Sie Daten in Trainings- und Testsätze untergliedern müssen. Sie können außerdem anpassen, wie die Daten aufgeteilt werden sollen. Einige Optionen unterstützen die zufällige Anordnung von Daten. Andere sind auf einen bestimmten Daten- oder Modelltyp zugeschnitten.

## <a name="configure-the-component"></a>Konfigurieren der Komponente

> [!TIP]
> Bevor Sie den Aufteilungsmodus auswählen, informieren Sie sich über alle Optionen, um den benötigten Aufteilungstyp zu ermitteln.
> Wenn Sie den Aufteilungsmodus ändern, werden möglicherweise alle anderen Optionen zurückgesetzt.

1. Fügen Sie Ihrer Pipeline im Designer die **Split Data**-Komponente hinzu. Sie finden diese Komponente unter **Datentransformation** in der Kategorie **Sample and Split**.

1. **Aufteilungsmodus**: Wählen Sie je nach Datentyp und gewünschter Aufteilung einen der folgenden Modi aus. Jeder Aufteilungsmodus verfügt über unterschiedliche Optionen.

   - **Zeilen aufteilen**: Verwenden Sie diese Option, wenn Sie Daten einfach in zwei Teile aufteilen möchten. Sie können angeben, wie viel Prozent der Daten jeweils in einem Teil enthalten sein sollen. Die Standardaufteilung der Daten ist 50:50.

     Sie können Zeilen für die einzelnen Gruppen auch nach dem Zufallsprinzip auswählen und geschichtete Stichproben verwenden. Bei geschichteten Stichproben müssen Sie eine einzelne Datenspalte auswählen, für die Werte gleichmäßig auf zwei Ergebnisdatasets aufgeteilt werden sollen.  

   - **Regular Expression Split** (Nach regulärem Ausdruck aufteilen): Wählen Sie diese Option, wenn Sie Ihr Dataset danach aufteilen möchten, ob ein Wert in einer einzelnen Spalte enthalten ist.

     Bei Stimmungsanalysen können Sie beispielsweise überprüfen, ob ein bestimmter Produktname in einem Textfeld enthalten ist. Anschließend können Sie das Dataset in Zeilen aufteilen, die den gewünschten Produktnamen enthalten, und Zeilen, die den gewünschten Produktnamen nicht enthalten.

   - **Relative Expression Split** (Relative Ausdrucksteilung):Verwenden Sie diese Option, wenn Sie eine Bedingung auf eine Ziffernspalte anwenden möchten. Die Ziffer kann für ein Datums-/Uhrzeitfeld, eine Spalte mit Alters- oder Währungsangaben oder sogar für einen Prozentsatz stehen. Beispielsweise ist es möglich, das Dataset basierend auf den Kosten der einzelnen Posten zu unterteilen, Personen nach Alter zu gruppieren oder Daten nach einem Kalenderdatum aufzuteilen.

### <a name="split-rows"></a>Aufteilen von Zeilen

1. Fügen Sie Ihrer Pipeline im Designer die [Split Data](./split-data.md)-Komponente hinzu, und verbinden Sie das Dataset, das Sie aufteilen möchten.
  
1. Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Split rows** (Zeilen aufteilen) aus. 

1. **Fraction of rows in the first output dataset** (Anteil der Zeilen im ersten Ausgabedataset): Ermitteln Sie anhand dieser Option, wie viele Zeilen der ersten (linken) Ausgabe zugeordnet werden. Alle weiteren Zeilen werden der zweiten (rechten) Ausgabe zugewiesen.

   Das Verhältnis stellt den Prozentsatz der Zeilen dar, die an das erste Ausgabedataset gesendet werden, sodass Sie eine Dezimalzahl zwischen 0 und 1 eingeben müssen.
     
   Wenn Sie z. B. **0,75** als Wert eingeben, wird das Dataset im Verhältnis 75:25 unterteilt. Bei dieser Aufteilung werden 75 Prozent der Zeilen an das erste Ausgabedataset gesendet. Die übrigen 25 Prozent werden an das zweite Ausgabedataset gesendet.
  
1. Wählen Sie die Option **Randomized split** (Zufällige Aufteilung), wenn Daten den beiden Gruppen nach dem Zufallsprinzip zugewiesen werden sollen. Dies ist die bevorzugte Option bei der Erstellung von Trainings- und Testdatasets.

1. **Zufälliger Seed**: Dieser Parameter wird ignoriert, wenn **Randomized split** auf false festgelegt ist. Andernfalls geben Sie einen nicht-negativen ganzzahligen Wert ein, um die Pseudo-Zufallsfolge der zu verwendenden Instanzen zu starten. Dieser Standardstartwert wird in allen Komponenten verwendet, die Zufallszahlen generieren. 

   Die Angabe eines Startwerts führt dazu, dass die Ergebnisse reproduzierbar sind. Wenn Sie die Ergebnisse einer Split-Operation wiederholen müssen, sollten Sie die gleiche Seed-Nummer für den Zufallszahlengenerator angeben. 

1. **Stratified split** (Geschichtete Aufteilung): Legen Sie diese Option auf **True** fest, um sicherzustellen, dass die beiden Ausgabedatasets eine repräsentative Stichprobe der Werte aus der *Schichtspalte* oder *Schichtungsschlüsselspalte* enthalten. 

   Bei geschichteten Stichproben werden die Daten unterteilt, sodass jedes Ausgabedataset annähernd den gleichen Prozentsatz der einzelnen Zielwerte aufweist. Sie können z. B. sicherstellen, dass Ihre Trainings- und Testsätze im Hinblick auf das Ergebnis oder eine andere Spalte (z. B. Geschlecht) ungefähr ausgeglichen sind.

1. Übermitteln Sie die Pipeline.


## <a name="select-a-regular-expression"></a>Auswählen eines regulären Ausdrucks

1. Fügen Sie Ihrer Pipeline die [Split Data](./split-data.md)-Komponente hinzu, und verbinden Sie sie als Eingabe mit dem Dataset, das Sie aufteilen möchten.  
  
1. Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Regular expression split** (Nach regulärem Ausdruck aufteilen) aus.

1. Geben Sie im Feld **Regular expression** (Regulärer Ausdruck) einen gültigen, regulären Ausdruck ein. 
  
   Der reguläre Ausdruck sollte der Python-Syntax für reguläre Ausdrücke folgen.

1. Übermitteln Sie die Pipeline.

   Basierend auf dem angegebenen regulären Ausdruck wird das Dataset in zwei Sätze von Zeilen unterteilt: Zeilen mit Werten, die mit dem Ausdruck übereinstimmen, und alle übrigen Zeilen. 

Die folgenden Beispiele zeigen, wie ein Dataset mithilfe der Option **Regulärer Ausdruck** geteilt werden kann. 

### <a name="single-whole-word"></a>Einzelnes ganzes Wort 

In diesem Beispiel werden in das erste Dataset alle Zeilen eingefügt, die den Text `Gryphon` in der Spalte `Text` enthalten. Die anderen Zeilen werden in die zweite Ausgabe von **Split Data** (Daten aufteilen) eingefügt.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>TEILZEICHENFOLGE

In diesem Beispiel wird nach der angegebenen Zeichenfolge an einer beliebigen Position innerhalb der zweiten Spalte des Datasets gesucht. Die Position ist hier durch den Indexwert 1 gekennzeichnet. Bei der Übereinstimmung muss die Groß-/Kleinschreibung beachtet werden.

```text
(\1) ^[a-f]
```

Das erste Ergebnisdataset enthält jede Zeile, in der die Indexspalte mit einem der folgenden Buchstaben beginnt: `a`, `b`, `c`, `d`, `e`, `f`. Alle anderen Zeilen werden zur zweiten Ausgabe weitergeleitet.

## <a name="select-a-relative-expression"></a>Auswählen eines relativen Ausdrucks

1. Fügen Sie Ihrer Pipeline die [Split Data](./split-data.md)-Komponente hinzu, und verbinden Sie sie als Eingabe mit dem Dataset, das Sie aufteilen möchten.
  
1. Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Relative Expression** (Relativer Ausdruck) aus.
  
1. Geben Sie im Feld **Relational expression** (Relationaler Ausdruck) einen Ausdruck ein, durch den ein Vergleichsvorgang für eine einzelne Spalte durchgeführt wird.

   Für die **numerische Spalte**:
   - Die Spalte enthält Ziffern jedes numerischen Datentyps einschließlich der Datums- und Uhrzeitdatentypen.
   - Im Ausdruck kann auf maximal einen Spaltennamen verwiesen werden.
   - Verwenden Sie für die UND-Verknüpfung das kaufmännische Und-Zeichen `&`. Verwenden Sie für die ODER-Verknüpfung den senkrechten Strich `|`.
   - Folgende Operatoren werden unterstützt: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - Vorgänge können nicht mithilfe von `(` und `)` gruppiert werden.
   
   Für die **Zeichenfolgenspalte**:
   - Folgende Operatoren werden unterstützt: `==`, `!=`.

1. Übermitteln Sie die Pipeline.

   Durch den Ausdruck wird das Dataset in zwei Sätzen von Zeilen unterteilt: Zeilen mit Werten, die die Bedingung erfüllen, und alle übrigen Zeilen.

In den folgenden Beispielen wird gezeigt, wie Sie ein Dataset mithilfe der Option **Relative Expression** (Relativer Ausdruck) in der **Split Data**-Komponente aufteilen können.  

### <a name="calendar-year"></a>Kalenderjahr

Ein gängiges Szenario ist das Aufteilen eines Datasets nach Jahren. Der folgende Ausdruck wählt alle Zeilen aus, in denen die Werte in der Spalte `Year` größer sind als `2010`.

```text
\"Year" > 2010
```

Der Datumsausdruck muss alle Datumskomponenten berücksichtigen, die in der Datenspalte enthalten sind. Das Format der Datumsangaben in der Datenspalte muss konsistent sein. 

In einer Datumsspalte mit dem Format `mmddyyyy` sollte der Ausdruck z. B. etwa wie folgt lauten:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Spaltenindex

Der folgende Ausdruck veranschaulicht die Verwendung des Spaltenindexes, um alle Zeilen in der ersten Spalte des Datasets auszuwählen, die Werte kleiner gleich 30, jedoch ungleich 20 enthalten.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 
