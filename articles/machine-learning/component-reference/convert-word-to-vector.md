---
title: 'Konvertieren von Wörtern in Vektoren: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit den drei mitgelieferten Word2Vec-Modellen ein Vokabular und die entsprechenden Worteinbettungen aus einem Textkorpus extrahieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: db3c1860ac1c986478168d66c9fdf8db65d3b893
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566413"
---
# <a name="convert-word-to-vector-component"></a>Komponente „Convert Word to Vector“ (Konvertieren von Wörtern in Vektoren)

In diesem Artikel wird die Verwendung der Komponente „Convert Word to Vector“ (Konvertieren von Wörtern in Vektoren) im Azure Machine Learning-Designer für folgende Aufgaben beschrieben:

- Anwenden verschiedener Word2Vec-Modelle (Word2Vec, FastText, vortrainiertes GloVe-Modell) auf den Textkorpus, den Sie als Eingabe angegeben haben.
- Generieren eines Vokabulars mit Worteinbettungen.

Diese Komponente verwendet die Gensim-Bibliothek. Weitere Informationen zu Gensim finden Sie auf dessen [offizieller Website](https://radimrehurek.com/gensim/apiref.html), die Tutorials und eine Erläuterung der Algorithmen enthält.

### <a name="more-about-converting-words-to-vectors"></a>Weitere Informationen zum Konvertieren von Wörtern in Vektoren

Das Konvertieren von Wörtern in Vektoren (oder Wortvektorisierung) ist ein NLP-Prozess (Natural Language Processing, Verarbeitung natürlicher Sprache). Er verwendet Sprachmodelle, um Wörtern Vektorraum zuzuordnen. Ein Vektorraum stellt jedes Wort durch einen Vektor aus reellen Zahlen dar. Er ermöglicht auch eine ähnliche Darstellung von Wörtern mit ähnlichen Bedeutungen.

Verwenden Sie Worteinbettungen als anfängliche Eingabe für nachgelagerte NLP-Tasks wie Textklassifizierung und Standpunktanalyse.

Es gibt verschiedene Worteinbettungstechnologien. In dieser Komponente haben wir drei häufig verwendete Methoden implementiert. Zwei, Word2Vec und FastText, sind Onlinetrainingsmodelle. Bei dem anderen handelt es sich um ein vortrainiertes Modell, glove-wiki-gigaword-100. 

Onlinetrainingsmodelle werden mit Ihren Eingabedaten trainiert. Vortrainierte Modelle werden offline mit einem größeren Textkorpus trainiert (z. B. Wikipedia, Google News), der in der Regel ungefähr 100 Milliarden Wörter enthält. Die Worteinbettung bleibt dann während der Wortvektorisierung konstant. Vortrainierte Wortmodelle bieten Vorteile, z. B. kürzere Trainingszeit, bessere codierte Wortvektoren und eine verbesserte Gesamtleistung.

Hier sind einige Informationen zu den Methoden:

+ Word2Vec ist eine der beliebtesten Techniken zum Erlernen von Worteinbettungen mithilfe eines flachen neuronalen Netzwerks. Die Theorie wird in diesem Dokument erläutert, das als PDF-Download zur Verfügung steht: [Efficient Estimation of Word Representations in Vector Space](https://arxiv.org/pdf/1301.3781.pdf) (Effiziente Schätzung für die Darstellung von Wörtern im Vektorraum). Die Implementierung in dieser Komponente basiert auf der [Gensim-Bibliothek für Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ Die FastText-Theorie wird in diesem Dokument erläutert, das als PDF-Download zur Verfügung steht: [Enriching Word Vectors with Subword Information](https://arxiv.org/pdf/1607.04606.pdf) (Anreichern von Wortvektoren mit Teilwortinformationen). Die Implementierung in dieser Komponente basiert auf der [Gensim-Bibliothek für FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Bei dem vortrainierten GloVe-Modell handelt es sich um glove-wiki-gigaword-100. Dies ist eine Sammlung vortrainierter Vektoren, die auf einem Wikipedia-Textkorpus basiert, der 5,6 Milliarden Token und 400.000 Vokabularwörter enthält, bei denen Groß-/Kleinschreibung nicht berücksichtigt wird. Ein PDF-Download ist verfügbar: [GloVe: Globale Vektoren für die Wortdarstellung](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Konfigurieren der Konvertierung von Wörtern in Vektoren

Diese Komponente erfordert ein Dataset mit einer Textspalte. Vorverarbeiteter Text ist besser.

1. Fügen Sie die Komponente **Convert Word to Vector** (Konvertieren von Wörtern in Vektoren) Ihrer Pipeline hinzu.

2. Stellen Sie als Eingabe für die Komponente ein Dataset mit einer oder mehreren Textspalten bereit.

3. Wählen Sie für **Zielspalte** nur eine Spalte aus, die zu verarbeitenden Text enthält.

    Da diese Komponente ein Vokabular aus Text erstellt, unterscheidet sich der Inhalt der verschiedenen Spalten, was zu unterschiedlichen Vokabularinhalten führt. Aus diesem Grund akzeptiert die Komponente nur eine Zielspalte.

4. Treffen Sie Ihre Wahl für **Word2Vec strategy** (Word2Vec-Strategie) aus **GloVe pretrained English Model** (vortrainiertes englisches GloVe-Modell), **Gensim Word2Vec** und **Gensim FastText**.

5. Wenn **Word2Vec strategy** **Gensim Word2Vec** oder **Gensim FastText** ist:

    + Treffen Sie Ihre Wahl für **Word2Vec Training Algorithm** (Word2Vec-Trainingsalgorithmus) aus **Skip_gram** und **CBOW**. Der Unterschied wird im ursprünglichen [Dokument (PDF)](https://arxiv.org/pdf/1301.3781.pdf) eingeführt.

        Die Standardmethode ist **Skip_gram**.

    + Geben Sie für **Length of word embedding** (Länge der Worteinbettung) die Dimensionalität der Wortvektoren an. Diese Einstellung entspricht dem Parameter `size` in Gensim.

        Die standardmäßige Einbettungsgröße ist 100.

    + Geben Sie für **Context window size** (Kontextfenstergröße) den maximalen Abstand zwischen dem vorhergesagten Wort und dem aktuellen Wort an. Diese Einstellung entspricht dem Parameter `window` in Gensim.

        Die Standardfenstergröße beträgt 5.

    + Geben Sie für **Number of epochs** (Anzahl der Epochen) die Anzahl der über den Korpus verlaufenden Epochen (Iterationen) an. Dies entspricht dem Parameter `iter` in Gensim.

        Die Standardanzahl der Epochen ist 5.

6. Geben Sie für die **Maximum vocabulary size** (Maximale Vokabulargröße) die maximale Anzahl der Wörter im generierten Vokabular an.

    Wenn die Anzahl der eindeutigen Wörter die maximale Anzahl übersteigt, löschen Sie die selten verwendeten Wörter.

    Die standardmäßige Vokabulargröße ist 10.000.

7. Geben Sie für **Minimum word count** (Minimale Wortanzahl) eine minimale Wortanzahl an. Die Komponente ignoriert alle Wörter, deren Häufigkeit unter diesem Wert liegt.

    Der Standardwert ist 5.

8. Übermitteln Sie die Pipeline.

## <a name="examples"></a>Beispiele

Die Komponente verfügt über eine Ausgabe:

+ **Vokabular mit Einbettungen**: Enthält das generierte Vokabular zusammen mit der Einbettung jedes Worts. Eine Dimension belegt eine Spalte.

Im folgenden Beispiel wird gezeigt, wie die Komponente „Convert Word to Vector“ funktioniert. Hierbei wird „Convert Word to Vector“ mit den Standardeinstellungen auf das vorverarbeitete Dataset „Wikipedia SP 500“ angewendet.

### <a name="source-dataset"></a>Quelldataset

Das Dataset enthält eine Kategoriespalte sowie den vollständigen Text, der von Wikipedia abgerufen wird. Die folgende Tabelle enthält nur einige repräsentative Beispiele.

|Text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>Ausgabevokabular mit Einbettungen

Die folgende Tabelle enthält die Ausgabe dieser Komponente, die das Wikipedia SP 500-Dataset als Eingabe übernimmt. In der Spalte ganz links wird das Vokabular angegeben. Dessen Einbettungsvektor wird durch die Werte der übrigen Spalten in derselben Zeile dargestellt.

|Vokabular|Embedding dim 0|Embedding dim 1|Embedding dim 2|Embedding dim 3|Embedding dim 4|Embedding dim 5|...|Embedding dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|Komponente|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|0,151542|0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|0,530901
foundation|-0,166819|0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
founder|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
location|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
campus|-0,281835|0,29312|0,106966|-0,031385|0,100777|0,061452|...|0,05978
infinite|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
loop|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

In diesem Beispiel haben wir den Standard **Gensim Word2Vec** für **Word2Vec strategy** verwendet, und **Training Algorithm** ist **Skip-gram**. **Length of word Embedding** ist 100, daher haben wir 100 Einbettungsspalten.

## <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Tipps und Antworten auf häufig gestellte Fragen.

+ Unterschied zwischen Onlinetraining und vortrainiertem Modell:

    In dieser Komponente „Convert Word to Vector“ wurden drei verschiedene Strategien bereitgestellt: zwei Onlinetrainingsmodelle und ein vortrainiertes Modell. Bei den Onlinetrainingsmodellen wird Ihr Eingabedataset als Trainingsdaten verwendet, und während des Trainings werden Vokabular und Wortvektoren generiert. Das vortrainierte Modell wird bereits von einem viel größeren Textkorpus trainiert, z. B. Wikipedia oder Twitter-Text. Das vortrainierte Modell ist tatsächlich eine Sammlung von Wort-/Einbettungspaaren.  

    Das vortrainierte GloVe-Modell fasst das Vokabular aus dem Eingabedataset zusammen und generiert für jedes Wort aus dem vortrainierten Modell einen Einbettungsvektor. Ohne Onlinetraining kann die Verwendung eines vortrainierten Modells Trainingszeit sparen. Dies bietet eine bessere Leistung, insbesondere wenn das Eingabedataset relativ klein ist.

+ Größe der Einbettung:

    Im Allgemeinen wird die Länge der Worteinbettung auf einen Wert im unteren Hunderterbereich festgelegt, z. B. 100, 200 oder 300. Eine kleine Einbettungsgröße bedeutet, dass der Vektorraum klein ist, was beim Einbetten von Wörtern zu Kollisionen führen kann.  

    Bei vortrainierten Modellen ist die Länge der Worteinbettungen fest. In diesem Beispiel beträgt die Einbettungsgröße von glove-wiki-gigaword-100 100.


## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 

Eine Liste von Fehlern, die spezifisch für die Designer-Komponenten sind, finden Sie unter [Machine Learning-Fehlercodes](designer-error-codes.md).
