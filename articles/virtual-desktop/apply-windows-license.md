---
title: Anwenden einer Windows-Lizenz auf virtuelle Sitzungshostcomputer – Azure
description: Hier wird beschrieben, wie die Windows-Lizenz für virtuelle Azure Virtual Desktop-Computer angewendet wird.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 856cefa33948f530ee859ee1eeadfaf2cdf73de6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471780"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Anwenden einer Windows-Lizenz auf virtuelle Sitzungshostcomputer

Kunden, die ordnungsgemäß für das Ausführen von Azure Virtual Desktop-Workloads lizenziert sind, können eine Windows-Lizenz auf Ihre virtuellen Sitzungshostcomputer anwenden und diese ausführen, ohne für eine weitere Lizenz zu bezahlen. Weitere Informationen finden Sie unter [Azure Virtual Desktop – Preise](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-azure-virtual-desktop-license"></a>Möglichkeiten zum Verwenden Ihrer Azure Virtual Desktop-Lizenz
Die Azure Virtual Desktop-Lizenzierung gestattet es Ihnen, eine Lizenz auf jeden virtuellen Windows- oder Windows Server-Computer anzuwenden, der als Sitzungshost in einem Hostpool registriert ist und Benutzerverbindungen empfängt. Diese Lizenz gilt nicht für virtuelle Computer, die als Dateifreigabeserver, Domänencontroller usw. ausgeführt werden.

Es gibt einige Möglichkeiten, die Azure Virtual Desktop-Lizenz zu verwenden:
- Sie können das [Azure Marketplace-Angebot](./create-host-pools-azure-marketplace.md) verwenden, um einen Hostpool und dessen virtuelle Sitzungshostcomputer zu erstellen. Virtuellen Computern, die auf diese Weise erstellt werden, wird die Lizenz automatisch zugewiesen.
- Sie können die [Azure Resource Manager-GitHub-Vorlage](./virtual-desktop-fall-2019/create-host-pools-arm-template.md) verwenden, um einen Hostpool und dessen virtuelle Sitzungshostcomputer zu erstellen. Virtuellen Computern, die auf diese Weise erstellt werden, wird die Lizenz automatisch zugewiesen.
- Sie können eine Lizenz auf einen vorhandenen virtuellen Sitzungshostcomputer anwenden. Führen Sie dazu zunächst die Schritte aus, die in [Erstellen eines Hostpools mit PowerShell](./create-host-pools-powershell.md) aufgeführt sind, um eine Hostpool und zugehörige virtuelle Computer zu erstellen, und kehren Sie dann zu diesem Artikel zurück, um zu erfahren, wie Sie die Lizenz anwenden.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Anwenden einer Windows-Lizenz auf einen virtuellen Sitzungshostcomputer
Stellen Sie sicher, dass Sie die [neueste Azure PowerShell-Version installiert und konfiguriert](/powershell/azure/)haben. Führen Sie das folgende PowerShell-Cmdlet aus, um die Windows-Lizenz anzuwenden:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Feststellen, ob für den virtuellen Sitzungshostcomputer der Lizenzierungsvorteil genutzt wird
Nachdem Sie Ihren virtuellen Computer bereitgestellt haben, führen Sie das folgende Cmdlet aus, um den Lizenztyp zu überprüfen:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Für einen virtuellen Sitzungshostcomputer mit der angewendeten Windows-Lizenz werden Informationen angezeigt, die in etwa wie folgt aussehen:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Für virtuelle Computer ohne die angewendete Windows-Lizenz werden Informationen angezeigt, die in etwa wie folgt aussehen:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Führen Sie das folgende Cmdlet aus, um eine Liste aller virtuellen Sitzungshostcomputer anzuzeigen, auf die in Ihrem Azure-Abonnement die Windows-Lizenz angewendet wurde:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Anforderungen für die Bereitstellung von Windows Server Remote Desktop Services

Wenn Sie Windows Server als Azure Virtual Desktop-Hosts in Ihrer Bereitstellung einsetzen, muss ein Remote-Desktop-Dienste-Lizenzserver von diesen virtuellen Computern aus erreichbar sein. Der Remote-Desktop-Dienste-Lizenzserver kann sich lokal oder in Azure befinden. Weitere Informationen finden Sie unter [Aktivieren des Remote-Desktop-Dienste-Lizenzservers](/windows-server/remote/remote-desktop-services/rds-activate-license-server).
