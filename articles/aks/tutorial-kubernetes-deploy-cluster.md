---
title: 'Tutorial zu Kubernetes in Azure: Bereitstellen eines Clusters'
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erstellen Sie einen AKS-Cluster und verwenden „kubectl“ für die Verbindung mit dem Kubernetes-Masterknoten.
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 731c656020629b857a19a215027b5c48dca435e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741622"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)

Kubernetes bietet eine verteilte Plattform für containerbasierte Anwendungen. Mit AKS können Sie schnell einen Kubernetes-Cluster erstellen, der für die Produktion bereit ist. In diesem Tutorial (Teil 3 von 7) wird ein Kubernetes-Cluster in AKS bereitgestellt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-AKS-Clusters, der sich bei einer Azure-Containerregistrierung authentifizieren kann
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von „kubectl“ für die Verbindung mit Ihrem AKS-Cluster

In nachfolgenden Tutorials wird die Anwendung Azure Vote im Cluster bereitgestellt, skaliert und aktualisiert.

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde ein Containerimage erstellt und in eine Azure Container Registry-Instanz hochgeladen. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, beginnen Sie mit [Tutorial 1: Erstellen von Containerimages][aks-tutorial-prepare-app].

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Für dieses Tutorial müssen Sie mindestens die Azure PowerShell-Version 5.9.0 ausführen. Führen Sie `Get-InstalledModule -Name Az` aus, um die Version zu ermitteln. Falls Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure PowerShell][azure-powershell-install] weitere Informationen.

---

## <a name="create-a-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters

AKS-Cluster können die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) verwenden. Mit diesen Steuerungen können Sie den Zugriff auf Ressourcen basierend auf Rollen definieren, die Benutzern zugewiesen sind. Berechtigungen werden kombiniert, wenn einem Benutzer mehrere Rollen zugewiesen sind, und Berechtigungen können auf einen einzelnen Namespace begrenzt sein oder für den gesamten Cluster gelten. Standardmäßig wird Kubernetes RBAC bei der Erstellung eines AKS-Clusters von der Azure CLI automatisch aktiviert.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Erstellen Sie mit [az aks create][] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* in der Ressourcengruppe mit dem Namen *myResourceGroup* erstellt. Diese Ressourcengruppe wurde im [vorherigen Tutorial][aks-tutorial-prepare-acr] in der Region *eastus* erstellt. Im folgenden Beispiel wird keine Region angegeben, sodass der AKS-Cluster auch in der Region *eastus* erstellt wird. Weitere Informationen zu Ressourcenbeschränkungen und regionaler Verfügbarkeit für AKS finden Sie unter [Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)][quotas-skus-regions].

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird automatisch eine Clusteridentität erstellt, da Sie keine angegeben haben. Hier wird dieser Clusteridentität die [Berechtigung zum Pullen von Images][container-registry-integration] aus der ACR-Instanz (Azure Container Registry) gewährt, die Sie im vorherigen Tutorial erstellt haben. Damit der Befehl erfolgreich ausgeführt werden kann, müssen Sie über die Rolle **Besitzer** oder **Azure-Kontoadministrator** für das Azure-Abonnement verfügen.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Erstellen Sie mit [New-AzAksCluster][new-azakscluster] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* in der Ressourcengruppe mit dem Namen *myResourceGroup* erstellt. Diese Ressourcengruppe wurde im [vorherigen Tutorial][aks-tutorial-prepare-acr] in der Region *eastus* erstellt. Im folgenden Beispiel wird keine Region angegeben, sodass der AKS-Cluster auch in der Region *eastus* erstellt wird. Weitere Informationen zu Ressourcenbeschränkungen und regionaler Verfügbarkeit für AKS finden Sie unter [Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)][quotas-skus-regions].

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird automatisch eine Clusteridentität erstellt, da Sie keine angegeben haben. Hier wird dieser Clusteridentität die [Berechtigung zum Pullen von Images][container-registry-integration] aus der ACR-Instanz (Azure Container Registry) gewährt, die Sie im vorherigen Tutorial erstellt haben. Damit der Befehl erfolgreich ausgeführt werden kann, müssen Sie über die Rolle **Besitzer** oder **Azure-Kontoadministrator** für das Azure-Abonnement verfügen.

```azurepowershell
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -GenerateSshKey -AcrNameToAttach <acrName>
```

---

Um zu vermeiden, dass die Rolle **Besitzer** oder **Azure-Kontoadministrator** benötigt wird, können Sie auch manuell einen Dienstprinzipal konfigurieren, um Images aus ACR zu pullen. Weitere Informationen finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md) oder unter [Abrufen von Images aus einer Azure-Containerregistrierung per Pull in einem Kubernetes-Cluster](../container-registry/container-registry-auth-kubernetes.md). Alternativ können Sie anstelle eines Dienstprinzipals zur einfacheren Verwaltung eine [verwaltete Identität](use-managed-identity.md) verwenden.

Nach einigen Minuten ist die Bereitstellung abgeschlossen, und es werden Informationen zur AKS-Bereitstellung im JSON-Format zurückgegeben.

> [!NOTE]
> Um zu gewährleisten, dass Ihr Cluster zuverlässig funktioniert, sollten Sie mindestens zwei Knoten verwenden.

## <a name="install-the-kubernetes-cli"></a>Installieren der Kubernetes-Befehlszeilenschnittstelle

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem lokalen Computer verwenden Sie den Kubernetes-Befehlszeilenclient [kubectl][kubectl].

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie Azure Cloud Shell verwenden, ist `kubectl` bereits installiert. Mit dem Befehl [az aks install-cli][] können Sie ihn auch lokal installieren:

```azurecli
az aks install-cli
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wenn Sie Azure Cloud Shell verwenden, ist `kubectl` bereits installiert. Sie können ihn auch mit dem Cmdlet [Install-AzAksKubectl][install-azakskubectl] lokal installieren:

```azurepowershell
Install-AzAksKubectl
```

---

## <a name="connect-to-cluster-using-kubectl"></a>Herstellen einer Verbindung mit dem Cluster mithilfe von „kubectl“

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit dem Befehl [az aks get-credentials][] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *myAKSCluster* in *myResourceGroup* abgerufen:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Import-AzAksCredential][import-azakscredential], um `kubectl` für die Verbindung mit Ihrem Kubernetes-Cluster zu konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *myAKSCluster* in *myResourceGroup* abgerufen:

```azurepowershell
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

---

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get nodes][kubectl-get], um eine Liste der Clusterknoten zu erhalten:

```azurecli-interactive
kubectl get nodes
```

In der folgenden Beispielausgabe ist die Liste der Clusterknoten zu sehen:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde ein Kubernetes-Cluster in AKS bereitgestellt, und Sie haben `kubectl` für die Verbindung damit konfiguriert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-AKS-Clusters, der sich bei einer Azure-Containerregistrierung authentifizieren kann
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von „kubectl“ für die Verbindung mit Ihrem AKS-Cluster

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie eine Anwendung im Cluster bereitgestellt wird.

> [!div class="nextstepaction"]
> [Bereitstellen einer Anwendung in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[install-azakskubectl]: /powershell/module/az.aks/install-azakskubectl
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
