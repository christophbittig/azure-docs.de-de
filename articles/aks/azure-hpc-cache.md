---
title: Integrieren von Azure HPC Cache in Azure Kubernetes Service
description: Hier erfahren Sie, wie Sie Azure HPC Cache in Azure Kubernetes Service integrieren
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 208147894b5fb483bc9ebcc49c01fd93013c3a3d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491524"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>Integrieren von Azure HPC Cache in Azure Kubernetes Service

[Azure HPC Cache][hpc-cache] beschleunigt den Zugriff auf Ihre Daten für High Performance Computing-Aufgaben (HPC). Aufgrund der Zwischenspeicherung von Dateien in Azure ermöglicht Azure HPC Cache für Ihren vorhandenen Workflow die Skalierbarkeit des Cloud Computing. Dieser Artikel zeigt Ihnen, wie Sie Azure HPC Cache in Azure Kubernetes Service (AKS) integrieren.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Ihr AKS-Cluster muss sich [in einer Region befinden, die Azure HPC Cache unterstützt][hpc-cache-regions].

Außerdem müssen Sie die Version 2.7 oder höher der Azure-Befehlszeilenschnittstelle installieren und konfigurieren. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].  Unter [hpc-cache-cli-prerequisites] finden Sie weitere Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle mit HPC Cache.

Außerdem müssen Sie die Erweiterung hpc-cache der Azure-Befehlszeilenschnittstelle installieren.  Gehen Sie wie folgt vor:

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>Einrichten von Azure HPC Cache

In diesem Abschnitt werden die Schritte zum Erstellen und Konfigurieren Ihres HPC Cache erläutert.

### <a name="1-find-the-aks-node-resource-group"></a>1. Suchen der AKS-Knotenressourcengruppe

Rufen Sie zunächst den Namen der Ressourcengruppe mit dem Befehl [az aks show][az-aks-show] ab, und fügen Sie den Abfrageparameter `--query nodeResourceGroup` hinzu. Sie erstellen Ihren HPC Cache in derselben Ressourcengruppe.

Im folgenden Beispiel wird der Name der Knoten-Ressourcengruppe für den AKS-Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. Erstellen des Cachesubnetzes

Es gibt eine Reihe von [Voraussetzungen][hpc-cache-prereqs], die erfüllt sein müssen, bevor sie einen HPC Cache ausführen.  Vor allem erfordert der Cache ein *dediziertes* Subnetz mit mindestens 64 verfügbaren IP-Adressen. Dieses Subnetz darf keine anderen VMs oder Container hosten.  Auf dieses Subnetz muss über die AKS-Knoten zugegriffen werden können.

Erstellen Sie das dedizierte HPC Cache-Subnetz:

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

Registrieren Sie den Ressourcenanbieter *Microsoft.StorageCache*:

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> Die Registrierung des Ressourcenanbieters kann einige Zeit dauern.

### <a name="3-create-the-hpc-cache"></a>3. Erstellen des HPC Cache

Erstellen Sie einen HPC Cache in der Knotenressourcengruppe aus Schritt 1 und in derselben Region wie Ihr AKS-Cluster. Verwenden Sie [az hpc-cache create][az-hpc-cache-create].

> [!NOTE]
> Die Erstellung des HPC Cache dauert ca. 20 Minuten.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. Erstellen eines Storage-Kontos und eines neuen Containers

Erstellen Sie das Azure Storage-Konto für den Blob Storage-Container.  Der HPC Cache speichert Inhalte zwischen, die in diesem Blob Storage-Container gespeichert sind.

> [!IMPORTANT]
> Sie müssen einen eindeutigen Speicherkontonamen auswählen.  Ersetzen Sie „uniquestorageaccount“ durch etwas, das für Sie eindeutig ist.

Überprüfen Sie, ob der von Ihnen ausgewählte Speicherkontoname verfügbar ist.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

Erstellen Sie den Blobcontainer im Speicherkonto.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --auth-mode login
```

Erteilen Sie Berechtigungen für das Azure HPC Cache-Dienstkonto, um auf Ihr Speicherkonto und Ihren Blobcontainer zuzugreifen.

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. Konfigurieren des Speicherziels

Fügen Sie den Blobcontainer Ihrem HPC Cache als Speicherziel hinzu.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. Einrichten des Clientlastenausgleichs

Erstellen Sie eine private Azure-DNS-Zone für die clientseitigen IP-Adressen.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

Erstellen Sie den Roundrobin-DNS-Namen.

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>Erstellen des persistenten AKS-Volumes

Erstellen Sie eine `pv-nfs.yaml`-Datei, um ein [persistentes Volume][persistent-volume] zu definieren.

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

Stellen Sie zunächst sicher, dass Sie über Anmeldeinformationen für Ihren Kubernetes-Cluster verfügen.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aktualisieren Sie *server* und *path* (Pfad) auf die Werte Ihres NFS-Volumes (Network File System), das Sie im vorherigen Schritt erstellt haben. Erstellen Sie das persistente Volume mit dem Befehl [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pv-nfs.yaml
```

Überprüfen Sie mithilfe des Befehls [kubectl describe][kubectl-describe], ob der Status des persistenten Volumes **Verfügbar** ist:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>Erstellen des Anspruchs auf persistente Volumes

Erstellen Sie eine Datei `pvc-nfs.yaml`, die einen [Anspruch auf ein persistentes Volume][persistent-volume-claim] definiert. Beispiel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 100Gi
```

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] den Anspruch auf ein persistentes Volume:

```console
kubectl apply -f pvc-nfs.yaml
```

Überprüfen Sie mithilfe des Befehls [kubectl describe][kubectl-describe], ob der Status des persistenten Volumes **Gebunden** ist:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>Einbinden des HPC Cache mit einem Pod

Erstellen Sie eine `nginx-nfs.yaml`-Datei, um einen Pod zu definieren, der den Anspruch auf ein persistentes Volume verwendet. Beispiel:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply].

```console
kubectl apply -f nginx-nfs.yaml
```

Stellen Sie mithilfe des Befehls [kubectl describe][kubectl-describe] sicher, dass der Pod ausgeführt wird:

```console
kubectl describe pod nginx-nfs
```

Stellen Sie sicher, dass Ihr Volume in den Pod eingebunden wurde, indem Sie [kubectl exec][kubectl-exec] verwenden, um eine Verbindung mit dem Pod herzustellen, und überprüfen Sie dann mit `df -h`, ob das Volume eingebunden ist.

```console
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

### <a name="running-applications-as-non-root"></a>Ausführen von Anwendungen als Nicht-Root-Benutzer

Wenn Sie eine Anwendung als Nicht-Root-Benutzer ausführen müssen, müssen Sie möglicherweise das Root-Squashing deaktivieren, um einen anderen Benutzer zum Besitzer eines Verzeichnisses zu machen.  Der Nicht-Root-Benutzer muss ein Verzeichnis besitzen, um auf das Dateisystem zugreifen zu können.  Damit der Benutzer ein Verzeichnis besitzt, muss der Root-Benutzer diesen Benutzer zum Besitzer des Verzeichnisses machen. Wenn der HPC Cache jedoch Root-Squashing einsetzt, wird dieser Vorgang verweigert, da der Root-Benutzer (UID 0) dem anonymen Benutzer zugeordnet wird.  Weitere Informationen zu Root-Squashing und Clientzugriffsrichtlinien finden Sie [hier][hpc-cache-access-policies].

### <a name="sending-feedback"></a>Senden von Feedback

Wir freuen uns darauf, von Ihnen zu hören.  Senden Sie Feedback oder Fragen an <aks-hpccache-feed@microsoft.com>.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure HPC Cache finden Sie in [Übersicht über HPC Cache][hpc-cache].
* Weitere Informationen zum Verwenden von NFS mit AKS finden Sie unter [Manuelles Erstellen und Verwenden eines Volumes eines Linux-NFS-Servers (Network File System) mit Azure Kubernetes Service (AKS)][aks-nfs].

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
