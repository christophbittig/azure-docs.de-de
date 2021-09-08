---
title: Referenz zu den Überwachungsdaten von Load Balancer
titleSuffix: Azure Load Balancer
description: Wichtige Referenzmaterialien für die Überwachung von Load Balancer
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296509"
---
# <a name="monitoring-load-balancer-data-reference"></a>Referenz zu den Überwachungsdaten von Load Balancer

Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für Load Balancer finden Sie unter [Überwachen von Load Balancer](monitor-load-balancer.md).

## <a name="metrics"></a>Metriken

### <a name="load-balancer-metrics"></a>Lastenausgleichsmetriken 

| Metrik | Ressourcentyp | BESCHREIBUNG | Empfohlene Aggregation |
| ------ | ------------- | ----------- | ----------------------- |
| Datenpfadverfügbarkeit | Öffentlicher und interner Load Blancer |  Standard Load Balancer wendet kontinuierlich den Datenpfad aus einer Region auf das Load Balancer-Front-End bis hin zum SDN-Stapel an, der Ihren virtuellen Computer unterstützt. Solange integre Instanzen verbleiben, folgt die Messung demselben Pfad wie der Datenverkehr mit Lastenausgleich Ihrer Anwendungen. Der Datenpfad, der von Ihren Kunden verwendet wird, wird ebenfalls überprüft. Die Messung ist für Ihre Anwendung nicht sichtbar und bewirkt keine Beeinträchtigung bei anderen Vorgängen. | Average |
| Integritätsteststatus | Öffentlicher und interner Load Blancer | Standard Load Balancer verwendet einen verteilten Integritätsprüfungsdienst, der die Integrität Ihres Anwendungsendpunkts gemäß Ihren Konfigurationseinstellungen überwacht. Diese Metrik stellt eine Aggregat- oder nach Endpunkt gefilterte Ansicht jedes Instanzendpunkts im Load Balancer-Pool bereit. Sie können sehen, wie Load Balancer die Integrität Ihrer Anwendung gemäß Ihrer Integritätsprüfungskonfiguration beurteilt. | Average |
| SYN-Anzahl (Synchronisierung) | Öffentlicher und interner Load Blancer | Standard Load Balancer verwendet einen verteilten Integritätsprüfungsdienst, der die Integrität Ihres Anwendungsendpunkts gemäß Ihren Konfigurationseinstellungen überwacht. Diese Metrik stellt eine Aggregat- oder nach Endpunkt gefilterte Ansicht jedes Instanzendpunkts im Load Balancer-Pool bereit. Sie können sehen, wie Load Balancer die Integrität Ihrer Anwendung gemäß Ihrer Integritätsprüfungskonfiguration beurteilt. | Average |
| Anzahl von SNAT-Verbindungen | Öffentlicher Load Balancer | Standard Load Balancer meldet die Anzahl von maskierten ausgehenden Datenflüssen an das Front-End mit der öffentlichen IP-Adresse. SNAT-Ports (Source Network Address Translation) stellen eine erschöpfbare Ressource dar. Diese Metrik kann einen Hinweis darauf geben, in welchem Umfang Ihre Anwendung SNAT für ausgehende Flows nutzt. Die Zählerstände für erfolgreiche und fehlgeschlagene ausgehende SNAT-Flows werden gemeldet und können zur Problembehebung und Analyse Ihrer ausgehenden Flows verwendet werden. | SUM |
| Zugeordnete SNAT-Ports | Öffentlicher Load Balancer | Load Balancer Standard meldet die Anzahl der pro Back-End-Instanz zugeordneten SNAT-Ports. | Average |
| Verwendete SNAT-Ports | Öffentlicher Load Balancer | Load Balancer Standard meldet die Anzahl der pro Back-End-Instanz genutzten SNAT-Ports. | Average |
| Byteanzahl | Öffentlicher und interner Load Blancer | Standard Load Balancer meldet die pro Front-End verarbeiteten Daten. Sie bemerken möglicherweise, dass die Bytes nicht gleichmäßig auf die Back-End-Instanzen verteilt sind. Dies ist zu erwarten, da der Azure Load Balancer-Algorithmus auf Flows basiert. | SUM |
| Paketzahl | Öffentlicher und interner Load Blancer | Standard Load Balancer meldet die pro Front-End verarbeiteten Pakete. | SUM |

Weitere Informationen finden Sie in der Liste [aller in Azure Monitor für Load Balancer unterstützten Plattformmetriken](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers).

## <a name="metric-dimensions"></a>Metrikdimensionen

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Den Metriken in Load Balancer sind die folgenden Dimensionen zugeordnet.

| Dimensionsname | BESCHREIBUNG |
| -------------- | ----------- |
| Front-End-IP | Die Front-End-IP-Adresse der relevanten Lastenausgleichsregel(n) |
| Front-End-Port | Der Front-End-Port der relevanten Lastenausgleichsregel(n) | 
| Back-End-IP | Die Back-End-IP-Adresse der relevanten Lastenausgleichsregel(n) |
| Back-End-Port | Der Back-End-Port der relevanten Lastenausgleichsregel(n) |
| Protokolltyp | Das Protokoll der relevanten Lastenausgleichsregel. Dies kann TCP oder UDP sein. |
| Direction | Die Richtung, in die der Datenverkehr fließt. Er kann eingehend oder ausgehend sein. | 
| Verbindungsstatus | Der Status der SNAT-Verbindung, die ausstehend, erfolgreich oder fehlerhaft sein kann. | 

## <a name="resource-logs"></a>Ressourcenprotokolle

Ressourcenprotokolle werden derzeit von Azure Load Balancer nicht unterstützt.

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen
### <a name="diagnostics-tables"></a>Diagnosetabellen

Diagnosetabellen werden derzeit von Azure Load Balancer nicht unterstützt.
## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie die Vorgänge in Zusammenhang mit Load Balancer, die im Aktivitätsprotokoll erstellt werden können.

| Vorgang | BESCHREIBUNG |
| --- | --- |
| Microsoft.Network/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
| Microsoft.Network/loadBalancers/write | Erstellt einen Lastenausgleich oder aktualisiert einen vorhandenen Lastenausgleich. |
| Microsoft.Network/loadBalancers/delete | Löscht einen Lastenausgleich. |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | Fragt die Portzuordnung für NAT-Regeln für eingehenden Datenverkehr ab. |
| Microsoft.Network/loadBalancers/backendAddressPools/read | Ruft eine Back-End-Adresspooldefinition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/backendAddressPools/write | Erstellt oder aktualisiert einen Back-End-Adresspool für den Lastenausgleich. |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | Löscht einen Back-End-Adresspool für den Lastenausgleich. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | Listet die Back-End-Adressen des Load Balancer-Back-End-Adresspools auf. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Ruft eine Front-End-IP-Konfigurationsdefinition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Verknüpft eine Front-End-IP-Konfiguration für den Lastenausgleich. Nicht warnbar. |
| Microsoft.Network/loadBalancers/inboundNatPools/read | Ruft eine eingehende NAT-Pooldefinition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Verknüpft einen NAT-Pool für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
| Microsoft.Network/loadBalancers/inboundNatRules/read | Ruft eine eingehende NAT-Regeldefinition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/inboundNatRules/write | Erstellt eine eingehende NAT-Regel für den Lastenausgleich oder aktualisiert eine vorhandene eingehende NAT-Regel für den Lastenausgleich. |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | Löscht eine eingehende NAT-Regel für den Lastenausgleich. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | Ruft eine Lastenausgleichsregel-Definition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/networkInterfaces/read | Ruft Verweise auf alle Netzwerkschnittstellen unter einem Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/outboundRules/read | Ruft eine ausgehende Regeldefinition für den Lastenausgleich ab. |
| Microsoft.Network/loadBalancers/probes/read | Ruft einen Lastenausgleichstest ab. |
| Microsoft.Network/loadBalancers/probes/join/action | Ermöglicht die Verwendung von Prüfpunkten eines Lastenausgleichs. Beispielsweise kann mit dieser Berechtigung die healthProbe-Eigenschaft einer VM-Skalierungsgruppe auf den Prüfpunkt verweisen. Nicht warnbar. |
| Microsoft.Network/loadBalancers/virtualMachines/read | Ruft Verweise auf alle virtuellen Computer unter einem Lastenausgleich ab. |

Weitere Informationen zum Schema von Aktivitätsprotokolleinträgen finden Sie unter [Aktivitätsprotokollschema](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Load Balancer finden Sie unter [Überwachen von Azure Load Balancer](./monitor-load-balancer.md).