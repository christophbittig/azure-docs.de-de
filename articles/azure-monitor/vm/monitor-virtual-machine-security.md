---
title: 'Überwachen von VMs mit Azure Monitor: Sicherheit'
description: Erfahren Sie mehr über Dienste zur Überwachung der Sicherheit von VMs und deren Beziehung zu Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674278"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Überwachen von VMs mit Azure Monitor: Sicherheitsüberwachung
Dieser Artikel ist Teil des Szenarios [Überwachen von VMs und der zugehörigen Workloads in Azure Monitor](monitor-virtual-machine.md). Darin werden die Azure-Dienste zum Überwachen der Sicherheit für Ihre VMs und deren Beziehung zu Azure Monitor beschrieben. Azure Monitor wurde konzipiert, um die Verfügbarkeit und Leistung Ihrer VMs und anderer Cloudressourcen zu überwachen. Während die in Azure Monitor gespeicherten operativen Daten nützlich sein können, um Sicherheitsvorfälle zu untersuchen, sind andere Dienste in Azure zur Überwachung der Sicherheit konzipiert. 

> [!IMPORTANT]
> Für die Sicherheitsdienste fallen unabhängig von Azure Monitor eigene Kosten an. Bevor Sie diese Dienste konfigurieren, sollten Sie sich über deren Preise informieren und die für Sie sinnvolle Investitionshöhe ermitteln.

## <a name="azure-services-for-security-monitoring"></a>Azure-Dienste für die Sicherheitsüberwachung
Azure Monitor konzentriert sich auf operative Daten wie Aktivitätsprotokolle, Metriken und von Log Analytics unterstützte Quellen, Windows-Ereignisse (ausgenommen Sicherheitsereignisse), Leistungsindikatoren, Protokolle und Syslog eingeschlossen. Die Sicherheitsüberwachung in Azure erfolgt über Azure Security Center und Azure Sentinel. Da durch diese Dienste jeweils zusätzliche Kosten entstehen, sollten Sie deren Nutzen für Ihre Umgebung ermitteln, bevor Sie sie implementieren.

[Azure Security Center](../../security-center/security-center-introduction.md) sammelt Informationen zu Azure-Ressourcen und Hybridservern. Wenngleich Security Center Sicherheitsereignisse erfassen kann, konzentriert sich Security Center auf das Sammeln von Bestandsdaten, Risikobewertungsergebnissen und Richtlinienüberwachungen, um auf Sicherheitsrisiken hinzuweisen und Korrekturmaßnahmen zu empfehlen. Zu den wichtigsten Funktionen gehören eine interaktive Netzwerkkarte, Just-in-Time-VM-Zugriff, adaptive Netzwerkhärtung und die adaptive Anwendungssteuerung zum Blockieren verdächtiger ausführbarer Dateien.

[Azure Defender für Server](../../security-center/azure-defender.md) ist die Serverbewertungslösung, die von Security Center bereitgestellt wird. Defender für Server kann Windows-Sicherheitsereignisse an Log Analytics senden. Für die Warnungserstellung oder Analyse stützt sich Security Center nicht auf Windows-Sicherheitsereignisse. Die Verwendung dieses Features ermöglicht die zentrale Archivierung von Ereignissen zu Untersuchungs- oder anderen Zwecken.

[Azure Sentinel](../../sentinel/overview.md) ist eine Lösung für Security Information & Event Management (SIEM) und für die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Sentinel sammelt Sicherheitsdaten aus einer Vielzahl von Microsoft- und Drittanbieterquellen, um Warnungen, Visualisierungen und Automatisierung bereitzustellen. Diese Lösung konzentriert sich darauf, so viele Sicherheitsprotokolle wie möglich zu konsolidieren, darunter auch Windows-Sicherheitsereignisse. Azure Sentinel kann außerdem Windows-Sicherheitsereignisprotokolle erfassen und einen Log Analytics-Arbeitsbereich gemeinsam mit Security Center verwenden. Sicherheitsereignisse können nur von Azure Sentinel oder von Security Center gesammelt werden, wenn derselbe Arbeitsbereich genutzt wird. Im Gegensatz zu Security Center sind Sicherheitsereignisse eine wichtige Komponente für die Warnungserstellung und Analyse in Azure Sentinel.

[Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) ist eine Endpunktsicherheitsplattform auf Unternehmensniveau, die Organisationen dabei unterstützt, komplexe Bedrohungen zu vermeiden, zu erkennen, zu untersuchen und darauf zu reagieren. Die Lösung wurde primär zum Schutz von Windows-Benutzergeräten entwickelt. Defender für Endpunkt überwacht Arbeitsstationen, Server, Tablets und Mobiltelefone mit verschiedenen Betriebssystemen auf Sicherheitsprobleme und -risiken. Defender für Endpunkt ist eng an Microsoft Endpoint Manager ausgerichtet, um Daten zu erfassen und Sicherheitsbewertungen bereitzustellen. Die Datensammlung basiert in erster Linie auf ETW-Ablaufverfolgungsprotokollen, die Speicherung erfolgt in einem isolierten Arbeitsbereich.

## <a name="integration-with-azure-monitor"></a>Integration in Azure Monitor
In der folgenden Tabelle werden die Integrationspunkte für Azure Monitor mit den Sicherheitsdiensten aufgeführt. Alle Dienste verwenden denselben Log Analytics-Agent. Dies verringert die Komplexität, da keine weiteren Komponenten auf Ihren VMs bereitgestellt werden. Security Center und Azure Sentinel speichern zugehörige Daten in einem Log Analytics-Arbeitsbereich, sodass Sie die von den verschiedenen Diensten gesammelten Daten mithilfe von Protokollabfragen korrelieren können. Alternativ können Sie auch eine benutzerdefinierte Arbeitsmappe erstellen, die Sicherheits-, Verfügbarkeits- und Leistungsdaten in einer einzigen Ansicht kombiniert.

| Integrationspunkt       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender für Endpunkt |
|:---|:---|:---|:---|:---|
| Erfasst Sicherheitsereignisse     |   | X | X | X |
| Speichert Daten im Log Analytics-Arbeitsbereich | X | X | X |   | 
| Verwendet den Log Analytics-Agent     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>Überlegungen zum Arbeitsbereichsentwurf
Wie unter [Überwachen von VMs mit Azure Monitor: Konfigurieren der Überwachung](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace) beschrieben, benötigen Azure Monitor und die Sicherheitsdienste einen Log Analytics-Arbeitsbereich. Abhängig von Ihren speziellen Anforderungen können Sie einen gemeinsamen Arbeitsbereich verwenden oder Ihre Verfügbarkeits- und Leistungsdaten von Ihren Sicherheitsdaten trennen. Ausführliche Informationen zur Logik, die Sie beim Entwerfen einer Arbeitsbereichskonfiguration berücksichtigen sollten, finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../logs/design-logs-deployment.md).

## <a name="agent-deployment"></a>Agent-Bereitstellung
Sie können Security Center für die automatische Bereitstellung des Log Analytics-Agents auf Azure-VMs konfigurieren. Auch wenn dies redundant erscheinen mag, da Azure Monitor denselben Agent bereitstellt, ist die Aktivierung für beide höchstwahrscheinlich sinnvoll, da sie jeweils eine eigene Konfiguration vornehmen. Wenn Security Center beispielsweise versucht, einen bereits durch Azure Monitor überwachten Computer bereitzustellen, verwendet er den bereits installierten Agent und fügt die Konfiguration für den Security Center-Arbeitsbereich hinzu.

## <a name="next-steps"></a>Nächste Schritte

* [Analysieren der für VMs gesammelten Überwachungsdaten](monitor-virtual-machine-analyze.md)
* [Erstellen von Warnungen anhand von gesammelten Daten](monitor-virtual-machine-alerts.md)
