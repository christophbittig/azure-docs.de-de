---
title: Auswerten der Ergebnisse von AutoML-Experimenten
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Diagramme und Metriken für jede Ausführung Ihrer Experimente des automatisierten maschinellen Lernens anzeigen und auswerten.
services: machine-learning
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: automl
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy21q2, automl
ms.openlocfilehash: 93bad9fa76940a31a26fca624507038a773bfd91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343266"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens

In diesem Artikel erfahren Sie, wie Sie Modelle, die durch Ihr Experiment für automatisiertes maschinelles Lernen (automatisiertes ML) trainiert wurden, bewerten und vergleichen können. Im Verlauf eines automatisierten ML-Experiments werden viele Ausführungen erstellt und jede Ausführung erstellt ein Modell. Für jedes Modell generiert das automatisierte maschinelle Lernen Auswertungsmetriken und Diagramme, mit denen Sie die Leistung des Modells messen können. 

Das automatisierte maschinelle Lernen generiert z. B. die folgenden Diagramme basierend auf dem Experimenttyp.

| Klassifizierung| Regression/Vorhersagen |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Konfusionsmatrix](#confusion-matrix)                       | [Residualshistogramm](#residuals)        |
| [ROC-Kurve (Receiver Operating Characteristic)](#roc-curve) | [Vorhergesagt im Vergleich zu den wahren Werten](#predicted-vs-true) |
| [Genauigkeit-Trefferquote-Kurve](#precision-recall-curve)      |                                          |
| [Prognosegütekurve](#lift-curve)                                   |                                          |
| [Kumulierte Gewinnkurve](#cumulative-gains-curve)           |                                          |
| [Kalibrierungskurve](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. (Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.)
- Ein Azure Machine Learning-Experiment, das erstellt wurde mit einer der folgenden Optionen:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (kein Code erforderlich)
  - [Python-SDK für Azure Machine Learning](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Anzeigen von Ausführungsergebnissen

Nachdem Ihr Experiment mit automatisiertem maschinellem Lernen abgeschlossen ist, können Sie einen Verlauf der Ausführungen über Folgendes finden:
  - Browser mit [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)
  - Jupyter Notebook mithilfe des [RunDetails Jupyter-Widgets](/python/api/azureml-widgets/azureml.widgets.rundetails)

Die folgenden Schritte und das Video zeigen Ihnen, wie Sie den Ausführungsverlauf sowie die Metriken und Diagramme der Modellauswertung im Studio anzeigen:

1. [Melden Sie sich beim Studio an](https://ml.azure.com/), und navigieren Sie zu Ihrem Arbeitsbereich.
1. Wählen Sie im linken Menü die Option **Experimente** aus.
1. Wählen Sie Ihr Experiment aus der Liste der Experimente aus.
1. Wählen Sie in der Tabelle unten auf der Seite eine automatisierte ML-Ausführung aus.
1. Wählen Sie auf der Registerkarte **Modelle** den **Algorithmusnamen** für das Modell aus, das Sie auswerten möchten.
1. Verwenden Sie auf der Registerkarte **Metriken** die Kontrollkästchen auf der linken Seite, um Metriken und Diagramme anzuzeigen.

![Schritte zum Anzeigen von Metriken in Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Klassifizierungsmetrik

Automatisiertes maschinelles Lernen berechnet Leistungsmetriken für jedes Klassifizierungsmodell, das für Ihr Experiment erstellt wurde. Diese Metriken basieren auf der scikit learn-Implementierung. 

Viele Klassifizierungsmetriken sind für die binäre Klassifizierung von zwei Klassen definiert und erfordern eine Mittelwerterstellung über die Klassen, um einen Score für die mehrklassige Klassifizierung zu erhalten. Scikit-learn bietet mehrere Methoden zur Mittelwerterstellung, von denen drei über das automatisierte maschinelle Lernen bereitgestellt werden: **macro**, **micro** und **weighted**.

- **Macro**: Berechnet die Metrik für jede Klasse und bildet den ungewichteten Mittelwert.
- **Micro**: Berechnet die Metrik global, indem die gesamten True Positive-, False Negative- und False Positive-Ergebnisse gezählt werden (unabhängig von den Klassen).
- **Weighted**: Berechnet die Metrik für jede Klasse und bildet den gewichteten Mittelwert basierend auf der Anzahl der Stichproben pro Klasse.

Während jede Methode zur Mittelwerterstellung ihre Vorteile hat, ist eine gemeinsame Überlegung bei der Auswahl der geeigneten Methode das Klassenungleichgewicht. Wenn Klassen eine unterschiedliche Anzahl von Stichproben aufweisen, kann es informativer sein, einen Macro-Mittelwert zu verwenden, bei dem Minderheitsklassen die gleiche Gewichtung wie Mehrheitsklassen erhalten. Weitere Informationen finden Sie unter [Gegenüberstellung von Binär- und Multiklassenmetriken beim automatisierten maschinellen Lernen](#binary-vs-multiclass-classification-metrics). 

In der folgenden Tabelle sind die Modellleistungsmetriken zusammengefasst, die automatisiertes ML für jedes Klassifizierungsmodell berechnet, das für Ihr Experiment generiert wird. Weitere Details finden Sie in der scikit-learn-Dokumentation, die im Feld **Berechnung** der jeweiligen Metrik verlinkt ist. 

> [!NOTE]
> Weitere Informationen zu Metriken für Bildklassifizierungsmodelle finden Sie im Abschnitt [Bildmetriken](#metrics-for-image-models-preview).

|Metric|BESCHREIBUNG|Berechnung|
|--|--|---|
|AUC | AUC ist die Fläche unter der [ROC-Kurve (Receiver Operating Characteristic Curve)](#roc-curve).<br><br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]<br> <br>Zu den unterstützten Metriknamen gehören <li>`AUC_macro`, das arithmetische Mittel der AUC für jede Klasse.<li> `AUC_micro`: wird durch Zählen aller True Positive-, False Negative- und False Positive-Ergebnisse berechnet. <li> `AUC_weighted`, das arithmetische Mittel des Ergebnisses für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse. <li> `AUC_binary`, der Wert von AUC, indem eine bestimmte Klasse als `true`-Klasse behandelt wird und alle anderen Klassen als `false`-Klasse kombiniert werden.<br><br>|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Die Genauigkeit ist der Anteil der Vorhersagen, die genau mit den wahren Klassenbezeichnungen übereinstimmen. <br> <br>**Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Die durchschnittliche Genauigkeit fasst eine Precision-Recall-Kurve als gewichteten Mittelwert der bei jedem Schwellenwert erzielten Genauigkeiten zusammen, wobei die Zunahme beim Recall aus dem vorherigen Schwellenwert als Gewichtung verwendet wird. <br><br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]<br> <br>Zu den unterstützten Metriknamen gehören<li>`average_precision_score_macro`, das arithmetische Mittel des durchschnittlichen Genauigkeitswerts jeder Klasse.<li> `average_precision_score_micro`: wird durch Zählen aller True Positive-, False Negative- und False Positive-Ergebnisse berechnet.<li>`average_precision_score_weighted`, das arithmetische Mittel der durchschnittlichen Genauigkeit für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse. <li> `average_precision_score_binary`, der Wert der durchschnittlichen Genauigkeit, indem eine bestimmte Klasse als `true`-Klasse behandelt wird und alle anderen Klassen als `false`-Klasse kombiniert werden.|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|„Balanced accuracy“ ist das arithmetische Mittel des Recalls für jede Klasse.<br> <br>**Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|„F1 score“ ist das harmonische Mittel aus Genauigkeit und Recall. Es ist ein gutes, ausgewogenes Maß für sowohl False Positive- als auch False Negative-Ergebnisse. True Negative-Ergebnisse werden dabei jedoch nicht berücksichtigt. <br> <br>**Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]<br> <br>Zu den unterstützten Metriknamen gehören<li>  `f1_score_macro`: Das arithmetische Mittel des F1-Ergebnisses für jede Klasse. <li> `f1_score_micro`: Wird global durch Zählen der insgesamt True Positive-, False Negative- und False Positive-Ergebnisse berechnet. <li> `f1_score_weighted`: Gewichteter Mittelwert nach Klassenhäufigkeit des F1-Ergebnisses für jede Klasse. <li> `f1_score_binary`, der Wert von f1, indem eine bestimmte Klasse als `true`-Klasse behandelt wird und alle anderen Klassen als `false`-Klasse kombiniert werden.|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Dies ist die Verlustfunktion, die bei der (multinomialen) logistischen Regression und deren Erweiterungen wie z. B. neuronalen Netzen verwendet wird. Sie ist definiert als die negative Log-Wahrscheinlichkeit der True-Bezeichnungen bei den Vorhersagen eines probabilistischen Klassifizierers. <br><br> **Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf)|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Der normalisierte Makro-Recall wird über das Recall-Makro gemittelt und normalisiert, damit die zufällige Leistung ein Ergebnis von 0 und die ideale Leistung einen Wert von 1 liefert. <br> <br>**Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>wobei `R` der erwartete Wert von `recall_score_macro` für zufällige Vorhersagen ist.<br><br>`R = 0.5`&nbsp;für&nbsp; binäre&nbsp;Klassifizierung. <br>`R = (1 / C)` für C-Klassen-Klassifizierungsprobleme.|
matthews_correlation | Der Matthews-Korrelationskoeffizient ist ein ausgewogenes Maß für die Genauigkeit, das auch dann verwendet werden kann, wenn eine Klasse viel mehr Stichproben als eine andere aufweist. Ein Koeffizient von 1 bedeutet perfekte Vorhersage, 0 zufällige Vorhersage und -1 inverse Vorhersage.<br><br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [-1, 1]|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precision (Genauigkeit)|Genauigkeit ist die Fähigkeit eines Modells, negative Stichproben nicht als positiv zu bezeichnen. <br><br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]<br> <br>Zu den unterstützten Metriknamen gehören <li> `precision_score_macro`, das arithmetische Mittel der Genauigkeit für jede Klasse. <li> `precision_score_micro`, wird global durch Zählen der insgesamt True Positive- und False Positive-Ergebnisse berechnet. <li> `precision_score_weighted`, das arithmetische Mittel der Genauigkeit für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse. <li> `precision_score_binary`, der Wert der Genauigkeit, indem eine bestimmte Klasse als `true`-Klasse behandelt wird und alle anderen Klassen als `false`-Klasse kombiniert werden.|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.precision_score.html)|
recall (Abruf)| Recall (Abruf) ist die Fähigkeit eines Modells, alle positiven Stichproben zu erkennen. <br><br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [0, 1]<br> <br>Zu den unterstützten Metriknamen gehören <li>`recall_score_macro`: Das arithmetische Mittel des Recalls für jede Klasse. <li> `recall_score_micro`: Wird global durch Zählen der insgesamt True Positive-, False Negative- und False Positive-Ergebnisse berechnet.<li> `recall_score_weighted`: Das arithmetische Mittel des Recalls für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse. <li> `recall_score_binary`, der Wert des Abrufs, indem eine bestimmte Klasse als `true`-Klasse behandelt wird und alle anderen Klassen als `false`-Klasse kombiniert werden.|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Die gewichtete Genauigkeit ist eine Genauigkeit, bei der jede Stichprobe mit der Gesamtzahl der Stichproben, die zur gleichen Klasse gehören, gewichtet wird. <br><br>**Ziel**: Je näher an 1, desto besser <br>**Bereich:** [0, 1]|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Gegenüberstellung von Binär- und Multiklassenmetriken für die Klassifizierung

Das automatisierte maschinelle Lernen erkennt automatisch, ob die Daten binär sind, und ermöglicht Benutzern auch das Aktivieren binärer Klassifizierungsmetriken, auch wenn es sich um Multiklassen-Daten handelt, indem eine `true`-Klasse angegeben wird. Multiklassen-Klassifizierungsmetriken werden unabhängig davon gemeldet, ob ein Dataset über zwei Klassen oder mehr verfügt. Binäre Klassifizierungsmetriken werden nur gemeldet, wenn die Daten binär sind oder die Benutzer die Option aktivieren. 

> [!Note]
> Wenn ein binärer Klassifizierungstask erkannt wird, suchen Sie mit `numpy.unique` den Satz von Bezeichnungen und verwenden die letzte Bezeichnung als `true`-Klasse. Da `numpy.unique` eine Sortierprozedur umfasst, ist die Auswahl der `true`-Klasse stabil.

Beachten Sie, dass Multiklassen-Klassifizierungsmetriken für die Multiklassen-Klassifizierung vorgesehen sind. Wenn eine solche Metrik auf ein binäres Dataset angewendet wird, wird keine Klasse als `true` behandelt. Metriken, die eindeutig für mehrere Klassen vorgesehen sind, sind mit dem Suffix `micro`, `macro`oder `weighted` gekennzeichnet. Beispiele hierfür sind etwa `average_precision_score`, `f1_score`, `precision_score`, `recall_score` und `AUC`. Anstatt beispielsweise den Abruf als `tp / (tp + fn)` zu berechnen, wird beim Durchschnittsabruf mit mehreren Klassen (`micro`, `macro` oder `weighted`) für beide Klassen eines Datasets mit Binärklassifizierung ein Durchschnitt gebildet. Dies entspricht der separaten Berechnung des Abrufs für die Klassen `true` und `false` und der anschließenden Bildung des Durchschnitts der beiden Klassen.

Obwohl die automatische Erkennung der binären Klassifizierung unterstützt wird, wird weiterhin empfohlen, die `true`-Klasse immer manuell anzugeben, um sicherzustellen, dass die binären Klassifizierungsmetriken für die richtige Klasse berechnet werden.

Um Metriken für binäre Klassifizierungsdatasets zu aktivieren, wenn es sich um ein Multiklassen-Dataset handelt, müssen Benutzer nur die Klasse angeben, die als `true`-Klasse behandelt werden soll, und diese Metriken werden dann berechnet.

## <a name="confusion-matrix"></a>Konfusionsmatrix

Konfusionsmatrizen bieten eine visuelle Darstellung dafür, wie ein Machine Learning-Modell systematische Fehler in seinen Vorhersagen für Klassifikationsmodelle macht. Das Wort „Konfusion“ im Namen stammt von einem Modell, das Stichproben „verwechselt“ oder falsch bezeichnet. Eine Zelle in Zeile `i` und Spalte `j` in einer Konfusionsmatrix enthält die Anzahl der Stichproben im Auswertungsdataset, die zur Klasse `C_i` gehören und vom Modell als Klasse `C_j` klassifiziert wurden.

Im Studio zeigt eine dunklere Zelle eine höhere Anzahl von Stichproben an. Durch Auswahl der Ansicht **Normalisiert** in der Dropdownliste wird jede Matrixzeile normalisiert, um den Prozentsatz der Klasse `C_i` anzuzeigen, der als Klasse `C_j` vorhergesagt wird. Der Vorteil der Standardansicht der **Rohdaten** ist, dass Sie sehen können, ob ein Ungleichgewicht in der Verteilung der tatsächlichen Klassen dazu geführt hat, dass das Modell Stichproben aus der Minderheitsklasse falsch klassifiziert hat, wobei es sich um ein häufiges Problem in unausgeglichenen Datasets handelt.

Die Konfusionsmatrix eines guten Modells wird die meisten Stichproben entlang der Diagonalen aufweisen.

### <a name="confusion-matrix-for-a-good-model"></a>Konfusionsmatrix für ein gutes Modell 
![Konfusionsmatrix für ein gutes Modell ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Konfusionsmatrix für ein ungültiges Modell
![Konfusionsmatrix für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-Kurve

Die ROC-Kurve (Receiver Operating Characteristic) stellt das Verhältnis zwischen der True Positive- (TPR) und der False Positive-Rate (FPR) dar, wenn sich der Entscheidungsschwellenwert ändert. Die ROC-Kurve kann weniger aussagekräftig sein, wenn Modelle auf der Grundlage von Datasets mit stark unausgeglichenen Klassen trainiert werden, da die Beiträge von Minderheitsklassen ggf. durch die Mehrheitsklasse verdrängt wird.

Der Bereich unter der Kurve (AUC) kann als der Anteil der richtig klassifizierten Stichproben interpretiert werden. Genauer gesagt, ist der AUC die Wahrscheinlichkeit, dass der Klassifizierer eine zufällig ausgewählte positive Stichprobe höher einstuft als eine zufällig ausgewählte negative Stichprobe. Die Form der Kurve gibt eine Vorstellung von der Beziehung zwischen TPR und FPR in Abhängigkeit vom Klassifizierungsschwellenwert oder der Entscheidungsgrenze.

Eine Kurve, die sich der oberen linken Ecke des Diagramms nähert, nähert sich einer TPR von 100 % und FPR von 0 %, dem bestmöglichen Modell. Ein zufälliges Modell würde eine ROC-Kurve entlang der `y = x`-Linie von der linken unteren Ecke bis zur rechten oberen Ecke erzeugen. Ein Modell, das schlechter als ein zufälliges Modell ist, würde eine ROC-Kurve aufweisen, die unter die `y = x`-Linie eintaucht.
> [!TIP]
> Für Klassifizierungsexperimente kann jedes der Liniendiagramme, die für automatisierte ML-Modelle erstellt werden, verwendet werden, um das Modell pro Klasse oder gemittelt über alle Klassen auszuwerten. Sie können zwischen diesen verschiedenen Ansichten umschalten, indem Sie auf die Klassenbezeichnungen in der Legende auf der rechten Seite des Diagramms klicken.

### <a name="roc-curve-for-a-good-model"></a>ROC-Kurve für ein gutes Modell
![ROC-Kurve für ein gutes Modell](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC-Kurve für ein ungültiges Modell
![ROC-Kurve für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Kurve zu Genauigkeit/Abruf

Die Kurve zu Genauigkeit/Abruf stellt das Verhältnis zwischen Genauigkeit und Abruf bei Änderung des Entscheidungsschwellwerts dar. Abruf ist die Fähigkeit eines Modells, alle positiven Stichproben zu erkennen und Genauigkeit ist die Fähigkeit eines Modells, negative Stichproben nicht als positiv zu bezeichnen. Einige geschäftliche Probleme erfordern möglicherweise einen höheren Abruf und andere eine höhere Genauigkeit, abhängig von der relativen Wichtigkeit der Vermeidung von False Negative- gegenüber False Positive-Ergebnissen.
> [!TIP]
> Für Klassifizierungsexperimente kann jedes der Liniendiagramme, die für automatisierte ML-Modelle erstellt werden, verwendet werden, um das Modell pro Klasse oder gemittelt über alle Klassen auszuwerten. Sie können zwischen diesen verschiedenen Ansichten umschalten, indem Sie auf die Klassenbezeichnungen in der Legende auf der rechten Seite des Diagramms klicken.
### <a name="precision-recall-curve-for-a-good-model"></a>Kurve zu Genauigkeit/Abruf für ein gutes Modell
![Kurve zu Genauigkeit/Abruf für ein gutes Modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Kurve zu Genauigkeit/Abruf für ein ungültiges Modell
![Kurve zu Genauigkeit/Abruf für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Kumulierte Gewinnkurve

Die kumulative Gewinnkurve stellt den Prozentsatz der richtig klassifizierten positiven Stichproben als Funktion des Prozentsatzes der betrachteten Stichproben dar, wobei wir die Stichproben in der Reihenfolge der vorhergesagten Wahrscheinlichkeit betrachten.

Um den Gewinn zu berechnen, sortieren Sie zunächst alle Stichproben von der höchsten zur niedrigsten vom Modell vorhergesagten Wahrscheinlichkeit. Nehmen Sie dann `x%` der höchstmöglichen Vorhersagen. Teilen Sie die Anzahl der in diesem `x%` erkannten positiven Stichproben durch die Gesamtzahl der positiven Stichproben, um den Gewinn zu erhalten. Der kumulative Gewinn ist der Prozentsatz an positiven Stichproben, die wir erkennen, wenn wir einen gewissen Prozentsatz der Daten berücksichtigen, der mit hoher Wahrscheinlichkeit zur positiven Klasse gehört.

Ein perfektes Modell wird alle positiven Stichproben über alle negativen Stichproben stellen, was eine kumulative Gewinnkurve ergibt, die aus zwei geraden Segmenten besteht. Die erste ist eine Linie mit der Steigung `1 / x` von `(0, 0)` bis `(x, 1)`, wobei `x` der Anteil der Stichproben ist, die zur positiven Klasse gehören (`1 / num_classes`, wenn die Klassen ausgeglichen sind). Die zweite ist eine horizontale Linie von `(x, 1)` bis `(1, 1)`. Im ersten Segment werden alle positiven Stichproben richtig klassifiziert und der kumulative Gewinn wechselt innerhalb der ersten `x%` der betrachteten Stichproben zu `100%`.

Das Baseline-Zufallsmodell hat eine kumulative Gewinnkurve, die `y = x` folgt, wobei für `x%` der betrachteten Stichproben nur etwa `x%` der gesamten positiven Stichproben erkannt wurden. Ein perfektes Modell für ein ausgeglichenes Dataset verfügt über eine Micro-Mittelwertkurve und über eine Macro-Mittelwertkurve mit der Steigung `num_classes`, bis der kumulative Gewinn 100 % beträgt, und dann horizontal verläuft, bis der Datenprozentwert 100 entspricht.
> [!TIP]
> Für Klassifizierungsexperimente kann jedes der Liniendiagramme, die für automatisierte ML-Modelle erstellt werden, verwendet werden, um das Modell pro Klasse oder gemittelt über alle Klassen auszuwerten. Sie können zwischen diesen verschiedenen Ansichten umschalten, indem Sie auf die Klassenbezeichnungen in der Legende auf der rechten Seite des Diagramms klicken.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Kumulative Gewinnkurve für ein gutes Modell
![Kumulative Gewinnkurve für ein gutes Modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Kumulative Gewinnkurve für ein ungültiges Modell
![Kumulative Gewinnkurve für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Prognosegütekurve

Die Prognosegütekurve zeigt, wie viel besser ein Modell im Vergleich zu einem Zufallsmodell abschneidet. Prognosegüte ist definiert als das Verhältnis des kumulativen Gewinns zum kumulativen Gewinn eines Zufallsmodells (die immer `1` sein sollte).

Diese relative Leistungsangabe berücksichtigt, dass die Klassifizierung mit zunehmender Klassenanzahl schwieriger wird. (Ein Zufallsmodell sagt bei einem Dataset mit zehn Klassen einen höheren Anteil von Stichproben falsch vorher als bei einem Dataset mit zwei Klassen.)

Die Baseline-Prognosegütekurve ist die `y = 1`-Linie, bei der die Modellleistung mit der eines Zufallsmodells übereinstimmt. Im Allgemeinen wird die Prognosegütekurve für ein gutes Modell in diesem Diagramm höher und weiter von der X-Achse entfernt sein, was zeigt, dass das Modell, wenn es in seinen Vorhersagen am zuversichtlichsten ist, um ein Vielfaches besser abschneidet als bloßes Raten.

> [!TIP]
> Für Klassifizierungsexperimente kann jedes der Liniendiagramme, die für automatisierte ML-Modelle erstellt werden, verwendet werden, um das Modell pro Klasse oder gemittelt über alle Klassen auszuwerten. Sie können zwischen diesen verschiedenen Ansichten umschalten, indem Sie auf die Klassenbezeichnungen in der Legende auf der rechten Seite des Diagramms klicken.
### <a name="lift-curve-for-a-good-model"></a>Prognosegütekurve für ein gutes Modell
![Prognosegütekurve für ein gutes Modell](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Prognosegütekurve für ein ungültiges Modell
![Prognosegütekurve für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibrierungskurve

Die Kalibrierungskurve stellt die Konfidenz eines Modells in Bezug auf seine Vorhersagen im Vergleich zum Anteil positiver Stichproben auf den jeweiligen Konfidenzebenen dar. Ein gut kalibriertes Modell wird 100 % der Vorhersagen richtig klassifizieren, denen es eine Konfidenz von 100 % zuweist, 50 % der Vorhersagen, denen es eine Konfidenz von 50 % zuweist, 20 % der Vorhersagen, denen es eine Konfidenz von 20 % zuweist usw. Ein perfekt kalibriertes Modell hat eine Kalibrierungskurve, die der `y = x`-Linie folgt, in der das Modell die Wahrscheinlichkeit, dass Stichproben zu den einzelnen Klassen gehören, perfekt vorhersagt.

Bei einem übermäßig zuverlässigen Modell werden Wahrscheinlichkeiten übermäßig nah bei Null bzw. Eins vorhergesagt, sodass hinsichtlich der Klasse der jeweiligen Stichproben selten eine Unsicherheit besteht, und die Kalibrierungskurve wird ähnlich wie ein rückwärtsgerichtet „S“ aussehen. Bei einem Modell mit zu geringer Konfidenz wird der vorhergesagten Klasse im Durchschnitt eine geringere Wahrscheinlichkeit zugewiesen, und die zugehörige Kalibrierungskurve wird ähnlich wie ein „S“ aussehen. Die Kalibrierungskurve stellt nicht die Fähigkeit eines Modells zur richtigen Klassifizierung dar, sondern seine Fähigkeit, seinen Vorhersagen die richtige Konfidenz zuzuordnen. Ein ungültiges Modell kann immer noch eine geeignete Kalibrierungskurve aufweisen, wenn das Modell eine geringe Konfidenz und eine hohe Unsicherheit richtig zuweist.

> [!NOTE]
> Die Kalibrierungskurve ist hinsichtlich der Anzahl der Stichproben empfindlich, sodass ein kleiner Validierungssatz zu verrauschten Ergebnissen führen kann, die schwer zu interpretieren sein können. Dies bedeutet nicht unbedingt, dass das Modell nicht richtig kalibriert ist.

### <a name="calibration-curve-for-a-good-model"></a>Kalibrierungskurve für ein gutes Modell
![Kalibrierungskurve für ein gutes Modell](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Kalibrierungskurve für ein ungültiges Modell
![Kalibrierungskurve für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Regressions-/Vorhersagemetriken

Das automatisierte maschinelle Lernen berechnet dieselben Leistungsmetriken für jedes erzeugte Modell, unabhängig davon, ob es sich um ein Regressions- oder Vorhersageexperiment handelt. Diese Metriken werden auch normalisiert, um einen Vergleich zwischen Modellen zu ermöglichen, die für Daten mit unterschiedlichen Bereichen trainiert wurden. Weitere Informationen finden Sie unter [Metrische Normalisierung](#metric-normalization).  

In der folgenden Tabelle sind die für die Regressions- und Vorhersageexperimente generierten Modellleistungsmetriken zusammengefasst. Wie die Klassifizierungsmetriken basieren auch diese Metriken auf den scikit learn-Implementierungen. Die entsprechende scikit learn-Dokumentation ist entsprechend verlinkt, und zwar im Feld **Berechnung**.

|Metrik|BESCHREIBUNG|Berechnung|
--|--|--|
explained_variance|Die erläuterte Varianz misst das Ausmaß, in dem ein Modell die Variation in der Zielvariablen erläutert. Hierbei handelt es sich um den prozentualen Rückgang der Varianz der ursprünglichen Daten im Vergleich zur Varianz der Fehler. Wenn der Mittelwert der Fehler 0 beträgt, entspricht er dem Ermittlungskoeffizienten (siehe r2_score weiter unten). <br> <br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** (-inf, 1]|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Der mittlere absolute Fehler ist der erwartete Wert des absoluten Differenzwerts zwischen dem Ziel und der Vorhersage.<br><br> **Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf) <br><br> Typen: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error geteilt durch den Bereich der Daten. | [Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Der mittlere absolute prozentuale Fehler (Mean Absolute Percentage Error, MAPE) ist ein Maß für die durchschnittliche Differenz zwischen einem vorhergesagten Wert und dem tatsächlichen Wert.<br><br> **Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf) ||
median_absolute_error|Der mittlere absolute Fehler ist der Median aller absoluten Differenzen zwischen dem Ziel und der Vorhersage. Dieser Verlust ist stabil in Bezug auf Ausreißer.<br><br> **Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf)<br><br>Typen: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error geteilt durch den Bereich der Daten. |[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (der Bestimmungskoeffizient) misst die proportionale Reduzierung der mittleren quadratischen Abweichung (MQA) in Relation zur Gesamtvarianz der betrachteten Daten. <br> <br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [-1, 1]<br><br>Hinweis: R<sup>2</sup> umfasst häufig den Bereich (-inf, 1]. Da die MQA größer als die beobachtete Varianz sein kann, kann R<sup>2</sup> je nach Daten und Modellvorhersagen beliebig große negative Werte haben. Gemeldete R<sup>2</sup>-Scores werden vom automatisierten maschinellen Lernen auf „-1“ beschnitten, sodass der Wert „-1“ für R<sup>2</sup> wahrscheinlich bedeutet, dass der tatsächliche R<sup>2</sup>-Score kleiner als „-1“ ist. Berücksichtigen Sie die anderen Metrikwerte und die Eigenschaften der Daten, wenn Sie einen negativen R<sup>2</sup>-Score interpretieren.|[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Die Wurzel aus dem mittleren quadratischen Fehler (Root Mean Squared Error, RMSE) ist die Quadratwurzel der erwarteten quadratischen Differenz zwischen dem Ziel und der Vorhersage. Für einen ausgewogenen Schätzer ist der RMSE gleich der Standardabweichung.<br> <br> **Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf)<br><br>Typen:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error geteilt durch den Bereich der Daten. |[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Die Wurzel aus dem mittleren quadratischen logarithmischen Fehler ist die Quadratwurzel des erwarteten quadratischen logarithmischen Fehlers.<br><br>**Ziel**: Je näher an 0, desto besser <br> **Bereich:** [0, inf) <br> <br>Typen: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error geteilt durch den Bereich der Daten.  |[Berechnung](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Die Spearman-Korrelation ist ein nicht parametrisches Maß für die Monotonie der Beziehung zwischen zwei Datasets. Im Gegensatz zur Pearson-Korrelation geht die Spearman-Korrelation nicht davon aus, dass beide Datasets normal verteilt sind. Wie bei anderen Korrelationskoeffizienten variiert Spearman zwischen -1 und 1, wobei 0 für keine Korrelation steht. Korrelationen von -1 oder 1 implizieren eine exakt monotone Beziehung. <br><br> Bei Spearman handelt es sich um eine Korrelationsmetrik der Rangfolge, was bedeutet, dass Änderungen an vorhergesagten oder tatsächlichen Werten das Spearman-Ergebnis nicht verändern, wenn sie die Rangfolge der vorhergesagten oder tatsächlichen Werte nicht verändern.<br> <br> **Ziel**: Je näher an 1, desto besser <br> **Bereich:** [-1, 1]|[Berechnung](https://docs.scipy.org/doc/scipy-1.5.2/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Metrische Normalisierung

Das automatisierte maschinelle Lernen normalisiert Regressions- und Vorhersagemetriken, was den Vergleich zwischen Modellen ermöglicht, die für Daten mit unterschiedlichen Bereichen trainiert wurden. Ein Modell, das für Daten mit einem größeren Bereich trainiert wurde, weist einen höheren Fehlerwert auf als dasselbe Modell, das für Daten mit einem kleineren Bereich trainiert wurde, es sei denn, dieser Fehler wird normalisiert.

Es gibt zwar keine Standardmethode zur Normalisierung von Fehlermetriken, aber beim automatisierten maschinellen Lernen wird der Fehler üblicherweise durch den Bereich der Daten geteilt: `normalized_error = error / (y_max - y_min)`

>[!Note]
>Der Bereich der Daten wird nicht mit dem Modell gespeichert. Wenn Sie mit dem selben Modell einen Rückschluss für einen Testsatz für zurückgehaltene Daten ziehen, können sich `y_min` und `y_max` entsprechend den Testdaten ändern und die normalisierten Metriken möglicherweise nicht direkt verwendet werden, um die Leistung der Modelle für Trainings- und Testsätze zu vergleichen. Sie können den Wert von `y_min` und `y_max` aus Ihrem Trainingssatz weitergeben, um den Vergleich fair zu gestalten.

Wenn ein Prognosemodell anhand von Zeitreihendaten ausgewertet wird, unternimmt das automatisierte maschinelle Lernen zusätzliche Schritte, um sicherzustellen, dass die Normalisierung pro Zeitreihen-ID (Körnung) erfolgt, da jede Zeitreihe wahrscheinlich eine andere Verteilung der Zielwerte aufweist.
## <a name="residuals"></a>Restdaten

Das Restdatendiagramm ist ein Histogramm der Vorhersagefehler (Restdaten), die für Regressions- und Vorhersageexperimente generiert wurden. Die Restdaten werden als `y_predicted - y_true` für alle Stichproben berechnet und dann als Histogramm angezeigt, um die Modellverzerrung zu zeigen.

In diesem Beispiel ist zu beachten, dass beide Modelle leicht verzerrt sind und einen niedrigeren als den tatsächlichen Wert vorhersagen. Dies ist nicht ungewöhnlich für ein Dataset mit einer ungleichmäßigen Verteilung der tatsächlichen Ziele, weist aber auf eine schlechtere Modellleistung hin. Ein gutes Modell besitzt eine Restdatenverteilung, die ihren Höhepunkt bei Null hat, mit wenigen Restdaten bei den Extremen. Ein weniger gutes Modell besitzt eine weit auseinander liegende Restdatenverteilung mit weniger Stichproben um Null herum.

### <a name="residuals-chart-for-a-good-model"></a>Restdatendiagramm für ein gutes Modell
![Restdatendiagramm für ein gutes Modell](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Restdatendiagramm für ein ungültiges Modell
![Restdatendiagramm für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Vorhergesagt im Vergleich zu den wahren Werten

Bei Regressions- und Vorhersageexperimenten stellt das Diagramm „Vorhergesagt im Vergleich zu den wahren Werten“ die Beziehung zwischen der Zielfunktion (wahre/tatsächliche Werte) und den Vorhersagen des Modells dar. Die wahren Werte werden entlang der X-Achse klassifiziert und für jede Klassifizierung wird der mittlere vorhergesagte Wert mit Fehlerindikatoren dargestellt. So können Sie erkennen, ob ein Modell bei der Vorhersage bestimmter Werte verzerrt ist. Die Linie zeigt die durchschnittliche Vorhersage und der schattierte Bereich zeigt die Varianz der Vorhersagen um diesen Mittelwert an.

Oft wird der häufigste wahre Wert die genauesten Vorhersagen mit der geringsten Varianz aufweisen. Der Abstand der Trendlinie von der Ideallinie `y = x`, bei der es nur wenige wahre Werte gibt, ist ein gutes Maß für die Modellleistung bei Ausreißern. Sie können das Histogramm am unteren Rand des Diagramms verwenden, um Rückschlüsse auf die tatsächliche Datenverteilung zu ziehen. Das Einbeziehen von weiteren Datenstichproben, bei denen die Verteilung gering ist, kann die Leistung des Modells bei unbekannten Daten verbessern.

In diesem Beispiel ist zu beachten, dass das bessere Modell eine Linie für „Vorhergesagt im Vergleich zu den wahren Werten“ aufweist, die näher an der idealen `y = x`-Linie liegt.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Diagramm zu „Vorhergesagt im Vergleich zu den wahren Werten“ für ein gutes Modell
![Diagramm zu „Vorhergesagt im Vergleich zu den wahren Werten“ für ein gutes Modell](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Diagramm zu „Vorhergesagt im Vergleich zu den wahren Werten“ für ein ungültiges Modell
![Diagramm zu „Vorhergesagt im Vergleich zu den wahren Werten“ für ein ungültiges Modell](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="metrics-for-image-models-preview"></a>Metriken für Bildmodelle (Vorschau)

Automatisiertes ML verwendet die Bilder aus dem Validierungsdataset, um die Leistung des Modells auszuwerten. Die Leistung des Modells wird auf **Epochenebene** gemessen, um zu verstehen, wie das Training verläuft. Eine Epoche vergeht, wenn ein gesamtes Dataset das neuronale Netz genau einmal vorwärts und rückwärts durchlaufen hat. 

### <a name="image-classification-metrics"></a>Bildklassifizierungsmetriken

Die primäre Metrik zur Auswertung ist **Genauigkeit** für binäre und mehrklassige Klassifizierungsmodelle und **IoU** ([Intersection over Union](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.jaccard_score.html#sklearn.metrics.jaccard_score)) für Multitagklassifizierungsmodelle.
Die Klassifizierungsmetriken für Bildklassifizierungmodelle sind mit denen identisch, die im Abschnitt [Klassifizierungsmetriken](#classification-metrics) definiert sind. Die einer Epoche zugeordneten Verlustwerte werden ebenfalls protokolliert, was dabei helfen kann, den Trainingsfortschritt zu überwachen und zu ermitteln, ob das Modell über- oder unterangepasst ist.

Jede Vorhersage eines Klassifizierungsmodells wird einer Zuverlässigkeitsbewertung zugeordnet, welche den Grad der Zuverlässigkeit angibt, mit der die Vorhersage getroffen wurde. Multitag-Bildklassifizierungsobjekte werden standardmäßig mit einem Bewertungsschwellenwert von 0,5 ausgewertet. Dies bedeutet, dass nur Vorhersagen mit mindestens diesem Grad an Zuverlässigkeit als eine für die zugeordnete Klasse positive Vorhersage betrachtet werden. Die Multiklassenklassifizierung verwendet keinen Bewertungsschwellenwert, stattdessen wird die Klasse mit der maximalen Zuverlässigkeitsbewertung als Vorhersage betrachtet. 

#### <a name="epoch-level-metrics-for-image-classification"></a>Metriken auf Epochenebene für die Bildklassifizierung
Anders als die Klassifizierungsmetriken für tabellarische Datasets protokollieren Bildungklassifizierungsmodelle alle Klassifizierungsmetriken wie unten dargestellt auf Epochenebene.

![Diagramme auf Epochenebene für die Bildklassifizierung](./media/how-to-understand-automated-ml/image-classification-accuracy.png)

#### <a name="summary-metrics-for-image-classification"></a>Zusammenfassungsmetriken für die Bildklassifizierung

Abgesehen von den skalaren Metriken, die auf Epochenebene protokolliert werden, protokollieren Bildklassifizierungsmodelle auch Zusammenfassungsmetriken wie die [Konfusionsmatrix](#confusion-matrix) und [Klassifizierungsdiagramme](#roc-curve) wie ROC-Kurven, Kurven zu Genauigkeit und Abruf sowie Klassifizierungsberichte für das Modell aus der besten Epoche, in der die höchste Bewertung der primären Metrik (Genauigkeit) erhalten wird.

Wie unten dargestellt bietet der Klassifizierungsbericht Werte auf Klassenebene für Metriken wie precision, recall, f1-score, support, auc und average_precision mit verschiedenen Methoden zur Mittelwerterstellung: macro, micro und weighted.
Weitere Informationen finden Sie in den Metrikdefinitionen im Abschnitt [Klassifizierungsmetriken](#classification-metrics).

![Klassifizierungsbericht für die Bildklassifizierung](./media/how-to-understand-automated-ml/image-classification-report.png)

### <a name="object-detection-and-instance-segmentation-metrics"></a>Objekterkennung und Instanzsegmentierungsmetriken

Jede Vorhersage aus einem Bildobjekterkennungs- oder Instanzsegmentierungsmodell wird einer Zuverlässigkeitsbewertung zugeordnet.
Die Vorhersagen mit einer Zuverlässigkeitsbewertung, die höher ist als der Bewertungsschwellenwert, werden als Vorhersagen ausgegeben und in der Metrikberechnung verwendet, deren Standardwert modellspezifisch ist und auf die über die Seite zur [Hyperparameteroptimierung](how-to-auto-train-image-models.md#model-specific-hyperparameters) (Hyperparameter `box_score_threshold`) verwiesen werden kann.

Die Metrikberechnung einer Bildobjekterkennung und eines Instanzsegmentierungsmodells basiert auf einer Überlappungsmessung, die von der Metrik **IoU** ([Intersection over Union](https://en.wikipedia.org/wiki/Jaccard_index)) definiert wird. Diese wird berechnet, indem der Bereich der Überlappung zwischen den Lerndaten und den Vorhersagen durch die Vereinigungsmenge der Lerndaten und den Vorhersagen geteilt wird. Die aus jeder Vorhersage berechnete IoU wird mit einem **Überlappungsschwellenwert** verglichen, der als IoU-Schwellenwert bezeichnet wird und bestimmt, wie viel eine Vorhersage mit benutzerannotierten Lerndaten überschneiden soll, um als positive Vorhersage betrachtet zu werden. Wenn der aus der Vorhersage berechnete IoU-Wert kleiner als der Überlappungsschwellenwert ist, wird die Vorhersage für die zugeordnete Klasse nicht als positiv betrachtet.

Die primäre Metrik zum Auswerten von Bildobjekterkennungs- und Instanzsegmentierungsmodellen ist die **Mean Average Precision (mAP)** . Die mAP ist der durchschnittliche Wert der durchschnittlichen Genauigkeit (Average Precision, AP) für alle Klassen. Objekterkennungsmodelle für automatisiertes ML unterstützen die Berechnung von mAP mithilfe der folgenden gängigen Methoden.

**Pascal VOC-Metriken**: 

[Pascal VOC](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/htmldoc/devkit_doc.html#SECTION00044000000000000000)-mAP ist die standardmäßige Art der mAP-Berechnung für Objekterkennungs- / Instanzsegmentierungsmodelle. Die mAP-Methode im Pascal-VOC-Stil berechnet den Bereich unter einer Version der Genauigkeits-Abruf-Kurve. Als erstes wird p(rᵢ), bei dem es sich um die Genauigkeit beim Abruf (i) handelt, für alle eindeutigen Abrufwerte berechnet. p(rᵢ) wird dann durch die maximale Genauigkeit ersetzt, die für jeden Abruf r' >= rᵢ abgerufen wird. Der Genauigkeitswert nimmt in dieser Version der Kurve monoton ab. Die Metrik Pascal VOC-maP wird standardmäßig mit einem IoU-Schwellenwert von 0,5 ausgewertet. Eine ausführliche Erläuterung dieses Konzepts finden Sie in diesem [Blog](https://jonathan-hui.medium.com/map-mean-average-precision-for-object-detection-45c121a31173).


**COCO-Metriken**: 

[COCO-Auswertungsmethoden](https://cocodataset.org/#detection-eval) verwenden eine interpolierte 101-Punkt-Methode für die Berechnung der durchschnittlichen Genauigkeit zusammen mit einer Mittelwerterstellung von mehr als zehn IoU-Schwellenwerten. AP@[.5:.95] entspricht der durchschnittlichen Genauigkeit für IoU von 0,5 bis 0,95 mit einer Schrittgröße von 0,05. Automatisiertes ML protokolliert alle zwölf Metriken, die von der COCO-Methode definiert werden, einschließlich der AP und AR in verschiedenen Skalierungen in den Anwendungsprotokollen, während auf der Benutzeroberfläche für Metriken nur die mAP mit einem IoU-Schwellenwert von 0,5 angezeigt wird. 

> [!TIP]
> Die Auswertung des Bildobjekterkennungsmodells kann COCO-Metriken verwenden, wenn der Hyperparameter `validation_metric_type` wie im Abschnitt zur [Hyperparameteroptimierung](how-to-auto-train-image-models.md#task-specific-hyperparameters) erläutert auf „COCO“ festgelegt ist.

#### <a name="epoch-level-metrics-for-object-detection-and-instance-segmentation"></a>Metriken auf Epochenebene für Objekterkennung und Instanzsegmentierung
Die mAP-, Genauigkeits- und Abrufwerte werden auf Epochenebene für Bildobjekterkennungs-/ Instanzsegmentierungsmodelle protokolliert. Die mAP-, Genauigkeits- und Abrufmetriken werden auch auf Klassenebene mit dem Namen „per_label_metrics“ protokolliert. Die „per_label_metrics“ sollten als Tabelle angezeigt werden. 

> [!NOTE]
> Metriken auf Epochenebene für Genauigkeit, Abruf und „per_label_metrics“ sind bei der Verwendung der „COCO“-Methode nicht verfügbar.

![Diagramme auf Epochenebene für die Objekterkennung](./media/how-to-understand-automated-ml/image-object-detection-map.png)

## <a name="model-explanations-and-feature-importances"></a>Modellerläuterung und Featurerelevanz

Während Metriken und Diagramme zur Modellauswertung gut geeignet sind, um die allgemeine Qualität eines Modells zu messen, ist die Überprüfung, welche Datasetfunktionen ein Modell für seine Vorhersagen verwendet hat, für eine verantwortungsvolle KI unerlässlich. Deshalb bietet das automatisierte maschinelle Lernen ein Modellerklärungsdashboard zum Messen und Melden der relativen Beiträge von Datasetfeatures. Informationen zum Anzeigen des Erklärungsdashboards in Azure Machine Learning Studio finden Sie [hier](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

Einen Code First-Ansatz zum Einrichten von Modellerklärungen für automatisierte ML-Experimente mit dem Azure Machine Learning Python SDK finden Sie [hier](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Interpretierbarkeit, die Erklärung des besten Modells, ist nicht für Vorhersageexperimente mit automatisiertem maschinellem Lernen verfügbar, die die folgenden Algorithmen als bestes Modell oder Ensemble empfehlen: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Durchschnitt 
> * Naiv
> * Saisonaler Durchschnitt 
> * Saisonal naiv

## <a name="next-steps"></a>Nächste Schritte
* Probieren Sie die Beispielnotebooks für die [Modellerklärung zum automatisierten Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) aus.
* Bei Fragen zum automatisierten maschinellen Lernen wenden Sie sich an askautomatedml@microsoft.com.
