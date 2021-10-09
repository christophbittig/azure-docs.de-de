---
title: Referenz für die Überwachung von Azure Key Vault-Daten
description: Wichtiges Referenzmaterial für die Überwachung von Key Vault
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: 53adbd28ad0b24b224f2427de0b108ce7f123dfd
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092526"
---
# <a name="monitoring-key-vault-data-reference"></a>Überwachen von Key Vault-Datenverweisen

Siehe [Überwachung von Key Vault](monitor-key-vault.md) für Details zum Sammeln und Analysieren von Überwachungsdaten für Key Vault.

## <a name="metrics"></a>Metriken


In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken für Key Vault aufgelistet.  

|Metriktyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| Verwaltetes HSM | [Microsoft.KeyVault/managedhsms](/azure-monitor/platform/resource-logs-categories.#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Key Vault-Metriken

Ressourcenanbieter und Typ: [Microsoft.KeyVault/vaults](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults)

| Name | Metrik | Einheit | Type | Beschreibung |
|:-------|:-----|:------------|:------------------|
| Tresorverfügbarkeit insgesamt | Verfügbarkeit      | Percent    | Average | Verfügbarkeit von Tresoranforderungen            | 
| Tresorauslastung insgesamt | SaturationShoebox | Percent | Average| Verwendete Tresorkapazität | 
| Dienst-API-Treffer, gesamt | ServiceApiHit | Anzahl | Anzahl | Anzahl der gesamten Service-API-Treffer |
| Gesamtwartezeit für Dienst-API | ServiceApiLatency | Millisekunden | Average | Gesamtlatenzzeit der API-Anforderungen des Dienstes |
| Dienst-API-Ergebnisse, gesamt | ServiceApiResult | Anzahl | Anzahl | Anzahl der gesamten Service-API-Ergebnisse |

Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](/azure-monitor/platform/metrics-supported).

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

Key Vault hat die folgenden Dimensionen, die mit seinen Metriken verbunden sind:

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt werden die Arten von Ressourcenprotokollen aufgeführt, die Sie für Key Vault sammeln können.

Als Referenz finden Sie eine Liste von [Microsoft.KeyVault/vaults](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults).  Ausführliche Informationen finden Sie unter [Azure Key Vault-Protokollierung](logging.md).

|Ressourcenprotokolltyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| Verwaltetes HSM | [Microsoft.KeyVault/managedhsms](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Dieser Abschnitt bezieht sich auf alle Azure Monitor Logs Kusto-Tabellen, die für Key Vault relevant und für die Abfrage durch Log Analytics verfügbar sind. 

|Ressourcentyp | Hinweise |
|-------|-----|
| [Schlüsseltresor](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

Eine Referenz zu allen Azure Monitor-Protokollen und Log Analytics-Tabellen finden Sie in der [Referenz zu Tabellen in Azure Monitor-Protokollen](/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Diagnosetabellen

Key Vault verwendet die [Azure-Diagnose](/azure-monitor/reference/tables/azurediagnostics), [Azure-Aktivität](/azure-monitor/reference/tables/azureactivity)-Tabelle und [Azure-Metriken](/azure-monitor/reference/tables/azuremetrics)-Tabellen, um Ressourcenprotokollinformationen zu speichern. Die folgenden Spalten sind relevant.

**Azure-Diagnose**

| Eigenschaft | Beschreibung |
|:--- |:---|
| _ResourceId | Ein eindeutiger Bezeichner für die Ressource, der der Datensatz zugeordnet ist. |
| CallerIPAddress | IP-Adresse des Benutzers, der die Operation durchgeführt hat UPN-Anspruch oder SPN-Anspruch je nach Verfügbarkeit. |
| DurationMs | Die Dauer des Vorgangs in Millisekunden. |
| httpStatusCode_d | Der von der Anforderung zurückgegebene HTTP-Statuscode (beispielsweise *200*) |
| Ebene | Ebene des Ereignisses. Einer der folgenden Werte: Kritisch, Fehler, Warnung, Informativ und Ausführlich. |
| Vorgangsname | Name des Vorgangs, z. B. Alert |
| properties_s |  |
| Region_s | |
| requestUri_s | Der URI der Clientanforderung. |
| Resource | |
| ResourceProvider | Ressourcenanbieter der Azure-Ressource, von der die Metrik gemeldet wird. |
| ResultSignature | |
| TimeGenerated | Datum und Uhrzeit der Erstellung des Datensatzes |

## <a name="see-also"></a>Siehe auch

- Siehe [Überwachung von Azure Key Vault](monitor-key-vault.md) für eine Beschreibung der Überwachung von Azure Key Vault.
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resources).
