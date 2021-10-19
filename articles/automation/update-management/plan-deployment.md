---
title: Bereitstellungsplan der Azure Automation-Updateverwaltung
description: In diesem Artikel werden die Überlegungen und Entscheidungen beschrieben, die bei der Vorbereitung der Bereitstellung der Azure Automation-Updateverwaltung zu treffen sind.
services: automation
ms.subservice: update-management
ms.date: 09/28/2021
ms.topic: conceptual
ms.openlocfilehash: fcf7235ae071dece392a11bacbf9f750569380ae
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715308"
---
# <a name="plan-your-update-management-deployment"></a>Planen Ihrer Bereitstellung der Updateverwaltung

## <a name="step-1---automation-account"></a>Schritt 1: Automation-Konto

Die Updateverwaltung ist ein Azure Automation-Feature und erfordert daher ein Automation-Konto. Sie können ein vorhandenes Automation-Konto in Ihrem Abonnement verwenden oder ein neues Konto erstellen, das nur für die Updateverwaltung und keine anderen Automation-Features vorgesehen ist.

## <a name="step-2---azure-monitor-logs"></a>Schritt 2: Azure Monitor-Protokolle

Die Updateverwaltung hängt von einem Log Analytics-Arbeitsbereich in Azure Monitor ab, um die von verwalteten Computern gesammelten Bewertungs- und Aktualisierungsstatus-Protokolldaten zu speichern. Die Integration in Log Analytics ermöglicht auch detaillierte Analysen und Warnungen in Azure Monitor. Sie können einen vorhandenen Arbeitsbereich in Ihrem Abonnement verwenden oder einen neuen, dedizierten Arbeitsbereich nur für die Updateverwaltung erstellen.

Wenn Sie noch keine Erfahrung mit Azure Monitor-Protokollen und dem Log Analytics-Arbeitsbereich haben, lesen Sie den Bereitstellungsleitfaden [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../../azure-monitor/logs/design-logs-deployment.md). 

## <a name="step-3---supported-operating-systems"></a>Schritt 3: Unterstützte Betriebssysteme

Die Updateverwaltung unterstützt bestimmte Versionen der Windows Server- und Linux-Betriebssysteme. Bevor Sie die Updateverwaltung aktivieren, vergewissern Sie sich, dass die Zielcomputer die [Betriebssystemanforderungen](operating-system-requirements.md) erfüllen. 

## <a name="step-4---log-analytics-agent"></a>Schritt 4: Log Analytics-Agent

Der [Log Analytics-Agent](../../azure-monitor/agents/log-analytics-agent.md) für Windows und Linux ist erforderlich, um die Updateverwaltung zu unterstützen. Der Agent wird sowohl für die Datensammlung als auch für die Automation-Systemrolle Hybrid Runbook Worker verwendet, um Updateverwaltung-Runbooks zu unterstützen, die zum Verwalten der Bewertungs- und Updatebereitstellungen auf dem Computer verwendet werden. 

Wenn der Log Analytics-Agent auf Azure-VMs noch nicht installiert ist, wird er automatisch mithilfe der Log Analytics-VM-Erweiterung für [Windows](../../virtual-machines/extensions/oms-windows.md) oder [Linux](../../virtual-machines/extensions/oms-linux.md) installiert, wenn Sie Updateverwaltung für den virtuellen Computer aktivieren. Der Agent ist so konfiguriert, dass er Berichte an den Log Analytics-Arbeitsbereich sendet, der mit dem Automation-Konto verknüpft ist, in dem die Updateverwaltung aktiviert ist.

Für Nicht-Azure-VMs oder -Server muss der Log Analytics-Agent für Windows oder Linux installiert sein und Berichte an den verknüpften Arbeitsbereich senden. Sie sollten den Log Analytics-Agent für Windows oder Linux installieren, indem Sie zuerst Ihren Computer mit [Servern mit Azure Arc-Unterstützung](../../azure-arc/servers/overview.md) verbinden und dann mit Azure Policy die integrierte Richtliniendefinition [Log Analytics-Agent für Linux-/Windows-Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuweisen. Wenn Sie alternativ die Überwachung der Computer mit [VM Insights](../../azure-monitor/vm/vminsights-overview.md) planen, verwenden Sie stattdessen die Initiative [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Wenn Sie einen Computer aktivieren, der aktuell von Operations Manager verwaltet wird, ist kein neuer Agent erforderlich. Die Arbeitsbereichsinformationen werden der Agent-Konfiguration hinzugefügt, wenn Sie die Verwaltungsgruppe mit dem Log Analytics-Arbeitsbereich verbinden.

Die Registrierung eines Computers für die Updateverwaltung in mehreren Log Analytics-Arbeitsbereichen (auch als „Multi-Homing“ bezeichnet) wird nicht unterstützt.

## <a name="step-5---network-planning"></a><a name="ports"></a> Schritt 5: Netzwerkplanung

Um Ihr Netzwerk für die Unterstützung der Updateverwaltung vorzubereiten, müssen Sie möglicherweise einige Infrastrukturkomponenten konfigurieren. Öffnen Sie beispielsweise Firewallports, um die von Updateverwaltung und Azure Monitor verwendete Kommunikation zu übergeben.

Überprüfen Sie die [Azure Automation-Netzwerkkonfiguration](../automation-network-configuration.md), um ausführliche Informationen zu den Ports, URLs und anderen Netzwerkdetails zu erhalten, die für die Updateverwaltung erforderlich sind, inklusive der Rolle Hybrid Runbook Worker. Wenn Sie von Ihren virtuellen Azure-Computern aus sicher und privat eine Verbindung mit dem Automation-Dienst herstellen möchten, lesen Sie [Verwenden von Azure Private Link](../how-to/private-link-security.md). 

Bei Windows-Computern müssen Sie auch Datenverkehr zu allen Endpunkten zulassen, die für den Windows Update-Agent erforderlich sind. Sie finden eine aktualisierte Liste der erforderlichen Endpunkte unter [Probleme im Zusammenhang mit HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Wenn Sie über eine lokale Bereitstellung der [Windows Server Update Services](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) (WSUS) verfügen, müssen Sie auch Datenverkehr zu dem in Ihrem [WSUS-Schlüssel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) angegebenen Server zulassen.

Informationen zu den erforderlichen Endpunkten für Red Hat Linux-Computer finden Sie unter [IP-Adressen der Server für die RHUI-Inhaltsübermittlung](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers). Informationen zu anderen Linux-Distributionen finden Sie in der jeweiligen Dokumentation des Anbieters.

Wenn Computer im Netzwerk aufgrund von IT-Sicherheitsrichtlinien keine Internetverbindung herstellen können, können Sie ein [Log Analytics-Gateway](../../azure-monitor/agents/gateway.md) einrichten und den Computer so konfigurieren, dass er die Verbindung mit Azure Automation und Azure Monitor über das Gateway herstellt.

## <a name="step-6---permissions"></a>Schritt 6: Berechtigungen

Zum Erstellen und Verwalten von Updatebereitstellungen benötigen Sie bestimmte Berechtigungen. Weitere Informationen zu diesen Berechtigungen finden Sie unter [Rollenbasierter Zugriff: Updateverwaltung](../automation-role-based-access-control.md#update-management-permissions).

## <a name="step-7---windows-update-agent"></a>Schritt 7: Windows Update-Client

Die Azure Automation-Updateverwaltung verwendet den Windows Update-Client, um Windows-Updates herunterzuladen und zu installieren. Vom Windows Update Agent (WUA) werden bestimmte Gruppenrichtlinieneinstellungen auf Computern verwendet, um eine Verbindung mit Windows Server Update Services (WSUS) oder Microsoft Update herzustellen. Diese Gruppenrichtlinieneinstellungen dienen auch dazu, die Softwareupdatekompatibilität zu prüfen und die Softwareupdates automatisch zu aktualisieren. Informationen zu unseren Empfehlungen finden Sie unter [Konfigurieren von Windows Update-Einstellungen für die Azure Automation-Updateverwaltung](configure-wuagent.md).

## <a name="step-8---linux-repository"></a>Schritt 8: Linux-Repository

Virtuelle Computer, die auf der Grundlage der über Azure Marketplace erhältlichen On-Demand-RHEL-Images (Red Hat Enterprise Linux) erstellt werden, werden für den Zugriff auf die in Azure bereitgestellte Red Hat-Updateinfrastruktur (RHUI) registriert. Alle anderen Linux-Distributionen müssen über das jeweilige Onlinedateirepository der Distributionen mithilfe der von der jeweiligen Distribution unterstützten Methoden aktualisiert werden.

Sie müssen das Plug-In „yum-security“ installieren, um Updates in Version 6 von Red Hat Enterprise zu klassifizieren. Unter Red Hat Enterprise Linux 7 ist das Plug-In bereits Teil von yum selbst, und Sie müssen also nichts installieren. Weitere Informationen finden Sie im folgenden [Knowledge-Artikel](https://access.redhat.com/solutions/10021) zu Red Hat.

## <a name="step-9---plan-deployment-targets"></a>Schritt 9: Planen von Bereitstellungszielen

Mit der Updateverwaltung können Sie Updates auf eine dynamische Gruppe ausrichten, die Azure- oder Nicht-Azure-Computer repräsentiert, sodass Sie sicherstellen können, dass bestimmte Computer immer die richtigen Updates zu den passendsten Zeiten erhalten. Eine dynamische Gruppe wird zur Bereitstellungszeit aufgelöst und basiert auf den folgenden Kriterien:

* Subscription
* Ressourcengruppen
* Standorte
* `Tags` 

Für nicht zu Azure gehörende Computer verwendet eine dynamische Gruppe gespeicherte Suchen, die auch als [Computergruppen](../../azure-monitor/logs/computer-groups.md) bezeichnet werden. Updatebereitstellungen, die sich auf eine Gruppe von Computern beziehen, sind nur über das Automation-Konto in der Option **Bereitstellungszeitpläne** der Updateverwaltung sichtbar, nicht über eine bestimmte Azure-VM.

Alternativ können Updates ausschließlich für einen ausgewählten virtuellen Azure-Computer verwaltet werden. Updatebereitstellungen, die sich auf den jeweiligen Computer beziehen, werden sowohl auf dem Computer als auch über das Automation-Konto in der Option **Bereitstellungszeitpläne** der Updateverwaltung angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie die Updateverwaltung, und wählen Sie Computer aus, die mit einer der folgenden Methoden verwaltet werden sollen:

- Verwendung einer [Azure Resource Manager-Vorlage](enable-from-template.md) für die Bereitstellung der Updateverwaltung in einem neuen oder vorhandenen Automation-Konto und Azure Monitor Log Analytics-Arbeitsbereich in Ihrem Abonnement. Sie konfiguriert nicht den Bereich der Computer, die verwaltet werden sollen. Dies erfolgt als gesonderter Schritt nach der Verwendung der Vorlage.

- Über Ihr [Automation-Konto](enable-from-automation-account.md) für einen oder mehrere Azure- und Nicht-Azure-Computer, einschließlich Azure Arc-fähiger Server.

- Verwenden des [Runbooks](enable-from-runbook.md) **Enable-AutomationSolution** zum Automatisieren des Onboardings von Azure-VMs.

- Für eine [ausgewählte Azure-VM](enable-from-vm.md) über die Seite **Virtuelle Computer** im Azure-Portal. Dieses Szenario steht für virtuelle Computer unter Linux oder Windows zur Verfügung.

- Für [mehrere virtuelle Azure-Computer](enable-from-portal.md), indem Sie sie auf der Seite **Virtuelle Computer** im Azure-Portal auswählen.
