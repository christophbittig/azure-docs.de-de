---
title: Installieren vorhandener Anwendungen mit Helm in AKS
description: Erfahren Sie, wie Sie das Helm-Paketerstellungstool verwenden, um Container in einem AKS-Cluster (Azure Kubernetes Service) bereitzustellen.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: c1370182d8ca13acb3d94856df784ecea34252ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354841"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installieren vorhandener Anwendungen mit Helm in Azure Kubernetes Service (AKS)

[Helm][helm] ist ein Open Source-Verpackungstool, das Ihnen dabei hilft, Kubernetes-Anwendungen zu installieren und ihren Lebenszyklus zu verwalten. Ähnlich wie Linux-Paket-Manager (z.B. *APT* und *Yum*) wird Helm zur Verwaltung von Kubernetes-Diagrammen verwendet, bei denen es sich um Pakete aus vorkonfigurierten Kubernetes-Ressourcen handelt.

Dieser Artikel veranschaulicht die Konfiguration und Verwendung von Helm in einem Kubernetes-Cluster in AKS.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Darüber hinaus wird in diesem Artikel vorausgesetzt, dass Sie über einen vorhandenen AKS-Cluster mit integrierter ACR verfügen. Weitere Informationen zum Erstellen eines AKS-Clusters mit integrierter ACR finden Sie unter [Authentifizieren per Azure Container Registry über Azure Kubernetes Service][aks-integrated-acr].

Außerdem muss die Helm CLI installiert sein, d. h. der Client, der auf Ihrem Entwicklungssystem ausgeführt wird. Sie ermöglicht es Ihnen, Anwendungen mit Helm zu starten, zu beenden und zu verwalten. Wenn Sie Azure Cloud Shell verwenden, ist die Helm-CLI bereits installiert. Installationsanweisungen für Ihre lokale Plattform finden Sie unter [Installieren von Helm][helm-install].

> [!IMPORTANT]
> Helm ist für die Ausführung auf Linux-Knoten vorgesehen. Wenn sich in Ihrem Cluster Windows Server-Knoten befinden, müssen Sie sicherstellen, dass Helm-Pods nur für die Ausführung auf Linux-Knoten geplant sind. Sie müssen auch sicherstellen, dass alle von Ihnen installierten Helm-Diagramme auch für die Ausführung auf den richtigen Knoten geplant sind. Die Befehle in diesem Artikel verwenden [node-selectors][k8s-node-selector], um sicherzustellen, dass die Pods für die richtigen Knoten geplant sind, aber nicht alle Helm-Charts können einen Knotenselektor verfügbar machen. Sie können auch andere Optionen für Ihren Cluster verwenden, z. B. [Taints][taints].

## <a name="verify-your-version-of-helm"></a>Überprüfen Ihrer Version von Helm

Überprüfen Sie mit dem Befehl `helm version`, ob Helm 3 installiert ist:

```console
helm version
```

Das folgende Beispiel zeigt, dass Helm, Version 3.0.0 installiert ist:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installieren einer Anwendung mit Helm v3

### <a name="add-helm-repositories"></a>Hinzufügen von Helm-Repositorys

Verwenden Sie den Befehl [helm repo][helm-repo-add], um das Repository *ingress-nginx* hinzuzufügen.

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Suchen von Helm-Diagrammen

Helm-Diagramme dienen zum Bereitstellen von Anwendungen in einem Kubernetes-Cluster. Verwenden Sie zum Suchen nach zuvor erstellten Helm-Charts den Befehl [helm search][helm-search]:

```console
helm search repo ingress-nginx
```

Die folgende verkürzte Beispielausgabe veranschaulicht einige der verfügbaren Helm-Diagramme:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Verwenden Sie zum Aktualisieren der Liste der Diagramme den Befehl [helm repo update][helm-repo-update].

```console
helm repo update
```

Das folgende Beispiel stellt das erfolgreiche Update eines Repositorys dar:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importieren der vom Helm-Chart verwendeten Images in Ihre ACR

In diesem Artikel wird das [Helm-Chart des NGINX-Eingangsdatencontrollers][ingress-nginx-helm-chart] verwendet, das auf drei Containerimages basiert. Verwenden Sie `az acr import`, um diese Images in Ihre ACR zu importieren.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
CONTROLLER_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v0.48.1
PATCH_REGISTRY=docker.io
PATCH_IMAGE=jettech/kube-webhook-certgen
PATCH_TAG=v1.5.1
DEFAULTBACKEND_REGISTRY=k8s.gcr.io
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> Zusätzlich zum Importieren von Containerimages in Ihre ACR können Sie auch Helm-Charts in Ihre ACR importieren. Weitere Informationen finden Sie unter [Pushen und Pullen von Helm-Charts in Azure Container Registry][acr-helm].

### <a name="run-helm-charts"></a>Ausführen von Helm-Diagrammen

Verwenden Sie zum Installieren von Diagrammen mit Helm den Befehl [helm install][helm-install-command], und geben Sie einen Releasenamen und den Namen des zu installierenden Diagramms an. Um die Installation eines Helm-Diagramms in Aktion zu sehen, installieren wir eine grundlegende nginx-Bereitstellung mithilfe eines Helm-Diagramms.

> [!TIP]
> Das folgende Beispiel erstellt einen Kubernetes-Namespace namens *ingress-basic* für die Eingangsressourcen und ist für die Funktionalität in diesem Namespace konzipiert. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an.

```console
ACR_URL=<REGISTRY_URL>

# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
     --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG
```

Die folgende verkürzte Beispielausgabe zeigt den Bereitstellungsstatus der Kubernetes-Ressource an, die vom Helm-Diagramm erstellt wurde:

```console
NAME: nginx-ingress
LAST DEPLOYED: Wed Jul 28 11:35:29 2021
NAMESPACE: ingress-basic
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller'
...
```

Verwenden Sie den Befehl `kubectl get services`, um den *EXTERNAL-IP*-Wert Ihres Dienstanbieters abzurufen.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Der folgende Befehl zeigt z. B. den Wert von *EXTERNAL-IP* für den Dienst *nginx-ingress-ingress-nginx-controller* an:

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.254.93   <EXTERNAL_IP>   80:30004/TCP,443:30348/TCP   61s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Releases auflisten

Verwenden Sie zum Anzeigen einer Liste der im Cluster installierten Releases den Befehl `helm list`.

```console
helm list --namespace ingress-basic
```

Im folgenden Beispiel wird das *my-nginx-ingress*-Release angezeigt, das im vorherigen Schritt bereitgestellt wurde:

```console
$ helm list --namespace ingress-basic
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress   ingress-basic   1               2021-07-28 11:35:29.9623734 -0500 CDT   deployed        ingress-nginx-3.34.0    0.47.0
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Zu diesen Ressourcen gehören Pods, Bereitstellungen und Dienste. Um diese Ressourcen zu bereinigen, verwenden Sie den Befehl [helm uninstall][helm-cleanup]. Geben Sie dabei den Namen Ihres Releases an, das Sie mit dem vorherigen Befehl `helm list` ermittelt haben.

```console
helm uninstall --namespace ingress-basic nginx-ingress
```

Das folgende Beispiel zeigt, dass das Release mit dem Namen *my-nginx-ingress* deinstalliert wurde:

```console
$ helm uninstall --namespace ingress-basic nginx-ingress

release "nginx-ingress" uninstalled
```

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von Kubernetes-Anwendungsbereitstellungen mit Helm finden Sie in der Helm-Dokumentation.

> [!div class="nextstepaction"]
> [Helm-Dokumentation][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
            
<!-- LINKS - internal -->
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md