---
title: Verwenden des NGINX-Eingangs
description: Verwenden des NGINX-Eingangs mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ee313000b5b2fd21e7f629c57f45bbe0d3557835
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066822"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Bereitstellen einer von Open Service Mesh (OSM) verwalteten Anwendung mit NGINX-Eingang

Open Service Mesh (OSM) ist ein schlankes, erweiterbares, cloudbasiertes Dienst-Mesh, mit dem Benutzer die Standardfunktionen für die Wahrnehmbarkeit für sehr dynamische Microserviceumgebungen einheitlich verwalten, sichern und erhalten können.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Anzeigen der aktuellen OSM-Cluster-Konfiguration
> - Erstellen der Namespaces für OSM zum Verwalten bereitgestellter Anwendungen in den Namespaces
> - Einbindung der von OSM zu verwaltenden Namespaces
> - Bereitstellen der Beispielanwendung
> - Überprüfen Sie die Anwendung, die innerhalb des AKS-Clusters läuft
> - Erstellen Sie einen NGINX-Ingress-Controller, der für die Anwendung verwendet wird
> - Verfügbar machen eines Diensts über das Azure-Anwendung Gateway Ingress über das Internet

## <a name="before-you-begin"></a>Voraussetzungen

Die in dieser Artikel beschriebenen Schritte gehen davon aus, dass Sie einen AKS-Cluster (Kubernetes `1.19+` und höher, mit aktiviertem Kubernetes RBAC) erstellt haben, eine `kubectl` Verbindung mit dem Cluster hergestellt haben (Wenn Sie bei einem dieser Punkte Hilfe benötigen, dann lesen Sie den [AKS-Schnellstart](./kubernetes-walkthrough.md) und haben das AKS OSM-Add-on installiert.

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- OSM, Version 0.11.1 oder höher
- JSON-Prozessor „jq“ ab Version 1.6

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Anzeigen und Überprüfen der aktuellen OSM-Cluster Konfiguration

Nachdem das OSM-Add-On für AKS im AKS-Cluster aktiviert wurde, können Sie die aktuellen Konfigurationsparameter in der Ressource „osm-mesh-config“ anzeigen. Führen Sie den folgenden Befehl aus, um die Eigenschaften anzuzeigen:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

Ausgabe zeigt die aktuelle OSM-Konfiguration für den Cluster an.

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

Beachten Sie, dass **enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist. Der Modus „Permissive traffic policy“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus erkennt OSM automatisch Dienste, die Teil des Dienstnetzes sind, und programmiert Regeln für Datenverkehrsrichtlinien auf jedem Envoy-Proxy-Sidecar, um mit diesen Diensten kommunizieren zu können. Ausführlichere Informationen zum toleranten Verkehrsmodus finden Sie im Artikel [Toleranter Verkehrsrichtlinienmodus](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="create-namespaces-for-the-application"></a>Anlegen von Namensräumen für die Anwendung

In diesem Tutorial verwenden Sie die OSM-Anwendung `bookstore`, die aus den folgenden Anwendungskomponenten besteht:

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

Legen Sie für jede dieser Anwendungskomponenten Namensräume an.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Die folgende Ausgabe wird angezeigt.

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Einbindung der von OSM zu verwaltenden Namespaces

Das Hinzufügen der Namespaces zum OSM-Mesh ermöglicht es dem OSM-Controller, die Envoy-Sidecar-Proxy-Container automatisch mit Ihrer Anwendung zu injizieren. Führen Sie den folgenden Befehl aus, um die Namespaces der OSM-Anwendung `bookstore` zu aktivieren.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Die folgende Ausgabe wird angezeigt.

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Bereitstellen der Bookstore-Anwendung auf dem AKS-Cluster

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Nachfolgend sind alle Bereitstellungen-Ausgänge zusammengefasst.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="update-the-bookbuyer-service"></a>Aktualisieren des Bookbuyer-Diensts

Aktualisieren Sie den `bookbuyer`-Dienst mit dem folgenden Dienstmanifest auf die richtige Konfiguration des Eingangsports:

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Überprüfen Sie die Bookstore-Anwendung, die innerhalb des AKS-Clusters läuft

Ab jetzt haben Sie die `bookstore`-Anwendung mit mehreren Containern bereitgestellt, aber sie ist nur innerhalb des AKS-Clusters zugänglich. Später werden wir den Azure Application Gateway Ingress Controller hinzufügen, um die Anwendung außerhalb des AKS-Clusters zu exponieren. Um zu überprüfen, ob die Anwendung innerhalb des Clusters ausgeführt wird, verwenden Sie eine Portweiterleitung, um die Benutzeroberfläche der `bookbuyer`-Komponenten anzuzeigen.

Ermitteln Sie zunächst den Namen des `bookbuyer`-Pods.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Ihrem `bookbuyer`-Pod wird ein eindeutiger Name angehängt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Sobald wir den Namen des Pods haben, können wir nun den Befehl port-forward verwenden, um einen Tunnel von unserem lokalen System zur Anwendung im AKS-Cluster einzurichten. Führen Sie den folgenden Befehl aus, um die Portweiterleitung für den lokalen Systemport 8080 einzurichten. Verwenden Sie erneut den angegebenen Bookbuyer-Pod-Namen.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Eine ähnliche Ausgabe wie die folgende sollte angezeigt werden:

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Rufen Sie bei bestehender Portweiterleitung die folgende URL in einem Browser auf `http://localhost:8080`. Sie sollten nun in der Lage sein, die Benutzeroberfläche der `bookbuyer`-Anwendung im Browser zu sehen, ähnlich wie in der folgenden Abbildung:

![OSM-Buchkäufer-App für NGINX UI-Image](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Erstellen eines Ingress-Controllers in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Wir werden den Ingress-Controller verwenden, um die von OSM verwaltete Anwendung dem Internet auszusetzen. Verwenden Sie zum Erstellen des Eingangscontrollers Helm, um nginx-ingress zu installieren. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

Der Eingangscontroller wird auf einem Linux-Knoten geplant. Windows Server-Knoten dürfen nicht auf dem Eingangscontroller ausgeführt werden. Ein Knotenselektor wird mit dem Parameter `--set nodeSelector` angegeben, um den Kubernetes-Scheduler anzuweisen, den NGINX-Eingangscontroller auf einem Linux-basierten Knoten auszuführen.

> [!TIP]
> Im folgenden Beispiel wird der Kubernetes-Namespace _ingress-basic_ für die Eingangsressourcen erstellt. Geben Sie ggf. einen Namespace für Ihre eigene Umgebung an.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Ein Kubernetes-Lastenausgleichsdienst wird für den NGINX-Eingangscontroller erstellt. Eine dynamische öffentliche IP-Adresse wird ähnlich wie in der folgenden Beispielausgabe erstellt:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Es wurden noch keine Eingangsregeln erstellt. Wenn Sie zu der öffentlichen IP-Adresse navigieren, wird derzeit die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt. Eingangsregeln werden in den folgenden Schritten konfiguriert.

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Den Buchkäufer-Dienst für das Internet freigeben

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Die folgende Ausgabe wird angezeigt.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>Anzeigen der NGINX-Protokolle

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Ausgabe zeigt den Status des NGINX-Ingress-Controllers an, wenn die Eingangsregel erfolgreich angewendet wurde:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Extern Anzeigen der NGINX-Dienste und des Buchkäufer-Diensts

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Da der Host-Name im Ingress-Manifest ein Pseudo-Name ist, der zum Testen verwendet wird, ist der DNS-Name nicht im Internet verfügbar. Alternativ können wir das curl-Programm verwenden und über den Hostname-Header der öffentlichen NGINX-IP-Adresse verfügen und einen 200-Code empfangen, der uns erfolgreich mit dem Buchkäufer-Dienst verbindet.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Die folgende Ausgabe wird angezeigt.

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```
