---
title: Verwalten einer vorhandenen Anwendung mit Open Service Mesh
description: Verwaltung einer vorhandenen Anwendung mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 570305de41d190852e5acaa178d838c21fba2347
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440687"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Verwalten einer vorhandenen Anwendung mit dem Open Service Mesh (OSM) Azure Kubernetes Service (AKS) Add-On

## <a name="before-you-begin"></a>Voraussetzungen

Die in dieser Anleitung beschriebenen Schritte setzen voraus, dass Sie zuvor das OSM AKS-Add-on für Ihren AKS-Cluster aktiviert haben. Lesen Sie andernfalls den Artikel [Bereitstellen des OSM-AKS-Add-Ons](./open-service-mesh-deploy-add-on.md), bevor Sie fortfahren. Außerdem muss Ihr AKS-Cluster Version Kubernetes `1.19+` und höher sein, die Kubernetes RBAC muss aktiviert sein und eine `kubectl` Verbindung mit dem Cluster hergestellt haben (wenn Sie Hilfe zu diesen Elementen benötigen, sehen Sie sich den [AKS-Schnellstart](./kubernetes-walkthrough.md)an und haben das AKS OSM-Add-on installiert.

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- Erweiterung `aks-preview`, Version 0.5.5 oder höher
- OSM-Version v0.8.0 oder höher
- JSON-Prozessor „jq“ ab Version 1.6

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Überprüfen Sie die OSM-Richtlinie (Open Service Mesh) für den Modus Permissiver Datenverkehr

Der Modus „Permissive Datenverkehr-Richtlinie“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus erkennt OSM automatisch Dienste, die Teil des Dienstnetzes sind, und programmiert Regeln für Datenverkehrsrichtlinien auf jedem Envoy-Proxy-Sidecar, um mit diesen Diensten kommunizieren zu können.

Führen Sie den folgenden Befehl aus, um den aktuellen Zuordnungsmodus des OSM für Ihren Cluster zu überprüfen:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

Die Ausgabe der OSM-MeshConfig sollte in etwa wie folgt aussehen:

```Output
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

Wenn **enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist, können Sie Ihre Namespaces ohne jegliche Unterbrechung der Dienst-zu-Dienst-Kommunikation sicher integrieren. Wenn **enablePermissiveTrafficPolicyMode** mit **false** konfiguriert ist, müssen Sie sicherstellen, dass die richtigen [SMI](https://smi-spec.io/)-Richtlinien für den Datenverkehrszugriff bereitgestellt werden und dass Sie über ein Dienstkonto verfügen, das jeden in Namespace bereitgestellten Dienst repräsentiert. Ausführlichere Informationen zum Modus „Permissive Traffic Policy“ finden Sie im Artikel [Permissive Traffic Policy Mode](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) (Modus „Permissive Traffic Policy“).

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Onboarding bestehender bereitgestellter Anwendungen mit Open Service Mesh (OSM) Permissive Traffic-Richtlinie konfiguriert als True

Als Erstes fügen wir den/die bereitgestellten Namespace(s) der Anwendung dem OSM zur Verwaltung hinzu. Im folgenden Beispiel wird der Namespace **bookstore** in OSM integriert.

```azurecli-interactive
osm namespace add bookstore
```

Die folgende Ausgabe wird angezeigt.

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Als nächstes sehen wir uns die aktuelle Pod-Bereitstellung in Namespace an. Führen Sie den folgenden Befehl aus, um die Pods im angegebenen Namespace anzuzeigen.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Die folgende, ähnliche Ausgabe wird angezeigt:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Beachten Sie, dass die Spalte **BEREIT** mit dem Wert **1/1** angezeigt wird, was bedeutet, dass der Anwendungs-Pod nur einen Container hat. Als nächstes müssen wir Ihre Anwendungsbereitstellungen neu starten, damit OSM den Envoy-Sidecar-Proxy-Container mit Ihrem Anwendungs-Pod injizieren kann. Lassen Sie sich eine Liste der Bereitstellungen in dem Namespace anzeigen.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Die folgende Ausgabe wird angezeigt.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Jetzt starten wir die Bereitstellung neu, um den Envoy-Sidecar-Proxy-Container mit Ihrem Anwendungs-Pod zu injizieren. Führen Sie den folgenden Befehl aus.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Die folgende Ausgabe wird angezeigt.

```Output
deployment.apps/bookbuyer restarted
```

Sehen wir uns die Pods im Namespace erneut an:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Nun sehen Sie, dass in der Spalte **BEREIT** nun **2/2** Container angezeigt werden, die für Ihren Pod bereit sind. Der zweite Container ist der Envoy-Sidecar-Proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Wir können den Pod weiter untersuchen, um den Envoy-Proxy anzuzeigen, indem wir den Befehl beschreiben ausführen, um die Konfiguration anzuzeigen.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Vergewissern Sie sich, dass Ihre Anwendung nach der Injektion des Envoy-Sidecar-Proxys noch funktionsfähig ist.

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboarding bestehender bereitgestellter Anwendungen mit Open Service Mesh (OSM) Permissive Traffic-Richtlinie konfiguriert als False

Wenn die OSM-Konfiguration für die permissive Datenverkehrsrichtlinie auf gesetzt ist, benötigt`false`OSM explizite [SMI](https://smi-spec.io/)-Datenverkehrs-Zugriffsrichtlinien, die für die Dienst-zu-Dienst-Kommunikation innerhalb Ihres Clusters eingesetzt werden. Derzeit verwendet OSM auch Kubernetes-Dienstkonten als Teil der Autorisierung der Dienst-zu-Dienst-Kommunikation. Um sicherzustellen, dass Ihre vorhandenen bereitgestellten Anwendungen kommunizieren, wenn Sie vom OSM-Mesh verwaltet werden, müssen wir überprüfen, ob ein Dienstkonto vorhanden ist, um Sie zu verwenden, die Anwendungsbereitstellung mit den Dienst Kontoinformationen aktualisieren und die Richtlinien für den [SMI](https://smi-spec.io/) -Datenverkehr anwenden.

### <a name="verify-kubernetes-service-accounts"></a>Kubernetes-Dienstkonten verifizieren

Überprüfen Sie, ob Sie über ein Kubernetes-Dienstkonto im Namespace verfügen, in dem Ihre Anwendung bereitgestellt wird.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Im Folgenden befindet sich ein Dienstkonto namens `bookbuyer` im Bookbuyer-Namespace.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Wenn Sie kein anderes Dienstkonto als das Standardkonto aufgeführt haben, müssen Sie eines für Ihre Anwendung erstellen. Verwenden Sie den folgenden Befehl als Beispiel, um ein Dienstkonto im bereitgestellten Namespace der Anwendung zu erstellen.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

### <a name="view-your-applications-current-deployment-specification"></a>Anzeigen der aktuellen Bereitstellungsspezifikation Ihrer Anwendung

Wenn Sie ein Dienstkonto aus dem vorherigen Abschnitt erstellen mussten, ist Ihre Anwendungsbereitstellung wahrscheinlich nicht mit einem bestimmten `serviceAccountName` in der Bereitstellungsspezifikation konfiguriert. Sie können die Bereitstellungsspezifikation Ihrer Anwendung mit den folgenden Befehlen anzeigen:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Eine Liste der Bereitstellungen wird in der Ausgabe aufgeführt.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Wir beschreiben nun die Bereitstellung als Überprüfung, ob ein Dienstkonto im Abschnitt Pod-Vorlage aufgeführt ist.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

In dieser speziellen Bereitstellung sehen Sie, dass ein Dienstkonto mit der im Abschnitt Pod-Vorlage aufgeführten Bereitstellung verknüpft ist. Bei dieser Bereitstellung wird das Dienstkonto „Buchkäufer“ verwendet. Wenn die Eigenschaft **Dienstkonto:** nicht angezeigt wird, ist die Bereitstellung nicht für die Verwendung eines Dienstkontos konfiguriert.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Es gibt mehrere Verfahren zum Aktualisieren Ihrer Bereitstellung, um ein Kubernetes-Dienstkonto hinzuzufügen. Lesen Sie die Dokumentation zu Kubernetes, um [eine Bereitstellung](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) Inline zu aktualisieren, oder [Konfigurieren Sie Dienstkonten für Pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Nachdem Sie Ihre Bereitstellungsspezifikation mit dem Dienstkonto aktualisiert haben, stellen Sie die Bereitstellung für den Cluster erneut bereit (kubectl apply-f your-deployment.yaml).

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Bereitstellen der erforderlichen SMI-Richtlinien

Der letzte Schritt, den autorisierten Datenverkehr im Mesh zuzulassen, besteht darin, die erforderlichen [SMI](https://smi-spec.io/)-Richtlinien für den Datenverkehr für Ihre Anwendung bereitzustellen. Der Umfang der Konfiguration, die Sie mit Richtlinien für den [SMI](https://smi-spec.io/)-Datenverkehr erreichen können, geht über den Rahmen dieser exemplarischen Vorgehensweise hinaus, aber wir erläutern einige der allgemeinen Komponenten der Spezifikation und zeigen Ihnen, wie Sie sowohl eine einfache TrafficTarget-als auch eine HTTPRouteGroup-Richtlinie konfigurieren, um die Kommunikation zwischen Diensten für Ihre Anwendung zu ermöglichen.

Mithilfe der [SMI](https://smi-spec.io/) - [**Datenverkehr-Zugriffssteuerung**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control)-Spezifikation können Benutzer die Zugriffssteuerungs-Richtlinie für Ihre Anwendungen definieren. Wir werden uns auf die api-Ressourcen **TrafficTarget** und **HTTPRoutGroup** konzentrieren.

Die Ressource TrafficTarget besteht aus drei wesentlichen Konfigurationseinstellungen Ziel, Regeln und Quellen. Ein Beispiel für ein TrafficTarget ist unten dargestellt.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

In der obigen Spezifikation TrafficTarget bezeichnet das `destination` Dienstkonto, das für den Zielquellendienst konfiguriert ist. Merken Sie sich, dass das Dienstkonto, das der Bereitstellung hinzugefügt wurde, verwendet wird, um den Zugriff auf die Bereitstellung zu autorisieren. Der `rules` Abschnitt in diesem speziellen Beispiel definiert den Typ des HTTP-Datenverkehrs, der über die Verbindung zugelassen wird. Sie können feinkörnige Regex-Muster für die HTTP-Header konfigurieren, um genau festzulegen, welcher Datenverkehr über HTTP erlaubt ist. Der `sources` Abschnitt ist der Dienst, von dem die Kommunikation ausgeht. Diese Spezifikation liest Buchkäufer für die Kommunikation mit der Buchhandlung.

Die HTTPRouteGroup-Ressource besteht aus einem oder einem Array von Übereinstimmungen von HTTP-Header Informationen und ist eine Voraussetzung für die Spezifikation-Traffictarget. Im folgenden Beispiel können Sie sehen, dass HTTPRouteGroup drei HTTP-Aktionen autorisiert: zwei GET-und ein POST-Vorgang.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Wenn Sie nicht wissen, welche Art von HTTP-Datenverkehr Ihre Front-End-Anwendung an andere Ebenen der Anwendung weiterleitet, können Sie, da die TrafficTarget-Spezifikation eine Regel erfordert, das Äquivalent einer "allow all"-Regel erstellen, indem Sie die unten stehende Spezifikation für HTTPRouteGroup verwenden.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Nachdem Sie die Spezifikationen TrafficTarget und HTTPRouteGroup konfiguriert haben, können Sie diese als eine YAML und bereitstellen. Im folgenden finden Sie die Beispielkonfiguration für die Buchhandlung.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Ausführlichere Informationen zur Spezifikation finden Sie auf der [SMI](https://smi-spec.io/)-Website.

## <a name="manage-the-applications-namespace-with-osm"></a>Verwalten des Namespace der Anwendung mit OSM

Als Nächstes konfigurieren Sie OSM, um den Namespace zu verwalten und die Bereitstellungen neu zu starten, damit der Envoy Sidecar Proxy mit der Anwendung eingefügt wird.

Führen Sie den folgenden Befehl aus, um den `azure-vote` Namespace für die Verwaltung meines OSM zu konfigurieren.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Starten Sie anschließend beide Bereitstellungen `azure-vote-front` mit `azure-vote-back` den folgenden Befehlen neu.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Wenn wir die Pods für den `azure-vote` Namespace anzeigen, wird die **fertige** Phase von `azure-vote-front` und `azure-vote-back` als 2/2 angezeigt, was bedeutet, dass der Envoy Sidecar Proxy neben der Anwendung eingefügt wurde.