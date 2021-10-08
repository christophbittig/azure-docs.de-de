---
title: Überwachen von Azure Container Registry
description: Hier erfahren Sie, wie Sie Azure Container Registry mithilfe von Azure Monitor-Features überwachen können.
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 08/13/2021
ms.openlocfilehash: ff82571b791f20cf46150be06e583af29f13dbd5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586966"
---
# <a name="monitor-azure-container-registry"></a>Überwachen von Azure Container Registry

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. In diesem Artikel wird das Überwachen von Daten beschrieben, die von Azure Container Registry generiert werden. Außerdem wird erläutert, wie Sie die Features von Azure Monitor nutzen können, um diese Daten zu analysieren und Warnungen dafür zu erstellen.

## <a name="monitor-overview"></a>Azure Monitor: Übersicht

Die Seite **Übersicht** im Azure-Portal enthält für jede Registrierung eine kurze Übersicht über die aktuelle Ressourcennutzung und -aktivität, z. B. Push- und Pullvorgänge. Diese allgemeinen Informationen sind nützlich, doch wird hier nur eine kleine Menge Daten angezeigt. 

:::image type="content" source="media/monitor-service/metrics-overview.png" alt-text="Übersicht über Registrierungsmetriken"::: 

## <a name="what-is-azure-monitor"></a>Was ist Azure Monitor?

Azure Container Registry erstellt Überwachungsdaten mit [Azure Monitor](../azure-monitor/overview.md), einem vollständigen Überwachungsdienst in Azure, der sämtliche Features für das Überwachen Ihrer Azure-Ressourcen sowie der Ressourcen in anderen Clouds und lokaler Ressourcen bereitstellt.

Beginnen Sie mit dem Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), in dem die folgenden Konzepte beschrieben werden:

- Was ist Azure Monitor?
- Kosten für die Überwachung
- In Azure gesammelte Überwachungsdaten
- Konfigurieren der Datensammlung
- Standardtools in Azure zum Analysieren von Überwachungsdaten sowie zum Generieren von Warnungen

Die folgenden Abschnitte basieren auf diesem Artikel. Darin werden die spezifischen von Azure Container Registry erfassten Daten beschrieben, und es werden Beispiele für die Konfiguration der Datensammlung und die Analyse dieser Daten mit Azure-Tools bereitgestellt.

## <a name="monitoring-data"></a>Überwachungsdaten 

Azure Container Registry sammelt dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die in [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Ausführliche Informationen zu den Metriken und Protokollen, die von Azure Container Registry erstellt werden, finden Sie in der [Referenz zu Azure Container Registry-Überwachungsdaten](monitor-service-reference.md).

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für Azure Container Registry sind in der [Referenz zu Azure Container Registry-Überwachungsdaten](monitor-service-reference.md#resource-logs) aufgeführt.

> [!TIP]
> Sie können Diagnoseeinstellungen für eine Registrierung auch erstellen, indem Sie im Portal zu Ihrer Registrierung navigieren. Wählen Sie im Menü unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

Die folgende Abbildung zeigt die Möglichkeiten, die Sie haben, wenn Sie Diagnoseeinstellungen für eine Registrierung aktivieren.

:::image type="content" source="media/monitor-service/diagnostic-settings.png" alt-text="Diagnoseeinstellungen für Container Registry":::

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics-preview"></a>Analysieren von Metriken (Vorschau)

Sie können Metriken für Azure Container Registry mit Metriken aus anderen Azure-Diensten mit dem Metrik-Explorer analysieren. Dazu wählen Sie im Menü **Azure Monitor** die Option **Metriken** aus. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

> [!TIP]
> Sie können auch zum Metrik-Explorer gelangen, indem Sie im Portal zu Ihrer Registrierung navigieren. Wählen Sie im Menü unter **Überwachung** die Option **Metriken (Vorschau)** aus.

Eine Liste der für Azure Container Registry erfassten Plattformmetriken finden Sie in der [Referenz zu Azure Container Registry-Überwachungsdaten im Abschnitt „Metriken“](monitor-service-reference.md#metrics).  

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

### <a name="azure-cli"></a>Azure CLI

Mit den folgenden Azure CLI-Befehlen können Informationen zu den Metriken von Azure Container Registry abgerufen werden.

* [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions): Auflisten von Metrikdefinitionen und -dimensionen
* [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list): Abrufen von Metrikwerten

### <a name="rest-api"></a>REST-API 

Mit der Azure Monitor-REST-API können Sie programmgesteuert Informationen zu den Metriken von Azure Container Registry abrufen.

* [Auflisten von Metrikdefinitionen und -dimensionen](/rest/api/monitor/metricdefinitions/list)
* [Abrufen von Metrikwerten](/rest/api/monitor/metrics/list)

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird in [Azure Monitor-Ressourcenprotokollschema](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema) beschrieben. Das Schema für Azure Container Registry-Ressourcenprotokolle finden Sie in der [Referenz zu Azure Container Registry-Daten](monitor-service-reference.md#schemas). 

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist ein Plattformprotokoll in Azure, das einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

Eine Liste der für Azure Container Registry erfassten Arten von Ressourcenprotokollen finden Sie in der [Referenz zu Azure Container Registry-Überwachungsdaten](monitor-service-reference.md#resource-logs).  

Eine Liste der Tabellen, die von Azure Monitor-Protokollen verwendet und von Log Analytics abgefragt werden können, finden Sie in der [Referenz zu Azure Container Registry-Überwachungsdaten](monitor-service-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie **Protokolle** im Menü **Azure Container Registry** auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf die aktuelle Registrierung festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Registrierungen oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/).

Beispielsweise ruft die folgende Abfrage die jüngsten 24 Stunden von Daten aus der Tabelle **ContainerRegistryRepositoryEvents** ab:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

In der folgenden Abbildung ist eine Beispielausgabe dargestellt:

:::image type="content" source="media/monitor-service/azure-monitor-query.png" alt-text="Abfragen von Protokolldaten":::

Mithilfe der folgenden Abfragen können Sie Ihre Registrierungsressource überwachen. 

### <a name="error-events-from-the-last-hour"></a>Fehlerereignisse innerhalb der letzten Stunde

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>Die 100 jüngsten Registrierungsereignisse

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identität des Benutzers oder Objekts, der bzw. das das Repository gelöscht hat

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identität des Benutzers oder Objekts, der bzw. das die Markierung gelöscht hat

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Vorgangsfehler auf Repositoryebene

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Authentifizierungsfehler für die Registrierung

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben Vor- und Nachteile.


<!-- only include next line if applications run on your service and work with App Insights. 

If you are creating or running an application which run on <*service*> [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) may offer additional types of alerts.
-->

In der folgenden Tabelle sind allgemeine und empfohlene Warnungsregeln für Azure Container Registry aufgeführt.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->
| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
| Metrik | Signal: Verwendeter Speicher<br/>Operator: Größer als<br/>Aggregationstyp: Mittelwert<br/>Schwellenwert: 5 GB|  Gibt eine Warnung aus, wenn der verwendete Registrierungsspeicher einen angegebenen Wert überschreitet.|
| | | |

### <a name="example-send-email-alert-when-registry-storage-used-exceeds-a-value"></a>Beispiel: Senden einer E-Mail-Warnung, wenn der verwendete Registrierungsspeicher einen Wert überschreitet

1. Navigieren Sie im Azure-Portal zu Ihrer Registrierung.
1. Wählen Sie unter **Überwachung** die Option **Metriken (Vorschau)** aus.
1. Wählen Sie im Metrik-Explorer unter **Metrik** die Option **Verwendeter Speicher** aus.
1. Wählen Sie **Neue Warnungsregel** aus.
1. Bestätigen Sie unter **Bereich** die Registrierungsressource, für die Sie eine Warnungsregel erstellen möchten.
1. Wählen Sie unter **Bedingung** die Option **Bedingung hinzufügen** aus.
    1. Wählen Sie unter **Signalname** die Option **Verwendeter Speicher** aus.
    1. Wählen Sie unter **Diagrammzeitraum** die Option **In den letzten 24 Stunden** aus.
    1. Wählen Sie unter **Warnungslogik** für **Schwellenwert** einen Wert wie z. B. *5* aus. Wählen Sie unter **Einheit** einen Wert wie z. B. *GB* aus.
    1. Übernehmen Sie für die übrigen Einstellungen die Standardwerte, und wählen Sie **Fertig** aus.
1. Wählen Sie unter **Aktionen** die Option **Aktionsgruppen hinzufügen** >  **+ Aktionsgruppe erstellen** aus.
    1. Geben Sie Details der Aktionsgruppe ein.
    1. Wählen Sie auf der Registerkarte **Benachrichtigungen** die Option **E-Mail/SMS/Push/Sprachanruf** aus, und geben Sie einen Empfänger wie *admin@contoso.com* ein. Klicken Sie auf **Überprüfen + erstellen**.
1. Geben Sie einen Namen und eine Beschreibung der Warnungsregel ein, und wählen Sie den Schweregrad aus.
1. Wählen Sie **Warnungsregel erstellen** aus.

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von Azure Container Registry erstellt werden, finden Sie in der [Referenz zu Azure Container Registry-Überwachungsdaten](monitor-service-reference.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
- Siehe [Registrierungsbenutzung zeigen](container-registry-skus.md#show-registry-usage), um zu erfahren, wie Sie eine Momentaufnahme der Speicherauslastung und des Ressourcenverbrauchs in Ihrer Registrierung erhalten.