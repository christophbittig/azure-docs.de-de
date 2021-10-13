---
title: Von der Azure Automation-Updateverwaltung unterstützte Clients
description: Dieser Artikel behandelt die von der Azure Automation-Updateverwaltung unterstützten Windows- und Linux-Betriebssysteme.
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: a470a3c8d4ab9ac4f390f7b18133f65f44fed096
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711645"
---
# <a name="operating-systems-supported-by-update-management"></a>Von der Updateverwaltung unterstützte Betriebssysteme

In diesem Artikel werden die unterstützten Windows- und Linux-Betriebssysteme sowie die Systemanforderungen für Computer oder Server aufgelistet, die von der Updateverwaltung verwaltet werden.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgende Tabelle enthält die unterstützten Betriebssysteme für Updatebewertungen und -patching. Das Patchen erfordert einen System Hybrid Runbook Worker, der automatisch installiert wird, wenn Sie den virtuellen Computer oder Server für die Verwaltung durch Updateverwaltung aktivieren. Informationen zu den Systemanforderungen für Hybrid Runbook Worker finden Sie unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md#prerequisites) und [Bereitstellen eines Linux Hybrid Runbook Workers](../automation-linux-hrw-install.md#prerequisites).

Bei allen Betriebssystemen wird von einer x64-Architektur ausgegangen. x86 wird für kein Betriebssystem unterstützt.

> [!NOTE]
> Die Updatebewertung von Linux-Computern wird nur in bestimmten Regionen unterstützt, die in der [Zuordnungstabelle](../how-to/region-mappings.md#supported-mappings) für Automation-Konto und Log Analytics-Arbeitsbereich aufgeführt sind.

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows Server 2019 (Datacenter/Standard mit Server Core)<br><br>Windows Server 2016 (Datacenter/Standard ohne Server Core)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM und SP1 Standard)| Die Updateverwaltung unterstützt Bewertungen und Patching für dieses Betriebssystem. Der [Hybrid Runbook Worker](../automation-windows-hrw-install.md) wird für Windows Server 2008 R2 unterstützt. |
|CentOS 6, 7 und 8       | Linux-Agents erfordern Zugriff auf ein Updaterepository. Für klassifizierungsbasiertes Patchen muss `yum` Sicherheitsdaten zurückgeben, über die CentOS in den RTM-Releases nicht verfügt. Weitere Informationen zu klassifizierungsbasiertem Patching unter CentOS finden Sie unter [Lösung für die Updateverwaltung in Azure](view-update-assessments.md#linux).          |
|Oracle Linux 6.x, 7.x, 8x | Linux-Agents erfordern Zugriff auf ein Updaterepository.        |
|Red Hat Enterprise 6, 7 und 8      | Linux-Agents erfordern Zugriff auf ein Updaterepository.        |
|SUSE Linux Enterprise Server 12, 15, 15.1 und 15.2      | Linux-Agents erfordern Zugriff auf ein Updaterepository.     |
|Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS und 20.04 LTS       |Linux-Agents erfordern Zugriff auf ein Updaterepository.         |

> [!NOTE]
> Die Updateverwaltung unterstützt keine sichere Automatisierung der Updateverwaltung über alle Instanzen in einer Azure-VM-Skalierungsgruppe. [Automatische Upgrades von Betriebssystemimages](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) ist die empfohlene Methode zum Verwalten von Betriebssystemimage-Upgrades in Ihrer Skalierungsgruppe.

## <a name="unsupported-operating-systems"></a>Nicht unterstützte Betriebssysteme

In der folgenden Tabelle werden Betriebssysteme aufgelistet, die von der Updateverwaltung nicht unterstützt werden:

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows-Client     | Clientbetriebssysteme (z.B. Windows 7 und Windows 10) werden nicht unterstützt.<br> Für Azure Windows Virtual Desktop (WVD) ist die empfohlene Methode<br> zum Verwalten von Updates [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) für die Patchverwaltung von Windows 10-Clientcomputern. |
|Windows Server 2016 Nano Server     | Wird nicht unterstützt.       |
|Azure Kubernetes Service-Knoten | Wird nicht unterstützt. Verwenden Sie den unter [Anwenden von Sicherheits- und Kernelupdates auf Linux-Knoten in Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md) beschriebenen Patchprozess.|

## <a name="system-requirements"></a>Systemanforderungen

Der folgende Abschnitt enthält Informationen zu betriebssystemspezifischen Anforderungen. Weitere Informationen finden Sie unter [Netzwerkplanung](plan-deployment.md#ports). Informationen zu den Anforderungen für TLS 1.2 finden Sie unter [TLS 1.2 für Azure Automation](../automation-managing-data.md#tls-12-for-azure-automation).

### <a name="windows"></a>Windows

Softwareanforderungen:

- .NET Framework 4.6 oder höher ist erforderlich. ([.NET Framework herunterladen](/dotnet/framework/install/guide-for-developers).)
- Windows PowerShell 5.1 ist erforderlich. ([Windows Management Framework 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616).)
- Die Updateverwaltungsfunktion hängt von der Systemrolle Hybrid Runbook Worker ab, und Sie sollten sicherstellen, dass ihre [Systemanforderungen](../automation-windows-hrw-install.md#prerequisites) erfüllt werden.

Windows Update-Agents müssen entweder für die Kommunikation mit einem WSUS-Server (Windows Server Update Services) konfiguriert sein oder über Zugriff auf Microsoft Update verfügen. Für Hybridcomputer empfehlen wir, den Log Analytics-Agent für Windows zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtliniendefinition [Log Analytics-Agent für Windows-Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Wenn Sie alternativ die Überwachung der VMs mit VM Insights planen, verwenden Sie stattdessen die Initiative [Aktivieren von VM Insights](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Sie können die Updateverwaltung zusammen mit Microsoft Endpoint Configuration Manager verwenden. Weitere Informationen zu Integrationsszenarien finden Sie unter [Integrieren der Updateverwaltung in Windows Endpoint Configuration Manager](mecmintegration.md). Der [Log Analytics-Agent für Windows](../../azure-monitor/agents/agent-windows.md) ist für Windows-Server erforderlich, die von Sites in Ihrer Configuration Manager-Umgebung verwaltet werden.

Über Azure Marketplace bereitgestellte virtuelle Windows-Computer sind standardmäßig so konfiguriert, dass sie automatisch Updates vom Windows Update-Dienst erhalten. Dieses Verhalten ändert sich nicht, wenn Sie Ihrem Arbeitsbereich virtuelle Windows-Computer hinzufügen. Wenn Sie Updates mithilfe der Updateverwaltung nicht aktiv verwalten, gilt das Standardverhalten (Updates werden automatisch angewendet).

> [!NOTE]
> Sie können die Gruppenrichtlinie so ändern, dass Computerneustarts nur vom Benutzer durchgeführt werden können, nicht aber vom System. Verwaltete Computer bleiben ggf. hängen, wenn die Updateverwaltung nicht berechtigt ist, den Computer ohne manuelle Benutzerinteraktion neu zu starten. Weitere Informationen finden Sie unter [Konfigurieren der Gruppenrichtlinieneinstellungen für automatische Updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

### <a name="linux"></a>Linux

Softwareanforderungen:

- Der Computer benötigt Zugriff auf ein Updaterepository, entweder privat oder öffentlich.
- Für die Interaktion mit der Updateverwaltung ist TLS 1.1 oder TLS 1.2 erforderlich.
- Die Updateverwaltungsfunktion hängt von der Systemrolle Hybrid Runbook Worker ab, und Sie sollten sicherstellen, dass ihre [Systemanforderungen](../automation-linux-hrw-install.md#prerequisites) erfüllt werden. Da die Updateverwaltung Automation-Runbooks verwendet, um die Bewertung und Aktualisierung Ihrer Computer zu initiieren, überprüfen Sie, [welche Python-Version](../automation-linux-hrw-install.md#supported-runbook-types) für Ihre unterstützte Linux-Distribution erforderlich ist.

> [!NOTE]
> Die Updatebewertung von Linux-Computern wird nur in bestimmten Regionen unterstützt. Informationen finden Sie in der [Zuordnungstabelle](../how-to/region-mappings.md#supported-mappings) für Automation-Konto und Log Analytics-Arbeitsbereich.

Für Hybridcomputer empfehlen wir, den Log Analytics-Agent für Linux zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtliniendefinition [Log Analytics-Agent für Linux-Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie die Updateverwaltung aktivieren und verwenden, lesen Sie [Planen Ihrer Bereitstellung der Updateverwaltung](plan-deployment.md).
