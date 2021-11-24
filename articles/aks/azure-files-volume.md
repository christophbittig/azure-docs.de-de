---
title: Manuelles Erstellen von Azure Files-Freigaben
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie manuell ein Volume mit Azure Files für die Verwendung mit mehreren parallelen Pods in Azure Kubernetes Service (AKS) erstellen.
services: container-service
ms.topic: article
ms.date: 07/08/2021
ms.openlocfilehash: d303e00c7f1a7ef76bb048048123b65eb42de402
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179955"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Manuelles Erstellen und Verwenden eines Volumes mit Azure Files-Freigabe in Azure Kubernetes Service (AKS)

Containerbasierte Anwendungen müssen häufig auf Daten in einem externen Datenvolume zugreifen und diese dauerhaft speichern. Wenn mehrere Pods gleichzeitig Zugriff auf dasselbe Speichervolume benötigen, können Sie Azure Files verwenden, um mithilfe des [Server Message Block-Protokolls (SMB)][smb-overview] eine Verbindung herzustellen. In diesem Artikel wird das manuelle Erstellen einer Azure Files-Freigabe und Anfügen dieser an einen Pod in AKS veranschaulicht.

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

Bevor Sie eine Azure Files-Freigabe als Kubernetes-Volume verwenden können, müssen Sie ein Azure Storage-Konto und die Dateifreigabe erstellen. Die folgenden Befehle erstellen die Ressourcengruppe *myAKSShare*, ein Speicherkonto und die Azure Files-Freigabe *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Notieren Sie sich den Speicherkontonamen und den Schlüssel, die am Ende der Skriptausgabe angezeigt werden. Diese Werte werden bei der Erstellung des Kubernetes-Volumes in einem der folgenden Schritte benötigt.

## <a name="create-a-kubernetes-secret"></a>Erstellen eines Kubernetes-Geheimnisses

Kubernetes benötigt Anmeldeinformationen für den Zugriff auf die im vorherigen Schritt erstellte Dateifreigabe. Diese Anmeldeinformationen werden in einem [Kubernetes-Geheimnis][kubernetes-secret] gespeichert, auf das bei der Erstellung eines Kubernetes-Pod verwiesen wird.

Verwenden Sie den Befehl `kubectl create secret`, um das Geheimnis zu erstellen. Das folgende Beispiel erstellt ein Geheimnis mit dem Namen *azure-secret* und füllt die Felder *azurestorageaccountname* und *azurestorageaccountkey* aus dem vorherigen Schritt. Um ein vorhandenes Azure Storage-Konto zu verwenden, geben Sie den Kontonamen und den Zugriffsschlüssel an.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Einbinden von Dateifreigaben als Inlinevolume
> Hinweis: Ein `azureFile`-Inlinevolume kann nur auf das Geheimnis im selben Namespace wie dem des Pods zugreifen. Um einen anderen Geheimnisnamespace anzugeben, verwenden Sie stattdessen das folgende Beispiel für ein persistentes Volume.

Um die Azure Files-Freigabe in den Pod einzubinden, konfigurieren Sie das Volume in der Containerspezifikation. Erstellen Sie eine neue Datei namens „`azure-files-pod.yaml`“ mit folgendem Inhalt. Wenn Sie den Namen oder den geheimen Namen der Files-Freigabe geändert haben, aktualisieren Sie *shareName* und *secretName*. Aktualisieren Sie bei Bedarf den Wert `mountPath`. Dies ist der Pfad, unter dem die Files-Freigabe im Pod eingebunden wird. Geben Sie für Windows Server-Container einen *mountPath* gemäß Windows-Pfadkonvention an (z. B. *D:* ).

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Um den Pod zu erstellen, verwenden Sie den Befehl `kubectl`.

```console
kubectl apply -f azure-files-pod.yaml
```

Sie verfügen nun über einen ausgeführten Pod mit einer Azure Files-Freigabe, die unter */mnt/azure* eingebunden ist. Sie können mit `kubectl describe pod mypod` überprüfen, ob die Freigabe erfolgreich eingebunden wurde. In der folgenden verkürzten Beispielausgabe ist das im Container eingebundene Volume angegeben:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Einbinden einer Dateifreigabe als persistentes Volume
 - Einbindungsoptionen

Der Standardwert für *fileMode* und *dirMode* lautet *0777* in Kubernetes-Version 1.15 und höher. Im folgenden Beispiel wird *0755* für das Objekt *PersistentVolume* festgelegt:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Zum Aktualisieren Ihrer Einbindungsoptionen erstellen Sie eine Datei *azurefile-mount-options-pv.yaml* mit einem *PersistentVolume*-Objekt. Beispiel:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Erstellen Sie eine Datei *azurefile-mount-options-pvc.yaml* mit einem *PersistentVolumeClaim*-Objekt, das *PersistentVolume* verwendet. Beispiel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Verwenden Sie die `kubectl`-Befehle, um *PersistentVolume* und *PersistentVolumeClaim* zu erstellen.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Vergewissern Sie sich, dass *PersistentVolumeClaim* erstellt und an *PersistentVolume* gebunden ist.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Aktualisieren Sie die Containerspezifikation so, dass auf *PersistentVolumeClaim* verwiesen und Ihr Pod aktualisiert wird. Beispiel:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

Da die Podspezifikation nicht direkt aktualisiert werden kann, verwenden Sie zum Löschen `kubectl`-Befehle, und erstellen Sie den Pod dann neu:

```console
kubectl delete pod mypod

kubectl apply -f azure-files-pod.yaml
```

## <a name="next-steps"></a>Nächste Schritte

Entsprechenden bewährte Methoden finden Sie unter [Bewährte Methoden für die Speicherung und Sicherungen in AKS][operator-best-practices-storage].

Weitere Informationen zur Interaktion von AKS-Clustern mit Azure Files finden Sie beim [Kubernetes-Plug-In für Azure Files][kubernetes-files].

Informationen zu Speicherklassenparametern finden Sie unter [Statische Bereitstellung („Bring Your Own File Share“)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
