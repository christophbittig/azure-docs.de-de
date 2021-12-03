---
title: Bereitstellung von Open Service Mesh Azure Kubernetes Service (AKS) Add-On mit Bicep
description: Bereitstellung von Open Service Mesh auf Azure Kubernetes Service (AKS) mithilfe von Bicep
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b0452588b8eb9eebbf8ab592ad2d0a8a0e9a6b92
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066784"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>Bereitstellung des Add-On Open Service Mesh (OSM) für Azure Kubernetes Service (AKS) mithilfe von Bicep

In diesem Artikel wird die Bereitstellung des OSM-Add-On für AKS unter Verwendung einer [Bicep](../azure-resource-manager/bicep/index.yml)-Vorlage beschrieben.

[Bicep](/azure/azure-resource-manager/bicep/overview) ist eine domänenspezifische Sprache (DSL), die eine deklarative Syntax zur Bereitstellung von Azure-Ressourcen verwendet. Bicep kann anstelle der Erstellung von Azure [ARM](/azure/azure-resource-manager/templates/overview)-Vorlagen für die Bereitstellung Ihrer Infrastruktur-als-Code Azure-Ressourcen verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Azure CLI, Version 2.20.0 oder höher
- OSM-Version v0.11.1 oder höher
- Ein öffentlicher SSH-Schlüssel, der für die Bereitstellung von AKS verwendet wird
- [Visual Studio Code](https://code.visualstudio.com/) unter Verwendung eines Bash-Terminals
- Visual Studio Code [Bicep-Erweiterung](../azure-resource-manager/bicep/install.md)

## <a name="install-the-aks-preview-extension"></a>Installieren der Erweiterung aks-preview

Sie benötigen mindestens Version 0.5.24 der Azure CLI-Erweiterung _aks-preview_. Installieren Sie die Erweiterung _aks-preview_ der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrieren der Previewfunktion `AKS-OpenServiceMesh`

Um einen AKS-Cluster zu erstellen, der das Open Service Mesh-Add-on verwenden kann, müssen Sie das `AKS-OpenServiceMesh` Feature-Flag in Ihrem Abonnement aktivieren.

Registrieren Sie das Featureflag `AKS-OpenServiceMesh` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Es dauert einige Minuten, bis der Status _Registered (Registriert)_ angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des _Microsoft.ContainerService_-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>Installieren Sie das OSM AKS Add-On für einen neuen AKS-Cluster mit Bicep

Für ein neues AKS-Cluster-Bereitstellungsszenario beginnen Sie mit einer brandneuen Bereitstellung eines AKS-Clusters mit aktiviertem OSM-Add-On beim Erstellen des Clusters. Die folgenden Anweisungen verwenden eine generische Bicep-Vorlage, die einen AKS-Cluster mit ephemeren Festplatten bereitstellt, das [`kubenet`](./configure-kubenet.md) CNI verwendet und das AKS OSM-Add-On aktiviert. Für fortgeschrittenere Bereitstellungsszenarien besuchen Sie die [Bicep](../azure-resource-manager/bicep/overview.md) -Dokumentation.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In Azure können Sie zusammengehörige Ressourcen mithilfe einer Ressourcengruppe verknüpfen. Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe an einem bestimmten Azure-Ort (Region) erstellt:

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>Erstellen Sie die Haupt- und Parameter-Bicep-Dateien

Erstellen Sie ein Verzeichnis zum Speichern der erforderlichen Bicep-Bereitstellungsdateien mit Visual Studio Code und einem geöffneten Bash-Terminal. Das folgende Beispiel erstellt ein Verzeichnis mit dem Namen `bicep-osm-aks-addon` und wechselt in das Verzeichnis

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

Als nächstes erstellen Sie die Haupt- und die Parameterdatei, wie im folgenden Beispiel gezeigt wird.

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

Öffnen Sie die Datei `osm.aks.bicep` und kopieren Sie den Inhalt des folgenden Beispiels in die Datei und speichern Sie dann die Datei.

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

Öffnen Sie die Datei`osm.aks.parameters.json` und kopieren Sie den Inhalt des folgenden Beispiels in die Datei. Fügen Sie die bereitstellungsspezifischen Parameter hinzu und speichern Sie dann die Datei.

> [!NOTE]
> Die Datei `osm.aks.parameters.json` ist eine Beispiel-Parameterdatei, die für die Bereitstellung von Bicep benötigt wird. Sie müssen die angegebenen Parameter speziell für Ihre Bereitstellungsumgebung aktualisieren. Für die in diesem Beispiel verwendeten spezifischen Parameterwerte müssen die folgenden Parameter aktualisiert werden. Sie sind _clusterName_, _clusterDNSPrefix_, _k8Version_, und _sshPubKey_. Um eine Liste der unterstützten Kubernetes-Versionen in Ihrer Region zu finden, verwenden Sie bitte den Befehl `az aks get-versions --location <region>`.

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>Bereitstellen der Bicep-Datei

Um die zuvor erstellten Bicep-Dateien bereitzustellen, öffnen Sie das Terminal und authentifizieren Sie sich mit dem Befehl `az login` bei Ihrem Azure-Konto für die Azure-CLI. Nachdem Sie sich bei Ihrem Azure-Abonnement authentifiziert haben, führen Sie die folgenden Befehle für die Bereitstellung aus.

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

Nach Abschluss der Bereitstellung sollte eine Meldung mit dem Hinweis angezeigt werden, dass die Bereitstellung erfolgreich war.

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

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Zugreifen auf die Konfiguration des AKS-OSM-Add-Ons

Derzeit können Sie auf die OSM-Controller-Konfiguration über die OSM MeshConfig-Ressource zugreifen und diese konfigurieren, und Sie können die OSM-Controller-Konfigurationseinstellungen über die CLI mit dem Get-Befehl **kubectl** anzeigen, wie es wie unten gezeigt ist.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

Die Ausgabe aus dem MeshConfig wird im Folgenden gezeigt:

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

Beachten Sie, dass **enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist. Der Modus „Permissive traffic policy“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus entdeckt OSM automatisch die Dienste, die Teil des Dienstnetzes sind. Die entdeckten Dienste verfügen über Datenverkehrsrichtlinien, die auf jedem Envoy-Proxy-Sidecar programmiert sind, um die Kommunikation zwischen diesen Diensten zu ermöglichen.

> [!WARNING]
> Bevor Sie fortfahren, vergewissern Sie sich bitte, dass der Modus „Permissive Traffic Policy“ auf **true** eingestellt ist; falls nicht, ändern Sie ihn bitte mit dem folgenden Befehl auf

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Azure-Ressourcen nicht mehr benötigt werden, verwenden Sie die Azure-CLI, um die Ressourcengruppe für das Testen der Bereitstellung zu löschen.

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
