---
title: Erstellen einer Windows-VM mithilfe einer Vorlage in Azure
description: Verwenden Sie eine Resource Manager-Vorlage und PowerShell, um ganz einfach einen neuen virtuellen Windows-Computer zu erstellen.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 1be03bee5005cbcc4b2eec3d469631d504d3c504
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690153"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Erstellen eines virtuellen Windows-Computer mit einer Resource Manager-Vorlage

**Gilt für**: :heavy_check_mark: Windows-VMs 

Es wird beschrieben, wie Sie einen virtuellen Windows-Computer mit einer Azure Resource Manager-Vorlage und mit Azure PowerShell über die Azure Cloud Shell erstellen. Mit der in diesem Artikel verwendeten Vorlage wird ein einzelner virtueller Computer mit Windows Server in einem neuen virtuellen Netzwerk mit nur einem Subnetz bereitgestellt. Informationen zum Erstellen eines virtuellen Linux-Computers finden Sie unter [Erstellen eines virtuellen Linux-Computers mithilfe von Azure Resource Manager-Vorlagen](../linux/create-ssh-secured-vm-from-template.md).

Eine Alternative ist die Bereitstellung der Vorlage vom Azure-Portal aus. Um die Vorlage im Portal zu öffnen, wählen Sie die Schaltfläche **In Azure bereitstellen** aus.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Die Erstellung eines virtuellen Azure-Computers umfasst normalerweise zwei Schritte:

- Erstellen Sie eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden.
- Erstellen Sie eine VM.

Im folgenden Beispiel wird eine VM über eine [Azure-Schnellstartvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json) erstellt. Hier ist eine Kopie der Vorlage angegeben:

[!code-json[create-windows-vm](~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json)]

Wählen Sie zum Ausführen des PowerShell-Skripts die Option **Testen Sie es.** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Wenn Sie nicht die Azure Cloud Shell verwenden, sondern PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial das Azure PowerShell-Modul verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Im vorherigen Beispiel haben Sie eine in GitHub gespeicherte Vorlage angegeben. Sie können eine Vorlage außerdem herunterladen oder erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

Hier sind einige zusätzlichen Ressourcen:

- Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](../../azure-resource-manager/index.yml).
- Die Schemas zu virtuellen Azure-Computern finden Sie in der [Azure-Vorlagenreferenz](/azure/templates/microsoft.compute/allversions).
- Weitere Beispiele für VM-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Verbinden mit dem virtuellen Computer

Im letzten PowerShell-Befehl aus dem vorherigen Skript wird der Name des virtuellen Computers angezeigt. Informationen zum Herstellen einer Verbindung mit dem virtuellen Computer finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](./connect-logon.md).

## <a name="next-steps"></a>Nächste Schritte

- Falls bei der Bereitstellung Probleme aufgetreten sind, können Sie den Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) lesen.
- Erfahren Sie, wie Sie einen virtuellen Computer erstellen und verwalten, indem Sie [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md) durcharbeiten.

Wenn Sie weitere Informationen zum Erstellen von Vorlagen benötigen, sehen Sie sich die JSON-Syntax und die Eigenschaften für die von Ihnen bereitgestellten Ressourcentypen an:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
