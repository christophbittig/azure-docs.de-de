---
title: Archivieren und Erstellen von Berichten mit Azure Monitor – Azure AD-Berechtigungsverwaltung
description: Erfahren Sie, wie Sie in der Azure Active Directory-Berechtigungsverwaltung mit Azure Monitor Protokolle archivieren und Berichte erstellen können.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 5/19/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4970ef21be1e5c440acc871f5ca5583da1916f52
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728506"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archivieren von Protokollen und Erstellen von Berichten mit Azure Monitor in der Azure AD-Berechtigungsverwaltung

Azure AD speichert Überwachungsereignisse im Überwachungsprotokoll für maximal 30 Tage. Sie können die Überwachungsdaten jedoch über den in [Wie lange speichert Azure AD die Berichtsdaten?](../reports-monitoring/reference-reports-data-retention.md) beschriebenen Standardaufbewahrungszeitraum hinaus aufbewahren, indem Sie diese an ein Azure Storage-Konto weiterleiten oder Azure Monitor verwenden. Anschließend können Sie Arbeitsmappen und benutzerdefinierte Abfragen und Berichte für diese Daten verwenden.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurieren von Azure AD für die Verwendung von Azure Monitor
Bevor Sie Azure Monitor-Arbeitsmappen verwenden, müssen Sie Azure AD so konfigurieren, dass eine Kopie der Überwachungsprotokolle an Azure Monitor gesendet wird.

Zum Archivieren von Azure AD-Überwachungsprotokollen müssen Sie über Azure Monitor in einem Azure-Abonnement verfügen. Weitere Informationen zu Voraussetzungen und geschätzten Kosten für die Verwendung von Azure Monitor finden Sie unter [Azure AD-Aktivitätsprotokolle in Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Erforderliche Rolle:** Globaler Administrator

1. Melden Sie sich im Azure-Portal als Benutzer mit globalen Administratorrechten an. Stellen Sie sicher, dass Sie Zugriff auf die Ressourcengruppe haben, die den Azure Monitor-Arbeitsbereich enthält.
 
1. Wählen Sie **Azure Active Directory** aus, und klicken Sie dann im linken Navigationsmenü unter „Überwachung“ auf **Diagnoseeinstellungen**. Überprüfen Sie, ob bereits eine Einstellung zum Senden der Überwachungsprotokolle an diesen Arbeitsbereich vorhanden ist.

1. Wenn noch keine Einstellung vorhanden ist, klicken Sie auf **Diagnoseeinstellung hinzufügen**. Befolgen Sie die Anweisungen im Artikel [Integrieren von Azure AD-Protokollen mit Azure Monitor-Protokollen](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor), um das Azure AD-Überwachungsprotokoll an den Azure Monitor-Arbeitsbereich zu senden.

    ![Bereich „Diagnoseeinstellungen“](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Nachdem das Protokoll an Azure Monitor gesendet wurde, wählen Sie **Log Analytics-Arbeitsbereiche** und dann den Arbeitsbereich aus, der die Azure AD-Überwachungsprotokolle enthält.

1. Wählen Sie **Nutzung und geschätzte Kosten** aus, und klicken Sie auf **Datenaufbewahrung**. Stellen Sie den Schieberegler auf die Anzahl der Tage ein, für die Daten zum Erfüllen Ihrer Überwachungsanforderungen aufbewahrt werden sollen.

    ![Fenster „Log Analytics-Arbeitsbereiche“](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Wenn Sie später den Bereich der Datumsangaben in Ihrem Arbeitsbereich anzeigen möchten, können Sie die Arbeitsmappe *Datumsbereich für archivierte Protokolle* verwenden:  
    
    1. Wählen Sie **Azure Active Directory** aus, und klicken Sie auf **Arbeitsmappen**. 
    
    1. Erweitern Sie den Abschnitt **Problembehandlung für Azure Active Directory**, und klicken Sie auf **Datumsbereich für archivierte Protokolle**. 


## <a name="view-events-for-an-access-package"></a>Anzeigen von Ereignissen für ein Zugriffspaket  

Um Ereignisse für ein Zugriffspaket anzuzeigen, benötigen Sie Zugriff auf den zugrunde liegenden Azure Monitor-Arbeitsbereich (siehe [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)) und eine der folgenden Rollen: 

- Globaler Administrator  
- Sicherheitsadministrator  
- Sicherheitsleseberechtigter  
- Berichtsleseberechtigter  
- Anwendungsadministrator  

Gehen Sie folgendermaßen vor, um Ereignisse anzuzeigen: 

1. Wählen Sie im Azure-Portal **Azure Active Directory** aus, und klicken Sie auf **Arbeitsmappen**. Wenn Sie nur über ein einziges Abonnement verfügen, fahren Sie mit Schritt 3 fort. 

1. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das den Arbeitsbereich enthält.  

1. Wählen Sie die Arbeitsmappe *Zugriffspaketaktivität* aus. 

1. Wählen Sie in dieser Arbeitsmappe einen Zeitbereich aus (wenn Sie nicht sicher sind, ändern Sie die Einstellung in **Alle**), und wählen Sie in der Dropdownliste aller Zugriffspakete mit Aktivitäten für diesen eine Zugriffspaket-ID aus. Es werden die Ereignisse im Zusammenhang mit dem Zugriffspaket angezeigt, die während des ausgewählten Zeitbereichs auftraten.

    ![Anzeigen von Zugriffspaketereignissen](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Jede Zeile enthält die Uhrzeit, die Zugriffspaket-ID, den Namen des Vorgangs, die Objekt-ID, den UPN und den Anzeigenamen des Benutzers, der den Vorgang gestartet hat.  Weitere Details sind im JSON-Code enthalten.

1. Wenn Sie überprüfen möchten, ob Änderungen an Anwendungsrollenzuweisungen für eine Anwendung vorgenommen wurden, die nicht auf Zugriffspaketzuweisungen zurückzuführen sind (wenn z. B. ein globaler Administrator einen Benutzer direkt einer Anwendungsrolle zugewiesen hat), können Sie die Arbeitsmappe mit dem Namen *Aktivität der Anwendungsrollenzuweisung* auswählen.

    ![Anzeige von App-Rollenzuweisungen](./media/entitlement-management-access-package-incompatible/workbook-ara.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Erstellen von benutzerdefinierten Azure Monitor-Abfragen mit dem Azure-Portal
Sie können eigene Abfragen für Azure AD-Überwachungsereignisse erstellen. Dazu zählen auch Ereignisse der Berechtigungsverwaltung.  

1. Klicken Sie im Azure-Portal unter Azure Active Directory im Abschnitt „Überwachung“ im linken Navigationsmenü auf **Protokolle**, um eine neue Abfrageseite zu erstellen.

1. Der Arbeitsbereich sollte oben links auf der Abfrageseite angezeigt werden. Wenn Sie mehrere Azure Monitor-Arbeitsbereiche haben und der Arbeitsbereich, den Sie zum Speichern von Azure AD-Überwachungsereignissen verwenden, nicht angezeigt wird, klicken Sie auf **Bereich auswählen**. Wählen Sie dann das richtige Abonnement und den entsprechenden Arbeitsbereich aus.

1. Löschen Sie als Nächstes im Textbereich der Abfrage die Zeichenfolge „Suchen *“, und ersetzen Sie sie durch die folgende Abfrage:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klicken Sie dann auf **Ausführen**. 

    ![Klicken auf „Ausführen“ zum Starten der Abfrage](./media/entitlement-management-logs-and-reporting/run-query.png)

In der Tabelle werden standardmäßig die Überwachungsprotokollereignisse der letzten Stunde für die Berechtigungsverwaltung angezeigt. Sie können die Einstellung „Zeitbereich“ ändern, um ältere Ereignisse anzuzeigen. Wenn Sie diese Einstellung ändern, werden jedoch nur Ereignisse angezeigt, die nach der Konfiguration von Azure AD für das Senden von Ereignissen an Azure Monitor aufgetreten sind.

Wenn Sie die ältesten und neuesten Überwachungsereignisse in Azure Monitor anzeigen möchten, verwenden Sie die folgende Abfrage:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Weitere Informationen zu den Spalten, die für Überwachungsereignisse in Azure Monitor gespeichert werden, finden Sie unter [Interpretieren des Azure AD-Überwachungsprotokollschemas in Azure Monitor](../reports-monitoring/overview-reports.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Erstellen von benutzerdefinierten Azure Monitor-Abfragen mit Azure PowerShell

Nachdem Sie Azure AD zum Senden von Protokollen an Azure Monitor konfiguriert haben, können Sie über PowerShell auf die Protokolle zugreifen. Sie können dann über Skripts oder die PowerShell-Befehlszeile Abfragen senden, ohne ein globaler Administrator im Mandanten sein zu müssen. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Sicherstellen der richtigen Rollenzuweisung für Benutzer oder Dienstprinzipal

Stellen Sie sicher, dass dem Benutzer oder Dienstprinzipal, der sich bei Azure AD authentifiziert, im Log Analytics-Arbeitsbereich die entsprechende Azure-Rolle zugewiesen ist. „Log Analytics-Leser“ oder „Log Analytics-Mitwirkender“ sind als Rollenoptionen verfügbar. Wenn Ihnen bereits eine dieser Rollen zugewiesen wurde, fahren Sie mit [Abrufen der Log Analytics-ID mit einem Azure-Abonnement](#retrieve-log-analytics-id-with-one-azure-subscription) fort.

Führen Sie die folgenden Schritte aus, um die Rollenzuweisung festzulegen und eine Abfrage zu erstellen:

1. Suchen Sie im Azure-Portal nach dem [Log Analytics-Arbeitsbereich](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Wählen Sie **Access Control (IAM)** aus.

1. Klicken Sie dann auf **Hinzufügen**, um eine Rollenzuweisung hinzuzufügen.

    ![Hinzufügen einer Rollenzuweisung](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installieren des Azure PowerShell-Moduls

Wenn Sie über die richtige Rollenzuweisung verfügen, starten Sie PowerShell, und [installieren Sie das Azure PowerShell-Modul](/powershell/azure/install-az-ps) (falls noch nicht geschehen). Geben Sie dazu Folgendes ein:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nun können Sie sich bei Azure AD authentifizieren und die ID des Log Analytics-Arbeitsbereichs abrufen, den Sie abfragen möchten.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Abrufen der Log Analytics-ID mit einem Azure-Abonnement
Wenn Sie ein einzelnes Azure-Abonnement und einen einzelnen Log Analytics-Arbeitsbereich haben, geben Sie Folgendes ein, um sich bei Azure AD zu authentifizieren, eine Verbindung mit dem jeweiligen Abonnement herzustellen und den Arbeitsbereich abzurufen:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Abrufen der Log Analytics-ID mit mehreren Azure-Abonnements

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) funktioniert jeweils nur in einem Abonnement. Wenn Sie also mehrere Azure-Abonnements haben, müssen Sie sicherstellen, dass Sie eine Verbindung mit dem Abonnement herstellen, zu dem der Log Analytics-Arbeitsbereich mit den Azure AD-Protokollen gehört. 
 
 Mit den folgenden Cmdlets können Sie eine Liste der Abonnements anzeigen und nach der ID des Abonnements mit dem Log Analytics-Arbeitsbereich suchen:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Sie können sich erneut authentifizieren und Ihre PowerShell-Sitzung mit einem Befehl (z. B. `Connect-AzAccount –Subscription $subs[0].id`) mit diesem Abonnement verknüpfen. Weitere Informationen zum Authentifizieren bei Azure über die PowerShell (einschließlich der nicht interaktiven Authentifizierung) finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

Wenn das Abonnement mehrere Log Analytics-Arbeitsbereiche enthält, können Sie mit dem Cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) eine Liste der Arbeitsbereiche zurückgeben. Dann können Sie nach dem Arbeitsbereich mit den Azure AD-Protokollen suchen. Das von diesem Cmdlet zurückgegebene Feld `CustomerId` ist identisch mit der Arbeitsbereichs-ID, die im Azure-Portal in der Übersicht der Log Analytics-Arbeitsbereiche angezeigt wird.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Senden der Abfrage an den Log Analytics-Arbeitsbereich
Abschließend können Sie, wenn Sie einen Arbeitsbereich identifiziert haben, mit dem Befehl [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) eine Kusto-Abfrage an diesen Arbeitsbereich senden. Diese Abfragen werden in der [Kusto-Abfragesprache](/azure/kusto/query/) geschrieben.
 
Sie können beispielsweise mit PowerShell-Cmdlets den Datenbereich der aufgezeichneten Überwachungsereignisse aus dem Log Analytics-Arbeitsbereich abrufen. Dazu senden Sie eine Abfrage ähnlich der Folgenden:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Mit einer Abfrage ähnlich der Folgenden können Sie auch Ereignisse der Berechtigungsverwaltung abrufen:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Nächste Schritte:
- [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md)