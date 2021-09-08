---
title: Verwenden von GPUs in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie GPUs für Hochleistungscompute- oder grafikintensive Workloads in Azure Kubernetes Service (AKS) verwendet werden können.
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: fa7415f015ad17cc2e8a5ff4822c8ff53578f054
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340292"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Verwenden von GPUs für computeintensive Workloads in Azure Kubernetes Service (AKS)

GPUs (Graphical Processing Units) werden häufig für computeintensive Workloads (also etwa für grafikintensive und visualisierungsorientierte Workloads) verwendet. AKS unterstützt die Erstellung von GPU-fähigen Knotenpools, um diese computrintensiven Workloads in Kubernetes auszuführen. Weitere Informationen zu verfügbaren GPU-fähigen virtuellen Computern finden Sie unter [GPU-optimierte VM-Größen in Azure][gpu-skus]. Für die AKS-Knotenpools wird die Mindestgröße *Standard_NC6* empfohlen.

> [!NOTE]
> GPU-fähige virtuelle Computer verfügen über spezielle Hardware, für die höhere Preise gelten und die möglicherweise nicht in allen Regionen verfügbar ist. Weitere Informationen finden Sie in der [Preisübersicht][azure-pricing] sowie auf der [Website zur regionalen Verfügbarkeit][azure-availability].

Die Verwendung GPU-fähiger Knotenpools steht aktuell nur für Linux-Knotenpools zur Verfügung.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, lesen Sie [Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters über die Azure-Befehlszeilenschnittstelle][aks-quickstart].

Außerdem muss mindestens die Version 2.0.64 der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="get-the-credentials-for-your-cluster"></a>Abrufen der Anmeldeinformationen für Ihren Cluster

Rufen Sie die Anmeldeinformationen für Ihren AKS-Cluster mit dem Befehl [az aks get-credentials][az-aks-get-credentials] ab. Im folgenden Beispiel werden die Anmeldeinformationen für den Cluster *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-the-nvidia-device-plugin"></a>Hinzufügen des NVIDIA-Geräte-Plug-Ins

Es gibt zwei Optionen zum Hinzufügen des NVIDIA-Geräte-Plug-Ins:

* Verwenden des AKS-GPU-Images
* Manuelles Installieren des NVIDIA-Geräte-Plug-Ins

> [!WARNING]
> Sie können jede der oben genannten Optionen verwenden. Installieren Sie jedoch nicht das NVIDIA-Geräte-Plug-In-DaemonSet mit Clustern, in denen das AKS-GPU-Image verwendet wird.

### <a name="update-your-cluster-to-use-the-aks-gpu-image-preview"></a>Aktualisieren des Clusters für die Verwendung des AKS-GPU-Images (Vorschau)

AKS stellt ein vollständig konfiguriertes AKS-Image bereit, in dem das [NVIDIA-Geräte-Plug-In für Kubernetes][nvidia-github] bereits enthalten ist.

Registrieren Sie das Feature `GPUDedicatedVHDPreview`:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Es kann einige Minuten dauern, bis der Status als **Registriert** angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az_feature_list) überprüfen:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Wenn der Status als registriert angezeigt wird, können Sie die Registrierung des `Microsoft.ContainerService`-Ressourcenanbieters mit dem Befehl [az provider register](/cli/azure/provider#az_provider_register) aktualisieren:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Verwenden Sie zum Installieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
```

Verwenden Sie zum Aktualisieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension update --name aks-preview
```

## <a name="add-a-node-pool-for-gpu-nodes"></a>Hinzufügen eines Knotenpools für GPU-Knoten

Verwenden Sie den Befehl [az aks nodepool add][az-aks-nodepool-add], um dem Cluster einen Knotenpool hinzuzufügen.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --aks-custom-headers UseGPUDedicatedVHD=true \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

Mit dem obigen Befehl wird dem Cluster *myAKSCluster* in der Ressourcengruppe *myResourceGroup* ein Knotenpool mit dem Namen *gpunp* hinzugefügt. Der Befehl legt außerdem die VM-Größe für die Knoten im Knotenpool auf *Standard_NC6* fest, aktiviert die automatische Clusterskalierung, konfiguriert die automatische Clusterskalierung so, dass mindestens ein Knoten und maximal drei Knoten im Knotenpool beibehalten werden, gibt einen spezialisierten AKS-GPU-Imageknoten im neuen Knotenpool und den Taint *sku=gpu:NoSchedule* für den Knotenpool an.

> [!NOTE]
> Ein Taint und eine VM-Größe können für Knotenpools nur während ihrer Erstellung festgelegt werden, die Autoskalierungseinstellungen können jedoch jederzeit aktualisiert werden.

> [!NOTE]
> Verwenden Sie *--aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true*, wenn Ihre GPU-SKU VMs der 2. Generation erfordert. Zum Beispiel:
> 
> ```azurecli
> az aks nodepool add \
>    --resource-group myResourceGroup \
>    --cluster-name myAKSCluster \
>    --name gpunp \
>    --node-count 1 \
>    --node-vm-size Standard_NC6 \
>    --node-taints sku=gpu:NoSchedule \
>    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
>    --enable-cluster-autoscaler \
>    --min-count 1 \
>    --max-count 3
> ```

### <a name="manually-install-the-nvidia-device-plugin"></a>Manuelles Installieren des NVIDIA-Geräte-Plug-Ins

Alternativ können Sie ein DaemonSet für das NVIDIA-Geräte-Plug-In bereitstellen. Diese DaemonSet führt einen Pod für jeden Knoten aus, um die erforderlichen Treiber für die GPUs bereitzustellen.

Fügen Sie mit dem Befehl [az aks nodepool add][az-aks-nodepool-add] dem Cluster einen Knotenpool hinzu.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

Mit dem obigen Befehl wird dem Cluster *myAKSCluster* in der Ressourcengruppe *myResourceGroup* ein Knotenpool mit dem Namen *gpunp* hinzugefügt. Der Befehl legt außerdem die VM-Größe für die Knoten im Knotenpool auf *Standard_NC6* fest, aktiviert die automatische Clusterskalierung, konfiguriert die automatische Clusterskalierung so, dass mindestens ein Knoten und maximal drei Knoten im Knotenpool beibehalten werden, und gibt den Taint *sku=gpu:NoSchedule* für den Knotenpool an.

> [!NOTE]
> Ein Taint und eine VM-Größe können für Knotenpools nur während ihrer Erstellung festgelegt werden, die Autoskalierungseinstellungen können jedoch jederzeit aktualisiert werden.

Erstellen Sie mit dem Befehl [kubectl create namespace][kubectl-create] einen Namespace, z. B. *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Erstellen Sie eine Datei mit dem Namen *nvidia-device-plugin-ds.yaml*, und fügen Sie das folgende YAML-Manifest ein. Dieses Manifest wird im Rahmen des Projekts [NVIDIA device plugin for Kubernetes][nvidia-github] (NVIDIA-Geräte-Plug-In für Kubernetes) bereitgestellt.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Erstellen Sie mit [kubectl apply][kubectl-apply] das DaemonSet, und vergewissern Sie sich, dass das NVIDIA-Geräte-Plug-In erfolgreich erstellt wurde, wie in der folgenden Beispielausgabe gezeigt:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Bestätigen, dass GPUs planbar sind

Nachdem Ihr AKS-Cluster erstellt wurde, bestätigen Sie, dass GPUs in Kubernetes planbar sind. Listen Sie zunächst die Knoten in Ihrem Cluster mit dem Befehl [kubectl get nodes][kubectl-get] auf:

```console
$ kubectl get nodes

NAME                   STATUS   ROLES   AGE   VERSION
aks-gpunp-28993262-0   Ready    agent   13m   v1.20.7
```

Verwenden Sie nun den Befehl [kubectl describe node][kubectl-describe], um zu bestätigen, dass die GPUs planbar sind. Unter dem Abschnitt *Capacity* (Kapazität) sollte die GPU als `nvidia.com/gpu:  1` aufgelistet werden.

Das folgende verkürzte Beispiel zeigt, dass eine GPU für den Knoten *aks-nodepool1-18821093-0* verfügbar ist:

```console
$ kubectl describe node aks-gpunp-28993262-0

Name:               aks-gpunp-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
[...]
 nvidia.com/gpu:                 1
[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Ausführen einer GPU-fähigen Workload

Um die GPU in Aktion zu sehen, planen Sie eine GPU-fähige Workload mit der entsprechenden Ressourcenanforderung. In diesem Beispiel wird ein [TensorFlow](https://www.tensorflow.org/)-Auftrag für das [MNIST-Dataset](http://yann.lecun.com/exdb/mnist/) ausgeführt.

Erstellen Sie eine Datei mit dem Namen *samples-tf-mnist-demo.yaml*, und fügen Sie das folgende YAML-Manifest ein. Das folgende Auftragsmanifest enthält ein Ressourcenlimit von `nvidia.com/gpu: 1`:

> [!NOTE]
> Wenn Sie beim Aufruf von Treibern einen Versionsfehler erhalten (z. B., dass die CUDA-Treiberversion für die CUDA-Laufzeitversion nicht ausreicht), überprüfen Sie das NVIDIA-Treibermatrix-Kompatibilitätsdiagramm: [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
      tolerations:
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
```

Führen Sie den Auftrag mithilfe des Befehls [kubectl apply][kubectl-apply] aus. Dieser Befehl analysiert die Manifestdatei und erstellt die definierten Kubernetes-Objekte:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Anzeigen des Status und der Ausgabe der GPU-fähigen Workload

Überwachen Sie den Status des Auftrags mithilfe des Befehls [kubectl get jobs][kubectl-get] mit dem Argument `--watch`. Es kann einige Minuten dauern, um zunächst das Image zu pullen und das Dataset zu verarbeiten. Wenn die Spalte *ABSCHLÜSSE* die Angabe *1/1* enthält, wurde der Auftrag erfolgreich abgeschlossen. Beenden Sie den Befehl `kubetctl --watch` durch Drücken von *STRG+C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Um die Ausgabe der GPU-fähigen Workload anzuzeigen, rufen Sie zunächst mithilfe des Befehls [kubectl get pods][kubectl-get] den Namen des Pods ab:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Nun verwenden Sie den Befehl [kubectl logs][kubectl-logs], um die Podprotokolle anzuzeigen. Die folgenden Beispielpodprotokolle bestätigen, dass das entsprechende GPU-Gerät ermittelt wurde: `Tesla K80`. Geben Sie den Namen für Ihren eigenen Pod an:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="use-container-insights-to-monitor-gpu-usage"></a>Verwenden von Container Insights zum Überwachen der GPU-Nutzung

Für [Container Insights mit AKS][aks-container-insights] sind die folgenden Metriken zum Überwachen der GPU-Nutzung verfügbar.

| Metrikname | Metrikdimension (Tags) | Beschreibung |
|-------------|-------------------------|-------------|
| containerGpuDutyCycle | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor`   | Der Prozentsatz der Zeit im Verlauf des letzten Beispielzeitraums (60 Sekunden), während dessen die GPU ausgelastet war/aktiv die Verarbeitung für einen Container ausgeführt hat. Der Arbeitszyklus ist eine Zahl zwischen 1 und 100. |
| containerGpuLimits | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | In jedem Container können Grenzwerte als eine oder mehrere GPUs angegeben werden. Es ist nicht möglich, einen Bruchteil einer GPU anzufordern oder einzuschränken. |
| containerGpuRequests | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | Jeder Container kann einen oder mehrere GPUs anfordern. Es ist nicht möglich, einen Bruchteil einer GPU anzufordern oder einzuschränken. |
| containerGpumemoryTotalBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Menge an GPU-Arbeitsspeicher in Bytes, die für einen bestimmten Container verwendet werden kann. |
| containerGpumemoryUsedBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Menge an GPU-Arbeitsspeicher in Bytes, die für einen bestimmten Container verwendet wird. |
| nodeGpuAllocatable | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Anzahl von GPUs in einem Knoten, die von Kubernetes verwendet werden können. |
| nodeGpuCapacity | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Gesamtanzahl der GPUs in einem Knoten. |

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der zugehörigen Kubernetes-Objekte, die in diesem Artikel erstellt wurden, den Befehl [kubectl delete job][kubectl delete] wie folgt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Nächste Schritte

Um Apache Spark-Aufträge auszuführen, lesen Sie [Ausführen von Apache Spark-Aufträgen in AKS][aks-spark].

Weitere Informationen zum Ausführen von Workloads für Machine Learning (ML) in Kubernetes finden Sie unter [Kubeflow-Labs][kubeflow-labs].

Informationen zur Verwendung von Azure Kubernetes Service mit Azure Machine Learning finden Sie in folgenden Artikeln:

* [Bereitstellen eines Modells in Azure Kubernetes Service][azureml-aks]
* [Bereitstellen eines Deep Learning-Modells für das Rückschließen mit einer GPU][azureml-gpu]
* [High-Performance-Bereitstellung mit Triton Inference Server (Vorschau)][azureml-triton]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
[azureml-aks]: ../machine-learning/how-to-deploy-azure-kubernetes-service.md
[azureml-gpu]: ../machine-learning/how-to-deploy-inferencing-gpus.md
[azureml-triton]: ../machine-learning/how-to-deploy-with-triton.md
[aks-container-insights]: monitor-aks.md#container-insights