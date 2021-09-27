---
title: Bereitstellen von dedizierten Azure-Hosts
description: Stellen Sie VMs und Skalierungsgruppen auf dedizierten Hosts bereit.
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: brittanyrowe
ms.openlocfilehash: 3bae87c87c7ab8dd1090f40b4e0589c84e2c54ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557115"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>Bereitstellen von VMs und Skalierungsgruppen auf dedizierten Hosts

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Dieser Artikel führt Sie durch die Erstellung eines [dedizierten Azure-Hosts](dedicated-hosts.md) zum Hosten Ihrer virtuellen Computer (VMs) und Skalierungsgruppeninstanzen.


## <a name="limitations"></a>Einschränkungen

- Die verfügbaren Größen und Hardwaretypen für dedizierte Hosts variieren je nach Region. Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

## <a name="create-a-host-group"></a>Erstellen einer Hostgruppe

Eine **Hostgruppe** ist eine Ressource, die eine Sammlung dedizierter Hosts darstellt. Sie erstellen eine Hostgruppe in einer Region und einer Verfügbarkeitszone und fügen ihr Hosts hinzu. Bei der Planung für Hochverfügbarkeit stehen zusätzliche Optionen zur Verfügung. Sie können eine oder beide der folgenden Optionen mit Ihren dedizierten Hosts verwenden:
- Ausdehnen über mehrere Verfügbarkeitszonen. In diesem Fall muss in jeder Zone, die Sie verwenden möchten, eine Hostgruppe vorhanden sein.
- Ausdehnen über mehrere Fehlerdomänen, die physischen Racks zugeordnet sind.

In beiden Fällen müssen Sie die Anzahl der Fehlerdomänen für Ihre Hostgruppe angeben. Wenn Ihre Gruppe keine Fehlerdomänen umfassen soll, verwenden Sie die Anzahl 1 von Fehlerdomänen.

Sie können auch sowohl Verfügbarkeitszonen als auch Fehlerdomänen verwenden.

### <a name="portal"></a>[Portal](#tab/portal)

In diesem Beispiel erstellen Sie eine Hostgruppe mit einer Verfügbarkeitszone und zwei Fehlerdomänen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Suchen Sie nach **Hostgruppe**, und wählen Sie dann in den Ergebnissen **Hostgruppen** aus.
1. Wählen Sie auf der Seite **Hostgruppen** die Option **Erstellen** aus.
1. Wählen Sie das Abonnement aus, das Sie verwenden möchten, und wählen Sie dann **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.
1. Geben Sie *myDedicatedHostsRG* unter **Name** ein, und wählen Sie dann **OK** aus.
1. Geben Sie unter **Host group name** (Name der Hostgruppe) die Zeichenfolge *myHostGroup* ein.
1. Wählen Sie unter **Standort** die Option **USA, Osten** aus.
1. Wählen Sie unter **Verfügbarkeitszone** die Option **1** aus.
1. Wählen Sie für **Fault domain count** (Anzahl von Fehlerdomänen) die Option **2** aus.
1. Wählen Sie **Automatische Platzierung** aus, um virtuelle Computer und Skalierungsgruppeninstanzen automatisch einem verfügbaren Host in dieser Gruppe zuzuweisen.
1. Wählen Sie **Bewerten + erstellen** aus, und warten Sie auf die Überprüfung.
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus, um die Hostgruppe zu erstellen.

Die Erstellung der Hostgruppe sollte nur wenige Sekunden dauern.


### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Nicht alle Host-SKUs sind in allen Regionen und allen Verfügbarkeitszonen verfügbar. Sie können die Hostverfügbarkeit und alle Angebotseinschränkungen auflisten, bevor Sie mit der Bereitstellung dedizierter Hosts beginnen.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

In diesem Beispiel verwenden wir [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create), um eine Hostgruppe mit Verfügbarkeitszonen und Fehlerdomänen zu erstellen.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

Fügen Sie den Parameter `--automatic-placement true` hinzu, damit Ihre VMs und Skalierungsgruppeninstanzen automatisch auf Hosts innerhalb einer Hostgruppe platziert werden. Weitere Informationen finden Sie unter [Manuelle und automatische Platzierung im Vergleich](dedicated-hosts.md#manual-vs-automatic-placement).


**Weitere Beispiele**

Sie können auch [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) verwenden, um eine Hostgruppe in Verfügbarkeitszone 1 (und ohne Fehlerdomänen) zu erstellen.

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

Im Folgenden wird mit [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) eine Hostgruppe nur mit Fehlerdomänen erstellt. (Diese kann in Regionen verwendet werden, in denen keine Verfügbarkeitszonen unterstützt werden.)

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

In diesem Beispiel wird [New-AzHostGroup](/powershell/module/az.compute/new-azhostgroup) verwendet, um eine Hostgruppe in Zone 1 mit zwei Fehlerdomänen zu erstellen.


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Fügen Sie den Parameter `-SupportAutomaticPlacement true` hinzu, damit Ihre VMs und Skalierungsgruppeninstanzen automatisch auf Hosts innerhalb einer Hostgruppe platziert werden. Weitere Informationen finden Sie unter [Manuelle und automatische Platzierung im Vergleich](dedicated-hosts.md#manual-vs-automatic-placement).

---


## <a name="create-a-dedicated-host"></a>Erstellen eines dedizierten Hosts

Erstellen Sie nun einen dedizierten Host in der Hostgruppe. Zusätzlich zu einem Namen müssen Sie die SKU für den Host angeben. Die Host-SKU erfasst die unterstützte VM-Serie sowie die Hardwaregenerierung für Ihren dedizierten Host.

Weitere Informationen zu den Host-SKUs und Preisen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

Beim Festlegen der Anzahl der Fehlerdomänen für Ihre Hostgruppe müssen Sie die Fehlerdomäne für Ihren Host angeben.

### <a name="portal"></a>[Portal](#tab/portal)

1. Wählen Sie links oben **Ressource erstellen** aus.
1. Suchen Sie nach **Dedizierter Host**, und wählen Sie dann in den Ergebnissen **Dedizierter Host** aus.
1. Wählen Sie auf der Seite **Dedizierter Host** die Option **Erstellen** aus.
1. Wählen Sie das Abonnement aus, das Sie verwenden möchten.
1. Wählen Sie *myDedicatedHostsRG* unter **Ressourcengruppe** aus.
1. Geben Sie unter **Instanzdetails** die Zeichenfolge *myHost* für **Name** ein, und wählen Sie als Standort *USA, Osten* aus.
1. Wählen Sie in **Hardwareprofil** die Option *Standard Es3 family – Type 1* (Standard-Es3-Familie – Typ 1) unter **Größenfamilie**, *myHostGroup* unter **Hostgruppe** und dann *1* unter **Fehlerdomäne** aus. Behalten Sie in den restlichen Feldern die Standardwerte bei.
1. Wählen Sie nach Abschluss **Bewerten + erstellen** aus, und warten Sie auf die Validierung.
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus, um den Host zu erstellen.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Erstellen Sie mit [az vm host create](/cli/azure/vm/host#az_vm_host_create) einen Host. Beim Festlegen der Anzahl der Fehlerdomänen für die Hostgruppe werden Sie aufgefordert, die Fehlerdomäne für den Host anzugeben.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

In diesem Beispiel verwenden wir [New-AzHost](/powershell/module/az.compute/new-azhost), um einen Host zu erstellen und die Fehlerdomäne auf 1 zu setzen.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>Erstellen eines virtuellen Computers (VM)

Erstellen Sie nun einen virtuellen Computer (VM) auf dem Host.

### <a name="portal"></a>[Portal](#tab/portal)

1. Klicken Sie links oben im Azure-Portal auf **Ressource erstellen**.
1. Suchen Sie im Suchfeld über der Liste mit den Azure Marketplace-Ressourcen nach dem gewünschten Image, wählen Sie es aus, und klicken Sie anschließend auf **Erstellen**.
1. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie dann *myDedicatedHostsRG* als **Ressourcengruppe** aus.
1. Geben Sie unter **Instanzdetails** *myVM* als **Namen des virtuellen Computers** ein, und wählen Sie *USA, Osten* als Ihre **Ort** aus.
1. Wählen Sie unter **Verfügbarkeitsoptionen** die Option **Verfügbarkeitszone** aus, und wählen Sie in der Dropdownliste *1* aus.
1. Wählen Sie für die Größe **Größe ändern** aus. Wählen Sie in der Liste der verfügbaren Größen eine der Esv3-Serie aus, z. B. **Standard E2s v3**. Möglicherweise müssen Sie den Filter löschen, um alle verfügbaren Größen anzuzeigen.
1. Füllen Sie die restlichen Felder auf der Registerkarte **Grundeinstellungen** nach Bedarf aus.
1. Wenn Sie angeben möchten, welchen Host Sie für Ihre VM verwenden möchten, wählen Sie oben auf der Seite die Registerkarte **Erweitert** und im Abschnitt **Host** die Option *myHostGroup* für die **Hostgruppe** und *myHost* für den **Host** aus. Andernfalls wird Ihr virtueller Computer automatisch auf einem Host mit Kapazität platziert.
    ![Auswählen von Hostgruppe und Host](./media/dedicated-hosts-portal/advanced.png)
1. Belassen Sie die übrigen Standardeinstellungen, und wählen Sie dann die Schaltfläche **Überprüfen + erstellen** am unteren Rand der Seite aus.
1. Wenn eine Meldung über die erfolgreiche Validierung angezeigt wird, wählen Sie **Erstellen** aus.

Die Bereitstellung des virtuellen Computers dauert ein paar Minuten.


### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Erstellen Sie mithilfe von [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer in einem dedizierten Host. Wenn Sie beim Erstellen der Hostgruppe eine Verfügbarkeitszone angegeben haben, müssen Sie beim Erstellen des virtuellen Computers dieselbe Zone verwenden. Ersetzen Sie die Werte wie „Image“ und „Hostname“ durch Ihre eigenen Werte. Wenn Sie eine Windows-VM erstellen, entfernen Sie `--generate-ssh-keys`, um zur Eingabe eines Kennworts aufgefordert zu werden.

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Verwenden Sie `--host`, anstatt die Hostgruppe mit `--host-group` anzugeben, um die VM auf einem bestimmten Host zu platzieren.

> [!WARNING]
> Wenn Sie einen virtuellen Computer auf einem Host erstellen, der nicht über ausreichende Ressourcen verfügt, wird der virtuelle Computer im Zustand „Fehler“ erstellt.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) eine neue VM auf unserem Host. Da sich in diesem Beispiel die Hostgruppe in Zone 1 befindet, müssen wir die VM in Zone 1 erstellen.


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Wenn Sie einen virtuellen Computer auf einem Host erstellen, der nicht über ausreichende Ressourcen verfügt, wird der virtuelle Computer im Zustand „Fehler“ erstellt.

---

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Sie können auch eine Skalierungsgruppe auf Ihrem Host erstellen.

### <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie eine Skalierungsgruppe bereitstellen, geben Sie die Hostgruppe an.

1. Suchen Sie nach *Skalierungsgruppe*, und wählen Sie in der Liste **VM-Skalierungsgruppen** aus.
1. Klicken Sie auf **Hinzufügen**, um eine neue Skalierungsgruppe zu erstellen.
1. Füllen Sie die Felder auf der Registerkarte **Grundeinstellungen** wie gewohnt aus. Stellen Sie dabei jedoch sicher, dass Sie eine VM-Größe derselben Serie auswählen wie bei Ihrem dedizierten Host, z. B. **Standard E2s v3**.
1. Wählen Sie auf der Registerkarte **Erweitert** für **Zuweisungsalgorithmus** die Option **Maximale Zuweisung** aus.
1. Wählen Sie bei **Hostgruppe** die gewünschte Hostgruppe in der Dropdownliste aus. Wenn Sie die Gruppe vor kurzem erstellt haben, kann es eine Minute dauern, bis diese in der Liste hinzugefügt wurde.


### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Wenn Sie eine Skalierungsgruppe mit [az vmss create](/cli/azure/vmss#az_vmss_create) bereitstellen, geben Sie die Hostgruppe mit `--host-group` an. In diesem Beispiel wird das neueste Ubuntu LTS-Image bereitgestellt. Zur Bereitstellung eines Windows-Image ersetzen Sie den Wert von `--image`, und entfernen Sie `--generate-ssh-keys`, um zur Eingabe eines Kennworts aufgefordert zu werden.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Wenn Sie den Host, auf dem die Skalierungsgruppe bereitgestellt werden soll, manuell auswählen möchten, fügen Sie `--host` und den Namen des Hosts hinzu.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Stellen Sie mit [New-AzVMSS](/powershell/module/az.compute/new-azvmss) eine Skalierungsgruppe auf dem Host bereit. Wenn Sie eine Skalierungsgruppe bereitstellen, geben Sie die Hostgruppe an.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Wenn Sie den Host, auf dem die Skalierungsgruppe bereitgestellt werden soll, manuell auswählen möchten, fügen Sie `--host` und den Namen des Hosts hinzu.

---

## <a name="add-an-existing-vm"></a>Hinzufügen eines vorhandenen virtuellen Computers

Sie können einem dedizierten Host eine VM hinzufügen. Diese muss allerdings zuerst beendet bzw. Ihre Zuordnung muss aufgehoben werden. Vergewissern Sie sich vor dem Verschieben eines virtuellen Computers auf einen dedizierten Host, dass die VM-Konfiguration unterstützt wird:

- Die VM-Größe muss sich in der gleichen Größenfamilie befinden wie der dedizierte Host. Wenn der dedizierte Host z. B. DSv3 ist, kann die VM-Größe Standard_D4s_v3, aber nicht Standard_A4_v2 sein.
- Der virtuelle Computer muss sich in der gleichen Region befinden wie der dedizierte Host.
- Der virtuelle Computer darf nicht Teil einer Näherungsplatzierungsgruppe sein. Entfernen Sie den virtuellen Computer aus der Näherungsplatzierungsgruppe, bevor Sie ihn auf einen dedizierten Host verschieben. Weitere Informationen finden Sie unter [Verschieben einer vorhandenen VM aus einer Näherungsplatzierungsgruppe](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group).
- Der virtuelle Computer darf sich nicht in einer Verfügbarkeitsgruppe befinden.
- Wenn sich der virtuelle Computer in einer Verfügbarkeitszone befindet, muss es sich dabei um die gleiche Verfügbarkeitszone handeln wie bei der Hostgruppe. Die Verfügbarkeitszoneneinstellungen für den virtuellen Computer und die Hostgruppe müssen identisch sein.

### <a name="portal"></a>[Portal](#tab/portal2)

Verschieben Sie den virtuellen Computer mithilfe des [Portals](https://portal.azure.com) auf einen dedizierten Host.

1. Öffnen Sie die Seite für den virtuellen Computer.
1. Wählen Sie **Beenden** aus, um den virtuellen Computer zu beenden bzw. seine Zuordnung aufzuheben.
1. Wählen Sie im Menü auf der linken Seite die Option **Konfiguration** aus.
1. Wählen Sie in den entsprechenden Dropdownmenüs eine Hostgruppe und einen Host aus.
1. Wählen Sie abschließend im oberen Bereich der Seite **Speichern** aus.
1. Klicken Sie im linken Menü auf **Übersicht**, nachdem der virtuelle Computer dem Host hinzugefügt wurde.
1. Wählen Sie oben auf der Seite **Starten** aus, um den virtuellen Computer neu zu starten.


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Ersetzen Sie die Werte der Variablen durch Ihre eigenen Informationen.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>Überprüfen des Status des Hosts

Wenn Sie wissen müssen, wie viel Kapazität auf einem Host noch verfügbar ist, können Sie den Status überprüfen.

### <a name="portal"></a>[Portal](#tab/portal)

1. Suchen Sie nach dem Host, und wählen Sie ihn aus.
1. Scrollen Sie auf der Seite **Übersicht** für den Host nach unten, um die Liste der für den Host noch verfügbaren Größen anzuzeigen. Dies sollte in etwa wie folgt aussehen:

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="Überprüfen Sie die verfügbare Kapazität des Hosts auf der Seite „Übersicht“ für den Host.":::

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Sie können mit [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) den Integritätsstatus des Hosts sowie die Anzahl von virtuellen Computern überprüfen, die Sie noch auf dem Host bereitstellen können.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

Die Ausgabe sieht etwa wie folgt aus:

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Sie können mit [Get-AzHost](/powershell/module/az.compute/get-azhost) mit dem Parameter `-InstanceView` den Integritätsstatus des Hosts sowie die Anzahl von virtuellen Computern überprüfen, die Sie noch auf dem Host bereitstellen können.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Die Ausgabe sieht etwa wie folgt aus:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>Löschen von Hosts 
Ihre dedizierten Hosts werden Ihnen auch dann in Rechnung gestellt, wenn keine virtuellen Computer bereitgestellt sind. Sie sollten, um Kosten zu sparen, alle Hosts löschen, die Sie zurzeit nicht verwenden.

Ein Host kann nur gelöscht werden, wenn er nicht mehr von virtuellen Computern verwendet wird.

### <a name="portal"></a>[Portal](#tab/portal)

1. Suchen Sie nach dem Host, und wählen Sie ihn aus.
1. Wählen Sie im Menü auf der linken Seite die Option **Instanzen** aus.
1. Wählen Sie jeden virtuellen Computer aus, und löschen Sie ihn.
1. Wenn alle virtuellen Computer gelöscht wurden, wechseln Sie zur Seite **Übersicht** für den Host zurück, und wählen Sie im oberen Menü **Löschen** aus.
1. Nachdem der Host gelöscht wurde, öffnen Sie die Seite für die Hostgruppe, und wählen Sie **Hostgruppe löschen** aus.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

 Löschen Sie die VMs mit [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Nachdem Sie die VMs gelöscht haben, können Sie den Host mithilfe von [az vm host delete](/cli/azure/vm/host#az_vm_host_delete) löschen.

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

Wenn Sie alle Hosts gelöscht haben, können Sie die Hostgruppe mit [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete) löschen.

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

Sie können auch die gesamte Ressourcengruppe mit einem einzigen Befehl löschen. Dabei werden alle in der Gruppe erstellten Ressourcen gelöscht, einschließlich aller VMs, Hosts und Hostgruppen.

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Löschen Sie die VMs mit [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Nachdem Sie die VMs gelöscht haben, können Sie den Host mithilfe von [Remove-AzHost](/powershell/module/az.compute/remove-azhost) löschen.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Wenn Sie alle Hosts gelöscht haben, können Sie die Hostgruppe mit [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) löschen.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Sie können mithilfe von [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) auch die gesamte Ressourcengruppe mit einem einzigen Befehl löschen. Dabei werden alle in der Gruppe erstellten Ressourcen gelöscht, einschließlich aller VMs, Hosts und Hostgruppen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der Übersicht zu [dedizierten Hosts](dedicated-hosts.md).

- Bei den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.

