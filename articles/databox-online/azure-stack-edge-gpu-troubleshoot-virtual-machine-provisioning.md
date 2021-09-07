---
title: Behandeln von Problemen bei der Bereitstellung virtueller Computer in Azure Stack Edge Pro GPU | Microsoft-Dokumentation
description: Beschreibt die Behandlung von Problemen, die bei der Bereitstellung eines neuen virtuellen Computers (Virtual Machine, VM) in Azure Stack Edge Pro GPU auftreten.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: e04d3a0a5b7c48117af1f597606658a878a3a6ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346786"
---
# <a name="troubleshoot-vm-deployment-in-azure-stack-edge-pro-gpu"></a>Behandeln von Problemen bei der Bereitstellung virtueller Computer in Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie häufige Fehler bei der Bereitstellung virtueller Computer (VMs) auf einem Azure Stack Edge Pro GPU-Gerät behoben werden. Dieser Artikel enthält Anleitungen zum Untersuchen der häufigsten Probleme, die bei der VM-Bereitstellung zu Timeouts und beim Erstellen von Netzwerkschnittstelle und virtuellem Computer zu Problemen führen.

Zum Diagnostizieren von Fehlern bei der VM-Bereitstellung überprüfen Sie die Gastprotokolle für den virtuellen Computer, bei dem der Fehler aufgetreten ist. Die Schritte zum Erfassen von VM-Gastprotokollen und deren Einbindung in ein Supportpaket finden Sie unter [Erfassen von VM-Gastprotokollen auf Azure Stack Edge Pro GPU-Geräten](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).

Informationen zu Problemen, die ein erfolgreiches Hochladen eines VM-Images vor der VM-Bereitstellung verhindern, finden Sie unter [Problembehandlung beim Hochladen von VM-Images in Azure Stack Edge Pro GPU](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).


## <a name="vm-provisioning-timeout"></a>Timeout bei der VM-Bereitstellung

Dieser Abschnitt enthält Informationen zu den häufigsten Ursachen eines Timeouts bei der VM-Bereitstellung und zur Problembehandlung.

Wenn bei der VM-Bereitstellung ein Timeout auftritt, wird der folgende Fehler angezeigt: 

![Screenshot: Im Azure-Portal angezeigter Fehler nach einem Timeout bei der VM-Bereitstellung in Azure Stack Edge.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-provisioning-timeout-01.png) 

Die folgenden Probleme sind die Hauptursachen für Timeouts bei der VM-Bereitstellung:
- Die IP-Adresse, die Sie dem virtuellen Computer zugewiesen haben, wird bereits verwendet. [Weitere Informationen](#vm-provisioning-timeout)
- Das VM-Image, das Sie zum Bereitstellen des virtuellen Computers verwendet haben, wurde nicht ordnungsgemäß vorbereitet. [Weitere Informationen](#vm-image-not-prepared-correctly)
- Standardgateway und DNS-Server konnten vom virtuellen Gastcomputer nicht erreicht werden. [Weitere Informationen](#gateway-dns-server-couldnt-be-reached-from-guest-vm)
- Bei einer Installation mit `cloud init` wurde entweder `cloud init` nicht ausgeführt, oder es gab Probleme bei der Ausführung. (Nur virtuelle Linux-Computer) [Weitere Informationen](#cloud-init-issues-linux-vms)
- Bei einem virtuellen Linux-Computer, der mit einem benutzerdefinierten VM-Image bereitgestellt wird, sind die Bereitstellungsflags in der Datei „/etc/waagent.conf“ nicht ordnungsgemäß angegeben. (Nur virtuelle Linux-Computer) [Weitere Informationen](#provisioning-flags-set-incorrectly-linux-vms)

### <a name="ip-assigned-to-the-vm-is-already-in-use"></a>Die dem virtuellen Computer zugewiesene IP-Adresse wird bereits verwendet

**Fehlerbeschreibung:** Dem virtuellen Computer wurde eine statische IP-Adresse zugewiesen, die bereits verwendet wird, und bei der VM-Bereitstellung ist ein Fehler aufgetreten. Dieser Fehler tritt auf, wenn die IP-Adresse in dem Subnetz, in dem der virtuelle Computer bereitgestellt wird, bereits verwendet wird. Wenn Sie einen virtuellen Computer über das Azure-Portal bereitstellen, überprüft der Prozess zwar, ob die IP-Adresse auf Ihrem Gerät vorhanden ist, kann aber nicht die IP-Adressen anderer Dienste oder virtueller Computer überprüfen, die sich möglicherweise ebenfalls in Ihrem Subnetz befinden. 

**Lösungsvorschlag:** Verwenden Sie eine statische IP-Adresse, die nicht verwendet wird, oder verwenden Sie eine dynamische IP-Adresse, die vom DHCP-Server bereitgestellt wird.

So suchen Sie nach einer doppelten IP-Adresse

- Führen Sie die Befehle `ping` und Test-NetConnection (`tnc`) von jeder Appliance im selben Netzwerk aus:

  ```
  ping <IP address>
  tnc <IP address>
  tnc <IP address> -CommonTCPPort “RDP”
  ```

Wenn Sie eine Antwort erhalten, wird die IP-Adresse, die Sie dem neuen virtuellen Computer zugewiesen haben, bereits verwendet.

### <a name="vm-image-not-prepared-correctly"></a>VM-Image wurde nicht ordnungsgemäß vorbereitet

**Fehlerbeschreibung:** Um ein VM-Image für die Verwendung auf einem Azure Stack Edge Pro GPU-Gerät vorzubereiten, müssen Sie einen bestimmten Workflow beachten. Sie müssen in Azure einen virtuellen Computer der 1.Generation erstellen, den virtuellen Computer anpassen, die VHD generalisieren und dann die Betriebssystem-VHD für diesen virtuellen Computer herunterladen. Das vorbereitete Image muss eine VHD der 1. Generation mit der Dateinamenerweiterung „vhd“ sein und den Typ „feste Größe“ aufweisen.

Eine Übersicht über die Anforderungen finden Sie unter [Erstellen benutzerdefinierter VM-Images für Ihr Azure Stack Edge Pro GPU-Gerät](azure-stack-edge-gpu-create-virtual-machine-image.md). Anleitungen zum Beheben von Problemen mit VM-Images finden Sie unter [Behandeln von Problemen beim Hochladen von VM-Images in Azure Stack Edge Pro GPU](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).  

**Lösungsvorschlag:** Schließen Sie den Workflow für die Vorbereitung Ihres VM-Images ab. Weitere Hinweise finden Sie in den folgenden Artikeln:

* [Benutzerdefinierte Workflows für VM-Images für virtuelle Windows- und Linux-Computer](azure-stack-edge-gpu-create-virtual-machine-image.md)
* [Vorbereiten eines generalisierten Images von einer Windows-VHD](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
* [Vorbereiten eines generalisierten Images von ISO](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
* [Verwenden eines speziellen Images zum Bereitstellen von virtuellen Computern](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)


### <a name="gateway-dns-server-couldnt-be-reached-from-guest-vm"></a>Gateway und DNS-Server konnten vom virtuellen Gastcomputer nicht erreicht werden

**Fehlerbeschreibung:** Wenn Standardgateway und DNS-Server bei der VM-Bereitstellung nicht erreicht werden können, tritt bei der VM-Bereitstellung ein Timeout und dadurch ein Fehler auf.

**Lösungsvorschlag:** Stellen Sie sicher, dass Standardgateway und DNS-Server für den virtuellen Computer erreichbar sind. Wiederholen Sie dann die VM-Bereitstellung.

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob Standardgateway und DNS-Server für den virtuellen Computer erreichbar sind:
1. [Stellen Sie eine Verbindung mit der VM her](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
2. Führen Sie die folgenden Befehle aus:

   ```
   ping <default gateway IP address>
   ping <DNS server IP address>
   ```

   Um die IP-Adressen für Standardgateway und DNS-Server zu ermitteln, wechseln Sie zur lokalen Benutzeroberfläche für Ihr Gerät. Wählen Sie den gewünschten Port aus, und zeigen Sie die Netzwerkeinstellungen an.

   ![Screenshot: Seite „Netzwerk“ für Azure Stack Edge-Gerät mit Anzeige der Netzwerkeinstellungen für Port 2.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gateway-dns-server-settings-01.png) 


### <a name="cloud-init-issues-linux-vms"></a>Probleme mit `cloud init` (virtuelle Linux-Computer)

**Fehlerbeschreibung:** Entweder wurde `cloud init` nicht ausgeführt, oder beim Ausführen von `cloud init` sind Probleme aufgetreten. `cloud-init` wird verwendet, um einen virtuellen Linux-Computer anzupassen, wenn dieser zum ersten Mal gestartet wird. Weitere Informationen finden Sie unter [cloud-init-Unterstützung für virtuelle Computer in Azure](../virtual-machines/linux/using-cloud-init.md).

**Lösungsvorschläge:** Gehen Sie wie folgt vor, um Probleme beim Ausführen von `cloud init` zu ermitteln:
1. [Stellen Sie eine Verbindung mit der VM her](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
1. Suchen Sie in den folgenden Protokolldateien nach `cloud init`-Fehlern:

   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent/log 

Führen Sie die folgenden Schritte aus, um nach einigen der häufigsten Probleme zu suchen, die eine erfolgreiche Ausführung von `cloud init` verhindern:

1. Stellen Sie sicher, dass das VM-Image auf `cloud init` basiert. Führen Sie den folgenden Befehl aus:

   `cloud-init --version`

   Mit diesem Befehl sollte die cloud-init-Versionsnummer zurückgegeben werden. Wenn das Image nicht auf `cloud init` basiert, werden mit dem Befehl keine Versionsinformationen zurückgegeben.

   Führen Sie den folgenden Befehl aus, um Hilfe zu `cloud init`-Optionen zu erhalten:

   `cloud-init --help` 

2. Stellen Sie sicher, dass die `cloud init`-Instanz erfolgreich ausgeführt werden kann, wenn die Datenquelle auf *Azure* festgelegt ist. 

   Wenn die Datenquelle auf *Azure* festgelegt ist, sieht der Eintrag in den *cloud-init*-Protokollen in etwa wie folgt aus.

   ![Abbildung: cloud-init-Protokolleintrag für ein VM-Image mit der Einstellung „Azure“ für die Datenquelle. Der identifizierende Text ist hervorgehoben.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/cloud-init-log-entry-01.png)

   Wenn die Datenquelle nicht auf Azure festgelegt ist, müssen Sie möglicherweise Ihr `cloud init`-Skript überarbeiten. Weitere Informationen finden Sie unter [Tiefere Einblicke in cloud-init](../virtual-machines/linux/cloud-init-deep-dive.md).


### <a name="provisioning-flags-set-incorrectly-linux-vms"></a>Falsch festgelegte Bereitstellungsflags (virtuelle Linux-Computer)

**Fehlerbeschreibung:** Um einen virtuellen Linux-Computer erfolgreich in Azure bereitzustellen, muss die Bereitstellung für das Image deaktiviert und die Bereitstellung mit `cloud init` aktiviert werden. Die Bereitstellungsflags, mit denen diese Werte festgelegt sind, werden für VM-Standardimages ordnungsgemäß konfiguriert. Wenn Sie ein benutzerdefiniertes VM-Image verwenden, müssen Sie sicherstellen, dass die Werte richtig sind. 

**Lösungsvorschlag:** Stellen Sie sicher, dass die Bereitstellungsflags in der Datei */etc/waagent.conf* die folgenden Werte aufweisen:<!--Move details to "Create a custom VM image" when the 2 active PRs against that article have been merged. Not before Friday release.-->

   | Funktion                      | Erforderlicher Wert                |
   |---------------------------------|-------------------------------|
   | Aktivieren der Bereitstellung             | `Provisioning.Enabled=n`      |
   | Verwenden von cloud-init für die Bereitstellung | `Provisioning.UseCloudInit=y` |


##  <a name="network-interface-creation-issues"></a>Probleme beim Erstellen der Netzwerkschnittstelle

Dieser Abschnitt enthält Hinweise für Probleme, die dazu führen, dass bei einer VM-Bereitstellung beim Erstellen der Netzwerkschnittstelle ein Fehler auftritt.


### <a name="nic-creation-timeout"></a>Timeout bei der NIC-Erstellung

**Fehlerbeschreibung:** Die Erstellung der Netzwerkschnittstelle auf dem virtuellen Computer wurde nicht innerhalb des zulässigen Zeitlimits abgeschlossen. Dieser Fehler kann durch DHCP-Serverprobleme in Ihrer Umgebung verursacht werden. 

Um zu überprüfen, ob die Netzwerkschnittstelle erfolgreich erstellt wurde, gehen Sie folgendermaßen vor:

1. Navigieren Sie im Azure-Portal zu der Azure Stack Edge-Ressource für Ihr Gerät (**Edge-Dienste** > **Virtuelle Computer**). Wählen Sie dann **Bereitstellungen** aus, und navigieren Sie zur VM-Bereitstellung. 

1. Wenn eine Netzwerkschnittstelle nicht erfolgreich erstellt wurde, wird der folgende Fehler angezeigt.

   ![Screenshot: Im Azure-Portal angezeigter Fehler, wenn die Netzwerkschnittstelle während der VM-Bereitstellung auf einem Azure Stack Edge-Gerät nicht erfolgreich erstellt werden kann.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/nic-creation-failed-01.png)

**Lösungsvorschlag:** Erstellen Sie den virtuellen Computer erneut, und weisen Sie ihm eine statische IP-Adresse zu.


## <a name="vm-creation-issues"></a>Probleme beim Erstellen virtueller Computer

In diesem Abschnitt werden häufige Probleme behandelt, die bei der Erstellung virtueller Computer auftreten.

### <a name="not-enough-memory-to-create-the-vm"></a>Nicht genügend Arbeitsspeicher zum Erstellen des virtuellen Computers

**Fehlerbeschreibung:** Wenn die VM-Erstellung aufgrund von unzureichendem Arbeitsspeicher fehlschlägt, wird der folgende Fehler angezeigt.
 
![Screenshot: Im Azure-Portal angezeigter Fehler, wenn die VM-Erstellung auf einem Azure Stack Edge-Gerät nicht erfolgreich ist.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-creation-failed-01.png)

**Lösungsvorschlag:** Überprüfen Sie den verfügbaren Arbeitsspeicher auf dem Gerät, und wählen Sie die VM-Größe entsprechend aus. Weitere Informationen finden Sie unter [In Azure Stack Edge unterstützte VM-Größen](azure-stack-edge-gpu-virtual-machine-sizes.md).

Der für die Bereitstellung eines virtuellen Computers verfügbare Arbeitsspeicher wird durch mehrere Faktoren beschränkt:

- Gesamter verfügbarer Arbeitsspeicher auf dem Gerät. Weitere Informationen finden Sie unter Compute- und Arbeitsspeicherspezifikationen in [Spezifikationen für Azure Stack Edge Pro GPU](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) und in [Technische Spezifikationen für Azure Stack Edge Mini R](azure-stack-edge-mini-r-technical-specifications-compliance.md#compute-memory).

- Der für Kubernetes und Apps im Kubernetes-Cluster erforderliche Computespeicher, wenn Kubernetes aktiviert ist.
- Der Zuschlag für jeden virtuellen Computer in Hyper-V.

**Lösungsvorschläge:**

- Verwenden Sie eine VM-Größe, die weniger Arbeitsspeicher erfordert.
- Beenden Sie im Portal alle virtuellen Computer, die nicht verwendet werden, bevor Sie den neuen virtuellen Computer bereitstellen.
- Löschen Sie alle virtuellen Computer, die nicht mehr verwendet werden.


### <a name="insufficient-number-of-gpus-to-create-gpu-vm"></a>Unzureichende Anzahl von GPUs zum Erstellen einer GPU-VM

Wenn Sie versuchen, einen virtuellen Computer auf einem GPU-Gerät bereitzustellen, auf dem bereits Kubernetes aktiviert ist, sind keine GPUs verfügbar, und bei der VM-Bereitstellung tritt der folgende Fehler auf:

![Screenshot: Im Azure-Portal angezeigter Fehler, wenn keine GPU-VM auf einem Azure Stack Edge-Gerät erstellt werden kann, weil keine GPUs verfügbar sind.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gpu-vm-creation-failed-01.png)

**Mögliche Ursachen:** Wenn Kubernetes aktiviert ist, bevor der virtuelle Computer erstellt wird, verwendet Kubernetes alle verfügbaren GPUs, und Sie können keine VMs mit GPU-optimierter Größe erstellen. Sie können so viele VMs mit GPU-optimierter Größe erstellen, wie GPUs verfügbar sind. Ihr Azure Stack Edge-Gerät kann mit 1 oder 2 GPUs ausgestattet sein.

**Lösungsvorschlag:** Informationen zu VM-Bereitstellungsoptionen auf einem Gerät mit 1 oder 2 GPUs, auf dem auch Kubernetes konfiguriert ist, finden Sie unter [GPU-VMs und Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).


## <a name="next-steps"></a>Nächste Schritte

- [Erfassen von VM-Gastprotokollen auf Azure Stack Edge Pro GPU-Geräten](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)<!--Does a failed VM have a guest log? Does it have GPU and memory metrics?-->
- [Behandeln von Problemen mit einer fehlgeschlagenen GPU-Erweiterungsinstallation](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
- [Behandeln von Fehlern bei Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)
