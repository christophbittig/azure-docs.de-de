---
title: Überwachen von delegierten Ressourcen in beliebigem Umfang
description: Azure Lighthouse unterstützt Sie dabei, Azure Monitor-Protokolle für alle Kundenmandanten auf skalierbare Weise zu verwenden.
ms.date: 08/12/2021
ms.topic: how-to
ms.openlocfilehash: d261fd41c300f317e34ff7cacafa53911b7bbc12
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736620"
---
# <a name="monitor-delegated-resources-at-scale"></a>Überwachen von delegierten Ressourcen in beliebigem Umfang

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von skalierbaren Vorgängen für mehrere Mandanten, wodurch Verwaltungsaufgaben effizienter werden.

In diesem Thema erfahren Sie, wie Sie [Azure Monitor-Protokolle](../../azure-monitor/logs/data-platform-logs.md) für alle von Ihnen verwalteten Mandanten auf skalierbare Weise verwenden. Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

> [!NOTE]
> Sorgen Sie dafür, dass Benutzern in Ihren Verwaltungsmandanten die [erforderlichen Rollen für die Verwaltung von Log Analytics-Arbeitsbereichen](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) für Ihre delegierten Kundenabonnements zugeteilt wurden.

## <a name="create-log-analytics-workspaces"></a>Erstellen von Log Analytics-Arbeitsbereichen

Um Daten zu sammeln, müssen Sie Log Analytics-Arbeitsbereiche erstellen. Diese Log Analytics-Arbeitsbereiche sind einzigartige Umgebungen für Daten, die von Azure Monitor gesammelt werden. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration. Datenquellen und Lösungen sind so konfiguriert, dass die zugehörigen Daten in einem bestimmten Arbeitsbereich gespeichert werden.

Es wird empfohlen, diese Arbeitsbereiche direkt in den Kundenmandanten zu erstellen. Auf diese Weise verbleiben die Daten in den Mandanten und werden nicht in Ihren Mandanten exportiert. Dies ermöglicht auch eine zentrale Überwachung aller Ressourcen und Dienste, die von Log Analytics unterstützt werden, sodass Sie flexibler auswählen können, welche Arten von Daten Sie überwachen möchten.

> [!TIP]
> Alle Automation-Konten, die für den Zugriff auf Daten aus einem Log Analytics-Arbeitsbereich verwendet werden, müssen im gleichen Mandanten wie der Arbeitsbereich erstellt werden.

Sie können einen Log Analytics-Arbeitsbereich im [Azure-Portal](../../azure-monitor/logs/quick-create-workspace.md), über die [Azure CLI](../../azure-monitor/logs/resource-manager-workspace.md) oder mit [Azure PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md) erstellen.

> [!IMPORTANT]
> Wenn alle Arbeitsbereiche in den Mandanten von Kunden erstellt werden, müssen die Microsoft.Insights-Ressourcenanbieter auch für ein Abonnement im Verwaltungsmandanten [registriert](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) werden. Wenn Ihr Verwaltungsmandant nicht über ein vorhandenes Azure-Abonnement verfügt, können Sie den Ressourcenanbieter manuell registrieren, indem Sie die folgenden PowerShell-Befehle verwenden:
>
> ```powershell
> $ManagingTenantId = "your-managing-Azure-AD-tenant-id"
> 
> # Authenticate as a user with admin rights on the managing tenant
> Connect-AzAccount -Tenant $ManagingTenantId
> 
> # Register the Microsoft.Insights resource providers Application Ids
> New-AzADServicePrincipal -ApplicationId 1215fb39-1d15-4c05-b2e3-d519ac3feab4
> New-AzADServicePrincipal -ApplicationId 6da94f3c-0d67-4092-a408-bb5d1cb08d2d 
> ```

## <a name="deploy-policies-that-log-data"></a>Bereitstellen von Richtlinien zum Protokollieren von Daten

Sobald Sie Ihre Log Analytics-Arbeitsbereiche erstellt haben können Sie [Azure Policy](../../governance/policy/index.yml) über alle Kundenhierarchien hinweg bereitstellen, sodass Diagnosedaten an den entsprechenden Arbeitsbereich in jedem Mandanten gesendet werden. Welche Richtlinien Sie genau bereitstellen, hängt von den Ressourcentypen ab, die Sie überwachen möchten.

Weitere Informationen zum Erstellen von Richtlinien finden Sie im [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md). Dieses [Communitytool](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) stellt ein Skript bereit, das Sie beim Erstellen von Richtlinien zum Überwachen der von Ihnen ausgewählten spezifischen Ressourcentypen unterstützt.

Wenn Sie festgelegt haben, welche Richtlinien Sie bereitstellen möchten, können Sie diese [in großem Stil für Ihre delegierten Abonnements bereitstellen](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analysieren der gesammelten Daten

Nachdem Sie Ihre Richtlinien bereitgestellt haben, werden Daten in die Log Analytics-Arbeitsbereiche protokolliert, die Sie in den jeweiligen Kundenmandanten erstellt haben. Um Einblicke in alle verwalteten Kunden zu erhalten, können Sie mithilfe von Tools wie [Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md) Informationen aus mehreren Datenquellen erfassen und analysieren.

## <a name="query-data-across-customer-workspaces"></a>Abfragen von Daten in mehreren Benutzerarbeitsbereichen

Sie können [Protokollabfragen](../../azure-monitor/logs/log-query-overview.md) ausführen, um Daten in Log Analytics-Arbeitsbereichen in verschiedenen Mandanten von Kunden abzurufen, indem Sie eine Union erstellen, die mehrere Arbeitsbereiche umfasst. Wenn Sie die TenantID-Spalte einschließen, sehen Sie, welche Ergebnisse zu welchen Mandanten gehören.

Mit der folgenden Beispielabfrage wird arbeitsbereichsübergreifend eine Union für die AzureDiagnostics-Tabelle in zwei separaten Mandanten von Kunden erstellt. In den Ergebnissen werden die Spalten „Category“, „ResourceGroup“ und „TenantID“ angezeigt.

``` Kusto
union AzureDiagnostics,
workspace("WS-customer-tenant-1").AzureDiagnostics,
workspace("WS-customer-tenant-2").AzureDiagnostics
| project Category, ResourceGroup, TenantId
```

Weitere Beispiele für Abfragen für mehrere Log Analytics-Arbeitsbereiche finden Sie unter [Ausführen arbeitsbereichs- und anwendungsübergreifender Protokollabfragen in Azure Monitor](../../azure-monitor/logs/cross-workspace-query.md).

> [!IMPORTANT]
> Wenn Sie ein Automationskonto verwenden, das zum Abfragen von Daten aus einem Log Analytics-Arbeitsbereich genutzt wird, muss dieses Automationskonto im gleichen Mandanten erstellt werden wie der Arbeitsbereich.

## <a name="view-alerts-across-customers"></a>Anzeigen von Warnungen für mehrere Kunden

Sie können [Warnungen](../../azure-monitor/alerts/alerts-overview.md) für die delegierten Abonnements in den von Ihnen verwalteten Kundenmandanten anzeigen.

Von Ihrem verwaltenden Mandanten aus können Sie [Aktivitätsprotokollwarnungen im Azure-Portal oder über APIs und Verwaltungstools erstellen, anzeigen und verwalten](../../azure-monitor/alerts/alerts-activity-log.md).

Filtern Sie die Warnungen mit einer [Azure Resource Graph](../../governance/resource-graph/overview.md)-Abfrage, um die Warnungen für mehrere Kunden zu aktualisieren. Sie können die Abfrage an Ihr Dashboard anheften und alle zugehörigen Kunden und Abonnements auswählen. Mit der folgenden Abfrage werden z. B. Warnungen mit den Schweregraden 0 und 1 angezeigt, die alle 60 Minuten aktualisiert werden.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Nächste Schritte

- Testen Sie die [Arbeitsmappe mit Aktivitätsprotokollen nach Domäne](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) auf GitHub.
- Erkunden Sie diese [von MVP erstellte Beispielarbeitsmappe](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), die die Berichterstellung für die Patchkompatibilität nachverfolgt, indem über mehrere Log Analytics-Arbeitsbereiche hinweg [Updateverwaltungsprotokolle abgefragt werden](../../automation/update-management/query-logs.md).
- Machen Sie sich mit weiteren [mandantenübergreifenden Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md) vertraut.