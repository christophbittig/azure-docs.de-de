---
title: Verwenden des Azure Application Gateway-Eingangs
description: Verwenden des Azure Application Gateway-Eingangs mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 70eaa03f3a10e01e9e3f17963f355117890a510d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066803"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Bereitstellen einer von Open Service Mesh (OSM) verwalteten Anwendung mit dem AKS-Add-On für den Azure Application Gateway-Eingang

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Anzeigen der aktuellen OSM-Cluster-Konfiguration
> - Erstellen der Namespaces für OSM zum Verwalten bereitgestellter Anwendungen in den Namespaces
> - Einbindung der von OSM zu verwaltenden Namespaces
> - Bereitstellen der Beispielanwendung
> - Überprüfen Sie die Anwendung, die innerhalb des AKS-Clusters läuft
> - Erstellen einer Azure Application Gateway-Instanz, die als Eingangscontroller für die Anwendung verwendet werden soll
> - Verfügbar machen eines Diensts über das Azure-Anwendung Gateway Ingress über das Internet

## <a name="before-you-begin"></a>Voraussetzungen

Die in dieser Anleitung beschriebenen Schritte setzen voraus, dass Sie zuvor das OSM AKS-Add-on für Ihren AKS-Cluster aktiviert haben. Lesen Sie andernfalls den Artikel [Bereitstellen des OSM-AKS-Add-Ons](./open-service-mesh-deploy-addon-az-cli.md), bevor Sie fortfahren. Außerdem muss Ihr AKS-Cluster Version Kubernetes `1.19+` und höher sein, die Kubernetes RBAC muss aktiviert sein und eine `kubectl` Verbindung mit dem Cluster hergestellt haben (wenn Sie Hilfe zu diesen Elementen benötigen, sehen Sie sich den [AKS-Schnellstart](./kubernetes-walkthrough.md)an und haben das AKS OSM-Add-on installiert.

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- OSM, Version 0.11.1 oder höher
- JSON-Prozessor „jq“ ab Version 1.6

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Anzeigen und Überprüfen der aktuellen OSM-Cluster Konfiguration

Nachdem das OSM-Add-On für AKS im AKS-Cluster aktiviert wurde, können Sie die aktuellen Konfigurationsparameter in der Ressource „osm-mesh-config“ anzeigen. Führen Sie den folgenden Befehl aus, um die Eigenschaften anzuzeigen:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

Die Ausgabe zeigt die aktuelle OSM-Netzkonfiguration für den Cluster an.

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

Beachten Sie, dass **enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist. Der Modus „Permissive traffic policy“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus erkennt OSM automatisch Dienste, die Teil des Dienstnetzes sind, und programmiert Regeln für Datenverkehrsrichtlinien auf jedem Envoy-Proxy-Sidecar, um mit diesen Diensten kommunizieren zu können.

## <a name="create-namespaces-for-the-application"></a>Anlegen von Namensräumen für die Anwendung

In diesem Lernprogramm verwenden wir die OSM-Buchhandelsanwendung, die aus den folgenden Anwendungskomponenten besteht:

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

Wenn Sie die Namespaces zum OSM-Netz hinzufügen, ermöglicht dies dem OSM-Controller, die Envoy-Sidecar-Proxy-Container automatisch mit Ihrer Anwendung zu injizieren. Führen Sie den folgenden Befehl aus, um die Namespaces der OSM-Buchhandelsanwendung zu aktivieren.

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

## <a name="deploy-the-bookstore-application"></a>Bereitstellen der Buchladen-Anwendung

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>Aktualisieren des `Bookbuyer`-Diensts

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

## <a name="verify-the-bookstore-application"></a>Überprüfen der Buchladen-Anwendung

Ab jetzt haben wir die Buchladen-Multicontainer-Anwendung bereitgestellt, aber sie ist nur innerhalb des AKS-Clusters zugänglich. Später werden wir den Azure Application Gateway Ingress Controller hinzufügen, um die Anwendung außerhalb des AKS-Clusters zu exponieren. Um zu überprüfen, ob die Anwendung innerhalb des Clusters ausgeführt wird, verwenden Sie eine Portweiterleitung, um die Benutzeroberfläche der `bookbuyer`-Komponenten anzuzeigen.

Ermitteln Sie zunächst den Namen des `bookbuyer`-Pods.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Ihrem `bookbuyer`-Pod wird ein eindeutiger Name angehängt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Sobald wir den Namen des Pods haben, können wir nun den Befehl port-forward verwenden, um einen Tunnel von unserem lokalen System zur Anwendung im AKS-Cluster einzurichten. Führen Sie den folgenden Befehl aus, um die Portweiterleitung für den lokalen Systemport 8080 einzurichten. Verwenden Sie erneut den spezifischen Podnamen `bookbuyer`.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Sie sollten eine ähnliche Ausgabe wie diese sehen.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Rufen Sie bei bestehender Portweiterleitung die folgende URL in einem Browser auf `http://localhost:8080`. Sie sollten nun in der Lage sein, die Benutzeroberfläche der `bookbuyer`-Anwendung im Browser zu sehen, ähnlich wie in der folgenden Abbildung:

![OSM-Buchkäufer-App für App Gateway UI-Bild](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>Erstellen einer Azure Application Gateway-Instanz zum Verfügbarmachen der `bookbuyer`-Anwendung

> [!NOTE]
> In den folgenden Anweisungen wird eine neue Instanz des Azure-Anwendung Gateways erstellt, das für den Eingang verwendet werden soll. Wenn Sie ein vorhandenes Azure Application Gateway verwenden möchten, fahren Sie mit dem Abschnitt zur Aktivierung des Add-ons Application Gateway Ingress Controller fort.

### <a name="deploy-a-new-application-gateway"></a>Bereitstellen eines neuen Application Gateway

> [!NOTE]
> Wir verweisen auf die bestehende Dokumentation zur Aktivierung des Add-ons Application Gateway Ingress Controller für einen bestehenden AKS-Cluster. Es wurden einige Anpassungen an die OSM-Materialien vorgenommen. Eine ausführlichere Dokumentation zu diesem Thema finden Sie [hier](../application-gateway/tutorial-ingress-controller-add-on-existing.md).

Sie stellen nun ein neues Application Gateway bereit, um ein vorhandenes Application Gateway zu simulieren, das Sie für den Lastenausgleich des Datenverkehrs zum AKS-Cluster _myCluster_ verwenden möchten. Das Application Gateway erhält den Namen _myApplicationGateway_, Sie müssen jedoch zunächst eine Ressource mit öffentlicher IP-Adresse und dem Namen _myPublicIp_ und ein neues virtuelles Netzwerk mit dem Namen _myVnet_ und dem Adressraum 11.0.0.0/8 erstellen sowie ein Subnetz mit dem Adressraum 11.1.0.0/16 und dem Namen _mySubnet_. Stellen Sie dann das Application Gateway in _mySubnet_ mithilfe von _myPublicIp_ bereit.

Wenn Sie ein AKS-Cluster und ein Application Gateway in separaten virtuellen Netzwerken verwenden, dürfen sich die Adressräume der beiden virtuellen Netzwerke nicht überlappen. Der Standardadressraum, in dem ein AKS-Cluster bereitgestellt wird, ist 10.0.0.0/8. Daher legen Sie das Adresspräfix für das virtuelle Netzwerk mit dem Application Gateway auf 11.0.0.0/8 fest.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Das AGIC-Add-On (Application Gateway Ingress Controller, Application Gateway-Eingangscontroller) unterstützt **nur** Application Gateway v2-SKUs (Standard und WAF) und **keine** Application Gateway v1-SKUs.

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Aktivieren des AGIC-Add-Ons im vorhandenen AKS-Cluster über die Azure CLI

Wenn Sie weiter mit der Azure CLI arbeiten möchten, können Sie nun fortfahren und das AGIC-Add-On in dem von Ihnen erstellten AKS-Cluster _myCluster_ aktivieren und angeben, dass das AGIC-Add-On die vorhandene Application Gateway-Instanz _myApplicationGateway_ verwenden soll, die Sie zuvor erstellt haben.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Sie können überprüfen, ob das Azure-Anwendung Gateway-AKS-Add-on mit dem folgenden Befehl aktiviert wurde.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Mit diesem Befehl sollte die Ausgabe als angezeigt werden `true` .

### <a name="peer-the-two-virtual-networks-together"></a>Peering zwischen den beiden virtuellen Netzwerken

Da der AKS-Cluster in einem eigenen virtuellen Netzwerk und das Application Gateway in einem anderen virtuellen Netzwerk bereitgestellt wurde, müssen Sie die beiden virtuellen Netzwerke per Peering miteinander verknüpfen, damit Datenverkehr vom Application Gateway zu den Pods im Cluster fließt. Für das Peering der beiden virtuellen Netzwerke muss der Azure CLI-Befehl zweimal ausgeführt werden, um sicherzustellen, dass die Verbindung bidirektional ist. Mit dem ersten Befehl wird eine Peeringverbindung vom virtuellen Netzwerk mit dem Application Gateway zum virtuellen AKS-Netzwerk erstellt. Mit dem zweiten Befehl wird eine Peeringverbindung in umgekehrter Richtung erstellt.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Verfügbarmachen des `bookbuyer`-Diensts im Internet

Wenden Sie das folgende Eingangsmanifest auf den AKS-Cluster an, um den `bookbuyer`-Dienst über die Azure Application Gateway-Instanz im Internet verfügbar zu machen.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

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

Die folgende Ausgabe muss angezeigt werden:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Da der Host-Name im Ingress-Manifest ein Pseudo-Name ist, der zum Testen verwendet wird, ist der DNS-Name nicht im Internet verfügbar. Alternativ können Sie das curl-Programm verwenden und den Hostnamenheader an die öffentliche Azure Application Gateway-IP-Adresse übergeben, um einen 200-Code zu empfangen, der erfolgreich eine Verbindung mit dem `bookbuyer`-Dienst herstellt.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Die folgende Ausgabe muss angezeigt werden:

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
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>Problembehandlung

- [AGIC Dokumentation zur Problembehandlung](../application-gateway/ingress-controller-troubleshoot.md)
- [Weitere Tools zur Problembehandlung sind im GitHub-Repository von Agic verfügbar.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)
