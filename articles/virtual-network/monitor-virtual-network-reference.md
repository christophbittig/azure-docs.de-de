---
title: Referenz zur Überwachung von Daten in virtuellen Azure-Netzwerken
description: Wichtiges Referenzmaterial für die Überwachung von virtuellen Azure-Netzwerken
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109861"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>Referenz zur Überwachung von Daten in virtuellen Azure-Netzwerken

Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für virtuelle Azure-Netzwerke finden Sie unter [Überwachen von virtuellen Azure-Netzwerken](monitor-virtual-network.md).

## <a name="metrics"></a>Metriken

In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken aufgeführt, die für ein virtuelles Azure-Netzwerk erfasst werden.  

| Metriktyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| Virtuelles Netzwerk | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| Netzwerkschnittstelle | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| Öffentliche IP-Adresse | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| NAT-Gateways | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Den Metriken in virtuellen Azure-Netzwerken sind die folgenden Dimensionen zugeordnet.

### <a name="dimensions-for-nat-gateway"></a>Dimensionen für NAT-Gateway

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **Direction (Out - In)** (Richtung (Ausgehend – Eingehend)) | Die Richtung des Datenverkehrs. Die unterstützten Werte lauten „Eingehend“ und „Ausgehend“. |
| **Protokoll** | Der Typ des Transportprotokolls. Die unterstützten Werte lauten „TCP“ und „UDP“. |

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt sind die Typen der Ressourcenprotokolle aufgeführt, die für Ressourcen erfasst werden können, die mit virtuellen Azure-Netzwerken verwendet werden. 

Eine Referenz finden Sie in der Liste [aller Typen von Ressourcenprotokollkategorien, die in Azure Monitor unterstützt werden](../azure-monitor/essentials/resource-logs-schema.md).

|Ressourcenprotokolltyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| Netzwerksicherheitsgruppe | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| Öffentliche IP-Adresse | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Dieser Abschnitt bezieht sich auf alle Kusto-Tabellen in Azure Monitor-Protokollen, die für virtuelle Azure-Netzwerke relevant und für die Abfrage durch Log Analytics verfügbar sind. 

|Ressourcentyp | Hinweise |
|-------|-----|
| Virtuelles Netzwerk | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| Netzwerkschnittstelle | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| Öffentliche IP-Adresse | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>Diagnosetabellen

**Virtuelles Netzwerk**

Das virtuelle Azure-Netzwerk verfügt nicht über Diagnoseprotokolle.

## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie die Vorgänge in Zusammenhang mit virtuellen Azure-Netzwerken, die im Aktivitätsprotokoll erstellt werden können.

| Vorgang | BESCHREIBUNG |
|:---|:---|
| Alle Verwaltungsvorgänge | Alle Verwaltungsvorgänge, einschließlich Erstellen, Aktualisieren und Löschen eines virtuellen Azure-Netzwerks |
| Erstellen oder Aktualisieren eines virtuellen Netzwerks | Ein virtuelles Netzwerk wurde erstellt oder aktualisiert. |
| Löschen eines virtuellen Netzwerks | Ein virtuelles Netzwerk wurde gelöscht.|

Weitere Informationen zum Schema von Aktivitätsprotokolleinträgen finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](../azure-monitor/essentials/activity-log-schema.md).

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung eines virtuellen Azure-Netzwerks finden Sie unter [Überwachen von virtuellen Azure-Netzwerken](monitor-virtual-network.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
