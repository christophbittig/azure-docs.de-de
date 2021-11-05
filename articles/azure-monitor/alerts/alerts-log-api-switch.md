---
title: Upgrade auf die aktuelle Azure Monitor-Protokollwarnungs-API
description: Hier erfahren Sie, wie Sie zur Protokollwarnungs-API „ScheduledQueryRules“ wechseln.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f3d55bfe93ec3bcaa713e77db6326488851994d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026220"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Upgrade von der Log Analytics-Legacywarnungs-API auf die aktuelle Protokollwarnungs-API

> [!NOTE]
> Dieser Artikel ist nur für die öffentlichen Azure-Regionen relevant (**nicht** für Azure Government oder die Azure China-Cloud).

> [!NOTE]
> Sobald sich ein Benutzer dazu entscheidet, zur aktuellen [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules) zu wechseln, ist keine Rückkehr zur älteren [Legacywarnungs-API von Log Analytics](./api-alerts.md) mehr möglich.

In der Vergangenheit haben Benutzer die [Log Analytics-Legacywarnungs-API](./api-alerts.md) verwendet, um Protokollwarnungsregeln zu verwalten. Aktuelle Arbeitsbereiche verwenden die [ScheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules). In diesem Artikel werden die Vorteile und der Wechsel von der Legacy-API zur aktuellen API beschrieben.

## <a name="benefits"></a>Vorteile

- einzelne Vorlage für die Erstellung von Warnungsregeln (zuvor drei separate Vorlagen erforderlich)
- Einzelne API für alle Protokollwarnungen für Azure-Ressourcen
- Unterstützung für zustandsbasierte und 1-minütige Protokollwarnungsvorschau
- [Unterstützung von PowerShell-Cmdlets](./alerts-log.md#managing-log-alerts-using-powershell)
- Ausrichtung von Schweregraden auf alle anderen Warnungstypen
- Möglichkeit zum Erstellen von [arbeitsbereichsübergreifenden Warnungsregeln](../logs/cross-workspace-query.md), die mehrere externe Ressourcen wie Log Analytics-Arbeitsbereiche oder Application Insights-Ressourcen umfassen
- Benutzer können Dimensionen angeben, um die Warnungen zu teilen.
- verlängerte Zeiträume von bis zu zwei Tagen bei Protokollwarnungen für Daten (zuvor auf einen Tag beschränkt)

## <a name="impact"></a>Auswirkung

- Alle neuen Regeln müssen mit der aktuellen API erstellt bzw. bearbeitet werden. Weitere Informationen finden Sie unter [Beispiel für die Verwendung über eine Azure-Ressourcenvorlage](alerts-log-create-templates.md) und [Verwalten von Protokollwarnungen mit PowerShell](./alerts-log.md#managing-log-alerts-using-powershell).
- Da Regeln zu von Azure Resource Manager überwachten Ressourcen in der aktuellen API werden und eindeutig sein müssen, wird die Ressourcen-ID der Regeln in diese Struktur geändert: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Die Anzeigenamen der Warnungsregel bleiben unverändert.

## <a name="process"></a>Prozess

Der Prozess des Wechsels ist nicht interaktiv und erfordert in den meisten Fällen keine manuellen Schritte. Die Warnungsregeln werden während oder nach dem Wechsel nicht beendet oder angehalten.
Führen Sie diesen Befehl aus, um alle Warnungsregeln zu ändern, die dem jeweiligen Log Analytics-Arbeitsbereich zugeordnet sind:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Mit Anforderungstext, der den folgenden JSON-Code enthält:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Dies ist ein Beispiel der Verwendung des Open-Source-Befehlszeilentools [ARMClient](https://github.com/projectkudu/ARMClient), das das Aufrufen des obigen API-Aufrufs vereinfacht:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": true}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Wenn der Wechsel erfolgreich ist, lautet die Antwort wie folgt:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Überprüfen des Wechselstatus des Arbeitsbereichs

Sie können auch diesen API-Aufruf verwenden, um den Wechselstatus zu überprüfen:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Sie können auch das [ARMClient](https://github.com/projectkudu/ARMClient)-Tool verwenden:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Wenn vom Log Analytics-Arbeitsbereich zur [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules) gewechselt wurde, lautet die Antwort wie folgt:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Wenn der Log Analytics-Arbeitsbereich nicht geändert wurde, lautet die Antwort wie folgt:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Monitor: Protokollwarnungen](./alerts-unified-log.md).
- Informationen zum [Verwalten Ihrer Protokollwarnungen mithilfe der API](alerts-log-create-templates.md)
- Informationen zum [Verwalten von Protokollwarnungen mit PowerShell](./alerts-log.md#managing-log-alerts-using-powershell)
- Erfahren Sie mehr über die neue [Azure Alerts-Benutzeroberfläche](./alerts-overview.md).
