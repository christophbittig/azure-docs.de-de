---
title: 'Train SVD Recommender (SVD-Empfehlung trainieren): Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Train SVD Recommender in Azure Machine Learning verwenden, um ein bayessches Empfehlungsmodul mithilfe des SVD-Algorithmus zu trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 18cab5a14f5dfbd092da9fe9bdde8dca4c129b86
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566555"
---
# <a name="train-svd-recommender"></a>Train SVD Recommender

In diesem Artikel wird die Verwendung der Komponente Train SVD Recommender im Azure Machine Learning-Designer beschrieben. Verwenden Sie diese Komponente, um ein Empfehlungsmodell zu trainieren, das auf dem SVD-Algorithmus (Singular Value Decomposition, Singulärwertzerlegung) basiert.  

Die Komponente „Train SVD Recommender“ liest ein Dataset mit „user-item-rating-Tripeln“ (dreiteiliges Element mit „Benutzer-Element-Bewertung“). Es gibt ein trainiertes SVD-Empfehlungsmodul zurück. Mithilfe des trainierten Modells können Sie dann Bewertungen vorhersagen oder Empfehlungen generieren, indem Sie eine Verbindung mit der Komponente [Score SVD Recommender](score-svd-recommender.md) herstellen.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Weitere Informationen zu Empfehlungsmodellen und dem SVD-Empfehlungsmodul  

Das Hauptziel eines Empfehlungssystems besteht darin, *Benutzern* des Systems *Elemente* zu empfehlen. Beispiele für Elemente sind Filme, Restaurants, Bücher oder Songs. Ein Benutzer kann eine Person, eine Gruppe von Personen oder eine andere Entität mit bevorzugten Elementen sein.  

Es gibt zwei grundlegende Ansätze für Empfehlungssysteme: 

+ Einen **inhaltsbasierten** Ansatz, bei dem Features für Benutzer und Elemente genutzt werden. Benutzer können anhand von Eigenschaften wie Alter und Geschlecht beschrieben werden. Elemente können anhand von Eigenschaften wie Autor und Hersteller beschrieben werden. Typische Beispiele für inhaltsbasierte Empfehlungssysteme finden Sie auf Partnersuche-Websites. 
+ Bei der **kombinierten Filterung** werden nur Bezeichner der Benutzer und Elemente verwendet. Hierfür werden implizite Informationen zu diesen Entitäten über eine Matrix mit Bewertungen (Sparsematrix) bezogen, die von den Benutzern für die Elemente abgegeben wurden. Wir können über einen Benutzer etwas über die bewerteten Elemente und von den anderen Benutzern erfahren, die dieselben Elemente bewertet haben.  

Das SVD-Empfehlungsmodul verwendet Bezeichner der Benutzer und Elemente sowie eine Matrix der Bewertungen, die von den Benutzern für die Elemente abgegeben wurden. Hierbei handelt es sich um ein *Empfehlungsmodul mit Zusammenarbeit*. 

Weitere Informationen zu SVD Recommender finden Sie im entsprechenden Forschungsbericht [Matrixfaktorisierungstechniken für Empfehlungssysteme](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Konfigurieren von Train SVD Recommender  

### <a name="prepare-data"></a>Vorbereiten von Daten

Bevor Sie die Komponente verwenden können, müssen Ihre Eingabedaten das Format aufweisen, das vom Empfehlungsmodell erwartet wird. Ein Trainingsdataset mit user-item-rating-Tripeln ist erforderlich.

+ Die erste Spalte enthält Benutzerbezeichner.
+ Die zweite Spalte enthält Elementbezeichner.
+ Die dritte Spalte enthält die Bewertung für das Benutzer-Element-Paar. Alle Werte müssen numerisch sein.  

Das Dataset mit **Filmbewertungen** im Azure Machine Learning-Designer (wählen Sie **Datasets** und dann **Beispiele**) veranschaulicht das erwartete Format:

![Filmbewertungen](media/module/movie-ratings-dataset.png)

In diesem Beispiel können Sie sehen, dass ein einzelner Benutzer mehrere Filme bewertet hat. 

### <a name="train-the-model"></a>Trainieren des Modells

1.  Fügen Sie die Komponente „Train SVD Recommender“ Ihrer Pipeline im Designer hinzu und verbinden Sie es mit den Trainingsdaten.  
   
2.  Geben Sie unter **Number of factors** (Anzahl von Faktoren) die Anzahl von Faktoren ein, die für das Empfehlungsmodul verwendet werden sollen.  
    
    Jeder Faktor misst, wie stark der Benutzer mit dem Element verbunden ist. Die Anzahl der Faktoren ist auch die Dimensionalität des latenten Faktorraums. Wenn die Anzahl von Benutzern und Elementen zunimmt, empfiehlt es sich, eine größere Anzahl von Faktoren festzulegen. Es kann aber zu Leistungseinbußen kommen, wenn die Zahl zu groß wird.
    
3.  **Number of recommendation algorithm iterations** (Anzahl von Iterationen für Empfehlungsalgorithmen) gibt an, wie oft der Algorithmus die Eingabedaten verarbeiten soll. Je höher diese Zahl ist, desto genauer sind die Vorhersagen. Eine höhere Anzahl bedeutet aber, dass das Training länger dauert. Der Standardwert ist 30.

4.  Geben Sie unter **Learning rate** (Lernrate) eine Zahl zwischen 0,0 und 2,0 ein, um die Schrittgröße für das Lernen zu definieren.

    Mit der Lernrate wird die Größe des Schritts bei jeder Iteration festgelegt. Ist die Schrittgröße zu groß, wird die optimale Lösung unter Umständen verfehlt. Ist die Schrittgröße zu klein, dauert die Ermittlung der besten Lösung länger. 
  
5.  Übermitteln Sie die Pipeline.  

## <a name="results"></a>Ergebnisse

Wenn Sie das Modell zur Bewertung verwenden möchten, verbinden Sie nach Abschluss der Pipelineausführung [Train SVD Recommender](train-svd-recommender.md) mit [Score SVD Recommender](score-svd-recommender.md), um Werte für neue Eingabebeispiele vorherzusagen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 
