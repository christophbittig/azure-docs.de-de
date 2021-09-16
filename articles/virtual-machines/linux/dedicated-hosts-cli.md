---
title: Bereitstellen von VMs und Skalierungsgruppeninstanzen auf dedizierten Hosts mithilfe der CLI
description: Bereitstellen von VMs und Skalierungsgruppeninstanzen auf dedizierten Hosts über die Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: 3fbc398a937e1b36e88cfd14b0c123d554ca2cd7
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687909"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Bereitstellung auf dedizierten Hosts mithilfe der Azure CLI
 
**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs 

Dieser Artikel führt Sie durch die Erstellung eines [dedizierten Azure-Hosts](../dedicated-hosts.md) zum Hosten Ihrer virtuellen Computer (VMs).

Stellen Sie sicher, dass mindestens Version 2.16.0 der Azure-Befehlszeilenschnittstelle installiert ist und Sie mit `az login` bei einem Azure-Konto angemeldet sind.


## <a name="limitations"></a>Einschränkungen

- Die verfügbaren Größen und Hardwaretypen für dedizierte Hosts variieren je nach Region. Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

## <a name="create-resource-group"></a>Ressourcengruppe erstellen
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie die Ressourcengruppe mithilfe von „az group create“. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myDHResourceGroup* am Standort *USA, Osten* erstellt.

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus
```

## <a name="list-available-host-skus-in-a-region"></a>Auflisten der in einer Region verfügbaren Host-SKUs

Nicht alle Host-SKUs sind in allen Regionen und allen Verfügbarkeitszonen verfügbar.

Listen Sie die Hostverfügbarkeit und alle Angebotseinschränkungen auf, bevor Sie mit der Bereitstellung dedizierter Hosts beginnen.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

## <a name="create-a-host-group"></a>Erstellen einer Hostgruppe

Eine **Hostgruppe** ist eine Ressource, die eine Sammlung dedizierter Hosts darstellt. Sie erstellen eine Hostgruppe in einer Region und einer Verfügbarkeitszone und fügen ihr Hosts hinzu. Bei der Planung für Hochverfügbarkeit stehen zusätzliche Optionen zur Verfügung. Sie können eine oder beide der folgenden Optionen mit Ihren dedizierten Hosts verwenden:
- Ausdehnen über mehrere Verfügbarkeitszonen. In diesem Fall muss in jeder Zone, die Sie verwenden möchten, eine Hostgruppe vorhanden sein.
- Ausdehnen über mehrere Fehlerdomänen, die physischen Racks zugeordnet sind.

In beiden Fällen müssen Sie die Anzahl der Fehlerdomänen für Ihre Hostgruppe angeben. Wenn Ihre Gruppe keine Fehlerdomänen umfassen soll, verwenden Sie die Anzahl 1 von Fehlerdomänen.

Sie können auch sowohl Verfügbarkeitszonen als auch Fehlerdomänen verwenden.


In diesem Beispiel verwenden wir [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create), um eine Hostgruppe mit Verfügbarkeitszonen und Fehlerdomänen zu erstellen.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

Fügen Sie den Parameter `--automatic-placement true` hinzu, damit Ihre VMs und Skalierungsgruppeninstanzen automatisch auf Hosts innerhalb einer Hostgruppe platziert werden. Weitere Informationen finden Sie unter [Manuelle und automatische Platzierung im Vergleich](../dedicated-hosts.md#manual-vs-automatic-placement).


### <a name="other-examples"></a>Weitere Beispiele

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

## <a name="create-a-host"></a>Erstellen eines Hosts

Nun erstellen Sie einen dedizierten Host in der Hostgruppe. Zusätzlich zu einem Namen müssen Sie die SKU für den Host angeben. Die Host-SKU erfasst die unterstützte VM-Serie sowie die Hardwaregenerierung für Ihren dedizierten Host.

Weitere Informationen zu den Host-SKUs und Preisen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

Erstellen Sie mit [az vm host create](/cli/azure/vm/host#az_vm_host_create) einen Host. Beim Festlegen der Anzahl der Fehlerdomänen für die Hostgruppe werden Sie aufgefordert, die Fehlerdomäne für den Host anzugeben.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```



## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Erstellen Sie mithilfe von [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer in einem dedizierten Host. Wenn Sie beim Erstellen der Hostgruppe eine Verfügbarkeitszone angegeben haben, müssen Sie beim Erstellen des virtuellen Computers dieselbe Zone verwenden.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Verwenden Sie `--host`, anstatt die Hostgruppe mit `--host-group` anzugeben, um die VM auf einem bestimmten Host zu platzieren.

> [!WARNING]
> Wenn Sie einen virtuellen Computer auf einem Host erstellen, der nicht über ausreichende Ressourcen verfügt, wird der virtuelle Computer im Zustand „Fehler“ erstellt.

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Wenn Sie eine Skalierungsgruppe bereitstellen, geben Sie die Hostgruppe an.

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


## <a name="check-the-status-of-the-host"></a>Überprüfen des Status des Hosts

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

## <a name="export-as-a-template"></a>Exportieren als Vorlage
Sie können eine Vorlage exportieren, wenn Sie eine weitere Entwicklungsumgebung mit den gleichen Parametern oder eine entsprechende Produktionsumgebung erstellen möchten. Der Resource Manager verwendet die JSON-Vorlagen, die alle Parameter für Ihre Umgebung definieren. Sie erstellen ganze Umgebungen durch Verweisen auf diese JSON-Vorlage. Sie können JSON-Vorlagen manuell erstellen oder eine vorhandene Umgebung exportieren, um die JSON-Vorlage zu erstellen. Verwenden Sie [az group export](/cli/azure/group#az_group_export), um Ihre Ressourcengruppe zu exportieren.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json
```

Dieser Befehl erstellt die `myDHResourceGroup.json`-Datei in Ihrem aktuellen Arbeitsverzeichnis. Wenn Sie eine Umgebung anhand dieser Vorlage erstellen, werden Sie aufgefordert, alle Ressourcennamen anzugeben. Sie können diese Namen in Ihre Vorlagendatei eintragen, indem Sie den Parameter `--include-parameter-default-value` dem Befehl `az group export` hinzufügen. Bearbeiten Sie die JSON-Vorlage zum Angeben der Ressourcennamen, oder erstellen Sie eine Datei „parameters.json“, in der die Ressourcennamen angegeben werden.

Verwenden Sie zum Erstellen einer Umgebung aus Ihrer Vorlage [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create).

```azurecli-interactive
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myDHResourceGroup.json
```


## <a name="clean-up"></a>Bereinigung

Ihre dedizierten Hosts werden Ihnen auch dann in Rechnung gestellt, wenn keine virtuellen Computer bereitgestellt sind. Sie sollten, um Kosten zu sparen, alle Hosts löschen, die Sie zurzeit nicht verwenden.

Ein Host kann nur gelöscht werden, wenn er nicht mehr von virtuellen Computern verwendet wird. Löschen Sie die VMs mit [az vm delete](/cli/azure/vm#az_vm_delete).

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

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der Übersicht zu [dedizierten Hosts](../dedicated-hosts.md).

- Sie können dedizierte Hosts auch über das [Azure-Portal](../dedicated-hosts-portal.md) erstellen.

- Bei den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.
