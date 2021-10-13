---
title: Übergang zur Verwaltung von Azure-Hybridvorteil auf Umfangsebene
description: In diesem Artikel werden die Änderungen und mehrere Übergangsszenarien beschrieben, um den Übergang zur Verwaltung von Azure-Hybridvorteil auf Umfangsebene zu veranschaulichen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: a813a8934ff66b10e0f3c7adce65887acebba6f8
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547315"
---
# <a name="transition-to-scope-level-management-of-azure-hybrid-benefit"></a>Übergang zur Verwaltung von Azure-Hybridvorteil auf Umfangsebene

Wenn Sie zur Verwaltung von Azure-Hybridvorteil auf Umfangsebene übergehen, entfällt die Notwendigkeit, den Vorteil auf Ressourcenebene zu konfigurieren. Dieser Artikel beschreibt die Änderungen und mehrere Übergangsszenarien, um das Ergebnis zu veranschaulichen. Für ein besseres Verständnis, wie die neue Lizenzverwaltung auf Umfangsebene Lizenzen und Rabatte auf Ihre Ressourcen anwendet, siehe: [Was ist die Verwaltung von Azure-Hybridvorteil auf Umfangsebene?](overview-azure-hybrid-benefit-scope.md)

## <a name="changes-to-individual-resource-configuration"></a>Änderungen an der individuellen Ressourcenkonfiguration

Wenn Sie einem Abonnement Lizenzen unter Verwendung der neuen Erfahrung zuweisen, werden die Änderungen im Azure-Portal angezeigt. Danach können Sie den Nutzen nicht mehr auf Ressourcenebene verwalten. Alle Änderungen, die Sie auf Bereichsebene vornehmen, überschreiben die Einstellungen auf der Ebene der einzelnen Ressource.

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="Screenshot von Informationen zum Überschreiben der SQL-Lizenz." lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> Wenn Sie die Einstellungen für den Azure-Hybridvorteil unter Verwendung von PowerShell, einer API oder außerhalb des Azure-Portals an einer Ressource ändern, wird Ihre Einstellungsänderung gespeichert. Sie hat jedoch keine Auswirkungen, solange das Abonnement oder Abrechnungskonto dieser Ressource durch Lizenzen abgedeckt ist, die einem Bereich zugewiesen sind. Wenn Sie jemals die Verwaltung von Azure-Hybridvorteil auf Umfangsebene kündigen, indem Sie alle Lizenzzuweisungen entfernen, werden Ihre Lizenzierungsrabatte wieder durch die Azure-Hybridvorteil-Einstellungen für jede Ressource bestimmt.

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>Prüfen Sie, wie viele SQL-Lizenzen Sie vor dem Übergang verwenden

Wenn Sie sich für die Verwaltung von Azure-Hybridvorteil-Erfahrung auf Umfangsebene anmelden, sehen Sie Ihre aktuelle Azure-Hybridvorteil-Nutzung, die für einzelne Ressourcen aktiviert ist. Weitere Informationen zur allgemeinen Erfahrung finden Sie unter [Erstellen von SQL Server-Lizenzzuweisungen für Azure-Hybridvorteil](create-sql-license-assignments.md). Wenn Sie ein Abonnement-Mitwirkender sind und nicht über die erforderliche Abrechnungsadministratorrolle verfügen, können Sie den Gebrauch der verschiedenen Arten von SQL Server-Lizenzen in Azure mithilfe eines PowerShell-Skripts analysieren. Das Skript generiert eine Momentaufnahme der Nutzung über mehrere Abonnements oder das gesamte Konto. Details und Beispiele für den Gebrauch des Skripts finden Sie im Beispielskript [sql-license-usage PowerShell](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit). Nachdem Sie das Skript ausgeführt haben, klären Sie mit Ihrem Abrechnungsadministrator die Möglichkeit, die Verwaltung von Azure-Hybridvorteil auf die Ebene des Abonnement- oder Abrechnungskontobereichs zu verlagern.

> [!NOTE]
> Das Skript umfasst Unterstützung für normalisierte Core-Lizenzen (NCL). 

## <a name="hadr-benefit-for-sql-server-vms"></a>HADR-Vorteil für SQL Server-VMs

Das neue Azure-Portal unterstützt die Vorteile der Hochverfügbarkeit und Disaster Recovery (HADR) für SQL Server-VMs vollständig. Wenn Ihre SQL Server-VM als HADR-Replikat konfiguriert ist, sind keine weiteren Aktionen erforderlich. Weitere Informationen zur Funktionsweise des SQL SERVER VM HADR-Vorteils finden Sie unter [SQL Server HADR und Azure-Hybridvorteil Koexistenz auf Umfangsebene](sql-server-hadr-licenses.md).

## <a name="transition-scenario-examples"></a>Beispiele für Übergangsszenarios

Lesen Sie die folgenden Beispiele für Übergangsszenarien, die am ehesten Ihrer Situation entsprechen.

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Migrieren Sie SQL-Workloads unter Verwendung von SQL Database mit Azure-Hybridvorteil von lokal in die Cloud

- **Lokale SQL Server-Workloads -** Zwei 16 Kerne-Computer, die geschäftskritische SQL Server Enterprise Edition-Datenbanken hosten, werden in die Cloud migriert.
- **Azure Ziel für die Migration der Workloads -** Nach einer Analyse wird entschieden, dass die Workloads auf zwei Azure SQL Managed Instances mit 16 Kernen ausgeführt werden sollen.
- **Lizenzen, die Azure zugewiesen werden sollen -** Unter Berücksichtigung der vorangegangenen Punkte werden 32 SQL Server Enterprise Edition Core-Lizenzen mit Software Assurance Azure zugewiesen.
- **Empfohlene Aktion -** Verwenden Sie das neue Azure-Portal, um 32 SQL Server Enterprise Edition-Core-Lizenzen dem entsprechenden Abonnement oder Gesamtabrechnungskonto zuzuweisen.
- **Ergebnis -** Eine einfach zu verwaltende, kostenoptimierte Migration von SQL Server-Workloads von lokal in die Cloud.

> [!NOTE] 
> Mit dem Azure-Hybridvorteil können die Azure zugewiesenen Lizenzen bis zu 180 Tage lang auch lokal genutzt werden, während die Workloads migriert, getestet und bereitgestellt werden.

### <a name="simplify-license-management-by-transitioning-to-scope-level-management-of-azure-hybrid-benefit"></a>Vereinfachen Sie das Verwalten von Abonnements, indem Sie auf die Verwaltung von Azure-Hybridvorteil auf Umfangsebene umsteigen

- **SQL Server-Ressourcen laufen –**  Ein 64-Core Azure SQL Database Business Critical läuft, wobei Azure-Hybridvorteil ausgewählt ist.
- **Verfügbare Lizenzen, die Azure zugewiesen werden können -** Ihr Beschaffungsteam bestätigt, dass es mehr als 64 SQL Server Enterprise Edition Core-Lizenzen mit Software Assurance gibt, die lokal nicht im Gebrauch sind.
- **Empfohlene Aktion -** Verwalten Sie die neue Verwaltung des Azure-Hybridvorteils auf Umfangsebene, um 64 SQL Server Enterprise Edition-Core-Lizenzen Azure zuzuweisen. Oder weisen Sie weitere Lizenzen nach Bedarf zu,wenn die Nutzung voraussichtlich bald zunehmen wird.
- **Ergebnis-** Der Übergang deckt die Kosten für die SQL Server-Software ab, so dass sich an der damit verbundenen Abrechnung nichts ändern sollte.

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>Sparen Sie mehr, indem Sie mehr SQL Server-Lizenzen zuweisen, um mehr Azure SQL-Ressourcen abzudecken

- **SQL Server-Ressourcen laufen -** Es laufen zwei Azure SQL Database Business Critical mit 16 Kernen, wobei Azure-Hybridvorteil nur für einen ausgewählt ist.
- **Verfügbare Lizenzen für die Zuweisung zu Azure -** Laut Ihrem Beschaffungsteam sind 48 SQL Server Enterprise Edition Core-Lizenzen mit Software Assurance weder lokal noch in Azure im Gebrauch.
- **Empfohlene Aktion -** Verwalten Sie die Verwaltung des Azure-Hybridvorteils auf Umfangsebene, um alle 48 SQL Server Enterprise-Edition-Core-Lizenzen zuzuweisen. Das sind 16 mehr als bei der Azure-Hybridvorteil-Auswahl auf Ressourcenebene.
- **Ergebnis -** Da mehr außerhalb von Azure gekaufte Lizenzen verwendet werden, sollten die in Azure in Rechnung gestellten Beträge reduziert werden. Außerdem: Da die Kosten pro Jahr für Software Assurance geringer sind als die jährlichen Kosten für SQL Server bei umlagefinanzierten Tarifen, werden Ihre Gesamtkosten für die SQL Server-Nutzung reduziert.

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>Stellen Sie Compliance wieder her, wenn eine übermäßige Nutzung von Azure-Hybridvorteilen festgestellt wird

- **SQL Server-Ressourcen laufen -** Es laufen drei universellen 8-Core-SQL-Datenbanken und eine 16-Core-SQL Server-VM für Unternehmen, wobei Azure-Hybridvorteil für alle ausgewählt ist. Die Anzahl der Azure-Hybridvorteil-Lizenzen, die dies erfordert, beträgt 24 Standard-Edition-Kerne + 16 SQL Server Enterprise-Kerne. Oder: 88 SQL Server Standard Edition (+ 0 SQL Server Enterprise Edition) - oder 22 SQL Server Enterprise (+ 0 SQL Server Standard Edition) könnten es auch abdecken. Das liegt daran, dass eine SQL Server Enterprise Edition Core-Lizenz und vier SQL Server Standard Edition Core-Lizenzen die gleiche Azure-Hybridvorteil-Nutzung für alle Azure SQL-Ressourcentypen abdecken können. Lesen Sie die Azure-Hybridvorteil-Regeln im Artikel [Was ist die Verwaltung von Azure-Hybridvorteil auf Umfangsebene?](overview-azure-hybrid-benefit-scope.md), um mehr über diese Flexibilität zu lernen.
- **Verfügbare Lizenzen für die Zuweisung zu Azure -** Laut Ihrem Beschaffungsteam sind 64 SQL Server Standard Edition Core-Lizenzen mit Software Assurance weder lokal noch in Azure in Gebrauch. Das ist weniger als die erforderliche Anzahl von 22 SQL Server Enterprise- oder 88 SQL Server Standard-Edition-Core-Lizenzen.
- **Empfohlene Aktion -** Um Compliance wiederherzustellen, identifizieren Sie 6 SQL Server Enterprise- oder 24 SQL Server Standard-Edition-Core-Lizenzen mit Software Assurance und weisen Sie diese sowie die bereits bestätigten 64 SQL Server Standard-Core-Lizenzen Azure zu, indem Sie Management auf Umfangsebene von Azure-Hybridvorteil nutzen.
- **Ergebnis -** Nicht-Einhaltung wird eliminiert und Azure-Hybridvorteil wird optimal genutzt, um die Kosten zu minimieren.
- **Alternative Maßnahme -** Weisen Sie Azure nur die verfügbaren 64 SQL Server Standard Edition Core-Lizenzen zu. Sie sind zwar konform, aber da diese Lizenzen nicht ausreichen, um die gesamte Azure-SQL-Nutzung abzudecken, werden Ihnen einige Nutzungsbasierte Gebühren berechnet.
## <a name="next-steps"></a>Nächste Schritte

- Folgen Sie dem Tutorial [Verwalten und Optimieren des Azure-Hybridvorteils für SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Gehen Sie zur Verwaltung von Lizenzen auf Umfangsebene über, indem Sie [SQL Server-Lizenzzuweisungen erstellen](create-sql-license-assignments.md).
- Lesen Sie das [FAQ zu Verwaltung von Azure-Hybridvorteil auf Umfangsebene](faq-azure-hybrid-benefit-scope.yml).