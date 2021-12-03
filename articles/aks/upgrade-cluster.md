---
title: Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)
description: Erfahren Sie, wie Sie das Upgrade eines Azure Kubernetes Service-Clusters (AKS) vornehmen, um die neuesten Funktionen und Sicherheitsupdates zu erhalten.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 8c5a395833cb19e4f5ce78f08ee37c2eb022169b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397336"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)

Im Rahmen des AKS-Clusterlebenszyklus müssen regelmäßige Upgrades auf die aktuelle Kubernetes-Version ausgeführt werden. Es ist wichtig, jeweils die aktuellen Sicherheitsversionen anzuwenden oder bei einem Upgrade die neuesten Features zu erhalten. In diesem Artikel wird veranschaulicht, wie Sie die Masterkomponenten oder einen einzelnen Standardknotenpool in einem AKS-Cluster aktualisieren.

Informationen zu AKS-Clustern, für die mehrere Knotenpools oder Windows Server-Knoten verwendet werden, finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Voraussetzungen

Der Artikel setzt voraus, dass Sie mindestens Version 2.0.65 der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

> [!WARNING]
> Durch ein AKS-Clusterupgrade werden Ihre Knoten als nicht planbar markiert und entleert (cordon/drain). Wenn Sie nur über ein geringes Computekontingent verfügen, kann das Upgrade möglicherweise nicht durchgeführt werden. Weitere Informationen finden Sie unter [Anfordern einer Kontingenterhöhung](../azure-portal/supportability/regional-quota-requests.md).

## <a name="check-for-available-aks-cluster-upgrades"></a>Suchen nach verfügbaren AKS-Clusterupgrades

Überprüfen Sie mithilfe des Befehls [az aks get-upgrades][az-aks-get-upgrades], welche Kubernetes-Versionen für Ihren Cluster verfügbar sind. Im folgenden Beispiel wird nach verfügbaren Upgrades für *myAKSCluster* in *myResourceGroup* gesucht:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Beim Upgrade eines unterstützten AKS-Clusters können Nebenversionen von Kubernetes nicht übersprungen werden. Alle Upgrades müssen nacheinander nach der Hauptversionsnummer ausgeführt werden. Beispielsweise sind Upgrades von *1.14.x* -> *1.15.x* oder *1.15.x* -> *1.16.x* zulässig, ein Upgrade von *1.14.x* -> *1.16.x* ist jedoch nicht möglich. 
> > Das Überspringen mehrerer Versionen ist nur möglich, wenn ein Upgrade von einer _nicht unterstützten Version_ auf eine _unterstützte Version_ erfolgt. Beispielsweise kann ein Upgrade von einer nicht unterstützten Version *1.10.x* auf eine unterstützte Version *1.15.x* durchgeführt werden.

Die Ausgabe im folgenden Beispiel zeigt, dass der Cluster auf die Versionen *1.19.1* und *1.19.3* aktualisiert werden kann:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Wenn kein Upgrade verfügbar ist, wird folgende Meldung angezeigt:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Anpassen des Upgrades für Knotenanstiege

> [!Important]
> Knotenanstiege erfordern ein Abonnementkontingent für die angeforderte maximale Anstiegsanzahl für jeden Upgradevorgang. Beispielsweise umfasst ein Cluster mit 5 Knotenpools, von denen jeder über 4 Knoten verfügt, insgesamt 20 Knoten. Wenn für jeden Knotenpool ein maximaler Anstiegswert von 50 % gilt, sind zusätzliche Compute- und IP-Kontingente von 10 Knoten (2 Knoten * 5 Pools) erforderlich, um das Upgrade abzuschließen.
>
> Wenn Sie Azure CNI verwenden, stellen Sie sicher, dass im Subnetz IPs verfügbar sind und die [Anforderungen von Azure CNI an IPs](configure-azure-cni.md) erfüllt werden.

Standardmäßig konfiguriert AKS Upgrades für einen Anstieg mit einem zusätzlichen Knoten. Ein Standardwert von „1“ für die Einstellung des maximalen Anstiegs ermöglicht AKS das Minimieren von Workloadunterbrechungen, indem vor dem Absperren/Ausgleichen vorhandener Anwendungen ein zusätzlicher Knoten erstellt wird, um einen Knoten einer älterer Version zu ersetzen. Der maximale Anstiegswert kann pro Knotenpool angepasst werden, um einen Kompromiss zwischen Upgradegeschwindigkeit und Upgradeunterbrechung zu ermöglichen. Wenn Sie den maximalen Anstiegswert erhöhen, wird der Upgradevorgang schneller abgeschlossen, aber das Festlegen eines hohen Werts für den maximalen Anstieg kann zu Unterbrechungen während des Upgradevorgangs führen. 

Beispielsweise ermöglicht ein maximaler Anstiegswert von 100 % den schnellstmöglichen Upgradevorgang (wobei die Knotenanzahl verdoppelt wird), führt aber auch dazu, dass alle Knoten im Knotenpool gleichzeitig ausgeglichen werden. Möglicherweise möchten Sie (z. B. für Testumgebungen) einen höheren Wert verwenden. Für Produktionsknotenpools wird ein maximaler Anstiegswert von 33 % empfohlen.

Für den maximalen Anstieg akzeptiert AKS sowohl ganzzahlige Werte als auch einen prozentualen Wert. Eine ganze Zahl wie z. B. „5“ gibt für den Anstieg fünf zusätzliche Knoten an. Der Wert „50 %“ gibt einen Anstiegswert der Hälfte der aktuellen Knotenanzahl im Pool an. Die Prozentwerte für den maximalen Anstieg können mindestens 1 % und maximal 100 % lauten. Ein Prozentwert wird auf die nächste Knotenanzahl aufgerundet. Wenn der maximale Anstiegswert zum Zeitpunkt des Upgrades niedriger ist als die aktuelle Knotenanzahl, wird die aktuelle Knotenanzahl für den maximalen Anstiegswert verwendet.

Während eines Upgrades kann der maximale Anstiegswert mindestens „1“ betragen und maximal der Anzahl von Knoten in Ihrem Knotenpool entsprechen. Sie können größere Werte festlegen, aber die maximale Anzahl von Knoten, die für den maximalen Anstieg verwendet wird, ist nicht größer als die Anzahl der Knoten im Pool zum Zeitpunkt des Upgrades.

> [!Important]
> Die Einstellung des maximalen Anstiegs für einen Knotenpools ist persistent.  Diese Einstellung wird bei nachfolgenden Kubernetes-Upgrades oder bei Knotenversionsupgrades verwendet. Sie können den maximalen Anstiegswert für Ihre Knotenpools jederzeit ändern. Für Produktionsknotenpools wird ein maximaler Anstiegswert von 33 % empfohlen.

Verwenden Sie die folgenden Befehle, um Werte für den maximalen Anstieg für neue oder vorhandene Knotenpools festzulegen.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Aktualisieren eines AKS-Clusters

Wenn Sie die Liste der verfügbaren Versionen für Ihren AKS-Cluster angezeigt haben, verwenden Sie den Befehl [az aks upgrade][az-aks-upgrade], um den Cluster zu aktualisieren. Während des Upgrades geschieht Folgendes: 
- AKS fügt dem Cluster, auf dem die angegebene Kubernetes-Version ausgeführt wird, einen neuen Pufferknoten (oder die in [max surge](#customize-node-surge-upgrade) konfigurierte Anzahl von Knoten) hinzu. 
- Einer der alten Knoten wird [abgesperrt und ausgeglichen][kubernetes-drain], um Unterbrechungen bei der Ausführung von Anwendungen zu minimieren. (Wenn Sie „max surge“ verwenden, werden so viele Knoten gleichzeitig [abgesperrt und ausgeglichen][kubernetes-drain], wie als Anzahl der angegebenen Pufferknoten angegeben wurde.) 
- Wenn der alte Knoten vollständig ausgeglichen wurde, wird für diesen ein Reimaging durchgeführt, damit er die neue Version erhält, und er wird zum Pufferknoten, damit der folgende Knoten aktualisiert werden kann. 
- Dieser Prozess wird wiederholt, bis alle Knoten im Cluster aktualisiert wurden. 
- Am Ende des Vorgangs wird der letzte Pufferknoten gelöscht, um die Anzahl der vorhandenen Agent-Knoten und die Zonenbalance beizubehalten.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Die Dauer des Clusterupgrades hängt von der Anzahl der vorhanden Knoten ab und kann einige Minuten in Anspruch nehmen.

> [!IMPORTANT]
> Stellen Sie sicher dass alle `PodDisruptionBudgets` (PDBs) die Verschiebung von jeweils mindestens einem Podreplikat gleichzeitig erlauben, da der Vorgang des Entleeren/Entfernens (drain/evict) sonst nicht ausgeführt werden kann.
> Wenn der Entleerungsvorgang nicht ausgeführt werden kann, wird entwurfsbedingt auch der Upgradevorgang nicht ausgeführt, um sicherzustellen, dass die Anwendungen nicht unterbrochen werden. Beheben Sie die Ursache für die Beendigung des Vorgangs (falsche PDBs, unzureichendes Kontingent usw.), und wiederholen Sie den Vorgang.

Überprüfen Sie nun mit dem Befehl [az aks show][az-aks-show], ob das Upgrade erfolgreich war:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Die Ausgabe im folgenden Beispiel zeigt, dass im Cluster nun Version *1.18.10* ausgeführt wird:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Festlegen des Kanals für automatische Upgrades

Zusätzlich zum Ausführen von manuellen Upgrades eines Clusters können Sie für den Cluster auch einen Kanal für automatische Upgrades festlegen. Folgende Upgradekanäle sind verfügbar:

|Kanal| Aktion | Beispiel
|---|---|---|
| `none`| Automatische Upgrades werden deaktiviert, und die aktuelle Kubernetes-Version des Clusters wird beibehalten.| Standardeinstellung, falls keine Änderung vorgenommen wird|
| `patch`| Es wird automatisch ein Upgrade des Clusters auf die neueste unterstützte Patchversion durchgeführt, sobald dieses verfügbar wird. Die Nebenversion wird beibehalten.| Ein Beispiel: In einem Cluster wird derzeit Version *1.17.7* ausgeführt, und die Versionen *1.17.9*, *1.18.4*, *1.18.6* und *1.19.1* sind verfügbar. In diesem Fall wird ein Upgrade auf *1.17.9* ausgeführt.|
| `stable`| In diesem Kanal wird automatisch ein Upgrade des Clusters auf die neueste unterstützte Patchversion der Nebenversion *N-1* ausgeführt. *N* steht dabei für die neueste unterstützte Nebenversion.| Ein Beispiel: Auf einem Cluster wird derzeit Version *1.17.7* ausgeführt, und die Versionen *1.17.9*, *1.18.4*, *1.18.6* und *1.19.1* sind verfügbar. In diesem Fall wird ein Upgrade auf *1.18.6* ausgeführt.
| `rapid`| In diesem Kanal wird automatisch ein Upgrade des Clusters auf die neueste unterstützte Patchversion der neuesten unterstützten Nebenversion ausgeführt.| Falls der Cluster eine Kubernetes-Version mit Nebenversion *N-2* aufweist, wobei *N* für die neueste unterstützte Nebenversion steht, wird zuerst ein Upgrade des Clusters auf die neueste unterstützte Patchversion der Nebenversion *N-1* ausgeführt. Ein Beispiel: Auf einem Cluster wird derzeit Version *1.17.7* ausgeführt, und die Versionen *1.17.9*, *1.18.4*, *1.18.6* und *1.19.1* sind verfügbar. In diesem Fall wird zuerst ein Upgrade auf *1.18.6* und dann ein Upgrade auf *1.19.1* ausgeführt.
| `node-image`| automatisches Upgrade des Knotenimages auf die neueste verfügbare Version.| Microsoft stellt häufig Patches und neue Images für Imageknoten bereit (in der Regel wöchentlich), aber Ihre ausgeführten Knoten erhalten die neuen Images nur, wenn Sie ein Knotenimage-Upgrade durchführen. Wenn Sie den Knotenimage-Kanal aktivieren, werden Ihre Knotenimages automatisch aktualisiert, sobald eine neue Version verfügbar ist. |

> [!NOTE]
> Bei den automatischen Clusterupgrades wird immer nur auf Kubernetes-Versionen mit allgemeiner Verfügbarkeit aktualisiert, niemals auf Vorschauversionen.

Automatische Clusterupgrades folgen demselben Prozess wie manuelle Upgrades. Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters][upgrade-cluster].

Um beim Erstellen eines Clusters den automatischen Upgradekanal festzulegen, verwenden Sie den Parameter *auto-upgrade-channel*, wie im folgenden Beispiel gezeigt.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Um den automatischen Upgradekanal für einen vorhandenen Cluster festzulegen, aktualisieren Sie den Parameter *auto-upgrade-channel*, wie im folgenden Beispiel gezeigt.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="using-cluster-auto-upgrade-with-planned-maintenance"></a>Verwenden von automatischem Clusterupgrade mit geplanter Wartung

Wenn Sie geplante Wartung und automatisches Upgrade verwenden, wird Ihr Upgrade während Ihres angegebenen Wartungsfensters gestartet. Weitere Informationen zur geplanten Wartung finden Sie unter [Verwenden der geplanten Wartung, um Wartungsfenster für AKS-Cluster (Azure Kubernetes Service) zu planen (Vorschau)][planned-maintenance].

## <a name="special-considerations-for-node-pools-that-span-multiple-availability-zones"></a>Besondere Überlegungen für Knotenpools, die sich über mehrere Verfügbarkeitszonen erstrecken

AKS verwendet Best-Effort-Zonenausgleich in Knotengruppen. Während eines Upgradeanstiegs sind Zonen für die Anstiegsknoten in VM-Skalierungssätzen im Voraus unbekannt. Dies kann vorübergehend zu einer unausgewogenen Zonenkonfiguration während eines Upgrades führen. AKS löscht jedoch den/die Surge-Knoten, sobald das Upgrade abgeschlossen ist, und bewahrt das ursprüngliche Zonengleichgewicht. Wenn Sie möchten, dass Ihre Zonen während des Upgrades ausgeglichen bleiben, erhöhen Sie den Surge auf ein Vielfaches von 3 Knoten. VM-Skalierungssätze verteilen ihre Knoten dann auf Verfügbarkeitszonen mit bestmöglichem Zonenausgleich.

Wenn Sie PVCs haben, die durch Azure LRS-Disks gesichert sind, sind diese an eine bestimmte Zone gebunden und werden möglicherweise nicht sofort wiederhergestellt, wenn der Surge-Knoten nicht mit der Zone des PVCs übereinstimmt. Dies kann zu Ausfallzeiten in Ihrer Anwendung führen, wenn der Upgrade-Vorgang weiterhin Knoten entleert, die PVs aber an eine Zone gebunden sind. Um diesen Fall zu bewältigen und eine hohe Verfügbarkeit aufrechtzuerhalten, konfigurieren Sie ein [Pod Disruption Budget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) für Ihre Anwendung. Dadurch kann Kubernetes Ihre Verfügbarkeitsanforderungen während des Entleerungsvorgangs von Upgrade einhalten. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde gezeigt, wie Sie einen vorhandenen AKS-Cluster aktualisieren. In verschiedenen Tutorials können Sie mehr über die Bereitstellung und Verwaltung von AKS-Clustern erfahren.

> [!div class="nextstepaction"]
> [AKS-Tutorials][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
[planned-maintenance]: planned-maintenance.md