---
title: Problembehandlung bei Open Service Mesh
description: Behandeln von Problemen mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b73c46896c142b76e644eed9815ae4d5e76b6f85
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227263"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) Add-on AKS Anleitungen zur Fehlerbehebung

Wenn Sie das AKS-Add-On für OSM bereitstellen, treten eventuell Probleme im Zusammenhang mit der Konfiguration des Dienstnetzes auf. Der folgende Leitfaden hilft Ihnen beim Beheben von Fehlern und allgemeinen Problemen.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>Überprüfen und Problembehandlung von OSM-Komponenten

### <a name="check-osm-controller-deployment"></a>OSM-Controller Bereitstellung überprüfen

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Ein fehlerfreier OSM-Controller würde wie folgt aussehen:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>Überprüfen des OSM-Controller-Pods

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Ein fehlerfreier OSM-Pod würde wie folgt aussehen:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Obwohl ein Controller zu einem bestimmten Zeitpunkt entfernt wurde, haben wir einen anderen Controller, der bereit ist 1/1 und mit 0 Neustarts. Wenn die Spalte BEREIT einen anderen Wert als 1/1 hat, befindet sich das Dienstnetz in einem defekten Zustand.
Die Spalte BEREIT mit 0/1 zeigt an, dass der Steuerebenen-Container abstürzt - wir müssen uns Protokolle besorgen. Siehe Abschnitt OSM-Controller-Protokolle vom Azure Support Center abrufen unten. Die Spalte BEREIT mit einer Zahl größer als 1 nach dem / würde anzeigen, dass Seitenwagen installiert sind. OSM Controller würde höchstwahrscheinlich nicht mit angeschlossenen Seitenwagen funktionieren.

### <a name="check-osm-controller-service"></a>OSM-Controller-Dienst prüfen

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Ein gesunder OSM-Controller-Dienst würde folgendermaßen aussehen:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> Die CLUSTER-IP wäre eine andere. Der Dienst NAME und PORT(S) müssen mit dem obigen Beispiel übereinstimmen.

### <a name="check-osm-controller-endpoints"></a>OSM-Controller-Endpunkte überprüfen

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Ein fehlerfreier OSM-Controller-Endpunkt bzw. gesunde OSM-Controller-Endpunkte würden wie folgt aussehen:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>OSM-Injektor-Bereitstellung prüfen

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Eine fehlerfreier OSM-Injektor-Bereitstellung würde wie folgt aussehen:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>OSM-Injektor-Pod prüfen

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Ein fehlerfreier OSM-Pod würde wie folgt aussehen:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>OSM-Injektordienst überprüfen

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Ein fehlerfreier OSM-Injektordienst würde wie folgt aussehen:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>OSM-Endpunkte überprüfen

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Ein fehlerfreier OSM-Endpunkt würde wie folgt aussehen:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>Prüfen Validieren und Mutieren von Webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Ein fehlerfreier OSM Validating Webhook würde so aussehen:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Ein fehlerfreier OSM Mutating Webhook würde wie folgt aussehen:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Prüfen Sie auf den Dienst und das CA-Bundle des Validating Webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Eine gut konfigurierte Validating-Webhook-Konfiguration würde genau so aussehen:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Prüfen Sie auf den Dienst und das CA-Bundle des mutierenden Webhooks

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Eine gut konfigurierte Mutating-Webhook-Konfiguration würde genau so aussehen:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Prüfen Sie, ob der OSM-Controller dem validierenden (oder mutierenden) Webhook ein CA-Bundle gegeben hat

> [!NOTE]
> Ab v0.8.2 ist es wichtig zu wissen, dass AKS RP den Validating Webhook installiert, AKS Reconciler dafür sorgt, dass er existiert, aber OSM Controller derjenige ist, der das CA-Bundle befüllt.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Diese Zahl gibt die Anzahl der Bytes oder die Größe des Zertifizierungsstellen Pakets an. Wenn dieser Wert leer, 0 oder eine Zahl unter 1000 ist, deutet dies darauf hin, dass das CA-Bundle nicht korrekt bereitgestellt wird. Ohne ein korrektes CA-Bundle würde der Validierungswebhook einen Fehler verursachen und den Benutzer daran hindern, Änderungen an der ConfigMap „osm-config“ im kube-system-Namespace vorzunehmen.

Ein Beispielfehler, wenn das CA-Bündel falsch ist:

- Ein Versuch, die osm-config ConfigMap zu ändern:

  ```azurecli-interactive
  kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```

- Error:

  ```
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

Workaround für den Fall, dass die **Validierende** Webhook-Konfiguration ein schlechtes Zertifikat hat:

- Option 1: Starten Sie den OSM-Controller neu. Hierdurch wird der OSM-Controller neu gestartet. Beim Start wird das CA-Bundle der beiden Webhooks Mutating und Validating überschrieben.

  ```azurecli-interactive
  kubectl rollout restart deployment -n kube-system osm-controller
  ```

- Option 2 - Option 2. Löschen Sie den Validierungs-Webhook - durch das Entfernen des Validierungs-Webhooks werden die Mutationen der `osm-config` ConfigMap nicht mehr validiert. Jeder Patch wird durchlaufen. Der AKS Reconciler wird irgendwann sicherstellen, dass der validierende Webhook existiert und ihn neu erstellen. Der OSM-Controller muss möglicherweise neu gestartet werden, um das CA-Bundle schnell neu zu schreiben.

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
  ```

- Option 3 - Löschen und patchen: Mit dem folgenden Befehl wird der validierende Webhook gelöscht, so dass wir beliebige Werte hinzufügen können, und es wird sofort versucht, einen Patch anzuwenden. Höchstwahrscheinlich wird der AKS Reconciler nicht genug Zeit haben, um den validierenden Webhook abzugleichen und wiederherzustellen, was uns die Möglichkeit gibt, eine Änderung als letzten Ausweg anzuwenden:

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```

### <a name="check-the-osm-mesh-config-resource"></a>Überprüfen der Ressource `osm-mesh-config`

Überprüfen des Vorhandenseins einer Datei:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Überprüfen des Inhalts der OSM-Netzkonfiguration

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

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

Ressourcenwerte von `osm-mesh-config`:

| Schlüssel | Typ | Standardwert | Beispiele für Kubectl-Patchbefehle |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | Zeichenfolge | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address | Zeichenfolge | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | Zeichenfolge | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | INT | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | Zeichenfolge | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | INT | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | Zeichenfolge | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | Zeichenfolge | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | Zeichenfolge | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>Namespaces überprüfen

> [!NOTE]
> Der kube-system-Namespace wird nie an einem Service Mesh teilnehmen und wird nie mit den unten stehenden Schlüsseln/Werten beschriftet und/oder annotiert.

Wir verwenden den `osm namespace add` Befehl, um Namespaces mit einem bestimmten Service Mesh zu verknüpfen.
Wenn ein k8s-Namespace Teil des Netzes ist (oder damit er Teil des Netzes sein kann), muss Folgendes zutreffen:

Anzeigen der Anmerkungen mit

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Die folgenden Eigenschaften müssen vorhanden sein:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Anzeigen der Etiketten mit

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Die folgenden Eigenschaften müssen vorhanden sein:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Wenn ein Namespace nicht mit `"openservicemesh.io/sidecar-injection": "enabled"` annotiert oder nicht mit `"openservicemesh.io/monitored-by": "osm"`dem OSM-Injektor beschriftet ist, werden keine Envoy-Sidecars hinzugefügt.

> [!NOTE]
> Nachdem `osm namespace add` aufgerufen wurde, werden nur **neue** Pods mit dem Sidecar „Envoy“ eingefügt. Vorhandene Pods müssen neu gestartet werden mit `kubectl rollout restart deployment ...`

### <a name="verify-the-smi-crds"></a>Überprüfen Sie die SMI-CRDs:

Überprüfen Sie, ob der Cluster über die erforderlichen CRDs verfügt:

```azurecli-interactive
kubectl get crds
```

Folgendes muss auf Ihrem Computer installiert sein:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Mit diesem Befehl können Sie die Versionen der installierten CRDs abrufen:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Erwartete Ausgabe:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM Controller v 0.8.2 erfordert die folgenden Versionen:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - Nicht unterstützt
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Wenn CRDs fehlen, verwenden Sie die folgenden Befehle, um diese im Cluster zu installieren:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

### <a name="certificate-management"></a>Zertifikatverwaltung

Informationen dazu, wie OSM Zertifikate für Envoy-Proxys ausstellt und verwaltet, die auf Anwendungspods ausgeführt werden, finden Sie auf der [OpenServiceMesh-Dokumentationswebsite](https://docs.openservicemesh.io/docs/guides/certificates/).

### <a name="upgrading-envoy"></a>Upgrade von Envoy

Wenn ein neuer Pod in einem Namespace erstellt wird, der vom Add-On überwacht wird, fügt OSM einen [Envoy-Proxy-Sidecar](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) in diesen Pod ein. Informationen zum Aktualisieren der Envoy-Version finden Sie im [Upgradeleitfaden](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/upgrade/#envoy) auf der OpenServiceMesh-Dokumentationswebsite.
