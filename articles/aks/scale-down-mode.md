---
title: Verwenden Sie den Scale-down-Modus für Ihren Azure Kubernetes Service (AKS)-Cluster (Vorschau)
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie den Scale-down-Modus in Azure Kubernetes Service (AKS) verwenden können.
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 6bd1f4c70d9c427d7f6487040453e7ecae199717
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800116"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>Verwenden des Scale-down-Modus zum Löschen/Deallokieren von Knoten in Azure Kubernetes Service (AKS) (Vorschau)

Standardmäßig erfordern Scale-up-Vorgänge, die manuell oder durch den Cluster-Autoscaler durchgeführt werden, die Zuweisung und Bereitstellung neuer Knoten, und Scale-down-Vorgänge löschen Knoten. Mit dem Scale-down-Modus können Sie entscheiden, ob Sie die Knoten in Ihrem Azure Kubernetes Service (AKS)-Cluster bei der Verkleinerung löschen oder freigeben möchten. 

Wenn sich eine Azure-VM im (freigegebenen) Zustand `Stopped` befindet, werden Ihnen die VM-Rechenressourcen nicht in Rechnung gestellt. Sie müssen jedoch weiterhin für alle an die VM angeschlossenen Betriebssystem- und Datenspeicherplatten bezahlen. Das bedeutet auch, dass die Container-Images auf diesen Knoten erhalten bleiben. Weitere Informationen finden Sie unter [Zustände und Abrechnung von virtuellen Maschinen][state-billing-azure-vm] in Azure. Dieses Verhalten ermöglicht eine schnellere Arbeitsgeschwindigkeit, da Ihre Bereitstellung zwischengespeicherte Bilder nutzt. Mit dem Scale-down-Modus müssen Sie keine Knoten mehr im Voraus bereitstellen und keine Container-Images mehr abrufen, wodurch Sie Rechenkosten sparen.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

> [!WARNING]
> Um alle deallokierten VMs zu erhalten, müssen Sie den Scale-down-Modus auf „Zuordnung aufheben" setzen. Dazu gehören auch VMs, die mithilfe von IaaS-APIs (Virtual Machine Scale Set APIs) deallokiert wurden. Wenn Sie den Verkleinerungsmodus auf Löschen setzen, werden alle nicht zugeordneten VMs entfernt.

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

### <a name="limitations"></a>Einschränkungen

- [Ephemere Betriebssystemdisketten][ephemeral-os] werden nicht unterstützt. Achten Sie darauf, dass Sie bei der Erstellung eines Clusters oder Knotenpools verwaltete Betriebssystemplatten über `--node-osdisk-type Managed` angeben.
- [Spot Node Pools][spot-node-pool] werden nicht unterstützt.

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Sie benötigen außerdem die Azure-CLI-Erweiterung *aks-preview* in der Version 0.5.30 oder höher. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>Registrieren der Previewfunktion `AKS-ScaleDownModePreview`

Sie müssen darüber hinaus das Featureflag `AKS-ScaleDownModePreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `AKS-ScaleDownModePreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>Verwendung des Verkleinerungsmodus zur Freigabe von Knoten bei der Verkleinerung

Durch die Einstellung von `--scale-down-mode Deallocate` werden die Knoten während eines Scale-Downs Ihres Clusters/Knotenpools freigegeben. Alle freigegebenen Knoten werden gestoppt. Wenn Ihr Cluster/Knotenpool skaliert werden muss, werden die freigegebenen Knoten zuerst gestartet, bevor neue Knoten bereitgestellt werden.

In diesem Beispiel erstellen wir einen neuen Knotenpool mit 20 Knoten und legen fest, dass die Knoten bei einer Verkleinerung über `--scale-down-mode Deallocate` freigegeben werden.

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

Wenn wir den Knotenpool skalieren und die Knotenanzahl auf 5 ändern, werden 15 Knoten freigegeben.

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>Löschen von zuvor freigegebenen Knoten

Um Ihre deallokierten Knoten zu löschen, können Sie Ihren Scale-down-Modus auf ändern, indem Sie`Delete``--scale-down-mode Delete` . Die 15 nicht zugewiesenen Knoten werden nun gelöscht.

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

> [!NOTE]
> Wenn Sie Ihren Scale-Down-Modus von `Deallocate` auf `Delete` und dann wieder auf `Deallocate` ändern, werden alle deallokierten Knoten gelöscht, während Ihr Knotenpool im Scale-Down-Modus `Deallocate` bleibt.

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>Verwendung des Scale-down-Modus zum Löschen von Knoten bei Scale-down

Das Standardverhalten von AKS ohne die Verwendung des Scale-down-Modus ist das Löschen der Knoten, wenn Sie Ihren Cluster verkleinern. Im Scale-down-Modus kann dies explizit durch die Einstellung `--scale-down-mode Delete`.

In diesem Beispiel erstellen wir einen neuen Knotenpool und legen fest, dass unsere Knoten bei einer Verkleinerung über `--scale-down-mode Delete` gelöscht werden sollen. Skalierungsvorgänge werden über den Cluster-Autoscaler abgewickelt.

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- Um mehr über das Upgrade Ihres AKS-Clusters zu erfahren, siehe [Upgrade eines AKS-Clusters][aks-upgrade]
- Weitere Informationen über den automatischen Cluster-Scaler finden Sie unter [Automatische Skalierung eines Clusters zur Erfüllung der Anwendungsanforderungen auf AKS][cluster-autoscaler]

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md