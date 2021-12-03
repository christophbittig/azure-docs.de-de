---
title: Problembehandlung von SQL Insights (Vorschau)
description: Erfahren Sie, wie Sie Probleme mit SQL Insights in Azure Monitor behandeln.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/03/2021
ms.openlocfilehash: bf3b03f7837be31cf025d9ec7ea06a86a5c62e18
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989354"
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

Wenn keine Protokolleinträge vorhanden sind, müssen Sie die Protokolle auf dem virtuellen Überwachungscomputer auf die folgenden Dienste überprüfen, die von zwei VM-Erweiterungen installiert werden:

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

Eine mögliche Ursache für den Status **Sammlung wird nicht durchgeführt** kann eine ungültige Konfiguration der Überwachungs-VM sein. Dies ist die einfachste Form der Konfiguration:

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

In dieser Konfiguration enthält die Datenbankverbindungszeichenfolge ein `$telegrafPassword`-Ersatztoken. SQL Insights ersetzt dieses Token durch das SQL Authentifizierungskennwort, das von Key Vault abgerufen wurde. Der Key Vault-URI wird im `telegrafPassword`-Konfigurationsabschnitt unter `secrets` angegeben.

#### <a name="secrets"></a>Geheimnisse
Geheimnisse sind Token, deren Werte zur Laufzeit von einer Azure Key Vault-Instanz abgerufen werden. Ein Geheimnis wird durch ein Wertpaar definiert, das den Schlüsseltresor-URI und einen Geheimnisnamen enthält. Diese Definition ermöglicht es SQL Insights, den Wert des Geheimnisses zur Laufzeit abzurufen und in der Downstreamkonfiguration zu verwenden.

Sie können so viele Geheimnisse wie nötig definieren, z. B. auch Geheimnisse, die in mehreren Key Vault-Instanzen gespeichert sind.

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

Die Berechtigungen für den Zugriff auf die Key Vault-Instanz werden einer verwalteten Identität auf der Überwachungs-VM erteilt. Dieser verwalteten Identität muss Get-Berechtigung für alle Key Vault-Geheimnisse gewährt werden, auf die in der Konfiguration des Überwachungsprofils verwiesen wird. Dies kann über das Azure-Portal, PowerShell, die Azure CLI oder eine Azure Resource Manager-Vorlage erledigt werden.

#### <a name="parameters"></a>Parameter
Parameter sind Token, auf die in der Profilkonfiguration über JSON-Vorlagen verwiesen werden kann. Parameter haben einen Namen und einen Wert. Werte können beliebige JSON-Typen sein, einschließlich Objekten und Arrays. In der Profilkonfiguration wird über den zugehörigen Namen mit der Konvention `.Parameters.<name>` auf einen Parameter verwiesen.

Parameter können in der Key Vault-Instanz mit der gleichen Konvention auf Geheimnisse verweisen. `sqlAzureConnections` verweist beispielsweise mit der Konvention `$telegrafPassword` auf das Geheimnis `telegrafPassword`.

Zur Laufzeit werden alle Parameter und Geheimnisse aufgelöst und mit der Profilkonfiguration zusammengeführt, um die Konfiguration zu generieren, die auf dem Computer tatsächlich verwendet werden soll.

> [!NOTE]
> Die Parameternamen von `sqlAzureConnections`, `sqlVmConnections` und `sqlManagedInstanceConnections` werden alle in der Konfiguration benötigt, auch wenn für einige von ihnen keine Verbindungszeichenfolgen angegeben werden.

## <a name="status-collecting-with-errors"></a>Status: Sammlung wird durchgeführt (Fehler vorhanden)
Der Überwachungscomputer weist den Status **Sammlung wird durchgeführt (Fehler vorhanden)** auf, wenn mindestens ein aktuelles *InsightsMetrics*-Protokoll vorhanden ist, aber Fehler in der Tabelle *Operation* vorliegen.

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
> Wenn in `WorkloadDiagnosticLogs` keine Daten angezeigt werden, müssen Sie ggf. Ihr Überwachungsprofil aktualisieren. Wählen Sie in SQL Insights im Azure-Portal **Profil verwalten** > **Profil bearbeiten** > **Überwachungsprofil aktualisieren** aus.

Für gängige Fälle werden in der Protokollansicht Tipps zur Problembehandlung zur Verfügung gestellt: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Protokollansicht für die Problembehandlung":::

## <a name="known-issues"></a>Bekannte Probleme

In der Vorschauversion von SQL Insights können die folgenden bekannten Probleme auftreten.

* **Fehlermeldung, dass die Anmeldung fehlgeschlagen ist, beim Herstellen einer Verbindung mit dem Server oder der Datenbank**. Die Verwendung bestimmter Sonderzeichen in SQL-Authentifizierungskennwörtern, die in der Konfiguration des virtuellen Überwachungscomputers oder in Key Vault gespeichert sind, kann verhindern, dass der virtuelle Überwachungscomputer eine Verbindung mit einem SQL Server oder einer Datenbank herstellen kann. Dieser Zeichensatz umfasst runde, eckige und geschweifte Klammern, das Dollarzeichen, Schrägstriche und umgekehrte Schrägstriche sowie Punkte (`[ { ( ) } ] $ \ / .`).

## <a name="best-practices"></a>Bewährte Methoden

* **Stellen Sie sicher, dass vom virtuellen Überwachungscomputer aus ein Zugriff auf den Key Vault möglich ist**. Wenn Sie Key Vault zum Speichern von SQL-Authentifizierungskennwörtern verwenden (dringend empfohlen), müssen Sie sicherstellen, dass die Netzwerk- und Sicherheitskonfiguration Zugriff des virtuellen Überwachungscomputers auf Key Vault zulässt. Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](/key-vault/general/access-behind-firewall.md) und [Konfigurieren von Azure Key Vault-Netzwerkeinstellungen](/key-vault/general/how-to-azure-key-vault-network-security.md). Um zu überprüfen, ob der virtuelle Überwachungscomputer auf Key Vault zugreifen kann, können Sie die folgenden Befehle in einer SSH-Sitzung über eine Verbindung zum virtuellen Computer ausführen. Sie sollten das Zugriffstoken und das Geheimnis erfolgreich abrufen können. Ersetzen Sie `[YOUR-KEY-VAULT-URL]`, `[YOUR-KEY-VAULT-SECRET]` und `[YOUR-KEY-VAULT-ACCESS-TOKEN]` durch die tatsächlichen Werte.

  ```bash
  # Get an access token for accessing Key Vault secrets
  curl 'http://[YOUR-KEY-VAULT-URL]/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true
  
  # Get Key Vault secret
  curl 'https://[YOUR-KEY-VAULT-URL]/secrets/[YOUR-KEY-VAULT-SECRET]?api-version=2016-10-01' -H "Authorization: Bearer [YOUR-KEY-VAULT-ACCESS-TOKEN]"
  ```

* **Aktualisieren Sie die Software auf dem virtuellen Überwachungscomputer**. Es wird dringend empfohlen, das Betriebssystem und die Erweiterungen auf dem virtuellen Überwachungscomputer regelmäßig zu aktualisieren. Wenn eine Erweiterung automatische Upgrades unterstützt, aktivieren Sie die betreffende Option.

* **Speichern Sie vorherige Konfigurationen**. Wenn Sie Änderungen am Überwachungsprofil oder an der Konfiguration des virtuellen Überwachungscomputers vornehmen möchten, sollten Sie zuerst eine Arbeitskopie Ihrer Konfigurationsdaten speichern. Wählen Sie auf der SQL Insights-Seite im Azure-Portal **Profil verwalten** > **Profil bearbeiten** aus, und kopieren Sie den Text der **aktuellen Überwachungsprofilkonfiguration** in eine Datei. Wählen Sie ebenso **Profil verwalten** > **Konfigurieren** für den virtuellen Überwachungscomputer aus, und kopieren Sie den Text der **aktuellen Überwachungskonfiguration** in eine Datei. Wenn nach Konfigurationsänderungen Datensammlungsfehler auftreten, können Sie die neue Konfiguration mit der bekanntermaßen funktionierenden Konfiguration mithilfe eines Textvergleichstools vergleichen, um Änderungen zu finden, die sich u. U. auf die Sammlung ausgewirkt haben.

## <a name="next-steps"></a>Nächste Schritte

- Im Artikel [Aktivieren von SQL Insights](sql-insights-enable.md) finden Sie weitere Informationen.
