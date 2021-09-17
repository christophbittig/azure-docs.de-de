---
title: Grundlegendes zu Instanz-IDs für virtuelle Computer in Azure-VM-Skalierungsgruppen
description: Grundlegendes zu Instanz-IDs für virtuelle Azure-VM-Skalierungsgruppen und die verschiedenen Arten ihres Auftretens
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 7b9b088eb3e9524e711ba4068c49f172e65b1be8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697458"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Grundlegendes zu Instanz-IDs für virtuelle Computer in Azure-VM-Skalierungsgruppen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel werden Instanz-IDs für Skalierungsgruppen und die verschiedenen Arten beschrieben, wie sie in Erscheinung treten.

## <a name="scale-set-instance-ids"></a>Instanz-IDs in einer Skalierungsgruppe

Jeder virtuelle Computer in einer Skalierungsgruppe erhält eine Instanz-ID, die ihn eindeutig identifiziert. Diese Instanz-ID wird in den Skalierungsgruppen-APIs für Vorgänge auf einem bestimmten virtuellen Computer in der Skalierungsgruppe verwendet. Beispielsweise können Sie eine bestimmte Instanz-ID zum Durchführen eines Reimaging angeben, wenn die Reimaging-API verwendet wird:

REST-API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (Weitere Informationen finden Sie in der [REST-API-Dokumentation](/rest/api/compute/virtualmachinescalesetvms/reimage).)

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Weitere Informationen finden Sie in der [PowerShell-Dokumentation](/powershell/module/az.compute/set-azvmssvm).)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Weitere Informationen finden Sie in der [Dokumentation zur Befehlszeilenschnittstelle](/cli/azure/vmss).)

Sie können die Liste der Instanz-IDs abrufen, indem Sie alle Instanzen in einer Skalierungsgruppe auflisten:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Weitere Informationen finden Sie in der [REST-API-Dokumentation](/rest/api/compute/virtualmachinescalesetvms/list).)

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Weitere Informationen finden Sie in der [PowerShell-Dokumentation](/powershell/module/az.compute/get-azvmssvm).)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Weitere Informationen finden Sie in der [Dokumentation zur Befehlszeilenschnittstelle](/cli/azure/vmss).)

Sie können auch [resources.azure.com](https://resources.azure.com) oder die [Azure-SDKs](https://azure.microsoft.com/downloads/) zum Auflisten der virtuellen Computer in einer Skalierungsgruppe verwenden.

Die genaue Darstellung der Ausgabe hängt von den für den Befehl angegebenen Optionen ab. Nachfolgend sehen Sie eine Beispielausgabe der CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Wie Sie sehen, ist die Eigenschaft „instanceId“ nur eine Dezimalzahl. Die Instanz-ID kann für neue Instanzen wiederverwendet werden, nachdem alte Instanzen gelöscht wurden.

>[!NOTE]
> Es gibt **keine Garantie** darüber, wie die Instanz-IDs den virtuellen Computern in der Skalierungsgruppe zugewiesen werden. Bisweilen scheinen sie fortlaufend anzusteigen, dies ist jedoch nicht immer der Fall. Übernehmen Sie eine Abhängigkeit nicht auf bestimmte Art und Weise, die in der Instanz-IDs an die virtuellen Computer zugewiesen sind.

## <a name="scale-set-vm-names"></a>Namen von Skalierungsgruppen-VMs

Die Beispielausgabe oben enthält auch einen „Namen“ für den virtuellen Computer. Dieser Name hat das Format „{Name-der-Skalierungsgruppe}_{Instanz-ID}“. Dieser Name wird im Azure-Portal angezeigt, wenn Sie Instanzen in einer Skalierungsgruppe auflisten:

![Screenshot einer Liste der Instanzen in einer VM-Skalierungsgruppe im Azure-Portal](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Der Teil {Instanz-ID} des Namens ist dieselbe Dezimalzahl wie die zuvor erläuterte Eigenschaft „instanceId“.

## <a name="instance-metadata-vm-name"></a>Name der Instanzmetadaten eines virtuellen Computers

Wenn Sie [Instanzmetadaten](../virtual-machines/windows/instance-metadata-service.md) direkt auf einem virtuellen Computer in einer Skalierungsgruppe abfragen, wird in der Ausgabe ein „Name“ angezeigt:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Dieser Name ist mit dem zuvor erläuterten Namen identisch.

## <a name="scale-set-vm-computer-name"></a>Computername der Skalierungsgruppen-VM

Jedem virtuellen Computer in einer Skalierungsgruppe wird außerdem ein Computername zugewiesen. Dieser Computername ist der Hostname des virtuellen Computers in der [von Azure bereitgestellten DNS-Namensauflösung innerhalb des virtuellen Netzwerks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Dieser Computername hat das Format „{Computernamenpräfix}{Basis-36-Instanz-ID}“.

Die {Basis-36-Instanz-ID} hat die [Basis 36](https://en.wikipedia.org/wiki/Base36) und besteht immer aus sechs Ziffern. Wenn die Basis 36-Darstellung der Zahl weniger als sechs Ziffern erfordert, wird die {Basis-36-Instanz-ID} mit Nullen ergänzt, damit sie sechs Ziffern aufweist. Beispiel: Eine Instanz mit dem {Computername-Präfix}-Wert „nsgvmss“ und der Instanz-ID 85 erhält den Computernamen „nsgvmss00002D“.

>[!NOTE]
> Das Computernamenpräfix ist eine Eigenschaft des Skalierungsgruppenmodells, die Sie festlegen können, sodass sie sich vom Skalierungsgruppennamen selbst unterscheidet.
