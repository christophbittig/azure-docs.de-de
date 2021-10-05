---
title: Problembehandlung von SQL Insights (Vorschau)
description: Erfahren Sie, wie Sie Probleme mit SQL Insights in Azure Monitor behandeln.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: b76e08103b7af7591e7f71d3fe40e1e018a45665
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610653"
---
# <a name="troubleshoot-sql-insights-preview"></a>Problembehandlung von SQL Insights (Vorschau)
Überprüfen Sie den Status des Überwachungscomputers auf der Registerkarte **Profil verwalten**, um Probleme mit der Datensammlung in SQL Insights zu beheben. Die folgenden Status sind möglich:

- **Sammlung wird durchgeführt** 
- **Sammlung wird nicht durchgeführt** 
- **Sammlung wird durchgeführt (Fehler vorhanden)** 
 
Wählen Sie den Status aus, um Protokolle und weitere Details anzuzeigen, mit denen Sie das Problem beheben können. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Screenshot: Status eines Überwachungscomputers.":::

## <a name="status-not-collecting"></a>Status: Sammlung wird nicht durchgeführt 
Der Überwachungscomputer weist den Status **Sammlung wird nicht durchgeführt** auf, wenn innerhalb der letzten zehn Minuten keine Daten für SQL in *InsightsMetrics* erfasst wurden. 

> [!NOTE]
> Stellen Sie sicher, dass Sie versuchen, Daten von einer [unterstützten SQL-Version](sql-insights-overview.md#supported-versions) zu sammeln. Der Versuch, Daten mit einem gültigen Profil und einer gültigen Verbindungszeichenfolge aber über eine nicht unterstützte Version von Azure SQL-Datenbank zu sammeln, führt beispielsweise dazu, dass der Status **Sammlung wird nicht durchgeführt** ausgegeben wird.

SQL Insights verwendet zum Abrufen dieser Informationen die folgende Abfrage:

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Überprüfen Sie, ob Protokolle von Telegraf vorhanden sind, mit denen Sie die Grundursache des Problems ermitteln können. Wenn Protokolleinträge vorhanden sind, können Sie **Sammlung wird nicht durchgeführt** auswählen und die Protokolle und Informationen zur Problembehandlung auf gängige Probleme überprüfen. 

Wenn keine Protokolleinträge vorhanden sind, müssen Sie die Protokolle auf der Überwachungs-VM auf die folgenden Dienste überprüfen, die von den zwei VM-Erweiterungen installiert werden:

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - Dienst: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Dienst: wli 
  - Dienst: ms-telegraf 
  - Dienst: td-agent-bit-wli 
  - Erweiterungsprotokoll für die Überprüfung auf Installationsfehler: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>wli-Dienstprotokolle 

Dienstprotokolle: `/var/log/wli.log`

Aktuelle Protokolle anzeigen: `tail -n 100 -f /var/log/wli.log`
 
Wenn das folgende Fehlerprotokoll angezeigt wird, deutet das auf ein Problem mit dem Dienst mdsd hin.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Telegraf-Dienstprotokolle 

Dienstprotokolle: `/var/log/ms-telegraf/telegraf.log`

Aktuelle Protokolle anzeigen: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

So zeigen Sie die aktuellen Fehler- und Warnungsprotokolle an: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

Die von Telegraf verwendete Konfiguration wird vom wli-Dienst generiert und unter `/etc/ms-telegraf/telegraf.d/wli` gespeichert.
 
Wenn eine fehlerhafte Konfiguration generiert wird, wird der ms-telegraf-Dienst möglicherweise nicht gestartet. Überprüfen Sie mithilfe des folgenden Befehls, ob der ms-telegraf-Dienst ausgeführt wird: `service ms-telegraf status`

Führen Sie den Telegraf-Dienst manuell mit dem folgenden Befehl aus, um die Fehlermeldungen des Diensts anzuzeigen: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd-Dienstprotokolle 

Sehen Sie sich die [Voraussetzungen](../agents/azure-monitor-agent-install.md#prerequisites) für den Azure Monitor-Agent an. 


Dienstprotokolle:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Wiederkehrende Fehler anzeigen: `tail -n 100 -f /var/log/mdsd.err`

Tragen Sie die folgenden Informationen zusammen, wenn Sie den Support kontaktieren müssen: 

- Protokolle in `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Protokoll in `/var/log/waagent.log` 
- Protokolle in `/var/log/mdsd*`
- Dateien in `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Ungültige Konfiguration der Überwachungs-VM

Eine mögliche Ursache für den Status **Sammlung wird nicht durchgeführt** kann eine ungültige Konfiguration der Überwachungs-VM sein. Dies ist die Standardkonfiguration:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Mit dieser Konfiguration werden die Ersetzungstoken angegeben, die in der Profilkonfiguration auf der Überwachungs-VM verwendet werden sollen. Außerdem können Sie auf Geheimnisse aus Azure Key Vault verweisen, sodass die Konfiguration keine Geheimniswerte enthalten muss. Dieser Ansatz wird dringend empfohlen.

#### <a name="secrets"></a>Geheimnisse
Geheimnisse sind Token, deren Werte zur Laufzeit von einer Azure Key Vault-Instanz abgerufen werden. Ein Geheimnis setzt sich aus einem Key Vault-Verweis und einem Geheimnisnamen zusammen. Dadurch kann Azure Monitor den dynamischen Wert des Geheimnisses abrufen und diesen als Downstreamkonfigurationsverweis verwenden.

Sie können in der Konfiguration beliebig viele Geheimnisse definieren, einschließlich der in separaten Key Vault-Instanzen gespeicherten Geheimnisse.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Die Berechtigungen für den Zugriff auf die Key Vault-Instanz werden einer verwalteten Identität auf der Überwachungs-VM erteilt. Azure Monitor erwartet, dass die Key Vault-Instanz mindestens Geheimnisse für den Zugriff auf die VM bereitstellt. Sie können sie über das Azure-Portal, PowerShell, die Azure CLI oder eine Azure Resource Manager-Vorlage aktivieren.

#### <a name="parameters"></a>Parameter
Parameter sind Token, auf die in der Profilkonfiguration über JSON-Vorlagen verwiesen werden kann. Parameter haben einen Namen und einen Wert. Werte können beliebige JSON-Typen sein, einschließlich Objekten und Arrays. In der Profilkonfiguration wird auf einen Parameter verwiesen, indem sein Name im Format `.Parameters.<name>` angegeben wird.

Parameter können in der Key Vault-Instanz mit der gleichen Konvention auf Geheimnisse verweisen. `sqlAzureConnections` verweist beispielsweise mit der Konvention `$telegrafPassword` auf das Geheimnis `telegrafPassword`.

Zur Laufzeit werden alle Parameter und Geheimnisse aufgelöst und mit der Profilkonfiguration zusammengeführt, um die Konfiguration zu generieren, die auf dem Computer tatsächlich verwendet werden soll.

> [!NOTE]
> Die Parameternamen von `sqlAzureConnections`, `sqlVmConnections` und `sqlManagedInstanceConnections` werden alle in der Konfiguration benötigt, auch wenn für einige von ihnen keine Verbindungszeichenfolgen angegeben werden.


## <a name="status-collecting-with-errors"></a>Status: Sammlung wird durchgeführt (Fehler vorhanden)
Der Überwachungscomputer weist den Status **Sammlung wird durchgeführt (Fehler vorhanden)** auf, wenn mindestens ein *InsightsMetrics*-Protokoll vorhanden ist, aber Fehler in der Tabelle *Operation* vorliegen.

SQL Insights verwendet zum Abrufen dieser Informationen die folgenden Abfragen:

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> Wenn im `WorkloadDiagnosticLogs`-Datentyp keine Daten angezeigt werden, müssen Sie möglicherweise Ihr Überwachungsprofil aktualisieren, um diese Daten zu speichern.  Wählen Sie in der SQL Insights-UX **Profil verwalten** > **Profil bearbeiten** > **Überwachungsprofil aktualisieren** aus.

Für gängige Fälle werden Informationen zur Problembehandlung in der Protokollansicht zur Verfügung gestellt: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Protokollansicht für die Problembehandlung":::

## <a name="next-steps"></a>Nächste Schritte

- Im Artikel [Aktivieren von SQL Insights](sql-insights-enable.md) finden Sie weitere Informationen.
