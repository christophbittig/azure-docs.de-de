---
title: Starten und Beenden einer AKS-Instanz (Azure Kubernetes Service)
description: Hier erfahren Sie, wie Sie eine AKS-Instanz (Azure Kubernetes Service) starten und beenden.
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8a19cc69d14e32a20819618efcf60594aef34be9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371978"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Beenden und Starten eines AKS-Clusters (Azure Kubernetes Service)

Ihre AKS-Workloads müssen möglicherweise nicht kontinuierlich ausgeführt werden – beispielsweise bei einem Entwicklungscluster, der nur während der Geschäftszeiten verwendet wird. In diesem Fall können Zeiten auftreten, in denen sich Ihr AKS-Cluster (Azure Kubernetes Service) im Leerlauf befindet und nur die Systemkomponenten ausgeführt werden. Dann können Sie den Speicherbedarf des Clusters reduzieren, indem Sie [alle `User`-Knotenpools auf 0 skalieren](scale-cluster.md#scale-user-node-pools-to-0). Der [`System`-Pool](use-system-pools.md) wird jedoch weiterhin für die Ausführung der Systemkomponenten benötigt, solange der Cluster ausgeführt wird.
Um Ihre Kosten während dieser Zeiträume weiter zu optimieren, können Sie Ihren Cluster vollständig ausschalten (beenden). Durch diese Aktion werden die Steuerungsebene und die Agent-Knoten vollständig beendet, sodass Sie alle Computerkosten sparen können, während Sie alle Ihre Objekte (mit Ausnahme eigenständiger Pods) und den Clusterstatus beibehalten, die gespeichert werden, wenn Sie sie erneut starten. So können Sie nach einem Wochenende genau dort weitermachen, wo Sie aufgehört haben. Sie haben auch die Möglichkeit, Ihren Cluster immer nur dann auszuführen, wenn Sie Ihre Batchaufträge ausführen.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart für die Verwendung mit der [Azure-Befehlszeilenschnittstelle][aks-quickstart-cli], [PowerShell][kubernetes-walkthrough-powershell] oder dem [Azure-Portal][aks-quickstart-portal].

### <a name="limitations"></a>Einschränkungen

Wenn Sie das Feature zum Starten/Beenden von Clustern verwenden, gelten die folgenden Einschränkungen:

- Diese Funktion wird nur für Back-End-Cluster für Virtual Machine Scale Sets unterstützt.
- Der Clusterstatus eines beendeten AKS-Clusters wird bis zu 12 Monate beibehalten. Wenn Ihr Cluster länger als 12 Monate angehalten wird, kann der Clusterstatus danach nicht mehr wiederhergestellt werden. Weitere Informationen finden Sie unter [Unterstützungsrichtlinien für Azure Kubernetes Service](support-policies.md).
- Nun ein angehaltener AKS-Cluster kann gestartet oder gelöscht werden. Wenn Sie einen Vorgang wie eine Skalierung oder ein Upgrade ausführen möchten, müssen Sie zuerst den Cluster starten.
- Die vom Kunden bereitgestellten PrivateEndpoints, die mit dem privaten Cluster verknüpft sind, müssen gelöscht und erneut erstellt werden, wenn Sie einen beendeten AKS-Cluster starten.
- Da der Beendigungsprozess alle Knoten leert, werden alle eigenständigen Pods (d. h. Pods, die nicht von einer Bereitstellung verwaltet werden, StatefulSet, DaemonSet, Auftrag usw.) gelöscht.

## <a name="stop-an-aks-cluster"></a>Beenden eines AKS-Clusters

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit dem Befehl `az aks stop` können Sie die Knoten und die Steuerungsebene eines AKS-Clusters beenden, der gerade ausgeführt wird. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* beendet.

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Mit dem Befehl [az aks show][az-aks-show] können Sie überprüfen, wann Ihr Cluster beendet wurde, und sicherstellen, dass für `powerState` wie in der folgenden Ausgabe `Stopped` angezeigt wird:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Wenn für `provisioningState` `Stopping` angezeigt wird, bedeutet das, dass Ihr Cluster noch nicht vollständig beendet wurde.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Mit dem Cmdlet [Stop-AzAksCluster][stop-azakscluster] können Sie die Knoten und die Steuerungsebene eines AKS-Clusters beenden, der gerade ausgeführt wird. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* beendet.

```azurepowershell-interactive
Stop-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Mit dem Cmdlet [Get-AzAksCluster][get-azakscluster] können Sie überprüfen, ob Ihr Cluster beendet wurde, und sicherstellen, dass für `ProvisioningState` wie in der folgenden Ausgabe `Stopped` angezeigt wird:

```Output
ProvisioningState       : Stopped
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Wenn für `ProvisioningState` `Stopping` angezeigt wird, bedeutet das, dass Ihr Cluster noch nicht vollständig beendet wurde.

---

> [!IMPORTANT]
> Wenn Sie [Budgets für die Unterbrechung von Pods](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) verwenden, kann der Beendigungsvorgang länger dauern, da der Ausgleichsprozess mehr Zeit in Anspruch nimmt.

## <a name="start-an-aks-cluster"></a>Starten eines AKS-Clusters

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit dem Befehl `az aks start` können Sie die Knoten und die Steuerungsebene eines beendeten AKS-Clusters starten. Der Cluster wird mit dem vorherigen Status der Steuerungsebene und der vorherigen Anzahl von Agentknoten neu gestartet.
Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* gestartet.

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Mit dem Befehl [az aks show][az-aks-show] können Sie überprüfen, wann Ihr Cluster gestartet wurde, und sicherstellen, dass für `powerState` wie in der folgenden Ausgabe `Running` angezeigt wird:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Wenn für `provisioningState` `Starting` angezeigt wird, bedeutet das, dass Ihr Cluster noch nicht vollständig gestartet wurde.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Mit dem Cmdlet [Start-AzAksCluster][start-azakscluster] können Sie die Knoten und die Steuerungsebene eines AKS-Clusters starten, der beendet wurde. Der Cluster wird mit dem vorherigen Status der Steuerungsebene und der vorherigen Anzahl von Agentknoten neu gestartet.
Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* gestartet.

```azurepowershell-interactive
Start-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Mit dem Cmdlet [Get-AzAksCluster][get-azakscluster] können Sie überprüfen, ob Ihr Cluster gestartet wurde und ob für `ProvisioningState` wie in der folgenden Ausgabe `Succeeded` angezeigt wird:

```Output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Wenn für `ProvisioningState` `Starting` angezeigt wird, bedeutet das, dass Ihr Cluster noch nicht vollständig gestartet wurde.

---

> [!NOTE]
> Wenn Sie die Clustersicherung starten, wird folgendes Verhalten erwartet:
>
> * Die IP-Adresse Ihres API-Servers kann sich ändern.
> * Wenn Sie automatische Clusterskalierung verwenden, liegt Ihre aktuelle Knotenanzahl beim Starten des Clusters möglicherweise nicht zwischen den von Ihnen festgelegten Mindest- und Höchstwerten für den Bereich. Der Cluster beginnt mit der Anzahl von Knoten, die er zum Ausführen seiner Workloads benötigt. Dies hat keine Auswirkungen auf Ihre Einstellungen für automatische Skalierung. Wenn Ihr Cluster Skalierungsvorgänge ausführt, wirken sich die Mindest- und Höchstwerte auf Ihre aktuelle Knotenanzahl aus. Der Cluster wird schließlich in den gewünschten Bereich gelangen und dort verbleiben, bis Sie ihn beenden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Skalieren von `User`-Pools auf 0 finden Sie unter [Skalieren von `User`-Pools auf 0](scale-cluster.md#scale-user-node-pools-to-0).
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
