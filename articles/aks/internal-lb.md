---
title: Erstellen eines internen Load Balancers
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie einen internen Lastenausgleich erstellen und verwenden, um Ihre Dienste über Azure Kubernetes Service (AKS) verfügbar zu machen.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 0e55ec0b6066b2b2582adf20acd646117c47f8e4
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666456"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Verwenden eines internen Lastenausgleichs mit Azure Kubernetes Service (AKS)

Zum Einschränken des Zugriffs auf Ihre Anwendungen in Azure Kubernetes Service (AKS) können Sie einen internen Lastenausgleich erstellen und verwenden. Durch einen internen Lastenausgleich können nur Anwendungen, die im gleichen virtuellen Netzwerk wie der Kubernetes-Cluster ausgeführt werden, auf einen Kubernetes-Dienst zugreifen. In diesem Artikel erfahren Sie, wie Sie einen internen Lastenausgleich mit Azure Kubernetes Service (AKS) erstellen und verwenden.

> [!NOTE]
> Azure Load Balancer ist in zwei SKUs verfügbar: *Basic* und *Standard*. Beim Erstellen eines AKS-Clusters wird normalerweise die SKU vom Typ „Standard“ verwendet.  Wenn Sie einen Dienst mit dem Typ „LoadBalancer“ erstellen, erhalten Sie denselben LB-Typ wie bei der Bereitstellung des Clusters. Weitere Informationen finden Sie unter [Vergleich der Azure Load Balancer-SKUs][azure-lb-comparison].

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

Die AKS-Clusteridentität benötigt die Berechtigung zum Verwalten von Netzwerkressourcen, wenn Sie ein bestehendes Subnetz oder eine vorhandene Ressourcengruppe verwenden. Weitere Informationen finden Sie unter [Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)][use-kubenet] oder [Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS)][advanced-networking]. Wenn Sie Ihren Lastenausgleich so konfigurieren, dass eine [IP-Adresse in einem anderen Subnetz][different-subnet] verwendet wird, stellen Sie sicher, dass die AKS-Clusteridentität auch Lesezugriff auf dieses Subnetz hat.

Weitere Informationen zu Berechtigungen finden Sie unter [Delegieren des Zugriffs auf andere Azure-Ressourcen][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Erstellen eines internen Load Balancers

Erstellen Sie, wie im folgenden Beispiel gezeigt, ein Dienstmanifest namens `internal-lb.yaml` mit dem Diensttyp *LoadBalancer* und der Anmerkung *azure-load-balancer-internal*, um einen internen Lastenausgleich zu erstellen:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Stellen Sie den internen Lastenausgleich über den Befehl [kubectl-apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f internal-lb.yaml
```

Ein Azure-Lastenausgleich wird in der Knotenressourcengruppe erstellt und mit dem gleichen virtuellen Netzwerk wie der AKS-Cluster verbunden.

Beim Anzeigen der Dienstdetails wird die IP-Adresse des internen Lastenausgleichs in der Spalte *EXTERNAL-IP* angezeigt. In diesem Zusammenhang bezieht sich *External* auf die externe Schnittstelle des Load Balancers, nicht etwa darauf, dass er eine öffentliche, externe IP-Adresse erhält. Es dauert möglicherweise ein oder zwei Minuten, bis sich die IP-Adresse von *\<pending\>* in eine tatsächliche interne IP-Adresse ändert, wie im folgenden Beispiel gezeigt:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Angeben einer IP-Adresse

Wenn Sie eine bestimmte IP-Adresse für den internen Lastenausgleich verwenden möchten, fügen Sie dem YAML-Manifest des Lastenausgleichs die Eigenschaft *loadBalancerIP* hinzu. Die angegebene IP-Adresse muss sich in diesem Szenario im gleichen Subnetz wie der AKS-Cluster befinden und darf keiner Ressource zugewiesen sein. Beispielsweise sollte keine IP-Adresse aus dem Bereich verwendet werden, der für das Kubernetes-Subnetz im AKS-Cluster festgelegt ist.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Nach Bereitstellung und bei Anzeige der Dienstdetails entspricht die IP-Adresse in der Spalte *EXTERNAL-IP* der angegebenen IP-Adresse:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Weitere Informationen zum Konfigurieren Ihres Lastenausgleichs in einem anderen Subnetz finden Sie unter [Angeben eines anderen Subnetzes][different-subnet].

## <a name="use-private-networks"></a>Verwenden privater Netzwerke

Beim Erstellen des AKS-Clusters können Sie erweiterte Netzwerkeinstellungen angeben. Dadurch können Sie den Cluster in einem vorhandenen virtuellen Azure-Netzwerk und in Subnetzen bereitstellen. In einem Szenario wird der AKS-Cluster in einem privaten Netzwerk bereitgestellt, das mit Ihrer lokalen Umgebung verbunden ist, und nur intern zugängliche Dienste werden ausgeführt. Weitere Informationen finden Sie unter [Konfigurieren eigener virtueller Subnetze mit Kubernetes][use-kubenet] oder [Azure CNI][advanced-networking].

Wenn Sie einen internen Lastenausgleich in einem AKS-Cluster bereitstellen möchten, der ein privates Netzwerk verwendet, sind keine Änderungen an den vorherigen Schritten erforderlich. Der Lastenausgleich wird in derselben Ressourcengruppe wie der AKS-Cluster erstellt. Dabei wird er jedoch wie im folgenden Beispiel gezeigt mit Ihrem privaten virtuellen Netzwerk und Subnetz verbunden:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Möglicherweise müssen Sie der Clusteridentität für Ihren AKS-Cluster die Rolle *Netzwerkmitwirkender* für die Ressourcengruppe gewähren, in der die Ressourcen Ihres virtuellen Azure-Netzwerks bereitgestellt werden. Zeigen Sie die Clusteridentität mit [az aks show][az-aks-show] an, z. B. `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"`. Verwenden Sie zum Erstellen einer Rollenzuweisung den Befehl [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Angeben eines anderen Subnetzes

Fügen Sie zum Angeben eines Subnetzes für den Lastenausgleich Ihrem Dienst die Anmerkung *azure-load-balancer-internal-subnet* hinzu. Das angegebene Subnetz muss sich dabei im gleichen virtuellen Netzwerk wie Ihr AKS-Cluster befinden. Nach der Bereitstellung ist die Adresse *EXTERNAL-IP* des Lastenausgleichs Teil des angegebenen Subnetzes.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Löschen des Lastenausgleichs

Wenn alle Dienste, die den internen Lastenausgleich verwenden, gelöscht werden, wird auch der Lastenausgleich selbst gelöscht.

Wie jede andere Kubernetes-Ressource können Sie auch einen Dienst direkt löschen (z. B. `kubectl delete service internal-app`), wodurch dann auch der zugrunde liegende Azure Load Balancer gelöscht wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Kubernetes-Diensten finden Sie in der entsprechenden [Dokumentation][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet