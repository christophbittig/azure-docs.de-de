---
title: Bereitstellen von Hochleistungsnetzwerk-VMs auf Ihrer Azure Stack Edge Pro-GPU
description: In diesem Artikel erfahren Sie, wie Sie Hochleistungsnetzwerk-VMs auf Ihrer Azure Stack Edge Pro-GPU bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2021
ms.author: alkohli
ms.openlocfilehash: 4b3f2a2b232bec60edbce204008e245bcabee09c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095977"
---
# <a name="deploy-high-performance-network-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Bereitstellen von Hochleistungsnetzwerk-VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Sie können virtuelle Computer (Virtual Machines, VMs) auf einem Azure Stack Edge Pro GPU-Gerät über das Azure-Portal, Vorlagen und Azure PowerShell-Cmdlets sowie über die Azure CLI bzw. Python-Skripts erstellen und verwalten. In diesem Artikel wird beschrieben, wie Sie eine Hochleistungsnetzwerk-VM auf Ihrem Azure Stack Edge Pro-GPU-Gerät erstellen und verwalten. 

## <a name="about-hpn-vms"></a>Informationen zu Hochleistungsnetzwerk-VMs

Mithilfe von Non-Uniform Memory Access-Architekturen (NUMA) lässt sich die Prozessorgeschwindigkeit erhöhen. In NUMA-Systemen sind CPUs in kleineren Systemen angeordnet, die als Knoten bezeichnet werden. Jeder Knoten verfügt über eigene Prozessoren und einen eigenen Arbeitsspeicher. Prozessoren wird in der Regel Arbeitsspeicher zugeteilt, der sie sich in ihrer Nähe befindet, damit der Zugriff schneller erfolgt. Weitere Informationen finden Sie unter [NUMA-Unterstützung](/windows/win32/procthread/numa-support).  

Auf Ihrem Azure Stack Edge-Gerät werden logische Prozessoren auf NUMA-Knoten verteilt, und Hochgeschwindigkeits-Netzwerkschnittstellen können an diese Knoten angefügt werden. Eine Hochleistungsnetzwerk-VM verfügt über dedizierte logische Prozessoren. Diese Prozessoren werden zuerst aus dem NUMA-Knoten, an den eine Hochgeschwindigkeits-Netzwerkschnittstelle angefügt ist, und dann aus anderen Knoten ausgewählt. Eine Hochleistungsnetzwerk-VM kann nur den Arbeitsspeicher des NUMA-Knotens verwenden, der den zugehörigen Prozessoren zugewiesen ist.  

Um Netzwerkanwendungen mit geringer Latenz und hohem Durchsatz auf den Hochleistungsnetzwerk-VMs auf Ihrem Gerät ausführen zu können, müssen Sie sicherstellen, dass vCPUs reserviert werden, die sich auf NUMA-Knoten 0 befinden. An diesen Knoten sind Mellanox-Hochgeschwindigkeits-Netzwerkschnittstellen (Port 5 und Port 6) angefügt.   

        
## <a name="hpn-vm-deployment-workflow"></a>Bereitstellungsworkflow für Hochleistungsnetzwerk-VMs

Die allgemeine Zusammenfassung des Bereitstellungsworkflows für Hochleistungsnetzwerk-VMs lautet wie folgt:

1. Aktivieren einer Netzwerkschnittstelle für Compute auf dem Azure Stack Edge-Gerät In diesem Schritt wird ein virtueller Switch an der angegebenen Netzwerkschnittstelle erstellt.
1. Aktivieren Sie die Cloudverwaltung von virtuellen Computern über das Azure-Portal.
1. Laden Sie mit dem Azure Storage-Explorer eine VHD in ein Azure Storage-Konto hoch. 
1. Verwenden Sie die hochgeladene VHD, um diese auf das Gerät herunterzuladen und ein VM-Image von der VHD zu erstellen. 
1. Reservieren Sie vCPUs auf dem Gerät für Hochleistungsnetzwerk-VMs.
1. Verwenden Sie die Ressourcen, die Sie in den vorherigen Schritten erstellt haben:
    1. VM-Image, das Sie erstellt haben
    1. Virtueller Switch, der der Netzwerkschnittstelle zugeordnet ist, an der Sie Compute aktiviert haben
    1. Subnetz, das dem virtuellen Switch zugeordnet ist

    Und erstellen Sie die folgenden Ressourcen inline, oder geben Sie diese inline an:
    1. VM-Name, und wählen Sie eine unterstützte Größe und Anmeldeinformationen für die Hochleistungsnetzwerk-VM aus. 
    1. Erstellen Sie neue Datenträger, oder fügen Sie vorhandene Datenträger an.
    1. Konfigurieren Sie eine statische oder dynamische IP-Adresse für die VM. Wenn Sie eine statische IP-Adresse bereitstellen, wählen Sie eine freie IP-Adresse im Subnetzbereich der für Compute aktivierten Netzwerkschnittstelle aus.

    Erstellen Sie anhand der oben genannten Ressourcen eine Hochleistungsnetzwerk-VM.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Erstellen und Verwalten von VMs auf Ihrem Gerät über das Azure-Portal beginnen, stellen Sie Folgendes sicher:

- Sie haben die Netzwerkeinstellungen auf Ihrem Azure Stack Edge Pro GPU-Gerät wie unter [Schritt 1: Konfigurieren eines Azure Stack Edge Pro GPU-Geräts](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device) beschrieben festgelegt.

    1. Sie haben eine Netzwerkschnittstelle für Compute aktiviert. Diese IP-Adresse der Netzwerkschnittstelle wird verwendet, um einen virtuellen Switch für die VM-Bereitstellung zu erstellen. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zu **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen.

        > [!IMPORTANT] 
        > Sie können nur einen Port für Compute konfigurieren.

    1. Aktivieren Sie Compute für die Netzwerkschnittstelle. Azure Stack Edge Pro GPU erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht.

-  Sie haben Zugriff auf eine Windows- oder Linux-VHD, die Sie verwenden, um das VM-Image für den virtuellen Computer zu erstellen, den Sie erstellen möchten.

Zusätzlich zu den oben genannten Voraussetzungen für die VM-Erstellung müssen Sie auch die folgenden Voraussetzungen speziell für Hochleistungsnetzwerk-VMs konfigurieren:

- Reservieren Sie vCPUs für Hochleistungsnetzwerk-VMs in der Mellanox-Schnittstelle. Folgen Sie diesen Schritten:

    1. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)

    1. Ermitteln Sie alle VMs, die auf Ihrem Gerät ausgeführt werden. Dies schließt Kubernetes-VMs oder alle VM-Workloads ein, die Sie möglicherweise bereitgestellt haben.

        ```powershell
        get-vm -force
        ```
    1. Beenden Sie alle VMs, die gerade ausgeführt werden.
    
        ```powershell
        stop-vm -force
        ```
    1. Rufen Sie `hostname` für Ihr Gerät ab. Dadurch sollte eine Zeichenfolge zurückgegeben werden, die dem Hostnamen des Geräts entspricht.

        ```powershell
        hostname
        ```
    1. Rufen Sie die logischen Prozessorindizes ab, die für Hochleistungsnetzwerk-VMs reserviert werden sollen.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName <Output of hostname command>
        ```
        Beispielausgabe:
    
        ```powershell    
        [dbe-1csphq2.microsoftdatabox.com]: PS>hostname
        1CSPHQ2
        [dbe-1csphq2.microsoftdatabox.com]: P> Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName 1CSPHQ2
         { Numa Node #0 : CPUs [4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19] }
         { Numa Node #1 : CPUs [24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39] }
        
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Reservieren Sie vCPUs für Hochleistungsnetzwerk-VMs. Die Anzahl der hier reservierten vCPUs bestimmt die verfügbaren vCPUs, die den Hochleistungsnetzwerk-VMs zugewiesen werden können. Weitere Informationen zur Anzahl der Kerne, die von jeder Hochleistungsnetzwerk-VM-Größe verwendet werden, finden Sie unter [Unterstützte Größen von Hochleistungsnetzwerk-VMs](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes). Auf Ihrem Gerät befinden sich die Mellanox-Ports 5 und 6 in NUMA-Knoten 0.
    
        ```powershell
        Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated <Logical indexes from the Get-HcsNumaLpMapping cmdlet> -AssignAllCpusToRoot $false
        ```
    
        Nachdem dieser Befehl ausgeführt wurde, wird das Gerät automatisch neu gestartet.

        Beispielausgabe: 
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated "4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39" -AssignAllCpusToRoot $false
        Requested Configuration requires a reboot...
        Machine will reboot in some time. Please be patient.
        [dbe-1csphq2.microsoftdatabox.com]: PS>    
        ```
    
        > [!NOTE]
        > - Sie können alle logischen Indizes aus beiden NUMA-Knoten im Beispiel reservieren oder nur einen Teil davon. Wenn Sie einen Teil der Indizes reservieren möchten, wählen Sie die Indizes im Geräteknoten aus, an den eine Mellanox-Netzwerkschnittstelle angefügt wurde, um eine optimale Leistung zu erzielen. Für eine Azure Stack Edge Pro-GPU lautet der NUMA-Knoten mit der Mellanox-Netzwerkschnittstelle 0 (null).  
        > - Die Liste der logischen Indizes muss eine gekoppelte Sequenz einer ungeraden und einer geraden Zahl enthalten. Beispiel: ((4,5)(6,7)(10,11)). Der Versuch, eine Liste von Zahlen wie `5,6,7` oder Paaren wie `4,6` festzulegen, funktioniert nicht.
        > - Durch die aufeinanderfolgende Ausführung von zwei `Set-HcsNuma`-Befehlen, um vCPUs zuzuweisen, wird die Konfiguration zurückgesetzt. Geben Sie die CPUs auch nicht mithilfe des Cmdlets „Set-HcsNuma“ frei, wenn Sie eine Hochleistungsnetzwerk-VM bereitgestellt haben.
  
    1. Warten Sie, bis der Neustart des Geräts abgeschlossen ist. Sobald das Gerät ausgeführt wird, öffnen Sie eine neue PowerShell-Sitzung. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)
    
    1. Überprüfen Sie die vCPU-Reservierung.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType MinRootAware -NodeName <Output of hostname command> 
        ```
        Ihre festgelegten Indizes sollten nicht in der Ausgabe angezeigt werden. Wenn die festgelegten Indizes in der Ausgabe enthalten sind, wurde der `Set`-Befehl nicht erfolgreich abgeschlossen. Wiederholen Sie den Befehl. Wenn das Problem weiterhin besteht, wenden Sie sich an Microsoft-Support. 
    
        Hier sehen Sie eine Beispielausgabe.
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS> Get-HcsNumaLpMapping -MapType MinRootAware -NodeName 1CSPHQ2
        { Numa Node #0 : CPUs [0, 1, 2, 3] }
        { Numa Node #1 : CPUs [20, 21, 22, 23] }
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Starten Sie die VMs neu, die Sie im früheren Schritt beendet haben.
    
        ```powershell
        start-vm 
        ```
    <!-- Start-vm doesn't seem to work alone. Get-vm alone doesn't seem to return my running VM"VmId"--> 



## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Führen Sie die folgenden Schritte aus, um eine Hochleistungsnetzwerk-VM auf Ihrem Gerät zu erstellen.

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu [Add a VM image](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm-image) (VM-Image hinzufügen). Sie verwenden dieses VM-Image im nächsten Schritt, um eine VM zu erstellen.

1. Führen Sie alle Schritte unter [Hinzufügen einer VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm) mit dieser Konfigurationsanforderung aus. 

    Wählen Sie auf der Registerkarte „Allgemeine Informationen“ unter [DSv2 or F-series supported for HPN](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes) (Für Hochleistungsnetzwerk-VMs unterstützte DSv2- oder F-Serie) eine VM-Größe aus.

    ![Screenshot: Registerkarte „Grundlagen“ im Assistenten zum Hinzufügen virtueller Computer für Azure Stack Edge. Die Registerkarte „Grundlagen“ und die Schaltfläche „Weiter: Datenträger“ sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-1.png)

1. Schließen Sie die verbleibenden Schritte für die VM-Erstellung ab. Die Erstellung der VM dauert etwa 30 Minuten. 

    ![Screenshot, der die Registerkarte „Überprüfen + erstellen“ im Assistenten zum Hinzufügen von VMs für Azure Stack Edge zeigt. Die Schaltfläche „Erstellen“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-2.png)

1. Nachdem die VM erfolgreich erstellt wurde, wird Ihre neue VM im Bereich **Übersicht** angezeigt. Wählen Sie die neu erstellte VM aus, um zu **Virtuelle Computer** zu wechseln.

    ![Screenshot des Bereichs „Virtuelle Computer“ eines Azure Stack Edge-Geräts. Die Bezeichnung „Virtuelle Computer“ und ein VM-Eintrag sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-3.png)

    Wählen Sie die VM aus, um die Details anzuzeigen.

    ![Screenshot: Registerkarte „Details“ im Bereich „Übersicht“ für einen virtuellen Computer in Azure Stack Edge. Die VM-Größe und die IP-Adresse unter „Netzwerk“ sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-4.png)

    Sie verwenden die IP-Adresse für die Netzwerkschnittstelle, um eine Verbindung mit der VM herzustellen.

    > [!NOTE]
    > Wenn die vCPUs vor der Bereitstellung nicht für Hochleistungsnetzwerk-VMs reserviert wurden, tritt bei der Bereitstellung ein `FabricVmPlacementErrorInsufficientNumaNodeCapacity`-Fehler auf.
                                                                                                                                                                         
## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der VM-Bereitstellung](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Überwachen der VM-Aktivität auf Ihrem Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Überwachen von CPU und Arbeitsspeicher auf einer VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

