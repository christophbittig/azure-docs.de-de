---
title: Anzeigen von Metriken – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Zugreifen auf Datenbankmetriken für Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620980"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>Anzeigen von Metriken in Azure Database for PostgreSQL – Hyperscale (Citus)

Ressourcenmetriken sind für jeden Knoten einer Hyperscale (Citus)-Servergruppe verfügbar und werden knotenübergreifend aggregiert.

## <a name="view-metrics"></a>Anzeigen von Metriken

Um auf Metriken für eine Hyperscale (Citus)-Servergruppe zuzugreifen, öffnen Sie im Azure-Portal unter **Überwachung** die Option **Metriken**.

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="Bildschirm mit Metriken":::

Wählen Sie eine Dimension und eine Aggregation aus, z. B. **CPU-Prozent** und **Max.** , um die über alle Knoten hinweg aggregierte Metrik anzuzeigen. Eine Erläuterung der einzelnen Metriken finden Sie [hier](concepts-hyperscale-monitoring.md#list-of-metrics).

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="Dimension auswählen":::

### <a name="view-metrics-per-node"></a>Anzeigen von Metriken pro Knoten

Das getrennte Anzeigen der Metriken einzelner Knoten im selben Diagramm wird als „Aufteilung“ bezeichnet.
Wählen Sie zum Aktivieren die Option **Aufteilung anwenden** aus:

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="Schaltfläche „Aufteilung anwenden“":::

Wählen Sie den Wert aus, nach dem aufgeteilt werden soll. Wählen Sie für Hyperscale (Citus)-Knoten die Option **Servername** aus.

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="Auswählen des Aufteilungswerts":::

Die Metriken werden nun in einer farblich codierten Linie pro Knoten dargestellt.

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="Diagrammlinien für mehrere Knoten":::

## <a name="next-steps"></a>Nächste Schritte

* [Überwachungskonzepte](concepts-hyperscale-monitoring.md) für Hyperscale (Citus)
