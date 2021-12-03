---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters mit Intel SGX-Knoten für Enclave Confidential Container mithilfe der Azure CLI'
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI einen AKS-Cluster (Azure Kubernetes Service) mit vertraulichen Knoten erstellen und eine Hallo Welt-App bereitstellen.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: 142f68d332b65bb00e2b9dd4c4ea8785481e9920
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101468"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Schnellstart: Bereitstellen eines AKS-Clusters mit Confidential Computing-Knoten über die Azure CLI

In dieser Schnellstartanleitung verwenden Sie die Azure CLI, um einen AKS-Cluster (Azure Kubernetes Service) mit Confidential Computing-Knoten (DCsv2/DCSv3) bereitzustellen. Anschließend führen Sie eine einfache Hallo Welt-Anwendung in einer Enclave aus. Sie können auch über das Azure-Portal einen Cluster bereitstellen und Confidential Computing-Knoten hinzufügen. Der Schwerpunkt dieser Schnellstartanleitung liegt jedoch auf der Azure CLI.

AKS ist ein verwalteter Kubernetes-Dienst, mit dem Entwickler oder Clusteroperatoren Cluster schnell bereitstellen und verwalten können. Weitere Informationen finden Sie in der [Einführung in AKS](../aks/intro-kubernetes.md) und in der Übersicht über [Confidential Computing-Knoten in Azure Kubernetes Service](confidential-nodes-aks-overview.md).

Features von Confidential Computing-Knoten:

- Linux-Workerknoten, die Linux-Container unterstützen
- Virtueller Computer der 2. Generation mit Ubuntu 18.04-VM-Knoten
- Intel SGX-fähige CPU zum Ausführen Ihrer Container in einer geschützten Vertraulichkeits-Enclave mithilfe von Encrypted Page Cache Memory (EPC). Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure Confidential Computing](./confidential-nodes-aks-faq.yml).
- Vorinstallierter Intel SGX DCAP-Treiber auf den Confidential Computing-Knoten. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure Confidential Computing](./confidential-nodes-aks-faq.yml).

> [!NOTE]
> Virtuelle DCsv2/DCsv3-Computer nutzen spezielle Hardware, für die höhere Preise gelten und die möglicherweise nicht in allen Regionen verfügbar ist. Weitere Informationen zu verfügbaren SKUs und unterstützten Regionen finden Sie [hier](virtual-machine-solutions-sgx.md). DCsv3-VMs befinden sich derzeit in begrenzten Regionen in der Vorschau. Weitere Informationen finden Sie auf der oben erwähnten Seite. 


## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

- Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Auf dem Bereitstellungscomputer muss mindestens die Azure CLI-Version 2.0.64 installiert und konfiguriert sein. 

  Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
- In Ihrem Abonnement müssen mindestens sechs DCsv2-Kerne verfügbar sein. 

  Das Kontingent für Confidential Computing liegt standardmäßig bei acht VM-Kernen pro Abonnement. Wenn Sie einen Cluster bereitstellen möchten, der mehr als acht Kerne erfordert, gehen Sie wie [hier](../azure-portal/supportability/per-vm-quota-requests.md) beschrieben vor, um ein Ticket für die Kontingenterhöhung zu erstellen.

## <a name="create-an-aks-cluster-with-enclave-aware-confidential-computing-nodes-and-add-on"></a>Erstellen Sie ein AKS-Cluster mit Confidential Computing-Knoten und -Add-On

Verwenden Sie die folgenden Anweisungen, um einen AKS-Cluster mit aktiviertem Confidential Computing-Add-On zu erstellen, dem Cluster einen Knotenpool hinzuzufügen und zu überprüfen, was Sie erstellt haben.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Erstellen eines AKS-Clusters mit einem Systemknotenpool

> [!NOTE]
> Wenn Sie bereits über einen AKS-Cluster verfügen, der die zuvor genannten Anforderungskriterien erfüllt, können Sie [direkt mit dem nächsten Abschnitt fortfahren](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster), um einen Pool mit Confidential Computing-Knoten hinzuzufügen.

Erstellen Sie zunächst mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe für den Cluster. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus2* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Erstellen Sie nun mithilfe des Befehls [az aks create][az-aks-create] einen AKS-Cluster mit aktiviertem Confidential Computing-Add-On:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addons confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Hinzufügen eines Benutzerknotenpool mit Confidential Computing-Funktionen zum AKS-Cluster 

Führen Sie den folgenden Befehl aus, um einen Benutzerknoten der Größe `Standard_DC2s_v2` mit drei Knoten zum AKS-Cluster hinzuzufügen. Sie können eine andere SKU aus der [Liste mit unterstützten DCsv2/Dcsv3-SKUs und -Regionen](../virtual-machines/dcv2-series.md) auswählen.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Nach der Ausführung des Befehls sollte ein neuer Knotenpool mit DCsv2 mit Confidential Computing-Add-On-DaemonSets ([SGX-Geräte-Plug-In](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)) sichtbar sein.

### <a name="verify-the-node-pool-and-add-on"></a>Überprüfen des Knotenpools und Add-Ons

Rufen Sie die Anmeldeinformationen für Ihren AKS-Cluster mit dem Befehl [az aks get-credentials][az-aks-get-credentials] ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Stellen Sie mithilfe des Befehls `kubectl get pods` sicher, dass die Knoten ordnungsgemäß erstellt wurden und die SGX-bezogenen DaemonSets in DCsv2-Knotenpools ausgeführt werden:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Wenn die Ausgabe wie der obige Code aussieht, ist Ihr AKS-Cluster bereit für die Ausführung vertraulicher Anwendungen.

Sie können in dieser Schnellstartanleitung zum Abschnitt [Bereitstellen von „Hallo Welt“ aus einer isolierten Enklavenanwendung](#hello-world) wechseln, um eine App in einer Enclave zu testen. Alternativ können Sie wie folgt vorgehen, um weitere Knotenpools in AKS hinzuzufügen. (AKS unterstützt die parallele Verwendung von SGX-Knotenpools und SGX-fremden Knotenpools).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Hinzufügen eines Confidential Computing-Knotenpools zu einem vorhandenen AKS-Cluster<a id="existing-cluster"></a>

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen AKS-Cluster ausführen, der die zuvor in dieser Schnellstartanleitung aufgeführten Voraussetzungen erfüllt.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Aktivieren des Confidential Computing-AKS-Add-Ons für den vorhandenen Cluster

Führen Sie den folgenden Befehl aus, um das Confidential Computing-Add-On zu aktivieren:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2dcsv3-user-node-pool-to-the-cluster"></a>Hinzufügen eines DCsv2/Dcsv3-Knotenpools zum Cluster

> [!NOTE]
> Damit Sie die Confidential Computing-Funktion nutzen können, muss der vorhandene AKS-Cluster über mindestens einen Knotenpool verfügen, der auf der SKU für virtuelle DCsv2/Dcsv3-Computer basiert. Weitere Informationen zu den SKUs für virtuelle Confidential Computing-Computer der DCs-v2-Serie finden Sie [hier](virtual-machine-solutions-sgx.md).

Führen Sie den folgenden Befehl aus, um einen Knotenpool zu erstellen:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Überprüfen Sie, ob der neue Knotenpool mit dem Namen *confcompool1* erstellt wurde:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Bestätigen, dass DaemonSets in vertraulichen Knotenpools ausgeführt werden

Melden Sie sich bei Ihrem vorhandenen AKS-Cluster an, um die folgende Überprüfung auszuführen:

```console
kubectl get nodes
```

Die Ausgabe sollte den neu hinzugefügten Pool *confcompool1* im AKS-Cluster enthalten. Möglicherweise werden auch andere DaemonSets angezeigt.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Wenn die Ausgabe wie der obige Code aussieht, ist Ihr AKS-Cluster bereit für die Ausführung vertraulicher Anwendungen. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Bereitstellen von „Hallo Welt“ aus einer isolierten Enklavenanwendung <a id="hello-world"></a>
Sie können jetzt eine Testanwendung bereitstellen. 

Erstellen Sie eine Datei mit dem Namen *hello-world-enclave.yaml*, und fügen Sie das folgende YAML-Manifest ein. Sie finden diesen Beispielanwendungscode im [Open Enclave-Projekt](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Bei dieser Bereitstellung wird davon ausgegangen, dass Sie das Add-On *confcom* bereitgestellt haben.

> [!NOTE]
> Im folgenden Beispiel wird ein öffentliches Container-Image von Docker Hub abgerufen. Es wird empfohlen, ein Pullgeheimnis für die Authentifizierung mithilfe eines Docker Hub-Kontos einrichten, anstatt einen anonymen Pull Request zu verwenden. Um die Zuverlässigkeit bei der Arbeit mit öffentlichen Inhalten zu verbessern, sollten Sie das Image in eine private Azure-Containerregistrierung importieren und dort verwalten. [Erfahren Sie mehr über die Arbeit mit öffentlichen Images](../container-registry/buffer-gate-public-content.md).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Erstellen Sie nun mithilfe des Befehls `kubectl apply` einen Beispielauftrag, der in einer sicheren Enklave geöffnet wird, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Durch Ausführen der folgenden Befehle können Sie sich vergewissern, dass von der Workload erfolgreich eine vertrauenswürdige Ausführungsumgebung (Enklave) erstellt wurde:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den folgenden Befehl, um den in dieser Schnellstartanleitung erstellten Confidential Computing-Knotenpool zu entfernen: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Verwenden Sie zum Löschen des AKS-Clusters den folgenden Befehl: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie Python, Node oder andere Anwendungen über vertrauliche Container aus. Verwenden Sie dazu die [Beispiele für vertrauliche Container auf GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Führen Sie mithilfe der [Beispiele für Enclave-fähige Azure-Container auf GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/) Enclave-fähige Anwendungen aus.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
