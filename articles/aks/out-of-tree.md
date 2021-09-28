---
title: Aktivieren von Cloud Controller Manager (Vorschau)
description: Erfahren Sie, wie Sie den strukturexternen Cloudanbieter aktivieren.
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 98f8fe2e00a7671078da7dae2174401b6c33fd30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667614"
---
# <a name="enable-cloud-controller-manager-preview"></a>Aktivieren von Cloud Controller Manager (Vorschau)

Als Cloudanbieter arbeitet Microsoft Azure eng mit der Kubernetes-Community zusammen, um unsere Infrastruktur im Auftrag von Benutzern zu unterstützen.

Derzeit erfolgt die Cloudanbieterintegration innerhalb von Kubernetes „strukturintern“ (in-tree), wobei alle Änderungen an cloudspezifischen Features dem standardmäßigen Kubernetes-Releasezyklus folgen müssen.  Wenn wir Probleme finden, Probleme beheben oder Erweiterungen einführen, muss dies innerhalb des Releasezyklus der Kubernetes-Community geschehen.

Die Kubernetes-Community führt jetzt ein strukturexternes Modell (out-of-tree) ein, bei dem die Cloudanbieter ihre Releases unabhängig vom Kubernetes-Releasezeitplan über die Komponente [cloud-provider-azure][cloud-provider-azure] steuern.  Wir haben die CSI-Treiber (Cloud Storage Interface) bereits als Standardeinstellung in Kubernetes, Version 1.21 und höher, eingeführt.

> [!Note]
> Wenn Sie Cloud Controller Manager in Ihrem AKS-Cluster aktivieren, werden dadurch auch die strukturexternen CSI-Treiber aktiviert.

Cloud Controller Manager ist der Standardcontroller von Kubernetes 1.22, der von AKS unterstützt wird.


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Die folgenden Ressourcen müssen installiert sein:

* Die Azure-CLI
* Erweiterung `aks-preview`, Version 0.5.5 oder höher
* Kubernetes, Version 1.20.x oder höher


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>Registrieren des `EnableCloudControllerManager`-Featureflags

Wenn Sie das Cloud Controller Manager-Feature verwenden möchten, müssen Sie das `EnableCloudControllerManager`-Featureflag für Ihr Abonnement aktivieren. 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>Erstellen eines AKS-Clusters mit Cloud Controller Manager

Um einen Cluster über Cloud Controller Manager zu erstellen, übergeben Sie `EnableCloudControllerManager=True` als Kundenheader mithilfe der Azure CLI an die Azure-API.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>Aktualisieren eines AKS-Clusters auf Cloud Controller Manager

Um ein Clusterupgrade für die Verwendung von Cloud Controller Manager durchzuführen, übergeben Sie `EnableCloudControllerManager=True` als Kundenheader mithilfe der Azure CLI an die Azure-API.

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu CSI-Treibern und zum Standardverhalten für Kubernetes-Versionen höher als 1.21 finden Sie in unserer [Dokumentation][csi-docs].

- Weitere Informationen zur Ausrichtung der Kubernetes-Community im Hinblick auf strukturexterne Anbieter finden Sie im [Blogbeitrag der Community][community-blog].


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure
