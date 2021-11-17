---
title: Integrieren von Azure Automation State Configuration in Azure Monitor-Protokolle
description: Dieser Artikel beschreibt, wie Desired State Configuration-Berichtsdaten von Azure Automation State Configuration an Azure Monitor-Protokolle gesendet werden.
services: automation
ms.subservice: dsc
ms.date: 08/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51404418080176792fc7dfce6cbc6b144dcdde41
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494190"
---
# <a name="integrate-azure-automation-state-configuration-with-azure-monitor-logs"></a>Integrieren von Azure Automation State Configuration in Azure Monitor-Protokolle

Azure Automation State Configuration behält den Knotenstatus 30 Tage lang bei. Sie können Knotenstatusdaten an [Azure Monitor-Protokolle](../azure-monitor/logs/data-platform-logs.md) senden, wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten. Der Konformitätsstatus kann im Azure-Portal oder mit PowerShell angezeigt werden, und zwar für Knoten und einzelne DSC-Ressourcen in Knotenkonfigurationen.

Azure Monitor-Protokolle bietet eine höhere operative Transparenz für Ihre Automation State Configuration-Daten, sodass schneller auf Incidents reagiert werden kann. Mit Azure Monitor-Protokolle können Sie:

- Complianceinformationen für verwaltete Knoten und einzelne Ressourcen abrufen
- Eine E-Mail oder Warnung basierend auf dem Compliancestatus auslösen
- Erweiterte Abfragen für Ihre verwalteten Knoten schreiben
- Den Compliancestatus über Automation-Konten korrelieren
- Benutzerdefinierte Ansichten und Suchabfragen zum Visualisieren von Runbookergebnissen, Runbookauftragsstatus und anderer wichtiger Schlüsselindikatoren oder Metriken verwenden

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Senden von Automation State Configuration-Berichten an Azure Monitor-Protokolle benötigen Sie Folgendes:

* Das PowerShell-[Az-Modul](/powershell/azure/new-azureps-module-az) ist installiert. Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.
- Ein Azure Automation-Konto. Weitere Informationen finden Sie unter [Einführung in Azure Automation](automation-intro.md).
- Einen Log Analytics-Arbeitsbereich Weitere Informationen finden Sie unter [Azure Monitor-Protokolle: Übersicht](../azure-monitor/logs/data-platform-logs.md).
- Mindestens einen Azure Automation DSC-Knoten. Weitere Informationen finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
- Modul [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0), Version 2.7.0.0 oder höher. Installationsschritte finden Sie unter [Problembehandlung der Desired State Configuration in Azure Automation](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Einrichten der Integration in Azure Monitor-Protokolle

Führen Sie zum Importieren von Daten aus Azure Automation State Configuration in Azure Monitor-Protokolle die folgenden Schritte aus. Informationen zu den Schritten unter Verwendung des Portals finden Sie unter [Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle](./automation-manage-send-joblogs-log-analytics.md).

1. Melden Sie sich von Ihrem Computer aus mit dem PowerShell-Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Geben Sie einen geeigneten Wert für die Variable `automationAccount` mit dem tatsächlichen Namen Ihres Automation-Kontos und für `workspaceName` mit dem tatsächlichen Namen Ihres Log Analytics-Arbeitsbereichs an. Führen Sie dann das Skript aus.

    ```powershell
    $automationAccount = "automationAccount"
    $law = "workspaceName"
    ```

1. Rufen Sie die Ressourcen-ID Ihres Automation-Kontos mit den folgenden PowerShell-Befehlen ab.

   ```powershell
   # Find the ResourceId for the Automation account
   $AutomationResourceId = (Get-AzResource `
      -ResourceType 'Microsoft.Automation/automationAccounts' | 
      WHERE {$_.Name -eq $automationAccount}).ResourceId
   ```

1. Rufen Sie die Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs mit den folgenden PowerShell-Befehlen ab.

   ```powershell
    # Find the ResourceId for the Log Analytics workspace
    $WorkspaceResourceId = (Get-AzResource `
        -ResourceType 'Microsoft.OperationalInsights/workspaces' | 
        WHERE {$_.Name -eq $law}).ResourceId
   ```

1. Zum Konfigurieren von Diagnoseeinstellungen für das Automation-Konto zum Weiterleiten von DSC-Knotenstatus-Protokolldaten an Azure Monitor-Protokolle erstellt das folgende PowerShell-Cmdlet eine Diagnoseeinstellung mit diesem Ziel.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $true `
        -Category 'DscNodeStatus'
   ```

   Wenn Sie die Weiterleitung von Protokolldaten von Azure Automation State Configuration an Azure Monitor-Protokolle beenden möchten, führen Sie das folgende PowerShell-Cmdlet aus.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $false `
        -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Anzeigen der DSC-Protokolle

Sie können die State Configuration-Protokolle nach DSC-Vorgängen durchsuchen, indem Sie die Azure Monitor Protokolle durchsuchen. Nachdem Sie die Integration in den Azure Monitor-Protokollen für Ihre Automation State Configuration-Daten eingerichtet haben, navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Automation-Konto. Wählen Sie dann unter **Überwachung** die Option **Protokolle** aus.

![Protokolle](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Schließen Sie das Dialogfeld **Abfragen**. Der Bereich „Protokollsuche“ wird mit einem Abfragebereich geöffnet, der auf Ihre Automation-Kontoressource bezogen ist. Die Datensätze für DSC-Vorgänge werden in der Tabelle `AzureDiagnostics` gespeichert. Um nicht kompatible Knoten zu suchen, geben Sie die folgende Abfrage ein.

```Kusto
AzureDiagnostics
| where Category == "DscNodeStatus"
| where OperationName contains "DSCNodeStatusData"
| where ResultType != "Compliant"
```

Filterdetails:

- Filtern Sie nach `DscNodeStatusData`, um für jeden State Configuration-Knoten Vorgänge zurückzugeben.
- Filtern Sie nach `DscResourceStatusData`, um für jede DSC-Ressource Vorgänge zurückzugeben, die in der auf diese Ressource angewandten Knotenkonfiguration aufgerufen wurden.
- Filtern Sie nach `DscResourceStatusData`, um Fehlerinformationen zu fehlerhaften DSC-Ressourcen zu erhalten.

Weitere Informationen zum Erstellen von Protokollabfragen, um Daten zu finden, finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Senden einer E-Mail bei einer fehlgeschlagenen DSC-Konformitätsprüfung

1. Kehren Sie zu Ihrer zuvor erstellten Abfrage zurück.

1. Klicken Sie auf die Schaltfläche „+ Neue Warnungsregel“, um mit der Warnungserstellung zu beginnen.

1. Ersetzen Sie in der folgenden Abfrage `NODENAME` durch den tatsächlichen Namen des verwalteten Knotens, und fügen Sie dann die überarbeitete Abfrage in das Textfeld **Suchabfrage** ein:

    ```kusto
    AzureDiagnostics
    | where Category == "DscNodeStatus"
    | where NodeName_s == "NODENAME"
    | where OperationName == "DscNodeStatusData"
    | where ResultType == "Failed"
    ```

   Wenn Sie Protokolle von mehreren Automation-Konten oder Abonnements in Ihrem Arbeitsbereich eingerichtet haben, können Sie Ihre Warnungen nach Abonnement oder Automation-Konto gruppieren. Leiten Sie den Namen des Automation-Kontos von der `Resource`-Eigenschaft in den Protokollsuchergebnissen der `DscNodeStatusData` ab.

1. Lesen Sie [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/alerts/alerts-metric.md), um die verbleibenden Schritte durchzuführen.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Suchen von Fehlern bei DSC-Ressourcen in allen Knoten

Ein Vorteil der Verwendung von Azure Monitor-Protokolle ist, dass Sie nach Fehlern bei der Überprüfung der Knoten suchen können. Um alle Instanzen von DSC-Ressourcen mit Fehlern aufzufinden, verwenden Sie die folgende Abfrage:

```kusto
AzureDiagnostics 
| where Category == "DscNodeStatus"
| where OperationName == "DscResourceStatusData"
| where ResultType == "Failed"
```

### <a name="view-historical-dsc-node-status"></a>Anzeigen von Verlaufsdaten zum DSC-Knotenstatus

Mit dieser Abfrage können Sie den Statusverlauf Ihres DSC-Knotens visualisieren:

```kusto
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.AUTOMATION" 
| where Category == "DscNodeStatus"
| where ResultType != "started"
| summarize count() by ResultType
``````

Mit dieser Abfrage wird ein Diagramm mit dem Knotenstatus über einen Zeitraum angezeigt.

## <a name="azure-monitor-logs-records"></a>Datensätze in Azure Monitor-Protokollen

Die Diagnose von Azure Automation erstellt zwei Kategorien von Datensätzen in Azure Monitor-Protokollen:

* Daten zum Knotenstatus (`DscNodeStatusData`)
* Daten zum Ressourcenstatus (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| Vorgangsname |`DscNodeStatusData`. |
| ResultType |Ein Wert, der angibt, ob der Knoten konform ist. |
| NodeName_s |Der Name des verwalteten Knotens. |
| NodeComplianceStatus_s |Statuswert, der angibt, ob der Knoten konform ist. |
| DscReportStatus |Statuswert, der angibt, ob die Complianceüberprüfung erfolgreich ausgeführt wurde. |
| ConfigurationMode | Der Modus, der zum Anwenden der Konfiguration auf den Knoten verwendet wird. Mögliche Werte: <ul><li>`ApplyOnly`: DSC wendet die Konfiguration an und führt nur dann weitere Schritte durch, wenn eine neue Konfiguration per Push an den Zielknoten übertragen oder eine neue Konfiguration von einem Server abgerufen wird. Nach der ersten Anwendung einer neuen Konfiguration führt DSC keine Überprüfung auf Abweichungen von einem zuvor konfigurierten Zustand durch. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor der Wert `ApplyOnly` in Kraft tritt. </li><li>`ApplyAndMonitor`: Dies ist der Standardwert. Der LCM wendet alle neuen Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor der Wert `ApplyAndMonitor` in Kraft tritt.</li><li>`ApplyAndAutoCorrect`: DSC wendet neue Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht, und wendet dann die aktuelle Konfiguration erneut an.</li></ul> |
| HostName_s | Der Name des verwalteten Knotens. |
| IPAddress | Die IPv4-Adresse des verwalteten Knotens. |
| Category | `DscNodeStatus`. |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
| NodeId_g | Eindeutiger Bezeichner (GUID), der den verwalteten Knoten identifiziert. |
| DscReportId_g | Eindeutiger Bezeichner (GUID), der den Bericht identifiziert. |
| LastSeenTime_t | Datum und Uhrzeit der letzten Anzeige des Berichts. |
| ReportStartTime_t | Datum und Uhrzeit des Berichtsstarts. |
| ReportEndTime_t | Datum und Uhrzeit des Berichtsabschlusses. |
| NumberOfResources_d | Die Anzahl der DSC-Ressourcen, die bei der Anwendung der Konfiguration auf den Knoten abgerufen wurden. |
| SourceSystem | Das Quellsystem, das angibt, wie die Daten in Azure Monitor-Protokollen gesammelt wurden. Immer `Azure` für Azure-Diagnose. |
| resourceId |Der Ressourcenbezeichner des Azure Automation-Kontos. |
| ResultDescription | Die Ressourcenbeschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Ein eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Complianceberichts handelt. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| Vorgangsname |`DscResourceStatusData`.|
| ResultType |Gibt an, ob die Ressource konform ist. |
| NodeName_s |Der Name des verwalteten Knotens. |
| Category | DscNodeStatus. |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
| NodeId_g |Eindeutiger Bezeichner (GUID), der den verwalteten Knoten identifiziert. |
| DscReportId_g |Eindeutiger Bezeichner (GUID), der den Bericht identifiziert. |
| DscResourceId_s |Der Name der DSC-Ressourceninstanz. |
| DscResourceName_s |Der Name der DSC-Ressource. |
| DscResourceStatus_s |Gibt an, ob die DSC-Ressource konform ist. |
| DscModuleName_s |Der Name des PowerShell-Moduls, das die DSC-Ressource enthält. |
| DscModuleVersion_s |Die Version des PowerShell-Moduls, das die DSC-Ressource enthält. |
| DscConfigurationName_s |Der Name der Konfiguration, die auf den Knoten angewendet wird. |
| ErrorCode_s | Der Fehlercode, wenn die Ressource fehlerhaft ist. |
| ErrorMessage_s |Die Fehlermeldung, wenn die Ressource fehlerhaft ist. |
| DscResourceDuration_d |Die Zeit in Sekunden, für die die DSC-Ressource ausgeführt wurde. |
| SourceSystem | So erfassen Azure Monitor-Protokolle die Daten. Immer `Azure` für Azure-Diagnose. |
| resourceId |Der Bezeichner des Azure Automation-Kontos. |
| ResultDescription | Die Beschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Ein eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Complianceberichts handelt. |

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).
- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
- Weitere Informationen zum Erstellen verschiedener Suchabfragen und zur Überprüfung der Automation State Configuration-Protokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokollen](../azure-monitor/logs/log-query-overview.md).
- Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
