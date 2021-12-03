---
title: 'Two-Class Averaged Perceptron: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die „Two-Class Averaged Perceptron“-Komponente (gemitteltes Perzeptron mit zwei Klassen) im Designer verwenden, um einen binären Klassifizierer zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 57be4bf26a9bb4622e590975c31111ee9614296e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566549"
---
# <a name="two-class-averaged-perceptron-component"></a>„Two-Class Averaged Perceptron“-Komponente

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente, um ein Machine Learning-Modell zu erstellen, das auf dem „Averaged Perceptron“-Algorithmus basiert.  
  
Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Sie können das Modell trainieren, indem Sie das Modell und das mit Tags versehene Dataset als Eingabe für [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabebeispiele vorherzusagen.  

### <a name="about-averaged-perceptron-models"></a>Informationen zu „Gemitteltes Perzeptron“-Modellen

Die *„Gemitteltes Perzeptron“-Methode* ist eine frühe und einfache Version eines neuronalen Netzwerks. Bei diesem Ansatz werden Eingaben basierend auf einer linearen Funktion in mehrere mögliche Ausgaben klassifiziert und dann mit einem Satz Gewichtungen kombiniert, die aus dem Featurevektor abgeleitet werden – daher der Name „Perzeptron“.

Die einfacheren Perzeptronmodelle eignen sich zum Lernen linear trennbarer Muster, während neuronale Netze (insbesondere tiefe neuronale Netze) komplexere Klassengrenzen modellieren können. Allerdings sind Perzeptrone schneller, und weil sie Fälle seriell verarbeiten, können Perzeptrone kontinuierlich trainiert verwendet.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Gewusst wie: Konfigurieren eines gemittelten Perzeptrons mit zwei Klassen

1.  Fügen Sie Ihrer Pipeline die Komponente **Two-Class Averaged Perceptron** hinzu.  

2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, geben Sie eine bestimmte Menge von Werten als Argumente an.

    -   **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  
  
3.  Geben Sie für **Learning rate** (Lernrate) einen Wert für die *Lernrate* an. Die Lernratenwerte bestimmen die Größe des Schritts, der jedes Mal, wenn das Modell getestet und korrigiert wird, beim stochastischen Gradientenverfahren verwendet wird.
  
     Indem Sie die Rate verkleinern, testen Sie das Modell häufiger, mit dem Risiko, dass Sie auf einem lokalen Plateau hängen bleiben. Mit größeren Schritten können Sie schneller konvergieren, mit dem Risiko, den echten Mindestwert zu unterschreiten.
  
4.  Geben Sie für **Maximum number of iterations** (Maximale Anzahl von Iterationen) die Häufigkeit ein, mit der der Algorithmus die Trainingsdaten überprüfen soll.  
  
     Ein frühes Stoppen bietet oft eine bessere Generalisierung. Ein Erhöhen der Anzahl von Iterationen verbessert die Anpassung, mit dem Risiko der Überanpassung.
  
5.  Geben Sie für **Random number seed** (Zufällig gewählter Startwert) optional einen ganzzahligen Wert ein, der als Startwert verwendet wird. Es empfiehlt sich, einen Startwert zu verwenden, wenn Sie die ausführungsübergreifende Reproduzierbarkeit der Pipeline sicherstellen möchten.  
  
1.  Verbinden Sie ein Trainingsdataset, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein markiertes Dataset und die Komponente [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine Parameterwerte an die [Tune Model Hyperparameters](tune-model-hyperparameters.md)-Komponente übergeben, ignoriert sie die Werte und verwendet die Standardwerte für das Lernprogramm, wenn sie einen Bereich von Einstellungen für jeden Parameter erwartet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.




## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 