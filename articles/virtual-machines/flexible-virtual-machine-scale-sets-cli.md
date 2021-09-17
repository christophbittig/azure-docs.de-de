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
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699102"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Preview: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen


In diesem Artikel wird beschrieben, wie Sie das Azure CLI verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 

Achten Sie darauf, dass Sie die aktuelle Version von [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.


> [!IMPORTANT]
> Das Feature für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="register-for-flexible-orchestration-mode"></a>Registrieren für den Orchestrierungsmodus „Flexibel“

Bevor Sie VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ bereitstellen können, müssen Sie Ihr Abonnement zunächst für die Previewfunktion registrieren. Die Registrierung kann mehrere Minuten dauern.

Verwenden Sie [az feature register](/cli/azure/feature#az_feature_register), um die Vorschauversion für Ihr Abonnement zu aktivieren.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>Erste Schritte mit flexiblen Skalierungsgruppen

Erstellen einer flexiblen VM-Skalierungsgruppe mit der Azure CLI

### <a name="add-multiple-vms-to-a-scale-set"></a>Hinzufügen mehrerer VMs zu einer Skalierungsgruppe

Im folgenden Beispiel wird ein Profil für VMs (VM-Typ, Netzwerkkonfiguration usw.) und die Anzahl der zu erstellenden Instanzen (Anzahl der Instanzen = 2) angegeben.  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
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