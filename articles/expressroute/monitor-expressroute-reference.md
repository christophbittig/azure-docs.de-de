---
title: Referenz zur Überwachung von ExpressRoute-Daten
description: Wichtige Referenzmaterialien für die Überwachung von ExpressRoute
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289795"
---
# <a name="monitoring-expressroute-data-reference"></a>Referenz zur Überwachung von ExpressRoute-Daten

Dieser Artikel enthält eine Referenz von Protokoll- und Metrikdaten, die gesammelt werden, um die Leistung und Verfügbarkeit von ExpressRoute zu analysieren.
Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für ExpressRoute finden Sie unter [Überwachen von ExpressRoute](monitor-expressroute.md).

## <a name="metrics"></a>Metriken

In diesem Abschnitt werden alle automatisch gesammelten Plattformmetriken für ExpressRoute aufgeführt. Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](../azure-monitor/essentials/metrics-supported.md).

| Metriktyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| ExpressRoute-Verbindung | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| ExpressRoute-Verbindungspeering | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| ExpressRoute-Gateways | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> Die Verwendung von *GlobalGlobalReachBitsInPerSecond* und *GlobalGlobalReachBitsOutPerSecond* ist nur sichtbar, wenn mindestens eine Global Reach-Verbindung hergestellt wird.
>

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Den Metriken in ExpressRoute sind die folgenden Dimensionen zugeordnet.

### <a name="dimension-for-expressroute-circuit"></a>Dimension für ExpressRoute-Verbindung

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **PeeringType** | Der Typ des konfigurierten Peerings Unterstützte Werte sind Microsoft und privates Peering. |
| **Peering** | Unterstützte Werte sind Primär und Sekundär. |
| **PeeredCircuitSkey** | Der Dienstschlüssel der ExpressRoute-Verbindung, der über Global Reach verbunden ist. |

### <a name="dimension-for-expressroute-gateway"></a>Dimension für ExpressRoute-Gateway

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **roleInstance** | Die Gatewayinstanz Jedes ExpressRoute-Gateway besteht aus mehreren Instanzen, und die unterstützten Werte sind GatewayTenantWork_IN_X (wobei X mindestens 0 und maximal der Anzahl von Gatewayinstanzen -1 entspricht). |

### <a name="dimension-for-express-direct"></a>Dimension für Express Direct

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **Link** | Der physische Link Jedes ExpressRoute Direct-Port besteht aus zwei physischen Links zur Redundanz, und die unterstützten Werte sind link1 und link2. |

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt werden die Typen der Ressourcenprotokolle aufgeführt, die für ExpressRoute erfasst werden können. 

|Ressourcenprotokolltyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| ExpressRoute-Verbindung | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

Eine Referenz finden Sie in der Liste [aller Typen von Ressourcenprotokollkategorien, die in Azure Monitor unterstützt werden](../azure-monitor/essentials/resource-logs-schema.md).

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Azure ExpressRoute verwendet Kusto-Tabellen aus Azure Monitor-Protokollen. Sie können diese Tabellen mit Log Analytics abfragen. Eine Referenz zu allen Azure Monitor-Protokollen und Log Analytics-Tabellen finden Sie in der [Referenz zu Tabellen in Azure Monitor-Protokollen](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie die Vorgänge in Zusammenhang mit ExpressRoute, die im Aktivitätsprotokoll erstellt werden können.

| Vorgang | BESCHREIBUNG |
|:---|:---|
| Alle Verwaltungsvorgänge | Alle administrativen Vorgänge, einschließlich Erstellen, Aktualisieren und Löschen einer ExpressRoute-Verbindung |
| Erstellen oder Aktualisieren einer ExpressRoute-Verbindung | Eine ExpressRoute-Verbindung wurde erstellt oder aktualisiert. |
| Löschen einer ExpressRoute-Verbindung | Eine ExpressRoute-Verbindung wurde gelöscht.|

Weitere Informationen zum Schema von Aktivitätsprotokolleinträgen finden Sie unter [Aktivitätsprotokollschema](../azure-monitor/essentials/activity-log-schema.md).

## <a name="schemas"></a>Schemas

Eine detaillierte Beschreibung des Schemas der obersten Ebene für Diagnoseprotokolle finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../azure-monitor/essentials/resource-logs-schema.md).

Beim Überprüfen von Metriken über die Protokollanalyse enthält die Ausgabe die folgenden Spalten:

|**Spalte**|**Typ**|**Beschreibung**|
| --- | --- | --- |
|TimeGrain|Zeichenfolge|PT1M (Metrikwerte werden jede Minute gepusht)|
|Anzahl|real|Normalerweise gleich 2 (jedes MSEE pusht jede Minute einen einzelnen Metrikwert).|
|Minimum|real|Der Mindestwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Maximum|real|Der Höchstwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Average|real|Entspricht (Minimum + Maximum)/2|
|Gesamt|real|Summe der beiden Metrikwerte aus beiden MSEEs (der Hauptwert, auf den sich bei der abgefragten Metrik konzentriert werden sollte)|

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von ExpressRoute finden Sie unter [Überwachen von Azure ExpressRoute](monitor-expressroute.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).