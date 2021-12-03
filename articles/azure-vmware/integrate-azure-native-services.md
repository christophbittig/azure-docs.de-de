---
title: Überwachen und Schützen von VMs mit nativen Azure-Diensten
description: Hier erfahren Sie, wie Sie native Microsoft Azure-Tools integrieren und bereitstellen, um Ihre Azure VMware Solution-Workloads zu überwachen und zu verwalten.
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: 8c95a74df7608aafbec09da9af94b0f82eb2ced3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315822"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>Überwachen und Schützen von VMs mit nativen Azure-Diensten

Mithilfe von nativen Microsoft Azure-Diensten können Sie Ihre virtuellen Computer (VMs) in einer Hybridumgebung (Azure, Azure VMware Solution und lokal) überwachen, verwalten und schützen. In diesem Artikel integrieren Sie native Azure-Dienste in Ihre private Azure VMware Solution-Cloud. Außerdem erfahren Sie, wie Sie die Tools verwenden, um Ihre VMs während ihres gesamten Lebenszyklus zu verwalten.

Zu den nativen Azure-Diensten, die Sie in Azure VMware Solution integrieren können, gehören:

- **Azure Arc** erweitert die Azure-Verwaltung auf jede Infrastruktur, einschließlich Azure VMware Solution, der lokalen Infrastruktur oder anderer Cloudplattformen. *Server mit Azure Arc-Unterstützung* bieten die Möglichkeit, physische Server und virtuelle Computer unter Windows und Linux zu verwalten, die [außerhalb](../azure-arc/servers/overview.md) von Azure, in Ihrem Unternehmensnetzwerk oder bei einem anderen Cloudanbieter gehostet werden. Sie können einen Kubernetes-Cluster, der in Ihrer Azure VMware Solution-Umgebung gehostet wird, mithilfe von [Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/overview.md) anfügen. 

- **Azure Monitor** sammelt, analysiert und verarbeitet Telemetriedaten aus Ihren lokalen und Cloud-Umgebungen. Es ist keine Bereitstellung erforderlich.  Sie können die Leistung des Gastbetriebssystems überwachen, um Anwendungsabhängigkeiten für virtuelle Azure VMware Solution-Computer oder lokale virtuelle Computer zu erkennen und zuzuordnen. Ihr Log Analytics-Arbeitsbereich in Azure Monitor ermöglicht das Sammeln von Protokollen und Leistungsindikatoren über den Log Analytics-Agent oder Erweiterungen. 

   Mit Azure Monitor können Sie Daten aus verschiedenen Quellen sammeln, um sie zu [überwachen und zu analysieren](../azure-monitor/agents/data-sources.md), sowie verschiedene [Datentypen für Analysen, Visualisierungen und Warnungen](../azure-monitor/data-platform.md). Sie können außerdem Warnungsregeln erstellen, um Probleme in Ihrer Umgebung wie hohe Ressourcennutzung, fehlende Patches, wenig Speicherplatz auf dem Datenträger und problematischer Takt Ihrer virtuellen Computer zu ermitteln. Sie können eine automatisierte Reaktion auf erkannte Ereignisse festlegen, indem Sie eine Warnung an die ITSM-Tools (IT-Service-Management) senden. Benachrichtigungen zu erkannten Warnungen können auch per E-Mail gesendet werden.

- **Azure Security Center** verstärkt die Sicherheit von Rechenzentren und bietet erweiterten Bedrohungsschutz über Hybrid-Workloads für die Cloud und die lokalen Umgebung. Es bewertet das Sicherheitsrisiko von Azure VMware Solution-VMs, löst bei Bedarf Warnungen aus und leitet sie zur Lösung an Azure Monitor weiter. Beispielsweise werden fehlende Betriebssystempatches, falsche Sicherheitskonfigurationen und [Endpunktschutz](../security-center/security-center-services.md) bewertet. Sie können Sicherheitsrichtlinien in [Microsoft Defender für Cloud](azure-security-integration.md) definieren.

- Die **Azure-Updateverwaltung** verwaltet Betriebssystemupdates für Ihre Windows- und Linux-Computer in einer Hybridumgebung in Azure Automation. Sie überwacht die Konformität von Patches und leitet Warnungen zu Abweichungen bei Patches zur Korrektur an Azure Monitor weiter. Die Azure-Updateverwaltung muss eine Verbindung mit Ihrem Log Analytics-Arbeitsbereich herstellen, um gespeicherte Daten zum Bewerten des Status von Updates auf Ihren VMs verwenden zu können.

- Der **Log Analytics-Arbeitsbereich** speichert Protokolldaten. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration zum Speichern von Daten. Sie können die virtuellen Azure VMware Solution-Computer über den Log Analytics-Agent überwachen. Computer, die mit dem Log Analytics-Arbeitsbereich verbunden sind, verwenden den [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md), um Daten zu Änderungen an installierter Software, Microsoft-Diensten, Windows-Registrierung und -Dateien sowie Linux-Daemons auf überwachten Servern zu sammeln. Wenn Daten verfügbar sind, sendet der Agent diese zur Verarbeitung an Azure Monitor-Protokolle. Von Azure Monitor-Protokollen wird Logik auf die empfangenen Daten angewendet, und die Daten werden aufgezeichnet und zu Analysezwecken verfügbar gemacht. Verwenden Sie Server mit Azure Arc-Unterstützung und [Unterstützung der VM-Erweiterung](../azure-arc/servers/manage-vm-extensions.md), um den Log Analytics-Agent bereitzustellen.

## <a name="benefits"></a>Vorteile

- Native Azure-Dienste können zum Verwalten Ihrer VMs in einer Hybridumgebung (Azure, Azure VMware Solution und lokal) verwendet werden.
- Integrierte Überwachung und Sichtbarkeit von VMs in Azure, Azure VMware Solution und an Ihrem lokalen Standort.
- Mit der Azure-Updateverwaltung in Azure Automation können Sie Betriebssystemupdates für Ihre Windows- und Linux-Computer verwalten.
- Microsoft Defender für Cloud bietet erweiterten Bedrohungsschutz, einschließlich:
  - Überwachung der Dateiintegrität
  - Dateilose Sicherheitswarnungen
  - Bewertung von Patches für Betriebssysteme
  - Bewertung von Sicherheitsfehlkonfigurationen
  - Bewertung von Endpoint Protection
- Stellen Sie den Log Analytics-Agent mithilfe der Unterstützung der VM-Erweiterung für Server mit Azure Arc-Unterstützung für neue und vorhandene virtuelle Computer einfach bereit.
- Ihr Log Analytics-Arbeitsbereich in Azure Monitor ermöglicht das Sammeln von Protokollen und Leistungsindikatoren über den Log Analytics-Agent oder Erweiterungen. Sammeln Sie Daten und Protokolle an einem einzigen Punkt, und präsentieren Sie diese Daten in verschiedenen nativen Azure-Diensten.
- Zu den Vorteilen Azure Monitor gehören:
  - Nahtlose Überwachung
  - Bessere Sichtbarkeit der Infrastruktur
  - Sofortige Benachrichtigungen
  - Automatische Behebung
  - Kosteneffizienz

## <a name="topology"></a>Topologie

Das Diagramm zeigt die integrierte Überwachungsarchitektur für Azure VMware Solution-VMs.

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="Diagramm: Architektur der integrierten Azure-Überwachung." border="false":::

Der Log Analytics-Agent ermöglicht die Sammlung von Protokolldaten aus Azure, Azure VMware Solution und lokalen VMs. Die erfassten Daten werden an Azure Monitor-Protokolle gesendet und in einem Log Analytics-Arbeitsbereich gespeichert. Sie können den Log Analytics-Agent mithilfe der [Unterstützung Arc-fähiger Server für VM-Erweiterungen](../azure-arc/servers/manage-vm-extensions.md) für neue und vorhandene VMs bereitstellen.

Nachdem der Log Analytics-Arbeitsbereich die Protokolle gesammelt hat, können Sie den Log Analytics-Arbeitsbereich mit Azure Security Center konfigurieren, um den Sicherheitsrisikostatus von Azure VMware Solution-VMs zu bewerten und eine Warnung für alle kritischen Sicherheitsrisiken auszulösen.  Beispielsweise werden fehlende Betriebssystempatches, falsche Sicherheitskonfigurationen und [Endpunktschutz](../security-center/security-center-services.md) bewertet.

Sie können den Log Analytics-Arbeitsbereich mit Azure Sentinel für Folgendes konfigurieren: Erkennen von Warnungen, Einblicke in Bedrohungen, Hunting und Reaktion auf Bedrohungen. Im obigen Diagramm ist Defender für Cloud über den Defender für Cloud-Connector mit Microsoft Sentinel verbunden. Azure Security Center leitet das Sicherheitsrisiko in der Umgebung an Azure Sentinel weiter, damit dort ein Incident erstellt wird und eine Zuordnung zu anderen Bedrohungen erfolgt. Sie können auch eine geplante Abfrageregel erstellen, um unerwünschte Aktivitäten zu erkennen und in Incidents umzuwandeln.

## <a name="before-you-start"></a>Vorbereitung

Wenn Sie neu in Azure sind oder mit einem der oben genannten Dienste nicht vertraut sind, lesen Sie die folgenden Artikel:

- [Übersicht über die Automation-Kontoauthentifizierung](../automation/automation-security-overview.md)
- [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../azure-monitor/logs/design-logs-deployment.md) und [Azure Monitor](../azure-monitor/overview.md)
- [Planung](../security-center/security-center-planning-and-operations-guide.md) und [Unterstützte Plattformen](../security-center/security-center-os-coverage.md) für Microsoft Defender für Cloud
- [Aktivieren von Azure Monitor für VMs: Übersicht](../azure-monitor/vm/vminsights-enable-overview.md)
- [Was sind Server mit Azure Arc-Aktivierung?](../azure-arc/servers/overview.md) und [Was ist Kubernetes mit Azure Arc-Aktivierung?](../azure-arc/kubernetes/overview.md)
- [Übersicht über die Updateverwaltung](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>Aktivieren der Azure-Updateverwaltung

Die [Azure-Updateverwaltung](../automation/update-management/overview.md) in Azure Automation verwaltet Betriebssystemupdates für Ihre Windows- und Linux-Computer in einer Hybridumgebung. Sie überwacht die Konformität von Patches und leitet Warnungen zu Abweichungen bei Patches zur Korrektur an Azure Monitor weiter. Die Azure-Updateverwaltung muss eine Verbindung mit Ihrem Log Analytics-Arbeitsbereich herstellen, um gespeicherte Daten zum Bewerten des Status von Updates auf Ihren VMs verwenden zu können.

1. Bevor Sie einen Log Analytics-Arbeitsbereich zur Azure-Updateverwaltung hinzufügen können, müssen Sie zuerst ein [Azure Automation-Konto erstellen](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >Sie können [mit einer ARM-Vorlage (Azure Resource Manager) ein Azure Automation-Konto erstellen](../automation/quickstart-create-automation-account-template.md). Bei Verwendung einer ARM-Vorlage werden im Vergleich zu anderen Bereitstellungsmethoden weniger Schritte benötigt.

1. [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/logs/quick-create-workspace.md). Wenn Sie es vorziehen, können Sie einen Arbeitsbereich auch über die [CLI](../azure-monitor/logs/resource-manager-workspace.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) oder eine [Azure Resource Manager-Vorlage](../azure-monitor/logs/resource-manager-workspace.md) erstellen.

1. [Aktivieren Sie die Updateverwaltung über ein Automation-Konto.](../automation/update-management/enable-from-automation-account.md) Dabei verknüpfen Sie Ihren Log Analytics-Arbeitsbereich mit Ihrem Automation-Konto. 
 
1. Nachdem Sie die Updateverwaltung aktiviert haben, können Sie [Updates auf virtuellen Computern bereitstellen und die Ergebnisse überprüfen](../automation/update-management/deploy-updates.md). 

## <a name="enable-microsoft-defender-for-cloud"></a>Aktivieren von Microsoft Defender für Cloud

Bewertet das Sicherheitsrisiko von Azure VMware Solution-VMs und gibt bei Bedarf Warnungen aus Diese Sicherheitswarnungen können zur Behebung an Azure Monitor weitergeleitet werden. Weitere Informationen finden Sie unter [Unterstützte Features für VMs](../security-center/security-center-services.md).

Defender für Cloud bietet zahlreiche Funktionen, einschließlich:

- Überwachung der Dateiintegrität
- Erkennung dateiloser Angriffe
- Bewertung von Patches für Betriebssysteme
- Bewertung von Sicherheitsfehlkonfigurationen
- Bewertung von Endpoint Protection

>[!NOTE]
>Microsoft Defender für Cloud ist ein vorkonfiguriertes Tool, das keine Bereitstellung erfordert, jedoch im Azure-Portal aktiviert werden muss.

1. [Hinzufügen von Azure VMware Solution-VMs zu Defender für Cloud](azure-security-integration.md#add-azure-vmware-solution-vms-to-defender-for-cloud).

2. [Aktivieren von Microsoft Defender für Cloud](../security-center/enable-azure-defender.md). Defender für Cloud wird die VMs hinsichtlich potenzieller Sicherheitsprobleme bewerten. Außerdem werden auf der Registerkarte „Übersicht“ [Sicherheitsempfehlungen](../security-center/security-center-recommendations.md) bereitgestellt.

3. [Festlegen von Sicherheitsrichtlinien](../security-center/tutorial-security-policy.md) in Defender für Cloud.

Weitere Informationen finden Sie unter [Integrieren von Microsoft Defender für Cloud in Azure VMware Solution](azure-security-integration.md).

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Durchführen des Onboardings für virtuelle Computer auf Servern mit Azure Arc-Unterstützung

Erweitern Sie die Azure-Verwaltung auf jede Infrastruktur, einschließlich Azure VMware Solution, der lokalen Infrastruktur oder anderer Cloudplattformen.  Informationen zum Aktivieren von Servern mit Azure Arc-Unterstützung für mehrere virtuelle Windows- oder Linux-Computer finden Sie unter [Verbinden von Hybridcomputern mit Azure im großen Stil](../azure-arc/servers/onboard-service-principal.md).



## <a name="onboard-hybrid-kubernetes-clusters-with-azure-arc-enabled-kubernetes"></a>Durchführen des Onboardings für Kubernetes-Hybridcluster mit Kubernetes mit Azure Arc-Unterstützung

Fügen Sie einen Kubernetes-Cluster, der in Ihrer Azure VMware Solution-Umgebung gehostet wird, mithilfe von Kubernetes mit Azure Arc-Unterstützung an. Weitere Informationen finden Sie unter [Erstellen eines Azure Arc-fähigen Onboardingdienstprinzipals (Vorschauversion)](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="deploy-the-log-analytics-agent"></a>Bereitstellen des Log Analytics-Agents

Sie können die Azure VMware Solution-VMs über den Log Analytics-Agent überwachen. Computer, die mit dem Log Analytics-Arbeitsbereich verbunden sind, verwenden den [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md), um Daten zu Änderungen an installierter Software, Microsoft-Diensten, Windows-Registrierung und -Dateien sowie Linux-Daemons auf überwachten Servern zu sammeln. Wenn Daten verfügbar sind, sendet der Agent diese zur Verarbeitung an Azure Monitor-Protokolle. Von Azure Monitor-Protokollen wird Logik auf die empfangenen Daten angewendet, und die Daten werden aufgezeichnet und zu Analysezwecken verfügbar gemacht.

Stellen Sie den Log Analytics-Agent mithilfe von [Servern mit Azure Arc-Unterstützung und Unterstützung der VM-Erweiterung](../azure-arc/servers/manage-vm-extensions.md) bereit.




## <a name="enable-azure-monitor"></a>Aktivieren von Azure Monitor

Sie können Daten aus verschiedenen Quellen sammeln, um sie zu [überwachen und zu analysieren](../azure-monitor/agents/data-sources.md), sowie verschiedene [Datentypen für Analysen, Visualisierungen und Warnungen](../azure-monitor/data-platform.md). Sie können außerdem Warnungsregeln erstellen, um Probleme in Ihrer Umgebung wie hohe Ressourcennutzung, fehlende Patches, wenig Speicherplatz auf dem Datenträger und problematischer Takt Ihrer virtuellen Computer zu ermitteln. Sie können eine automatisierte Reaktion auf erkannte Ereignisse festlegen, indem Sie eine Warnung an die ITSM-Tools (IT-Service-Management) senden. Benachrichtigungen zu erkannten Warnungen können auch per E-Mail gesendet werden.

Überwachen Sie die Leistung des Gastbetriebssystems, um Anwendungsabhängigkeiten für virtuelle Azure VMware Solution-Computer oder lokale virtuelle Computer zu erkennen und zuzuordnen. Ihr Log Analytics-Arbeitsbereich in Azure Monitor ermöglicht das Sammeln von Protokollen und Leistungsindikatoren über den Log Analytics-Agent oder Erweiterungen. 


1. [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../azure-monitor/logs/design-logs-deployment.md)

1. [Aktivieren von Azure Monitor für VMs: Übersicht](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs](../azure-monitor/vm/vminsights-configure-workspace.md)

1. Erstellen Sie Warnungsregeln, um Probleme in Ihrer Umgebung zu identifizieren:

   - [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../azure-monitor/alerts/alerts-metric.md)

   - [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/alerts/alerts-log.md)

   - [Aktionsregeln](../azure-monitor/alerts/alerts-action-rules.md) zum Festlegen automatisierter Aktionen und Benachrichtigungen

   - [Verbinden von Azure mit ITSM-Tools mithilfe des ITSM-Connectors](../azure-monitor/alerts/itsmc-overview.md)


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Azure VMware Solution Netzwerk- und Interkonnektivitätskonzepte behandelt haben, möchten Sie vielleicht mehr über [die Integration von Microsoft Defender für Cloud in Azure VMware Solution](azure-security-integration.md) erfahren.
