---
title: Planen der Kostenverwaltung für Azure Data Factory
description: Hier erfahren Sie, wie Sie Kosten für Azure Data Factory mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.subservice: pricing
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 11/01/2021
ms.openlocfilehash: 51f71bcf39f0b54bf0f7fb389fbbb9797fbb2151
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469462"
---
# <a name="plan-to-manage-costs-for-azure-data-factory"></a>Planen der Kostenverwaltung für Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Data Factory planen und verwalten. 

Zunächst verwenden Sie zu Beginn des ETL-Projekts zur Kostenschätzung eine Kombination aus Azure-Preisen und Verbrauchs- und Preisrechnern pro Pipeline, um die Kosten für Azure Data Factory zu planen, bevor Sie Ressourcen für den Dienst hinzufügen. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die voraussichtlichen Kosten. Nachdem Sie mit der Nutzung von Azure Data Factory-Ressourcen begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen, und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen, und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Kosten für Azure Data Factory stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Data Factory erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse in Cost Management unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-data-factory"></a>Kostenschätzung vor der Verwendung von Azure Data Factory
 
Verwenden Sie den [ADF-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=data-factory), um eine Schätzung der Kosten für die Ausführung Ihrer ETL-Workload in Azure Data Factory zu erhalten.  Um den Rechner zu verwenden, müssen Sie Details wie die Anzahl der Aktivitätsausführungen, die Anzahl der Stunden für Datenintegrationseinheiten, den für den Datenfluss verwendeten Computetyp, die Anzahl der Kerne, die Anzahl der Instanzen, die Ausführungsdauer usw. eingeben.

Eine der häufig gestellten Fragen zum Preisrechner ist, welche Werte als Eingaben verwendet werden sollen.  Während der Proof-of-Concept-Phase können Sie Testläufe mithilfe von Beispieldatasets durchführen, um den Verbrauch verschiedener ADF-Einheiten zu ermitteln.  Anschließend können Sie basierend auf der Nutzung des Beispieldatasets eine Projektion der Nutzung für das vollständige Dataset und den Operationalisierungszeitplan erstellen.

> [!NOTE]
> Die in diesen Beispielen verwendeten Preise sind hypothetisch und stellen keine tatsächliche Preisgestaltung dar.

Angenommen, Sie müssen täglich 1 TB Daten von AWS S3 zu Azure Data Lake Gen2 verschieben.  Sie können ein Proof of Concept für das Verschieben von 100 GB Daten durchführen, um den Durchsatz der Datenerfassung zu messen und den entsprechenden abgerechneten Verbrauch zu ermitteln.

Im Folgenden finden Sie ein Detailbeispiel für die Ausführung der Kopieraktivität (Ihre tatsächliche Zeit hängt von der Form Ihres spezifischen Datasets, von Netzwerkgeschwindigkeiten, von Ausgangsgrenzwerten beim S3-Konto, von Eingangsgrenzwerten bei ADLS Gen2 und anderen Faktoren ab).

:::image type="content" source="media/plan-manage-costs/s3-copy-run-details.png" alt-text="Ausführung der S3-Kopieraktivität":::

Durch Verwendung der [Überwachung des Verbrauchs auf Pipelineausführungsebene](#monitor-consumption-at-pipeline-run-level) können Sie den entsprechenden Verbrauch für die Datenverschiebungseinheiten abrufen:

:::image type="content" source="media/plan-manage-costs/s3-copy-pipeline-consumption.png" alt-text="Verbrauch für die S3-Kopierpipeline":::

Folglich beträgt die Gesamtzahl von DIU-Stunden (Datenintegrationseinheiten) für den gesamten Monat, die für die Verschiebung von 1 TB pro Tag benötigt wird:

1,2667 (DIU-Stunden) * (1 TB / 100 GB) * 30 (Tage in einem Monat) = 380 DIU-Stunden

Nun können Sie 30 Aktivitätsausführungen und 380 DIU-Stunden im ADF-Preisrechner eingeben, um eine Schätzung ihrer monatlichen Rechnung zu erhalten:

:::image type="content" source="media/plan-manage-costs/s3-copy-pricing-calculator.png" alt-text="Preisrechner für den S3-Kopiervorgang":::

## <a name="understand-the-full-billing-model-for-azure-data-factory"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Azure Data Factory

Azure Data Factory wird auf der Azure-Infrastruktur ausgeführt, für die Kosten anfallen, wenn Sie neue Ressourcen bereitstellen. Es ist wichtig zu verstehen, dass möglicherweise andere zusätzliche Infrastrukturkosten anfallen.

### <a name="how-youre-charged-for-azure-data-factory"></a>Die Gebühren für Azure Data Factory

Azure Data Factory ist ein serverloser und elastischer Datenintegrationsdienst, der für die Cloud skaliert wurde.  Dies bedeutet, dass es keine feste Computegröße gibt, die Sie bei der Planung als Spitzenlast berücksichtigen müssen. Stattdessen geben Sie an, wie viele Ressourcen bei Bedarf pro Vorgang zuzuordnen sind, sodass Sie die ETL-Prozesse auf eine besser skalierbare Weise gestalten können. Außerdem wird ADF auf Basis eines verbrauchsbasierten Plans abgerechnet, sodass Sie nur für die tatsächliche Nutzung bezahlen.

Wenn Sie Azure Data Factory-Ressourcen erstellen oder verwenden, werden Ihnen möglicherweise die folgenden Verbrauchseinheiten in Rechnung gestellt:

- Orchestrierungsaktivitätsausführungen: Ihnen werden Gebühren basierend auf der Anzahl der orchestrierten Aktivitätsausführungen in Rechnung gestellt.
- Datenintegrationseinheitsstunden (DIU): Für Kopieraktivitäten, die auf Azure Integration Runtime ausgeführt werden, werden Ihnen Gebühren basierend auf der Anzahl der verwendeten DIU und der Ausführungsdauer in Rechnung gestellt.
- vCore-Stunden: Für das Ausführen und das Debuggen von Datenflüssen werden Ihnen Gebühren basierend auf Computetyp, Anzahl der virtuellen Kerne und Ausführungsdauer berechnet.


Am Ende Ihres Abrechnungszeitraums werden die Gebühren für die einzelnen Verbrauchseinheiten summiert. Ihre Rechnung zeigt einen Abschnitt für alle Azure Data Factory-Kosten an. Für jede Verbrauchseinheit besteht ein separates Zeilenelement.

### <a name="other-costs-that-might-accrue-with-azure-data-factory"></a>Andere Kosten, die bei Azure Data Factory anfallen können

Wenn Sie Ressourcen für die Azure Data Factory (ADF) erstellen, werden auch Ressourcen für andere Azure-Dienste erstellt. Dazu gehören:

- Pipelineaktivitätsausführung
- Externe Pipelineaktivitätsausführung
- Erstellen/Bearbeiten/Abrufen/Überwachen von Data Factory-Artefakten
- Dauer der SSIS Integration Runtime (IR) basierend auf Instanztyp und Dauer

> [!NOTE]
> Sie können Ihren ADF- und anderen Azure-Ressourcen das gleiche Tag zuweisen, indem Sie diese in die gleiche Kategorie setzen, um ihre konsolidierte Abrechnung einzusehen. Das ADF-Tag wird von allen SSIS-IRs darin geerbt. Wenn Sie Ihr ADF-Tag ändern, müssen Sie alle SSIS-IRs beenden und neu starten, damit sie das neue Tag erben können. Weitere Informationen finden Sie im Abschnitt [Neukonfigurieren der SSIS IR](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir).

### <a name="using-azure-prepayment-with-azure-data-factory"></a>Verwenden der Azure-Vorauszahlung mit Azure Data Factory

Sie können Azure Data Factory-Gebühren mit der Azure-Vorauszahlungsgutschrift begleichen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="monitor-costs"></a>Überwachen der Kosten

Azure Data Factory-Kosten können auf Factory-, Pipeline- und Aktivitätsausführungsebene überwacht werden.

### <a name="monitor-costs-at-factory-level"></a>Überwachen der Kosten auf Factory-Ebene

Wenn Sie Azure-Ressourcen mit Data Factory verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.) Sobald mit der Data Factory-Nutzung begonnen wird, fallen Kosten an, und Sie können die Kosten in der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) sehen.

Bei Verwendung der Kostenanalyse können Sie Data Factory-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

So zeigen Sie Data Factory-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie **Bereich** aus, um zu einem anderen Bereich der Kostenanalyse zu wechseln.
3. Standardmäßig werden die Kosten für Dienste im ersten Ringdiagramm angezeigt. Wählen Sie im Diagramm den Bereich „Azure Data Factory v2“ aus.

Die tatsächlichen monatlichen Kosten werden beim ersten Öffnen der Kostenanalyse angezeigt. Im folgenden Beispiel werden alle monatlichen Nutzungskosten angezeigt.

:::image type="content" source="media/all-costs.png" alt-text="Beispiel der akkumulierten Kosten für ein Abonnement":::

- Wählen Sie zum Eingrenzen der Kosten auf einen Dienst wie Data Factory die Option **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **Azure Data Factory v2** aus.

Im folgenden Beispiel werden nur die Kosten für Data Factory angezeigt.

:::image type="content" source="media/service-specific-cost.png" alt-text="Beispiel der akkumulierten Kosten für ServiceName":::

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und Data Factory-Kosten nach Ressourcengruppe werden ebenfalls angezeigt. Von hier aus können Sie die Kosten selbst überprüfen.

### <a name="monitor-consumption-at-pipeline-run-level"></a>Überwachen der Nutzung auf Pipelineausführungsebene

Je nach Typ der Aktivitäten in der Pipeline, Menge der zu verschiebenden und transformierenden Daten und Komplexität der Transformation werden bei der Ausführung einer Pipeline verschiedene Verbrauchseinheiten für die Abrechnung in Azure Data Factory gezählt.

Sie können den Umfang der Nutzung für verschiedene Verbrauchseinheiten für einzelne Pipelineausführungen auf der Azure Data Factory-Benutzeroberfläche anzeigen. Um die Benutzeroberfläche für die Überwachung zu öffnen, wählen Sie die Kachel **Überwachen und verwalten** im Blatt „Data Factory“ des [Azure-Portals](https://portal.azure.com/) aus. Wenn Sie sich bereits auf der Benutzeroberfläche von ADF befinden, klicken Sie auf der linken Seitenleiste auf das Symbol **Überwachen**. Die standardmäßige Überwachungsansicht ist eine Liste der Pipelineausführungen.

Wenn Sie auf die Schaltfläche **Verbrauch** neben dem Namen der Pipeline klicken, wird ein Popupfenster angezeigt, in dem die aggregierte Nutzung der Pipelineausführung für alle Aktivitäten in der Pipeline angezeigt wird.

:::image type="content" source="media/plan-manage-costs/pipeline-run-consumption.png" alt-text="Nutzung durch die Pipelineausführung":::

:::image type="content" source="media/plan-manage-costs/pipeline-consumption-details.png" alt-text="Details zur Pipelinenutzung":::

In der Ansicht für die Nutzung durch die Pipelineausführung wird der für jede ADF-Verbrauchseinheit genutzte Betrag für die jeweilige Pipeline ausgeführt, der tatsächliche Preis wird jedoch nicht angezeigt, da der Betrag, der Ihnen in Rechnung gestellt wird, vom Typ Ihres Azure-Kontos und vom verwendeten Währungstyp abhängig ist.  Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md).

### <a name="monitor-consumption-at-activity-run-level"></a>Überwachen der Nutzung auf Aktivitätsausführungsebene
Nachdem Sie den aggregierten Verbrauch auf Pipelineausführungsebene ermittelt haben, müssen Sie in bestimmten Szenarien einen weiteren Drilldown ausführen und ermitteln, welche Aktivität in der Pipeline die teuerste ist.

Um die Nutzung auf Aktivitätsausführungsebene abzurufen, wechseln Sie zur Benutzeroberfläche **Erstellen und überwachen** Ihrer Data Factory. Auf der Registerkarte **Überwachen** wird eine Liste der Pipelineausführungen angezeigt. Klicken Sie auf den Link **Pipelinename**, um auf die Liste von Aktivitätsausführungen in der Pipelineausführung zuzugreifen.  Klicken Sie auf die Schaltfläche **Ausgabe** neben dem Aktivitätsnamen, und suchen Sie in der JSON-Ausgabe nach der Eigenschaft **billableDuration**:

Im Folgenden sehen Sie eine Beispielausgabe für eine Kopieraktivitätsausführung:

:::image type="content" source="media/plan-manage-costs/copy-output.png" alt-text="Ausgabe für Kopiervorgang":::

Hier sehen Sie eine Beispielausgabe für die Ausführung einer Zuordnungsdatenflussaktivität:

:::image type="content" source="media/plan-manage-costs/dataflow-output.png" alt-text="Datenflussausgabe":::

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den beim Erstellen eines Budgets verfügbaren Filteroptionen finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
- [Azure Data Factory – Preise](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Grundlegendes zu Azure Data Factory-Preisen anhand von Beispielen](./pricing-concepts.md)
- [Azure Data Factory-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
