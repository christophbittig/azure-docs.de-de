---
title: Verwenden der Score Wide & Deep Recommender-Komponente
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Score Wide & Deep Recommender-Komponente in Azure Machine Learning verwenden, um Empfehlungsvorhersagen für ein Dataset zu bewerten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 81754fced554327d02f5f3aaea80590e70654900
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566506"
---
# <a name="score-wide-and-deep-recommender"></a>Score Wide and Deep Recommender

In diesem Artikel wird beschrieben, wie Sie die Komponente **Score Wide & Deep Recommender** im Azure Machine Learning-Designer verwenden, um ausgehend von einem trainierten Empfehlungsmodell, das auf Wide & Deep Learning von Google basiert, Vorhersagen zu treffen.

Das Wide and Deep-Empfehlungsmodul kann zwei verschiedene Arten von Vorhersagen generieren:

- [Vorhersagen von Bewertungen für einen bestimmten Benutzer und ein bestimmtes Element](#predict-ratings)

- [Empfehlen von Elementen für einen bestimmten Benutzer](#recommend-items)


Beim Erstellen der letzten Art von Vorhersagen können Sie im *Produktionsmodus* oder *Auswertungsmodus* arbeiten.

- Im **Produktionsmodus** werden alle Benutzer oder Elemente berücksichtigt und normalerweise in einem Webdienst verwendet. Sie können Scores auch für neue Benutzer erstellen, nicht nur für Benutzer, die während des Trainings angezeigt werden. 

- Der **Auswertungsmodus** arbeitet mit einer reduzierten Gruppe von Benutzern oder Elementen, die ausgewertet werden können, und wird normalerweise beim Experimentieren verwendet.

Weitere Informationen zum Wide and Deep-Empfehlungsmodul und der zugrunde liegenden Theorie finden Sie im entsprechenden Forschungsbericht:  [Wide & Deep Learning für Empfehlungssysteme](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Konfigurieren von Score Wide and Deep Recommender

Diese Komponente unterstützt verschiedene Arten von Empfehlungen, die jeweils unterschiedliche Anforderungen aufweisen. Klicken Sie auf den Link für den Typ Ihrer Daten und die Art der Empfehlung, die Sie erstellen möchten.

+ [Vorhersagen von Bewertungen](#predict-ratings)
+ [Empfehlen von Elementen](#recommend-items)

### <a name="predict-ratings"></a>Vorhersagen von Bewertungen

Wenn Sie Bewertungen vorhersagen, berechnet das Modell anhand der Trainingsdaten, wie ein Benutzer auf ein bestimmtes Element reagiert. Daher müssen die Eingabedaten für die Bewertung sowohl einen Benutzer als auch das zu bewertende Element bereitstellen.

1. Fügen Sie Ihrem Experiment ein trainiertes Wide & Deep-Empfehlungsmodul hinzu, und verbinden Sie sie mit dem **trainierten Wide and Deep-Empfehlungsmodell**.  Sie müssen das Modell mit [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) erstellen.

2. **Vorhersageart des Empfehlungsmoduls:** Wählen Sie **Rating Prediction** (Bewertungsvorhersage) aus. Es sind keine weiteren Parameter erforderlich.

3. Fügen Sie die Daten hinzu, für die Sie Vorhersagen erstellen möchten, und verbinden Sie sie mit dem **zu bewertenden Dataset**.

    Zum Vorhersagen von Bewertungen muss das Eingabedataset Benutzer-Element-Paare enthalten.

    Das Dataset kann eine optionale dritte Spalte mit Bewertungen für das Benutzer-Element-Paar in der ersten und zweiten Spalte enthalten, diese dritte Spalte wird jedoch bei der Vorhersage ignoriert.

4.  (Optional). Wenn Sie über ein Dataset mit Benutzerfeatures verfügen, verbinden Sie es mit **Benutzerfeatures**.

    Das Dataset der Benutzerfeatures muss die Benutzer-IDs in der ersten Spalte enthalten. Die restlichen Spalten sollten Werte enthalten, die die Benutzer charakterisieren, z B. Geschlecht, Vorlieben, Standort usw.
  
    Features von Benutzern, die Elemente im Trainingsdataset bewertet haben, werden von **Score Wide and Deep Recommender** ignoriert, da sie bereits beim Training berücksichtigt wurden. Filtern Sie Ihr Dataset daher im Voraus, um nur *Kaltstartbenutzer* oder Benutzer, die keine Elemente bewertet haben, zu berücksichtigen.

    > [!WARNING]
    > Wenn das Modell ohne Verwendung von Benutzerfeatures trainiert wurde, können Sie während der Bewertung keine Benutzerfeatures einführen.

5. Wenn Sie ein Dataset mit Elementfeatures haben, können Sie es mit **Elementfeatures** verbinden.

    Das Dataset mit Elementfeatures muss einen Elementbezeichner in der ersten Spalte enthalten. Die übrigen Spalten enthalten Werte, die die Elemente charakterisieren.

    Features von bewerteten Elementen im Trainingsdataset werden von **Score Wide and Deep Recommender** ignoriert, da sie bereits beim Training berücksichtigt wurden. Beschränken Sie Ihr Bewertungsdataset daher auf *Kaltstartelemente* oder auf Elemente, die nicht von Benutzern bewertet wurden.

    > [!WARNING]
    > Wenn das Modell ohne Verwendung von Elementfeatures trainiert wurde, können Sie während der Bewertung keine Elementfeatures einführen.

7. Führen Sie das Experiment aus.

### <a name="results-for-rating-predictions"></a>Ergebnisse der Bewertungsvorhersagen 

Das Ausgabedataset enthält drei Spalten, die den Benutzer, das Element und die vorhergesagte Bewertung für jeden Benutzer und jedes Element des Eingabe enthalten.

Außerdem werden während der Bewertung die folgenden Änderungen angewendet:

-  Für eine numerische Benutzer- oder Elementfeaturespalte werden fehlende Werte automatisch mit dem **Durchschnitt** der nicht fehlenden Trainingssetwerte ersetzt werden. Für ein kategorisches Feature werden fehlende Werte durch denselben kategorischen Wert ersetzt und nicht durch andere mögliche Werte des Features.
-  Bei Featurewerten wird keine Übersetzung angewendet, um eine geringe Datendichte aufrechtzuerhalten.

### <a name="recommend-items"></a>Empfohlene Elemente

Zum Empfehlen von Elementen für Benutzer geben Sie eine Liste von Benutzern und Elementen als Eingabe an. Aus diesen Daten generiert das Modell anhand seiner Kenntnisse über vorhandene Elemente und Benutzer eine Liste von Elementen, die für den jeweiligen Benutzer wahrscheinlich attraktiv sind. Sie können die Anzahl der zurückgegebenen Empfehlungen anpassen und einen Schwellenwert für die Anzahl der vorherigen Empfehlungen festlegen, die zum Generieren einer Empfehlung erforderlich sind.

1. Fügen Sie Ihrem Experiment ein trainiertes Wide and Deep-Empfehlungsmodell hinzu, und verbinden Sie es mit dem **trainierten Wide and Deep-Empfehlungsmodell**.  Sie müssen das Modell mit [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) erstellen.

2. Um einer bestimmten Liste von Benutzern Elemente zu empfehlen, legen Sie **Recommender prediction kind** (Vorhersageart des Empfehlungsmoduls) auf **Item Recommendation** (Elementempfehlung) fest.

3. **Empfohlene Elementauswahl** Geben Sie an, ob Sie die Bewertungskomponente in der Produktion oder für die Modellauswertung verwenden, indem Sie einen der folgenden Werte auswählen:

    - **From Rated Items (for model evaluation)** (Aus bewerteten Elementen (für Modellauswertung)): Wählen Sie diese Option aus, wenn Sie ein Modell entwickeln oder testen. Diese Option aktiviert den **Auswertungsmodus**, und die Komponente gibt Empfehlungen nur für die Elemente im eingegebenen Dataset aus, die bewertet wurden.
    - **From All Items** (Aus allen Elementen): Wählen Sie diese Option aus, wenn Sie ein Experiment für die Verwendung in einem Webdienst oder in der Produktion einrichten.  Diese Option aktiviert den **Produktionsmodus**, und die Komponente gibt Empfehlungen für alle Elemente aus, die während des Trainings aufgetreten sind.
    - **Aus nicht bewerteten Elementen (um Benutzern neue Elemente vorzuschlagen)** : Wählen Sie diese Option aus, wenn die Komponente nur Empfehlungen auf der Grundlage der Elemente im Trainingsdataset generieren soll, die nicht bewertet wurden. 
4. Fügen Sie das Dataset hinzu, für das Sie Vorhersagen erstellen möchten, und verbinden Sie es mit dem **zu bewertenden Dataset**.

    - Für die Option **From All Items** (Aus allen Elementen) muss das Eingabedataset aus nur einer Spalte mit den Bezeichnern von Benutzern bestehen, für die Empfehlungen gegeben werden sollen.

        Das Dataset kann zwei weitere Spalten mit Element-IDs und Bewertungen enthalten, diese werden jedoch ignoriert. 

    - Wenn Sie die Option **From Rated Items (for model evaluation)** (Aus bewerteten Elementen (für Modellauswertung)) auswählen, muss das Eingabedataset aus **Benutzer-Element-Paaren** bestehen. Die erste Spalte muss den **Benutzerbezeichner** enthalten. Die zweite Spalte muss die entsprechenden **Elementbezeichner** enthalten.

        Das Dataset kann eine dritte Spalte mit Benutzer-Element-Bewertungen enthalten, diese Spalte wird jedoch ignoriert.
        
    - Bei Verwendung von **From Unrated Items (to suggest new items to users)** (Aus nicht bewerteten Elementen (zum Vorschlagen neuer Elemente für Benutzer)) muss das Eingabedataset aus Benutzer-Element-Paaren bestehen. Die erste Spalte muss die Benutzer-ID enthalten. Die zweite Spalte muss die entsprechenden Element-IDs enthalten.

        Das Dataset kann eine dritte Spalte mit Benutzer-Element-Bewertungen enthalten, diese Spalte wird jedoch ignoriert.

5. (Optional). Wenn Sie über ein Dataset mit **Benutzerfeatures** verfügen, verbinden Sie es mit **Benutzerfeatures**.

    Die erste Spalte des Datasets der Benutzerfeatures muss die Benutzer-IDs enthalten. Die restlichen Spalten sollten Werte enthalten, die den Benutzer charakterisieren, z B. Geschlecht, Vorlieben, Standort usw.

    Features von Benutzern, die Elemente bewertet haben, werden von **Score Wide and Deep Recommender** ignoriert, da diese Features bereits beim Training berücksichtigt wurden. Daher können Sie Ihr Dataset im Voraus filtern, um nur *Kaltstartbenutzer* oder Benutzer, die keine Elemente bewertet haben, zu berücksichtigen.

    > [!WARNING]
    >  Wenn das Modell ohne Verwendung von Benutzerfeatures trainiert wurde, können Sie während der Bewertung keine Features anwenden.

6. (Optional) Wenn Sie ein Dataset mit **Elementfeatures** haben, können Sie es mit **Elementfeatures** verbinden.

    Die erste Spalte des Datasets mit Elementfeatures muss den Elementbezeichner enthalten. Die übrigen Spalten enthalten Werte, die die Elemente charakterisieren.

    Features von bewerteten Elementen werden von **Score Wide and Deep Recommender** ignoriert, da diese Features bereits beim Training berücksichtigt wurden. Daher können Sie Ihr Bewertungsdataset auf *Kaltstartelemente* oder auf Elemente beschränken, die nicht von Benutzern bewertet wurden.

    > [!WARNING]
    >  Verwenden Sie keine Elementfeatures für Bewertungen, wenn das Modell ohne Verwendung der Elementfeatures trainiert wurde.  

7. **Maximum number of items to recommend to a user** (Maximale Anzahl von Elementen, die einem Benutzer empfohlen werden sollen): Geben Sie die Anzahl der Elemente ein, die für jeden Benutzer zurückgegeben werden soll. Standardmäßig werden fünf Elemente empfohlen.

8. **Minimum size of the recommendation pool per user** (Minimale Größe des Empfehlungspools pro Benutzer): Geben Sie einen Wert ein, der angibt, wie viele vorherige Empfehlungen erforderlich sind.  Standardmäßig ist dieser Parameter auf 2 festgelegt. Das bedeutet, dass das Element von mindestens zwei anderen Benutzern empfohlen werden muss.

    Diese Option sollte nur verwendet werden, wenn Sie im Auswertungsmodus bewerten. Die Option ist nicht verfügbar, wenn Sie **From All Items** (Aus allen Elementen) oder **From Unrated Items (to suggest new items to users)** (Aus nicht bewerteten Elementen (zum Vorschlagen neuer Elemente für Benutzer)) auswählen.

9. Verwenden Sie für **From Unrated Items (to suggest new items to users)** (Aus nicht bewerteten Elementen (zum Vorschlagen neuer Elemente für Benutzer)) den dritten Eingabeport mit dem Namen **Training Data** (Trainingsdaten), um bereits bewertete Elemente aus den Vorhersageergebnissen zu entfernen.

    Um diesen Filter anzuwenden, verbinden Sie das ursprüngliche Trainingsdataset mit dem Eingabeport.

10. Führen Sie das Experiment aus.
### <a name="results-of-item-recommendation"></a>Ergebnisse der Elementempfehlung

Das von **Score Wide and Deep Recommender** zurückgegebene bewertete Dataset enthält eine Liste der empfohlenen Elemente für jeden Benutzer.

- Die erste Spalte enthält die Benutzer-IDs.
- Abhängig vom Wert, den Sie für **Maximum number of items to recommend to a user** (Maximale Anzahl von Elementen, die einem Benutzer empfohlen werden sollen) festgelegt haben, werden verschiedene zusätzliche Spalten generiert. Jede Spalte enthält ein empfohlenes Element (nach Bezeichner). Die Empfehlungen werden nach der Affinität zwischen Benutzer und Element geordnet, wobei das Element mit der höchsten Affinität in die Spalte **Item 1** (Element 1) platziert wird.

##  <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Antworten auf einige häufig gestellte Fragen zur Verwendung des Wide & Deep-Empfehlungsmoduls zum Erstellen von Vorhersagen.  

###  <a name="cold-start-users-and-recommendations"></a>Kaltstartbenutzer und Empfehlungen

Zum Erstellen von Empfehlungen benötigt die **Score Wide and Deep Recommender**-Komponente in der Regel dieselben Eingaben, die Sie beim Trainieren des Modells verwendet haben, einschließlich einer Benutzer-ID. Dies liegt daran, dass der Algorithmus wissen muss, ob er während des Trainings etwas über diesen Benutzer gelernt hat. 

Bei neuen Benutzern verfügen Sie aber möglicherweise nicht über eine Benutzer-ID, sondern nur über einige Features wie z. B. Alter, Geschlecht usw.

Sie können trotzdem Empfehlungen zu neuen Benutzern im System erstellen, indem Sie sie als *Kaltstartbenutzer* behandeln. Für solche Benutzer verwendet der Empfehlungsalgorithmus weder die Vorgeschichte noch vorherige Bewertungen, sondern nur Benutzerfeatures.

Zu Vorhersagezwecken wird ein Kaltstartbenutzer als Benutzer mit einer ID definiert, die nicht zum Trainieren verwendet wurde. Um sicherzustellen, dass die IDs nicht mit den im Training verwendeten IDs identisch sind, können Sie neue Bezeichner erstellen. Sie können beispielsweise zufällige IDs innerhalb eines bestimmten Bereichs erstellen oder Kaltstartbenutzern im Voraus eine Reihe von IDs zuweisen.

Wenn Sie keine gemeinsamen Filterdaten besitzen, z. B. einen Vektor der Benutzerfeatures, sollten Sie besser ein Klassifizierungs- oder Regressions-Lernmodul verwenden.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Produktionsnutzung des Wide and Deep-Empfehlungsmoduls

Wenn Sie das Wide and Deep-Empfehlungsmodul erprobt haben und das Modell anschließend in der Produktionsumgebung einsetzen, sollten Ihnen die folgenden wichtigen Unterschiede bei Verwendung des Empfehlungsmoduls im Auswertungsmodus und im Produktionsmodus bewusst sein:

- Die Auswertung erfordert definitionsgemäß Vorhersagen, die anhand der *grundlegende Wahrheit* in einem Testsatz überprüft werden können. Wenn Sie das Empfehlungsmodul auswerten, dürfen daher nur Elemente vorhergesagt werden, die im Testsatz bewertet wurden. Dies schränkt notwendigerweise die möglichen vorhergesagten Werte ein.

    Wenn Sie das Modell jedoch operationalisieren, ändern Sie in der Regel den Vorhersagemodus, sodass Empfehlungen auf der Grundlage aller möglichen Elemente getroffen werden, um die besten Vorhersagen zu erhalten. Bei vielen dieser Vorhersagen gibt es keine entsprechende grundlegende Wahrheit, sodass die Genauigkeit der Empfehlung nicht wie beim Einstellen in Experimenten überprüft werden kann.

- Wenn Sie in der Produktionsumgebung keine Benutzer-ID, sondern nur einen Featurevektor bereitstellen, erhalten Sie als Antwort unter Umständen eine Liste aller Empfehlungen für alle möglichen Benutzer. Achten Sie darauf, dass Sie eine Benutzer-ID angeben.

    Um die Anzahl der zurückgegebenen Empfehlungen zu beschränken, können Sie auch die maximale Anzahl der pro Benutzer zurückgegebenen Elemente angeben. 



## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der verfügbaren Komponenten](component-reference.md) von Azure Machine Learning. 
