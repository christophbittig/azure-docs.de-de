---
title: Optimieren der Leistung von Transformationen in Zuordnungsdatenflüssen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Leistung von Transformationen in Zuordnungsdatenflüssen in Azure Data Factory- und Azure Synapse Analytics-Pipelines optimieren.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 28b7847c4341751bd7a2beacdf61e138551f991c
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293561"
---
# <a name="optimizing-transformations"></a>Optimieren von Transformationen

Wenden Sie die folgenden Strategien an, um die Leistung von Transformationen in Zuordnungsdatenflüssen in Azure Data Factory- und Azure Synapse Analytics-Pipelines zu optimieren.

## <a name="optimizing-joins-exists-and-lookups"></a>Optimieren von Joins, Exists-Transformationen und Suchvorgängen

### <a name="broadcasting"></a>Übertragung

Wenn bei Joins, Suchvorgängen und Exists-Transformationen der Arbeitsspeicher des Workerknotens groß genug für einen oder beide Datenströme ist, können Sie die Leistung optimieren, indem Sie die **Übertragung** aktivieren. Bei einer Übertragung senden Sie kleine Datenrahmen an alle Knoten im Cluster. Dies ermöglicht für die Spark-Engine die Durchführung eines Joinvorgangs, ohne dass die Daten im großen Datenstrom neu angeordnet werden. Standardmäßig entscheidet die Spark-Engine automatisch, ob eine Seite eines Joins übertragen werden soll. Wenn Sie mit Ihren eingehenden Daten vertraut sind und wissen, dass ein Datenstrom erheblich kleiner als der andere ist, können Sie für die Übertragung die Option **Feststehend** auswählen. Bei der feststehenden Übertragung wird Spark gezwungen, den ausgewählten Datenstrom zu übertragen. 

Wenn die Größe der übertragenen Daten für den Spark-Knoten zu hoch ist, tritt ggf. ein Fehler vom Typ „Nicht genügend Arbeitsspeicher“ auf. Verwenden Sie Cluster vom Typ **Arbeitsspeicheroptimiert**, um Fehler vom Typ „Nicht genügend Arbeitsspeicher“ zu vermeiden. Wenn bei den Datenflussausführungen Übertragungstimeouts auftreten, können Sie die Broadcastoptimierung deaktivieren. Dies führt jedoch zu Datenflüssen mit geringerer Leistung.

Für die Arbeit mit Datenquellen, die mehr Zeit für Abfragen erfordern (z. B. große Datenbankabfragen), wird es empfohlen, die Übertragung für Joins zu deaktivieren. Quellen mit langen Abfragezeiten können zu Spark-Timeouts führen, wenn der Cluster versucht, Daten an Computeknoten zu übertragen. Das Deaktivieren der Übertragung ist außerdem eine gute Entscheidung, wenn Sie über einen Datenstrom in Ihrem Datenfluss verfügen, der Werte zur späteren Verwendung in einer Suchtransformation aggregiert. Dieses Muster kann den Spark-Optimierer verwirren und zu Timeouts führen.

:::image type="content" source="media/data-flow/joinoptimize.png" alt-text="Optimieren der Join-Transformation":::

### <a name="cross-joins"></a>Kreuzprodukte

Wenn Sie Literalwerte in Ihren Verknüpfungsbedingungen verwenden oder auf beiden Seiten eines Joins über mehrere Übereinstimmungen verfügen, führt Spark den Joinvorgang als „Kreuzprodukt“ aus. Ein Kreuzprodukt ist ein vollständiges kartesisches Produkt, mit dem dann die verknüpften Werte herausgefiltert werden. Dieser Vorgang ist deutlich langsamer als andere Jointypen. Stellen Sie sicher, dass Sie auf beiden Seiten Ihrer Verknüpfungsbedingungen über Spaltenverweise verfügen, um eine Beeinträchtigung der Leistung zu vermeiden.

### <a name="sorting-before-joins"></a>Sortieren vor Joinvorgängen

Im Gegensatz zu „Merge Join“ in Tools wie SSIS ist die Join-Transformation kein obligatorischer Vorgang vom Typ „Merge Join“. Für die Joinschlüssel ist vor der Transformation kein Sortiervorgang erforderlich. Die Verwendung von „Transformationen sortieren“ wird in Zuordnungsdatenflüssen nicht empfohlen.

## <a name="window-transformation-performance"></a>Leistung der Fenstertransformation

Die [Fenstertransformation im Zuordnungsdatenfluss](data-flow-window.md) partitioniert die Daten nach dem Wert in Spalten, die Sie als Teil der ```over()```-Klausel in den Transformationseinstellungen auswählen. Es gibt eine Reihe sehr gängiger Aggregat- und Analysefunktionen, die in der Fenstertransformation bereitgestellt werden. Wenn Ihr Anwendungsfall aber darin besteht, ein Fenster für das gesamte Dataset für die Rangfolge ```rank()``` oder die Zeilennummer ```rowNumber()``` zu generieren, empfiehlt es sich, stattdessen die [Rangtransformation](data-flow-rank.md) und die [Transformation für Ersatzschlüssel](data-flow-surrogate-key.md) zu verwenden. Die Transformation bietet bei Verwendung dieser Funktionen eine bessere Leistung bei Vorgängen für das gesamte Dataset.

## <a name="repartitioning-skewed-data"></a>Erneutes Partitionieren bei Datenschiefe

Bei bestimmten Transformationen, z. B. Joins und Aggregatvorgängen, werden Ihre Datenpartitionen neu angeordnet und können gelegentlich zu Datenschiefe führen. Datenschiefe bedeutet, dass Daten nicht gleichmäßig auf die Partitionen verteilt werden. Bei hoher Datenschiefe kann es zu einer Verlangsamung von nachgeschalteten Transformationen und Senkenschreibvorgängen kommen. Sie können die Datenschiefe bei Ihnen während einer Datenflussausführung jederzeit überprüfen, indem Sie in der Überwachungsanzeige auf die Transformation klicken.

:::image type="content" source="media/data-flow/skewness-kurtosis.png" alt-text="Schiefe und Wölbung":::

In der Überwachungsanzeige wird angegeben, wie die Daten auf die einzelnen Partitionen verteilt sind, und die beiden Metriken für „Schiefe“ und „Wölbung“ werden angezeigt. Die **Schiefe** ist ein Maß dafür, wie asymmetrisch die Daten sind. Der Wert kann „Positiv“, „Null“, „Negativ“ oder „Nicht definiert“ lauten. Bei einer negativen Datenschiefe ist der linke Ausläufer länger als der rechte Ausläufer. Die **Wölbung** ist das Maß dafür, ob die Daten eine Verteilung mit schweren oder leichten Rändern aufweisen. Hohe Wölbungswerte sind nicht wünschenswert. Ideale Bereiche für die Datenschiefe liegen zwischen -3 und 3, und bei der Wölbung werden Werte angestrebt, die unter 10 liegen. Eine einfache Möglichkeit zur Interpretation dieser Zahlen besteht darin, im Partitionsdiagramm zu überprüfen, ob ein Balken deutlich größer als die anderen ist.

Falls Ihre Daten nach einer Transformation nicht gleichmäßig partitioniert sind, können Sie die [Registerkarte „Optimieren“](concepts-data-flow-performance.md#optimize-tab) verwenden, um sie neu zu partitionieren. Die Neuanordnung von Daten nimmt Zeit in Anspruch und führt ggf. nicht zu einer Verbesserung der Datenflussleistung.

> [!TIP]
> Wenn Sie Ihre Daten neu partitionieren, aber die Daten bei Ihnen von nachgeschalteten Transformationen dann neu angeordnet werden, sollten Sie die Hashpartitionierung für eine Spalte verwenden, die als Joinschlüssel genutzt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die Datenflussleistung](concepts-data-flow-performance.md)
- [Optimieren von Quellen](concepts-data-flow-performance-sources.md)
- [Optimieren von Senken](concepts-data-flow-performance-sinks.md)
- [Verwenden von Datenflüssen in Pipelines](concepts-data-flow-performance-pipelines.md)

Lesen Sie die folgenden Artikel zu Datenflüssen in Bezug auf die Leistung:

- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)
- [Überwachen der Datenflussleistung](concepts-data-flow-monitoring.md)
- [Leistung der Integration Runtime](concepts-integration-runtime-performance.md)
