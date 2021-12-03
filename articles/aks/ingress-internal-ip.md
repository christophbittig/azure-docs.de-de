---
title: Eingangscontroller im internen Netzwerk
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller für ein internes privates Netzwerk in einem Azure Kubernetes Service-Cluster (AKS) installieren und konfigurieren.
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: e33daa41f0ddae73e58d77b89350c46023010d71
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554102"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Erstellen eines Eingangscontrollers für ein internes virtuelles Netzwerk in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem AKS-Cluster (Azure Kubernetes Service) bereitstellen. Der Eingangscontroller wurde für ein internes privates virtuelles Netzwerk und eine IP-Adresse konfiguriert. Externer Zugriff ist nicht zulässig. Es werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- Erstellen eines Eingangscontrollers, der Let's Encrypt für das automatische Generieren von TLS-Zertifikaten [mit einer dynamischen öffentlichen IP-Adresse][aks-ingress-tls] oder [mit einer statischen öffentlichen IP-Adresse][aks-ingress-static-tls] verwendet

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird der NGINX-Eingangscontroller mithilfe von [Helm 3][helm] auf einer [unterstützten Version von Kubernetes][aks-supported versions] installiert. Stellen Sie sicher, dass Sie die neueste Version von Helm verwenden und auf das Helm-Repository *ingress-nginx* zugreifen können. Die in diesem Artikel beschriebenen Schritte sind mit früheren Versionen des Helm-Charts, des NGINX-Eingangscontrollers oder von Kubernetes möglicherweise nicht kompatibel. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

Für den Artikel wird außerdem mindestens Version 2.0.64 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

Darüber hinaus wird in diesem Artikel vorausgesetzt, dass Sie über einen vorhandenen AKS-Cluster mit [integrierter ACR-Instanz][aks-integrated-acr] verfügen.

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importieren der vom Helm-Diagramm verwendeten Images in Ihre ACR

Wenn Sie einen AKS-Cluster mit einem privaten Netzwerk verwenden, ist es häufig erforderlich, die Herkunft der im Cluster verwendeten Containerimages zu verwalten. Weitere Informationen finden Sie unter [Best Practices für Containerimageverwaltung und Sicherheit in Azure Kubernetes Service (AKS)][aks-container-best-practices].  Zur Unterstützung dieser Anforderung und der Vollständigkeit halber basieren die Beispiele in diesem Artikel auf dem Import der drei Containerimages, die vom [Helm-Chart des NGINX-Eingangscontrollers][ingress-nginx-helm-chart] in Ihre ACR verwendet werden.

Verwenden Sie `az acr import`, um diese Images in Ihre ACR-Instanz zu importieren.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> Zusätzlich zum Importieren von Containerimages in Ihre ACR können Sie auch Helm-Diagramme in Ihre ACR importieren. Weitere Informationen finden Sie unter [Pushen und Pullen von Helm-Charts in Azure Container Registry][acr-helm].

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Standardmäßig wird ein NGINX-Eingangscontroller mit Zuweisung einer dynamischen öffentlichen IP-Adresse erstellt. Eine verbreitete Konfigurationsanforderung stellt die Verwendung eines internen privaten Netzwerks und einer IP-Adresse dar. Dieser Ansatz ermöglicht die Beschränkung des Zugriffs auf Ihre Dienste auf interne Benutzer, sodass kein externer Zugriff möglich ist.

Erstellen Sie eine Datei mit dem Namen *internal-ingress.yaml* anhand der folgenden Beispielmanifestdatei. In diesem Beispiel wird *10.240.0.42* der Ressource *loadBalancerIP* zugewiesen. Geben Sie eine eigene interne IP-Adresse für die Verwendung für den Eingangscontroller an. Stellen Sie sicher, dass diese IP-Adresse nicht bereits in Ihrem virtuellen Netzwerk verwendet wird. Wenn Sie außerdem ein vorhandenes virtuelles Netzwerk und Subnetz verwenden, müssen Sie Ihren AKS-Cluster mit den richtigen Berechtigungen zur Verwaltung des virtuellen Netzwerks und Subnetzes konfigurieren. Weitere Informationen finden Sie unter [Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)][aks-configure-kubenet-networking] oder [Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS)][aks-configure-advanced-networking].

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Nun stellen Sie das *nginx-ingress*-Diagramm mit Helm bereit. Um die im vorherigen Schritt erstellte Manifestdatei verwenden zu können, fügen Sie den Parameter `-f internal-ingress.yaml` hinzu. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Der Eingangscontroller muss ebenfalls auf einem Linux-Knoten geplant werden. Windows Server-Knoten dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace namens *ingress-basic* für die Eingangsressourcen erstellt, und es ist beabsichtigt, in diesem Namespace zu arbeiten. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an. Wenn in Ihrem AKS-Cluster die rollenbasierte Zugriffssteuerung (RBAC) von Kubernetes nicht aktiviert ist, fügen Sie den Helm-Befehlen `--set rbac.create=false` hinzu.

> [!TIP]
> Wenn Sie die [Beibehaltung der Clientquell-IP][client-source-ip] für Anforderungen an Container in Ihrem Cluster aktivieren möchten, fügen Sie dem Helm-Installationsbefehl `--set controller.service.externalTrafficPolicy=Local` hinzu. Die Clientquell-IP wird in der Anforderungskopfzeile unter *X-Forwarded-For* gespeichert. Bei der Verwendung eines Eingangscontrollers mit aktivierter Clientquell-IP-Beibehaltung funktioniert TLS-Pass-Through nicht.

```console
# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic --create-namespace \
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
    --set controller.admissionWebhooks.patch.image.digest="" \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set defaultBackend.image.digest=""
```

Wird der Kubernetes-Lastenausgleichsdienst für den NGINX-Eingangscontroller erstellt, wird Ihre interne IP-Adresse zugewiesen. Sie rufen die öffentliche IP-Adresse mit dem Befehl `kubectl get service` ab.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Es dauert ein paar Minuten, bis die IP-Adresse dem Dienst zugewiesen ist, wie in der folgenden Beispielausgabe gezeigt wird:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Es wurden noch keine Eingangsregeln erstellt, sodass die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt wird, wenn Sie zur internen IP-Adresse navigieren. Eingangsregeln werden in den folgenden Schritten konfiguriert.

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Um den Eingangscontroller in Aktion zu sehen, führen Sie zwei Demoanwendungen im AKS-Cluster aus. In diesem Beispiel verwenden Sie `kubectl apply`, um mehrere Instanzen einer einfachen *Hallo Welt*-Anwendung auszuführen.

Erstellen Sie die Datei *aks-helloworld.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Erstellen Sie die Datei *ingress-demo.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Führen Sie die beiden Demoanwendungen mit `kubectl apply` aus:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Erstellen einer Eingangsroute

Beide Anwendungen werden jetzt in Ihrem Kubernetes-Cluster ausgeführt. Zum Weiterleiten von Datenverkehr an die einzelnen Anwendungen erstellen Sie eine Kubernetes-Eingangsressource. Die Eingangsressource konfiguriert Regeln, die den Datenverkehr an eine der beiden Anwendungen weiterleiten.

Im folgenden Beispiel wird der Datenverkehr an die Adresse `http://10.240.0.42/` an den Dienst mit dem Namen `aks-helloworld` weitergeleitet. Datenverkehr an die Adresse `http://10.240.0.42/hello-world-two` wird an den Dienst `ingress-demo` weitergeleitet.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: ingress-demo
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
```

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply -f hello-world-ingress.yaml`.

```console
kubectl apply -f hello-world-ingress.yaml
```

Die folgende Beispielausgabe zeigt, wie die Eingangsressource erstellt wird.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testen des Eingangscontrollers

Navigieren Sie mit einem Webclient zu den beiden Anwendungen, um die Routen für den Eingangscontroller zu testen. Bei Bedarf können Sie diese ausschließlich interne Funktion schnell von einem Pod im AKS-Cluster aus testen. Erstellen Sie einen Testpod, und fügen Sie an diesen eine Terminalsitzung an:

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Installieren Sie `curl` mit `apt-get` im Pod:

```console
apt-get update && apt-get install -y curl
```

Greifen Sie nun mit `curl` auf die Adresse des Kubernetes-Eingangscontrollers zu, z.B. *http://10.240.0.42* . Geben Sie Ihre eigene interne IP-Adresse, die Sie beim Bereitstellen des Eingangscontrollers im ersten Schritt dieses Artikels verwendet haben, an.

```console
curl -L http://10.240.0.42
```

Die Adresse wurde ohne zusätzlichen Pfad angegeben, sodass der Eingangscontroller standardmäßig die Route */* verwendet. Die erste Demoanwendung wird zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Fügen Sie nun den Pfad */hello-world-two* der Adresse hinzu, z.B. *http://10.240.0.42/hello-world-two* . Die zweite Demoanwendung wird mit dem benutzerdefinierten Titel zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Diese Ressourcen enthalten Pods, Bereitstellungen und Dienste. Sie können zur Bereinigung der Ressourcen entweder den gesamten Beispielnamespace oder die einzelnen Ressourcen löschen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Löschen des Beispielnamespace und aller Ressourcen

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Löschen einzelner Ressourcen

Mehr Kontrolle bietet eine andere Vorgehensweise, bei der Sie einzelne Ressourcen löschen. Listen Sie mit dem Befehl `helm list` die Helm-Releases auf. 

```console
helm list --namespace ingress-basic
```

Suchen Sie nach Diagrammen mit den Namen *nginx-ingress* und *aks-helloworld*, wie in der folgenden Beispielausgabe gezeigt:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Deinstallieren Sie die Versionen mit dem Befehl `helm uninstall`.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Im folgenden Beispiel wird die NGINX-Eingangsbereitstellung deinstalliert.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Entfernen Sie als nächstes die beiden Beispielanwendungen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Entfernen Sie die Eingangsroute, die Datenverkehr an die Beispiel-Apps weitergeleitet hat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Abschließend können Sie den Namespace selbst löschen. Verwenden Sie dazu den `kubectl delete`-Befehl mit dem Namespacenamen:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden einige externe Komponenten in AKS berücksichtigt. Weitere Informationen zu diesen Komponenten finden Sie auf den folgenden Projektseiten:

- [Helm-Befehlszeilenschnittstelle][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers mit einer dynamischen öffentlichen IP-Adresse und Konfigurieren von Let's Encrypt für das automatische Generieren von TLS-Zertifikaten][aks-ingress-tls]
- [Erstellen eines Eingangscontrollers mit einer statischen öffentlichen IP-Adresse und Konfigurieren von Let's Encrypt für das automatische Generieren von TLS-Zertifikaten][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-supported versions]: supported-kubernetes-versions.md
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-container-best-practices]: operator-best-practices-container-image-management.md
