---
title: Problembehandlung für SQL Insights (Vorschauversion)
description: Problembehandlung für SQL Insights in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 53940c21a96da9b763a0b2f25400fb13cbba7098
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119676"
---
# <a name="troubleshooting-sql-insights-preview"></a>Problembehandlung für SQL Insights (Vorschauversion)
Überprüfen Sie auf der Registerkarte **Profil verwalten** den Status des Überwachungscomputers, um Probleme mit der Datensammlung in SQL Insights zu behandeln. Mögliche Zustände:

- Sammlung wird durchgeführt 
- Sammlung wird nicht durchgeführt 
- Sammlung wird durchgeführt (Fehler vorhanden) 
 
Klicken Sie auf den **Status**, um die Protokolle und weitere Details anzuzeigen. Diese können für die Behebung des Problems nützlich sein. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Status des Überwachungscomputers":::

## <a name="not-collecting-state"></a>Status „Sammlung wird nicht durchgeführt“ 
Der Überwachungscomputer weist den Status *Sammlung wird nicht durchgeführt* auf, wenn innerhalb der letzten zehn Minuten keine Daten für SQL in *InsightsMetrics* erfasst wurden. 

> [!NOTE]
> Stellen Sie sicher, dass Sie versuchen, Daten von einer [unterstützten SQL-Version](sql-insights-overview.md#supported-versions) zu sammeln. Der Versuch, Daten mit einem gültigen Profil und einer gültigen Verbindungszeichenfolge aber über eine nicht unterstützte Version von Azure SQL-Datenbank zu sammeln, führt beispielsweise dazu, dass der Status „Sammlung wird nicht durchgeführt“ ausgegeben wird.

SQL Insights verwendet zum Abrufen dieser Informationen die folgende Abfrage:

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Überprüfen Sie, ob Protokolle von Telegraf vorhanden sind, mit denen Sie die Ursache des Problems ermitteln können. Wenn Protokolleinträge vorhanden sind, können Sie auf *Sammlung wird nicht durchgeführt* klicken und in den Protokollen und Informationen zur Problembehandlung nach gängigen Problemen suchen. 


Wenn keine Protokolle vorhanden sind, müssen Sie die Protokolle auf der Überwachungs-VM auf die folgenden Dienste überprüfen, die von den zwei VM-Erweiterungen installiert werden:

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
 

Wenn das folgende Fehlerprotokoll angezeigt wird, deutet das auf ein Problem mit dem Dienst **mdsd** hin.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Telegraf-Dienstprotokolle 

Dienstprotokolle: `/var/log/ms-telegraf/telegraf.log`

Aktuelle Protokolle anzeigen: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` Aktuelle Fehler- und Warnungsprotokolle anzeigen: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Die von Telegraf verwendete Konfiguration wird vom wli-Dienst generiert und unter `/etc/ms-telegraf/telegraf.d/wli` gespeichert.
 
Wenn der ms-telegraf-Dienst aufgrund einer fehlerhaft generierten Konfiguration nicht starten kann, sollten Sie überprüfen, ob er mit dem Befehl `service ms-telegraf status` ausgeführt werden kann.

Führen Sie den Telegraf-Dienst manuell mit dem folgenden Befehl aus, um die Fehlermeldungen des Dienst anzuzeigen: 

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

Eine ungültige Konfiguration der Überwachungs-VM kann eine Ursache für den Status *Sammlung wird nicht durchgeführt* sein.  Die Standardkonfiguration lautet:

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

Mit dieser Konfiguration werden die Ersetzungstoken angegeben, die in der Profilkonfiguration auf der Überwachungs-VM verwendet werden sollen. Außerdem können Sie auf Geheimnisse aus dem Azure Key Vault verweisen, sodass die Konfiguration keine Geheimniswerte enthalten muss. Dieser Ansatz wird dringend empfohlen.

#### <a name="secrets"></a>Geheimnisse
Geheimnisse sind Token, deren Werte zur Laufzeit von einer Azure Key Vault-Instanz abgerufen werden. Ein Geheimnis setzt sich aus einem Key Vault-Verweis und einem Geheimnisnamen zusammen. Dadurch kann Azure Monitor den dynamischen Wert des Geheimnisses abrufen und diesen als Downstream-Konfigurationsverweis verwenden.

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

Die Berechtigungen für den Zugriff auf die Key Vault-Instanz werden einer verwalteten Dienstidentität auf der Überwachungs-VM erteilt. Azure Monitor erwartet, dass die Key Vault-Instanz mindestens Geheimnisse für den Zugriff auf die VM bereitstellt. Die Aktivierung ist über das Azure-Portal, PowerShell, die CLI oder Resource Manager-Vorlagen möglich.

#### <a name="parameters"></a>Parameter
Parameter sind Token, auf die in der Profilkonfiguration über JSON-Vorlagen verwiesen werden kann. Parameter haben einen Namen und einen Wert. Werte können beliebige JSON-Typen sein, einschließlich Objekten und Arrays. In der Profilkonfiguration wird auf einen Parameter verwiesen, indem sein Name im Format `.Parameters.<name>` angegeben wird.

Parameter können in der Key Vault-Instanz mit der gleichen Konvention auf Geheimnisse verweisen. `sqlAzureConnections` verweist beispielsweise mit der Konvention `$telegrafPassword` auf das Geheimnis `telegrafPassword`.

Zur Laufzeit werden alle Parameter und Geheimnisse aufgelöst und mit der Profilkonfiguration zusammengeführt, um die Konfiguration zu generieren, die auf dem Computer tatsächlich verwendet werden soll.

> [!NOTE]
> Die Parameternamen von `sqlAzureConnections`, `sqlVmConnections` und `sqlManagedInstanceConnections` werden alle in der Konfiguration benötigt, auch wenn für einige davon keine Verbindungszeichenfolgen vorhanden sein sollten.


## <a name="collecting-with-errors-state"></a>Status „Sammlung wird durchgeführt (Fehler vorhanden)“
Der Überwachungscomputer weist den Status *Sammlung wird nicht durchgeführt (Fehler vorhanden)* auf, wenn mindestens ein *InsightsMetrics*-Protokoll vorhanden ist, aber Fehler in der Tabelle *Operation* vorliegen.

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
> Enthält der Datentyp „WorkloadDiagnosticLogs“ keine Daten, müssen Sie möglicherweise Ihr Überwachungsprofil aktualisieren, um diese Daten zu speichern.  Wählen Sie in der SQL Insights-Umgebung „Profil verwalten“ > „Profil bearbeiten“ > „Überwachungsprofil aktualisieren“ aus.


Für gängige Fälle werden Informationen zur Problembehandlung in der Protokollansicht zur Verfügung gestellt: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Protokollansicht für die Problembehandlung":::



## <a name="next-steps"></a>Nächste Schritte

- Im Artikel [Aktivieren von SQL Insights](sql-insights-enable.md) finden Sie weitere Informationen.
