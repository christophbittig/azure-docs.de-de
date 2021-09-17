---
title: Bereitstellen von Azure-Spot-VMs mithilfe der Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mithilfe der Befehlszeilenschnittstelle Azure-Spot-VMs bereitstellen, um Kosten zu sparen.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 81753681776ab4fd8ae42081ac3d5b0998d4f219
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692064"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Bereitstellen von Azure-Spot-VMs mithilfe der Azure-Befehlszeilenschnittstelle

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie [Azure Spot-VMs](../spot-vms.md) verwenden, profitieren Sie von unserer ungenutzten Kapazität und erzielen erhebliche Kosteneinsparungen. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VMs durch die Azure-Infrastruktur entfernt. Aus diesem Grund eignen sich Azure-Spot-VMs hervorragend für Workloads, die Unterbrechungen tolerieren, z. B. Batchverarbeitungsaufträge, Dev/Test-Umgebungen, umfangreiche Computeworkloads und mehr.

Die Preise für Azure-Spot-VMs variieren je nach Region und SKU. Weitere Informationen finden Sie unter den VM-Preisen für [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) und [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Sie haben die Möglichkeit, einen maximalen Preis festzulegen, den Sie pro Stunde für die VM bezahlen möchten. Der maximale Preis für eine Azure-Spot-VM kann in US-Dollar (USD) mit bis zu fünf Dezimalstellen festgelegt werden. Der Wert `0.98765` würde beispielsweise einem maximalen Preis von 0,98765 US-Dollar pro Stunde entsprechen. Wenn Sie den maximalen Preis auf `-1` festlegen, wird die VM nicht basierend auf dem Preis entfernt. Der Preis für die VM entspricht dem aktuellen Preis für Azure-Spot-VMs oder dem Preis für eine Standard-VM, je nachdem, welcher Preis niedriger ist, solange Kapazität und Kontingente verfügbar sind. Weitere Informationen zum Festlegen des maximalen Preises finden Sie unter [Azure-Spot-VMs – Preise](../spot-vms.md#pricing).

Der Vorgang zum Erstellen einer Azure-Spot-VM mithilfe der Azure-Befehlszeilenschnittstelle ist identisch mit dem im [Schnellstartartikel](./quick-create-cli.md) beschriebenen Vorgang. Fügen Sie einfach den Parameter „--priority Spot“ hinzu, legen Sie `--eviction-policy` entweder auf die Standardeinstellung „Deallocate“ oder auf `Delete` fest, und geben Sie einen maximalen Preis oder `-1` an. 


## <a name="install-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle

Zum Erstellen von Azure-Spot-VMs muss mindestens Version 2.0.74 der Azure-Befehlszeilenschnittstelle ausgeführt werden. Führen Sie **az --version** aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

Melden Sie sich mit [az login](/cli/azure/reference-index#az_login) in Azure an.

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Erstellen einer Azure-Spot-VM

Dieses Beispiel zeigt, wie Sie eine Azure-Spot-VM mit Linux bereitstellen, die nicht basierend auf dem Preis entfernt wird. Die Entfernungsrichtlinie wird so festgelegt, dass die Zuordnung der VM aufgehoben wird. Dadurch kann sie zu einem späteren Zeitpunkt neu gestartet werden. Wenn Sie die VM und den zugrunde liegenden Datenträger beim Entfernen löschen möchten, legen Sie `--eviction-policy` auf `Delete` fest.

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Nachdem die VM erstellt wurde, können Sie den maximalen Abrechnungspreis für alle VMs in der Ressourcengruppe abfragen.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulieren einer Entfernung

Sie können die Entfernung einer Azure Spot-VM mithilfe von REST, PowerShell oder der CLI simulieren, um zu testen, wie gut die Anwendung auf einen plötzlichen Entfernungsvorgang reagiert.

In den meisten Fällen ist für automatisierte Tests von Anwendungen die Nutzung der REST-API [Virtual Machines – Simulate Eviction](/rest/api/compute/virtualmachines/simulateeviction) (Simulieren der Entfernung von virtuellen Computern) sinnvoll. Bei REST bedeutet `Response Code: 204`, dass die simulierte Entfernung erfolgreich war. Wenn Sie die Reaktion Ihrer App auf die Entfernung der VM automatisieren möchten, können Sie die simulierte Entfernung mit dem Dienst [Geplantes Ereignis](scheduled-events.md) kombinieren.

Ein Video zur Nutzung von geplanten Ereignissen finden Sie unter [Azure Friday – Using Azure Scheduled Events to Prepare for VM Maintenance](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) (Azure Friday: Vorbereiten einer VM-Wartung mithilfe von geplanten Ereignissen in Azure).


### <a name="quick-test"></a>Schnelltest

Damit Sie schnell die Funktionsweise einer simulierten Entfernung testen können, führen Sie die Schritte zum Abfragen des Diensts für geplante Ereignisse aus, um eine simulierte Entfernung mithilfe von Azure CLI anzuzeigen.

Der Dienst für geplante Ereignisse wird für Ihren Dienst aktiviert, wenn Sie zum ersten Mal Ereignisse anfordern. 

Stellen Sie eine Remoteverbindung mit dem virtuellen Computer her, und öffnen Sie eine Eingabeaufforderung. 

Geben Sie in der Eingabeaufforderung Ihres virtuellen Computers folgenden Code ein:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Die erste Antwort kann bis zu zwei Minuten dauern. Anschließend sollten die Ausgaben nahezu sofort angezeigt werden.

Ist auf einem Computer die Azure CLI installiert (wie etwa auf Ihrem lokalen Computer), simulieren Sie die Entfernung mit [az vm simulate-eviction](/cli/azure/vm#az_vm_simulate_eviction). Ersetzen Sie die Namen der Ressourcengruppe und der VM durch Ihre eigenen. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
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

Sie können auch sehen, wann der virtuelle Computer entfernt wird, indem Sie das Kontrollkästchen aktivieren `"NotBefore"`. Der virtuelle Computer wird nicht vor dem angegebenen Zeitpunkt entfernt. Dies ist das Fenster, in dem Ihre Anwendung ordnungsgemäß geschlossen wird.


## <a name="next-steps"></a>Nächste Schritte

Sie können eine Azure-Spot-VM auch mithilfe von [Azure PowerShell](../windows/spot-powershell.md), über das [Portal](../spot-portal.md) oder mit einer [Vorlage](spot-template.md) erstellen.

Fragen Sie Preisinformationen ab, indem Sie die [Azure-Einzelhandelspreis-API](/rest/api/cost-management/retail-prices/azure-retail-prices) verwenden, um Informationen zu Azure-Spot-VMs zu erhalten. `meterName` und `skuName` enthalten beide `Spot`.

Informationen zu eventuell auftretenden Fehlern finden Sie unter [Fehlercodes](../error-codes-spot.md).