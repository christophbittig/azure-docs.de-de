---
title: Überwachen der Integrität des Log Analytics-Arbeitsbereichs in Azure Monitor
description: Hier wird beschrieben, wie Sie die Integrität Ihres Log Analytics-Arbeitsbereichs mithilfe von Daten in der Tabelle „Operation“ überwachen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 4f127245ea36a7183603f5115739d317282ea686
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113009522"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Überwachen der Integrität des Log Analytics-Arbeitsbereichs in Azure Monitor
Um die Leistung und Verfügbarkeit Ihres Log Analytics-Arbeitsbereichs in Azure Monitor zu gewährleisten, müssen Sie in der Lage sein, jedes auftretende Probleme proaktiv zu erkennen. In diesem Artikel wird beschrieben, wie Sie die Integrität Ihres Log Analytics-Arbeitsbereichs mithilfe von Daten in der Tabelle [Operation](/azure/azure-monitor/reference/tables/operation) überwachen. Diese Tabelle ist in jedem Log Analytics-Arbeitsbereich enthalten und enthält Fehler und Warnungen, die im Arbeitsbereich auftreten. Wir empfehlen Ihnen, Warnungen für Probleme vom Typ „Warnung“ und „Fehler“ zu erstellen.

## <a name="_logoperation-function"></a>_LogOperation-Funktion

Azure Monitor-Protokolle senden Details zu einem Problem an die Tabelle [Operation](/azure/azure-monitor/reference/tables/operation) in dem Arbeitsbereich, in dem das Problem aufgetreten ist. Die Systemfunktion **_LogOperation** basiert auf der Tabelle **Operation** und bietet vereinfachte Informationen für Analysen und Warnungen.

## <a name="columns"></a>Spalten

Die Funktion **_LogOperation** gibt die Spalten in der folgenden Tabelle zurück.

| Column | BESCHREIBUNG |
|:---|:---|
| TimeGenerated | Der Zeitpunkt in UTC, zu dem der Vorfall aufgetreten ist. |
| Kategorie  | Die Vorgangskategoriegruppe. Hiermit kann nach Vorgangstypen gefiltert werden und ermöglicht das Erstellen genauerer Systemüberwachungen und Warnungen. Eine Liste der Kategorien finden Sie im Abschnitt unten. |
| Vorgang  | Eine Beschreibung des Vorgangstyps. Für diesen Vorgang kann darauf hingewiesen werden, dass einer der Log Analytics-Grenzwerte erreicht wurde, ein Problem mit dem Back-End-Prozess besteht oder eine andere Dienstmeldung angezeigt wird. |
| Ebene | Der Schweregrad des Problems:<br>– Info: Es ist keine besondere Aufmerksamkeit erforderlich.<br>– Warnung: Der Prozess wurde nicht erwartungsgemäß abgeschlossen, und Aufmerksamkeit ist erforderlich.<br>- Fehler: Verarbeitungsfehler, Eingreifen erforderlich. 
| Detail | Ausführliche Beschreibung des Vorgangs, enthält die spezifische Fehlermeldung. |
| _ResourceId | Die Ressourcen-ID der Azure-Ressource, die mit dem Vorgang im Zusammenhang steht.  |
| Computer | Der Computername, wenn der Vorgang mit einem Azure Monitor-Agent im Zusammenhang steht. |
| CorrelationId | Hiermit werden aufeinander folgende verwandte Vorgänge gruppiert. |


## <a name="categories"></a>Kategorien

In der folgenden Tabelle werden die Kategorien der Funktion „_LogOperation“ beschrieben. 

| Kategorie | BESCHREIBUNG |
|:---|:---|
| Erfassung           | Vorgänge, die Teil des Datenerfassungsprozesses sind. |
| Agent               | Weist auf ein Problem mit der Agentinstallation hin. |
| Datensammlung     | Vorgänge im Zusammenhang mit Datensammlungsprozessen. |
| Zielgruppenadressierung  | Vorgang des Typs *ConfigurationScope* wurde verarbeitet. |
| Bewertungslösung | Ein Bewertungsprozess wurde ausgeführt. |


### <a name="ingestion"></a>Erfassung
Erfassungsvorgänge sind Probleme, die während der Datenerfassung aufgetreten sind, einschließlich Benachrichtigungen über das Erreichen der Grenzwerte für den Azure Log Analytics-Arbeitsbereich. Fehlerbedingungen in dieser Kategorie können auf Datenverluste hindeuten und müssen daher unbedingt überwacht werden. In der folgenden Tabelle finden Sie Details zu diesen Vorgängen. Informationen zu Diensteinschränkungen für Log Analytics-Arbeitsbereiche finden Sie unter [Azure Monitor-Diensteinschränkungen](../service-limits.md#log-analytics-workspaces).

 
#### <a name="operation-data-collection-stopped"></a>Vorgang: Datensammlung beendet  
„Die Datensammlung wurde beendet, da das Tageslimit für kostenlose Daten erreicht wurde. Erfassungsstatus = OverQuota“

Innerhalb der letzten sieben Tage wurde für die Sammlung von Protokollen das Tageslimit erreicht. Das Limit besteht entweder, weil der Arbeitsbereich auf „Free-Tarif“ festgelegt ist, oder für den Arbeitsbereich wurde ein Limit für die tägliche Sammlung konfiguriert.
Hinweis: Nachdem das festgelegte Limit erreicht wurde, wird Ihre Datensammlung für den Tag automatisch beendet und erst am nächsten Sammlungstag fortgesetzt. 
 
Empfohlene Aktionen: 
*   Überprüfen Sie die Tabelle „_LogOperation“ auf Ereignisse vom Typ „Sammlung beendet“ und „Sammlung fortgesetzt“.</br>
`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Data collection"`
*   [Erstellen Sie eine Warnung](./manage-cost-storage.md#alert-when-daily-cap-is-reached) für das Vorgangsereignis „Datensammlung beendet“. Über diese Warnung können Sie eine Benachrichtigung erhalten, wenn das Sammlungslimit erreicht wurde.
*   Daten, die nach dem Erreichen des Tageslimits für die Sammlung erfasst werden, gehen verloren. Verwenden Sie das Blatt mit den „Erkenntnissen zu Arbeitsbereichen“, um die Nutzungsraten für die einzelnen Quellen zu überprüfen. Sie können auch die Entscheidung treffen, [Ihr maximales tägliches Datenvolumen zu verwalten](./manage-cost-storage.md#manage-your-maximum-daily-data-volume) oder eine [Änderung in einen Tarif vorzunehmen](./manage-cost-storage.md#changing-pricing-tier), der für Ihr Sammlungsratenmuster geeignet ist. 
* Die Datensammlungsrate wird pro Tag berechnet und zu Beginn des nächsten Tags zurückgesetzt. Sie können das Ereignis „Sammlung fortsetzen“ auch überwachen, indem Sie für das Vorgangsereignis „Datensammlung fortgesetzt“ eine [Warnung erstellen](./manage-cost-storage.md#alert-when-daily-cap-is-reached).

#### <a name="operation-ingestion-rate"></a>Vorgang: Erfassungsrate
„Die Rate für das Datenerfassungsvolumen hat den Schwellenwert für Ihren Arbeitsbereich von {0:0.00} MB pro Minute überschritten, und es wurden Daten verworfen.“ 

Empfohlene Aktionen:
*   Überprüfen Sie die Tabelle „_LogOperation“ auf das folgende Erfassungsratenereignis: `_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Ingestion rate"` 
  Hinweis: Vorgangstabelle im Arbeitsbereich alle sechs Stunden, während der Schwellenwert weiterhin überschritten wird. 
*   [Erstellen Sie eine Warnung](./manage-cost-storage.md#alert-when-daily-cap-is-reached) für das Vorgangsereignis „Datensammlung beendet“. Über diese Warnung können Sie eine Benachrichtigung erhalten, wenn das Limit erreicht wurde.
*   Daten, die gesammelt wurden, während die Erfassungsrate 100 % erreicht hat, werden verworfen und gehen verloren. 

Verwenden Sie das Blatt „Erkenntnisse zu Arbeitsbereichen“, um Ihre Nutzungsmuster zu überprüfen und ggf. eine Reduzierung zu erzielen.</br>
Weitere Informationen: </br>
[Azure Monitor-Diensteinschränkungen](../service-limits.md#data-ingestion-volume-rate) </br>
[Verwalten von Nutzung und Kosten für Azure Monitor-Protokolle](./manage-cost-storage.md#alert-when-daily-cap-is-reached)  

 
#### <a name="operation-maximum-table-column-count"></a>Vorgang: Maximale Anzahl von Tabellenspalten
„Daten vom Typ \<**table name**\> wurden verworfen, weil die Anzahl von Feldern (\<**new fields count**\>) über dem Limit von \<**current field count limit**\> benutzerdefinierten Feldern pro Datentyp liegt.“ 

Empfohlene Aktionen: Für benutzerdefinierte Tabellen können Sie zur [Analyse der Daten](./parse-text.md) in Abfragen wechseln.

#### <a name="operation-field-content-validation"></a>Vorgang: Überprüfung des Feldinhalts
„Die Werte \<**field name**\> vom Typ \<**table name**\> der folgenden Felder wurden auf die maximal zulässige Größe von \<**field size limit**\> Byte gekürzt. Passen Sie Ihre Eingabe entsprechend an.“ 

Von Azure-Protokollen wurde ein Feld verarbeitet, für das das Größenlimit überschritten wurde. Das Feld wurde auf das zulässige Limit für Felder gekürzt. Es ist nicht ratsam, Felder zu senden, für die das zulässige Limit überschritten wird, weil dies zu Datenverlust führt. 

Empfohlene Aktionen: Überprüfen Sie die Quelle des betroffenen Datentyps:
*   Falls die Daten über die HTTP-Datensammler-API gesendet werden, müssen Sie Ihren Code bzw. Ihr Skript ändern, um die Daten vor der Erfassung aufzuteilen.
*   Ändern Sie bei benutzerdefinierten Protokollen, die vom Log Analytics-Agent gesammelt werden, die Protokollierungseinstellungen für „application\tool“.
*   Erstellen Sie bei allen anderen Datentypen einen Supportfall. 
</br>Weitere Informationen: [Azure Monitor-Diensteinschränkungen](../service-limits.md#data-ingestion-volume-rate) 

### <a name="data-collection"></a>Datensammlung
#### <a name="operation-azure-activity-log-collection"></a>Vorgang: Erfassen des Azure-Aktivitätsprotokolls
„Der Zugriff auf das Abonnement ist verloren gegangen. Stellen Sie sicher, dass sich das Abonnement \<**subscription id**\> auf dem Azure Active Directory-Mandanten \<**tenant id**\> befindet. Wenn das Abonnement auf einen anderen Mandanten übertragen wird, hat dies keine Auswirkungen auf die Dienste. Die Übertragung der Informationen für den Mandanten kann aber bis zu eine Stunde dauern. '"

Beschreibung: In einigen Situationen, z. B. beim Verschieben eines Abonnements auf einen anderen Mandanten, fließen die Azure-Aktivitätsprotokolle unter Umständen nicht mehr in den Arbeitsbereich. In diesen Situationen müssen wir die Verbindung für das Abonnement wieder herstellen, indem wir den in diesem Artikel beschriebenen Prozess verwenden.

Empfohlene Aktionen: 
* Gehen Sie wie folgt vor, falls das in der Warnmeldung angegebene Abonnement nicht mehr vorhanden ist: Navigieren Sie unter „Arbeitsbereichsdatenquellen“ zum Blatt „Azure-Aktivitätsprotokoll“, wählen Sie das relevante Abonnement aus, und wählen Sie dann die Schaltfläche „Trennen“ aus.
* Gehen Sie wie folgt vor, falls Sie keinen Zugriff mehr auf das in der Warnmeldung angegebene Abonnement haben:
  * Führen Sie Schritt 1 aus, um die Verbindung mit dem Abonnement zu trennen. 
  * Wenden Sie sich zum Fortsetzen der Sammlung von Protokollen aus diesem Abonnement an den Abonnementbesitzer, um die Berechtigungen korrigieren und die Sammlung von Aktivitätsprotokollen wieder aktivieren zu lassen.
* [Erstellen Sie eine Diagnoseeinstellung](../essentials/activity-log.md#send-to-log-analytics-workspace), um das Aktivitätsprotokoll an einen Log Analytics-Arbeitsbereich zu senden. 

### <a name="agent"></a>Agent
#### <a name="operation-linux-agent"></a>Vorgang: Linux-Agent
„Fehler bei zwei aufeinanderfolgenden Konfigurationsanwendungen aus OMS-Einstellungen“

Die Konfigurationseinstellungen im Portal wurden geändert.

Empfohlene Aktion: Dieses Problem tritt auf, falls der Agent die neuen Konfigurationseinstellungen nicht abrufen kann. Sie müssen den Agent neu installieren, um dieses Problem zu beheben. Überprüfen Sie die Tabelle „_LogOperation“ auf das Agent-Ereignis.</br>

 `_LogOperation | where TimeGenerated >= ago(6h) | where Category == "Agent" | where Operation == "Linux Agent"  | distinct _ResourceId`

In der Liste sind die Ressourcen-IDs aufgeführt, für die der Agent über die falsche Konfiguration verfügt.
Sie müssen die aufgeführten Agents neu installieren, um das Problem zu beheben.

   

## <a name="alert-rules"></a>Warnregeln
Verwenden Sie [Protokollabfragewarnungen](../alerts/alerts-log-query.md) in Azure Monitor, um proaktiv benachrichtigt zu werden, wenn ein Problem in Ihrem Log Analytics-Arbeitsbereich erkannt wird. Verwenden Sie eine Strategie, bei der Sie rechtzeitig auf Probleme reagieren können und gleichzeitig die Kosten reduziert werden. Für Ihr Abonnement werden Gebühren für die einzelnen Warnungsregeln berechnet, wie dies unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) angegeben ist.

Als Strategie wird empfohlen, mit zwei Warnungsregeln basierend auf dem Schweregrad des Problems zu beginnen. Verwenden Sie eine größere Häufigkeit (z. B. alle 5 Minuten) für Fehler und eine geringere Häufigkeit (z. B. alle 24 Stunden) für Warnungen. Da Fehler auf potenzielle Datenverluste hindeuten, sollten Sie schnell darauf reagieren, um Verluste zu minimieren. Warnungen weisen in der Regel auf ein Problem hin, das keine sofortige Aufmerksamkeit erfordert, sodass Sie diese täglich überprüfen können.

Zum Erstellen der Protokollwarnungsregeln folgenden Sie der Vorgehensweise unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../alerts/alerts-log.md). In den folgenden Abschnitten werden die Details für jede Regel beschrieben.


| Abfrage | Schwellenwert | Zeitraum | Häufigkeit |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Mit diesen Warnungsregeln wird bei allen Vorgängen auf gleiche Weise mit einem Fehler oder einer Warnung reagiert. Sobald Sie mit den Vorgängen, die Warnungen generieren, besser vertraut sind, können Sie auf bestimmte Vorgänge anders reagieren. Beispielsweise können Sie Benachrichtigungen für bestimmte Vorgänge an verschiedene Personen senden. 

Um eine Warnungsregel für einen bestimmten Vorgang zu erstellen, verwenden Sie eine Abfrage, die die Spalten **Category** und **Operation** enthält. 

Im folgenden Beispiel wird eine Warnung erstellt, wenn die Rate für das Erfassungsvolumen 80 % des Limits erreicht hat.

- Ziel: Auswählen Ihres Log Analytics-Arbeitsbereichs
- Kriterien:
  - Signalname: Benutzerdefinierte Protokollsuche
  - Suchabfrage: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Basierend auf: Anzahl der Ergebnisse
  - Bedingung: Größer als
  - Schwellenwert: 0
  - Zeitraum: 5 (Minuten)
  - Häufigkeit: 5 (Minuten)
- Name der Warnungsregel: Daily data limit reached
- Schweregrad: Warnung (Schweregrad 1)


Im folgenden Beispiel wird eine Warnung erstellt, wenn die Datensammlung das Tageslimit erreicht hat. 

- Ziel: Auswählen Ihres Log Analytics-Arbeitsbereichs
- Kriterien:
  - Signalname: Benutzerdefinierte Protokollsuche
  - Suchabfrage: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Basierend auf: Anzahl der Ergebnisse
  - Bedingung: Größer als
  - Schwellenwert: 0
  - Zeitraum: 5 (Minuten)
  - Häufigkeit: 5 (Minuten)
- Name der Warnungsregel: Daily data limit reached
- Schweregrad: Warnung (Schweregrad 1)
 
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollwarnungen](../alerts/alerts-log.md).
- [Sammeln Sie Abfrageüberwachungsdaten](./query-audit.md) für Ihren Arbeitsbereich.
