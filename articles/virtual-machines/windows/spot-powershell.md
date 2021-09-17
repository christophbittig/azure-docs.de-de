---
title: Bereitstellen von Azure-Spot-VMs mithilfe von PowerShell
description: Erfahren Sie, wie Sie Azure-Spot-VMs mit Azure PowerShell bereitstellen, um Kosten zu sparen.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee1f915045ed3272d8f93559efcf06c0c2bcc011
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689315"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Bereitstellen von Azure-Spot-VMs mithilfe von Azure PowerShell

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie [Azure Spot-VMs](../spot-vms.md) verwenden, profitieren Sie von unserer ungenutzten Kapazität und erzielen erhebliche Kosteneinsparungen. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VMs durch die Azure-Infrastruktur entfernt. Aus diesem Grund eignen sich Azure-Spot-VMs hervorragend für Workloads, die Unterbrechungen tolerieren, z. B. Batchverarbeitungsaufträge, Dev/Test-Umgebungen, umfangreiche Computeworkloads und mehr.

Die Preise für Azure-Spot-VMs variieren je nach Region und SKU. Weitere Informationen finden Sie unter den VM-Preisen für [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) und [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Weitere Informationen zum Festlegen des maximalen Preises finden Sie unter [Azure-Spot-VMs – Preise](../spot-vms.md#pricing).

Sie haben die Möglichkeit, einen maximalen Preis festzulegen, den Sie pro Stunde für die VM bezahlen möchten. Der maximale Preis für eine Azure-Spot-VM kann in US-Dollar (USD) mit bis zu fünf Dezimalstellen festgelegt werden. Der Wert `0.98765` würde beispielsweise einem maximalen Preis von 0,98765 US-Dollar pro Stunde entsprechen. Wenn Sie den maximalen Preis auf `-1` festlegen, wird die VM nicht basierend auf dem Preis entfernt. Der Preis für die VM entspricht dem aktuellen Preis für Spot-VMs oder dem Preis für eine Standard-VM, je nachdem, welcher Preis niedriger ist, solange Kapazitäten und Kontingente verfügbar sind.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie eine Spot-VM mithilfe von [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig), um die Konfiguration zu erstellen. Schließen Sie außerdem `-Priority Spot` ein, und legen Sie `-MaxPrice` auf eine der folgenden Optionen fest:
- `-1`, damit der virtuelle Computer nicht basierend auf dem Preis entfernt wird
- einen Dollarbetrag mit bis zu 5 Stellen. Beispielsweise bedeutet `-MaxPrice .98765`, dass die Zuordnung des virtuellen Computers aufgehoben wird, sobald der Preis für eine Spot-VM ca. 0,98765 USD pro Stunde übersteigt.


In diesem Beispiel wird eine Spot-VM erstellt, deren Zuordnung nicht basierend auf den Preisen aufgehoben wird. (Dies erfolgt nur, wenn Azure die Kapazität wieder benötigt.) Die Entfernungsrichtlinie wird so festgelegt, dass die Zuordnung der VM aufgehoben wird. Dadurch kann sie zu einem späteren Zeitpunkt neu gestartet werden. Wenn Sie die VM und den zugrunde liegenden Datenträger beim Entfernen löschen möchten, legen Sie `-EvictionPolicy` in `New-AzVMConfig` auf `Delete` fest.


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Nachdem die VM erstellt wurde, können Sie den maximalen Preis für alle VMs in der Ressourcengruppe abfragen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simulieren einer Entfernung

Sie können die Entfernung einer Azure Spot-VM mithilfe von REST, PowerShell oder der CLI simulieren, um zu testen, wie gut die Anwendung auf einen plötzlichen Entfernungsvorgang reagiert.

In den meisten Fällen ist für automatisierte Tests von Anwendungen die Nutzung der REST-API [Virtual Machines – Simulate Eviction](/rest/api/compute/virtualmachines/simulateeviction) (Simulieren der Entfernung von virtuellen Computern) sinnvoll. Bei REST bedeutet `Response Code: 204`, dass die simulierte Entfernung erfolgreich war. Wenn Sie die Reaktion Ihrer App auf die Entfernung der VM automatisieren möchten, können Sie die simulierte Entfernung mit dem Dienst [Geplantes Ereignis](scheduled-events.md) kombinieren.

Ein Video zur Nutzung von geplanten Ereignissen finden Sie unter [Azure Friday – Using Azure Scheduled Events to Prepare for VM Maintenance](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) (Azure Friday: Vorbereiten einer VM-Wartung mithilfe von geplanten Ereignissen in Azure).


### <a name="quick-test"></a>Schnelltest

Damit Sie schnell die Funktionsweise einer simulierten Entfernung testen können, führen Sie die Schritte zum Abfragen des Diensts für geplante Ereignisse aus, um eine simulierte Entfernung mithilfe von PowerShell anzuzeigen.

Der Dienst für geplante Ereignisse wird für Ihren Dienst aktiviert, wenn Sie zum ersten Mal Ereignisse anfordern. 

Stellen Sie eine Remoteverbindung mit dem virtuellen Computer her, und öffnen Sie eine Eingabeaufforderung. 

Geben Sie in der Eingabeaufforderung Ihres virtuellen Computers folgenden Code ein:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Die erste Antwort kann bis zu zwei Minuten dauern. Anschließend sollten die Ausgaben nahezu sofort angezeigt werden.

Ist auf einem Computer das Azure PowerShell-Modul installiert (wie etwa auf Ihrem lokalen Computer), simulieren Sie die Entfernung mit [Set-AzVM](/powershell/module/az.compute/set-azvm). Ersetzen Sie die Namen der Ressourcengruppe und der VM durch Ihre eigenen. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Bei erfolgreicher Durchführung der Anforderung enthält die Antwortausgabe `Status: Succeeded`.

Wechseln Sie schnell zurück zur Remoteverbindung mit Ihrer Azure Spot-VM, und führen Sie die Abfrage des Endpunkts für geplante Ereignisse noch mal aus. Wiederholen Sie den folgenden Befehl, bis Sie eine Ausgabe mit weiteren Informationen erhalten:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Sobald der Dienst für geplante Ereignisse die Benachrichtigung zur Entfernung erhält, wird Ihnen eine Antwort ähnlich der folgenden zurückgegeben:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Wie Sie sehen, wird als Ereignistyp `"EventType":"Preempt"` und als VM-Ressource `"Resources":["myspotvm"]` angezeigt. 

Zudem können Sie anhand des Werts `"NotBefore"` erkennen, wann der virtuelle Computer entfernt wird. Da der virtuelle Computer nicht vor der in `NotBefore` angegebenen Zeit entfernt wird, kann die Anwendung bis zu diesem Zeitpunkt ordnungsgemäß geschlossen werden.


## <a name="next-steps"></a>Nächste Schritte

Sie können eine Azure-Spot-VM auch über die [Azure-Befehlszeilenschnittstelle](../linux/spot-cli.md), das [Portal](../spot-portal.md) oder eine [Vorlage](../linux/spot-template.md) erstellen.

Fragen Sie Preisinformationen ab, indem Sie die [Azure-Einzelhandelspreis-API](/rest/api/cost-management/retail-prices/azure-retail-prices) verwenden, um Informationen zu Preisen von Azure-Spot-VMs zu erhalten. `meterName` und `skuName` enthalten beide `Spot`.

Informationen zu eventuell auftretenden Fehlern finden Sie unter [Fehlercodes](../error-codes-spot.md).
