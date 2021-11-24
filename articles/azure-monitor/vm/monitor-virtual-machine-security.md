---
title: 'Überwachen von VMs mit Azure Monitor: Sicherheit'
description: Erfahren Sie mehr über Dienste zur Überwachung der Sicherheit von VMs und deren Beziehung zu Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: c34a860f7b949a387af8c4905a948e1f47f68057
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310298"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Überwachen von VMs mit Azure Monitor: Sicherheitsüberwachung
Dieser Artikel ist Teil des Szenarios [Überwachen von VMs und der zugehörigen Workloads in Azure Monitor](monitor-virtual-machine.md). Darin werden die Azure-Dienste zum Überwachen der Sicherheit für Ihre VMs und deren Beziehung zu Azure Monitor beschrieben. Azure Monitor wurde konzipiert, um die Verfügbarkeit und Leistung Ihrer VMs und anderer Cloudressourcen zu überwachen. Während die in Azure Monitor gespeicherten operativen Daten nützlich sein können, um Sicherheitsvorfälle zu untersuchen, sind andere Dienste in Azure zur Überwachung der Sicherheit konzipiert. 

> [!IMPORTANT]
> Für die Sicherheitsdienste fallen unabhängig von Azure Monitor eigene Kosten an. Bevor Sie diese Dienste konfigurieren, sollten Sie sich über deren Preise informieren und die für Sie sinnvolle Investitionshöhe ermitteln.

## <a name="azure-services-for-security-monitoring"></a>Azure-Dienste für die Sicherheitsüberwachung
Azure Monitor konzentriert sich auf operative Daten wie Aktivitätsprotokolle, Metriken und von Log Analytics unterstützte Quellen, Windows-Ereignisse (ausgenommen Sicherheitsereignisse), Leistungsindikatoren, Protokolle und Syslog eingeschlossen. Führen Sie die Sicherheitsüberwachung in Azure mithilfe von Microsoft Defender für Cloud und Microsoft Sentinel aus. Da durch diese Dienste jeweils zusätzliche Kosten entstehen, sollten Sie deren Nutzen für Ihre Umgebung ermitteln, bevor Sie sie implementieren.


## <a name="integration-with-azure-monitor"></a>Integration in Azure Monitor
In der folgenden Tabelle werden die Integrationspunkte für Azure Monitor mit den Sicherheitsdiensten aufgeführt. Alle Dienste verwenden denselben Log Analytics-Agent. Dies verringert die Komplexität, da keine weiteren Komponenten auf Ihren VMs bereitgestellt werden. Defender für Cloud und Microsoft Sentinel speichern zugehörige Daten in einem Log Analytics-Arbeitsbereich, sodass Sie die von den verschiedenen Diensten gesammelten Daten mithilfe von Protokollabfragen korrelieren können. Alternativ können Sie auch eine benutzerdefinierte Arbeitsmappe erstellen, die Sicherheits-, Verfügbarkeits- und Leistungsdaten in einer einzigen Ansicht kombiniert.

| Integrationspunkt       | Azure Monitor | Microsoft Defender für Cloud | Microsoft Sentinel | Defender für Endpunkt |
|:---|:---|:---|:---|:---|
| Erfasst Sicherheitsereignisse     |   | X | X | X |
| Speichert Daten im Log Analytics-Arbeitsbereich | X | X | X |   | 
| Verwendet den Log Analytics-Agent     | X | X | X | X | 



## <a name="agent-deployment"></a>Agent-Bereitstellung
Sie können Defender für Cloud für die automatische Bereitstellung des Log Analytics-Agents auf virtuellen Azure-Computern konfigurieren. Auch wenn dies redundant erscheinen mag, da Azure Monitor denselben Agent bereitstellt, ist die Aktivierung für beide höchstwahrscheinlich sinnvoll, da sie jeweils eine eigene Konfiguration vornehmen. Wenn Defender für Cloud zum Beispiel versucht, einen Computer bereitzustellen, der bereits von Azure Monitor überwacht wird, wird der bereits installierte Agent verwendet und die Konfiguration für den Defender für Cloud-Arbeitsbereich hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* [Analysieren der für VMs gesammelten Überwachungsdaten](monitor-virtual-machine-analyze.md)
* [Erstellen von Warnungen anhand von gesammelten Daten](monitor-virtual-machine-alerts.md)
