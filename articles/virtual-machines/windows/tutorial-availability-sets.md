---
title: Bereitstellen von virtuellen Computern in einer Verfügbarkeitsgruppe mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie Azure PowerShell zum Bereitstellen hochverfügbarer virtueller Computer in Verfügbarkeitsgruppen verwenden.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 369f0df05ff31eca2bf3323c672a7ff8b0ab42eb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689664"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Erstellen und Bereitstellen virtueller Computer in einer Verfügbarkeitsgruppe mithilfe von Azure PowerShell
**Gilt für**: :heavy_check_mark: Windows-VMs 

In diesem Tutorial erfahren Sie, wie Sie die Verfügbarkeit und Zuverlässigkeit Ihrer virtuellen Computer mithilfe von Verfügbarkeitsgruppen erhöhen. Verfügbarkeitsgruppen sorgen dafür, dass die von Ihnen in Azure bereitgestellten virtuellen Computer auf mehrere isolierte Hardwareknoten in einem Cluster verteilt werden. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verfügbarkeitsgruppe erstellen
> * Erstellen eines virtuellen Computers in einer Verfügbarkeitsgruppe
> * Überprüfen der verfügbaren VM-Größen
> * Überprüfen des Azure Advisor


## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen

Die Hardware an einem Standort ist in mehrere Updatedomänen und Fehlerdomänen unterteilt. Eine **Updatedomäne** ist eine Gruppe von virtuellen Computern und der zugrunde liegenden physischen Hardwarekomponenten, die gleichzeitig neu gestartet werden können. Virtuelle Computer in ein und derselben **Fehlerdomäne** nutzen denselben Speicher sowie eine Stromquelle und einen Netzwerkswitch gemeinsam.  

Sie können mithilfe von [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) eine Verfügbarkeitsgruppe erstellen. In diesem Beispiel beträgt die Anzahl von Update- und Fehlerdomänen *2*, und die Verfügbarkeitsgruppe hat den Namen *myAvailabilitySet*.

Erstellen Sie eine Ressourcengruppe.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Erstellen Sie mit [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) mit dem Parameter `-sku aligned` eine verwaltete Verfügbarkeitsgruppe.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Erstellen von virtuellen Computern in einer Verfügbarkeitsgruppe
Virtuelle Computer müssen in der Verfügbarkeitsgruppe erstellt werden, um sicherzustellen, dass sie ordnungsgemäß auf die Hardwarekomponenten verteilt werden. Nach der Erstellung kann einer Verfügbarkeitsgruppe kein vorhandener virtueller Computer mehr hinzugefügt werden. 


Verwenden Sie beim Erstellen eines virtuellen Computers mit [New-AzVM](/powershell/module/az.compute/new-azvm) den Parameter `-AvailabilitySetName`, um den Namen der Verfügbarkeitsgruppe anzugeben.

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) zuerst den Benutzernamen und das Kennwort des VM-Administrators fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie nun mit [New-AzVM](/powershell/module/az.compute/new-azvm) zwei virtuelle Computer in der Verfügbarkeitsgruppe.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Die Erstellung und Konfiguration der beiden virtuellen Computer dauert einige Minuten. Anschließend verfügen Sie über zwei virtuelle Computer, die auf die zugrunde liegenden Hardwarekomponenten verteilt sind. 

Im Portal sollten Sie bei der Verfügbarkeitsgruppe unter **Ressourcengruppen** > **myResourceGroupAvailability** > **myAvailabilitySet** sehen können, dass die virtuellen Computer auf zwei Fehler- und Updatedomänen verteilt sind.

![Verfügbarkeitsgruppe im Portal](./media/tutorial-availability-sets/fd-ud.png)

> [!NOTE]
> Unter bestimmten Umständen können zwei VMs in derselben Verfügbarkeitsgruppe dieselbe Fehlerdomäne gemeinsam aufweisen. Dies können Sie bestätigen, indem Sie zur betreffenden Verfügbarkeitsgruppe wechseln und die Spalte Fehlerdomäne überprüfen. Der Grund kann die folgende Abfolge beim Bereitstellen der VMs sein:
> 1. 1\. VM bereitstellen
> 1. 1\. VM beenden/freigeben
> 1. 2\. VM bereitstellen
Unter diesen Umständen wird der Betriebssystemdatenträger der 2. VM möglicherweise in der Fehlerdomäne der 1. VM erstellt, sodass die 2. VM ebenfalls in derselben Fehlerdomäne landet. Um dieses Problem zu vermeiden, wird empfohlen, die VMs zwischen den Bereitstellungen nicht zu beenden oder ihre Zuordnung aufzuheben.

## <a name="check-for-available-vm-sizes"></a>Prüfen der verfügbaren VM-Größen 

Wenn Sie einen virtuellen Computer in einer Verfügbarkeitsgruppe erstellen, müssen Sie wissen, welche VM-Größen in der Hardware verfügbar sind. Rufen Sie mit dem Befehl [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) alle verfügbaren Größen für virtuelle Computer ab, die Sie in der Verfügbarkeitsgruppe bereitstellen können.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Überprüfen des Azure Advisor 

Sie können mit dem Azure Advisor auch weitere Informationen zu Methoden zur Verbesserung der Verfügbarkeit Ihrer virtuellen Computer abrufen. Der Azure Advisor analysiert Ihre Konfiguration und Nutzungstelemetrie und macht anschließend Vorschläge zu Lösungen, die die Wirtschaftlichkeit, Leistung, Verfügbarkeit und Sicherheit Ihrer Azure-Ressourcen steigern können.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, wählen Sie die Option **Alle Dienste** aus, und geben Sie **Advisor** ein. Auf dem Advisor-Dashboard werden personalisierte Empfehlungen für das ausgewählte Abonnement angezeigt. Weitere Informationen finden Sie unter [Erste Schritte mit dem Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verfügbarkeitsgruppe erstellen
> * Erstellen eines virtuellen Computers in einer Verfügbarkeitsgruppe
> * Überprüfen der verfügbaren VM-Größen
> * Überprüfen des Azure Advisor

Im nächsten Tutorial erhalten Sie Informationen zu VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen einer VM-Skalierungsgruppe](tutorial-create-vmss.md)
