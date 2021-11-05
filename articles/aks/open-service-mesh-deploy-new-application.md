---
title: Verwalten einer neuen Anwendung mit Open Service Mesh
description: Wie verwalte ich eine neue Anwendung mit Open Service Mesh?
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 1dc58fa0709cdf1e09482a3f3ac3ee05788cd1eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066860"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Verwalten einer neuen Anwendung mit dem Open Service Mesh (OSM) Azure Kubernetes Service (AKS) Add-on

## <a name="before-you-begin"></a>Voraussetzungen

Die in dieser Anleitung beschriebenen Schritte gehen davon aus, dass Sie einen AKS-Cluster (Kubernetes `1.19+` und höher, mit aktiviertem Kubernetes RBAC) erstellt haben, eine `kubectl` Verbindung mit dem Cluster hergestellt haben (Wenn Sie bei einem dieser Punkte Hilfe benötigen, dann lesen Sie den [AKS-Schnellstart](./kubernetes-walkthrough.md) und haben das AKS OSM-Add-on installiert.

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- OSM, Version 0.11.1 oder höher
- JSON-Prozessor "jq" Version 1.6+

## <a name="create-namespaces-for-the-application"></a>Anlegen von Namensräumen für die Anwendung

In dieser exemplarischen Vorgehensweise wird die OSM Bookstore-Anwendung verwendet, die über die folgenden Kubernetes-Dienste verfügt:

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

Wenn Sie die Namespaces zum OSM-Netz hinzufügen, ermöglicht diese Aktion dem OSM-Controller, die Envoy-Sidecar-Proxy-Container automatisch in Ihre Anwendung zu injizieren. Führen Sie den folgenden Befehl aus, um die Namespaces der OSM-Buchhandelsanwendung zu aktivieren.

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
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="checkpoint-what-got-installed"></a>Prüfpunkt: Was wurde installiert?

Die Anwendung "Bookstore" ist ein Beispiel für eine mehrschichtige Anwendung, die sich gut zum Testen der Funktionalität des Dienstnetzes eignet. Die Anwendung besteht aus vier Diensten, nämlich `bookbuyer`, `bookthief`, `bookstore` und `bookwarehouse`. Sowohl der `bookbuyer`- als auch der `bookthief`-Dienst kommunizieren mit dem `bookstore`-Dienst, um Bücher vom `bookstore`-Dienst abzurufen. Der Dienst `bookstore` ruft Bücher aus dem Dienst `bookwarehouse` ab, um die Dienste `bookbuyer` und `bookthief` zu versorgen. Dies ist eine einfache mehrschichtige Anwendung, die gut zeigt, wie ein Dienstnetz zum Schutz und zur Autorisierung der Kommunikation zwischen den Anwendungsdiensten verwendet werden kann. Im weiteren Verlauf der Erläuterung werden wir die SMI-Richtlinien (Service Mesh Interface) aktivieren und deaktivieren, um den Diensten die Kommunikation über OSM zu erlauben oder zu verbieten. Unten sehen Sie ein Architekturdiagramm dessen, was für den Bookstore-Anwendung installiert wurde.

![Architektur der OSM-Bookbuyer-App](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Überprüfen Sie die Bookstore-Anwendung, die innerhalb des AKS-Clusters läuft

Wir haben jetzt die `bookstore` Multicontainer-Anwendung bereitgestellt, aber sie ist nur innerhalb des AKS-Clusters zugänglich. Spätere Tutorials werden Sie dabei unterstützen, die Anwendung außerhalb des Clusters über einen Eingangsdatencontroller zu exponieren. Im Moment werden wir die Portweiterleitung nutzen, um auf die Anwendung `bookbuyer` innerhalb des AKS-Clusters zuzugreifen und zu überprüfen, ob sie Bücher vom Dienst `bookstore` kauft.

Um zu überprüfen, ob die Anwendung innerhalb des Clusters läuft, werden wir eine Portweiterleitung verwenden, um die Benutzeroberfläche der Komponenten `bookbuyer` und `bookthief` anzuzeigen.

Ermitteln wir zunächst den Namen des `bookbuyer`-Pods

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Ihrem `bookbuyer`-Pod wird ein eindeutiger Name angehängt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Sobald wir den Namen des Pods haben, können wir nun den Befehl port-forward verwenden, um einen Tunnel von unserem lokalen System zur Anwendung im AKS-Cluster einzurichten. Führen Sie den folgenden Befehl aus, um die Portweiterleitung für den lokalen Systemport 8080 einzurichten. Verwenden Sie erneut den angegebenen Bookbuyer-Pod-Namen.

> [!NOTE]
> Für alle Befehle zur Portweiterleitung verwenden Sie am besten ein zusätzliches Terminal, damit Sie diese Anleitung weiter durcharbeiten können und die Verbindung zum Tunnel nicht unterbrochen wird. Am besten ist es auch, wenn Sie den Port-Forward-Tunnel außerhalb der Azure Cloud Shell einrichten.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Die Ausgabe sollte etwa folgendermaßen aussehen:

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Rufen Sie bei bestehender Portweiterleitung die folgende URL in einem Browser auf `http://localhost:8080`. Sie sollten nun die Benutzeroberfläche der `bookbuyer`-Anwendung im Browser sehen können, ähnlich wie in der folgenden Abbildung.

![OSM Bookbuyer App UI Image](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Sie werden auch feststellen, dass die Gesamtzahl der gekauften Bücher für den Dienst `bookstore` v1 weiter ansteigt. Der Dienst `bookstore` v2 wurde noch nicht eingerichtet. Wir werden den Dienst `bookstore` v2 einsetzen, wenn wir die SMI-Verkehrsteilungsrichtlinien demonstrieren.

Sie können dasselbe auch für den Dienst `bookthief` überprüfen.

```azurecli-interactive
kubectl get pod -n bookthief
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Ihrem `bookthief`-Pod wird ein eindeutiger Name angehängt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Portweiterleitung zu `bookthief` pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Rufen Sie in einem Browser die folgende URL auf `http://localhost:8080`. Sie sollten sehen, dass der `bookthief` derzeit Bücher vom `bookstore`-Dienst stiehlt! Später werden wir eine Verkehrsrichtlinie implementieren, um den `bookthief` zu stoppen.

![OSM Bookthief-App UI-Bild](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>OSM-Modus „Permissiver Datenverkehr“ für das Netz deaktivieren

Nun deaktivieren wir die Richtlinie für den Zuordnungsmodus für den Datenverkehr, und OSM benötigt explizite [SMI](https://smi-spec.io/) -Richtlinien, die für den Cluster bereitgestellt werden, um die Kommunikation im Netz von jedem Dienst zuzulassen. Um den zulässigen Verkehrsmodus zu deaktivieren, aktualisieren Sie die OSM MeshConfig-Ressourceneigenschaft mit dem folgenden Befehl und ändern Sie den Wert von `true` auf `false`.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden.

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

Um zu überprüfen, ob der Modus "Permissiver Verkehr" deaktiviert wurde, kehren Sie per Portforward zum `bookbuyer`- oder `bookthief`-Pod zurück, um die Benutzeroberfläche im Browser anzuzeigen und zu sehen, ob die Anzahl der gekauften oder gestohlenen Bücher nicht mehr erhöht wird. Stellen Sie sicher, dass Sie den Browser neu laden. Wenn das Inkrementieren beendet wurde, wurde die Richtlinie ordnungsgemäß angewendet. Du hast den `bookthief` erfolgreich daran gehindert, Bücher zu stehlen, aber weder der `bookbuyer` kann von dem `bookstore` kaufen noch der `bookstore` kann Bücher von dem `bookwarehouse` zurückholen. Als nächstes implementieren wir [SMI](https://smi-spec.io/) -Richtlinien, um nur die Dienste in dem Mesh zuzulassen, mit dem Sie kommunizieren möchten. Ausführlichere Informationen über den permissiven Verkehrsmodus finden Sie im Artikel [Permissiver Verkehrsrichtlinienmodus](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Anwenden der SMI-Richtlinien für den Datenverkehrs-Zugriff

Nachdem wir nun alle Kommunikationen im Netz deaktiviert haben, erlauben wir unserem `bookbuyer`-Dienst, mit unserem `bookstore`-Dienst für den Kauf von Büchern zu kommunizieren, und erlauben unserem `bookstore`-Dienst, mit unserem `bookwarehouse`-Dienst zu kommunizieren, um Bücher zum Verkauf abzurufen.

Stellen Sie die folgenden [SMI](https://smi-spec.io/) -Richtlinien bereit.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
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
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Sie können nun eine Portweiterleitungssitzung auf den Pods `bookbuyer` oder `bookstore` einrichten und feststellen, dass sowohl die Metrik für gekaufte als auch für verkaufte Bücher wieder ansteigt. Sie können dasselbe auch für den `bookthief`-Pod tun, um zu überprüfen, dass er immer noch nicht in der Lage ist, Bücher zu stehlen.

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Anwenden von Richtlinien zur Aufteilung des Datenverkehrs an der Service-Mesh-Schnittstelle (SMI)

Für unsere abschließende Demonstration erstellen wir eine Richtlinie zum Aufteilen des [SMI](https://smi-spec.io/)-Datenverkehrs, um die Gewichtung der Kommunikation zwischen einem Dienst und mehreren Diensten als Backend zu konfigurieren. Mit der Traffic-Split-Funktion können Sie Verbindungen zu einem Dienst schrittweise auf einen anderen übertragen, indem Sie den Traffic auf einer Skala von 0 bis 100 gewichten.

Die folgende Grafik ist ein Diagramm der zu implementierenden [SMI](https://smi-spec.io/)-Traffic-Split-Richtlinie. Wir setzen eine weitere `Bookstore` Anwendung als Version 2 ein und teilen dann den eingehenden Datenverkehr von `bookbuyer` auf, wobei wir 25 % des Datenverkehrs auf den `bookstore` v1-Dienst und 75 % auf den `bookstore` v2-Dienst verteilen.

![OSM-Bookbuyer-Traffic-Split-Diagramm](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Stellen Sie den Dienst `bookstore` v2 bereit.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
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
EOF
```

Die folgende Ausgabe wird angezeigt.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Setzen Sie nun die Richtlinie zur Aufteilung des Datenverkehrs ein, um den `bookbuyer`-Datenverkehr auf die beiden `bookstore`-Dienste v1 und v2 aufzuteilen.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Die folgende Ausgabe wird angezeigt.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Richten Sie einen Port-Forward-Tunnel zum `bookbuyer`-Pod ein und Sie sollten nun sehen, dass Bücher vom `bookstore` v2-Dienst gekauft werden. Wenn Sie die Zunahme der Käufe weiter beobachten, sollten Sie eine schnellere Zunahme der Käufe über den `bookstore` v2-Dienst feststellen.

![OSM-Bookbuyer-Benutzeroberfläche zu verkauften Büchern](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)
