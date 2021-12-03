---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Verwaltung nach der Migration'
description: Nachdem die Migration erfolgreich abgeschlossen wurde, müssen in der nächsten Phase die neuen cloudbasierten Datenworkloadressourcen verwaltet werden.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5c7ef5f12cf4ec1f6776abbc405cf5145c27c0f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297447"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-post-migration-management"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Verwaltung nach der Migration

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Datenmigration mit MySQL Workbench](09-data-migration-with-mysql-workbench.md)

## <a name="monitoring-and-alerts"></a>Überwachung und Warnungen

Nachdem die Migration erfolgreich abgeschlossen wurde, müssen in der nächsten Phase die neuen cloudbasierten Datenworkloadressourcen verwaltet werden. Verwaltungsvorgänge umfassen sowohl Aktivitäten auf Steuerungsebene als auch auf Datenebene. Aktivitäten auf Steuerungsebene sind Aktivitäten, die mit den Azure-Ressourcen oder mit der Datenebene **innerhalb** der Azure-Ressourcen (in diesem Fall MySQL) in Verbindung stehen.

Azure Database für MySQL bietet die Möglichkeit, diese beiden Arten von Aktivitäten mithilfe von Azure-basierten Tools wie [Azure Monitor](../../../azure-monitor/overview.md), [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) und [Microsoft Sentinel](../../../sentinel/overview.md) zu überwachen. Zusätzlich zu den Azure-basierten Tools können SIEM-Systeme (Security Information and Event Management) ebenfalls für die Nutzung dieser Protokolle konfiguriert werden.

Unabhängig davon, welches Tool Sie für die Überwachung der neuen cloudbasierten Workloads verwenden, müssen Warnungen erstellt werden, mit denen Azure- und Datenbankadministratoren vor verdächtigen Aktivitäten gewarnt werden. Wenn ein bestimmtes Warnereignis über einen klar definierten Korrekturpfad verfügt, können Warnungen automatisierte [Azure-Runbooks](../../../automation/learn/powershell-runbook-managed-identity.md) auslösen, um das Problem zu beheben.

Der erste Schritt in der Entwicklung einer vollständig überwachten Umgebung besteht darin, den Datenfluss von MySQL-Protokolldaten nach Azure Monitor zu ermöglichen. Unter [Konfigurieren von und Zugreifen auf Überwachungsprotokolle für Azure Database for MySQL im Azure-Portal](../../howto-configure-audit-logs-portal.md) finden Sie weitere Informationen.

Sobald Protokolldaten fließen, verwenden Sie die Abfragesprache [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), um die verschiedenen Protokollinformationen abzufragen. Administratoren, die mit KQL nicht vertraut sind, finden [hier](/azure/data-explorer/kusto/query/sqlcheatsheet) ein Cheat Sheet für SQL zu KQL oder weitere Informationen auf der Seite [Erste Schritte für Protokollabfragen in Azure Monitor](../../../azure-monitor/logs/get-started-queries.md).

So können Sie beispielsweise die Speicherauslastung von Azure Database for MySQL abfragen:

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "memory\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
So rufen Sie die CPU-Auslastung ab:

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "cpu\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
Nachdem Sie die KQL-Abfrage erstellt haben, erstellen Sie basierend auf diesen Abfragen [Protokollwarnungen](../../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="server-parameters"></a>Serverparameter

Im Rahmen der Migration wurden wahrscheinlich die lokalen [Serverparameter](../../concepts-server-parameters.md) geändert, um einen schnellen ausgehenden Datenverkehr zu unterstützen. Außerdem wurden Änderungen an den Parametern von Azure Database for MySQL vorgenommen, um einen schnellen eingehenden Datenverkehr zu unterstützen. Die Azure-Serverparameter sollten nach der Migration auf ihre ursprünglichen lokalen Werte zurückgesetzt werden, die für Ihre Workloads optimiert sind.

Achten Sie jedoch darauf, dass Sie die Serverparameter überprüfen und Änderungen vornehmen, die sowohl für die Workload als auch für die Umgebung geeignet sind. Einige Werte, die hervorragend für eine lokale Umgebung geeignet waren, sind für eine cloudbasierte Umgebung möglicherweise nicht optimal. Vergewissern Sie sich außerdem bei der Planung der Migration Ihrer aktuellen lokalen Parameter zu Azure, dass diese dort tatsächlich festgelegt werden können.

Einige Parameter dürfen in der Azure Database for MySQL nicht geändert werden.

## <a name="powershell-module"></a>PowerShell-Modul

Das Azure-Portal und Windows PowerShell können verwendet werden, um Azure Database for MySQL zu verwalten. Im Rahmen der ersten Schritte mit PowerShell müssen Sie zunächst die Azure PowerShell-Cmdlets für MySQL mithilfe des folgenden PowerShell-Befehls installieren:

`Install-Module -Name Az.MySql`

Sehen Sie sich nach der Installation der Module die folgenden Referenztutorials an, um zu erfahren, welche Vorteile Sie aus der Erstellung von Skripten Ihrer Verwaltungsaktivitäten ziehen können:

  - [Tutorial: Entwerfen einer Azure Database for MySQL-Instanz mithilfe von PowerShell](../../tutorial-design-database-using-powershell.md)

  - [Sichern und Wiederherstellen eines Azure Database for MySQL-Servers mithilfe von PowerShell](../../howto-restore-server-powershell.md)

  - [Konfigurieren von Serverparametern in Azure Database for MySQL mit PowerShell](../../howto-configure-server-parameters-using-powershell.md)

  - [Automatische Speichervergrößerung für einen Azure Database for MySQL-Server mithilfe von PowerShell](../../howto-auto-grow-storage-powershell.md)

  - [Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL mithilfe von PowerShell](../../howto-read-replicas-powershell.md)

  - [Neustarten eines Azure Database for MySQL-Servers mithilfe von Azure PowerShell](../../howto-restart-server-powershell.md)

## <a name="azure-database-for-mysql-upgrade-process"></a>Upgradeprozess: Azure Database for MySQL

Da Azure Database for MySQL ein PaaS-Angebot ist, sind Administratoren nicht für die Verwaltung von Updates auf dem Betriebssystem oder der MySQL-Software verantwortlich. Es sollte jedoch beachtet werden, dass der Upgradeprozess beliebig erfolgen kann, und dass MySQL-Serverworkloads im Rahmen der Upgradebereitstellung gestoppt werden können. Sorgen Sie für diese Ausfallzeiten vor, indem Sie die Workloads an ein Lesereplikat umleiten, falls eine bestimmte Instanz in den Wartungsmodus wechselt.

> [!NOTE]
> Für diese Art der Failoverarchitektur müssen möglicherweise Änderungen an der Anwendungsdatenebene vorgenommen werden, damit diese Art von Failoverszenario unterstützt wird. Wenn das Lesereplikat als Lesereplikat beibehalten und nicht höher gestuft wird, kann die Anwendung die Daten nur lesen. Wenn ein Vorgang dann versucht, Informationen in die Datenbank zu schreiben, kann u. U. ein Fehler auftreten.

Das Benachrichtigungsfeature [Geplante Wartung](../../concepts-monitoring.md#planned-maintenance-notification) informiert Ressourcenbesitzer bis zu 72 Stunden vor der Installation eines Updates oder kritischen Sicherheitspatches. Datenbankadministratoren müssen die Anwendungsbenutzer möglicherweise über geplante und ungeplante Wartung benachrichtigen.

> [!NOTE]
> Die Wartungsbenachrichtigungen von Azure Database for MySQL sind von großer Bedeutung. Durch die Wartung können die Datenbank und die damit verbundenen Anwendungen für einen bestimmten Zeitraum ausfallen.

## <a name="wwi-scenario"></a>WWI-Szenario

WWI verwendet die Azure-Aktivitätsprotokolle, um den Datenfluss der MySQL-Protokollierung an einen [Log Analytics-Arbeitsbereich](../../../azure-monitor/logs/design-logs-deployment.md) zu ermöglichen. Dieser Arbeitsbereich ist so konfiguriert, dass er Teil von [Microsoft Sentinel](../../../sentinel/index.yml) ist, damit sämtliche [Threat Analytics](../../concepts-security.md#threat-protection)-Ereignisse angezeigt und Incidents erstellt werden.

Die MySQL-DBAs haben die [Azure PowerShell-Cmdlets](../../quickstart-create-mysql-server-database-using-azure-powershell.md) für Azure Database for MySQL installiert, um die Verwaltung des MySQL-Servers zu automatisieren. Auf diese Weise ist es nicht notwendig, sich jedes Mal erneut im Azure-Portal anzumelden.

## <a name="management-checklist"></a>Checkliste für die Verwaltung

  - Erstellen Sie Ressourcenwarnungen für allgemeine Dinge wie CPU und Arbeitsspeicher.

  - Stellen Sie sicher, dass die Serverparameter nach der Migration für die entsprechende Zieldatenworkload konfiguriert sind.

  - Erstellen Sie Skripts für häufige administrative Aufgaben.

  - Richten Sie Benachrichtigungen zu Wartungsereignissen wie Upgrades und Patches ein. Benachrichtigen Sie die Benutzer nach Bedarf.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Optimierung](./11-optimization.md)
