---
title: 'Trainieren des Anomalieerkennungsmodells: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente zum Trainieren eines Anomalieerkennungsmodells verwenden, um ein trainiertes Modell zur Erkennung von Anomalien zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 8114492c9740cf706e9d2ba6fbd99f22029ba769
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289829"
---
# <a name="train-anomaly-detection-model-component"></a>Komponente „Trainieren eines Anomalieerkennungsmodells“

In diesem Artikel wird beschrieben, wie Sie die Komponente „Train Anomaly Detection Model“ (Trainieren eines Anomalieerkennungsmodells) im Azure Machine Learning-Designer verwenden, um ein trainiertes Modell zur Anomalieerkennung zu erstellen.

Als Eingabe für diese Komponente werden mehrere Modellparameter für das Anomalieerkennungsmodell sowie ein Dataset ohne Bezeichnung verwendet. Es gibt ein trainiertes Anomalieerkennungsmodell sowie einen Satz von Bezeichnungen für die Trainingsdaten zurück.  

Weitere Informationen zu den Algorithmen für die Anomalieerkennung im Designer finden Sie unter [PCA-basierte Erkennung von Anomalien](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Konfigurieren des Moduls zum Trainieren eines Anomalieerkennungsmodells 

1.  Fügen Sie die Komponente **Train Anomaly Detection Model** zu Ihrer Pipeline im Designer hinzu. Sie finden diese Komponente in der Kategorie **Anomalieerkennung**.

2. Verbinden Sie eine der Komponenten zur Anomalieerkennung, z. B. die Komponente für die [PCA-basierte Erkennung von Anomalien](pca-based-anomaly-detection.md).

    Andere Arten von Modellen werden nicht unterstützt. Wenn Sie die Pipeline ausführen, erhalten Sie die Fehlermeldung „All models must have the same learner type“ (Alle Modelle müssen denselben Learnertyp haben).  

3.  Konfigurieren Sie die Komponente zur Anomalieerkennung, indem Sie die Bezeichnungsspalte auswählen und andere, für den Algorithmus spezifische Parameter festlegen.  

4.  Fügen Sie ein Trainingsdataset an die rechte Eingabe von **Trainieren eines Anomalieerkennungsmodells** an.  

5.  Übermitteln Sie die Pipeline.  

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um die Modellparameter anzuzeigen, klicken Sie mit der rechten Maustaste auf die Komponente, und wählen Sie **Visualisieren** aus. 

+ Verwenden Sie die Komponente [Bewertungsmodell](score-model.md) mit neuen Eingabedaten, um Vorhersagen zu erstellen.

+ Wenn Sie eine Momentaufnahme des trainierten Modells speichern möchten, wählen Sie die Komponente aus. Klicken Sie dann auf das Symbol **Register dataset** (Dataset registrieren) unterhalb der Registerkarte **Protokolle und Ausgaben** im Bereich rechts.   

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 

Eine Liste der Fehler, die bei Designer-Komponenten auftreten können, finden Sie unter [Ausnahmen und Fehlercodes für den Designer](designer-error-codes.md).
