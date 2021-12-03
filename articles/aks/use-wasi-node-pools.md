---
title: Erstellen von WebAssembly System Interface-Knotenpools (WASI) in Azure Kubernetes Service (AKS) zum Ausführen Ihrer WebAssembly-Workload (WASM) (Vorschau)
description: Erfahren Sie, wie Sie einen WebAssembly System Interface-Knotenpool (WASI) in Azure Kubernetes Service (AKS) erstellen, um Ihre WebAssembly-Workload (WASM) in Kubernetes auszuführen.
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 91649a6c7893970eee5685554385eed1493c0094
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265961"
---
# <a name="create-webassembly-system-interface-wasi-node-pools-in-azure-kubernetes-service-aks-to-run-your-webassembly-wasm-workload-preview"></a>Erstellen von WebAssembly System Interface-Knotenpools (WASI) in Azure Kubernetes Service (AKS) zum Ausführen Ihrer WebAssembly-Workload (WASM) (Vorschau)

[WebAssembly (WASM)][wasm] ist ein Binärformat, das für schnelle Downloads und maximale Ausführungsgeschwindigkeit in einer WASM-Runtime optimiert ist. Eine WASM-Runtime ist für die Ausführung in einer Zielarchitektur und die Ausführung von WebAssemblys in einer Sandbox konzipiert – vom Hostcomputer isoliert und mit nahezu nativer Leistung. Standardmäßig können WebAssemblys nur dann auf Ressourcen auf dem Host außerhalb der Sandbox zugreifen, wenn dies explizit zugelassen wurde, und sie können nicht über Sockets kommunizieren, um auf Umgebungsvariablen oder HTTP-Datenverkehr zuzugreifen. Der Standard [WASI (WebAssembly System Interface)][wasi] definiert eine API für WASM-Runtimes, um für die Umgebung und Ressourcen außerhalb des Hosts mithilfe eines Funktionsmodells Zugriff auf WebAssemblys bereitzustellen. [Krustlet][krustlet] ist ein Open-Source-Projekt, mit dem WASM-Module in Kubernetes ausgeführt werden können. Krustlet erstellt ein Kubelet, das auf Knoten mit einer WASM/WASI-Runtime ausgeführt wird. Mit AKS können Sie Knotenpools erstellen, die WASM-Assemblys mithilfe von Knoten mit WASM/WASI-Runtimes und Krustlets ausführen.

## <a name="before-you-begin"></a>Vorbereitung

WASM/WASI-Knotenpools befinden sich derzeit in der Vorschau.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

In diesem Artikel wird [Helm 3][helm] verwendet, um das *nginx*-Chart in einer unterstützten Version von Kubernetes zu installieren. Stellen Sie sicher, dass Sie das neueste Release von Helm verwenden und auf das Helm-Repository *bitnami* zugreifen können. Die in diesem Artikel beschriebenen Schritte sind mit früheren Helm-Chart- oder Kubernetes-Versionen möglicherweise nicht kompatibel.

Außerdem müssen die folgenden Ressourcen installiert sein:

* Die aktuelle Version der Azure-Befehlszeilenschnittstelle
* Die Erweiterung `aks-preview`, Version 0.5.34 oder höher

### <a name="register-the-wasmnodepoolpreview-preview-feature"></a>Registrieren der Previewfunktion `WasmNodePoolPreview`

Sie müssen darüber hinaus das Featureflag `WasmNodePoolPreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `WasmNodePoolPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "WasmNodePoolPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WasmNodePoolPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle `aks-preview`

Sie benötigen außerdem die Azure CLI-Erweiterung *aks-preview* in der Version 0.5.34 oder höher. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Einschränkungen

* Sie können WebAssemblys und Container nicht im gleichen Knotenpool ausführen.
* Nur die WebAssembly-Runtime (WASI) ist über den Anbieter „Wasmtime“ verfügbar.
* Die WASM/WASI-Knotenpools können nicht für den Systemknotenpool verwendet werden.
* Der *os-type* für WASM/WASI-Knotenpools muss Linux sein.
* Krustlet funktioniert derzeit nicht mit Azure CNI. Weitere Informationen finden Sie im [GitHub-Issue zur CNI-Unterstützung für Krustlet][krustlet-cni-support].
* Ein Krustlet stellt keine Netzwerkkonfiguration für WebAssemblys bereit. Das WebAssembly-Manifest muss die Netzwerkkonfiguration bereitstellen, z. B. die IP-Adresse.

## <a name="add-a-wasmwasi-node-pool-to-an-existing-aks-cluster"></a>Hinzufügen eines WASM/WASI-Knotenpools zu einem vorhandenen AKS-Cluster

Um einen WASM/WASI-Knotenpool hinzuzufügen, verwenden Sie den Befehl [az aks nodepool add][az-aks-nodepool-add]. Das folgende Beispiel erstellt einen WASI-Knotenpool namens *mywasipool* mit einem Knoten.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mywasipool \
    --node-count 1 \
    --workload-runtime wasmwasi 
```

> [!NOTE]
> Der Standardwert für den Parameter *workload-runtime* lautet *ocicontainer*. Um einen Knotenpool zu erstellen, der Containerworkloads ausführt, lassen Sie den Parameter *workload-runtime* aus, oder legen Sie den Wert auf *ocicontainer* fest.

Überprüfen Sie den Wert *workloadRuntime* mithilfe von `az aks nodepool show`. Zum Beispiel:

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mywasipool
```

Die folgende Beispielausgabe zeigt, dass *mywasipool* den *workloadRuntime*-Typ *WasmWasi* aufweist.

```output
{
  ...
  "name": "mywasipool",
  ..
  "workloadRuntime": "WasmWasi"
}
```

Überprüfen Sie bei einem WASM/WASI, ob der Taint auf `kubernetes.io/arch=wasm32-wagi:NoSchedule` und `kubernetes.io/arch=wasm32-wagi:NoExecute` festgelegt ist, wodurch verhindert wird, dass in diesem Knotenpool Containerpods geplant werden. Außerdem sollte „nodeLabels“ als `kubernetes.io/arch: wasm32-wasi` angezeigt werden, wodurch verhindert wird, dass WASM-Pods in regulären Containerknotenpools (OCI) geplant werden.

> [!NOTE]
> Die Taints für einen WASI-Knotenpool sind bei Verwendung von `az aks nodepool list` nicht sichtbar. Verwenden Sie `kubectl`, um zu überprüfen, ob die Taints auf den Knoten im WASI-Knotenpool festgelegt sind.

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Der unten angegebene Befehl bewirkt Folgendes:  

```azurecli
az aks get-credentials -n myakscluster -g myresourcegroup
```

Verwenden Sie `kubectl get nodes`, um die Knoten in Ihrem Cluster anzuzeigen.

```output
$ kubectl get nodes -o wide
NAME                                 STATUS   ROLES   AGE   VERSION         INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
aks-mywasipool-12456878-vmss000000   Ready    agent    9m   1.0.0-alpha.1   WASINODE_IP   <none>        <unknown>            <unknown>          mvp
aks-nodepool1-12456878-vmss000000    Ready    agent   13m   v1.20.9         NODE1_IP      <none>        Ubuntu 18.04.6 LTS   5.4.0-1059-azure   containerd://1.4.9+azure
```

Speichern Sie den Wert von *WASINODE_IP*, da er in einem späteren Schritt verwendet wird.

Verwenden Sie `kubectl describe node`, um die Bezeichnungen und Taints auf einem Knoten im WASI-Knotenpool anzuzeigen. Das folgende Beispiel zeigt die Details von *aks-mywasipool-12456878-vmss000000*.

```output
$ kubectl describe node aks-mywasipool-12456878-vmss000000

Name:               aks-mywasipool-12456878-vmss000000
Roles:              agent
Labels:             agentpool=mywasipool
...
                    kubernetes.io/arch=wasm32-wagi
...
Taints:             kubernetes.io/arch=wasm32-wagi:NoExecute
                    kubernetes.io/arch=wasm32-wagi:NoSchedule
```


## <a name="running-wasmwasi-workload"></a>Ausführen der WASM/WASI-Workload

Um eine Workload in einem WASM-/WASI-Knotenpool auszuführen, fügen Sie Ihrer Bereitstellung einen Knotenselektor und Toleranzen hinzu. Zum Beispiel:

```yml
...
spec:
  nodeSelector:
    kubernetes.io/arch: "wasm32-wagi"
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
...
```

Um eine Beispielbereitstellung auszuführen, erstellen Sie eine `wasi-example.yaml`-Datei mit der folgenden YAML-Definition:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: krustlet-wagi-demo
  labels:
    app: krustlet-wagi-demo
  annotations:
    alpha.wagi.krustlet.dev/default-host: "0.0.0.0:3001"
    alpha.wagi.krustlet.dev/modules: |
      {
        "krustlet-wagi-demo-http-example": {"route": "/http-example", "allowed_hosts": ["https://api.brigade.sh"]},
        "krustlet-wagi-demo-hello": {"route": "/hello/..."},
        "krustlet-wagi-demo-error": {"route": "/error"},
        "krustlet-wagi-demo-log": {"route": "/log"},
        "krustlet-wagi-demo-index": {"route": "/"}
      }
spec:
  hostNetwork: true
  nodeSelector:
    kubernetes.io/arch: wasm32-wagi
  containers:
    - image: webassembly.azurecr.io/krustlet-wagi-demo-http-example:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-http-example
    - image: webassembly.azurecr.io/krustlet-wagi-demo-hello:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-hello
    - image: webassembly.azurecr.io/krustlet-wagi-demo-index:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-index
    - image: webassembly.azurecr.io/krustlet-wagi-demo-error:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-error
    - image: webassembly.azurecr.io/krustlet-wagi-demo-log:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-log
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
```

Verwenden Sie `kubectl`, um Ihre Beispielbereitstellung auszuführen:

```azurecli-interactive
kubectl apply -f wasi-example.yaml
```

> [!NOTE]
> Der Pod für die Beispielbereitstellung bleibt möglicherweise im Status *Registered*. Dieses Verhalten ist erwartet, und Sie können mit dem nächsten Schritt fortfahren.

Erstellen Sie `values.yaml` mithilfe des folgenden YAML-Beispiels, und ersetzen Sie *WASINODE_IP* durch den Wert aus dem vorherigen Schritt.

```yml
serverBlock: |-
  server {
    listen 0.0.0.0:8080;
    location / {
            proxy_pass http://WASINODE_IP:3001;
    }
  }
```

Fügen Sie mithilfe von [Helm][helm] das Repository *bitnami* hinzu, und installieren Sie das *nginx*-Chart mit der Datei `values.yaml`, die Sie im vorherigen Schritt erstellt haben. Durch die Installation von NGINX mit der obigen `values.yaml`-Datei wird ein Reverseproxy zur Beispielbereitstellung erstellt, sodass Sie über eine externe IP-Adresse darauf zugreifen können.

>[!NOTE]
> Im folgenden Beispiel wird ein öffentliches Container-Image von Docker Hub abgerufen. Es wird empfohlen, ein Pullgeheimnis für die Authentifizierung mithilfe eines Docker Hub-Kontos einrichten, anstatt einen anonymen Pull Request zu verwenden. Um die Zuverlässigkeit bei der Arbeit mit öffentlichen Inhalten zu verbessern, sollten Sie das Image in eine private Azure-Containerregistrierung importieren und dort verwalten. [Erfahren Sie mehr über die Arbeit mit öffentlichen Images][dockerhub-callout].

```console
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install hello-wasi bitnami/nginx -f values.yaml
```

Verwenden Sie `kubectl get service`, um die externe IP-Adresse des Diensts *hello-wasi-ngnix* anzuzeigen.

```output
$ kubectl get service
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
hello-wasi-nginx   LoadBalancer   10.0.58.239   EXTERNAL_IP      80:32379/TCP   112s
kubernetes         ClusterIP      10.0.0.1      <none>           443/TCP        145m
```

Überprüfen Sie mit dem Befehl `curl` im Pfad `/hello` von *EXTERNAL_IP*, ob die Beispielbereitstellung ausgeführt wird.

```azurecli-interactive
curl EXTERNAL_IP/hello
```

Die folgende Beispielausgabe bestätigt, dass die Beispielbereitstellung ausgeführt wird.

```output
$ curl EXTERNAL_IP/hello
hello world
```

> [!NOTE] 
> Informationen zum Veröffentlichen des Diensts in Ihrer eigenen Domäne finden Sie unter [Azure DNS][azure-dns-zone] und dem Projekt [external-dns][external-dns].

## <a name="clean-up"></a>Bereinigen

Verwenden Sie `helm delete`, um NGINX zu entfernen.

```console
helm delete hello-wasi
```

Verwenden Sie `kubectl delete`, um die Beispielbereitstellung zu entfernen.

```azurecli-interactive
kubectl delete -f wasi-example.yaml
```

Verwenden Sie `az aks nodepool delete`, um den WASM/WASI-Knotenpool zu entfernen.

```azurecli-interactive
az aks nodepool delete --name mywasipool -g myresourcegroup --cluster-name myakscluster
```


<!-- EXTERNAL LINKS -->
[helm]: https://helm.sh/
[krustlet]: https://krustlet.dev/
[krustlet-cni-support]: https://github.com/krustlet/krustlet/issues/533
[wasm]: https://webassembly.org/
[wasi]: https://wasi.dev/
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- INTERNAL LINKS -->

[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[dockerhub-callout]: ../container-registry/buffer-gate-public-content.md
[install-azure-cli]: /cli/azure/install-azure-cli
[use-multiple-node-pools]: use-multiple-node-pools.md
[use-system-pool]: use-system-pools.md
