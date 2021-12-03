---
title: Planen und Bereitstellen von Servern mit Azure Arc-Unterstützung
description: Erfahren Sie, wie Sie eine große Anzahl von Computern für Server mit Azure Arc-Unterstützung aktivieren, um die Konfiguration wichtiger Sicherheits-, Verwaltungs- und Überwachungsfunktionen in Azure zu vereinfachen.
ms.date: 08/27/2021
ms.topic: conceptual
ms.openlocfilehash: b32090caf8167874e61bbca0c5f3782557abd620
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340285"
---
# <a name="plan-and-deploy-azure-arc-enabled-servers"></a>Planen und Bereitstellen von Servern mit Azure Arc-Unterstützung

Die Bereitstellung eines IT-Infrastrukturdiensts oder einer Geschäftsanwendung ist für jedes Unternehmen eine Herausforderung. Damit dies erfolgreich umgesetzt werden kann und dabei keine unangenehmen Überraschungen und unerwartete Kosten auftreten, müssen Sie gründlich planen, sodass Sie bestmöglich vorbereitet sind. Um die Bereitstellung von Servern mit Azure Arc-Unterstützung jeder Größenordnung zu planen, sollten Sie die Entwurfs- und Bereitstellungskriterien abdecken, die erfüllt sein müssen, damit die Aufgaben erfolgreich abgeschlossen werden können.

Damit die Bereitstellung problemlos fortgesetzt werden kann, sollte Ihr Plan folgende Punkte deutlich hervorheben:

* Rollen und Zuständigkeiten.
* Inventar physischer Server oder virtueller Computer, um zu überprüfen, ob sie die Netzwerk- und Systemanforderungen erfüllen.
* Die Fertigkeiten und Schulungen, die erforderlich sind, um eine erfolgreiche Bereitstellung und laufende Verwaltung zu ermöglichen.
* Akzeptanzkriterien und wie Sie den Erfolg verfolgen.
* Tools oder Methoden, die zur Automatisierung der Bereitstellungen verwendet werden sollen.
* Identifizierte Risiken und Entschärfungspläne, um Verzögerungen, Unterbrechungen usw. zu vermeiden.
* Vermeiden von Störungen während der Bereitstellung.
* Was ist der Eskalationspfad, wenn ein erhebliches Problem auftritt?

Dieser Artikel soll sicherstellen, dass Sie für eine erfolgreiche Bereitstellung von Servern mit Azure Arc-Unterstützung auf mehreren physischen Produktionsservern oder virtuellen Computern in Ihrer Umgebung vorbereitet sind.

Weitere Informationen zu unseren Empfehlungen für die Bereitstellung in großem Umfang finden Sie in diesem Video.

> [!VIDEO https://www.youtube.com/embed/Cf1jUPOB_vs]

## <a name="prerequisites"></a>Voraussetzungen

* Auf Ihren Computern wird ein [unterstütztes Betriebssystem](agent-overview.md#supported-operating-systems) für den Connected Machine-Agent ausgeführt.
* Ihre Computer sind von Ihrem lokalen Netzwerk oder einer anderen Cloudumgebung aus entweder direkt oder über einen Proxyserver mit Ressourcen in Azure verbunden.
* Zum Installieren und Konfigurieren des Connected Machine-Agents der Server mit Azure Arc-Unterstützung ist ein Konto mit erhöhten Rechten (d. h. Administrator oder Root-Benutzer) auf den Computern vorhanden.
* Zum Durchführen des Onboardings von Computern müssen Sie der Rolle **Onboarding verbundener Azure-Computer** angehören.
* Zum Lesen, Ändern oder Löschen eines Computers müssen Sie der Rolle **Ressourcenadministrator für Azure Connected Machine** angehören.

## <a name="pilot"></a>Pilotphase

Vor der Bereitstellung auf allen Produktionscomputern sollten Sie diesen Bereitstellungsprozess zunächst auswerten, bevor Sie ihn in Ihrer Umgebung umfassend anwenden. Identifizieren Sie für eine Pilotphase eine repräsentative Stichprobe von Computern, von denen nicht die Fähigkeit Ihres Unternehmens abhängt, den Geschäftsbetrieb aufrechtzuerhalten. Sie sollten sicher sein, dass Sie genügend Zeit für die Durchführung der Pilotphase und die Bewertung seiner Auswirkungen haben: Wir empfehlen mindestens 30 Tage.

Richten Sie einen formalen Plan ein, der den Umfang und die Details der Pilotphase beschreibt. Im folgenden Beispiel erfahren Sie, was ein Plan enthalten sollte, um Ihnen den Einstieg zu erleichtern.

* Ziele: Beschreibt die geschäftlichen und technischen Anstöße, die zu der Entscheidung geführt haben, dass eine Pilotphase notwendig ist.
* Auswahlkriterien: Gibt die Kriterien zur Auswahl der Aspekte der Lösung an, die eine Pilotphase zeigen sollte.
* Umfang: Beschreibt den Umfang der Pilotphase, der die Lösungskomponenten, den erwarteten Zeitplan, die Dauer der Pilotphase und die Anzahl der Zielcomputer umfasst, aber nicht darauf beschränkt ist.
* Erfolgskriterien und Metriken: Definiert die Erfolgskriterien der Pilotphase und bestimmte Measures, die zum Ermitteln des Erfolgsniveaus verwendet werden.
* Schulungsplan: Beschreibt den Plan für die Schulung von System Engineers, Administratoren usw., die im Rahmen der Pilotphase neu bei Azure und Azure-Diensten sind.
* Übergangsplan: Beschreibt die Strategie und die Kriterien, die für den Übergang von der Pilotphase zur Produktion verwendet werden.
* Rollback: Beschreibt die Verfahren für das Rollback einer Pilotphase in den Zustand vor der Bereitstellung.
* Risiken: Listet alle identifizierten Risiken für die Durchführung der Pilotphase und in Verbindung mit der Produktionsbereitstellung auf.

## <a name="phase-1-build-a-foundation"></a>Phase 1: Erstellen eines Fundaments

In dieser Phase aktivieren System Engineers oder Administratoren die Kernfunktionen in den Azure-Abonnements ihrer Organisationen, um die Grundlage zu schaffen, bevor sie ihre Computer für die Verwaltung durch Server mit Azure Arc-Unterstützung und andere Azure-Dienste aktivieren.

|Aufgabe |Detail |Duration |
|-----|-------|---------|
| [Erstellen einer Ressourcengruppe](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Eine dedizierte Ressourcengruppe, um nur Server mit Azure Arc-Unterstützung einzubeziehen und die Verwaltung und Überwachung dieser Ressourcen zu zentralisieren. | Eine Stunde |
| Anwenden von [Tags](../../azure-resource-manager/management/tag-resources.md) zur Unterstützung der Organisation von Computern. | Bewerten und Entwickeln einer auf die IT ausgerichteten [Markierungsstrategie](/azure/cloud-adoption-framework/decision-guides/resource-tagging/), mit der die Komplexität der Verwaltung Ihrer Server mit Azure Arc-Unterstützung reduziert und Verwaltungsentscheidungen vereinfacht werden können. | Ein Tag |
| Entwerfen und Bereitstellen von [Azure Monitor-Protokollen](../../azure-monitor/logs/data-platform-logs.md) | Bewerten von [Entwurfs- und Bereitstellungsüberlegungen](../../azure-monitor/logs/design-logs-deployment.md), um zu bestimmen, ob Ihre Organisation einen vorhandenen Log Analytics-Arbeitsbereich verwenden oder einen anderen implementieren sollte, um gesammelte Protokolldaten von Hybridservern und -computern zu speichern.<sup>1</sup> | Ein Tag |
| [Entwickeln eines Azure Policy](../../governance/policy/overview.md)-Governanceplans | Bestimmen Sie, wie Sie die Governance von Hybridservern und -computern im Abonnement- oder Ressourcengruppenumfang mit Azure Policy implementieren. | Ein Tag |
| Konfigurieren der [rollenbasierten Zugriffssteuerung](../../role-based-access-control/overview.md) (Role Based Access Control, RBAC) | Entwickeln Sie einen Zugriffsplan, um zu steuern, wer Zugriff auf die Verwaltung von Servern mit Azure Arc-Unterstützung hat und wer Daten aus anderen Azure-Diensten und -Lösungen anzeigen kann. | Ein Tag |
| Identifizieren von Computern mit bereits installiertem Log Analytics-Agent | Führen Sie die folgende Protokollabfrage in [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) durch, um die Konvertierung vorhandener Log Analytics-Agent-Bereitstellungen in einem von der Erweiterung verwalteten Agent zu unterstützen:<br> Heartbeat <br> &#124;, wobei TimeGenerated > ago(30d) <br> &#124;, wobei ResourceType == "machines" und (ComputerEnvironment == "Non-Azure") <br> &#124; zusammenfassen nach Computer, ResourceProvider, ResourceType, Computer Environment | Eine Stunde |

<sup>1</sup> Ein wichtiger Aspekt bei der Bewertung des Entwurfs Ihres Log Analytics-Arbeitsbereichs ist die Integration in Azure Automation zur Unterstützung der Funktionen „Updateverwaltung“ und „Änderungsnachverfolgung und Bestand“ sowie Microsoft Defender für Cloud und Microsoft Sentinel. Wenn Ihre Organisation bereits über ein Automation-Konto verfügt und dessen Verwaltungsfeatures aktiviert hat, die mit einem Log Analytics-Arbeitsbereich verknüpft sind, bewerten Sie, ob Sie Verwaltungsvorgänge zentralisieren und optimieren und die Kosten minimieren können, indem Sie diese vorhandenen Ressourcen nutzen, um zum Vergleich ein doppeltes Konto, einen doppelten Arbeitsbereich usw. zu erstellen.

## <a name="phase-2-deploy-azure-arc-enabled-servers"></a>Phase 2: Bereitstellen von Servern mit Azure Arc-Unterstützung

Als Nächstes ergänzen Sie die in Phase 1 gelegte Grundlage, indem Sie den Connected Computer-Agent für die Server mit Azure Arc-Unterstützung vorbereiten und bereitstellen.

|Aufgabe |Detail |Duration |
|-----|-------|---------|
| Herunterladen des vordefinierten Installationsskripts | Überprüfen Sie das vordefinierte Installationsskript für die Bereitstellung des Connected Machine-Agents im großen Umfang, um Ihre Anforderungen der automatischen Bereitstellung zu unterstützen, und passen Sie es an.<br><br> Beispiel für das Onboarding von Ressourcen im großen Umfang:<br><br> <ul><li> [Grundlegendes Skript für die Bereitstellung im großen Umfang](onboard-service-principal.md)</ul></li> <ul><li>[Onboarding von VMware-vSphere-Windows Server-VMs im großen Umfang](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Onboarding von VMware-vSphere-Linux-VMs im großen Umfang](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Onboarding von AWS EC2-Instanzen mithilfe von Ansible im großen Umfang](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Bereitstellung im großen Umfang mit PowerShell-Remoting](./onboard-powershell.md) (nur Windows)</ul></li>| Ein oder mehrere Tage, abhängig von den Anforderungen, Organisationsprozessen (z. B. Änderungs- und Release Management) und verwendeter Automatisierungsmethode. |
| [Erstellen eines Dienstprinzipals](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Erstellen Sie einen Dienstprinzipal, um Computer mit Azure PowerShell oder über das Portal nicht-interaktiv zu verbinden.| Eine Stunde |
| Bereitstellen des Connected Machine-Agents für Ihre Zielserver und -computer |Stellen Sie die Skripts mit dem Automatisierungstool auf Ihren Servern bereit, und stellen Sie deren Verbindung mit Azure her.| Ein oder mehrere Tage, abhängig von Ihrem Freigabeplan und je nachdem, ob ein Rollout in Phasen stattfindet. |

## <a name="phase-3-manage-and-operate"></a>Phase 3: Verwaltung und Betrieb

In Phase 3 aktivieren Administratoren oder System Engineers die Automatisierung manueller Aufgaben für Verwaltung und Betrieb des Connected Machine-Agents und des Computers während ihres Lebenszyklus.

|Aufgabe |Detail |Duration |
|-----|-------|---------|
|Erstellen einer Resource Health-Warnung |Wenn ein Server länger als 15 Minuten keine Heartbeats an Azure sendet, kann dies bedeuten, dass er offline ist, die Netzwerkverbindung blockiert wurde oder der Agent nicht ausgeführt wird. Entwickeln Sie einen Plan, wie Sie auf diese Vorfälle reagieren und sie untersuchen, und verwenden Sie [Resource Health-Warnungen](../..//service-health/resource-health-alert-monitor-guide.md), um benachrichtigt zu werden, wenn sie beginnen.<br><br> Geben Sie Folgendes an, wenn Sie die Warnung konfigurieren:<br> **Ressourcentyp** = **Server mit Azure Arc-Unterstützung**<br> **Aktueller Ressourcenstatus** = **Nicht verfügbar**<br> **Vorheriger Ressourcenstatus** = **Verfügbar** | Eine Stunde |
|Erstellen einer Azure Advisor-Warnung | Für einen optimalen Betrieb und um sicherzustellen, dass die aktuellsten Sicherheits- und Fehlerfixes wirksam sind, sollten Sie den Agent der Server mit Azure Arc-Unterstützung stets auf dem neuesten Stand zu halten. Veraltete Agents werden mit einer [Azure Advisor-Warnung](../../advisor/advisor-alerts-portal.md) identifiziert.<br><br> Geben Sie Folgendes an, wenn Sie die Warnung konfigurieren:<br> **Empfehlungstyp** = **Upgrade auf die aktuelle Version des Azure Connected Machine-Agents** | Eine Stunde |
|[Weisen Sie Azure-Richtlinien](../../governance/policy/assign-policy-portal.md) Ihrem Abonnement- oder Ressourcengruppenbereich zu. |Weisen Sie dem Abonnement- oder Ressourcengruppenbereich die [Richtlinie](../../azure-monitor/vm/vminsights-enable-policy.md) **Azure Monitor für VMs aktivieren** zu (und andere, die Ihren Anforderungen entsprechen). Mit Azure Policy können Sie Richtliniendefinitionen zuweisen, die die erforderlichen Agents für VM-Erkenntnisse in Ihrer gesamten Umgebung installieren.| Varies |
|[Aktivieren der Updateverwaltung für Ihre Server mit Azure Arc-Unterstützung](../../automation/update-management/enable-from-automation-account.md) |Konfigurieren Sie die Updateverwaltung in Azure Automation zur Verwaltung von Betriebssystemupdates für Ihre Windows- und Linux-VMs, die bei Servern mit Azure Arc-Unterstützung registriert sind. | 15 Minuten |

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

* Erfahren Sie, wie Sie die Bereitstellung mit anderen Azure-Diensten wie Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) und anderen unterstützten [Azure-VM-Erweiterungen](manage-vm-extensions.md) vereinfachen können.
