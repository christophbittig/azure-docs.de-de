---
title: Planen der Kostenverwaltung für Azure Synapse Analytics
description: Hier erfahren Sie, wie Sie Kosten für Azure Synapse Analytics mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: bd1e55c216a314bfc6e132979d5a4999d4f363eb
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850620"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planen und Verwalten der Kosten für Azure Synapse Analytics

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Synapse Analytics planen und verwalten. Bevor Sie Ressourcen für den Dienst zur Kostenschätzung hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten für Azure Synapse Analytics zu planen. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die voraussichtlichen Kosten. Nachdem Sie mit der Nutzung von Azure Synapse Analytics-Ressourcen begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Kosten für Azure Synapse Analytics stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Azure Synapse Analytics erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Schätzen der Kosten vor der Verwendung von Azure Synapse

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten zu schätzen, bevor Sie Azure Synapse Analytics hinzufügen.

Azure Synapse verfügt über verschiedene Ressourcen, für die unterschiedliche Kosten anfallen, wie in der folgenden Kostenschätzung zu sehen ist. 

![Beispiel für die geschätzten Kosten im Azure-Preisrechner](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Azure Synapse Analytics

Azure Synapse wird in einer Azure-Infrastruktur ausgeführt, für die beim Bereitstellen der neuen Ressource zusammen mit Azure Synapse Kosten anfallen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. 

### <a name="how-youre-charged-for-azure-synapse-analytics"></a>Die Gebühren für Azure Synapse Analytics

Wenn Sie Azure Synapse Analytics-Ressourcen erstellen oder verwenden, werden Ihnen möglicherweise die folgenden Verbrauchseinheiten in Rechnung gestellt:

- Datenauswertung und Data Warehousing 
    - Dedizierter SQL-Pool: Ihnen werden Gebühren basierend auf der Anzahl von DWU-Blöcken und den ausgeführten Stunden in Rechnung gestellt.
    - Speicher: Ihnen werden Gebühren basierend auf der Anzahl der gespeicherten TBs in Rechnung gestellt.
    - Serverloser SQL-Pool - Die Abrechnung erfolgt nach dem TB der verarbeiteten Daten.
- Apache Spark Pool: Ihnen werden Gebühren basierend auf der Anzahl der Instanzen und der ausgeführten Stunden in Rechnung gestellt.
- Datenintegration 
    - Orchestrierungsaktivitätsausführungen: Ihnen werden Gebühren basierend auf der Anzahl der Aktivitätsausführungen in Rechnung gestellt.
    - Datenverschiebung: Für Kopieraktivitäten, die auf Azure Integration Runtime ausgeführt werden, werden Ihnen Gebühren basierend auf der Anzahl der verwendeten DIU und der Ausführungsdauer in Rechnung gestellt.
    - Datenflüsse vCore-Stunden: Für das Ausführen und das Debuggen von Datenflüssen werden Ihnen Gebühren basierend auf Computetyp, Anzahl der virtuellen Kerne und Ausführungsdauer berechnet.

Am Ende Ihres Abrechnungszeitraums werden die Gebühren für die einzelnen Verbrauchseinheiten summiert. Ihre Rechnung zeigt einen Abschnitt für alle Azure Synapse Analytics-Kosten an. Für jede Verbrauchseinheit besteht ein separates Zeilenelement.

### <a name="other-costs-that-might-accrue-with-azure-synapse-analytics"></a>Sonstige ggf. anfallende Kosten für Azure Synapse Analytics

Wenn Sie Ressourcen für Azure Synapse erstellen, werden auch Ressourcen für andere Azure-Dienste erstellt. Dazu gehören:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Nachdem Sie Azure Synapse-Ressourcen gelöscht haben, sind die folgenden Ressourcen möglicherweise weiterhin vorhanden. Hierfür fallen weiter Kosten an, bis Sie sie löschen.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Verwenden der Azure-Vorauszahlungsgutschrift mit Azure Synapse 

Sie können Azure Synapse-Gebühren mit der Azure-Vorauszahlungsgutschrift begleichen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

### <a name="pre-purchase-plan-for-azure-synapse"></a>Erwerbsplan mit Vorauszahlung für Azure Synapse

Sie können Kosten für Azure Synapse Analytics sparen, indem Sie Azure Synapse-Commit-Einheiten (SCUs) für ein Jahr im Voraus erwerben. Sie können die vorab erworbenen SCUs jederzeit während der Laufzeit des Kaufs nutzen. Weitere Informationen finden Sie unter [Optimieren der Kosten für Azure Synapse Analytics mit einem Erwerbsplan mit Vorauszahlung](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md).


## <a name="review-estimated-costs-in-the-azure-portal"></a>Überprüfen von geschätzten Kosten im Azure-Portal

Wenn Sie Ressourcen für Azure Synapse Analytics erstellen, werden die geschätzten Kosten angezeigt. Ein Arbeitsbereich verfügt über einen serverlosen SQL-Pool, der mit dem Arbeitsbereich erstellt wurde. Für den serverlosen SQL-Pool fallen erst dann Gebühren an, wenn Sie Abfragen ausführen. Andere Ressourcen, wie z. B. dedizierte SQL-Pools und serverlose Apache Spark-Pools, müssen innerhalb des Arbeitsbereichs erstellt werden.

So erstellen Sie Azure Synapse Analytics Arbeitsbereich und rufen den geschätzten Preis auf:

1. Navigieren Sie im Azure-Portal zum Dienst.
2. Erstellen Sie die Ressource.
3. Überprüfen Sie den in der Zusammenfassung angezeigten geschätzten Preis.
4. Schließen Sie die Ressourcenerstellung ab.

![Beispiel für geschätzte Kosten beim Erstellen einer Ressource](./media/plan-manage-costs/create-workspace-cost.png)


Wenn Ihr Azure-Abonnement über ein Ausgabenlimit verfügt, wird von Azure verhindert, dass Ihre Ausgaben den Guthabenbetrag übersteigen. Beim Erstellen und Nutzen von Azure-Ressourcen wird Ihr Guthaben verwendet. Wenn Sie Ihr Guthabenlimit erreicht haben, werden die von Ihnen bereitgestellten Ressourcen für den Rest des Abrechnungszeitraums deaktiviert. Sie können Ihr Guthabenlimit nicht ändern, aber Sie können es entfernen. Weitere Informationen zu Ausgabenlimits finden Sie unter [Azure-Ausgabenlimit](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure Synapse-Ressourcen verwenden, entstehen Ihnen Kosten. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.) Sobald Sie mit der Nutzung von Ressourcen in Azure Synapse beginnen, fallen Kosten an, die unter [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) angezeigt werden.

Bei Verwendung der Kostenanalyse können Sie Kosten für Azure Synapse Analytics in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

So zeigen Sie Azure Synapse-Kosten in der Kostenanalyse an

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den Bereich, entweder das Abonnement oder die Ressourcengruppe, im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.
3. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den Bereich „Azure Synapse“ aus.

Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

![Beispiel der akkumulierten Kosten für ein Abonnement](./media/plan-manage-costs/actual-monthly-costs.png)

- Wählen Sie zum Eingrenzen der Kosten auf einen Dienst, z. B. Azure Synapse, die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **Azure Synapse Analytics** aus.

In diesem Beispiel werden nur die Kosten für Azure Synapse angezeigt.

![Beispiel der akkumulierten Kosten für ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und Azure Synapse-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den beim Erstellen eines Budgets verfügbaren Filteroptionen finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Weitere Optionen zum Verwalten und Reduzieren der Kosten für Azure Synapse 

### <a name="serverless-sql-pool"></a>Serverloser SQL-Pool

Weitere Informationen zu den Kosten für den serverlosen SQL-Pool finden Sie unter [Kostenverwaltung für serverlose SQL-Pools in Azure Synapse Analytics](./sql/data-processed.md).

### <a name="dedicated-sql-pool"></a>Dedizierter SQL-Pool

Sie können die Kosten für einen dedizierten SQL-Pool steuern, indem Sie die Ressource pausieren, wenn sie nicht verwendet wird. Wenn Sie die Datenbank z.B. nachts oder am Wochenende nicht verwenden, können Sie sie während dieser Zeiträume anhalten und während des Tages wieder fortsetzen. Weitere Informationen finden Sie unter [Anhalten und Fortsetzen von Computeressourcen im dedizierten Synapse-SQL-Pool über das Azure-Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?context=/azure/synapse-analytics/context/context).

### <a name="serverless-apache-spark-pool"></a>Serverloser Apache Spark-Pool

Um die Kosten für Ihren serverlosen Apache Spark-Pool zu kontrollieren, aktivieren Sie das serverlose Feature „Automatische Pause“ für Apache Spark, und legen Sie den Timeoutwert entsprechend fest.  Wenn Sie Synapse Studio für Ihre Entwicklung verwenden, sendet das Studio eine „Keepalive“-Nachricht, damit die Sitzung aktiv bleibt, was ebenfalls konfigurierbar ist. Legen Sie also einen kurzen Timeoutwert für die automatische Pause fest.  Wenn Sie fertig sind, schließen Sie Ihre Sitzung und der Apache Spark-Pool wird automatisch angehalten, sobald der Timeoutwert erreicht ist.
 
Erstellen Sie während der Entwicklung mehrere Apache Spark-Pooldefinitionen unterschiedlicher Größe.  Das Erstellen von Apache Spark-Pooldefinitionen ist kostenlos und Ihnen wird nur deren Nutzung in Rechnung gestellt.  Die Nutzung von Apache Spark in Azure Synapse wird pro vCore-Stunde und anteilig pro Minute berechnet.  Verwenden Sie z. B. kleine Poolgrößen für die Codeentwicklung und -validierung, während Sie umfangreichere Poolgrößen für Leistungstests verwenden.

### <a name="data-integration---pipelines-and-data-flows"></a>Datenintegration: Pipelines und Datenflüsse 

Weitere Informationen zu den Kosten für die Datenintegration finden Sie unter [Planen und Verwalten von Kosten für Azure Data Factory](../data-factory/plan-manage-costs.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
- Erfahren Sie mehr über die Planung und Verwaltung von Kosten für [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md).
