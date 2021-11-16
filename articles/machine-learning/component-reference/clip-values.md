---
title: Beschneiden von Werten
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mit der Komponente „Clip Values“ (Werte beschneiden) in Azure Machine Learning Ausreißer erkennen und ihre Werte beschneiden oder ersetzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: f93026b6b0cc0add6d2b30e13e2ba0c91740a647
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566657"
---
# <a name="clip-values"></a>Beschneiden von Werten

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie die „Clip Values“-Komponente, um Datenwerte zu identifizieren, die oberhalb oder unterhalb eines Schwellenwerts liegen, und diese optional durch einen Mittelwert, eine Konstante oder einen anderen Ersatzwert zu ersetzen.  

Sie verbinden die Komponente mit einem Dataset, das die zu beschneidenden Zahlen enthält, wählen die Spalten zum Bearbeiten aus und legen dann einen Schwellenwert oder einen Wertebereich sowie eine Ersetzungsmethode fest. Die Komponente kann entweder nur die Ergebnisse, oder die veränderten, an das ursprüngliche Dataset angefügten Werte ausgeben.

## <a name="how-to-configure-clip-values"></a>Konfigurieren von Clip Values

Ermitteln Sie zunächst die Spalten, die Sie beschneiden möchten, und die zu verwendende Methode. Es wird empfohlen, Beschneidungsmethoden zuerst an einer kleinen Teilmenge der Daten zu testen.

Die Komponente verwendet die gleichen Kriterien und die gleiche Ersetzungsmethode auf **alle** Spalten an, die Sie in die Auswahl einschließen. Achten Sie daher darauf, Spalten auszuschließen, die Sie nicht ändern möchten.

Wenn Sie auf einige Spalten Beschneidungsmethoden oder andere Kriterien anwenden möchten, müssen Sie für jede Gruppe ähnlicher Spalten eine neue Instanz von **Clip Values** verwenden.

1.  Fügen Sie Ihrer Pipeline die **Clip Values**-Komponente hinzu, und verbinden Sie sie mit dem Dataset, das Sie ändern möchten. Sie finden diese Komponente unter **Datentransformation** in der Kategorie **Scale and Reduce** (Skalieren und verringern). 
  
1.  Wählen Sie in der **Liste der Spalten** mithilfe der Spaltenauswahl die Spalten aus, auf die **Clip Values** angewandt werden soll.  
  
1.  Wählen Sie für **Set of thresholds** (Satz von Schwellenwerten) in der Dropdownliste eine der folgenden Optionen aus. Diese Optionen legen fest, wie die Ober- und Untergrenze für zulässige Werte bzw. Werte, die beschnitten werden müssen, festgelegt werden.  
  
    - **ClipPeaks:** Wenn Sie Werte anhand von Spitzenwerten abschneiden, geben Sie nur einen oberen Grenzwert ein. Werte, die größer als dieser Grenzwert sind, werden ersetzt.
  
    -  **ClipSubpeaks:** Wenn Sie Werte anhand von Unterspitzenwerten abschneiden, geben Sie nur einen unteren Grenzwert ein. Werte, die kleiner als dieser Grenzwert sind, werden ersetzt.  
  
    - **ClipPeaksAndSubpeaks:** Wenn Sie Werte anhand von Spitzen- und Unterspitzenwerten abschneiden, können Sie sowohl einen oberen als auch einen unteren Grenzwert angeben. Werte, die außerhalb dieses Bereichs liegen, werden ersetzt. Werte, die den Grenzwerten entsprechen, werden nicht geändert.
  
1.  Abhängig von Ihrer Auswahl im vorherigen Schritt können Sie die folgenden Schwellenwerte festlegen: 

    + **Unterer Schwellenwert:** wird nur angezeigt, wenn Sie **ClipSubPeaks** auswählen.
    + **Oberer Schwellenwert:** wird nur angezeigt, wenn Sie **ClipPeaks** auswählen.
    + **Schwellenwert:** wird nur angezeigt, wenn Sie **ClipPeaksAndSubPeaks** auswählen.

    Wählen Sie für jeden Schwellenwert **Konstante** oder **Perzentil** aus.

1. Wenn Sie **Konstante** auswählen, geben Sie den maximalen oder minimalen Wert in das Textfeld ein. Beispiel: Sie wissen, dass der Wert 999 als Platzhalterwert verwendet wurde. Sie können **Konstante** für den oberen Schwellenwert auswählen und unter **Constant value for upper threshold** (Konstanter Wert für oberen Schwellenwert) den Wert „999“ eingeben.
  
1. Wenn Sie **Perzentil** auswählen, schränken Sie die Spaltenwerte auf einen Perzentilbereich ein. 

    Beispiel: Sie möchten nur die Werte im Perzentilbereich von 10–80 behalten und alle anderen ersetzen. Wählen Sie **Perzentil** aus, und geben Sie dann „10“ unter **Percentile value for lower threshold** (Perzentilwert für unteren Schwellenwert) und „80“ unter **Percentile value for upper threshold** (Perzentilwert für oberen Schwellenwert) ein. 

    Einige Beispiele zur Verwendung von Perzentilbereichen finden Sie im Abschnitt zu [Perzentilen](#examples-for-clipping-using-percentiles).  
  
1.  Definieren Sie einen Ersatzwert.

    Zahlen, die genau mit den angegebenen Grenzwerten übereinstimmen, werden als innerhalb des zulässigen Wertebereichs angesehen und daher nicht ersetzt. Alle Zahlen, die außerhalb des angegebenen Bereichs liegen, werden durch den Ersatzwert ersetzt. 
  
    + **Ersatzwert für Spitzen:** definiert den Ersatzwert für alle Spaltenwerte, die über dem angegebenen Schwellenwert liegen.  
    + **Ersatzwert für Unterspitzen:** definiert den Ersatzwert für alle Spaltenwerte, die unter dem angegebenen Schwellenwert liegen.  
    + Wenn Sie die Option **ClipPeaksAndSubpeaks** verwenden, können Sie separate Ersatzwerte für die oberen und unteren abgeschnittenen Werte angeben.  

    Die folgenden Ersatzwerte werden unterstützt:  
  
    -   **Schwellenwert:** ersetzt abgeschnittene Werte durch den angegebenen Schwellenwert.  
  
    -   **Mittelwert:** ersetzt abgeschnittene Werte durch den Mittelwert der Spaltenwerte. Der Mittelwert wird berechnet, bevor Werte abgeschnitten werden.  
  
    -   **Median:** ersetzt abgeschnittene Werte durch den Median der Spaltenwerte. Der Median wird berechnet, bevor Werte abgeschnitten werden.   
  
    -   **Fehlend:** Ersetzt beschnittene Werte durch einen fehlenden (leeren) Wert.  
  
1.  **Indikatorspalten hinzufügen:** Wählen Sie diese Option aus, wenn Sie eine neue Spalte generieren möchten, die Aufschluss darüber gibt, ob der angegebene Abschneidungsvorgang auf die Daten in dieser Zeile angewandt wurde. Diese Option ist nützlich, wenn Sie einen neuen Satz von Beschneidungs- und Ersetzungswerten testen.  
  
1. **Flag für Überschreiben:** Geben Sie an, wie die neuen Werte generiert werden sollen. Standardmäßig erstellt **Clip Values** eine neue Spalte mit den am gewünschten Schwellenwert abgeschnittenen Spitzenwerten. Neue Werte überschreiben die ursprüngliche Spalte.  
  
    Deaktivieren Sie diese Option, um die ursprüngliche Spalte beizubehalten und eine neue Spalte mit den beschnittenen Werten hinzuzufügen.  
  
1.  Übermitteln Sie die Pipeline.  
  
    Klicken Sie mit der rechten Maustaste auf die **Clip Values**-Komponente, und wählen Sie **Visualisieren** aus, oder wählen Sie die Komponente aus und wechseln Sie im rechten Bereich zur Registerkarte **Ausgaben**. Klicken Sie auf das Histogrammsymbol in den **Port outputs** (Portausgaben), um die Werte zu überprüfen und sicherzustellen, dass der Clippingvorgang Ihren Erwartungen entspricht.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Beispiele für das Beschneiden mithilfe von Perzentilen

Um zu verstehen, wie das Beschneiden nach Perzentilen funktioniert, stellen Sie sich ein Dataset mit 10 Zeilen vor, die jeweils eine Instanz der Werte 1 bis 10 aufweisen.  
  
- Wenn Sie ein Perzentil als oberen Schwellenwert verwenden, müssen bei dem Wert für das 90. Perzentil 90 % aller Werte im Dataset kleiner als dieser Wert sein.  
  
- Wenn Sie ein Perzentil als unteren Schwellenwert verwenden, müssen bei dem Wert für das 10. Perzentil 10 % aller Werte im Dataset kleiner als dieser Wert sein.  
  
1.  Wählen Sie für **Set of thresholds** die Option **ClipPeaksAndSubPeaks** aus.  
  
1.  Wählen Sie für **Upper threshold** die Option **Percentile** aus, und geben Sie für **Percentile number** den Wert 90 ein.  
  
1.  Wählen Sie für **Upper substitute value** die Option **Missing Value** aus.  
  
1.  Wählen Sie für **Lower threshold** die Option **Percentile** aus, und geben Sie für **Percentile number** den Wert 10 ein.  
  
1.  Wählen Sie für **Lower substitute value** die Option **Missing Value** aus.  
  
1.  Deaktivieren Sie die Option **Overwrite flag**, und wählen Sie die Option **Add indicator column** aus.  
  
Probieren Sie nun dieselbe Pipeline mit 60 als oberem Perzentilschwellenwert und 30 als unterem Perzentilschwellenwert aus, und verwenden Sie den Schwellenwert als Ersatzwert. In der folgenden Tabelle werden die beiden Ergebnisse verglichen:  
  
1.  Ersetzen durch fehlend; oberer Schwellenwert = 90; unterer Schwellenwert = 20  
  
1.  Ersetzen durch Schwellenwert; Oberer Perzentilwert = 60; unterer Perzentilwert = 40  
  
|Ursprüngliche Daten|Ersetzen durch fehlend|Ersetzen durch Schwellenwert|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 
