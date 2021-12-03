---
title: Problembehandlung bei Open Service Mesh
description: Behandeln von Problemen mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 27a553bee765dd1369490cd9f6825cc44c48c59a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065663"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) Add-on AKS Anleitungen zur Fehlerbehebung

Wenn Sie das AKS-Add-On für OSM bereitstellen, treten eventuell Probleme im Zusammenhang mit der Konfiguration des Dienstnetzes auf. Der folgende Leitfaden hilft Ihnen beim Beheben von Fehlern und allgemeinen Problemen.

## <a name="verifying-and-troubleshooting-osm-components"></a>Überprüfen und Problembehandlung von OSM-Komponenten

### <a name="check-osm-controller-deployment-pod-and-service"></a>Überprüfen von Bereitstellung, Pod und Dienst des OSM-Controllers

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-controller
```

Ein fehlerfreier OSM-Controller würde wie folgt aussehen:

```Output
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-controller   2/2     2            2           3m4s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/osm-controller-65bd8c445c-zszp4   1/1     Running   0          2m
pod/osm-controller-65bd8c445c-xqhmk   1/1     Running   0          16s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                       AGE
service/osm-controller   ClusterIP   10.96.185.178   <none>        15128/TCP,9092/TCP,9091/TCP   3m4s
service/osm-validator    ClusterIP   10.96.11.78     <none>        9093/TCP                      3m4s
```

> [!NOTE]
> Für die osm-controller-Dienste würde die CLUSTER-IP anders lauten. Der Dienst NAME und PORT(S) müssen mit dem obigen Beispiel übereinstimmen.

### <a name="check-osm-injector-deployment-pod-and-service"></a>Überprüfen von Bereitstellung, Pod und Dienst des OSM-Injektors

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-injector
```

Ein fehlerfreier OSM-Injektor würde wie folgt aussehen:

```Output
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-injector   2/2     2            2           4m37s

NAME                                READY   STATUS    RESTARTS   AGE
pod/osm-injector-5c49bd8d7c-b6cx6   1/1     Running   0          4m21s
pod/osm-injector-5c49bd8d7c-dx587   1/1     Running   0          4m37s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/osm-injector   ClusterIP   10.96.236.108   <none>        9090/TCP   4m37s
```

### <a name="check-osm-bootstrap-deployment-pod-and-service"></a>Überprüfen von Bereitstellung, Pod und Dienst des OSM-Bootstraps

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-bootstrap
```

Ein fehlerfreies OSM-Bootstrap würde wie folgt aussehen:

```Output
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-bootstrap   1/1     1            1           5m25s

NAME                                 READY   STATUS    RESTARTS   AGE
pod/osm-bootstrap-594ffc6cb7-jc7bs   1/1     Running   0          5m25s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/osm-bootstrap   ClusterIP   10.96.250.208   <none>        9443/TCP,9095/TCP   5m25s
```

### <a name="check-validating-and-mutating-webhooks"></a>Prüfen Validieren und Mutieren von Webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Ein fehlerfreier OSM Validating Webhook würde so aussehen:

```Output
NAME              WEBHOOKS   AGE
aks-osm-validator-mesh-osm   1      81m
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

### <a name="check-the-osm-mesh-config-resource"></a>Überprüfen der Ressource `osm-mesh-config`

Überprüfen des Vorhandenseins einer Datei:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Überprüfen des Inhalts der OSM-Netzkonfiguration

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
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
      address: jaeger.kube-system.svc.cluster.local
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
| spec.traffic.enableEgress | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.0.0.0/32","1.1.1.1/24"]}}}'  --type=merge` |
| spec.traffic.inboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"inboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | Zeichenfolge | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"enableDebugServer":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.address | Zeichenfolge | `"jaeger.kube-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.kube-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.endpoint | Zeichenfolge | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.port | INT | `9411`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.observability.tracing.osmLogLevel | Zeichenfolge | `"info"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"osmLogLevel": "info"}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | Zeichenfolge | `"error"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | INT | `0` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | Zeichenfolge | `"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"envoyImage":"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | Zeichenfolge | `"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"initContainerImage":"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | Zeichenfolge | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |
| spec.featureFlags.enableWASMStats | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableWASMStats":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEgressPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEgressPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableMulticlusterMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableMulticlusterMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableSnapshotCacheMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableSnapshotCacheMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableAsyncProxyServiceMapping | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableAsyncProxyServiceMapping":"false"}}}'  --type=merge` |
| spec.featureFlags.enableIngressBackendPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableIngressBackendPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEnvoyActiveHealthChecks | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEnvoyActiveHealthChecks":"false"}}}'  --type=merge` |


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

### <a name="verify-osm-crds"></a>Überprüfen von OSM-CRDs:

Überprüfen Sie, ob der Cluster über die erforderlichen CRDs verfügt:

```azurecli-interactive
kubectl get crds
```

Folgendes muss auf Ihrem Computer installiert sein:

- egresses.policy.openservicemesh.io
- httproutegroups.specs.smi-spec.io 
- ingressbackends.policy.openservicemesh.io
- meshconfigs.config.openservicemesh.io
- multiclusterservices.config.openservicemesh.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io

Mit diesem Befehl können Sie die Versionen der installierten SMI-CRDs abrufen:

```azurecli-interactive
osm mesh list
```

Erwartete Ausgabe:

```
MESH NAME   MESH NAMESPACE   VERSION   ADDED NAMESPACES
osm         kube-system      v0.11.1

MESH NAME   MESH NAMESPACE   SMI SUPPORTED
osm         kube-system      HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha2,TrafficTarget:v1alpha3

To list the OSM controller pods for a mesh, please run the following command passing in the mesh's namespace
        kubectl get pods -n <osm-mesh-namespace> -l app=osm-controller
```

OSM Controller v0.11.1 erfordert die folgenden Versionen:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - Nicht unterstützt
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)


### <a name="certificate-management"></a>Zertifikatverwaltung

Informationen dazu, wie OSM Zertifikate für Envoy-Proxys ausstellt und verwaltet, die auf Anwendungspods ausgeführt werden, finden Sie auf der [OpenServiceMesh-Dokumentationswebsite](https://docs.openservicemesh.io/docs/guides/certificates/).

### <a name="upgrading-envoy"></a>Upgrade von Envoy

Wenn ein neuer Pod in einem Namespace erstellt wird, der vom Add-On überwacht wird, fügt OSM einen [Envoy-Proxy-Sidecar](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) in diesen Pod ein. Informationen zum Aktualisieren der Envoy-Version finden Sie im [Upgradeleitfaden](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/upgrade/#envoy) auf der OpenServiceMesh-Dokumentationswebsite.
