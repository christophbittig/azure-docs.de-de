---
title: 'Convert to CSV (Konvertieren in CSV): Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der Komponente „Convert to CSV“ (Konvertieren in CSV) im Azure Machine Learning-Designer ein Dataset in eine CSV-Datei konvertieren, die später wiederverwendet werden kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9f3d86c823305f5b39766d677b288c339cbf3d7b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566473"
---
# <a name="convert-to-csv-component"></a>Konvertieren in CSV-Komponente

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Nutzen Sie diese Komponente, um ein Dataset in ein CSV-Format zu konvertieren, das heruntergeladen, exportiert oder mit R- oder Python-Skriptkomponenten geteilt werden kann.

### <a name="more-about-the-csv-format"></a>Weitere Informationen zum CSV-Format 

Das CSV-Format (Comma-Separated Values) ist ein Dateiformat, das von vielen externen Tools für maschinelles Lernen verwendet wird. CSV ist ein gängiges Austauschformat bei der Arbeit mit Open-Source-Sprachen wie R oder Python.

Auch wenn Sie den größten Teil der Arbeit in Azure Machine Learning erledigen, gibt es Situationen, in denen es sinnvoll sein kann, Ihr Dataset in das CSV-Format zu konvertieren, um es in externen Tools zu verwenden. Beispiel:

+ Laden Sie die CSV-Datei herunter, um sie in Excel zu öffnen, oder importieren Sie sie in eine relationale Datenbank.  
+ Speichern Sie die CSV-Datei im Cloudspeicher, und verbinden Sie sie mit Power BI, um Visualisierungen zu erstellen.  
+ Verwenden Sie das CSV-Format, um Daten zur Verwendung in R und Python aufzubereiten. 

Wenn Sie einen Datensatz in das CSV-Format konvertieren, wird die CSV-Datei in Ihrem Azure ML-Arbeitsbereich gespeichert. Sie können ein Azure-Speicherhilfsprogramm verwenden, um die Datei direkt zu öffnen und zu verwenden. Sie können auch auf die CSV-Datei im Designer zugreifen, indem Sie die Komponente **Convert to CSV** (In CSV konvertieren) und dann das Histogrammsymbol unter der Registerkarte **Outputs** (Ausgaben) im rechten Fensterbereich auswählen, um die Ausgabe anzuzeigen. Sie können die CSV-Datei aus dem Ergebnisordner in ein lokales Verzeichnis herunterladen.  

## <a name="how-to-configure-convert-to-csv"></a>Konfigurieren von „Convert to CSV“


1.  Fügen Sie Ihrer Pipeline die „Convert to CSV“-Komponente hinzu. Diese Komponente befindet sich im Designer in der Gruppe **Datentransformation**. 

2. Verbinden Sie diese mit einer beliebigen Komponente, die ein Dataset ausgibt.   
  
3.  Übermitteln Sie die Pipeline.

### <a name="results"></a>Ergebnisse
  

Wählen Sie die Registerkarte **Ausgaben** im rechten Bereich von **Convert to CSV** (In CSV konvertieren) aus, und wählen Sie dann eines dieser Symbole unter den **Portausgaben** aus.  

+ **Dataset registrieren**: Wählen Sie das Symbol aus, und speichern Sie die CSV-Datei als separates Dataset in den Azure ML-Arbeitsbereich zurück. Sie finden das Dataset als Komponente in der Komponentenstruktur unter dem Abschnitt **My Datasets** (Meine Datasets).

 + **Ausgabe anzeigen**: Wählen Sie das Augensymbol aus, und folgen Sie den Anweisungen, um den Ordner **Results_dataset** zu durchsuchen und die Datei „data.csv“ herunterzuladen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 