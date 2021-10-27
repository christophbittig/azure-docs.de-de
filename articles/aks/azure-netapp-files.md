---
title: Integrieren von Azure NetApp Files in Azure Kubernetes Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure NetApp Files in Azure Kubernetes Service bereitstellen.
services: container-service
ms.topic: article
ms.date: 10/18/2021
ms.openlocfilehash: a88f2ac33d22852f1b14be65434eb2e354c45155
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162315"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrieren von Azure NetApp Files in Azure Kubernetes Service

Ein persistentes Volume stellt ein Speicherelement dar, das für die Verwendung in Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet und dynamisch oder statisch bereitgestellt werden. Dieser Artikel zeigt die Erstellung von [Azure NetApp Files][anf]-Volumes, die von Pods in einem AKS-Cluster (Azure Kubernetes Service) verwendet werden sollen.

[Azure NetApp Files][anf] ist ein leistungsstarker Dateispeicherdienst auf Unternehmensniveau, der in Azure ausgeführt wird. Im Hinblick auf die Verwendung von Azure NetApp Files-Volumes für Kubernetes-Workloads stehen Kubernetes-Benutzern zwei Optionen zur Verfügung:

* **Statisches** Erstellen von Azure NetApp Files-Volumes: In diesem Szenario erfolgt die Erstellung von Volumes außerhalb von AKS. Volumes werden mithilfe der `az`/Azure-Benutzeroberfläche erstellt und dann durch die Erstellung eines `PersistentVolume` für Kubernetes verfügbar gemacht. Statisch erstellte Azure NetApp Files-Volumes haben viele Einschränkungen (z. B. können sie nicht erweitert werden, müssen überprovisioniert werden usw.) und werden für die meisten Anwendungsfälle nicht empfohlen.
* Erstellen Sie Azure NetApp Files Volumes **nach Bedarf**, orchestriert durch Kubernetes: Diese Methode ist die **bevorzugte** Betriebsart zur Erstellung mehrerer Volumes direkt über Kubernetes und wird mit [Astra Trident](https://docs.netapp.com/us-en/trident/index.html) erreicht. Astra Trident ist ein CSI-kompatibler dynamischer Speicherorchestrator, der bei der nativen Bereitstellung von Volumes über Kubernetes hilft.

Die Verwendung eines CSI-Treibers zur direkten Nutzung von Azure NetApp Files Volumes aus AKS-Workloads wird **für die meisten Anwendungsfälle dringend empfohlen**. Diese Anforderung wird mithilfe von Astra Trident erfüllt, einem Open-Source-Orchestrator für dynamischen Speicher für Kubernetes. Astra Trident ist ein Storage-Orchestrator der Enterprise-Klasse, der speziell für Kubernetes entwickelt wurde und vollständig von NetApp unterstützt wird. Er vereinfacht den Zugriff auf Speicher über Kubernetes-Cluster durch die Automatisierung der Speicherbereitstellung. Sie können den Container Storage Interface-Treiber (CSI-Treiber) von Astra Trident für Azure NetApp Files nutzen, um zugrunde liegende Details zu abstrahieren und bedarfsorientierte Volumes zu erstellen, zu erweitern und Momentaufnahmen zu erstellen. Darüber hinaus können Sie mit dem auf dem Trident basierenden [Dienst zur](https://cloud.netapp.com/astra-control) Sicherung, Wiederherstellung, Verschiebung und Verwaltung des Anwendungsdatenlebenszyklus Ihrer AKS-Workloads clusterübergreifend innerhalb und über Azure-Regionen hinweg den Anwendungsdatenlebenszyklus verwenden, um Ihre Geschäfts- und Dienstkontinuitätsanforderungen zu erfüllen.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Ihr AKS-Cluster muss sich auch [in einer Region befinden, die Azure NetApp Files unterstützt][anf-regions].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

### <a name="prerequisites"></a>Voraussetzungen

Bei Verwendung von Azure NetApp Files ist Folgendes zu beachten:

* Azure NetApp Files ist nur [in ausgewählten Azure-Regionen][anf-regions] verfügbar.
* Nach der ersten Bereitstellung eines AKS-Clusters können Sie Azure NetApp Files-Volumes entweder statisch oder dynamisch bereitstellen.
* Wenn Sie die dynamische Bereitstellung für Azure NetApp Files verwenden möchten, installieren und konfigurieren Sie [Astra Trident](https://docs.netapp.com/us-en/trident/index.html), Version 19.07 oder höher.

## <a name="configure-azure-netapp-files"></a>Konfigurieren von Azure NetApp Files

Registrieren des *Microsoft NetApp*-Ressourcenanbieters:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dieser Vorgang kann einige Zeit in Anspruch nehmen.

Wenn Sie ein Azure NetApp-Konto für die Verwendung mit AKS erstellen, müssen Sie das Konto in der **Knoten**-Ressourcengruppe erstellen. Rufen Sie zunächst den Namen der Ressourcengruppe mit dem Befehl [az aks show][az-aks-show] ab, und fügen Sie den Abfrageparameter `--query nodeResourceGroup` hinzu. Im folgenden Beispiel wird die Knoten-Ressourcengruppe für den AKS-Cluster namens *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Erstellen Sie ein Azure NetApp Files-Konto in **Knoten**-Ressourcengruppe und in derselben Region wie Ihr AKS-Cluster, indem Sie [az netappfiles account create][az-netappfiles-account-create] verwenden. Im folgenden Beispiel wird ein Konto namens *myaccount1* in der Ressourcengruppe *MC_myResourceGroup_myAKSCluster_eastus* und in der Region *eastus* erstellt:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Erstellen Sie mit [az netappfiles pool create][az-netappfiles-pool-create] einen neuen Kapazitätspool. Im folgenden Beispiel wird ein neuer Kapazitätspool namens *mypool1* mit einer Größe von 4 TB und dem Servicelevel *Premium* erstellt:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Erstellen Sie ein Subnetz, um mithilfe von [az network vnet subnet create][az-network-vnet-subnet-create][an Azure NetApp Files zu delegieren][anf-delegate-subnet]. *Dieses Subnetz muss sich dabei im selben virtuellen Netzwerk wie Ihr AKS-Cluster befinden.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Volumes können entweder statisch oder dynamisch bereitgestellt werden. Beide Optionen werden im Folgenden ausführlich behandelt.

## <a name="provision-azure-netapp-files-volumes-statically"></a>Statisches Bereitstellen von Azure NetApp Files-Volumes

Erstellen Sie ein Volume mithilfe von [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

### <a name="create-the-persistentvolume"></a>Erstellen von PersistentVolume

Listen Sie die Details Ihres Volumes mithilfe von [az netappfiles volume show][az-netappfiles-volume-show] auf.

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Erstellen Sie eine Datei `pv-nfs.yaml`, die ein PersistentVolume definiert. Ersetzen Sie `path` durch das *creationToken* und `server` durch die *ipAddress* aus dem vorherigen Befehl. Beispiel:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aktualisieren Sie *server* und *path* (Pfad) auf die Werte Ihres NFS-Volumes (Network File System), das Sie im vorherigen Schritt erstellt haben. Erstellen Sie das PersistentVolume mit dem Befehl [kubectl apply][kubectl-apply]:

```console
kubectl apply -f pv-nfs.yaml
```

Vergewissern Sie sich, dass der *Status* von PersistentVolume *Verfügbar* ist, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pv pv-nfs
```

### <a name="create-the-persistentvolumeclaim"></a>Erstellen von PersistentVolumeClaim

Erstellen Sie eine Datei `pvc-nfs.yaml`, die ein PersistentVolume definiert. Beispiel:

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
      storage: 1Gi
```

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] den PersistentVolumeClaim:

```console
kubectl apply -f pvc-nfs.yaml
```

Vergewissern Sie sich, dass der *Status* von PersistentVolumeClaim *Gebunden* ist, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pvc pvc-nfs
```

### <a name="mount-with-a-pod"></a>Einbinden mit einem Pod

Erstellen Sie eine Datei `nginx-nfs.yaml`, die einen Pod definiert, der einen PersistentVolumeClaim verwendet. Beispiel:

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
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
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

Vergewissern Sie sich, dass der Pod *Ausgeführt* wird, indem Sie den Befehl [kubectl describe][kubectl-describe] verwenden:

```console
kubectl describe pod nginx-nfs
```

Stellen Sie sicher, dass Ihr Volume in den Pod eingebunden wurde, indem Sie [kubectl exec][kubectl-exec] verwenden, um eine Verbindung mit dem Pod herzustellen, und überprüfen Sie dann mit `df -h`, ob das Volume eingebunden ist.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>Dynamisches Bereitstellen von Azure NetApp Files-Volumes

### <a name="install-and-configure-astra-trident"></a>Installieren und Konfigurieren von Astra Trident

Um Volumes dynamisch bereitstellen zu können, müssen Sie Astra Trident installieren. Astra Trident ist der dynamische Speicherbereitstellungsdienst von NetApp, der speziell für Kubernetes entwickelt wurde. Vereinfachen Sie die Speichernutzung für Kubernetes-Anwendungen mithilfe von [CSI-Treibern (Container Storage Interface)](https://kubernetes-csi.github.io/docs/) nach Industriestandard. Astra Trident wird in Kubernetes-Clustern als Pods bereitgestellt und bietet dynamische Speicherorchestrierungsdienste für Ihre Kubernetes-Workloads.

Weitere Informationen finden Sie in der [Dokumentation]https://docs.netapp.com/us-en/trident/index.html).

Bevor Sie mit dem nächsten Schritt fortfahren, müssen Sie Folgendes durchführen:

1. **Installieren Sie Astra Trident**. Trident kann über den Operator/Helm-Chart/`tridentctl` installiert werden. In den unten angegebenen Anweisungen wird erläutert, wie Astra Trident über den Operator installiert werden kann. Informationen zur Funktionsweise der anderen Installationsmethoden finden Sie im [Installationshandbuch](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html).

2. **Erstellen Sie ein Back-End**. Um Astra Trident über das Azure NetApp Files-Abonnement und den Ort zu informieren, an dem Volumes erstellt werden müssen, wird ein Back-End erstellt. Für diesen Schritt sind Details zum Konto erforderlich, das im vorherigen Schritt erstellt wurde.

#### <a name="install-astra-trident-using-the-operator"></a>Installieren von Astra Trident über den Operator

In diesem Abschnitt werden Sie durch die Installation von Astra Trident über den Operator geleitet. Sie können die Installation auch über eine der anderen Methoden durchführen:

* [Helm-Chart](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-operator.html).
* [`tridentctl`](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-tridentctl.html).

Informationen zur Funktionsweise der einzelnen Optionen und zum Ermitteln der für Sie am besten geeigneten Option finden Sie unter [Bereitstellen von Trident](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html).

Laden Sie Astra Trident aus dem [GitHub-Repository](https://github.com/NetApp/trident/releases) herunter. Wählen Sie die gewünschte Version aus, und laden Sie das Installationspaket herunter.

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
Stellen Sie den Operator über `deploy/bundle.yaml` bereit.

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

Erstellen Sie einen `TridentOrchestrator` zum Installieren von Astra Trident.

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

Der Operator wird anhand der Parameter installiert, die in der `TridentOrchestrator`-Spezifikation bereitgestellt werden. Informationen zu den Konfigurationsparametern und Beispiel-Back-Ends finden Sie in den ausführlichen Leitfäden für [Installation](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html) und [Back-End](https://docs.netapp.com/us-en/trident/trident-use/backends.html).

Vergewissern Sie sich, dass Astra Trident installiert wurde. 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>Erstellen eines Back-Ends

Erstellen Sie nach der Installation von Astra Trident ein Back-End, das auf Ihr Azure NetApp Files-Abonnement verweist.

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

Vor dem Ausführen des Befehls müssen Sie `backend-anf.yaml` mit Details zum Azure NetApp Files-Abonnement aktualisieren:

* `subscriptionID` für das Azure-Abonnement, in dem Azure NetApp Files aktiviert ist. The 
* `tenantID`, `clientID` und `clientSecret` aus einer [App-Registrierung](../active-directory/develop/howto-create-service-principal-portal.md) in Azure Active Directory (AD) mit ausreichenden Berechtigungen für den Azure NetApp Files-Dienst. Die App-Registrierung muss die in Azure vordefinierte Rolle `Owner` oder `Contributor` aufweisen.
* Azure-Standort, der mindestens ein delegiertes Subnetz enthält.

Darüber hinaus können Sie einen anderen Servicelevel angeben. Azure NetApp Files stellt drei [Servicelevel](../azure-netapp-files/azure-netapp-files-service-levels.md) bereit: Standard, Premium und Ultra.

### <a name="create-a-storageclass"></a>Erstellen einer StorageClass

Mit einer Speicherklasse wird festgelegt, wie eine Speichereinheit dynamisch in einem persistenten Volume erstellt wird. Um Azure NetApp Files-Volumes zu nutzen, muss eine Speicherklasse erstellt werden. Erstellen Sie eine Datei namens `anf-storageclass.yaml`, und kopieren Sie das unten angegebene Manifest.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

Verwenden Sie den Befehl [kubectl apply][kubectl-apply] zum Erstellen der Speicherklasse:

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>Erstellen eines PersistentVolumeClaim

Ein PersistentVolumeClaim (PVC) ist eine Speicheranforderung von einem Benutzer. Nach der Erstellung eines PersistentVolumeClaim erstellt Astra Trident automatisch ein Azure NetApp Files-Volume und macht es für Kubernetes-Workloads verfügbar.

Erstellen Sie eine Datei namens `anf-pvc.yaml`, und geben Sie das folgende Manifest an. In diesem Beispiel wird ein 1-TiB-Volume mit *ReadWriteMany* erstellt.

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] einen Anspruch auf ein persistentes Volume:

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>Verwenden des persistenten Volumes

Nachdem der PVC erstellt wurde, kann ein Pod für den Zugriff auf das Azure NetApp Files-Volume gestartet werden. Mithilfe des folgenden Manifests kann ein NGINX-Pod definiert werden, der das im vorherigen Schritt erstellte Azure NetApp Files-Volume einbindet. In diesem Beispiel wird das Volume unter `/mnt/data` eingebunden.

Erstellen Sie eine Datei namens `anf-nginx-pod.yaml`, die das folgende Manifest enthält:

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

Erstellen Sie den Pod mit dem Befehl [kubectl apply][kubectl-apply].

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

Kubernetes hat nun einen Pod mit dem eingebundenen Volume erstellt, auf das im Container `nginx` unter `/mnt/data` zugegriffen werden kann. Bestätigen Sie dies durch Überprüfen der Ereignisprotokolle für den Pod über `kubectl describe`:

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident unterstützt zahlreiche Features für Azure NetApp Files, darunter Folgende:

* [Erweitern von Volumes](https://docs.netapp.com/us-en/trident/trident-use/vol-expansion.html)
* [Bedarfsbasierte Volumemomentaufnahmen](https://docs.netapp.com/us-en/trident/trident-use/vol-snapshots.html)
* [Importieren von Volumes](https://docs.netapp.com/us-en/trident/trident-use/vol-import.html)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure NetApp Files finden Sie unter [Was ist Azure NetApp Files?][anf].

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
