---
title: Azure Machine Learning – Spickzettel für Algorithmen – Designer
titleSuffix: Azure Machine Learning
description: Eine druckbare Version des Spickzettels für Machine Learning-Algorithmen, mit dem Sie den richtigen Algorithmus für Ihr Vorhersagemodell im Azure Machine Learning-Designer auswählen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 10/21/2021
adobe-target: true
ms.openlocfilehash: f14a468130dbadfc9ae6a5668a9e1c71b880e818
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564915"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Spickzettel mit Machine Learning-Algorithmen für Azure Machine Learning-Designer

Mithilfe des **Spickzettels für Azure Machine Learning-Algorithmen** können Sie im Designer den richtigen Algorithmus für ein Predictive Analytics-Modell wählen.

Azure Machine Learning bietet eine umfangreiche Bibliothek von Algorithmen der Typen ***Klassifizierung** _, _*_Empfehlungssystem_*_, _*_Clustering_*_, _*_Anomalieerkennung_*_, _*_Regression_*_ und _ *_Textanalyse_* *. Jede ist speziell auf eine andere Art von Machine Learning-Problem ausgelegt.

Weitere Informationen finden Sie unter [Auswählen von Algorithmen](how-to-select-algorithms.md).

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Herunterladen: Spickzettel für Machine Learning-Algorithmen

**Laden Sie das Cheat Sheet hier herunter: [Machine Learning – Cheat Sheet für Algorithmen (11x17 Zoll)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Spickzettel für Machine Learning-Algorithmen: Erfahren Sie, wie ein Machine Learning-Algorithmus ausgewählt wird.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.png)

Sie können das Cheat Sheet für Machine Learning-Algorithmen im Kleinformat herunterladen und drucken, um praktische Hilfe bei der Wahl eines Algorithmus zu erhalten.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Verwenden des Spickzettels für Machine Learning-Algorithmen

Die Vorschläge in diesem Cheat Sheet für Algorithmen stellen nur grobe Richtlinien dar. Einige können leicht abgeändert werden und andere sogar stark überarbeitet. Dieses Cheat Sheet dient lediglich als Ausgangspunkt und Anregung. Sie sollten auch direkte Vergleiche zwischen verschiedenen Algorithmen mit Ihren Daten durchführen. Es gibt keinen Ersatz für das grundsätzliche Verständnis der einzelnen Algorithmen und des Systems, das Ihre Daten generiert hat.

Jeder Algorithmus für maschinelles Lernen hat seinen eigenen Stil oder induktiven Bias. Für ein bestimmtes Problem können verschiedene Algorithmen geeignet sein, aber ein Algorithmus passt möglicherweise besser als andere. Es ist jedoch nicht immer schon im Vorfeld klar, welches der am besten geeignete Algorithmus ist. In diesen Fällen werden im Cheat Sheet mehrere Algorithmen zusammen angegeben. Eine geeignete Strategie bestünde darin, einen Algorithmus zu testen und bei nicht zufriedenstellenden Ergebnissen einen anderen zu versuchen. 

Rufen Sie die [Referenz zu Algorithmen und Komponenten](component-reference/component-reference.md) auf, um mehr über die Algorithmen im Azure Machine Learning-Designer zu erfahren.

## <a name="kinds-of-machine-learning"></a>Arten des maschinellen Lernens

Es gibt drei Arten von maschinellem Lernen: *beaufsichtigtes Lernen*, *unbeaufsichtigtes Lernen* und *vertiefendes Lernen*.

### <a name="supervised-learning"></a>Beaufsichtigtes Lernen

Beim beaufsichtigten Lernen wird jeder Datenpunkt bezeichnet oder einer Kategorie oder einem Wert von Interesse zugeordnet. Ein Beispiel für eine Kategoriebezeichnung ist die Zuordnung eines Bildes zu "Katze" oder "Hund". Ein Beispiel für eine Wertbezeichnung ist der Verkaufspreis für ein gebrauchtes Auto. Ziel beim beaufsichtigten Lernen ist es, viele bezeichnete Beispiele wie diese zu untersuchen und dann Vorhersagen zu zukünftigen Datenpunkten zu treffen. Zum Beispiel, um neue Fotos mit dem richtigen Tier zu identifizieren oder präzise Verkaufspreise für gebrauchte Pkw festzulegen. Dies ist eine häufige und nützliche Verwendung für das maschinelle Lernen.

### <a name="unsupervised-learning"></a>Unbeaufsichtigtes Lernen

Beim unbeaufsichtigten Lernen sind Datenpunkten keine Bezeichnungen zugeordnet. Stattdessen besteht das Ziel von Algorithmen zum unbeaufsichtigten Lernen im Organisieren der Daten in einer bestimmten Form oder in der Beschreibung ihrer Struktur. Beim unbeaufsichtigten Lernen werden Daten in Clustern gruppiert (wie bei K-Means), oder es werden andere Möglichkeiten der Darstellung komplexer Daten in einfacherer Form gesucht.

### <a name="reinforcement-learning"></a>Vertiefendes Lernen

Beim vertiefenden Lernen wählt der Algorithmus eine Aktion als Reaktion auf jeden Datenpunkt aus. Diese Vorgehensweise wird häufig in der Robotik angewendet. Dabei ist der Satz der Sensorenwerte zu einem bestimmten Zeitpunkt ein Datenpunkt, und der Algorithmus muss dann die nächste Aktion des Roboters auswählen. Diese Methode eignet sich auch für Anwendungen im Zusammenhang mit dem Internet der Dinge. Der Lernalgorithmus erhält außerdem kurz danach ein Erfolgssignal, das angibt, wie gut die Entscheidung war. Basierend auf diesem Signal ändert der Algorithmus die Strategie zum Erreichen des bestmöglichen Ergebnisses. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Auswählen von Algorithmen](how-to-select-algorithms.md).

* [Erfahren Sie mehr zu Studio in Azure Machine Learning und zum Azure-Portal](overview-what-is-azure-machine-learning.md).

* [Tutorial: Erstellen eines Vorhersagemodells im Azure Machine Learning-Designer](tutorial-designer-automobile-price-train-score.md).

* [Weitere Informationen zu Deep Learning im Vergleich zum maschinellen Lernen](concept-deep-learning-vs-machine-learning.md).
