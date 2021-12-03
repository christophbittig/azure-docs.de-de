---
title: '„Latent Dirichlet Allocation“: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die Komponente „Latent Dirichlet Allocation“ verwenden, um andernfalls nicht klassifizierten Text in Kategorien zu gruppieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f8256a6cfd3b59f6f47cc703d05451ea594885cb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566460"
---
# <a name="latent-dirichlet-allocation-component"></a>„Latent Dirichlet Allocation“-Komponente

In diesem Artikel wird beschrieben, wie Sie die „Latent Dirichlet Allocation“-Komponente im Azure Machine Learning-Designer verwenden, um andernfalls nicht klassifizierten Text in Kategorien zu gruppieren. 

Latent Dirichlet Allocation (LDA) wird häufig bei der Verarbeitung natürlicher Sprache (Natural Language Processing) verwendet, um ähnliche Texte zu finden. Ein anderer häufig verwendeter Begriff ist *Themenmodellierung*.

Diese Komponente verwendet eine Textspalte und generiert diese Ausgaben:

+ Den Quelltext mit einem Score für jede Kategorie

+ Eine Featurematrix mit extrahierten Begriffen und Koeffizienten für jede Kategorie

+ Eine Transformation, die Sie speichern und erneut auf neuen Eingabetext anwenden können

Diese Komponente verwendet die Scikit-learn-Bibliothek. Weitere Informationen zu scikit-learn finden Sie im [GitHub-Repository](https://github.com/scikit-learn/scikit-learn), das Tutorials und eine Erläuterung des Algorithmus enthält.

## <a name="more-about-latent-dirichlet-allocation"></a>Weitere Informationen zu Latent Dirichlet Allocation

LDA ist im Allgemeinen keine Methode zur Klassifizierung. Aber es verwendet einen generativen Ansatz, sodass Sie keine bekannten Klassenbezeichnungen bereitstellen und dann die Muster herleiten müssen.  Stattdessen generiert der Algorithmus ein Wahrscheinlichkeitsmodell, das verwendet wird, um Gruppen von Themen zu identifizieren. Sie können das Wahrscheinlichkeitsmodell zum Klassifizieren von vorhandenen Trainingsfällen oder von neuen Fällen verwenden, die Sie als Eingabe für das Modell bereitstellen.

Möglicherweise bevorzugen Sie ein generatives Modell, da es starke Annahmen zur Beziehung zwischen dem Text und den Kategorien vermeidet. Es verwendet nur die Verteilung von Wörtern zur mathematischen Modellierung von Themen.

Die Theorie wird in diesem Dokument erläutert, das als PDF-Download zur Verfügung steht: [Latent Dirichlet Allocation: Blei, Ng, and Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

Die Implementierung in dieser Komponente basiert auf der [Scikit-learn-Bibliothek](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) für LDA.

Weitere Informationen finden Sie im Abschnitt mit den [technischen Hinweisen](#technical-notes).

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Konfigurieren der Latent Dirichlet Allocation

Diese Komponente erfordert ein Dataset, das eine Textspalte enthält, die entweder roh oder vorverarbeitet ist.

1. Fügen Sie Ihrer Pipeline die **Latent Dirichlet Allocation**-Komponente hinzu.

2. Geben Sie als Eingabe für die Komponente ein Dataset an, das mindestens eine Textspalte enthält.

3. Wählen Sie als **Zielspalten** mindestens eine Spalte aus, die zu analysierenden Text enthält.

    Sie können mehrere Spalten auswählen, diese müssen jedoch den Datentyp **String** aufweisen.

    Da LDA eine große Featurematrix aus dem Text erstellt, analysieren Sie in der Regel eine einzelne Textspalte.

4. Geben Sie für **Number of topics to model** (Anzahl der zu modellierenden Themen) einen Integer zwischen 1 und 1000 ein, der angibt, wie viele Kategorien oder Themen Sie aus dem Eingabetext ableiten möchten.

    Standardmäßig werden fünf Themen erstellt.

5. Geben Sie für **N-grams** die maximale Länge von N-Grammen an, die während der Hasherstellung generiert werden sollen.

    Der Standardwert beträgt 2, sodass sowohl Bigramme als auch Unigramme generiert werden.

6. Wählen Sie die Option **Normalize** (Normalisieren) aus, um Ausgabewerte in Wahrscheinlichkeiten zu konvertieren. 

    Anstatt die transformierten Werte als Integer darzustellen, werden die Werte im Ausgabe- und Featuredataset somit wie folgt transformiert:

    + Werte im Dataset werden als Wahrscheinlichkeit dargestellt durch `P(topic|document)`.

    + Werte in der Feature-Themen-Matrix werden als Wahrscheinlichkeit dargestellt durch `P(word|topic)`.

    > [!NOTE] 
    > Im Azure Machine Learning-Designer unterstützt die scikit-learn-Bibliothek ab Version 0.19 keine nicht normalisierte *doc_topic_distr*-Ausgabe mehr. In dieser Komponente kann der Parameter **Normalisieren** nur auf die Ausgabe der *Themenmatrix* des Features angewendet werden. Die Ausgabe eines *transformierten Datasets* ist immer normalisiert.

7. Wählen Sie die Option **Show all options** (Alle Optionen anzeigen) aus, und legen Sie sie auf **TRUE** fest, wenn Sie die folgenden erweiterten Parameter festlegen möchten.

    Diese Parameter sind spezifisch für die scikit-learn-Implementierung von LDA. Es gibt einige gute Tutorials zu LDA in scikit-learn sowie das offizielle [Dokument zu scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parameter „Rho“** . Geben Sie eine vorherige Wahrscheinlichkeit für eine geringe Datendichte von Themenverteilungen an. Dieser Parameter entspricht dem Parameter `topic_word_prior` in „sklearn“. Verwenden Sie den Wert **1**, wenn Sie davon ausgehen, dass die Verteilung der Wörter flach ist, d. h., dass alle Wörter als gleich wahrscheinlich angesehen werden. Wenn Sie davon ausgehen, dass die meisten Wörter nur eine geringe Datendichte aufweisen, können Sie einen niedrigeren Wert festlegen.

    + **Parameter „Alpha“** . Geben Sie eine vorherige Wahrscheinlichkeit für eine geringe Datendichte von Themengewichtungen pro Dokument an. Dieser Parameter entspricht dem Parameter `doc_topic_prior` in „sklearn“.

    + **Geschätzte Anzahl von Dokumenten**. Geben Sie eine Zahl für Ihre beste Schätzung der Anzahl der zu verarbeitenden Dokumente (Zeilen) ein. Mit diesem Parameter kann die Komponente eine Hashtabelle mit ausreichender Größe zuordnen. Er entspricht dem Parameter `total_samples` in scikit-learn.

    + **Größe des Batches**. Geben Sie eine Zahl ein, die angibt, wie viele Zeilen in jeden Textbatch eingeschlossen werden sollen, der an das LDA-Modell gesendet wird. Dieser Parameter entspricht dem Parameter `batch_size` in scikit-learn.

    + **Anfänglicher Wert der Iterationen im Aktualisierungszeitplan für das Lernen**. Geben Sie den Startwert an, mit dem die Lernrate für frühe Iterationen beim Onlinelernen herabgestuft wird. Dieser Parameter entspricht dem Parameter `learning_offset` in scikit-learn.

    + **Während der Aktualisierung auf die Iteration aufgewandte Leistung**. Geben Sie den Leistungsgrad an, der auf Anzahl von Iterationen aufgewandt wird, um die Lernrate während Onlineupdates zu steuern. Dieser Parameter entspricht dem Parameter `learning_decay` in scikit-learn.

    + **Anzahl der Durchgänge über die Daten**. Geben Sie an, wie oft der Algorithmus die Daten maximal durchlaufen soll. Dieser Parameter entspricht dem Parameter `max_iter` in scikit-learn.

8. Wählen Sie eine der Optionen **Build dictionary of ngrams** (Wörterbuch aus N-Grammen erstellen) oder **Build dictionary of ngrams prior to LDA** (Vor LDA Wörterbuch aus N-Grammen erstellen) aus, wenn Sie die Liste der N-Gramme in einem anfänglichen Durchlauf erstellen möchten, bevor Text klassifiziert wird.

    Wenn Sie das erste Wörterbuch vorab erstellen, können Sie es später beim Überprüfen des Modells verwenden. Zuordnungen von Ergebnissen zu Text anstelle von numerischen Indizes sind in der Regel einfacher zu interpretieren. Das Speichern des Wörterbuchs dauert jedoch länger und verbraucht zusätzlichen Speicher.

9. Geben Sie für **Maximum size of ngram dictionary** (Maximale Größe des N-Gramm-Wörterbuchs) die Gesamtanzahl von Zeilen ein, die im N-Gramm-Wörterbuch erstellt werden können.

    Diese Option ist nützlich, um die Größe des Wörterbuchs zu steuern. Wenn aber die Anzahl der N-Gramme in der Eingabe diese Größe überschreitet, können Konflikte auftreten.

10. Übermitteln Sie die Pipeline. Die LDA-Komponente verwendet das Bayes-Theorem, um zu bestimmen, welche Themen einzelnen Wörtern zugeordnet sein können. Wörter sind Themen oder Gruppen nicht exklusiv zugeordnet. Stattdessen weist jedes N-Gramm eine erlernte Wahrscheinlichkeit auf, mit einer der ermittelten Klassen verknüpft zu sein.

## <a name="results"></a>Ergebnisse

Die Komponente verfügt über zwei Ausgaben:

+ **Transformiertes Dataset:** Diese Ausgabe enthält den Eingabetext, eine angegebene Anzahl von ermittelten Kategorien und die Scores für jedes Textbeispiel und jede Kategorie.

+ **Feature-Themen-Matrix:** In der linken Spalte ist die extrahierte Textfunktion enthalten. Eine Spalte für jede Kategorie enthält den Score für dieses Feature in dieser Kategorie.


### <a name="lda-transformation"></a>LDA-Transformation

Diese Komponente gibt auch die *LDA-Transformation* aus, die LDA auf das Dataset anwendet.

Sie können diese Transformation speichern und für andere Datasets wiederverwenden. Dieses Verfahren kann nützlich sein, wenn Sie mit einem großen Korpus trainiert haben und die Koeffizienten oder Kategorien wiederverwenden möchten.

Um diese Transformation wiederzuverwenden, wählen Sie das Symbol **Register Dataset** (Datasets registrieren) im rechten Bereich der „Latent Dirichlet Allocation“-Komponente aus, um die Komponente in der Komponentenliste in der Kategorie **Datasets** zu halten. Anschließend können Sie diese Komponente mit der [Transformation anwenden](apply-transformation.md)-Komponente verbinden, um diese Transformation wiederzuverwenden.

### <a name="refining-an-lda-model-or-results"></a>Optimieren von LDA-Modellen oder -Ergebnissen

Normalerweise können Sie nicht ein einzelnes LDA-Modell erstellen, das alle Anforderungen erfüllt. Selbst ein Modell, das für eine Aufgabe konzipiert wurde, kann viele Iterationen erfordern, um die Genauigkeit zu verbessern. Es wird empfohlen, alle diese Methoden zur Verbesserung des Modells auszuprobieren:

+ Ändern der Modellparameter
+ Analysieren der Ergebnisse mithilfe von Visualisierung
+ Rücksprache mit Fachleuten, um zu ermitteln, ob die generierten Themen nützlich sind

Qualitative Maßnahmen können für eine Auswertung der Ergebnisse ebenfalls hilfreich sein. Zum Auswerten der Ergebnisse der Themenmodellierung sollten Sie Folgendes berücksichtigen:

+ Genauigkeit Sind ähnliche Elemente wirklich ähnlich?
+ Diversität Kann das Modell zwischen ähnlichen Elementen unterscheiden, wenn dies für das Geschäftsproblem erforderlich ist?
+ Skalierbarkeit: Funktioniert es für eine breite Streuung von Textkategorien oder nur für eine eng gefasste Zieldomäne?

Die Genauigkeit von Modellen, die auf LDA basieren, kann häufig durch die Verarbeitung natürlicher Sprache verbessert werden, um Text zu bereinigen, zusammenzufassen und zu vereinfachen oder zu kategorisieren. Beispielsweise können die folgenden Techniken, die alle in Azure Machine Learning unterstützt werden, die Klassifizierungsgenauigkeit verbessern:

+ Stoppwortentfernung

+ Kasusnormalisierung

+ Lemmatisierung oder Wortstammerkennung

+ Erkennung benannter Entitäten

Weitere Informationen finden Sie unter [Vorverarbeiten von Text](preprocess-text.md).

Sie können im Designer für die Textverarbeitung auch R- oder Python-Bibliotheken verwenden: [R-Skript ausführen](execute-r-script.md), [Python-Skript ausführen](execute-python-script.md)



## <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

### <a name="implementation-details"></a>Details zur Implementierung

Standardmäßig werden die Verteilungen von Ausgaben für transformierte Datasets und Feature-Themen-Matrizen als Wahrscheinlichkeiten normalisiert:

+ Das transformierte Dataset wird als bedingte Wahrscheinlichkeit von Themen in einem Dokument normalisiert. In diesem Fall ist die Summe der einzelnen Zeilen gleich 1.

+ Die Feature-Themen-Matrix wird als bedingte Wahrscheinlichkeit von Wörtern in einem Thema normalisiert. In diesem Fall ist die Summe der einzelnen Spalten gleich 1.

> [!TIP]
> Gelegentlich gibt die Komponente möglicherweise ein leeres Thema zurück. Meistens ist die Ursache eine pseudozufällige Initialisierung des Algorithmus. In diesem Fall können Sie versuchen, zugehörige Parameter zu ändern. Ändern Sie z. B. die maximale Größe des N-Gramm-Wörterbuchs oder die Anzahl von Bits für die Featurehasherstellung.

### <a name="lda-and-topic-modeling"></a>LDA und Themenmodellierung

Latent Dirichlet Allocation wird für die *inhaltsbasierte Themenmodellierung* verwendet. Dies bedeutet grundsätzlich das Erlernen von Kategorien aus nicht klassifiziertem Text. Bei der inhaltsbasierten Themenmodellierung ist ein Thema eine Verteilung von Wörtern.

Angenommen, Sie haben einen Korpus von Kundenrezensionen bereitgestellt, der viele Produkte enthält. Der Text der Rezensionen, die von Kunden im Lauf der Zeit übermittelt wurden, enthält viele Begriffe, die zum Teil in mehreren Themen verwendet werden.

Ein *Thema*, das durch den LDA-Prozess identifiziert wird, kann eine Rezension für ein einzelnes Produkt darstellen, oder es kann eine Gruppe von Produktrezensionen darstellen. Für LDA ist das Thema lediglich eine Wahrscheinlichkeitsverteilung über die Zeit für eine Gruppe von Wörtern.

Die Begriffe sind selten exklusiv für ein einzelnes Produkt. Sie können sich auf andere Produkte beziehen oder allgemeine Begriffe sein, die für alles gelten („großartig“, „schrecklich“). Andere Begriffe können Füllwörter sein. Die LDA-Methode versucht jedoch nicht alle Wörter der Welt zu erfassen und die Beziehung zwischen Wörtern zu verstehen (abgesehen von den Wahrscheinlichkeiten zusammen auftretender Wörter). Sie kann nur Wörter gruppieren, die in der Zieldomäne verwendet werden.

Nachdem die Begriffsindizes berechnet wurden, vergleicht ein distanzabhängiges Ähnlichkeitsmeasure einzelne Textzeilen, um festzustellen, ob zwei Textkomponenten ähnlich sind. Beispielsweise können Sie feststellen, dass das Produkt mehrere Namen aufweist, die stark korreliert sind. Möglicherweise werden Sie auch feststellen, dass stark negative Begriffe normalerweise einem bestimmten Produkt zugeordnet sind. Sie können das Ähnlichkeitsmeasure sowohl zum Identifizieren verwandter Begriffe als auch zum Erstellen von Empfehlungen verwenden.

###  <a name="component-parameters"></a>Komponentenparameter

|Name|type|Range|Optional|Standard|BESCHREIBUNG|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s)|Spaltenauswahl||Erforderlich|StringFeature|Name oder Index der Zielspalte.|  
|Number of topics to model|Integer|[1;1000]|Erforderlich|5|Modellieren der Dokumentverteilung anhand von N Themen.|  
|N-grams|Integer|[1;10]|Erforderlich|2|Anzahl der N-Gramme an, die während der Hasherstellung generiert werden.|  
|Normalize|Boolean|„true“ oder „false“|Erforderlich|true|Normalisieren der Ausgabe in Wahrscheinlichkeiten.  Das transformierte Dataset ist P(Thema&#124;Dokument), und die Feature-Themen-Matrix ist P(Wort&#124;Thema).|  
|Show all options|Boolean|„true“ oder „false“|Erforderlich|False|Zeigt zusätzliche Parameter an, die speziell für die Online-LDA mit scikit-learn gelten.|  
|Rho parameter|Float|[0.00001;1.0]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|0.01|Wort im Thema vor der Verteilung.|  
|Alpha parameter|Float|[0.00001;1.0]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|0.01|Thema im Dokument vor der Verteilung.|  
|Estimated number of documents|Integer|[1;int.MaxValue]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|1000|Estimated number of documents Entspricht dem Parameter `total_samples`.|  
|Größe des Batches|Integer|[1;1024]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|32|Größe des Batches.|  
|Initial value of iteration used in learning rate update schedule|Integer|[0;int.MaxValue]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|0|Anfangswert, der die Lernrate für frühe Iterationen herabstuft. Entspricht dem Parameter `learning_offset`.|  
|Power applied to the iteration during updates|Float|[0.0;1.0]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|0.5|Die auf die Iterationsanzahl aufgewendete Leistung zum Steuern der Lernrate. Entspricht dem Parameter `learning_decay`. |  
|Anzahl der Trainingsiterationen|Integer|[1;1024]|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|25|Anzahl der Trainingsiterationen.|  
|Build dictionary of ngrams|Boolean|„true“ oder „false“|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) *nicht* aktiviert ist.|True|Erstellt vor dem Berechnen der LDA ein Wörterbuch von N-Grammen. Nützlich für die Untersuchung und Interpretation des Modells.|  
|Maximum size of ngram dictionary|Integer|[1;int.MaxValue]|Gilt, wenn die Option **Build dictionary of ngrams** den Wert **True** aufweist.|20000|Maximale Größe des N-Gramm-Wörterbuchs. Wenn die Anzahl der Token in der Eingabe diese Größe überschreitet, können Konflikte auftreten.|  
|Anzahl von Bits für die Featurehasherstellung.|Integer|[1;31]|Gilt, wenn das Kontrollkästchen **Show all options** *nicht* aktiviert ist und **Build dictionary of ngrams** den Wert **False** aufweist.|12|Anzahl von Bits für die Featurehasherstellung.| 
|Build dictionary of ngrams prior to LDA|Boolean|„true“ oder „false“|Gilt, wenn das Kontrollkästchen **Show all options** (Alle Optionen anzeigen) aktiviert ist.|True|Erstellt vor der LDA ein Wörterbuch von N-Grammen. Nützlich für die Untersuchung und Interpretation des Modells.|  
|Maximum number of ngrams in dictionary|Integer|[1;int.MaxValue]|Gilt, wenn das Kontrollkästchen **Show all options** aktiviert ist und **Build dictionary of ngrams** den Wert **True** aufweist.|20000|Maximale Größe des Wörterbuchs. Wenn die Anzahl der Token in der Eingabe diese Größe überschreitet, können Konflikte auftreten.|  
|Number of hash bits|Integer|[1;31]|Gilt, wenn das Kontrollkästchen **Show all options** aktiviert ist und **Build dictionary of ngrams** den Wert **False** aufweist.|12|Anzahl von Bits für die Featurehasherstellung.|   


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 

Eine Liste der Fehler, die bei den Komponenten auftreten, finden Sie unter [Ausnahmen und Fehlercodes für den Designer](designer-error-codes.md).
