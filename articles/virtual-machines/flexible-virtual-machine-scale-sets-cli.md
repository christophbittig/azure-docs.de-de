---
title: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI
description: Erfahren Sie, wie Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe im Azure-CLI erstellen.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: a0c6d8dc1f9e032ca76393c52be76d1803873f8b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166413"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen


In diesem Artikel wird beschrieben, wie Sie das Azure CLI verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 

Achten Sie darauf, dass Sie die aktuelle Version von [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="get-started-with-flexible-scale-sets"></a>Erste Schritte mit flexiblen Skalierungsgruppen

Erstellen einer flexiblen VM-Skalierungsgruppe mit der Azure CLI

### <a name="add-multiple-vms-to-a-scale-set"></a>Hinzufügen mehrerer VMs zu einer Skalierungsgruppe

Im folgenden Beispiel wird ein Profil für VMs (VM-Typ, Netzwerkkonfiguration usw.) und die Anzahl der zu erstellenden Instanzen (Anzahl der Instanzen = 2) angegeben.  

```azurecli-interactive
az vmss create
--name $vmssName
--resource-group $rg
--image UbuntuLTS
--instance-count 2
--orchestration-mode flexible
```

### <a name="add-a-single-vm-to-a-scale-set"></a>Hinzufügen einer VM zu einer Skalierungsgruppe

Das folgende Beispiel zeigt die Erstellung einer flexiblen Skalierungsgruppe ohne VM-Profil, wobei die Anzahl der Fehlerdomänen auf 1 gesetzt wird. Eine VM wird erstellt und dann der flexiblen Skalierungsgruppe hinzugefügt.

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine flexible Skalierungsgruppe im Azure-Portal erstellen.](flexible-virtual-machine-scale-sets-portal.md)