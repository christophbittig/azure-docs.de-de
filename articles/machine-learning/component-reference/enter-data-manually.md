---
title: 'Daten manuell eingeben: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Manuelle Dateneingabe in Azure Machine Learning verwenden, um einen kleinen Datensatz durch Eingabe von Werten zu erstellen. Das Dataset kann mehrere Spalten enthalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 836b43a26f89856f2a3c122c5f5ab1f81d0680db
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566683"
---
# <a name="enter-data-manually-component"></a>Komponente Manuelle Dateneingabe

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie die Komponente **Manuelle Dateneingabe**, um einen kleinen Datensatz durch Eingabe von Werten zu erstellen. Das Dataset kann mehrere Spalten enthalten.
  
Diese Komponente kann in Szenarien wie diesen hilfreich sein:  
  
- Generieren einer kleinen Menge von Werten für Tests  
- Erstellen einer Kurzliste mit Bezeichnungen  
- Eingeben einer Liste mit Spaltennamen zum Einfügen in ein Dataset

## <a name="create-a-dataset"></a>Erstellen eines Datasets 
  
1. Fügen Sie die Komponente [Manuelle Dateneingabe](./enter-data-manually.md) zu Ihrer Pipeline hinzu. Sie finden diese Komponente in der Kategorie **Dateneingabe und -ausgabe** in Azure Machine Learning. 
  
1. Wählen Sie für **DataFormat** eine der folgenden Optionen aus. Diese Optionen bestimmen, wie die von Ihnen bereitgestellten Daten analysiert werden sollen. Die Anforderungen für jedes Format sind sehr unterschiedlich, weshalb Sie die entsprechenden Themen unbedingt lesen sollten.  
  
   - **ARFF**: Das von Weka verwendete Dateiformat mit Attributbeziehungen (Attribute Relation File Format)   
   - **CSV**: Format mit durch Trennzeichen getrennten Werten. Weitere Informationen finden Sie unter [Convert to CSV](./convert-to-csv.md) (Konvertieren in das CSV-Format).    
   - **SVMLight**: Ein Format, das von Vowpal Wabbit und anderen Frameworks für maschinelles Lernen verwendet wird.    
   - **TSV**: Format mit durch Tabulator getrennten Werten.

   Wenn Sie ein Format wählen und keine Daten bereitstellen, die den Formatvorgaben entsprechen, tritt ein Laufzeitfehler auf.
  
1. Klicken Sie in das Textfeld **Daten**, um mit der Dateneingabe zu beginnen. Die folgenden Formate erfordern besondere Aufmerksamkeit:  
  
   - **CSV**: Um mehrere Spalten zu erstellen, fügen Sie Text mit Kommas als Trennzeichen ein, oder geben Sie mehrere Spalten mit Kommas zwischen den Feldern ein.
  
     Wenn Sie die Option **HasHeader** auswählen, können Sie die erste Zeile der Werte als Spaltenüberschrift verwenden.  
  
     Wenn Sie diese Option deaktivieren, werden die Spaltennamen (Col1, Col2 usw.) verwendet. Sie können Spaltennamen später mit der Option [Edit Metadata](./edit-metadata.md) (Metadaten bearbeiten) hinzufügen oder ändern.  
  
   - **TSV**: Um mehrere Spalten zu erstellen, fügen Sie Text mit Tabulatoren als Trennzeichen ein, oder geben Sie mehrere Spalten mit Tabulatoren zwischen den Feldern ein.  
  
     Wenn Sie die Option **HasHeader** auswählen, können Sie die erste Zeile der Werte als Spaltenüberschrift verwenden.  
  
     Wenn Sie diese Option deaktivieren, werden die Spaltennamen (Col1, Col2 usw.) verwendet. Sie können Spaltennamen später mit der Option [Edit Metadata](./edit-metadata.md) (Metadaten bearbeiten) hinzufügen oder ändern.  
  
   - **ARFF**: Fügen Sie eine vorhandene ARFF-Formatdatei ein. Wenn Sie Werte direkt eingeben, fügen Sie die optionalen Kopfzeilen- und erforderlichen Attributfelder am Anfang der Daten hinzu. 

     Beispielsweise können die folgenden Kopf- und Attributzeilen einer einfachen Liste hinzugefügt werden. Die Überschrift der Spalte lautet dann `SampleText`. Beachten Sie, dass der Zeichenfolgentyp nicht unterstützt wird.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data component  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Geben oder fügen Sie Werte im SVMLight-Format ein.  
  
     Das folgende Beispiel stellt beispielsweise die ersten Zeilen des Datasets „Blood Donation“ im SVMLight-Format dar:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Wenn Sie die Komponente [Daten manuell eingeben](./enter-data-manually.md) ausführen, werden diese Zeilen wie folgt in einen Datensatz aus Spalten und Indexwerten umgewandelt:  
  
     |Col1|Col2|Col3|Col4|Bezeichnungen|  
     |-|-|-|-|-|  
     |0,00016|0,004|0,999961|0,00784|1|  
     |0|0,004|0,999955|0,008615|1|  
  
1. Drücken Sie nach jeder Zeile die EINGABETASTE, um eine neue Zeile zu beginnen.      
     
   Wenn Sie mehrmals die EINGABETASTE drücken, um mehrere leere Zeilen am Ende hinzuzufügen, werden die leeren Zeilen entfernt oder abgeschnitten.  
  
   Wenn Sie Zeilen mit fehlenden Werten erstellen, können Sie diese später jederzeit wieder herausfiltern.  
  
1. Verbinden Sie den Ausgangsanschluss mit anderen Komponenten, und führen Sie die Pipeline aus.  
  
   Um den Datensatz zu betrachten, klicken Sie mit der rechten Maustaste auf die Komponente und wählen **Visualisieren**.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 