---
title: Planen der Kostenverwaltung
titleSuffix: Azure Machine Learning
description: Planen und verwalten Sie Kosten für Azure Machine Learning mithilfe der Kostenanalyse im Azure-Portal. Erhalten Sie weitere Tipps zur Senkung der Kosten, um Ihre Kosten beim Erstellen von ML-Modellen zu senken.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.date: 10/21/2021
ms.openlocfilehash: 23a1efabb005cf943542a4bc0e9e00ebd430030f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562757"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>Planen der Kostenverwaltung für Azure Machine Learning

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Machine Learning planen und verwalten. Bevor Sie Ressourcen hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten zu planen. Überprüfen Sie dann beim Hinzufügen der Azure-Ressourcen die geschätzten Kosten. 

Nachdem Sie mit der Verwendung der Azure Machine Learning-Ressourcen begonnen haben, können Sie die Features für die Kostenverwaltung verwenden, um Budgets festzulegen und die Kosten zu überwachen. Überprüfen Sie darüber hinaus die vorhergesagten Kosten, und ermitteln Sie Ausgabentrends, um Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind.

Beachten Sie, dass die Kosten für Azure Machine Learning nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung darstellen. Bei Nutzung anderer Azure-Dienste werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die unter Ihrem Azure-Abonnement genutzt werden, einschließlich der Dienste von Drittanbietern. In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Machine Learning planen und verwalten. Nachdem Sie sich mit der Verwaltung der Kosten für Azure Machine Learning vertraut gemacht haben, können Sie ähnliche Methoden anwenden, um die Kosten für alle Azure-Dienste zu verwalten, die unter Ihrem Abonnement genutzt werden.

Weitere Informationen zur Kostenoptimierung finden Sie unter [Verwalten und Optimieren von Kosten in Azure Machine Learning](how-to-manage-optimize-cost.md).

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). 

Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Schätzen der Kosten vor der Verwendung von Azure Machine Learning

- Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) für eine Kostenschätzung, bevor Sie die Ressourcen in einem Azure Machine Learning-Arbeitsbereich erstellen.
Wählen Sie auf der linken Seite **KI und Machine Learning** und dann **Azure Machine Learning** aus, um anzufangen.  

Der folgende Screenshot zeigt die Kostenschätzung unter Verwendung des Preisrechners:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Beispiel für die geschätzten Kosten im Azure-Preisrechner.":::

Wenn Sie Ihrem Arbeitsbereich neue Ressourcen hinzufügen, kehren Sie zu diesem Preisrechner zurück, und fügen Sie hier die gleiche Ressource hinzu, um Ihre Kostenschätzung zu aktualisieren.

Weitere Informationen finden Sie unter [Azure Machine Learning – Preise](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Azure Machine Learning

Azure Machine Learning wird in einer Azure-Infrastruktur ausgeführt, in der beim Bereitstellen der neuen Ressource Kosten für Azure Machine Learning anfallen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. Sie müssen diese Kosten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen. 


### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Häufig anfallende Kosten für Azure Machine Learning

Wenn Sie Ressourcen für einen Azure Machine Learning-Arbeitsbereich erstellen, werden auch Ressourcen für andere Azure-Dienste erstellt. Sie lauten wie folgt:

* Konto vom Typ „Basic“ für [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Azure-Blockblobspeicher](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (Universell V1)
* [Schlüsseltresor](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Wenn Sie eine [Compute-Instanz](concept-compute-instance.md) erstellen, bleibt die VM eingeschaltet, damit sie für Ihre Arbeit verfügbar ist.  [Richten Sie einen Zeitplan ein](how-to-create-manage-compute-instance.md#schedule), um die Compute-Instanz automatisch zu starten und zu beenden (Vorschau), wenn Sie nicht planen, sie zu verwenden, und damit Kosten zu sparen.
 
### <a name="costs-might-accrue-before-resource-deletion"></a>Vor dem Löschen von Ressourcen möglicherweise anfallende Kosten

Bevor Sie einen Azure Machine Learning-Arbeitsbereich im Azure-Portal oder mit der Azure CLI löschen, sollten Sie wissen, dass die folgenden Unterressourcen allgemeine Kosten darstellen, die auch dann anfallen, wenn Sie nicht aktiv im Arbeitsbereich arbeiten. Wenn Sie planen, zu einem späteren Zeitpunkt zu Ihrem Azure Machine Learning-Arbeitsbereich zurückzukehren, können für diese Ressourcen weiterhin Kosten anfallen.

* VMs
* Load Balancer
* Virtual Network
* Bandbreite

Jede VM wird pro Stunde abgerechnet, die sie ausgeführt wird. Die Kosten hängen von den VM-Spezifikationen ab. VMs, die ausgeführt werden, aber nicht aktiv an einem Dataset arbeiten, werden weiterhin über den Lastenausgleich in Rechnung gestellt. Für jede Compute-Instanz wird pro Tag ein Lastenausgleich abgerechnet. Für alle 50 Knoten eines Computeclusters wird ein Standardlastenausgleich abgerechnet. Jeder Lastenausgleich wird mit ca. 0,33 USD pro Tag berechnet. Löschen Sie die Computeressource, um Kosten für den Lastenausgleich bei beendeten Compute-Instanzen und Computeclustern zu vermeiden. Pro Abonnement und Region wird ein virtuelles Netzwerk abgerechnet. Virtuelle Netzwerke können sich nicht über Regionen oder Abonnements erstrecken. Beim Einrichten privater Endpunkte in Setups von virtuellen Netzwerken können auch Kosten anfallen. Die Bandbreite wird nach Nutzung berechnet. Je mehr Daten übertragen werden, desto höher die Kosten.

### <a name="costs-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Nachdem Sie im Azure-Portal oder mit der Azure CLI einen Azure Machine Learning-Arbeitsbereich gelöscht haben, sind die folgenden Ressourcen weiterhin vorhanden. Hierfür fallen weiter Kosten an, bis Sie sie löschen.

* Azure Container Registry
* Azure-Blockblobspeicher
* Key Vault
* Application Insights

Verwenden Sie das SDK, um den Arbeitsbereich zusammen mit den abhängigen Ressourcen zu löschen:

```python
ws.delete(delete_dependent_resources=True)
```

Wenn Sie eine Azure Kubernetes Service-Instanz (AKS) in Ihrem Arbeitsbereich erstellen oder Computeressourcen an Ihren Arbeitsbereich anfügen, müssen Sie das Löschen hierfür separat im [Azure-Portal](https://portal.azure.com) durchführen.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Verwenden der Azure-Vorauszahlungsgutschrift mit Azure Machine Learning

Sie können Azure Machine Learning-Gebühren mit der Azure-Vorauszahlungsgutschrift begleichen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Überprüfen von geschätzten Kosten im Azure-Portal

<!-- Note for Azure service writer: If your service shows estimated costs when a user is creating resources in the Azure portal, at a minimum, insert this section as a brief walkthrough that steps through creating a Azure Machine Learning resource where the estimated cost is shown to the user, updated for your service. Add a screenshot where the estimated costs or subscription credits are shown.

If your service doesn't show costs as they create a resource or if estimated costs aren't shown to users before they use your service, then omit this section.

For example, you might start with the following (modify for your service):
-->

Wenn Sie Computeressourcen für Azure Machine Learning erstellen, werden die geschätzten Kosten angezeigt.

So erstellen Sie eine *Compute-Instanz* und zeigen den geschätzten Preis an:

1. Melden Sie sich beim [Azure Machine Learning Studio](https://ml.azure.com) an.
1. Wählen Sie auf der linken Seite **Compute** aus.
1. Wählen Sie oben in der Symbolleiste die Option **+Neu** aus.
1. Überprüfen Sie den geschätzten Preis, der für jede verfügbare VM-Größe angezeigt wird.
1. Schließen Sie die Ressourcenerstellung ab.


:::image type="content" source="media/concept-plan-manage-cost/create-resource.png" alt-text="Beispiel für geschätzte Kosten beim Erstellen einer Compute-Instanz." lightbox="media/concept-plan-manage-cost/create-resource.png" :::

Wenn Ihr Azure-Abonnement über ein Ausgabenlimit verfügt, wird von Azure verhindert, dass Ihre Ausgaben den Guthabenbetrag übersteigen. Beim Erstellen und Nutzen von Azure-Ressourcen wird Ihr Guthaben verwendet. Wenn Sie Ihr Guthabenlimit erreicht haben, werden die von Ihnen bereitgestellten Ressourcen für den Rest des Abrechnungszeitraums deaktiviert. Sie können Ihr Guthabenlimit nicht ändern, aber Sie können es entfernen. Weitere Informationen zu Ausgabenlimits finden Sie unter [Azure-Ausgabenlimit](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure-Ressourcen mit Azure Machine Learning verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit hängen vom Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder von der Einheitennutzung (Bytes, Megabytes usw.) ab. Sobald mit der Nutzung von Azure Machine Learning begonnen wird, fallen Kosten an, die in der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) angezeigt werden.

Bei Verwendung der Kostenanalyse zeigen Sie Azure Machine Learning-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle an. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

So zeigen Sie Azure Machine Learning-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.
3. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den Bereich „Azure Machine Learning“ aus.

Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

:::image type="content" source="media/concept-plan-manage-cost/all-costs.png" alt-text="Beispiel der akkumulierten Kosten für ein Abonnement." lightbox="media/concept-plan-manage-cost/all-costs.png" :::


Wählen Sie zum Eingrenzen der Kosten auf einen einzelnen Dienst (z. B. Azure Machine Learning) die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie anschließend **Azure Machine Learning** aus.

In diesem Beispiel werden nur die Kosten für Azure Machine Learning angezeigt.

:::image type="content" source="media/concept-plan-manage-cost/vm-specific-cost.png" alt-text="Beispiel der akkumulierten Kosten für „Dienstname“." lightbox="media/concept-plan-manage-cost/vm-specific-cost.png" :::

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und die Azure Machine Learning-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.
## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Weitere Optionen zum Verwalten und Reduzieren der Kosten für Azure Machine Learning

Verwenden Sie die folgenden Tipps, um Ihre Kosten für Computeressourcen zu verwalten und zu optimieren.

- Konfigurieren Sie Ihre Trainingscluster für automatische Skalierung
- Legen Sie Kontingente für Ihr Abonnement und Ihre Arbeitsbereiche fest
- Legen Sie Terminierungsrichtlinien für Ihre Trainingsläufe fest
- Verwenden Sie virtuelle Computer (VMs) mit niedriger Priorität
- Planen Sie das automatische Herunterfahren und Starten von Compute-Instanzen.
- Verwenden Sie eine reservierte Azure-VM-Instanz
- Trainieren Sie lokal
- Parallelisieren Sie das Training
- Legen Sie Richtlinien für die Aufbewahrung und das Löschen von Daten fest
- Stellen Sie Ressourcen in derselben Region bereit
- Löschen Sie Instanzen und Cluster, wenn Sie nicht planen, sie in naher Zukunft zu verwenden.

Weitere Informationen finden Sie unter [Verwalten und Optimieren von Kosten in Azure Machine Learning](how-to-manage-optimize-cost.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten und Optimieren von Kosten in Azure Machine Learning](how-to-manage-optimize-cost.md).
- [Verwalten von Budgets, Kosten und Kontingenten für Azure Machine Learning auf Unternehmensebene](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)
- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
