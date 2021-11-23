---
title: 'Server mit Azure Arc-Unterstützung: Übersicht'
description: Hier erfahren Sie, wie Sie außerhalb von Azure gehostete Server mithilfe von Servern mit Azure Arc-Unterstützung wie eine Azure-Ressource verwalten.
ms.date: 09/30/2021
ms.topic: overview
ms.openlocfilehash: c390cbbb6f08f4f9082b0764125ab9a14407de95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287265"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Was sind Server mit Azure Arc-Unterstützung?

Server mit Azure Arc-Unterstützung bieten die Möglichkeit, physische Server und virtuelle Computer unter Windows und Linux zu verwalten, die *außerhalb* von Azure in Ihrem Unternehmensnetzwerk oder bei einem anderen Cloudanbieter gehostet werden. Diese Verwaltungsfunktion ist so konzipiert, dass sie mit der Verwaltung nativer virtueller Azure-Computer konsistent ist. Wenn ein Hybridcomputer mit Azure verbunden wird, wird er zu einem verbundenen Computer und in Azure wie eine Ressource behandelt. Jeder verbundene Computer verfügt über eine Ressourcen-ID, mit der der Computer in eine Ressourcengruppe aufgenommen werden kann. Nun können Sie von Azure-Standardkonstrukten profitieren, z. B. Azure Policy und der Anwendung von Tags. Dienstanbieter, die die lokale Infrastruktur eines Kunden verwalten, können ihre Hybridcomputer genau wie heute mit [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) mit nativen Azure-Ressourcen über mehrere Kundenumgebungen hinweg verwalten.

Um diese Erfahrung mit Ihren Hybridcomputern bereitzustellen, müssen Sie den Azure Connected Machine-Agent auf jedem Computer installieren. Dieser Agent bietet keine weiteren Funktionen und ist kein Ersatz für den [Log Analytics-Agent](../../azure-monitor/agents/log-analytics-agent.md) von Azure. Der Log Analytics-Agent für Windows und Linux ist in folgenden Fällen erforderlich:

* Sie möchten das Betriebssystem und die Workloads, die auf dem Computer ausgeführt werden, proaktiv überwachen,
* Sie möchten es mithilfe von Automation-Runbooks oder Lösungen wie Updateverwaltung verwalten.
* Verwenden Sie andere Azure-Dienste wie [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md).

## <a name="supported-cloud-operations"></a>Unterstützte Cloudvorgänge 

Wenn Sie Ihren Computer mit Servern mit Azure Arc-Unterstützung verbinden, können Sie dadurch die folgenden Vorgangsfunktionen ausführen, wie in der folgenden Tabelle beschrieben.

|Vorgangsfunktion |BESCHREIBUNG | 
|--------------------|------------|
|**Steuern** ||
| Azure Policy |Zuweisen von [Azure Policy-Gastkonfigurationen](../../governance/policy/concepts/guest-configuration.md) zu Überwachungseinstellungen innerhalb des Computers. Informationen zu den Kosten der Nutzung von Azure Policy-Gastkonfigurationsrichtlinien mit Servern mit Azure-Arc-Unterstützung finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/azure-policy/).|
|**Schützen** ||
| Microsoft Defender für Cloud | Schützen Sie Azure-fremde Server mit [Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint), das über [Microsoft Defender für Cloud](../../security-center/defender-for-servers-introduction.md) enthalten ist, um auch auf diese die Bedrohungserkennung, das Sicherheitsrisikomanagement und die proaktive Überwachung auf potenzielle Sicherheitsbedrohungen anzuwenden. Microsoft Defender für Cloud zeigt die Warnungen und Korrekturvorschläge aus den erkannten Bedrohungen an. |
| Microsoft Sentinel | Computer, die mit Servern mit Arc-Unterstützung verbunden sind, können [mit Microsoft Sentinel konfiguriert](scenario-onboard-azure-sentinel.md) werden, um sicherheitsbezogene Ereignisse zu sammeln und sie mit anderen Datenquellen zu korrelieren. |
|**Konfigurieren** ||
| Azure-Automatisierung |Automatisieren Sie häufige und zeitaufwändige Verwaltungsaufgaben mithilfe von PowerShell und Python-[Runbooks](../../automation/automation-runbook-execution.md).<br> Bewerten von Konfigurationsänderungen bei installierter Software, Microsoft-Diensten, Windows-Registrierung und -Dateien und Linux-Daemons mittels [Änderungsnachverfolgung und Bestand](../../automation/change-tracking/overview.md).<br> Verwalten Sie für die Verwaltung von Betriebssystemupdates für Ihre Windows- und Linux-Server die [Updateverwaltung](../../automation/update-management/overview.md). |
| Azure Automanage (Vorschau) | Automatisieren Sie das Onboarding und die Konfiguration einer Reihe von Azure-Diensten, wenn Sie [Automanage für Server mit Arc-Unterstützung](../../automanage/automanage-arc.md) verwenden. |
| VM-Erweiterungen | Stellt Konfigurations- und Automatisierungsaufgaben nach der Bereitstellung mittels unterstützter [VM-Erweiterungen von Servern mit Arc-Unterstützung](manage-vm-extensions.md) für Ihren Nicht-Azure-Windows- oder Nicht-Linux-Computer zur Verfügung. |
|**Überwachen**|
| Azure Monitor | Überwachen Sie die Leistung des Gastbetriebssystems des verbundenen Computers, und ermitteln Sie Anwendungskomponenten, um deren Prozesse und Abhängigkeiten mit anderen Ressourcen über [Azure Insights](../../azure-monitor/vm/vminsights-overview.md) zu überwachen. Sammeln Sie andere Protokolldaten, z. B. Leistungsdaten und Ereignisse, vom Betriebssystem oder von Workloads, die auf dem Computer mit dem [Log Analytics-Agent](../../azure-monitor/agents/agents-overview.md#log-analytics-agent) ausgeführt werden. Diese Daten werden in einem [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/design-logs-deployment.md) gespeichert. |

> [!NOTE]
> Zum aktuellen Zeitpunkt wird das Aktivieren der Updateverwaltung von Azure Automation direkt von einem Server mit Azure Arc-Unterstützung aus nicht unterstützt. Informationen zu den Anforderungen und zum Aktivieren für Ihren Server finden Sie unter [Aktivieren der Updateverwaltung aus Ihrem Automation-Konto](../../automation/update-management/enable-from-automation-account.md).

Erfasste und in einem Log Analytics-Arbeitsbereich gespeicherte Protokolldaten des Hybridcomputers enthalten jetzt computerspezifische Eigenschaften wie etwa eine Ressourcen-ID, um den Protokollzugriff mit [resource-context](../../azure-monitor/logs/design-logs-deployment.md#access-mode) (Ressourcenkontext) zuzulassen.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

Weitere Informationen dazu, wie Server mit Azure Arc-Unterstützung verwendet werden können, um Überwachungs-, Sicherheits- und Updatedienste von Azure in Hybrid- und Multicloudumgebungen zu implementieren, finden Sie im folgenden Video.

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>Unterstützte Regionen

Eine definitive Liste der unterstützten Regionen mit Servern mit Azure Arc-Unterstützung finden Sie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

In den meisten Fällen sollte der Standort, den Sie beim Erstellen des Installationsskripts auswählen, die Azure-Region sein, die dem Standort Ihres Computers geografisch am nächsten ist. Ruhende Daten werden innerhalb der geografischen Azure-Region gespeichert, in der sich die von Ihnen angegebene Region befindet, was ggf. auch Auswirkungen auf die Wahl der Region hat, wenn Data Residency-Anforderungen erfüllt werden müssen. Wenn die Azure-Region, mit der Ihr Computer eine Verbindung herstellt, von einem Ausfall betroffen ist, wird der verbundene Computer zwar nicht beeinträchtigt, es kann jedoch sein, dass Verwaltungsvorgänge mit Azure nicht abgeschlossen werden können. Wenn Sie über mehrere Standorte verfügen, die einen geografisch redundanten Dienst unterstützen, empfiehlt es sich im Falle eines regionalen Ausfalls, die Computer an den einzelnen Standorten jeweils mit einer anderen Azure-Region zu verbinden.

Die folgenden Metadateninformationen zum verbundenen Computer werden gesammelt und in der Region gespeichert, in der die Azure Arc-Computerressource konfiguriert ist:

- Betriebssystemname und -version
- Computername
- Vollqualifizierter Domänenname (FQDN) des Computers
- Version des Connected Machine-Agents

Wenn der Computer beispielsweise mit Azure Arc in der Region „USA, Osten“ registriert ist, werden diese Daten in der Region „USA“ gespeichert.

## <a name="supported-environments"></a>Unterstützte Umgebungen

Server mit Azure Arc-Unterstützung unterstützen die Verwaltung physischer Server und virtueller Computer, die *außerhalb* von Azure gehostet werden. Spezifische Informationen dazu, welche Hybrid Cloud-Umgebungen, die virtuelle Computer hosten, unterstützt werden, finden Sie unter [Voraussetzungen für den Connected Machine-Agent](agent-overview.md#supported-environments).

> [!NOTE]
> Server mit Azure Arc-Unterstützung sind nicht für die Verwaltung in Azure ausgeführter virtueller Computer vorgesehen, und dies wird auch nicht unterstützt.

## <a name="agent-status"></a>Agent-Status

Der Connected Machine-Agent sendet alle fünf Minuten eine reguläre Heartbeatnachricht an den Dienst. Wenn der Dienst keine solchen Heartbeatnachrichten mehr von einem Computer empfängt, wird dieser Computer als offline betrachtet, und der Status wird im Portal innerhalb von 15 bis 30 Minuten automatisch in **Getrennt** geändert. Wenn wieder eine Heartbeatnachricht des Connected Machine-Agents empfangen wird, ändert sich der Status automatisch in **Verbunden**.

## <a name="service-limits"></a>Diensteinschränkungen

Bei Servern mit Azure Arc-Unterstützung gilt ein Grenzwert für die Anzahl von Instanzen, die in jeder Ressourcengruppe erstellt werden können. Es gelten keine Grenzwerte auf Abonnement- oder Dienstebene. Informationen zu den Grenzwerten für Ressourcentypen finden Sie im Artikel [Ressourcen ohne Beschränkung auf 800 Instanzen pro Ressourcengruppe](../../azure-resource-manager/management/resources-without-resource-group-limit.md#microsofthybridcompute).

## <a name="next-steps"></a>Nächste Schritte

* Bevor Sie Server mit Azure Arc-Unterstützung auf mehreren Hybridcomputern auswerten oder aktivieren, lesen Sie [Übersicht über den Connected Machine-Agent](agent-overview.md), um die Anforderungen und technischen Details zum Agent und Bereitstellungsmethoden zu verstehen.

* Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.
