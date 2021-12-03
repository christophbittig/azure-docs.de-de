---
title: Ausführen eines Python-Skripts im Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Modell „Ausführen von Python-Skripts“ in Azure Machine Learning-Designer verwenden, um in Python geschriebene benutzerdefinierte Vorgänge auszuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: likebupt
ms.author: keli19
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer, devx-track-python
ms.openlocfilehash: 7c944ddd07f549a4956d334fea809d80f02db337
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564877"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Ausführen von Python-Code in Azure Machine Learning-Designer

In diesem Artikel erfahren Sie, wie Sie die [Execute Python Script](algorithm-module-reference/execute-python-script.md)-Komponente (Python-Skript ausführen) verwenden, um benutzerdefinierte Logik zu Azure Machine Learning hinzuzufügen. In der folgenden Schrittanleitung verwenden Sie die Pandas-Bibliothek, um einfache Features zu entwickeln.

Sie können den integrierten Code-Editor verwenden, um schnell einfache Python-Logik hinzuzufügen. Wenn Sie komplexeren Code hinzufügen oder zusätzliche Python-Bibliotheken hochladen möchten, sollten Sie die Methode mit der ZIP-Datei verwenden.

Die standardmäßige Ausführungsumgebung verwendet die Anacondas-Distribution von Python. Eine vollständige Liste der vorinstallierten Pakete finden Sie auf der Seite [„Execute Python Script“-Komponentenreferenz](algorithm-module-reference/execute-python-script.md).

![Eingabezuordnung zum Ausführen von Python-Code](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Führen Sie im Designer geschriebenen Python-Code aus.

### <a name="add-the-execute-python-script-component"></a>Hinzufügen der „Execute Python Script“-Komponente

1. Suchen Sie die **Execute Python Script**-Komponente in der Designerpalette. Es befindet sich im Abschnitt **Python (Programmiersprache)** .

1. Ziehen Sie die Komponente per Drag & Drop auf den Pipelinecanvas.

### <a name="connect-input-datasets"></a>Verbinden von Eingabedatasets

In diesem Artikel wird das Beispieldataset **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)) verwendet. 

1. Ziehen Sie Ihr Dataset per Drag & Drop auf die Pipelinecanvas.

1. Verbinden Sie den Ausgabeport des Datasets mit dem oben links befindlichen Eingabeport der **Execute Python Script**-Komponente. Der Designer stellt die Eingabe dem Einstiegspunktskript als Parameter zur Verfügung.
    
    Der rechte Eingangsport ist für gezippte Python-Bibliotheken reserviert.

    ![Verbinden von Datasets](media/how-to-designer-python/connect-dataset.png)
        

1. Notieren Sie sich, welchen Eingangsport Sie verwenden. Der Designer weist den linken Eingangsport der Variablen `dataset1` und den mittleren Eingangsport zu `dataset2` zu. 

Eingabekomponenten sind optional, da Sie Daten direkt in der **Execute Python Script**-Komponente generieren oder importieren können.

### <a name="write-your-python-code"></a>Schreiben Sie Ihren Python-Code

Der Designer stellt Ihnen ein erstes Einstiegspunktskript zur Verfügung, mit dem Sie Ihren eigenen Python-Code bearbeiten und eingeben können. 

In diesem Beispiel verwenden Sie Pandas, um zwei Spalten aus dem Automobildataset, **Price** (Preis) und **Horsepower** (PS), zu kombinieren, um eine neue Spalte, **Dollars per horsepower** (US-Dollar pro PS), zu erstellen. Diese Spalte gibt an, wie viel Sie für jedes PS zahlen, was ein nützliches Feature sein könnte, um zu entscheiden, ob ein Auto ein gutes Geschäft für den Preis ist. 

1. Wählen Sie die **Execute Python Script**-Komponente aus.

1. Wählen Sie im Bereich rechts neben der Canvas das Textfeld **Python-Skript** aus.

1. Kopieren Sie den folgenden Code, und fügen Sie ihn in das Textfeld ein.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Ihre Pipeline sollte wie im folgenden Bild aussehen:
    
    ![Ausführen einer Python-Pipeline](media/how-to-designer-python/execute-python-pipeline.png)

    Das Einstiegspunktskript muss die Funktion `azureml_main` enthalten. Es gibt zwei Funktionsparameter, die den beiden Eingabeports für die **Execute Python Script**-Komponente zugeordnet sind.

    Der Rückgabewert muss ein Pandas-Datenrahmen sein. Sie können bis zu zwei Datenrahmen als Komponentenausgaben zurückgeben.
    
1. Übermitteln Sie die Pipeline.

Jetzt verfügen Sie über ein Dataset mit dem neuen Feature **Dollars/HP** (US-Dollar/PS), das beim Training eines Empfehlungssystems für Autos nützlich sein könnte. Dies ist ein Beispiel für die Featureextraktion und Verringerung der Dimensionalität. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [ihre eigenen Daten](how-to-designer-import-data.md) in Azure Machine Learning-Designer importieren können.
