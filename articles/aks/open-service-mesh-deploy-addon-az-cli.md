---
title: Bereitstellen von Open Service Mesh
description: Bereitstellen von Open Service Mesh in Azure Kubernetes Service (AKS) mittels Azure CLI
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 93ff4f0d8565f439bc16e887b0dd31e8f14249e9
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025741"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-using-azure-cli"></a>Bereitstellen des Open Service Mesh AKS-Add-Ons mittels Azure CLI

In diesem Artikel wird erläutert, wie Sie das OSM-Add-On in AKS bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure CLI, Version 2.20.0 oder höher
- OSM, Version 0.11.1 oder höher

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installieren von Open Service Mesh (OSM) Azure Kubernetes Service (AKS) Add-on für einen neuen AKS-Cluster

Für ein neues AKS-Cluster-Bereitstellungsszenario beginnen Sie mit einer brandneuen Bereitstellung eines AKS-Clusters und aktivieren das OSM-Add-on beim Erstellen des Clusters.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Das folgende Beispiel wird verwendet, um eine Ressourcengruppe an einem angegebenen Standort (Region) zu erstellen:

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Bereitstellen eines AKS-Clusters mit aktiviertem OSM-Add-on

Sie werden nun einen neuen AKS-Cluster mit aktiviertem OSM-Add-on bereitstellen.

> [!NOTE]
> Bitte beachten Sie, dass der folgende AKS-Bereitstellungsbefehl ephemere Festplatten des Betriebssystems verwendet. Weitere Informationen zu [Kurzlebige Betriebssystemdatenträger für AKS](./cluster-configuration.md#ephemeral-os)finden Sie unter

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS-Cluster-Zugangsberechtigungen abrufen

Holen Sie sich die Zugangsdaten für den neuen Managed Kubernetes-Cluster.

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Aktivieren von Open Service Mesh (OSM) Azure Kubernetes Service (AKS) Add-on für einen bestehenden AKS-Cluster

Für ein bestehendes AKS-Cluster-Szenario aktivieren Sie das OSM-Add-on für einen bestehenden AKS-Cluster, der bereits eingesetzt wurde.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Aktivieren des OSM-Add-on für einen vorhandenen AKS-Cluster

Um das AKS-OSM-Add-on zu aktivieren, müssen Sie den `az aks enable-addons --addons` Befehl ausführen, der den Parameter übergibt. `open-service-mesh`

> [!NOTE]
> Damit die OSM-Add-On-Bereitstellung erfolgreich ist, sollte nur eine OSM-Meshinstanz in Ihrem Cluster bereitgestellt werden. Wenn Sie über andere OSM-Meshinstanzen in Ihrem Cluster verfügen, deinstallieren Sie sie, bevor Sie den Befehl `enable-addons` ausführen.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

Es sollte eine Ausgabe ähnlich der unten gezeigten Ausgabe angezeigt werden, um zu bestätigen, dass das AKS-OSM-Add-on installiert wurde.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Überprüfen der AKS-OSM-Add-on-Installation

Es gibt mehrere Befehle, die ausgeführt werden müssen, um zu überprüfen, ob alle Komponenten des AKS-OSM-Add-Ins aktiviert sind und ausgeführt werden:

Zuerst können wir die Add-on-Profile des Clusters-Abfragen, um den aktivierten Status der installierten Add-ons zu überprüfen. Der folgende Befehl sollte „true“ zurückgeben.

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Die folgenden `kubectl` Befehle melden den Status des OSM-Controllers.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

### <a name="check-osm-add-on-version"></a>Überprüfen der OSM-Add-On-Version

Die installierte OSM-Add-On-Version sollte v0.11.1 oder höher sein. Um dies zu überprüfen, können Sie den folgenden Befehl ausführen, um die Imageversion für den osm-controller zu überprüfen, der im Imagetag codiert ist: 

```azurecli-interactive
kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Zugreifen auf die Konfiguration des AKS-OSM-Add-Ons

Zurzeit können Sie über die OSM-MeshConfig-Ressource auf die OSM-Controllerkonfiguration zugreifen und diese konfigurieren. Um die Konfigurationseinstellungen des OSM-Controllers über die Befehlszeilenschnittstelle anzuzeigen, verwenden Sie den Get-Befehl **kubectl**, wie unten dargestellt.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

Die Ausgabe der MeshConfig sollte in etwa wie folgt aussehen:

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

> [!WARNING]
> Bevor Sie fortfahren, vergewissern Sie sich bitte, dass der Modus „Permissive Traffic Policy“ auf **true** eingestellt ist; falls nicht, ändern Sie ihn bitte mit dem folgenden Befehl auf

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Deaktivieren Sie das OSM-Add-on für Ihren AKS-Cluster

Führen Sie den folgenden Befehl aus, um das OSM-Add-On zu deaktivieren:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```
Nachdem das OSM-Add-On deaktiviert wurde, verbleiben die folgenden Ressourcen im Cluster:
1. Benutzerdefinierte OSM-MeshConfig-Ressource
2. Geheimnisse der OSM-Steuerungsebene
3. OSM-Konfiguration zum Ändern des Webhooks
4. OSM-Konfiguration zum Überprüfen des Webhooks
5. OSM-CRDs

> [!IMPORTANT]
> Sie müssen diese zusätzlichen Ressourcen entfernen, nachdem Sie das OSM-Add-On deaktiviert haben. Wenn Sie diese Ressourcen in Ihrem Cluster belassen, kann es zu Problemen kommen, wenn Sie das OSM-Add-On in Zukunft erneut aktivieren.

So entfernen Sie die verbleibenden Ressourcen:

1. Löschen der MeshConfig-Konfigurationsressource
```azurecli-interactive
kubectl delete --ignore-not-found meshconfig -n kube-system osm-mesh-config
```

2. Löschen der Geheimnisse der OSM-Steuerungsebene
```azurecli-interactive
kubectl delete --ignore-not-found secret -n kube-system osm-ca-bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
```

3. Löschen der OSM-Konfiguration zum Ändern des Webhooks
```azurecli-interactive
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-injector --ignore-not-found
```

4. Löschen der OSM-Konfiguration zum Überprüfen des Webhooks
```azurecli-interactive
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-controller --ignore-not-found
```

5. Löschen der OSM-CRDs: Anleitungen zu OSM-CRDs und deren Löschung finden Sie in [dieser Dokumentation](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/uninstall/#removal-of-osm-cluster-wide-resources).

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
