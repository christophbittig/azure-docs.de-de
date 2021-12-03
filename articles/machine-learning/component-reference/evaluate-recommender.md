---
title: 'Evaluate Recommender (Evaluieren von Empfehlungswerten): Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Evaluate Recommender“ (Evaluieren von Empfehlungswerten) in Azure Machine Learning verwenden können, um die Genauigkeit der Vorhersagen eines Empfehlungsmodells zu evaluieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: aa43a0e7fdf7214f52855ad2bc7601188c305474
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566458"
---
# <a name="evaluate-recommender"></a>Evaluate Recommender

In diesem Artikel wird die Verwendung der Komponente Evaluate Recommender im Azure Machine Learning-Designer beschrieben. Das Ziel besteht darin, die Genauigkeit der Vorhersagen zu messen, die von einem Empfehlungsmodell erstellt wurden. Mit dieser Komponente können Sie verschiedene Arten von Empfehlungen evaluieren:  
  
-   Für einen Benutzer und ein Element vorhergesagte Bewertungen    
-   Für einen Benutzer empfohlene Elemente  
  
Wenn Sie Vorhersagen mit einem Empfehlungsmodell erstellen, werden für jeden dieser unterstützten Vorhersagetypen geringfügig andere Ergebnisse zurückgegeben. Die Komponente „Evaluate Recommender“ leitet die Art der Vorhersage aus dem Spaltenformat des bewerteten Datasets ab. Beispielsweise kann das bewertete Dataset Folgendes enthalten:

- user-item-rating-Tripel
- Benutzer und deren empfohlenen Elemente

In der Komponente werden außerdem die entsprechenden Leistungsmetriken anhand des Typs der Vorhersage angewendet, die erstellt wird. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Konfigurieren von „Evaluate Recommender“

In der Komponente „Evaluate Recommender“ wird eine Vorhersage, die von einem Empfehlungsmodell ausgegeben wurde, mit den entsprechenden „Ground Truth“-Daten verglichen. Beispielsweise werden mit der Komponente [Score SVD Recommender](score-svd-recommender.md) bewertete Datasets erzeugt, die Sie mit „Evaluate Recommender“ analysieren können.

### <a name="requirements"></a>Anforderungen

Für „Evaluate Recommender“ sind die folgenden Datasets als Eingabe erforderlich. 
  
#### <a name="test-dataset"></a>Testdataset

Das Testdataset enthält die „Ground Truth“-Daten in Form von dreiteiligen Elementen (Tripel) vom Typ „Benutzer-Element-Bewertung“ (user-item-rating).  

#### <a name="scored-dataset"></a>Bewertetes Dataset

Das bewertete Dataset enthält die Vorhersagen, die vom Empfehlungsmodell generiert wurden.  
  
Die Spalten in diesem zweiten Dataset richten sich nach der Art der Vorhersage, die Sie während des Bewertungsprozesses durchgeführt haben. Beispielsweise kann das bewertete Dataset Folgendes enthalten:

- Benutzer, Elemente und die Bewertungen, die der Benutzer wahrscheinlich für das Element vergeben würde
- Eine Liste der Benutzer und der für sie empfohlenen Elemente 

### <a name="metrics"></a>Metriken

Leistungsmetriken für das Modell werden basierend auf dem Typ der Eingabe generiert. Die folgenden Abschnitte enthalten weitere Informationen.

## <a name="evaluate-predicted-ratings"></a>Evaluieren von vorhergesagten Bewertungen  

Wenn Sie vorhergesagte Bewertungen auswerten, muss das bewertete Dataset (zweite Eingabe für „Evaluate Recommender“) user-item-rating-Tripel enthalten, die die folgenden Anforderungen erfüllen:
  
-   Die erste Spalte des Datasets enthält die Benutzerbezeichner.    
-   Die zweite Spalte enthält die Element-IDs.  
-   Die dritte Spalte enthält die entsprechenden Bewertungen für die Benutzer-Element-Kombinationen.  
  
> [!IMPORTANT] 
> Damit die Auswertung erfolgreich sein kann, müssen die Spalten die Namen `User`, `Item` bzw. `Rating` haben.  
  
„Evaluate Recommender“ vergleicht die Bewertungen im „Ground Truth“-Dataset mit den vorhergesagten Bewertungen des bewerteten Datasets. Anschließend werden der mittlere absolute Fehler (Mean Absolute Error, MAE) und der mittlere quadratische Fehler (Root Mean Squared Error, RMSE) berechnet.



## <a name="evaluate-item-recommendations"></a>Evaluieren von Elementempfehlungen

Wenn Sie Elementempfehlungen evaluieren, sollten Sie ein bewertetes Dataset verwenden, das die empfohlenen Elemente für jeden Benutzer enthält:
  
-   Die erste Spalte des Datasets muss die Benutzer-ID enthalten.    
-   Alle nachfolgenden Spalten sollten die entsprechenden empfohlenen Element-IDs enthalten, die danach geordnet sind, wie relevant ein Element für den Benutzer ist. 

Bevor Sie dieses Dataset einbinden, empfiehlt es sich, das Dataset so zu sortieren, dass die relevantesten Elemente am Anfang stehen.  

> [!IMPORTANT] 
> Damit „Evaluate Recommender“ funktioniert, müssen die Spaltennamen `User`, `Item 1`, `Item 2`, `Item 3` usw. lauten.  
  
„Evaluate Recommender“ berechnet den durchschnittlichen Wert für Normalized Discounted Cumulative Gain (NDCG) und gibt ihn im Ausgabedataset zurück.  
  
Da es nicht möglich ist, die tatsächlichen „Ground Truth“-Werte für die empfohlenen Elemente zu kennen, verwendet „Evaluate Recommender“ die Benutzer-Element-Bewertungen im Testdataset als Nutzen (Gains) bei der Berechnung von NDCG. Für eine Evaluierung muss die Komponente Empfehlungsbewertung nur Empfehlungen für Elemente mit „Ground Truth“-Bewertungen (im Testdataset) generieren.  
  

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 
