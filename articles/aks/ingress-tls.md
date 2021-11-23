---
title: Erstellen eines Eingangs mit automatischem TLS
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller installieren und konfigurieren, der Let‘s Encrypt für die automatische TLS-Zertifikatsgenerierung in einem Azure Kubernetes Service-Cluster (AKS) verwendet.
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 9de0d9e0c85afe7608f59a17ce28f2ba1f50c1bd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553111"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Erstellen eines HTTPS-Eingangscontrollers in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem AKS-Cluster (Azure Kubernetes Service) bereitstellen. Das Projekt [cert-manager][cert-manager] wird verwendet, um automatisch [Let‘s Encrypt][lets-encrypt]-Zertifikate zu generieren und zu konfigurieren. Schließlich werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers, der mit Let's Encrypt automatisch TLS-Zertifikate mit einer statischen öffentlichen IP-Adresse generiert][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

In diesem Artikel wird ferner vorausgesetzt, dass Sie [eine benutzerdefinierte Domäne][custom-domain] mit einer [DNS-Zone][dns-zone] besitzen, die sich in derselben Ressourcengruppe befindet wie Ihr AKS-Cluster.

In diesem Artikel wird der NGINX-Eingangscontroller mithilfe von [Helm 3][helm] auf einer [unterstützten Version von Kubernetes][aks-supported versions] installiert. Stellen Sie sicher, dass Sie das neueste Release von Helm verwenden und auf die Helm-Repositorys `ingress-nginx` und `jetstack` zugreifen können. Die in diesem Artikel beschriebenen Schritte sind mit früheren Versionen des Helm-Charts, des NGINX-Eingangscontrollers oder von Kubernetes möglicherweise nicht kompatibel.

Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm]. Eine Upgradeanleitung finden Sie in der [Helm-Installationsdokumentation. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter „Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)“. ][helm-install]

Für den Artikel wird außerdem mindestens Version 2.0.64 der Azure-Befehlszeilenschnittstelle benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

Darüber hinaus wird in diesem Artikel vorausgesetzt, dass Sie über einen vorhandenen AKS-Cluster mit integrierter ACR verfügen. Weitere Informationen zum Erstellen eines AKS-Clusters mit integrierter ACR finden Sie unter [Authentifizieren per Azure Container Registry über Azure Kubernetes Service][aks-integrated-acr].

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importieren der vom Helm-Diagramm verwendeten Images in Ihre ACR

In diesem Artikel wird das [Helm-Chart des NGINX-Eingangsdatencontrollers][ingress-nginx-helm-chart] verwendet, das auf drei Containerimages basiert. Verwenden Sie `az acr import`, um diese Images in Ihre ACR zu importieren.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
SOURCE_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v1.0.4
PATCH_IMAGE=ingress-nginx/kube-webhook-certgen
PATCH_TAG=v1.1.1
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5
CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.5.4
CERT_MANAGER_IMAGE_CONTROLLER=jetstack/cert-manager-controller
CERT_MANAGER_IMAGE_WEBHOOK=jetstack/cert-manager-webhook
CERT_MANAGER_IMAGE_CAINJECTOR=jetstack/cert-manager-cainjector

az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $SOURCE_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG
```

> [!NOTE]
> Zusätzlich zum Importieren von Containerimages in Ihre ACR können Sie auch Helm-Diagramme in Ihre ACR importieren. Weitere Informationen finden Sie unter [Pushen und Pullen von Helm-Charts in Azure Container Registry][acr-helm].

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Verwenden Sie zum Erstellen des Eingangscontrollers den Befehl `helm`, um *nginx-ingress* zu installieren. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Der Eingangscontroller muss ebenfalls auf einem Linux-Knoten geplant werden. Windows Server-Knoten dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace namens *ingress-basic* für die Eingangsressourcen erstellt, und es ist beabsichtigt, in diesem Namespace zu arbeiten. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an.

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

Während der Installation wird eine öffentliche Azure-IP-Adresse für den Eingangscontroller erstellt. Diese öffentliche IP-Adresse ist für die Lebensdauer des Eingangscontrollers statisch. Wenn Sie den Eingangscontroller löschen, geht die Zuweisung der öffentlichen IP-Adresse verloren. Wenn Sie dann einen weiteren Eingangscontroller erstellen, wird eine neue öffentliche IP-Adresse zugewiesen. Wenn Sie die Verwendung der öffentlichen IP-Adresse beibehalten möchten, können Sie stattdessen [einen Eingangscontroller mit einer statischen öffentlichen IP-Adresse erstellen][aks-ingress-static-tls].

Sie rufen die öffentliche IP-Adresse mit dem Befehl `kubectl get service` ab. Es dauert möglicherweise einige Minuten, bis die IP-Adresse dem Dienst zugewiesen wird.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Es wurden noch keine Eingangsregeln erstellt. Wenn Sie zu der öffentlichen IP-Adresse navigieren, wird die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt.

## <a name="add-an-a-record-to-your-dns-zone"></a>Hinzufügen eines A-Eintrags zu Ihrer DNS-Zone

Fügen Sie Ihrer DNS-Zone mithilfe von [az network dns record-set a add-record][az-network-dns-record-set-a-add-record] einen *A*-Eintrag mit der externen IP-Adresse des NGINX-Diensts hinzu.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name "*" \
    --ipv4-address MY_EXTERNAL_IP
```

### <a name="configure-an-fqdn-for-the-ingress-controller"></a>Konfigurieren eines FQDN für den Eingangscontroller
Optional können Sie anstelle einer benutzerdefinierten Domäne einen vollqualifizierten Domänennamen (FQDN) für die IP-Adresse des Eingangscontrollers konfigurieren.  Ihr FQDN hat das Format `<CUSTOM LABEL>.<AZURE REGION NAME>.cloudapp.azure.com`.

Es gibt zwei Methoden für diese Konfiguration, die unten beschrieben werden.

#### <a name="method-1-set-the-dns-label-using-the-azure-cli"></a>Methode 1: Festlegen der DNS-Bezeichnung mithilfe der Azure CLI
Beachten Sie, dass dieses Beispiel für eine Bash-Shell gilt.

```bash
# Public IP address of your ingress controller
IP="MY_EXTERNAL_IP"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME

# Display the FQDN
az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
 ```

#### <a name="method-2-set-the-dns-label-using-helm-chart-settings"></a>Methode 2: Festlegen der DNS-Bezeichnung mithilfe der Helm-Diagrammeinstellungen
Sie können eine Anmerkungseinstellung mithilfe des Parameters `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` an Ihre Helm-Diagrammkonfiguration übergeben.  Dies kann entweder festgelegt werden, wenn der Eingangscontroller zum ersten Mal bereitgestellt wird, oder später konfiguriert werden.
Im folgenden Beispiel wird gezeigt, wie diese Einstellung aktualisiert wird, nachdem der Controller bereitgestellt wurde.

```
DNS_LABEL="demo-aks-ingress"
NAMESPACE="nginx-basic"

helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"=$DNS_LABEL

```

## <a name="install-cert-manager"></a>Installieren von cert-manager

Der NGINX-Eingangscontroller unterstützt TLS-Terminierung. Es gibt verschiedene Möglichkeiten zum Abrufen und Konfigurieren von Zertifikaten für HTTPS. Dieser Artikel veranschaulicht die Verwendung von [cert-manager][cert-manager], über den die automatische Zertifikatsgenerierung und Verwaltungsfunktionalität mit [Let‘s Encrypt][lets-encrypt] bereitgestellt werden.

So installieren Sie den cert-manager-Controller

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version $CERT_MANAGER_TAG \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG
```

Weitere Informationen zur cert-manager-Konfiguration finden Sie im [cert-manager-Projekt][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Erstellen eines CA-Clusterausstellers

Bevor Zertifikate ausgestellt werden können, benötigt cert-manager eine [Aussteller][cert-manager-issuer]- oder [Clusteraussteller][cert-manager-cluster-issuer]-Ressource. Die Kubernetes-Ressourcen sind funktionell identisch, aber `Issuer` arbeitet in einem einzelnen Namespace, `ClusterIssuer` hingegen in allen Namespaces. Weitere Informationen finden Sie in der [cert-manager-Dokumentation zu „Issuers“][cert-manager-issuer].

Erstellen Sie einen Clusteraussteller, wie z.B. `cluster-issuer.yaml`, mithilfe des folgenden Beispielmanifests. Aktualisieren Sie die E-Mail-Adresse mit einer gültigen Adresse aus Ihrer Organisation:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Um den Aussteller zu erstellen, verwenden Sie den Befehl `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Ein Eingangscontroller und eine Zertifikatsverwaltungslösung wurden konfiguriert. Nun führen wir zwei Demoanwendungen im AKS-Cluster-aus. In diesem Beispiel wird Helm verwendet, um mehrere Instanzen einer einfachen *Hallo Welt*-Anwendung auszuführen.

Um den Eingangscontroller in Aktion zu sehen, führen Sie zwei Demoanwendungen im AKS-Cluster aus. In diesem Beispiel verwenden Sie `kubectl apply`, um mehrere Instanzen einer einfachen *Hallo Welt*-Anwendung auszuführen.

Erstellen Sie die Datei *aks-helloworld-one.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Erstellen Sie die Datei *aks-helloworld-two.yaml*, und kopieren Sie den folgenden YAML-Beispielcode hinein:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Führen Sie die beiden Demoanwendungen mit `kubectl apply` aus:

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Erstellen einer Eingangsroute

Beide Anwendungen werden jetzt in Ihrem Kubernetes-Cluster ausgeführt. Sie sind jedoch mit einem Dienst vom Typ `ClusterIP` konfiguriert und nicht über das Internet zugänglich. Um sie öffentlich verfügbar zu machen, erstellen Sie eine Kubernetes-Eingangsressource. Die Eingangsressource konfiguriert Regeln, die den Datenverkehr an eine der beiden Anwendungen weiterleiten.

Im folgenden Beispiel wird Datenverkehr an die Adresse *hello-world-ingress.MY_CUSTOM_DOMAIN* an den Dienst *aks-helloworld-one* weitergeleitet. Datenverkehr an die Adresse *hello-world-ingress.MY_CUSTOM_DOMAIN/hello-world-two* wird an den Dienst *aks-helloworld-two* weitergeleitet. Datenverkehr an *hello-world-ingress.MY_CUSTOM_DOMAIN/static* wird für statische Ressourcen an den Dienst *aks-helloworld-one* weitergeleitet.

> [!NOTE]
> Wenn Sie einen FQDN für die IP-Adresse des Eingangscontrollers anstelle einer benutzerdefinierten Domäne konfiguriert haben, verwenden Sie den FQDN anstelle von *hello-world-ingress.MY_CUSTOM_DOMAIN*. Wenn Ihr FQDN z. B. *demo-aks-ingress.eastus.cloudapp.azure.com* lautet, ersetzen Sie *hello-world-ingress.MY_CUSTOM_DOMAIN* durch *demo-aks-ingress.eastus.cloudapp.azure.com* in `hello-world-ingress.yaml`.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml` mithilfe des unten stehenden Beispiel-YAMLs. Aktualisieren Sie *hosts* und *host* auf den DNS-Namen, den Sie in einem vorherigen Schritt erstellt haben.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-two
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port:
              number: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - path:
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld-one
            port: 
              number: 80
        path: /static(/|$)(.*)
```

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Überprüfen Sie, ob ein Zertifikatsobjekt erstellt wurde.

Als Nächstes muss eine Zertifikatressource erstellt werden. Die Zertifikatressource definiert das gewünschte X.509-Zertifikat. Weitere Informationen finden Sie unter [cert-manager-Zertifikate][cert-manager-certificates]. „Cert-manager“ hat automatisch ein Zertifikatsobjekt für Sie erstellt, indem „ingress-shim“ verwendet wurde, das seit v0.2.2 automatisch mit „Cert-manager“ bereitgestellt wird. Weitere Informationen finden Sie in der [Dokumentation zu „ingress-shim“][ingress-shim].

Verwenden Sie den Befehl `kubectl get certificate --namespace ingress-basic`, um sich zu vergewissern, dass das Zertifikat erfolgreich erstellt wurde, und überprüfen Sie, ob *READY* den Wert *True* hat, was mehrere Minuten dauern kann.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testen der Konfiguration für eingehende Daten

Öffnen Sie einen Webbrowser unter *hello-world-ingress.MY_CUSTOM_DOMAIN* für Ihren Kubernetes-Eingangscontroller. Beachten Sie, dass Sie zur Verwendung von HTTPS umgeleitet werden, dass das Zertifikat vertrauenswürdig ist, und dass die Demoanwendung im Webbrowser angezeigt wird. Fügen Sie den Pfad */hello-world-two* hinzu, und beachten Sie, dass die zweite Demoanwendung mit dem benutzerdefinierten Titel angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten, die Zertifikate und die Beispiel-Apps zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Diese Ressourcen enthalten Pods, Bereitstellungen und Dienste. Sie können zur Bereinigung der Ressourcen entweder den gesamten Beispielnamespace oder die einzelnen Ressourcen löschen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Löschen des Beispielnamespace und aller Ressourcen

Verwenden Sie den `kubectl delete`-Befehl mit dem Namespacenamen, um den gesamten Beispielnamespace zu löschen. Alle Ressourcen im Namespace werden gelöscht.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Löschen einzelner Ressourcen

Mehr Kontrolle bietet eine andere Vorgehensweise, bei der Sie einzelne Ressourcen löschen. Entfernen Sie zuerst die Clusterausstellerressourcen:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Listen Sie mit dem Befehl `helm list` die Helm-Releases auf. Suchen Sie nach Diagrammen mit den Namen *nginx* und *cert-manager*, wie in der folgenden Beispielausgabe gezeigt:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Deinstallieren Sie die Versionen mit dem Befehl `helm uninstall`. Im folgenden Beispiel werden die NGINX-Eingangs- und Zertifikat-Managerbereitstellungen deinstalliert.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Entfernen Sie als nächstes die beiden Beispielanwendungen:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Entfernen Sie die Eingangsroute, die Datenverkehr an die Beispiel-Apps weitergeleitet hat:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Abschließend können Sie den Namespace selbst löschen. Verwenden Sie dazu den `kubectl delete`-Befehl mit dem Namespacenamen:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden einige externe Komponenten in AKS berücksichtigt. Weitere Informationen zu diesen Komponenten finden Sie auf den folgenden Projektseiten:

- [Helm-Befehlszeilenschnittstelle][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]
- [cert-manager][cert-manager]

Sie können außerdem:

- [Erstellen eines einfachen Eingangscontrollers mit Konnektivität mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- [Erstellen eines Eingangscontrollers, der Ihre eigenen TLS-Zertifikate verwendet][aks-ingress-own-tls]
- [Erstellen eines Eingangscontrollers, der mit Let's Encrypt automatisch TLS-Zertifikate mit einer statischen öffentlichen IP-Adresse generiert][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.io/docs/concepts/certificate/
[ingress-shim]: https://cert-manager.io/docs/usage/ingress/
[cert-manager-cluster-issuer]: https://cert-manager.io/docs/concepts/issuer/
[cert-manager-issuer]: https://cert-manager.io/docs/concepts/issuer/
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-supported versions]: supported-kubernetes-versions.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
