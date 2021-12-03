---
title: Planen und Verwalten von Kosten
description: Erfahren Sie, wie Sie die Kosten für Azure SQL-Datenbank mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 5f1464c6fb74bc04dacb230cd2a5993f7566e82a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546878"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planen und Verwalten der Kosten für Azure SQL-Datenbank

In diesem Artikel erfahren Sie, wie Sie die Kosten für Azure SQL-Datenbank planen und verwalten. 

Zunächst verwenden Sie den Azure-Preisrechner zum Hinzufügen der Azure-Ressourcen und überprüfen die geschätzten Kosten. Nachdem Sie mit der Nutzung von Azure SQL-Datenbank-Ressourcen begonnen haben, können Sie Cost Management-Features verwenden, um Budgets festzulegen und Kosten zu überwachen. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends ermitteln, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind. Die Kosten für Azure SQL-Datenbank stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Azure SQL-Datenbank erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die unter Ihrem Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern.

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse unterstützt die meisten Azure-Kontotypen, jedoch nicht alle. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf ein Azure-Konto verfügen. 

Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Anfängliche Kostenaspekte bei SQL-Datenbank

Beim Arbeiten mit Azure SQL-Datenbank sind mehrere Aspekte zum Kostensparen zu beachten:

### <a name="vcore-or-dtu-purchasing-models"></a>vCore- oder DTU-basierte Kaufmodelle 

Azure SQL-Datenbank unterstützt zwei Kaufmodelle: vCore- und DTU-basiert. Die Abrechnung richtet sich nach dem Kaufmodell. Es ist daher beim Planen und Abwägen der Kosten wichtig, zu verstehen, welches Modell am besten für Ihre Workload geeignet ist. Informationen zu den vCore- und DTU-basierten Kaufmodellen finden Sie unter [Auswählen zwischen dem vCore-basierten und dem DTU-basierten Kaufmodell](purchasing-models.md).

### <a name="provisioned-or-serverless"></a>Bereitgestellt oder serverlos

Beim vCore-basierten Kaufmodell unterstützt Azure SQL-Datenbank ebenfalls zwei Arten von Computeebenen: bereitgestellten Durchsatz und serverlos. Die Abrechnung variiert je nach Computeebene. Es ist daher beim Planen und Abwägen der Kosten wichtig, zu verstehen, welche am besten für Ihre Workload geeignet ist. Weitere Informationen finden Sie unter [Übersicht über das Modell für virtuelle Kerne – Computetarife](service-tiers-sql-database-vcore.md#compute-tiers).

Bei der bereitgestellten Computeebene des vCore-basierten Kaufmodells können Sie Ihre vorhandenen Lizenzen gegen Rabattpreise tauschen. Einzelheiten finden Sie unter [Azure-Hybridvorteil](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pools für elastische Datenbanken

In Umgebungen mit mehreren Datenbanken mit variablen und unvorhersehbaren Verbrauchsanforderungen können Pools für elastische Datenbanken im Vergleich zur Bereitstellung derselben Anzahl von Singletons Kosteneinsparungen bieten. Details finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Schätzen der Kosten für Azure SQL-Datenbank

Nutzen Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten für verschiedene Konfigurationen von Azure SQL-Datenbank zu schätzen. Weitere Informationen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

Die Informationen und Preise in der folgenden Abbildung dienen nur als Beispiele: 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Beispiel für den Azure SQL-Datenbank-Preisrechner":::

Sie können auch abschätzen, wie sich verschiedene Optionen für Aufbewahrungsrichtlinien auf die Kosten auswirken. Die Informationen und Preise in der folgenden Abbildung dienen nur als Beispiele:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Beispiel für den Azure SQL-Datenbank-Preisrechner für den Speicher":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Azure SQL-Datenbank

Azure SQL-Datenbank wird in einer Azure-Infrastruktur ausgeführt, für die beim Bereitstellen der neuen Ressource zusammen mit Azure SQL-Datenbank Kosten anfallen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann.  

Azure SQL-Datenbank wird nach einem vorhersagbaren Stundensatz abgerechnet (nicht beim serverlosen Modell). Wenn die SQL-Datenbank weniger als eine Stunde aktiv war, wird Ihnen die höchste ausgewählte Dienstebene, die Höchstmenge an bereitgestellten Speicherkapazitäten und E/As während dieser Stunde in Rechnung gestellt – unabhängig von der Nutzung und davon, ob die Datenbank weniger als eine Stunde aktiv war.

Die Abrechnung hängt von der SKU Ihres Produkts, der Generation der Hardware Ihrer SKU und der Kategorie der Verbrauchseinheit ab. Für Azure SQL-Datenbank sind die folgenden SKUs möglich:

- Basic (B)
- Standard (S)
- Premium (P)
- Universell (GP)
- Unternehmenskritisch (BC)
- Für Speicher sind georedundanter Speicher (GRS), lokal redundanter Speicher (LRS) und zonenredundanter Speicher (ZRS) verfügbar.
- Es ist auch möglich, eine veraltete SKU aus veralteten Ressourcenangeboten zu verwenden.

Weitere Informationen finden Sie unter [Dienstebenen](service-tiers-general-purpose-business-critical.md). 

Die folgende Tabelle zeigt die gängigsten Verbrauchseinheiten für die Abrechnung und die möglichen SKUs für **Singletons**: 

| Messung| Mögliche SKU(s) | BESCHREIBUNG | 
| :----|:----|:----|
| Sicherung\* | GP/BC/HS | Misst die Speichernutzung durch Sicherungen und wird nach der genutzten Speichermenge in GB pro Monat abgerechnet. | 
| Sicherung (LTR) | GRS/LRS/ZRS/GF | Misst die Speichernutzung durch langfristige Sicherungen, die über die Langzeitaufbewahrung konfiguriert sind, und wird nach der genutzten Speichermenge abgerechnet. | 
| Compute  | B/S/P/GP/BC  | Misst den Verbrauch Ihrer Computeressourcen pro Stunde. | 
| Compute (primäres/benanntes Replikat) | HS | Misst den Verbrauch Ihrer Computeressourcen pro Stunde für Ihr primäres HS-Replikat. 
| Compute (Hochverfügbarkeitsreplikat)             | HS | Misst den Verbrauch Ihrer Computeressourcen pro Stunde für Ihr sekundäres HS-Replikat. | 
| Compute (ZR-Add-On)              | GP | Misst den Verbrauch Ihrer Computeressourcen pro Minute für Ihr zonenredundantes hinzugefügtes Replikat. | 
| Compute (serverlos)             | GP | Misst den Verbrauch Ihrer serverlosen Computeressourcen pro Minute.  | 
| Lizenz | GP/BC/HS | Die monatliche Abrechnung für Ihre SQL Server-Lizenz. | 
| Speicher | B/S\*/P\*/G/BC/HS | Wird monatlich abgerechnet nach der pro Stunde gespeicherten Datenmenge. |

\* Beim DTU-Kaufmodell wird eine anfängliche Speichermenge für Daten und Sicherungen ohne zusätzliche Kosten bereitgestellt. Die Größe des Speichers hängt von der ausgewählten Dienstebene ab. Zusätzlicher Datenspeicher kann in den Standard- und Premium-Tarifen erworben werden. Weitere Informationen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

Die folgende Tabelle zeigt die gängigsten Verbrauchseinheiten für die Abrechnung und die möglichen SKUs für **Pools für elastische Datenbanken**: 

| Messung| Mögliche SKU(s) | BESCHREIBUNG | 
|:----|:----|:----|
| Sicherung\* | GP/BC  | Misst die Speichernutzung durch Sicherungen und wird nach GB pro Stunde monatlich abgerechnet. | 
| Compute  | B/S/P/GP/BC | Misst den Verbrauch Ihrer Computeressourcen pro Stunde, z. B. virtuelle Kerne und Arbeitsspeicher oder Datenbanktransaktionseinheiten (Database Transaction Units, DTUs). | 
| Lizenz | GP/BC | Die monatliche Abrechnung für Ihre SQL Server-Lizenz. | 
| Speicher | B/S\*/P\*/GP/HS | Wird monatlich abgerechnet, sowohl nach der auf dem Laufwerk gespeicherten Datenmenge (Speicherplatz pro Stunde) als auch nach dem Durchsatz in Megabits pro Sekunde (MBPS). |

\* Beim DTU-Kaufmodell wird eine anfängliche Speichermenge für Daten und Sicherungen ohne zusätzliche Kosten bereitgestellt. Die Größe des Speichers hängt von der ausgewählten Dienstebene ab. Zusätzlicher Datenspeicher kann in den Standard- und Premium-Tarifen erworben werden. Weitere Informationen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

### <a name="using-monetary-credit-with-azure-sql-database"></a>Verwenden von Guthaben mit Azure SQL-Datenbank

Sie können Gebühren für Azure SQL-Datenbank mit der Azure-Vorauszahlungsgutschrift (früher als Mindestverbrauch bezeichnet) bezahlen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Überprüfen von geschätzten Kosten im Azure-Portal

Sie können während der Erstellung einer Azure SQL-Datenbank-Instanz die geschätzten Kosten beim Konfigurieren der Computeebene einsehen. 

Um diesen Bildschirm zu öffnen, wählen Sie auf der Registerkarte **Grundlagen** der Seite **SQL-Datenbank erstellen** die Option **Datenbank konfigurieren** aus. Die Informationen und Preise in der folgenden Abbildung dienen nur als Beispiele:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Beispiel für die Kostenschätzung im Azure-Portal":::

Wenn Ihr Azure-Abonnement über ein Ausgabenlimit verfügt, wird von Azure verhindert, dass Ihre Ausgaben den Guthabenbetrag übersteigen. Beim Erstellen und Nutzen von Azure-Ressourcen wird Ihr Guthaben verwendet. Wenn Sie Ihr Guthabenlimit erreicht haben, werden die von Ihnen bereitgestellten Ressourcen für den Rest des Abrechnungszeitraums deaktiviert. Sie können Ihr Guthabenlimit nicht ändern, aber Sie können es entfernen. Weitere Informationen zu Ausgabenlimits finden Sie unter [Azure-Ausgabenlimit](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Überwachen der Kosten

Sobald Sie mit der Verwendung von Azure SQL-Datenbank beginnen, können Sie die geschätzten Kosten im Portal anzeigen. Führen Sie die folgenden Schritte aus, um die Kostenschätzung zu überprüfen:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zur Ressourcengruppe für Ihre Azure SQL-Datenbank. Sie finden die Ressourcengruppe, indem Sie zu Ihrer Datenbank navigieren und im Abschnitt **Übersicht** die Option **Ressourcengruppe** auswählen.
1. Wählen Sie im Menü die Option **Kostenanalyse** aus.
1. Zeigen Sie **Akkumulierte Kosten** an, und legen Sie das Diagramm unten auf **Dienstname** fest. Dieses Diagramm zeigt eine Schätzung Ihrer aktuellen Kosten für SQL-Datenbank. Um die Kosten für die gesamte Seite auf Azure SQL-Datenbank einzugrenzen, wählen Sie **Filter hinzufügen** und dann **Azure SQL-Datenbank** aus. Die Informationen und Preise in der folgenden Abbildung dienen nur als Beispiele:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Beispiel für akkumulierte Kosten im Azure-Portal":::

Von hier aus können Sie die Kosten selbst überprüfen. Weitere Informationen zu den unterschiedlichen Einstellungen für die Kostenanalyse finden Sie unter [Verwenden von Kostenwarnungen zum Überwachen von Verbrauch und Ausgaben](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie weitere Analysen Ihrer Kosten durchführen müssen. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Weitere Optionen zum Verwalten und Reduzieren der Kosten für Azure SQL-Datenbank

Mit Azure SQL-Datenbank können Sie Ressourcen auch zentral auf- oder abskalieren, um die Kosten basierend auf Ihren Anwendungsanforderungen zu steuern. Weitere Informationen finden Sie unter [Dynamisches Skalieren von Datenbankressourcen](scale-resources.md).

Mit einer Reservierung von Computeressourcen für ein bis drei Jahre können Sie weitere Kosten sparen. Weitere Informationen dazu finden Sie unter [Sparen von Kosten für Ressourcen mit reservierter Kapazität](reserved-capacity-overview.md).


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.