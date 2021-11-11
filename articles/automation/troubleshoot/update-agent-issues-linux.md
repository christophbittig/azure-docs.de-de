---
title: Beheben von Problemen mit dem Linux Update-Agent in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Fehler und Probleme mit dem Linux Update-Agent in der Updateverwaltung beheben können.
services: automation
ms.date: 11/01/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 99758393b3d6545d534563a67f1346f7622b56db
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477096"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Beheben von Problemen mit dem Linux Update-Agent

Es gibt viele mögliche Gründe dafür, warum Ihr Computer in der Updateverwaltung nicht als „bereit“ (fehlerfrei) angezeigt wird. Sie können die Integrität eines Linux-Hybrid Runbook Worker-Agents überprüfen, um das zugrunde liegende Problem zu ermitteln. Im Folgenden finden Sie die drei Bereitschaftszustände eines Computers:

* Bereit: Der Hybrid Runbook Worker ist bereitgestellt und wurde vor weniger als einer Stunde zuletzt gesehen.
* Nicht verbunden: Der Hybrid Runbook Worker ist bereitgestellt und wurde vor über einer Stunde zuletzt gesehen.
* Nicht konfiguriert: Der Hybrid Runbook Worker wurde nicht gefunden, oder die Bereitstellung wurde nicht abgeschlossen.

> [!NOTE]
> Möglicherweise gibt es eine kleine Verzögerung zwischen der Anzeige im Azure-Portal und dem aktuellen Zustand eines Computers.

In diesem Artikel wird erläutert, wie Sie die Problembehandlung für Azure-Computer im Azure-Portal und für Nicht-Azure-Computer im [Offlineszenario](#troubleshoot-offline) ausführen.

> [!NOTE]
> Das Problembehandlungsskript leitet Datenverkehr zurzeit nicht über einen Proxyserver weiter, wenn ein solcher konfiguriert ist.

## <a name="start-the-troubleshooter"></a>Starten der Problembehandlung

Klicken Sie für Azure-Computer im Portal unter der Spalte **Update-Agent-Bereitschaft** auf den Link **Problembehandlung**, um die Seite „Troubleshoot Update Agent“ (Problembehandlung von Update-Agent) zu öffnen. Bei Azure-fremden Computern gelangen Sie über den Link zu diesem Artikel. Eine Anleitung für die Problembehandlung bei Nicht-Azure-Computern finden Sie im Abschnitt „Offlineproblembehandlung“.

![Seite mit VM-Liste](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Für die Überprüfungen muss die VM ausgeführt werden. Wenn die VM nicht ausgeführt wird, wird **VM starten** angezeigt.

Klicken Sie auf der Seite „Troubleshoot Update Agent“ (Problembehandlung von Update-Agent) auf **Run Checks** (Überprüfungen ausführen), um mit der Problembehandlung zu beginnen. Die Problembehandlung verwendet [Befehl ausführen](../../virtual-machines/linux/run-command.md), um ein Skript auf dem Computer auszuführen, mit dem Abhängigkeiten überprüft werden. Wenn die Problembehandlung abgeschlossen ist, werden die Ergebnisse der Überprüfungen zurückgegeben.

![Seite „Problembehandlung“](../media/update-agent-issues-linux/troubleshoot-page.png)

Wenn die Überprüfungen abgeschlossen sind, werden die Ergebnisse im Fenster zurückgegeben. Die verschiedenen Überprüfungsabschnitte liefern Informationen zu den Aspekten, die jede Überprüfung untersucht.

![Seite mit Überprüfungen des Update-Agents](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Voraussetzungsprüfungen

### <a name="operating-system"></a>Betriebssystem

Die Betriebssystemprüfung verifiziert, ob der Hybrid Runbook Worker unter einem der [unterstützten Betriebssysteme](../update-management/operating-system-requirements.md#supported-operating-systems) ausgeführt wird.

## <a name="monitoring-agent-service-health-checks"></a>Integritätsüberprüfungen für den Monitoring Agent-Dienst

### <a name="log-analytics-agent"></a>Log Analytics-Agent

Durch diese Überprüfung wird sichergestellt, dass der Log Analytics-Agent für Linux installiert ist. Anweisungen zum Installieren finden Sie unter [Installieren des Agents für Linux](../../azure-monitor/vm/monitor-virtual-machine.md#agents).

### <a name="log-analytics-agent-status"></a>Status des Log Analytics-Agents

Durch diese Überprüfung wird sichergestellt, dass der Log Analytics-Agent für Linux ausgeführt wird. Wenn der Agent nicht aktiv ist, können Sie den folgenden Befehl ausführen, um ihn neu zu starten. Weitere Informationen zur Problembehandlung für den Agent finden Sie unter [Problembehandlung für Hybrid Runbook Worker – Linux](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multi-Homing

Durch diese Überprüfung wird ermittelt, ob der Agent Meldungen an mehrere Arbeitsbereiche ausgibt. Die Updateverwaltung unterstützt kein Multi-Homing.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Hiermit wird überprüft, ob der Log Analytics-Agent für Linux über das Hybrid Runbook Worker-Paket verfügt. Dieses Paket ist erforderlich, damit die Updateverwaltung funktioniert. Weitere Informationen finden Sie unter [Der Log Analytics-Agent für Linux wird nicht ausgeführt](hybrid-runbook-worker.md#oms-agent-not-running).

Die Updateverwaltung lädt Hybrid Runbook Worker-Pakete vom Endpunkt für Vorgänge herunter. Wenn der Hybrid Runbook Worker nicht ausgeführt wird und die Überprüfung des [Endpunkts für Vorgänge](#operations-endpoint) fehlschlägt, kann das Update daher fehlschlagen.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker-Status

Durch diese Überprüfung wird sichergestellt, dass der Hybrid Runbook Worker auf dem Computer ausgeführt wird. Die Prozesse im folgenden Beispiel sollten vorhanden sein, wenn der Hybrid Runbook Worker ordnungsgemäß ausgeführt wird.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Konnektivitätsprüfungen

### <a name="general-internet-connectivity"></a>Allgemeine Internetkonnektivität

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf das Internet hat.

### <a name="registration-endpoint"></a>Registrierungsendpunkt

Durch diese Überprüfung wird festgestellt, ob der Hybrid Runbook Worker im Log Analytics-Arbeitsbereich ordnungsgemäß mit Azure Automation kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Registrierungsendpunkt zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpunkt für Vorgänge

Bei dieser Überprüfung wird untersucht, ob der Log Analytics-Agent ordnungsgemäß mit dem Auftragsruntime-Datendienst kommunizieren kann.

Die Proxy- und Firewallkonfigurationen müssen die Kommunikation des Hybrid Runbook Worker-Agents mit dem Auftragsruntime-Datendienst zulassen. Eine Liste der Adressen und zu öffnenden Ports finden Sie unter [Netzwerkplanung](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics-Endpunkt 1

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

### <a name="log-analytics-endpoint-2"></a>Log Analytics-Endpunkt 2

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

### <a name="log-analytics-endpoint-3"></a>Log Analytics-Endpunkt 3

Durch diese Überprüfung wird sichergestellt, dass der Computer Zugriff auf die Endpunkte hat, die vom Log Analytics-Agent benötigt werden.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Offlineproblembehandlung

Sie können die Problembehandlung offline auf einem Hybrid Runbook Worker ausführen, indem Sie das Skript lokal ausführen. Das Python-Skript, [UM_Linux_Troubleshooter_Offline.py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), finden Sie auf GitHub.

> [!NOTE]
> Die aktuelle Version des Problembehandlungsskripts unterstützt Ubuntu 20.04 nicht.
>

Hier sehen Sie eine Beispielausgabe dieses Skripts:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Nächste Schritte

[Beheben von Hybrid Runbook Worker-Problemen](hybrid-runbook-worker.md)