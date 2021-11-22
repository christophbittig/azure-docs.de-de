---
title: Verwalten von Nutzung und Kosten für Azure Monitor-Protokolle
description: Hier erfahren Sie, wie Sie in Azure Monitor den Tarif ändern und das Datenvolumen sowie die Aufbewahrungsrichtlinie für Ihren Log Analytics-Arbeitsbereich verwalten.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2021
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37b6411dbddd92f9a0ba8ba8908b1f848d761683
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319698"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Monitor-Protokolle ermitteln und steuern. In einem verwandten Artikel, [Überwachen der Nutzung und der geschätzten Kosten](../usage-estimated-costs.md), wird erläutert, wie die Nutzung und geschätzten Kosten über mehrere Azure-Überwachungsfeatures hinweg für unterschiedliche Preismodelle angezeigt werden. Alle Preise und Kosten in diesem Artikel dienen nur zu Beispielzwecken. 

Azure Monitor-Protokolle sind für die Skalierung und Unterstützung der täglichen Sammlung, Indizierung und Speicherung enormer Datenmengen aus beliebigen Quellen in Ihrem Unternehmen oder aus in Azure bereitgestellten Quellen konzipiert.  Auch wenn dies die primäre Motivation für die Verwendung in Ihrer Organisation ist, geht es jedoch letztendlich um Kosteneffizienz. In diesem Zusammenhang ist es wichtig zu wissen, dass die Kosten eines Log Analytics-Arbeitsbereichs nicht nur auf dem Umfang der gesammelten Daten basieren, sondern auch davon abhängen, welcher Tarif gewählt wurde und wie lange die von den verbundenen Quellen generierten Daten gespeichert werden.  

In diesem Artikel wird beschrieben, wie Sie das erfasste Datenvolumen und den Speicherzuwachs proaktiv überwachen können. Sie erfahren außerdem, wie Grenzwerte definiert werden, um die damit verbundenen Kosten zu kontrollieren. 

## <a name="pricing-model"></a>Preismodell

Log Analytics nutzt ein **nutzungsbasiertes Zahlungsmodell** auf Grundlage des verbrauchten Datenvolumens. Darüber hinaus wird die optionale längere Datenaufbewahrung berücksichtigt. Das Datenvolumen wird als Größe der gespeicherten Daten in GB (10^9 Bytes) gemessen. Jeder Log Analytics-Arbeitsbereich wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt. Die bei der Erfassung anfallende Datenmenge kann erheblich sein und hängt von folgenden Faktoren ab: 

  - Den aktivierten Verwaltungslösungen und deren Konfiguration
  - Anzahl und Typ der überwachten Ressourcen
  - Typ der Daten, die von jeder überwachten Ressource gesammelt wurden
  
Zusätzlich zum Modell der nutzungsbasierten Bezahlung bietet Log Analytics **Mindestabnahme-Tarife**. Mit diesen Tarifen können Sie Einsparungen von bis zu 30 Prozent gegenüber der nutzungsbasierten Zahlung erzielen. Bei der Mindestabnahme verpflichten Sie sich dazu, eine Datenerfassung ab 100 GB/Tag zu einem niedrigeren Preis als bei der nutzungsbasierten Zahlung zu erwerben. Jegliche Nutzung über die Mindestabnahme hinaus (Überschreitung) wird zu demselben Preis pro GB wie bei der aktuellen Mindestabnahme abgerechnet. Bei Mindestabnahmen besteht ein Verpflichtungszeitraum von 31 Tagen. Während dieses Verpflichtungszeitraums können Sie zu einer höheren Mindestabnahme wechseln (hierbei beginnt der 31-tägige Verpflichtungszeitraum erneut), aber es ist erst nach Ablauf des Verpflichtungszeitraums möglich, zur nutzungsbasierten Zahlung oder zu einer niedrigeren Mindestabnahme zurückzukehren. Die Abrechnung für Mindestabnahmen erfolgt täglich. [Erfahren Sie mehr](https://azure.microsoft.com/pricing/details/monitor/) über die Tarife „Nutzungsbasierte Zahlung“ und „Mindestabnahme“ in Log Analytics. 

> [!NOTE]
> Ab dem 2. Juni 2021 werden **Kapazitätsreservierungen** als **Mindestabnahmen** bezeichnet. Erfasste Daten oberhalb der Mindestabnahme (Überschreitung) werden nun zu demselben Preis pro GB wie bei der aktuellen Mindestabnahme abgerechnet. Dadurch verringern sich die Kosten im Vergleich zur alten Abrechnungsmethode für nutzungsbasierte Zahlung, und Benutzer mit großen Datenvolumen müssen ihre Mindestabnahmestufe nicht immer wieder nachjustieren. Außerdem wurden drei neue Mindestabnahmestufen hinzugefügt: 1000, 2000 und 5000 GB/Tag. 

In allen Tarifen wird die Datengröße eines Ereignisses anhand einer Zeichenfolgendarstellung der Eigenschaften berechnet, die in Log Analytics für dieses Ereignis gespeichert werden, unabhängig davon, ob die Daten von einem Agent gesendet oder während des Erfassungsvorgangs hinzugefügt werden. Dies schließt alle [benutzerdefinierten Felder](custom-fields.md) ein, die beim Sammeln von Daten hinzugefügt und anschließend in Log Analytics gespeichert werden. Mehrere Eigenschaften, die alle Datentypen gemein haben, einschließlich einiger [Log Analytics-Standardeigenschaften](./log-standard-columns.md), werden bei der Berechnung der Ereignisgröße nicht berücksichtigt. Dazu gehören `_ResourceId`, `_SubscriptionId`, `_ItemId`, `_IsBillable`, `_BilledSize` und `Type`. Alle anderen Eigenschaften, die in Log Analytics gespeichert werden, sind in der Berechnung der Ereignisgröße enthalten. Bei einigen Datentypen fallen keinerlei Gebühren für die Datenerfassung an, wie z. B. den Typen [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity), [Heartbeat](/azure/azure-monitor/reference/tables/heartbeat), [Nutzung](/azure/azure-monitor/reference/tables/usage) und [Vorgang](/azure/azure-monitor/reference/tables/operation). Einige Lösungen verfügen über lösungsspezifische Richtlinien für die kostenlose Datenerfassung, z. B. macht [ Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/) Abhängigkeitsvisualisierungsdaten für die ersten 180 Tage einer Serverbewertung kostenlos. Um zu ermitteln, ob ein Ereignis von der Abrechnung für die Datenerfassung ausgeschlossen wurde, können Sie die Eigenschaft [_IsBillable](log-standard-columns.md#_isbillable) verwenden, wie [unten](#data-volume-for-specific-events) gezeigt. Die Nutzung wird in GB (10^9 Bytes) angegeben. 

Für einige Lösungen wie [Microsoft Defender für Cloud](https://azure.microsoft.com/pricing/details/azure-defender/), [Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) und [Konfigurationsverwaltung](https://azure.microsoft.com/pricing/details/automation/) gelten zudem eigene Preismodelle. 

### <a name="log-analytics-dedicated-clusters"></a>Dedizierte Log Analytics-Cluster

[Dedizierte Log Analytics-Cluster](logs-dedicated-clusters.md) sind Sammlungen von Arbeitsbereichen in einem einzelnen verwalteten Azure Data Explorer-Cluster, die erweiterte Szenarien unterstützen, z. B. [kundenseitig verwaltete Schlüssel](customer-managed-keys.md). Für Dedizierte Log Analytics-Cluster gilt dasselbe Mindestabnahme-Tarifmodell wie für Arbeitsbereiche, abgesehen davon, dass ein Cluster eine Mindestabnahme von mindestens 500 GB/Tag haben muss. Für Cluster gibt es keine nutzungsbasierte Bezahlung. Die Mindestabnahme für Cluster weist einen Verpflichtungszeitraum von 31 Tagen nach Erhöhung der Mindestabnahme auf. Während des Verpflichtungszeitraums kann die Mindestabnahme nicht herabgesetzt, aber jederzeit erhöht werden. Wenn Arbeitsbereiche einem Cluster zugeordnet sind, erfolgt die Abrechnung der Datenerfassung für diese Arbeitsbereiche auf Clusterebene anhand der konfigurierten Mindestabnahme. Erfahren Sie mehr über das [Erstellen eines Log Analytics-Clusters](customer-managed-keys.md#create-cluster) und das [Zuordnen von Arbeitsbereichen zu diesem Cluster](customer-managed-keys.md#link-workspace-to-cluster). Informationen zum Tarif für Mindestabnahmen finden Sie auf der Seite [Azure Monitor – Preise]( https://azure.microsoft.com/pricing/details/monitor/).

Die Mindestabnahme für Cluster wird mit Azure Resource Manager anhand des Parameters `Capacity` unter `Sku` programmgesteuert konfiguriert. `Capacity` wird in GB-Einheiten angegeben und kann Werte von 500, 1000, 2000 oder 5000 GB/Tag aufweisen. Jegliche Nutzung über die Mindestabnahme hinaus (Überschreitung) wird zu demselben Preis pro GB wie bei der aktuellen Mindestabnahme abgerechnet.  Weitere Informationen finden Sie unter [Kundenseitig verwalteter Schlüssel für Azure Monitor](customer-managed-keys.md).

Für die Abrechnung des Verbrauchs in einem Cluster stehen zwei Modi zur Verfügung. Diese können beim [Erstellen eines Clusters](logs-dedicated-clusters.md#create-a-dedicated-cluster) durch den Parameter `billingType` angegeben oder nach der Erstellung festgelegt werden. Die beiden Modi sind: 

- **Cluster** (Standardeinstellung): In diesem Fall werden erfasste Daten auf der Clusterebene abgerechnet. Die erfassten Datenmengen aus den einzelnen Arbeitsbereichen, die einem Cluster zugeordnet sind, werden aggregiert, um die tägliche Abrechnung für den Cluster zu berechnen. Zuordnungen pro Knoten von [Microsoft Defender für Cloud](../../security-center/index.yml) werden vor dieser Aggregation von Daten in allen Arbeitsbereichen im Cluster auf Arbeitsbereichsebene angewendet. 

- **Arbeitsbereiche**: Die Mindestabnahmekosten für Ihren Cluster werden den Arbeitsbereichen im Cluster proportional gemäß dem jeweiligen Datenerfassungsvolumen zugeordnet (nach Berücksichtigung der knotenspezifischen Zuordnungen von [Microsoft Defender für Cloud](../../security-center/index.yml) für jeden Arbeitsbereich). Wenn das gesamte Datenvolumen, das an einem Tag in einem Cluster erfasst wird, unter der Mindestabnahme liegt, werden für die einzelnen Arbeitsbereiche jeweils die erfassten Daten zum effektiven GB-basierten Mindestabnahmesatz abgerechnet. Hierbei wird ein Teil der Mindestabnahme abgerechnet, und der ungenutzte Teil der Mindestabnahme wird für die Clusterressource in Rechnung gestellt. Wenn das gesamte Datenvolumen, das an einem Tag in einem Cluster erfasst wird, über der Mindestabnahme liegt, wird für die einzelnen Arbeitsbereiche jeweils ein Teil der Mindestabnahme (basierend auf dem Anteil der an diesem Tag erfassten Daten) abgerechnet, und für jeden Arbeitsbereich wird jeweils ein Teil der erfassten Daten in Rechnung gestellt, die über die Mindestabnahme hinausgehen. Wenn das gesamte Datenvolumen, das an einem Tag in einem Arbeitsbereich erfasst wird, oberhalb der Mindestabnahme liegt, wird nichts für die Clusterressource abgerechnet.

In Clusterabrechnungsoptionen wird die Datenaufbewahrung für die einzelnen Arbeitsbereiche abgerechnet. Die Clusterabrechnung beginnt mit der Clustererstellung, unabhängig davon, ob dem Cluster Arbeitsbereiche zugeordnet sind. Arbeitsbereiche, die einem Cluster zugeordnet sind, weisen keinen eigenen Tarif mehr auf.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Schätzen der Kosten für die Verwaltung Ihrer Umgebung 

Wenn Sie noch keine Azure Monitor-Protokolle verwenden, können Sie mit dem [Azure Monitor-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=monitor) die Kosten für die Nutzung von Log Analytics schätzen. Geben Sie in das **Suchen**-Feld „Azure Monitor“ ein und wählen Sie dann die entsprechende Azure Monitor-Kachel aus. Scrollen Sie auf der Seite nach unten zu **Azure Monitor**, und erweitern Sie den Abschnitt **Log Analytics**. Hier können Sie die Datenmenge in GB eingeben, die Sie erfassen möchten. Wenn Sie bereits Azure Monitor-Protokolle auswerten, können Sie die Datenstatistiken aus Ihrer eigenen Umgebung verwenden. Unten wird erläutert, wie die [Anzahl der überwachten VMs](#understanding-nodes-sending-data) und das [erfasste Datenvolumen im Arbeitsbereich](#understanding-ingested-data-volume) bestimmt werden. Wenn Sie Log Analytics noch nicht verwenden, finden Sie hier einige Anleitungen zum Schätzen von Datenmengen:

1. **Überwachen von VMs:** Bei aktivierter typischer Überwachung werden pro überwachter VM monatlich 1 GB bis 3 GB Daten erfasst. 
2. **Überwachen von Azure Kubernetes Service(AKS)-Clustern:** Details zu den erwarteten Datenvolumes für die Überwachung eines typischen AKS-Clusters finden Sie [hier](../containers/container-insights-cost.md#estimating-costs-to-monitor-your-aks-cluster). Befolgen Sie diese [Best Practices](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost), um die Überwachungskosten Ihres AKS-Clusters zu kontrollieren. 
3. **Anwendungsüberwachung:** Der Azure Monitor-Preisrechner enthält einen Datenvolumenschätzer basierend auf dem Verbrauch Ihrer Anwendung sowie einer statistischen Analyse der durch Application Insights erfassten Datenmengen. Aktivieren Sie im Abschnitt „Application Insights“ des Preisrechners den Schalter neben „Schätzen des Datenvolumens basierend auf der Anwendungsaktivität“, um die Schätzung zu verwenden. 

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Anzeigen des Log Analytics-Verbrauchs auf Ihrer Azure-Rechnung 

Die einfachste Art, Ihren in Rechnung gestellten Verbrauch für einen bestimmten Log Analytics-Arbeitsbereich anzuzeigen, besteht darin, die Seite **Übersicht** des Arbeitsbereichs zu besuchen und in der rechten oberen Ecke des Bereichs „Essentials“ am Seitenanfang auf **View Cost** (Kosten anzeigen) zu klicken. Dadurch wird die Kostenanalyse von Azure Cost Management + Billing gestartet, die bereits auf diesen Arbeitsbereich begrenzt ist.  

Alternativ können Sie im Hub [Azure Cost Management + Billing](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) beginnen. Hier können Sie die Funktion „Kostenanalyse“ verwenden, um Ihre Ausgaben für Azure-Ressourcen anzuzeigen. Zum Verfolgen Ihrer Log Analytics-Ausgaben fügen Sie einen Filter nach „Ressourcentyp“ (zu „microsoft.operationalinsights/workspace“ für Log Analytics oder „microsoft.operationalinsights/cluster“ für Log Analytics-Cluster) hinzu. Wählen Sie unter **Gruppieren nach** die Option **Kategorie der Verbrauchseinheit** oder **Verbrauchseinheit** aus. Andere Dienste, z. B. Microsoft Defender für Cloud und Microsoft Sentinel, rechnen ihre Nutzung auch anhand der Log Analytics-Arbeitsbereichsressourcen ab. Um die Zuordnung zum Dienstnamen zu sehen, können Sie die Tabellenansicht anstelle eines Diagramms auswählen. 

Um Ihren Verbrauch besser zu verstehen, können Sie [Ihre Verbrauchsdaten aus dem Azure-Portal herunterladen](../../cost-management-billing/understand/download-azure-daily-usage.md). In der heruntergeladenen Kalkulationstabelle wird der Verbrauch pro Azure-Ressource (beispielsweise Log Analytics-Arbeitsbereich) pro Tag aufgeführt. In dieser Excel-Tabelle können Sie den Verbrauch für Ihre Log Analytics-Arbeitsbereiche ermitteln, indem Sie zuerst nach der Spalte „Kategorie der Verbrauchseinheit“ filtern, um „Log Analytics“, „Insight & Analytics“ (von einigen der Legacytarife verwendet) und „Azure Monitor“ (von Tarifen für Mindestabnahmen verwendet) anzuzeigen, und dann in der Spalte „Instanz-ID“ einen Filter namens „contains workspace“ (enthält Arbeitsbereich) oder „contains cluster“ (enthält Cluster) hinzufügen (Letzterer dient zum Einbeziehen der Log Analytics-Clusternutzung). Der Verbrauch wird in der Spalte „Verbrauchte Menge“ aufgeführt, und die Einheit für jeden Eintrag wird in der Spalte „Maßeinheit“ angegeben. Weitere Informationen unter [Überprüfen Ihrer individuellen Rechnung für das Azure-Abonnement](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="understand-your-usage-and-optimizing-your-pricing-tier"></a>Verstehen Ihres Verbrauchs und Optimieren Ihres Tarifs
<a name="understand-your-usage-and-estimate-costs"></a>

Verwenden Sie **Log Analytics Usage and Estimated Costs** (Log Analytics-Nutzung und geschätzte Kosten), um mehr über Ihre Nutzungstrends zu erfahren und den kosteneffizientesten Log Analytics-Tarif auszuwählen. Dort wird angezeigt, wie viel Daten von jeder Lösung gesammelt sowie wie viele Daten behalten werden. Auch wird eine Schätzung Ihrer Kosten für jeden Tarif angezeigt, die auf Ihren aktuellen Datenerfassungsmustern basiert. 

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Nutzung und geschätzte Kosten":::

Um Ihre Dateien ausführlicher zu untersuchen, wählen Sie auf der Seite **Nutzung und geschätzte Kosten** das Symbol in der oberen rechten Ecke einer der beiden Tabellen. Sie können mit dieser Abfrage weitere Details zu Ihrer Nutzung abrufen.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Ansicht „Protokolle“":::

Auf der Seite **Nutzung und geschätzte Kosten** können Sie Ihr Datenvolumen für den Monat überprüfen. Dieses beinhaltet alle abrechenbaren Daten, die in Ihrem Log Analytics-Arbeitsbereich empfangen und aufbewahrt wurden.  
 
Die Gebühren für Log Analytics fließen in Ihre Azure-Rechnung ein. Die Details Ihrer Azure-Rechnung finden Sie im Bereich **Abrechnung** des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions).  

## <a name="changing-pricing-tier"></a>Ändern des Tarifs

Gehen Sie folgendermaßen vor, um den Log Analytics-Tarif Ihres Arbeitsbereichs zu ändern:

1. Öffnen Sie in Ihrem Arbeitsbereich im Azure-Portal **Nutzung und geschätzte Kosten**. Hier wird eine Liste aller Tarife angezeigt, die für diesen Arbeitsbereich verfügbar sind.

2. Überprüfen Sie die geschätzten Kosten für die einzelnen Tarife. Diese Schätzungen basierend auf der Nutzung der letzten 31 Tage, d. h. bei dieser Kostenschätzung wird davon ausgegangen, dass die letzten 31 Tage repräsentativ für Ihre typische Nutzung sind. Im Beispiel unten können Sie sehen, dass die Kosten für diesen Arbeitsbereich basierend auf den Datenmustern der letzten 31 Tage im Modell der nutzungsbasierten Zahlung (Nr. 1) geringer wären als bei einer Mindestabnahme von 100 GB/Tag (Nr. 2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Tarife":::
    
3. Wählen Sie nach Überprüfung der geschätzten Kosten basierend auf der Nutzung der letzten 31 Tage **Auswählen** aus, wenn Sie sich für einen Wechsel des Tarifs entscheiden.  

### <a name="changing-pricing-tier-via-arm"></a>Ändern des Tarifs über ARM

Sie können den [Tarif auch über Azure Resource Manager festlegen](./resource-manager-workspace.md). Dabei verwenden Sie das `sku`-Objekt, um den Tarif festzulegen, und den Parameter `capacityReservationLevel`, wenn der Tarif `capacityresrvation` ist. (Erfahren Sie mehr über das [Festlegen von Arbeitsbereichseigenschaften über ARM](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces?tabs=json#workspacesku-object).) Nachfolgend sehen Sie eine beispielhafte Azure Resource Manager-Vorlage zum Festlegen einer Mindestabnahme (in Resource Manager als `capacityreservation` bezeichnet) von 300 GB/Tag für Ihren Arbeitsbereich. 

```
{
  "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "YourWorkspaceName",
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "location": "yourWorkspaceRegion",
      "properties": {
                    "sku": {
                      "name": "capacityreservation",
                      "capacityReservationLevel": 300
                    }
      }
    }
  ]
}
```

Wenn Sie diese Vorlage in PowerShell verwenden möchten, melden Sie sich nach der [Installation des Azure Az PowerShell-Moduls](/powershell/azure/install-az-ps) mit `Connect-AzAccount` bei Azure an, wählen Sie mit `Select-AzSubscription -SubscriptionId YourSubscriptionId` das Abonnement aus, das Ihren Arbeitsbereich enthält, und wenden Sie die Vorlage an (in einer Datei namens „template.json“ gespeichert):

```
New-AzResourceGroupDeployment -ResourceGroupName "YourResourceGroupName" -TemplateFile "template.json"
```

Um den Tarif auf andere Werte festzulegen, z. B. nutzungsbasierte Zahlung (für die SKU als `pergb2018` bezeichnet), lassen Sie die Eigenschaft `capacityReservationLevel` weg. Erfahren Sie mehr über das [Erstellen von ARM-Vorlagen](../../azure-resource-manager/templates/template-tutorial-create-first-template.md), das [Hinzufügen einer Ressource zu Ihrer Vorlage](../../azure-resource-manager/templates/template-tutorial-add-resource.md) und das [Anwenden von Vorlagen](../resource-manager-samples.md). 

### <a name="tracking-pricing-tier-changes"></a>Überwachen von Tarifänderungen

Tarifänderungen für einen Arbeitsbereich werden im [Aktivitätsprotokoll](../essentials/activity-log.md) mit einem Event mit dem Vorgang „Arbeitsbereich erstellen“ aufgezeichnet. Die Registerkarte **Änderungsverlauf** des Events zeigt die alten und neuen Tarife in der Zeile `properties.sku.name` an.  Klicken Sie in Ihrem Arbeitsbereich auf die Option „Aktivitätsprotokoll“, um Events anzuzeigen, die auf einen bestimmten Arbeitsbereich begrenzt sind. Sie können eine Benachrichtigung für den Vorgang „Arbeitsbereich erstellen“ erstellen, um die Änderungen der Tarife zu überwachen. 

## <a name="legacy-pricing-tiers"></a>Legacytarife

Abonnements, die am 2. April 2018 einen Log Analytics-Arbeitsbereich oder eine Application Insights Ressource enthielten oder mit einem Enterprise Agreement verknüpft sind, das vor dem 1. Februar 2019 begann und noch immer aktiv ist, haben weiterhin Zugriff auf die Legacytarife **Kostenlose Testversion**, **Eigenständig (pro GB)** und **Pro Knoten (OMS)** . Für Arbeitsbereiche im Tarif „kostenlose Testversion“ ist die tägliche Datenerfassung auf 500 MB beschränkt (mit Ausnahme von Sicherheitsdatentypen, die von [Microsoft Defender für Cloud](../../security-center/index.yml) gesammelt werden). Darüber hinaus ist die Datenaufbewahrung auf 7 Tage beschränkt. Der Tarif „Kostenlose Testversion“ dient nur zu Evaluierungszwecken. Für den Free-Tarif wird keine SLA bereitgestellt.  Für Arbeitsbereiche im Tarif „Eigenständig“ oder „Pro Knoten“ gilt eine vom Benutzer festlegbare Datenaufbewahrungsfrist von 30 bis 730 Tagen.

Die Nutzung des Tarifs „Eigenständig“ wird anhand des erfassten Datenvolumens abgerechnet. Dieses wird im **Log Analytics**-Dienst gemeldet, und die Verbrauchseinheit heißt „Analysierte Daten“. 

Der Tarif „Pro Knoten“ wird pro überwachter VM (Knoten) mit einer Granularität von einer Stunde berechnet. Für jeden überwachten Knoten werden dem Arbeitsbereich 500 MB Daten pro Tag zugeteilt, die nicht berechnet werden. Diese Zuteilung wird auf Stundenbasis berechnet und täglich auf Arbeitsbereichsebene aggregiert. Daten, die über die aggregierte tägliche Datenzuteilung hinaus erfasst werden, werden pro GB als Datenüberschreitung berechnet. Der Dienst wird auf Ihrer Rechnung als **Insight & Analytics** für die Log Analytics-Nutzung ausgewiesen, wenn für den Arbeitsbereich der Tarif „Pro Knoten“ festgelegt ist. Die Nutzung wird mit drei Verbrauchseinheiten gemeldet:

- **Knoten**: der Verbrauch für die Anzahl der überwachten Knoten (VMs) in Einheiten von Knotenmonaten.
- **Datenüberschreitung pro Knoten**: Anzahl von GB an Daten, die über die aggregierte Datenzuordnung hinaus erfasst wurden.
- **Pro Knoten enthaltene Daten**: Menge der erfassten Daten, die durch die aggregierte Datenzuordnung abgedeckt wurden. Diese Verbrauchseinheit wird auch verwendet, wenn der Arbeitsbereich in allen Tarifen verfügbar ist, um die Menge der von Microsoft Defender für Cloud abgedeckten Daten anzuzeigen.

> [!TIP]
> Falls für Ihren Arbeitsbereich Zugriff auf den Tarif **Pro Knoten** besteht und Sie sich die Frage stellen, ob die Kosten beim Tarif mit nutzungsbasierter Zahlung niedriger wären, können Sie mit der [unten angegebenen Abfrage](#evaluating-the-legacy-per-node-pricing-tier) leicht eine Empfehlung abrufen. 

Vor April 2016 erstellte Arbeitsbereiche haben weiterhin Zugriff auf die ursprünglichen Tarife **Standard** und **Premium**, für die eine feste Datenaufbewahrung von 30 bzw. 365 Tagen gilt. Neue Arbeitsbereiche können nicht im Tarif **Standard** oder **Premium** erstellt werden, und wenn ein Arbeitsbereich aus diesen Tarifen verschoben wird, kann er nicht zurück verschoben werden. Die Verbrauchseinheiten für die Datenerfassung werden bei diesen veralteten Tarifen auf Ihrer Azure-Rechnung als „Analysierte Daten“ bezeichnet.

### <a name="legacy-pricing-tiers-and-microsoft-defender-for-cloud"></a>Legacy-Tarife und Microsoft Defender für Cloud

Es gibt auch einige Besonderheiten bei der Verwendung von Log Analytics-Legacy-Tarifen und der Berechnung der Nutzung für [Microsoft Defender für Cloud](../../security-center/index.yml). 

- Wenn für den Arbeitsbereich der Legacy-Tarif „Standard“ oder „Premium“ gilt, wird Microsoft Defender für Cloud nur für die Log Analytics-Datenerfassung und nicht pro Knoten berechnet.
- Wenn sich der Arbeitsbereich im Legacy-Tarif „Pro Knoten“ befindet, wird Microsoft Defender für Cloud mithilfe des aktuellen [knotenbasierten Microsoft Defender für Cloud-Preismodells](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet. 
- Wenn Microsoft Defender für Cloud vor dem 19. Juni 2017 aktiviert wurde, wird Microsoft Defender für Cloud in anderen Tarifen (einschließlich Mindestabnahmetarifen) nur die Log Analytics-Datenerfassung in Rechnung gestellt. Andernfalls wird Microsoft Defender für Cloud mit dem aktuellen knotenbasierten Preismodell von Microsoft Defender für Cloud abgerechnet.

Weitere Details zu den Einschränkungen der Tarife finden Sie unter [Azure-Abonnement- und -Dienstgrenzen, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Keiner der Legacytarife bietet regionsbezogene Preise.  

> [!NOTE]
> Wenn Sie die Berechtigungen nutzen möchten, die Sie durch den Kauf der OMS E1-Suite, OMS E2-Suite oder des OMS-Add-Ons für System Center erwerben, wählen Sie den Tarif *Pro Knoten* für Log Analytics aus.

## <a name="log-analytics-and-microsoft-defender-for-cloud"></a>Log Analytics und Microsoft Defender für Cloud

Die Abrechnung von [Microsoft Defender für Server (Defender für Cloud)](../../security-center/index.yml) ist eng an die Log Analytics-Abrechnung gebunden. Microsoft Defender für Cloud [rechnet anhand der Anzahl überwachter Dienste](https://azure.microsoft.com/pricing/details/azure-defender/) ab und bietet eine Datenzuordnung von 500 MB/Server/Tag, die auf eine Teilmenge von [Sicherheitsdatentypen](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) und die Datentypen „Update“ und „UpdateSummary“ angewendet wird, wenn die Lösung für die Updateverwaltung nicht im Arbeitsbereich ausgeführt wird oder die Zielgruppenadressierung aktiviert ist. Weitere Informationen finden Sie [hier](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance). Die Anzahl der überwachten Server wird pro Stunde berechnet. Die täglichen Datenzuordnungsbeiträge von jedem überwachten Server werden auf Arbeitsbereichsebene aggregiert. Wenn sich der Arbeitsbereich in der alten Preisstufe "Pro Knoten" befindet, werden die Zuweisungen für Microsoft Defender für Cloud und Log Analytics kombiniert und gemeinsam auf alle abrechenbaren erfassten Daten angewendet.  

## <a name="change-the-data-retention-period"></a>Ändern des Datenaufbewahrungszeitraums

Die folgenden Schritte zeigen, wie Sie die Aufbewahrungsdauer von Protokolldaten in Ihrem Arbeitsbereich konfigurieren. Die Datenaufbewahrung auf Arbeitsbereichsebene kann für alle Arbeitsbereiche von 30 bis 730 Tagen (2 Jahre) konfiguriert werden, es sei denn, sie verwenden den Legacytarif „kostenlose Testversion“. Die Aufbewahrung einzelner Datentypen kann bis zu 4 Tage kurz festgelegt werden. Informieren Sie sich über die [Preise](https://azure.microsoft.com/pricing/details/monitor/) für eine längere Datenaufbewahrung. Um Daten länger als 730 Tage aufzubewahren, können Sie den [Export von Daten aus dem Log Analytics-Arbeitsbereich](logs-data-export.md) in Erwägung ziehen.

### <a name="workspace-level-default-retention"></a>Standardaufbewahrung auf Arbeitsbereichsebene

So legen Sie die Standardaufbewahrungsdauer für Ihren Arbeitsbereich fest:
 
1. Wählen Sie im linken Bereich des Azure-Portals über Ihren Arbeitsbereich **Nutzung und geschätzte Kosten** aus.
2. Wählen Sie im oberen Bereich der Seite **Nutzungs- und geschätzte Kosten** die Option **Datenaufbewahrung** aus.
3. Passen Sie dort mithilfe des Schiebereglers die Anzahl von Tagen an, und wählen Sie anschließend **OK** aus.  Wenn Sie sich im Tarif *kostenlose Testversion* befinden, können Sie den Datenaufbewahrungszeitraum nicht ändern. Sie müssen in einen kostenpflichtigen Tarif wechseln, um diese Einstellung zu ändern.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Ändern des Datenaufbewahrungszeitraums für den Arbeitsbereich":::

Wenn die Aufbewahrungsdauer herabgesetzt wird, besteht eine Toleranzperiode von mehreren Tagen, bevor Daten, die älter als die Datenaufbewahrungseinstellung sind, entfernt werden. 

Auf der Seite **Datenaufbewahrung** können Sie die Einstellung für die Datenaufbewahrung auf 30, 31, 60, 90, 120, 180, 270, 365, 550 und 730 Tage festlegen. Wenn eine andere Einstellung erforderlich ist, kann diese mit [Azure Resource Manager](./resource-manager-workspace.md) und dem Parameter `retentionInDays` konfiguriert werden. Wenn Sie die Datenaufbewahrung auf 30 Tage festlegen, können Sie mit dem Parameter `immediatePurgeDataOn30Days` eine sofortige Bereinigung älterer Daten auslösen (und damit den Toleranzzeitraum außer Kraft setzen). Dies kann in Szenarien nützlich sein, in denen für die Compliance das direkte Entfernen von Daten erforderlich ist. Diese sofortige Bereinigung wird ausschließlich über Azure Resource Manager verfügbar gemacht. 

Arbeitsbereiche mit einer Datenaufbewahrung von 30 Tagen behalten Daten möglicherweise tatsächlich für 31 Tage bei. Wenn es zwingend erforderlich ist, dass Daten nur 30 Tage lang aufbewahrt werden, legen Sie mit Azure Resource Manager und dem Parameter `immediatePurgeDataOn30Days` die Datenaufbewahrung auf 30 Tage fest.  

Standardmäßig werden zwei Datentypen – `Usage` und `AzureActivity` – kostenlos für mindestens 90 Tage aufbewahrt. Wenn die Aufbewahrung des Arbeitsbereichs auf mehr als 90 Tage verlängert wird, wird auch die Aufbewahrung dieser Datentypen verlängert. Für diese Datentypen werden auch keine Gebühren für die Datenerfassung erhoben. 

Datentypen von arbeitsbereichsbasierten Application Insights-Ressourcen (`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` und `AppTraces`) werden ebenfalls standardmäßig 90 Tage lang kostenlos aufbewahrt. Die jeweilige Aufbewahrungsdauer kann mithilfe der Funktion für die Aufbewahrung nach Datentyp angepasst werden. 

Die [Bereinigungs-API](/rest/api/loganalytics/workspacepurge/purge) von Log Analytics hat keine Auswirkungen auf die Aufbewahrungsabrechnung und ist für die Verwendung in einer sehr begrenzten Anzahl von Fällen vorgesehen. Um Ihre Aufbewahrungsrechnung zu verringern, muss die Aufbewahrungsdauer entweder für den Arbeitsbereich oder für bestimmte Datentypen gesenkt werden. Weitere Informationen finden Sie im [Leitfaden für personenbezogene Daten, die in Log Analytics und Application Insights gespeichert sind](./personal-data-mgmt.md).

### <a name="retention-by-data-type"></a>Aufbewahrung nach Datentyp

Es ist auch möglich, unterschiedliche Aufbewahrungseinstellungen für einzelne Datentypen zwischen 4 und 730 Tagen anzugeben (mit Ausnahme von Arbeitsbereichen im Legacytarif „kostenlose Testversion“), mit denen die Standardaufbewahrungseinstellungen auf Arbeitsbereichsebene außer Kraft gesetzt werden. Jeder Datentyp ist eine Unterressource des Arbeitsbereichs. Die SecurityEvent-Tabelle kann in [Azure Resource Manager](../../azure-resource-manager/management/overview.md) beispielsweise wie folgt adressiert werden:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Beachten Sie, dass beim Datentyp (Tabelle) die Groß- und Kleinschreibung beachtet wird. Geben Sie Folgendes an, um die aktuellen datentypspezifischen Aufbewahrungseinstellungen für einen bestimmten Datentyp (in diesem Beispiel SecurityEvent) abzurufen:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Die Aufbewahrungseinstellung wird nur für einen Datentyp zurückgegeben, wenn sie explizit für diesen festgelegt ist. Bei Datentypen, deren Aufbewahrung nicht explizit eingestellt wurde (und die somit die Aufbewahrungseinstellung des Arbeitsbereichs erben), wird durch diesen Befehl nichts zurückgegeben. 

Um die aktuellen datentypspezifischen Aufbewahrungseinstellungen für alle Datentypen in Ihrem Arbeitsbereich abzurufen, für die eine datentypspezifische Aufbewahrung festgelegt wurde, lassen Sie einfach den spezifischen Datentyp aus. Beispiel:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Wenn Sie die Aufbewahrung für einen bestimmten Datentyp (in diesem Beispiel SecurityEvent) auf 730 Tage festlegen möchten, geben Sie Folgendes an:

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Die gültigen Werte für `retentionInDays` sind 4 bis 730.

Für die Datentypen `Usage` und `AzureActivity` kann keine benutzerdefinierte Aufbewahrung festgelegt werden. Für sie gilt die maximale Standardaufbewahrungsdauer des Arbeitsbereichs bzw. eine Dauer von 90 Tagen. 

Ein großartiges Tool für das Herstellen einer direkten Verbindung mit Azure Resource Manager zum Festlegen der Aufbewahrung nach Datentyp ist das OSS-Tool [ARMclient](https://github.com/projectkudu/ARMClient).  Weitere Informationen zu ARMClient finden Sie in den Artikeln von [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) und Daniel Bowbyes.  Dies ist ein Beispiel für die Verwendung von ARMClient. Darin wird eine Aufbewahrungsdauer von 730 Tagen für SecurityEvent-Daten festgelegt:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Durch das Festlegen der Aufbewahrung für einzelne Datentypen können Sie Ihre Kosten für die Datenaufbewahrung senken. Bei ab Oktober 2019 (der Monat, in dem diese Funktion eingeführt wurde) gesammelten Daten kann die Reduzierung der Aufbewahrungszeit einiger Datentypen die Kosten für die Aufbewahrung mit der Zeit senken. Wenn die Daten zuvor gesammelt wurden, wird das Festlegen einer kürzeren Aufbewahrungszeit für einen einzelnen Typ Ihre Kosten für die Aufbewahrung nicht beeinträchtigen.  

## <a name="manage-your-maximum-daily-data-volume"></a>Verwalten Ihres maximalen täglichen Datenvolumens

Sie können eine tägliche Obergrenze konfigurieren und die tägliche Erfassung für Ihren Arbeitsbereich einschränken. Dabei ist jedoch Vorsicht geboten, da dieses Limit möglichst nicht erreicht werden sollte. Andernfalls verlieren Sie die Daten des restlichen Tages. Dies kann sich auf Azure-Dienste und -Lösungen auswirken, deren Funktionalität unter Umständen von der Verfügbarkeit aktueller Daten im Arbeitsbereich abhängt. Infolgedessen kann auch die Integrität von Ressourcen, die IT-Diensten zugrunde liegen, nicht mehr zuverlässig überwacht werden, und es können keine Warnungen empfangen werden. Die tägliche Obergrenze ist dazu gedacht, einen **unerwarteten Anstieg** des Datenvolumens aus Ihren verwalteten Ressourcen zu verhindern und den Grenzwert einzuhalten – oder ungeplante Gebühren für Ihren Arbeitsbereich zu vermeiden. Es ist nicht angemessen, ein Tageslimit so festzulegen, dass diese jeden Tag in einem Arbeitsbereich erreicht wird.

Die tägliche Obergrenze wird für jeden Arbeitsbereich zu einer anderen Stunde des Tages angewendet. Die Zurücksetzungsstunde wird auf der Seite **Daily Cap** (Tägliche Obergrenze) angezeigt (siehe unten). Diese Zurücksetzungsstunde kann nicht konfiguriert werden. 

Kurz nach Erreichen des Tageslimits werden für den Rest des Tages keine kostenpflichtigen Datentypen mehr gesammelt. Die Standardlatenz beim Anwenden des Tageslimits führt dazu, dass das Limit nicht genau beim festgelegten Tageslimit angewandt wird. Im oberen Seitenbereich erscheint ein Warnbanner für den ausgewählten Log Analytics-Arbeitsbereich, und an die Tabelle *Operation* wird unter der Kategorie **LogManagement** ein Vorgangsereignis gesendet. Die Datensammlung wird nach der unter *Daily limit will be set at* (Tageslimit wird festgelegt um) definierten Zurücksetzungszeit fortgesetzt. Es empfiehlt sich, eine Warnungsregel auf der Grundlage dieses Vorgangsereignisses zu definieren und so zu konfigurieren, dass bei Erreichen des Tageslimits für Daten eine Benachrichtigung erfolgt. Weitere Informationen im Abschnitt [Warnung beim Erreichen der täglichen Obergrenze](#alert-when-daily-cap-is-reached). 

> [!NOTE]
> Das Tageslimit kann die Datensammlung nicht so genau wie die angegebene Obergrenze beenden, sodass einige überschüssige Daten zu erwarten sind. Dies gilt insbesondere dann, wenn der Arbeitsbereich große Datenmengen empfängt. Werden Daten über die Obergrenze hinaus gesammelt, werden sie dennoch in Rechnung gestellt. Eine Abfrage, die beim Untersuchen des Verhaltens des Tageslimits hilfreich ist, finden Sie im Abschnitt [Anzeigen der Auswirkung der täglichen Obergrenze](#view-the-effect-of-the-daily-cap) dieses Artikels. 

> [!WARNING]
> Die Sammlung folgender Datentypen wird durch die tägliche Obergrenze nicht beendet: **WindowsEvent**, **SecurityAlert**, **SecurityBaseline**, **SecurityBaselineSummary**, **SecurityDetection**, **SecurityEvent**, **WindowsFirewall**, **MaliciousIPCommunication**, **LinuxAuditLog**, **SysmonEvent**, **ProtectionStatus**, **Update** und **UpdateSummary**. Dies gilt allerdings nicht für Arbeitsbereiche, in denen Microsoft Defender für Cloud vor dem 19. Juni 2017 installiert wurde. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifizieren des zu definierenden Tageslimits für Daten

Eine Erklärung des Datenerfassungstrends sowie der zu definierenden täglichen Obergrenze finden Sie unter [Analysieren der Datennutzung in Log Analytics](../usage-estimated-costs.md). Wählen Sie das Limit mit Bedacht, da Sie Ihre Ressourcen nicht überwachen können, nachdem es erreicht wurde. 

### <a name="set-the-daily-cap"></a>Festlegen der täglichen Obergrenze

In den folgenden Schritten erfahren Sie, wie Sie ein Tageslimit für die vom Log Analytics-Arbeitsbereich erfasste Datenmenge konfigurieren.  

1. Klicken Sie links in Ihrem Arbeitsbereich auf **Nutzung und geschätzte Kosten**.
2. Wählen Sie im oberen Bereich der Seite **Nutzung und geschätzte Kosten** für den ausgewählten Arbeitsbereich die Option **Datenobergrenze** aus. 
3. Standardmäßig ist **Tägliche Obergrenze** auf **AUS** gestellt. Um sie zu aktivieren, wählen Sie **EIN** aus und legen das Limit für das Datenvolumen in GB/Tag fest.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Konfigurieren des Log Analytics-Datenlimits":::

Sie können Azure Resource Manager verwenden, um die tägliche Obergrenze zu konfigurieren. Um ihn zu konfigurieren, setzen Sie den Parameter `dailyQuotaGb` unter `WorkspaceCapping` wie unter [Arbeitsbereiche: Erstellen oder Aktualisieren](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping) beschrieben. 

Änderungen an der täglichen Obergrenze können mithilfe der folgenden Abfrage nachverfolgt werden:

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

Weitere Informationen zur Funktion „_LogOperation“ finden Sie [hier](./monitor-workspace.md). 

### <a name="view-the-effect-of-the-daily-cap"></a>Anzeigen der Auswirkung der täglichen Obergrenze

Um die Auswirkung der täglichen Obergrenze zu sehen, ist es wichtig, die nicht in der täglichen Obergrenze enthaltenen Sicherheitsdatentypen sowie die Zurücksetzungsstunde für Ihren Arbeitsbereich zu berücksichtigen. Die Zurücksetzungsstunde für die tägliche Obergrenze wird auf der Seite **Tägliche Obergrenze** angezeigt. Mithilfe der folgenden Abfrage können Sie die Datenmengen, die der täglichen Obergrenze unterliegen, zwischen den Zurücksetzungen nachverfolgen. In diesem Beispiel ist die Zurücksetzungsstunde für den Arbeitsbereich 14:00. Sie müssen dies für Ihren Arbeitsbereich aktualisieren.

```kusto
let DailyCapResetHour=14;
Usage
| where DataType !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) // Quantity in units of MB
| render areachart  
```
Fügen Sie die Datentypen `Update` und `UpdateSummary` zur Zeile `where Datatype` hinzu, wenn die Updateverwaltung-Lösung nicht im Arbeitsbereich ausgeführt wird oder die Zielgruppenadressierung für Lösungen aktiviert ist ([weitere Informationen](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)).


### <a name="alert-when-daily-cap-is-reached"></a>Warnung bei Erreichen der täglichen Obergrenze

Im Azure-Portal wird bei Erreichen des Schwellenwerts für das Datenlimit zwar ein visueller Hinweis angezeigt, dieses Verhalten steht jedoch möglicherweise nicht im Einklang mit der gewünschten Behandlung von Betriebsproblemen, die eine umgehende Reaktion erfordern. Wenn Sie eine Warnbenachrichtigung erhalten möchten, können Sie in Azure Monitor eine neue Warnregel erstellen. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Warnungen](../alerts/alerts-metric.md).

Im Anschluss finden Sie die empfohlenen Einstellungen für die Warnung zur Abfrage der Tabelle `Operation` mithilfe der Funktion `_LogOperation`. (Weitere Informationen finden Sie [hier](./monitor-workspace.md).) 

- Ziel: Wählen Sie Ihre Log Analytics-Ressource aus.
- Kriterien: 
   - Signalname: Benutzerdefinierte Protokollsuche
   - Suchabfrage: `_LogOperation | where Operation =~ "Data collection stopped" | where Detail contains "OverQuota"`
   - Basierend auf: Anzahl der Ergebnisse
   - Bedingung: Größer als
   - Schwellenwert: 0
   - Zeitraum: 5 (Minuten)
   - Häufigkeit: 5 (Minuten)
- Name der Warnungsregel: Daily data limit reached
- Schweregrad: Warnung (Schweregrad 1)

Nachdem die Warnung definiert wurde, wird bei Erreichen des Limits eine Warnung ausgelöst und die in der Aktionsgruppe definierte Reaktion ausgeführt. Sie kann Ihr Team auf folgende Weise benachrichtigen:

- E-Mail-Nachrichten und SMS
- Automatisierte Aktionen mithilfe von Webhooks
- Azure Automation-Runbooks
- [Integriert in eine externe ITSM-Lösung](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Ermittlung per Problembehandlung, warum die Nutzung höher als erwartet ist

Eine höhere Nutzung wird durch eine bzw. beide der folgenden Bedingungen verursacht:
- Mehr Knoten als erwartet senden Daten an den Log Analytics-Arbeitsbereich. Infomationen dazu im Artikel [Grundlegendes zu Knoten, die Daten senden](#understanding-nodes-sending-data).
- Mehr Daten als erwartet werden an den Log Analytics-Arbeitsbereich gesendet (vielleicht aufgrund des Beginns der Verwendung einer neuen Lösung oder einer Konfigurationsänderung an einer bestehenden Lösung). Infomationen dazu im Artikel [Grundlegendes zur erfassten Datenmenge](#understanding-ingested-data-volume).

Wenn Sie eine hohe Datenerfassungsrate beobachten, die anhand der `Usage`-Datensätze gemeldet wird (siehe Abschnitt [Datenvolumen nach Lösung](#data-volume-by-solution)), aber nicht dieselben Ergebnisse bei der direkten Summierung von `_BilledSize` für den [Datentyp](#data-volume-for-specific-events) feststellen, ist möglicherweise eine beträchtliche Anzahl spät eintreffender Daten vorhanden. Informationen zur Diagnose dieses Problems finden Sie im Abschnitt [Spät eintreffende Daten](#late-arriving-data) dieses Artikels. 

### <a name="log-analytics-workspace-insights"></a>Log Analytics-Arbeitsbereich Insights

Hier erfahren Sie Grundlegendes zu den auf dem Tab **Verbrauch** der Arbeitsmappe [Log Analytics-Arbeitsbereich Insights](log-analytics-workspace-insights-overview.md)angezeigten Datenmengen. Auf dem **Dashboard „Nutzung“** sehen Sie auf einfache Weise:
- Welche Datentabellen das größte Datenvolumen in der Haupttabelle erfassen  
- Welche Daten beitragenden Ressourcen die wichtigsten sind 
- Was der Trend der Datenerfassung ist

Sie können zu den **zusätzlichen Abfragen** pivotieren, um einfach weitere Abfragen auszuführen, die beim Verstehen Ihrer Datenmuster hilfreich sind. 

Weitere Informationen zu den [Funktionen des Tabs „Verbrauch“](log-analytics-workspace-insights-overview.md#usage-tab) 

Diese Arbeitsmappe kann viele der Fragen beantworten, ohne überhaupt eine Abfrage ausführen zu müssen. Wenn Sie spezifischere Fragen beantworten oder ausführlichere Analysen durchführen wollen, helfen Ihnen die Abfragen in den nächsten beiden Abschnitten beim Einstieg. 

## <a name="understanding-nodes-sending-data"></a>Ermitteln der Knoten, die Daten senden

Um die Anzahl von Knoten zu ermitteln, die im letzten Monat täglich Heartbeats vom Agent gemeldet haben, verwenden Sie die folgende Abfrage:

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Zum Abrufen der Anzahl von Knoten, die in den letzten 24 Stunden Daten gesendet haben, verwenden Sie die folgende Abfrage: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Eine Liste der Knoten, die Daten senden (und die Menge der jeweils gesendeten Daten) erhalten Sie mit dieser Abfrage:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Nach Legacytarif „Pro Knoten“ abgerechnete Knoten

Beim [Legacytarif „Pro Knoten“](#legacy-pricing-tiers) werden Knoten auf Stundenbasis abgerechnet, und es werden zudem Knoten nicht gezählt, die nur eine Reihe von Sicherheitsdatentypen senden. Die tägliche Anzahl von Knoten würde der folgenden Abfrage ähneln:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Die Anzahl der Einheiten auf der Rechnung ist in Knotenmonaten angegeben, die durch `billableNodeMonthsPerDay` in der Abfrage dargestellt ist. Wenn im Arbeitsbereich die Lösung für die Updateverwaltung installiert ist, fügen Sie die Datentypen **Update** und **UpdateSummary** der Liste in der WHERE-Klausel in der obigen Abfrage hinzu. Der tatsächliche Abrechnungsalgorithmus weist zusätzliche Komplexität auf, wenn eine Zielgruppenadressierung verwendet wird, die in der obigen Abfrage nicht dargestellt ist. 


> [!TIP]
> Verwenden Sie diese `find`-Abfragen mit Bedacht, da umfassende Scans verschiedener Datentypen [ressourcenintensiv](./query-optimization.md#query-performance-pane) sind. Wenn Sie keine Ergebnisse **pro Computer** benötigen, dann führen Sie Abfragen nach dem Datentyp **Nutzung** aus (siehe unten).

## <a name="understanding-ingested-data-volume"></a>Grundlegendes zur erfassten Datenmenge

Auf der Seite **Nutzung und geschätzte Kosten** zeigt das Diagramm *Datenerfassung pro Lösung* die Gesamtmenge an gesendeten Daten sowie die von jeder Lösung gesendete Datenmenge an. Sie können Trends ermitteln, z. B. ob der gesamte Datenverbrauch (oder der Verbrauch einer bestimmte Lösung) ansteigt, konstant bleibt oder abnimmt. 

### <a name="data-volume-for-specific-events"></a>Datenmenge für bestimmte Ereignisse

Um die Größe der erfassten Daten für eine bestimmte Gruppe von Ereignissen zu überprüfen, können Sie die jeweilige Tabelle (in diesem Beispiel `Event`) abfragen und dann die Abfrage auf die relevanten Ereignisse (in diesem Beispiel Ereignis-ID 5145 oder 5156) beschränken:

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
```

Beachten Sie, dass durch die Klausel `where _IsBillable = true` Datentypen bestimmter Lösungen herausgefiltert werden, für die keine Erfassungsgebühren anfallen. Weitere Informationen zu `_IsBillable` finden Sie [hier](./log-standard-columns.md#_isbillable).

### <a name="data-volume-by-solution"></a>Datenvolumen nach Lösung

Die Abfrage, mit der die abrechenbare Datenmenge nach Lösung während des letzten Monats (ohne den letzten angebrochenen Tag) angezeigt wird, kann unter Verwendung des Datentyps [Usage](/azure/azure-monitor/reference/tables/usage) (Nutzung) wie folgt erstellt werden:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

Mit der Klausel mit `TimeGenerated` wird nur sichergestellt, dass die Abfrage im Azure-Portal über den Standardwert von 24 Stunden hinausgeht. Bei Verwendung des Datentyps **Nutzung** stellen `StartTime` und `EndTime` die Zeitrahmen dar, für die Ergebnisse angezeigt werden. 

### <a name="data-volume-by-type"></a>Datenmenge nach Typ

Sie können die Daten genauer untersuchen, um Trends für die einzelnen Datentypen zu erkennen:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Oder verwenden Sie Folgendes, um eine Tabelle nach Lösung und Typ für den letzten Monat anzuzeigen:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Datenmenge nach Computer

Der Datentyp **Nutzung** enthält keine Informationen auf Computerebene. Um die **Größe** der pro Computer erfassten abrechenbaren Daten anzuzeigen, verwenden Sie die [Eigenschaft](./log-standard-columns.md#_billedsize) **_BilledSize**, die die Größe in Bytes angibt:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

Die **_IsBillable**-[Eigenschaft](./log-standard-columns.md#_isbillable) gibt an, ob für die erfassten Daten Gebühren anfallen. Der Typ **Nutzung** wird weggelassen, da es nur um die Analyse von Datentrends geht. 

Um die Anzahl von Ereignissen pro Computer anzuzeigen, für die **Gebühren** anfallen, verwenden Sie diese Abfrage: 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> Verwenden Sie diese `find`-Abfragen mit Bedacht, da umfassende Scans verschiedener Datentypen [ressourcenintensiv](./query-optimization.md#query-performance-pane) sind. Wenn Sie keine Ergebnisse **pro Computer** benötigen, dann führen Sie Abfragen nach dem Datentyp **Nutzung** aus.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Datenmenge nach Azure-Ressource, Ressourcengruppe oder Abonnement

Für Daten von in Azure gehosteten Knoten können Sie die **Größe** der erfassten Daten __pro Computer__ mit der [Eigenschaft _ResourceId](./log-standard-columns.md#_resourceid) abrufen, die den vollständigen Pfad zur Ressource enthält:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Für Daten von in Azure gehosteten Knoten können Sie die **Größe** der erfassten Daten __pro Azure-Abonnement__ nach Abonnement-ID mit der **Eigenschaft _SubscriptionId** wie folgt abrufen:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Zum Abrufen des Datenvolumens nach Ressourcengruppe können Sie **_ResourceId** parsen:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Wenn nötig können Sie die **_ResourceId** auch vollständiger parsen:

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Verwenden Sie diese `find`-Abfragen mit Bedacht, da umfassende Scans verschiedener Datentypen [ressourcenintensiv](./query-optimization.md#query-performance-pane) sind. Wenn Sie keine Ergebnisse pro Abonnement, Ressourcengruppe oder Ressourcenname benötigen, dann führen Sie Abfragen nach dem Datentyp **Nutzung** aus.

> [!WARNING]
> Einige der Felder vom Datentyp **Nutzung** sind zwar weiterhin im Schema enthalten, wurden jedoch als veraltet markiert und ihre Werte werden nicht mehr eingepflegt. Dies sind neben **Computer** Felder in Bezug auf die Erfassung (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** und **AverageProcessingTimeMs**).

## <a name="late-arriving-data"></a>Spät eintreffende Daten

Es kann vorkommen, dass Daten mit alten Zeitstempeln erfasst werden. Das kann beispielsweise der Fall sein, wenn ein Agent aufgrund eines Konnektivitätsproblems nicht mit Log Analytics kommunizieren kann oder wenn das Datum oder die Uhrzeit eines Hosts nicht korrekt ist. Dies zeigt sich evtl. durch eine offensichtliche Diskrepanz zwischen den erfassten Daten, die vom Datentyp **Nutzung** gemeldet werden, und einer Abfrage, die **_BilledSize** über die Rohdaten für einen bestimmten Tag summiert, der durch **TimeGenerated**, dem Zeitstempel für den Generierungszeitpunkt des Ereignisses, angegeben wird.

Verwenden Sie zum Diagnostizieren von Problemen mit spät eintreffenden Daten neben der Spalte **TimeGenerated** auch die Spalte **_TimeReceived** ([weitere Informationen](./log-standard-columns.md#_timereceived)). **_TimeReceived** ist die Zeit, zu der der Datensatz vom Azure Monitor-Erfassungspunkt in der Azure-Cloud empfangen wurde. Wenn Sie z. B. die **Nutzung**-Datensätze verwenden, haben Sie am 2. Mai 2021 hohe Erfassungsvolumen für **W3CIISLog**-Daten beobachtet. Nachfolgend sehen Sie eine Abfrage, mit der die Zeitstempel für diese erfassten Daten ermittelt werden: 

```Kusto
W3CIISLog
| where TimeGenerated > datetime(1970-01-01)
| where _TimeReceived >= datetime(2021-05-02) and _TimeReceived < datetime(2021-05-03) 
| where _IsBillable == true
| summarize BillableDataMB = sum(_BilledSize)/1.E6 by bin(TimeGenerated, 1d)
| sort by TimeGenerated asc 
```

Die Anweisung `where TimeGenerated > datetime(1970-01-01)` ist lediglich vorhanden, um der Log Analytics-Benutzeroberfläche den Hinweis zu geben, dass alle Daten zu überprüfen sind.  

## <a name="querying-for-common-data-types"></a>Abfragen von häufig verwendeten Datentypen

Wenn Sie die Datenquelle für einen bestimmten Datentyp näher untersuchen möchten, finden Sie hier einige nützliche Beispielabfragen:

+ **Arbeitsbereichsbasierte Application Insights**-Ressourcen
  - Weitere Informationen finden Sie unter [Verwalten der Nutzung und der Kosten für Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources).
+ **Sicherheitslösung**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Protokollverwaltungslösung**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Datentyp **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Datentyp **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Datentyp **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datentyp **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Tipps zum Reduzieren der Datenmenge

Auf dieser Tabelle finden Sie einige Vorschläge zum Verringern des Umfangs der erfassten Protokolle.

| Quelle mit hohem Datenvolumen | Reduzieren des Datenvolumens |
| -------------------------- | ------------------------- |
| Datensammlungsregeln      | Der [Azure Monitor-Agent](../agents/azure-monitor-agent-overview.md) verwendet Datensammlungsregeln, um das Sammeln von Daten zu verwalten. Sie können die [Datensammlung einschränken](../agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries), indem Sie benutzerdefinierte XPath-Abfragen verwenden. | 
| Container Insights         | [Konfigurieren Sie Container Insights](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost), um nur die Daten zu erfassen, die Sie benötigen. |
| Microsoft Sentinel | Überprüfen Sie alle [Sentinel-Datenquellen](../../sentinel/connect-data-sources.md), die kürzlich als Quellen für zusätzliches Datenvolumen aktiviert wurden. [Weitere Informationen](../../sentinel/azure-sentinel-billing.md) zu Kosten und Abrechnung von Sentinel |
| Sicherheitsereignisse            | Wählen Sie [Sicherheitsereignisse vom Typ „Allgemein“ oder „Minimal“](../../security-center/security-center-enable-data-collection.md#data-collection-tier) aus. <br> Ändern der Sicherheitsüberwachungsrichtlinie, sodass nur benötigte Ereignisse erfasst werden. Überprüfen Sie insbesondere die Notwendigkeit zum Erfassen von Ereignissen für Folgendes: <br> - [Überwachung der Filterplattform](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [Überwachung der Registrierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10)) <br> - [Überwachung des Dateisystems](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10)) <br> - [Überwachung von Kernelobjekten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10)) <br> - [Überwachung der Handleänderung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10)) <br> – Überwachung von Wechselmedien |
| Leistungsindikatoren       | Ändern Sie die [Leistungsindikatoren-Konfiguration](../agents/data-sources-performance-counters.md) folgendermaßen: <br> – Reduzieren der Sammlungshäufigkeit <br> – Reduzieren der Anzahl von Leistungsindikatoren |
| Ereignisprotokolle                 | Ändern Sie die [Ereignisprotokollkonfiguration](../agents/data-sources-windows-events.md) folgendermaßen: <br> – Reduzieren der Anzahl von erfassten Ereignisprotokollen <br> - Ausschließliches Erfassen von erforderlichen Ereignisebenen. Erfassen Sie beispielsweise keine Ereignisse der Ebene *Informationen*. |
| syslog                     | Ändern Sie die [syslog-Konfiguration](../agents/data-sources-syslog.md) folgendermaßen: <br> – Reduzieren der Anzahl von erfassten Einrichtungen <br> - Ausschließliches Erfassen von erforderlichen Ereignisebenen. Erfassen Sie beispielsweise keine Ereignisse der Ebenen *Info* und *Debuggen*. |
| AzureDiagnostics           | Ändern Sie die [Ressourcenprotokollsammlung](../essentials/diagnostic-settings.md#create-in-azure-portal) folgendermaßen: <br> – Verringern der Anzahl von Ressourcen, die Protokolle an Log Analytics senden <br> – Ausschließliches Erfassen von erforderlichen Protokollen |
| Lösungsdaten von Computern, für die die Lösung nicht erforderlich ist | Verwenden Sie die [Zielgruppenadressierung für Lösungen](../insights/solution-targeting.md), um Daten nur für erforderliche Gruppen mit Computern zu erfassen. |
| Application Insights | Erwägen Sie die Optionen zum [Verwalten des Application Insights-Datenvolumens](../app/pricing.md#managing-your-data-volume). |
| [Azure SQL-Analyse](../insights/azure-sql.md) | Passen Sie die Überwachungseinstellungen mit [Set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) an. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Abrufen von Knoten gemäß der Abrechnung im Tarif „Pro Knoten“

Um eine Liste von Computern abzurufen, die als Knoten berechnet werden, wenn für den Arbeitsbereich der Legacytarif „Pro Knoten“ gilt, suchen Sie Knoten, die **kostenpflichtige Datentypen** senden (einige Datentypen sind kostenlos). Verwenden Sie dazu die [Eigenschaft _IsBillable](./log-standard-columns.md#_isbillable) und das linksäußere Feld des vollständig qualifizierten Domänennamens. Dies gibt die Anzahl der Computer mit abgerechneten Daten pro Stunde zurück (was der Granularität entspricht, mit der Knoten gezählt und abgerechnet werden):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Abrufen der Knotenanzahl für Sicherheit und Automatisierung

Um die Anzahl der verschiedenen Sicherheitsknoten anzuzeigen, können Sie diese Abfrage verwenden:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Die Anzahl der verschiedenen Automation-Knoten können Sie mit dieser Abfrage anzeigen:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Evaluieren des Legacy-Tarifs „Pro Knoten“

Die Entscheidung, ob für Arbeitsbereiche mit Zugriff auf den Legacytarif **Pro Knoten** anstelle des derzeitigen Tarifs ein aktueller Tarif wie **Nutzungsbasierte Zahlung** oder **Mindestabnahme** besser geeignet ist, fällt Kunden häufig nicht leicht.  Hierfür muss der Kunde einerseits mit den Fixkosten pro überwachtem Knoten im Tarif „Pro Knoten“ und der enthaltenen Datenzuordnung von 500 MB pro Knoten und Tag vertraut sein und andererseits auch wissen, welche Kosten für die erfassten Daten im Tarif mit nutzungsbasierter Zahlung (pro GB) anfallen. 

Zur Vereinfachung dieser Bewertung können Sie die folgende Abfrage verwenden, um eine Empfehlung zum optimalen Tarif zu erhalten, die auf den Nutzungsmustern des Arbeitsbereichs basiert. Bei dieser Abfrage werden die überwachten Knoten überprüft und die Daten ermittelt, die in den letzten sieben Tagen in einem Arbeitsbereich erfasst wurden. Für jeden Tag wird dann ausgewertet, welcher Tarif optimal gewesen wäre. Zum Verwenden der Abfrage müssen Sie:

- angeben, ob der Arbeitsbereich Microsoft Defender für Cloud verwendet, indem Sie **workspaceHasSecurityCenter** auf **True** oder **False** festlegen. 
- die Preise aktualisieren, wenn Sie über bestimmte Rabatte verfügen.
- die Anzahl der Tage angeben, die rückwirkend ermittelt und analysiert werden sollen, indem Sie **daysToEvaluate** festlegen. Dies ist hilfreich, wenn die Abfrage bei Berücksichtigung der Daten von sieben Tagen zu lange dauert.

Hier sehen Sie die Abfrage für Tarifempfehlungen:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Defender for Cloud (formerly known as Azure Security Center)
let PerNodePrice = 15.; // Enter your monthly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CommitmentTier100Price = 196.; // Enter your price for the 100 GB/day commitment tier
let CommitmentTier200Price = 368.; // Enter your price for the 200 GB/day commitment tier
let CommitmentTier300Price = 540.; // Enter your price for the 300 GB/day commitment tier
let CommitmentTier400Price = 704.; // Enter your price for the 400 GB/day commitment tier
let CommitmentTier500Price = 865.; // Enter your price for the 500 GB/day commitment tier
let CommitmentTier1000Price = 1700.; // Enter your price for the 1000 GB/day commitment tier
let CommitmentTier2000Price = 3320.; // Enter your price for the 2000 GB/day commitment tier
let CommitmentTier5000Price = 8050.; // Enter your price for the 5000 GB/day commitment tier
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CommitmentTier100DailyCost = CommitmentTier100Price + max_of(billableGB - 100, 0.)* CommitmentTier100Price/100.
| extend CommitmentTier200DailyCost = CommitmentTier200Price + max_of(billableGB - 200, 0.)* CommitmentTier200Price/200.
| extend CommitmentTier300DailyCost = CommitmentTier300Price + max_of(billableGB - 300, 0.)* CommitmentTier300Price/300.
| extend CommitmentTier400DailyCost = CommitmentTier400Price + max_of(billableGB - 400, 0.)* CommitmentTier400Price/400.
| extend CommitmentTier500DailyCost = CommitmentTier500Price + max_of(billableGB - 500, 0.)* CommitmentTier500Price/500.
| extend CommitmentTier1000DailyCost = CommitmentTier1000Price + max_of(billableGB - 1000, 0.)* CommitmentTier1000Price/1000.
| extend CommitmentTier2000DailyCost = CommitmentTier2000Price + max_of(billableGB - 2000, 0.)* CommitmentTier2000Price/2000.
| extend CommitmentTier5000DailyCost = CommitmentTier5000Price + max_of(billableGB - 5000, 0.)* CommitmentTier5000Price/5000.
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CommitmentTier100DailyCost,CommitmentTier200DailyCost,
    CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CommitmentTier100DailyCost, "Commitment tier (100 GB/day)",
    MinCost == CommitmentTier200DailyCost, "Commitment tier (200 GB/day)",
    MinCost == CommitmentTier300DailyCost, "Commitment tier (300 GB/day)",
    MinCost == CommitmentTier400DailyCost, "Commitment tier (400 GB/day)",
    MinCost == CommitmentTier500DailyCost, "Commitment tier (500 GB/day)",
    MinCost == CommitmentTier1000DailyCost, "Commitment tier (1000 GB/day)",
    MinCost == CommitmentTier2000DailyCost, "Commitment tier (2000 GB/day)",
    MinCost == CommitmentTier5000DailyCost, "Commitment tier (5000 GB/day)",
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CommitmentTier100DailyCost, CommitmentTier200DailyCost, CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Diese Abfrage spiegelt nicht exakt wider, wie der Verbrauch berechnet wird, bietet aber in den meisten Fällen Empfehlungen für einen Tarif.  

> [!NOTE]
> Wenn Sie die Berechtigungen nutzen möchten, die Sie durch den Kauf der OMS E1-Suite, OMS E2-Suite oder des OMS-Add-Ons für System Center erwerben, wählen Sie den Tarif *Pro Knoten* für Log Analytics aus.

## <a name="create-an-alert-when-data-collection-is-high"></a>Erstellen einer Warnung für den Fall, dass die Datensammlung hoch ist

In diesem Abschnitt wird beschrieben, wie Sie über Azure Monitor-[Protokollwarnungen](../alerts/alerts-unified-log.md) eine Warnung erstellen, wenn das Datenvolumen in den letzten 24 Stunden einen angegebenen Wert überschritten hat. 

Führen Sie folgende Schritte aus, um zu warnen, wenn das in den letzten 24 Stunden erfasste abrechenbare Datenvolumen größer als 50 GB ist: 

- **Definieren der Warnungsbedingung**: Festlegen Ihres Log Analytics-Arbeitsbereichs als Ressourcenziel
- **Warnungskriterien**:
   - **Signalname**: **Benutzerdefinierte Protokollsuche**
   - **Suchabfrage**: `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50`.  
   - **Warnungslogik**: **Basiert auf** *Anzahl von Ergebnissen* und **Bedingung** ist *Größer als* ein **Schwellenwert** von *0*
   - **Zeitraum** von *1440* Minuten und **Warnungshäufigkeit** alle *1440* Minuten zur Ausführung einmal am Tag.
- **Definieren der Warnungsdetails**:
   - **Name**: *Abrechenbares Datenvolumen größer als 50 GB in 24 Stunden*
   - **Schweregrad**: *Warnung*

Geben Sie eine vorhandene [Aktionsgruppe](../alerts/action-groups.md) an, oder erstellen Sie eine neue, damit Sie benachrichtigt werden, wenn die Protokollwarnung Kriterien erfüllt.

Wenn Sie eine Warnung erhalten, verwenden Sie die Schritte in den Abschnitten oben, um per Problembehandlung zu ermitteln, warum die Nutzung höher als erwartet ist.

## <a name="data-transfer-charges-using-log-analytics"></a>Gebühren für die Datenübertragung mit Log Analytics

Das Senden von Daten an Log Analytics kann Gebühren für die Bandbreitennutzung nach sich ziehen. Dies ist jedoch auf VMs beschränkt, auf denen ein Log Analytics-Agent installiert ist, und gilt nicht bei Verwendung von Diagnoseeinstellungen oder anderen Connectors, die in Microsoft Sentinel integriert sind. Wie auf der [Seite Azure-Bandbreitenpreise](https://azure.microsoft.com/pricing/details/bandwidth/) beschrieben, wird die Datenübertragung zwischen Azure-Diensten in zwei Regionen mit dem normalen Preis als ausgehende Datenübertragung abgerechnet. Eingehende Datenübertragungen sind kostenlos. Diese Gebühr ist jedoch sehr niedrig im Vergleich zu den Kosten für die Log Analytics-Datenerfassung. Folglich muss sich die Kontrolle von Kosten für Log Analytics auf Ihr [erfasstes Datenvolumen](#understanding-ingested-data-volume) konzentrieren. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Beheben des Problems, dass Log Analytics keine Daten mehr erfasst

Wenn Sie den Legacytarif „kostenlose Testversion“ nutzen und an einem Tag mehr als 500 MB Daten gesendet haben, wird die Datensammlung für den Rest des Tages beendet. Das Erreichen des Tageslimits ist häufig die Ursache dafür, dass Log Analytics die Datensammlung beendet oder Daten scheinbar fehlen. Log Analytics erstellt ein Ereignis vom Typ **Vorgang**, wenn die Datensammlung beginnt und endet. Führen Sie die folgende Abfrage in der Suche aus, um zu überprüfen, ob Sie das Tageslimit erreichen und Daten fehlen: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Wenn die Datensammlung endet, hat **OperationStatus** den Wert **Warnung**. Wenn die Datensammlung beginnt, hat **OperationStatus** den Wert **Erfolgreich**. Die folgende Tabelle führt die Gründe für das Beenden der Datensammlung sowie eine empfohlene Aktion zum Fortsetzen der Datensammlung auf:

|Grund für die Beendigung der Datensammlung| Lösung| 
|-----------------------|---------|
|Tägliche Obergrenze des Arbeitsbereichs wurde erreicht|Warten Sie, bis die Datensammlung am Folgetag automatisch neu gestartet wird, oder erhöhen Sie das Tageslimit für das Datenvolumen, wie unter „Verwalten des maximalen täglichen Datenvolumens“ beschrieben. Der Zeitpunkt für das Zurücksetzen der täglichen Obergrenze wird auf der Seite **Tägliche Obergrenze** angezeigt. |
| Ihr Arbeitsbereich hat die [Rate für das Datenerfassungsvolumen](../service-limits.md#log-analytics-workspaces) erreicht | Das Standardratenlimit für das Erfassungsvolumen für Daten, die von Azure-Ressourcen mit Diagnoseeinstellungen gesendet werden, beträgt ungefähr 6 GB/Minute pro Arbeitsbereich. Dies ist ein ungefährer Wert, da die tatsächliche Größe je nach Protokolllänge und Komprimierungsverhältnis zwischen den Datentypen variieren kann. Dieses Limit gilt nicht für Daten, die von Agents oder der Data Collector-API gesendet werden. Wenn Sie Daten mit einer höheren Rate an einen einzelnen Arbeitsbereich senden, werden einige Daten gelöscht, und es wird alle sechs Stunden ein Ereignis an die Tabelle Vorgang im Arbeitsbereich gesendet, während der Schwellenwert weiterhin überschritten wird. Wenn das Datenerfassungsvolumen weiterhin das Ratenlimit überschreitet oder Sie es wahrscheinlich in Kürze erreichen werden, können Sie eine Erhöhung für Ihren Arbeitsbereich anfordern, indem Sie eine E-Mail an LAIngestionRate@microsoft.com senden oder eine Supportanfrage öffnen. Mit der Abfrage `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` können Sie nach dem Ereignis suchen, das auf eine Überschreitung des Ratenlimits der Datenerfassung hinweist. |
|Tageslimit oder kostenloser Legacytarif erreicht |Warten Sie, bis die Datensammlung am Folgetag automatisch neu gestartet wird, oder wechseln Sie zu einem kostenpflichtigen Tarif.|
|Das Azure-Abonnement befindet sich aus folgendem Grund in einem angehaltenen Zustand:<br> Kostenlose Testversion endete<br> Azure Pass ist abgelaufen<br> Monatliches Ausgabenlimit ist erreicht (z. B. in einem MSDN- oder Visual Studio-Abonnement)|Konvertieren in ein kostenpflichtiges Abonnement<br> Limit entfernen oder warten, bis das Limit zurückgesetzt wird|

Um benachrichtigt zu werden, wenn die Datensammlung beendet wird, verwenden Sie die Schritte, die im Abschnitt [Warnung bei Erreichen der täglichen Obergrenze](#alert-when-daily-cap-is-reached) beschrieben werden. Um eine E-Mail-, Webhook- oder Runbookaktion für die Warnungsregel zu konfigurieren, führen Sie die Schritte aus, die unter [Erstellen einer Aktionsgruppe](../alerts/action-groups.md) beschrieben sind. 

## <a name="limits-summary"></a>Zusammenfassung der Grenzwerte

Es gibt zusätzliche Log Analytics-Grenzwerte, von denen einige vom Log Analytics-Tarif abhängen. Sie sind unter [Azure-Abonnement- und -Dienstgrenzen, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces) dokumentiert.


## <a name="next-steps"></a>Nächste Schritte

- Informationen dazu, wie Sie die Suchsprache verwenden, finden Sie unter [Protokollsuchen in Azure Monitor-Protokollen](../logs/log-query-overview.md). Sie können Suchabfragen verwenden, um für die Nutzungsdaten eine zusätzliche Analyse durchzuführen.
- Führen Sie die unter [Erstellen, Anzeigen und Verwalten von Warnungen mithilfe von Azure Monitor](../alerts/alerts-metric.md) beschriebenen Schritte aus, um benachrichtigt zu werden, wenn ein Suchkriterium erfüllt ist.
- Verwenden Sie die [Zielgruppenadressierung für Lösungen](../insights/solution-targeting.md), um Daten nur für erforderliche Gruppen mit Computern zu erfassen.
- Machen Sie sich mit [Filterrichtlinie für Microsoft Defender für Cloud](../../security-center/security-center-enable-data-collection.md) vertraut, um eine effektive Richtlinie zur Erfassung von Ereignissen zu konfigurieren.
- Ändern Sie die [Leistungsindikatorenkonfiguration](../agents/data-sources-performance-counters.md).
- Informationen zum Ändern der Einstellungen für die Ereigniserfassung finden Sie unter [Datenquellen für Windows-Ereignisprotokolle in Log Analytics](../agents/data-sources-windows-events.md).
- Informationen zum Ändern der Einstellungen für die Syslog-Sammlung finden Sie unter [Syslog-Datenquellen in Log Analytics](../agents/data-sources-syslog.md).
- Informationen zum Ändern der Einstellungen für die Syslog-Sammlung finden Sie unter [Syslog-Datenquellen in Log Analytics](../agents/data-sources-syslog.md).
