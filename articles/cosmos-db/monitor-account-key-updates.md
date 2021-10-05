---
title: Überwachen Ihres Azure Cosmos DB-Kontos auf Updates und Erneuerungen von Schlüsseln
description: Verwenden Sie die Metrik „Kontoschlüssel aktualisiert“, um Ihr Konto auf Schlüsselupdates zu überwachen. Diese Metrik zeigt an, wie oft die primären und sekundären Schlüssel für ein Konto aktualisiert werden und wann sie geändert wurden.
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598407"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>Überwachen Ihres Azure Cosmos DB-Kontos auf Updates und Erneuerungen von Schlüsseln

Azure Monitor für Azure Cosmos DB bietet Metriken, Warnungen und Protokolle zum Überwachen Ihres Kontos. Sie können Dashboards erstellen und an Ihre Anforderungen anpassen. Da die Azure Cosmos DB-Metriken standardmäßig erfasst werden, müssen Sie nichts explizit aktivieren oder konfigurieren. Sie verwenden die Metrik **Kontoschlüssel aktualisiert**, um Ihr Konto auf Schlüsselupdates zu überwachen. Diese Metrik zeigt an, wie oft die primären und sekundären Schlüssel für ein Konto aktualisiert werden und wann sie geändert wurden. Sie können auch Warnungen einrichten, um Benachrichtigungen zu erhalten, wenn ein Schlüssel aktualisiert wird.

## <a name="monitor-key-updates-with-metrics"></a>Überwachen von Schlüsselupdates mit Metriken

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie auf der Navigationsleiste auf der linken Seite die Option **Monitor** und dann **Metrik** aus.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Bereich „Metriken“ in Azure Monitor" border="true":::

1. Wählen Sie im Bereich **Metriken** den Bereich der Ressource aus, für die Sie Metriken anzeigen möchten.

   1. Wählen Sie das erforderliche **Abonnement** und als **Ressourcentyp** die Option **Azure Cosmos DB-Konten** aus. Es wird eine Liste der Ressourcengruppen angezeigt, in denen sich die Azure Cosmos DB-Konten befinden.

   1. Wählen Sie eine **Ressourcengruppe** und dann eines Ihrer vorhandenen Azure Cosmos-Konten aus. Wählen Sie Übernehmen.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="Auswählen des Kontobereichs zum Anzeigen von Metriken" border="true":::

1. Wählen Sie für das Feld **Metrik** die Metrik **Kontoschlüssel aktualisiert** aus. Übernehmen Sie im Feld **Aggregation** den Standardwert **Anzahl**. In dieser Ansicht wird die Gesamtanzahl der Aktualisierungen des primären und sekundären Schlüssels für das ausgewählte Konto angezeigt. Sie können im Graphen auch eine Zeitskala auswählen und das Datum und die Uhrzeit anzeigen, zu der der Schlüssel aktualisiert wurde.

1. Um anzuzeigen, welcher Schlüssel geändert wurde, wählen Sie die Option **Teilung anwenden** aus. Wählen Sie **KeyType** aus, und legen Sie die Eigenschaften **Limit** und **Sort** fest. Der Graph ist jetzt nach Updates von Primär- und Sekundärschlüssel aufgeteilt, wie in der folgenden Abbildung dargestellt:

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="Metrikdiagramm zu Updates von Primär- und Sekundärschlüsseln" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>Konfigurieren von Warnungen für ein Schlüsselupdate

Möglicherweise möchten Sie ein Konto auf Schlüsselupdates überwachen. Wenn ein Schlüssel rotiert oder aktualisiert wird, müssen die abhängigen Clientanwendungen aktualisiert werden, damit sie weiterhin funktionieren. Durch das Konfigurieren von Warnungen können Sie Benachrichtigungen erhalten, wenn ein Schlüssel aktualisiert wird.

Befolgen Sie die Anweisungen im Artikel [Erstellen einer Warnung](create-alerts.md) mit einigen Anpassungen. Wählen Sie als Warnungsbedingung das Signal **Kontoschlüssel aktualisiert** aus. Wählen Sie die Dimension **KeyType** und dann **Primärer Schlüssel** oder **Sekundärer Schlüssel** aus. Je nach ausgewähltem Schlüsseltyp wird eine Warnung ausgelöst, wenn der Schlüssel aktualisiert wird.

Der folgende Screenshot zeigt, wie Sie eine Warnung vom Typ „Kritisch“ bei einer Aktualisierung der Kontoschlüssel konfigurieren:

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="Konfigurieren einer Warnung, um eine E-Mail-Benachrichtigung zu erhalten, wenn Kontoschlüssel aktualisiert werden":::

## <a name="next-steps"></a>Nächste Schritte

* Überwachen der Azure Cosmos DB-Daten anhand der [Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md) in Azure
* [Überwachen von Azure Cosmos DB-Vorgängen auf Steuerungsebene](audit-control-plane-logs.md)