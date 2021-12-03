---
title: 'Daten verbinden: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Join Data im Azure Machine Learning Designer verwenden, um zwei Datensätze zusammenzuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: a19ae7de4355e5b4d2d8ce4379ef3359edd55f07
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566632"
---
# <a name="join-data"></a>Verknüpfen von Daten

Dieser Artikel beschreibt, wie man die  **Join Data**-Komponente im Azure Machine Learning Designer verwendet wird, um zwei Datensätze mithilfe einer datenbankähnlichen Verknüpfungsoperation zusammenzuführen.  

## <a name="how-to-configure-join-data"></a>Konfigurieren von „Join Data“ (Daten verknüpfen)

Wenn Sie einen Join-Vorgang für zwei Datasets ausführen möchten, müssen die beiden über eine Schlüsselspalte miteinander verknüpft sein. Zusammengesetzte Schlüssel mit mehreren Spalten werden ebenfalls unterstützt. 

1. Fügen Sie die zu kombinierenden Datensätze hinzu, und ziehen Sie dann die Komponente **Join Data** in Ihre Pipeline. 

    Sie finden die Komponente in der Kategorie **Datentransformation**, unter **Manipulation**.

1. Verbinden Sie die Datensätze mit der Komponente **Join Data**. 
 
1. Wählen Sie **Launch column selector** (Spaltenauswahl starten) aus, um mindestens eine Schlüsselspalte auszuwählen. Zur Erinnerung: Wählen Sie sowohl Spalten für die linke Eingabe als auch Spalten für die rechte Eingabe aus.

    Einzelner Schlüssel:

    Wählen Sie eine einzelne Schlüsselspalte für beide Eingaben aus.
    
    Zusammengesetzter Schlüssel:

    Wählen Sie alle Schlüsselspalten aus der linken und rechten Eingabe in der gleichen Reihenfolge aus. Die Komponente **Join Data** verbindet die Tabellen, wenn alle Schlüsselspalten übereinstimmen. Aktivieren Sie die Option **Allow duplicates and preserve column order in selection** (Duplikate zulassen und Spaltenreihenfolge in der Auswahl beibehalten), wenn die Spaltenreihenfolge von der Originaltabelle abweicht. 

    ![Spaltenauswahl](media/module/join-data-column-selector.png)


1. Wählen Sie die Option **Groß-/Kleinschreibung beachten** aus, wenn die Beachtung der Groß-/Kleinschreibung bei einem Join-Vorgang für eine Textspalte erhalten bleiben soll. 
   
1. Geben Sie über die Dropdownliste **Verknüpfungstyp** an, wie die Datasets vereint werden sollen.  
  
    * **Innerer Join:** Ein *innerer Join* ist der gängigste Join-Vorgang. Bei diesem Typ werden die vereinten Zeilen nur zurückgegeben, wenn die Werte der Schlüsselspalten übereinstimmen.  
  
    * **Linker äußerer Join:** Bei einem *linken äußeren Join* werden verknüpfte Zeilen für alle Zeilen aus der linken Tabelle zurückgegeben. Wenn für eine Zeile in der linken Tabelle keine übereinstimmenden Zeilen in der rechten Tabelle vorhanden sind, weist die zurückgegebene Zeile für alle Spalten, die aus der rechten Tabelle stammen, fehlende Werte auf. Für fehlende Werte kann auch ein Ersatzwert angegeben werden.  
  
    * **Vollständiger äußerer Join:** Bei einem *vollständigen äußeren Join* werden alle Zeilen aus der linken Tabelle (**Tabelle 1**) und aus der rechten Tabelle (**Tabelle 2**) zurückgegeben.  
  
         Für jede Zeile aus einer Tabelle, für die keine übereinstimmenden Zeilen in der jeweils anderen Tabelle vorhanden sind, enthält das Ergebnis eine Zeile mit fehlenden Werten.  
  
    * **Linke halbe Verknüpfung:** Bei einer *linken halben Verknüpfung* werden nur die Werte aus der linken Tabelle zurückgegeben, wenn die Werte der Schlüsselspalten übereinstimmen.  

1. Für die Option **Keep right key columns in joined table** (Rechte Schlüsselspalten in verknüpfter Tabelle beibehalten) gilt Folgendes:

    * Aktivieren Sie diese Option, wenn die Schlüssel aus beiden Eingabetabellen angezeigt werden sollen.
    * Deaktivieren Sie diese Option, wenn nur die Schlüsselspalten aus der linken Eingabe zurückgegeben werden sollen.

1. Übermitteln Sie die Pipeline.

1. Um die Ergebnisse anzuzeigen, klicken Sie mit der rechten Maustaste auf **Daten verknüpfen**, und wählen Sie **Visualisieren** aus.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 