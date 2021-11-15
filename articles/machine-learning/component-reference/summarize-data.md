---
title: Zusammenfassen von Daten
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Summarize Data“ (Zusammenfassen von Daten) in Azure Machine Learning verwenden, um einen grundlegenden deskriptiven Statistikbericht für die Spalten in einem Dataset zu generieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 459804fb7c3a962b7d3ee29928984f435f0dea06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566528"
---
# <a name="summarize-data"></a>Zusammenfassen von Daten

Dieser Artikel beschreibt eine Komponente von Azure Machine Learning Designer.

Verwenden Sie die Komponente „Summarize Data“ (Zusammenfassen von Daten), um eine Menge von statistischen Normwerten zu erstellen, die jede Spalte in der Eingabetabelle beschreiben.

Aggregierende Parameter (Zusammenfassungsstatistiken) sind nützlich, wenn Sie die Merkmale eines gesamten Datasets verstehen möchten. Beispielsweise könnte es sein, dass Sie Folgendes wissen möchten:

- Wie viele Werte fehlen in jeder Spalte?
- Wie viele eindeutige Werte sind in einer Featurespalte vorhanden?
- Was groß ist die mittlere und die Standardabweichung für jede Spalte?

Die Komponente berechnet die wichtigen Scores für jede Spalte und gibt eine Zeile mit Zusammenfassungsstatistiken für jede Variable (Datenspalte) zurück, die als Eingabe bereitgestellt wird.

## <a name="how-to-configure-summarize-data"></a>Konfigurieren von „Summarize Data“  

1. Fügen Sie die Komponente **Summarize Data** Ihrer Pipeline hinzu. Sie finden diese Komponente in der Kategorie **Statistical Functions** (Statistische Funktionen) im Designer.

1. Stellen Sie eine Verbindung mit dem Dataset her, für das Sie einen Bericht generieren möchten.

    Möchten Sie den Bericht nur für einige Spalten erstellen, verwenden Sie die Komponente [Select Columns in Dataset](select-columns-in-dataset.md) (Auswählen von Spalten in Dataset), um die Teilmenge der Spalten zu projizieren, mit denen Sie arbeiten möchten.

1. Es sind keine weiteren Parameter erforderlich. Standardmäßig analysiert die Komponente alle Spalten, die als Eingabe bereitgestellt werden, und abhängig vom Typ der Werte in den Spalten gibt das Modul einen relevanten Satz von statistischen Werten zurück, die im Abschnitt [Ergebnisse](#results) beschrieben sind.

1. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Der Bericht der Komponente kann die folgenden Statistiken enthalten. 

|Spaltenname|BESCHREIBUNG|
|------|------|  
|**Feature**|Name der Spalte|
|**Count**|Anzahl aller Zeilen|
|**Unique Value Count**|Anzahl der eindeutigen Werte in der Spalte|
|**Missing Value Count**|Anzahl der eindeutigen Werte in der Spalte|
|**Min**|Kleinster Wert in der Spalte|  
|**Max**|Größter Wert in der Spalte|
|**Mean**|Mittelwert aller Spaltenwerte|
|**Mean Deviation**|Mittlere Abweichung von den Spaltenwerten|
|**1st Quartile**|Wert beim ersten Quartil|
|**Median**|Medianwert der Spalte|
|**3rd Quartile**|Wert beim dritten Quartil|
|**Mode**|Mode der Spaltenwerte|
|**Bereich**|Ganze Zahl, die die Anzahl der Werte zwischen dem kleinsten und dem größten Wert darstellt|
|**Sample Variance**|Varianz für die Spalte; siehe Hinweis|
|**Sample Standard Deviation**|Standardabweichung für die Spalte; siehe Hinweis|
|**Sample Skewness**|Schiefe für die Spalte; siehe Hinweis|
|**Sample Kurtosis**|Wölbung für die Spalte; siehe Hinweis|
|**P0.5**|0,5-%-Perzentil|
|**P1**|1-%-Perzentil|
|**P5**|5-%-Perzentil|
|**P95**|95-%-Perzentil|
|**P99.5**|99,5-%-Perzentil |

## <a name="technical-notes"></a>Technische Hinweise

- Bei nicht numerischen Spalten werden nur die Werte für „Count“, „Unique value count“ und „Missing value count“ berechnet. Bei anderen statistischen Daten wird ein NULL-Wert zurückgegeben.

- Spalten mit booleschen Werten werden anhand der folgenden Regeln verarbeitet:

    - Bei der Berechnung von „Min“ wird ein logisches AND angewandt.
    
    - Bei der Berechnung von „Max“ wird ein logisches OR angewandt.
    
    - Bei der Berechnung von „Range“ (Bereich) überprüft die Komponente zuerst, ob die Anzahl der eindeutigen Werte in der Spalte dem Wert „2“ entspricht.
    
    - Beim Berechnen statistischer Daten, die eine Gleitkommaberechnung erfordern, werden Werte mit TRUE als „1,0“ und Werte mit FALSE als „0,0“ behandelt.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an.  
