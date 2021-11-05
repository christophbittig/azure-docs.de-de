---
title: Starten und Beenden eines Knotenpools in Azure Kubernetes Service (AKS) (Vorschau)
description: Hier erfahren Sie, wie Sie einen Knotenpool in Azure Kubernetes Service (AKS) starten oder beenden.
services: container-service
ms.topic: article
ms.date: 10/25/2021
author: qpetraroia
ms.author: qpetraroia
ms.openlocfilehash: 4bf2eeda7b13d520bca769e9698e4ea5abebd584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095519"
---
# <a name="start-and-stop-an-azure-kubernetes-service-aks-node-pool-preview"></a>Starten und Beenden eines Knotenpools in Azure Kubernetes Service (AKS) (Vorschau)

Ihre AKS-Workloads müssen möglicherweise nicht kontinuierlich ausgeführt werden, wie z. B. ein Entwicklungscluster mit Knotenpools, auf denen bestimmte Workloads ausgeführt werden. Um Ihre Kosten zu optimieren können Sie Ihre Knotenpools in Ihrem AKS-Cluster vollständig deaktivieren (beenden), sodass Sie Computekosten sparen können.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart für die Verwendung mit der [Azure-Befehlszeilenschnittstelle][aks-quickstart-cli], [PowerShell][kubernetes-walkthrough-powershell] oder dem [Azure-Portal][aks-quickstart-portal].

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Außerdem benötigen Sie die Azure CLI-Erweiterung *aks-preview*. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-previewstartstopagentpool-preview-feature"></a>Registrieren der Previewfunktion `PreviewStartStopAgentPool`

Sie müssen darüber hinaus das Featureflag `PreviewStartStopAgentPool` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `PreviewStartStopAgentPool` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PreviewStartStopAgentPool"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PreviewStartStopAgentPool')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-node-pool"></a>Beenden eines AKS-Knotenpools

> [!IMPORTANT]
> Beim Starten/Beenden des Knotenpools wird folgendes Verhalten erwartet:
>
> * Sie können Systempools nicht beenden.
> * Spot-Knotenpools werden unterstützt.
> * Beendete Knotenpools können aktualisiert werden.
> * Der Cluster und der Knotenpool müssen ausgeführt werden.

Verwenden Sie `az aks nodepool stop`, um einen ausgeführten AKS-Knotenpool zu beenden. Im folgenden Beispiel wird der Knotenpool *testnodepool* beendet:

```azurecli-interactive
az aks nodepool stop --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Mit dem Befehl [az aks show][az-aks-show] können Sie überprüfen, wann Ihr Knotenpool beendet wurde, und sicherstellen, dass für `powerState` wie in der folgenden Ausgabe `Stopped` angezeigt wird:

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Stopped"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Wenn von `provisioningState` `Stopping` anzeigt wird, wurde Ihr Knotenpool noch nicht vollständig beendet.

## <a name="start-a-stopped-aks-node-pool"></a>Starten eines beendeten AKS-Knotenpools

Verwenden Sie `az aks nodepool start`, um einen beendeten AKS-Knotenpool zu starten. Im folgenden Beispiel wird der beendete Knotenpool mit dem Namen *testnodepool* gestartet:

```azurecli-interactive
az aks nodepool start --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Sie können überprüfen, ob Ihr Knotenpool mit [az aks show][az-aks-show] gestartet wurde und bestätigen, dass `Running` von `powerState` anzeigt wird. Beispiel:

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Running"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Wenn von `provisioningState` `Starting` anzeigt wird, wurde Ihr Knotenpool noch nicht vollständig gestartet.

---

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Skalieren von `User`-Pools auf 0 finden Sie unter [Skalieren von `User`-Pools auf 0](scale-cluster.md#scale-user-node-pools-to-0).
- Informationen zum Beenden Ihres Clusters finden Sie unter [Starten/Beenden des Clusters](start-stop-cluster.md).
- Informationen zum Einsparen von Kosten mithilfe von Spot-Instanzen finden Sie unter [Hinzufügen eines Spot-Knotenpools zu AKS](spot-node-pool.md).
- Weitere Informationen zu den Unterstützungsrichtlinien für AKS finden Sie unter [Unterstützungsrichtlinien für AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster