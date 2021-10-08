---
title: Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen
description: Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen (Azure Role-Based Access Control, auf Azure-Rollen basierte Zugriffssteuerung) für die letzten 90 Tage.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 43ee281bc9696740c7909ef6b6758d4542808721
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755223"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen

In einigen Fällen benötigen Sie Informationen zu Änderungen bei der auf Azure-Rollen basierten Zugriffssteuerung (Role-Based Access Control, Azure RBAC), beispielsweise für die Überwachung oder Problembehandlung. Jedes Mal, wenn ein Benutzer Änderungen an den Rollenzuweisungen oder Rollendefinitionen innerhalb Ihrer Abonnements vornimmt, werden die Änderungen im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md) aufgezeichnet. Sie können die Aktivitätsprotokolle öffnen, um alle Azure RBAC-Änderungen für die letzten 90 Tage anzuzeigen.

## <a name="operations-that-are-logged"></a>Protokollierte Vorgänge

Folgende Vorgänge im Bezug auf Azure RBAC werden im Aktivitätsprotokoll erfasst:

- Erstellen von Rollenzuweisungen
- Löschen von Rollenzuweisungen
- Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition
- Löschen von benutzerdefinierten Rollendefinition

## <a name="azure-portal"></a>Azure-Portal

Der einfachste Einstieg besteht im Anzeigen der Aktivitätsprotokolle im Azure-Portal. Der folgende Screenshot zeigt ein Beispiel für Rollenzuordnungsvorgänge im Aktivitätsprotokoll. Es enthält ebenfalls eine Option, um die Protokolle als CSV-Datei herunterzuladen.

![Screenshot: Aktivitätsprotokolle im Portal](./media/change-history-report/activity-log-portal.png)

Um weitere Informationen zu erhalten, klicken Sie auf einen Eintrag, um den Zusammenfassungsbereich zu öffnen. Klicken Sie auf die Registerkarte **JSON**, um ein detailliertes Protokoll zu erhalten.

![Aktivitätsprotokolle über das Portal mit geöffnetem Zusammenfassungsbereich – Screenshot](./media/change-history-report/activity-log-summary-portal.png)

Das Aktivitätsprotokoll im Portal verfügt über mehrere Filter. Im Folgenden die Filter für die auf Azure-Rollen basierte Zugriffssteuerung:

| Filtern | value |
| --------- | --------- |
| Ereigniskategorie | <ul><li>Administrative</li></ul> |
| Vorgang | <ul><li>Erstellen von Rollenzuweisungen</li><li>Löschen von Rollenzuweisungen</li><li>Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition</li><li>Löschen von benutzerdefinierten Rollendefinition</li></ul> |

Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md).


## <a name="interpret-a-log-entry"></a>Interpretieren eines Protokolleintrags

Die Protokollausgabe der Registerkarte „JSON“, von Azure PowerShell oder Azure CLI kann viele Informationen enthalten. Im Folgenden finden Sie einige der Schlüsseleigenschaften, nach denen Sie suchen können, wenn Sie versuchen, einen Protokolleintrag zu interpretieren. Möglichkeiten zum Filtern der Protokollausgabe mit Azure PowerShell oder Azure CLI finden Sie in den folgenden Abschnitten.

> [!div class="mx-tableFixed"]
> | Eigenschaft | Beispielwerte | BESCHREIBUNG |
> | --- | --- | --- |
> | authorization:action | Microsoft.Authorization/roleAssignments/write | Erstellen von Rollenzuweisungen |
> |  | Microsoft.Authorization/roleAssignments/delete | Löschen von Rollenzuweisungen |
> |  | Microsoft.Authorization/roleDefinitions/write | Erstellen oder Aktualisieren einer Rollendefinition |
> |  | Microsoft.Authorization/roleDefinitions/delete | Löschen einer Rollendefinition |
> | authorization:scope | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Bereich für die Aktion |
> | caller | admin@example.com<br/>{objectId} | Der Benutzer oder das System, von dem die Aktion initiiert wurde |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | Zeitpunkt der Aktion |
> | status:value | Gestartet<br/>Erfolgreich<br/>Fehler | Status der Aktion |

## <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie den Befehl [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog), um Aktivitätsprotokolle mit Azure PowerShell anzuzeigen.

Dieser Befehl führt alle Änderungen an der Rollenzuweisung in einem Abonnement für die letzten sieben Tage auf:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Dieser Befehl führt alle Änderungen an der Rollendefinition in einer Ressourcengruppe für die letzten sieben Tage auf:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtern der Protokollausgabe

Die Protokollausgabe kann viele Informationen enthalten. Dieser Befehl führt alle Änderungen an der Rollenzuweisung und der Rollendefinition in einem Abonnement für die letzten sieben Tage auf und filtert die Ausgabe:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Folgendes zeigt ein Beispiel für die gefilterte Protokollausgabe beim Erstellen einer Rollenzuweisung:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Wenn Sie einen Dienstprinzipal zum Erstellen von Rollenzuweisungen verwenden, ist der Wert der Caller-Eigenschaft eine Objekt-ID des Dienstprinzipals. Sie können mithilfe von [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) Informationen zum Dienstprinzipal abrufen.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Verwenden Sie den Befehl [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list), um Aktivitätsprotokolle mit der Azure CLI anzuzeigen.

Dieser Befehl listet die Aktivitätsprotokolle in einer Ressourcengruppe vom 1. März auf und blickt sieben Tage voraus:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Dieser Befehl listet die Aktivitätsprotokolle für den Autorisierungsressourcenanbieter vom 1. März auf und blickt sieben Tage voraus:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtern der Protokollausgabe

Die Protokollausgabe kann viele Informationen enthalten. Dieser Befehl führt alle Änderungen an der Rollenzuweisung und der Rollendefinition in einem Abonnement sieben Tage vorausblickend auf und filtert die Ausgabe:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Folgendes zeigt ein Beispiel für die gefilterte Protokollausgabe beim Erstellen einer Rollenzuweisung:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md) ist ein weiteres Tool, das Sie verwenden können, um Azure RBAC-Änderungen für alle Azure-Ressourcen zu erfassen und zu analysieren. Azure Monitor-Protokolle bietet die folgenden Vorteile:

- Schreiben komplexer Abfragen und Logiken
- Integration in Warnungen, Power BI und andere Tools
- Aufbewahren von Daten für längere Zeiträume
- Querverweise mit anderen Protokollen wie Sicherheitsprotokollen, Protokollen virtueller Computer und benutzerdefinierten Protokollen

Folgende Schritte sind für den Einstieg grundlegend:

1. [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/logs/quick-create-workspace.md)

1. [Konfigurieren der Lösung für die Aktivitätsprotokollanalyse](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) für Ihren Arbeitsbereich.

1. [Anzeigen der Aktivitätsprotokolle](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) Sie können schnell zur Übersichtsseite für die Lösung zur Aktivitätsprotokollanalyse navigieren, indem Sie auf die Option **Protokolle** klicken.

   ![Option „Azure Monitor-Protokolle“ im Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Verwenden Sie optional [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md), um die Protokolle abzufragen und anzuzeigen. Weitere Informationen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md).

Im Folgenden finden Sie eine Abfrage, die neue Rollenzuweisungen zurückgibt, die vom Anbieter der Zielressource geordnet wurden:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Im Folgenden finden Sie eine Abfrage, die Änderungen an der Rollenzuweisung zurückgibt, die in einem Diagramm angezeigt werden:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Screenshot: Aktivitätsprotokolle im Advanced Analytics-Portal](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nächste Schritte
* [Anzeigen von Aktivitätsprotokollen zur Überwachung von Aktionen in Ressourcen](../azure-monitor/essentials/activity-log.md)
* [Überwachen der Abonnementaktivität per Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md)
