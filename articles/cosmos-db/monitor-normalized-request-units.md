---
title: Überwachen von normalisierten RU/s für einen Azure Cosmos-Container oder ein -Konto
description: Es wird beschrieben, wie Sie für einen Vorgang die Nutzung der normalisierten Anforderungseinheiten in Azure Cosmos DB überwachen. Besitzer eines Azure Cosmos DB-Kontos können so ermitteln, für welche Vorgänge mehr Anforderungseinheiten verbraucht werden.
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: e7e52208602ed2e82fed8201d1b4ba91c295946d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593855"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Überwachen von normalisierten RU/s für einen Azure Cosmos-Container oder ein -Konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor für Azure Cosmos DB verfügt über eine Metrikansicht zum Überwachen Ihres Kontos und zum Erstellen von Dashboards. Weil die Azure Cosmos DB-Metriken standardmäßig erfasst werden, müssen Sie bei diesem Feature nichts explizit aktivieren oder konfigurieren.

Mithilfe der Metrik **Normalisierter RU-Verbrauch** wird erfasst, wie gut ausgelastet die Partitionsschlüsselbereiche im Hinblick auf den Datenverkehr sind. Azure Cosmos DB verteilt den Durchsatz gleichmäßig auf alle Partitionsschlüsselbereiche. Diese Metrik stellt eine Ansicht des maximalen Durchsatzverbrauchs für einen Partitionsschlüsselbereich pro Sekunde bereit. Verwenden Sie diese Metrik zum Berechnen der RU/s-Nutzung für den Partitionsschlüsselbereich bei einem bestimmten Container. Wenn Sie diese Metrik verwenden und ein hoher Prozentsatz der Auslastung von Anforderungseinheiten über alle Partitionsschlüsselbereiche in Azure Monitor angezeigt wird, sollten Sie den Durchsatz erhöhen, um die Anforderungen Ihrer Workload zu erfüllen. Beispiel: Die normalisierte Auslastung ist definiert als die maximale Auslastung der RU/s über alle Partitionsschlüsselbereiche. Nehmen Sie z. B. an, Ihr maximaler Durchsatz beträgt 20.000 RU/s und Sie haben zwei Partitionsschlüsselbereiche, P_1 und P_2, die jeweils auf 10.000 RU/s skaliert werden können. In einer bestimmten Sekunde, wenn P_1 6000 RUs und P_2 8000 RUs verwendet hat, ist die normalisierte Auslastung MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Was zu erwarten und zu tun ist, wenn der Wert für normalisierte RU/s höher ist

Wenn der normalisierte RU/s-Verbrauch für den angegebenen Partitionsschlüsselbereich 100 % erreicht und ein Client in diesem Zeitfenster von 1 Sekunde weiterhin Anforderungen an diesen Bereich sendet, wird ein Fehler des Typs „ Ratenbegrenzung“ gemeldet. Der Client sollte die empfohlene Wartezeit einhalten und die Anforderung wiederholen. Das SDK vereinfacht die Behandlung dieser Situation durch Wiederholung vorkonfigurierter Zeiten, indem es entsprechend wartet.  Es ist nicht erforderlich, dass der Fehler „RU-Ratenbegrenzung“ angezeigt wird, nur weil der Wert für normalisierte RU 100 % erreicht hat. Der Grund: Die normalisierte RU ist ein einzelner Wert, der die maximale Auslastung für alle Partitionsschlüsselbereiche darstellt. Ein Partitionsschlüsselbereich ist möglicherweise ausgelastet, aber die anderen Bereiche können die Anforderungen problemlos verarbeiten. Beispielsweise führt ein einzelner Vorgang wie eine gespeicherte Prozedur, die alle RU/s für einen Partitionsschlüsselbereich verbraucht, zu einer kurzen Spitze im normalisierten RU/s-Verbrauch. In solchen Fällen gibt es keine unmittelbaren Ratenbegrenzungsfehler, wenn die Anforderungsrate niedrig ist oder Anforderungen an andere Partitionen in verschiedenen Partitionsschlüsselbereichen gestellt werden. 

Anhand der Azure Monitor-Metriken können Sie die Vorgänge über den Statuscode für die SQL-API mithilfe der Metrik **Anforderungen insgesamt** einfacher ermitteln. Später können Sie diese Anforderungen nach dem Statuscode 429 filtern und nach **Vorgangstyp** aufteilen.  

Um die Anforderungen zu ermitteln, bei denen die Rate begrenzt ist, empfiehlt es sich, diese Informationen über Diagnoseprotokolle zu erfassen.

Wenn ein kontinuierlicher Spitzenwert von 100 % normalisiertem RU/s-Verbrauch vorliegt oder der Wert über mehrere Partitionsschlüsselbereiche fast 100 % beträgt, wird empfohlen, den Durchsatz zu erhöhen. Anhand der Azure Monitor-Metriken und der Azure Monitor-Diagnoseprotokolle können Sie herausfinden, welche Vorgänge einen hohen Verbrauch haben und wie hoch deren Spitzenauslastung ist.

Kurz gesagt, kann mithilfe der Metrik **Normalisierter RU-Verbrauch** erkannt werden, welcher Partitionsschlüsselbereich höher ausgelastet ist. Auf diese Weise erhalten Sie die Verzerrung des Durchsatzes auf einen Partitionsschlüsselbereich. Später können Sie die Auslastung im **PartitionKeyRUConsumption**-Protokoll in den Monitor-Protokollen nachverfolgen, um Informationen darüber zu erhalten, welche logischen Partitionsschlüssel eine hohe Auslastung aufweisen. Dadurch wird entweder die Auswahl des Partitionsschlüssels oder die Anwendungslogik geändert. Zum Auflösen der Ratenbegrenzung verteilen Sie die Datenlast beispielsweise auf mehrere Partitionen, oder erhöhen Sie einfach den Durchsatz, wenn dies wirklich erforderlich ist. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Anzeigen der Metrik für normalisierten Anforderungseinheitenverbrauch

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie auf der Navigationsleiste auf der linken Seite die Option **Monitor** und anschließend **Metrik** aus.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Bereich „Metriken“ in Azure Monitor" border="true":::

3. Klicken Sie im Bereich **Metriken** auf **Ressource auswählen**. Wählen Sie dann das erforderliche **Abonnement** und die **Ressourcengruppe** aus. Wählen Sie unter **Ressourcentyp** die Option **Azure Cosmos DB-Konten** aus. Wählen Sie dann eins der vorhandenen Azure Cosmos-Konten und anschließend **Anwenden** aus.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="Wählen Sie den Kontobereich für die Anzeige der Metriken" border="true":::

4. Als Nächstes können Sie eine Metrik aus der Liste der verfügbaren Metriken auswählen. Sie können spezifische Metriken für Anforderungseinheiten, Speicher, Wartezeit, Verfügbarkeit, Cassandra usw. auswählen. Ausführliche Informationen zu allen verfügbaren Metriken in dieser Liste finden Sie im Artikel [Metriken nach Kategorie](monitor-cosmos-db-reference.md). In diesem Beispiel wählen wir die Metrik **Normalisierter RU-Verbrauch** und **Max** als Aggregationswert aus.

   Zusätzlich zu diesen Angaben können Sie auch **Zeitbereich** und **Zeitgranularität** für die Metriken auswählen. Sie können Metriken maximal für die letzten 30 Tage anzeigen.  Nach Anwendung des Filters wird ein darauf basierendes Diagramm angezeigt.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Auswählen einer Metrik über das Azure-Portal" border="true":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filter für normalisierten Anforderungseinheitenverbrauch

Sie können Metriken und das angezeigte Diagramm auch nach bestimmten Werten für **CollectionName**, **DatabaseName**, **PartitionKeyRangeID** und **Region** filtern. Wählen Sie zum Filtern der Metriken **Filter hinzufügen** aus, und wählen Sie die erforderliche Eigenschaft (z. B. **CollectionName**) und den entsprechenden Wert aus, der Sie interessiert. Im Diagramm werden die normalisierten RU-Verbrauchseinheiten angezeigt, die für den Container im ausgewählten Zeitraum verbraucht wurden.  

Sie können Metriken mit der Option **Apply splitting** (Aufteilung anwenden) gruppieren. Bei Datenbanken mit gemeinsam genutztem Durchsatz zeigt die normalisierte RU-Metrik nur Daten auf Datenbankebene an, es werden keine Daten pro Sammlung angezeigt. Bei einer Datenbank mit gemeinsam genutztem Durchsatz werden somit keine Daten angezeigt, wenn Sie die Aufteilung nach Sammlungsname anwenden.

Die normalisierte Metrik für den Verbrauch von Anforderungseinheiten für jeden Container wird wie in der folgenden Abbildung dargestellt angezeigt:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Anwenden von Filtern auf die Metrik für normalisierten Anforderungseinheitenverbrauch" border="true":::

## <a name="next-steps"></a>Nächste Schritte

* Überwachen der Azure Cosmos DB-Daten anhand der [Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md) in Azure
* [Überwachen von Azure Cosmos DB-Vorgängen auf Steuerungsebene](audit-control-plane-logs.md)
