---
title: Vertiefendes Lernen – Personalisierung
titleSuffix: Azure Cognitive Services
description: Die Personalisierung verwendet Informationen über Aktionen und den aktuellen Kontext, um bessere Rangfolgevorschläge zu machen. Die Informationen zu diesen Aktionen und zum Kontext sind die Attribute oder Eigenschaften, die als Features bezeichnet werden.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 6ad6414060bc744a85aaecfc8b9ceeac8dd57d30
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830756"
---
# <a name="what-is-reinforcement-learning"></a>Was ist vertiefendes Lernen?

Vertiefendes Lernen ist ein Machine Learning-Ansatz, bei dem Verhaltensweisen durch Abrufen von Feedback zu ihrer Verwendung gelernt werden.
 
Vertiefendes Lernen geschieht durch:

* Bereitstellen einer Möglichkeit oder eines Freiheitsgrads zum Ausüben eines Verhaltens – z.B. Treffen von Entscheidungen oder einer Auswahl.
* Bereitstellen von Kontextinformationen über Umgebung und Auswahl.
* Bereitstellen von Feedback darüber, wie gut mit dem Verhalten ein bestimmtes Ziel erreicht wird.

Es gibt viele Untertypen und Stile des vertiefenden Lernens, und in der Personalisierung wird dieses Konzept folgendermaßen eingesetzt:

* Ihre Anwendung bietet die Möglichkeit, einen Teil des Inhalts aus einer Liste von Alternativen anzuzeigen.
* Ihre Anwendung bietet Informationen zu jeder Alternative und dem Kontext des Benutzers.
* Ihre Anwendung berechnet eine _Belohnungsbewertung_.

Im Gegensatz zu manchen Ansätzen des vertiefenden Lernens erfordert die Personalisierung keine Simulation als Arbeitsumgebung. Ihre Lernalgorithmen sind dazu konzipiert, auf eine Außenwelt zu reagieren (im Gegensatz dazu, sie zu steuern) und von jedem Datenpunkt zu lernen, mit dem Verständnis, dass dies eine einzigartige Möglichkeit ist, deren Erstellen Zeit und Geld kostete, und dass eine mögliche Belohnung verloren geht, wenn suboptimale Leistung erfolgt.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Welche Art von Algorithmen für vertiefendes Lernen verwendet die Personalisierung?

Die aktuelle Version der Personalisierung verwendet **kontextabhängige Banditen**, einen Ansatz für vertiefendes Lernen, bei dem es um das Treffen von Entscheidungen oder Auswahl zwischen diskreten Aktionen in einem bestimmten Kontext geht.

Der _Entscheidungsspeicher_, das Modell, das trainiert wurde, um die bestmögliche Entscheidung in einem bestimmten Kontext zu treffen, verwendet eine Reihe von linearen Modellen. Mit diesen wurden wiederholt Geschäftsergebnisse erzielt, und sie sind ein bewährter Ansatz, zum Teil, weil sie sehr schnell ohne Training in vielen Durchläufen von der realen Welt lernen können, und zum Teil, weil sie überwachte Lernmodelle und Modelle tiefer neuronaler Netzwerke ergänzen können.

Die Durchsuchen/Ausbeuten-Datenverkehrszuweisung erfolgt nach dem Zufallsprinzip nach dem für das Durchsuchen festgelegten Prozentsatz, und der Standardalgorithmus für das Durchsuchen ist Epsilon-Greedy.

### <a name="history-of-contextual-bandits"></a>Verlauf der kontextabhängigen Banditen

John Langford prägte den Namen der „kontextabhängigen Banditen“ (Contextual Bandits, Langford und Zhang [2007]), um einen formbaren Teil des vertiefenden Lernens zu beschreiben, und hat an einem halben Dutzend Artikeln gearbeitet, die unser Verständnis von der Art des Lernens in diesem Paradigma verbessert haben:

* Beygelzimer et Al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer und Langford [2009]
* Li et al. [2010]

John hat auch vorher mehrere Tutorials zu Themen wie Joint Prediction (Gemeinsame Vorhersage, ICML 2015), Contextual Bandit Theory (Theorie zu kontextabhängigen Banditen, NIPS 2013), Active Learning (Aktives Lernen, ICML 2009) und Sample Complexity Bounds (Beispielkomplexitätsbegrenzungen, ICML 2003) verfasst.

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Welche Machine Learning-Rahmen verwendet die Personalisierung?

Die Personalisierung verwendet derzeit [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) als Grundlage für das Machine Learning. Dieser Rahmen ermöglicht maximalen Datendurchsatz und niedrigste Latenzzeit beim Aufstellen von Personalisierungsrangfolgen und Trainieren des Modells mit allen Ereignissen.

## <a name="references"></a>References

* [Making Contextual Decisions with Low Technical Debt](https://arxiv.org/abs/1606.03966) (Treffen von Kontextentscheidungen mit geringem technischem Aufwand)
* [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453) (Ein Reduzierungsansatz zu fairer Klassifizierung)
* [Efficient Contextual Bandits in Non-stationary Worlds](https://arxiv.org/abs/1708.01799) (Effiziente kontextabhängige Banditen in nicht stationären Welten)
* [Residual Loss Prediction: Reinforcement learning With No Incremental Feedback](https://openreview.net/pdf?id=HJNMYceCW) (Residual Loss Prediction: vertiefendes Lernen ohne inkrementelles Feedback)
* [Mapping Instructions and Visual Observations to Actions with Reinforcement Learning](https://arxiv.org/abs/1704.08795) (Zuordnungsanweisungen und visuelle Beobachtungen zu Aktionen mit vertiefendem Lernen)
* [Learning to Search Better Than Your Teacher](https://arxiv.org/abs/1502.02206) (Lernen, besser zu suchen als dein Lehrer)

## <a name="next-steps"></a>Nächste Schritte

[Offlineauswertung](concepts-offline-evaluation.md) 