---
title: Open Service Mesh mit Azure Arc-Unterstützung (Vorschauversion)
description: Die Open Service Mesh-Erweiterung (OSM) im Arc-fähigem Kubernetes-Cluster
services: azure-arc
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: ebf73d6a79048a7cd08b0995e98da229f9df46ca
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350151"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Open Service Mesh mit Azure Arc-Unterstützung (Vorschauversion)

[Öffnen des Dienst-Mesh (OSM)](https://docs.openservicemesh.io/) ist ein schlankes, erweiterbares, cloudbasiertes Dienst-Mesh, mit dem Benutzer die Standardfunktionen für die Wahrnehmbarkeit für sehr dynamische Microservice-Umgebungen einheitlich verwalten, sichern und erhalten können.

OSM führt eine Envoy-basierte Kontrollebene auf Kubernetes aus, kann mit [SMI](https://smi-spec.io/)-APIs konfiguriert werden und funktioniert, indem ein Envoy-Proxy als Sidecar-Container neben jeder Instanz Ihrer Anwendung injiziert wird. [Erfahren Sie mehr](https://docs.openservicemesh.io/#features) über die von Open Service Mesh bereitgestellten Service Mesh-Szenarien.

### <a name="support-limitations-for-arc-enabled-open-service-mesh"></a>Die Support-Einschränkungen für ein Arc-fähiges Open Service Mesh

- In einem mit Arc-verbundenen Kubernetes-Cluster kann nur eine Instanz von dem Open Service Mesh bereitgestellt werden
- Die öffentliche Vorschau ist für die Open Service Mesh Version V 0.8.4 und höher verfügbar. Die neueste Version des Releases finden Sie [hier](https://github.com/Azure/osm-azure/releases).
- Aktuell werden die folgende Kubernetes-Distributionen unterstützt
    - AKS Engine
    - Cluster API Azure
    - Google Kubernetes Engine
    - Canonical Kubernetes-Verteilung
    - Rancher Kubernetes Engine
    - OpenShift Kubernetes-Verteilung
    - Amazon Elastic Kubernetes-Dienst
- Die Azure Monitor Integration mit einem Azure Arc-fähigem Open Service Mesh ist mit [eingeschränktem Support](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md) verfügbar.


[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie alle allgemeinen Voraussetzungen für die [hier](extensions.md#prerequisites) aufgeführten Clustererweiterungen erfüllt haben.
- Verwenden Sie dafür die az k8s-Erweiterungs-CLI-Version >= V 0.4.0

## <a name="install-arc-enabled-open-service-mesh-osm-on-an-arc-enabled-kubernetes-cluster"></a>So installieren Sie ein Arc-fähiges Open Service Mesh (OSM) auf einem Arc-fähigen Kubernetes-Cluster

Bei den folgenden Schritten wird davon ausgegangen, dass Sie bereits über einen Cluster mit unterstützter Kubernetes-Verteilung verfügen, der mit Azure Arc verbunden ist.

### <a name="install-a-specific-version-of-osm"></a>So installieren Sie eine bestimmte OSM Version

Stellen Sie sicher, dass Ihre KUBECONFIG-Umgebungsvariable auf die kubeconfig des Kubernetes-Clusters verweist, in dem die OSM-Erweiterung installiert werden soll.

Legen Sie die folgenden Umgebungsvariablen fest:

```azurecli-interactive
export VERSION=0.8.4
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

Während sich das Arc-fähige Open Service Mesh in der Vorschau befindet, akzeptiert der `az k8s-extension create`-Befehl nur `pilot` für die Kennzeichnung `--release-train`. `--auto-upgrade-minor-version` ist immer auf `false` festgelegt, und eine Version muss bereitgestellt werden. Wenn Sie über ein OpenShift-Cluster verfügen, verwenden Sie die Schritte im [Abschnitt](#install-a-specific-version-of-osm-on-openshift-cluster).

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

Sie sollten eine Ausgabe ähnlich wie die unten angezeigte sehen. Es kann 3 bis 5 Minuten lang dauern, bis das tatsächliche OSM-Helm-Chart im Cluster bereitgestellt wird. Bis diese Bereitstellung erfolgt, wird die InstallState-Datei weiterhin als ausstehend bezeichnet.

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>Installieren Sie eine bestimmte OSM-Version im OpenShift-Cluster

1. Kopieren Sie den folgenden Inhalt, und speichern Sie ihn in einer JSON-Datei. Wenn Sie bereits eine Konfigurationseinstellungsdatei erstellt haben, fügen Sie bitte der vorhandenen Datei die folgende Zeile hinzu, damit Ihre früheren Änderungen erhalten bleiben.
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   Legen Sie den Dateipfad als Umgebungsvariable fest:
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. Führen Sie den Befehl `az k8s-extension create` aus, der zum Erstellen der OSM-Erweiterung verwendet wird und übergeben Sie die Einstellungsdatei mithilfe der folgenden Konfigurationseinstellungen:
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. Fügen Sie jedem Dienstkonto für die Anwendungen im Mesh die privilegierte [Sicherheitskontexteinschränkung](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html) hinzu.
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

Es kann 3 bis 5 Minuten lang dauern, bis das tatsächliche OSM-Helm-Chart im Cluster bereitgestellt wird. Bis diese Bereitstellung erfolgt, wird die InstallState-Datei weiterhin als ausstehend bezeichnet.

Stellen Sie sicher, dass die Einstellung des privilegierten Init-Containers nicht auf den Standardwert zurückgestellt wird. Dazu übergeben Sie dem „osm. OpenServiceMesh.enablePrivilegedInitContainer“ die „true“-Konfigurationseinstellung für alle nachfolgenden az k8s-Erweiterungserstellungsbefehle.

### <a name="install-arc-enabled-osm-using-arm-template"></a>Installieren Sie ein Arc-fähiges OSM mithilfe einer ARM-Vorlage

Nachdem Sie Ihren Cluster mit Azure Arc verbunden haben, erstellen Sie eine JSON-Datei im folgenden Format. Stellen Sie sicher, den Wert <cluster-name> zu aktualisieren:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "0.8.4",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

Legen Sie nun die Umgebungsvariablen fest:

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

Führen Sie abschließend den folgenden Befehl aus, um die OSM-Erweiterung über die az-CLI zu installieren:

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

Sie sollten nun die OSM-Ressourcen anzeigen und die OSM-Erweiterung in Ihrem Cluster verwenden können.

## <a name="validate-the-arc-enabled-open-service-mesh-installation"></a>Überprüfen Sie die Arc-fähige Open Service Mesh-Installation

Führen Sie den folgenden Befehl aus.

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

Sie sollten eine JSON-Ausgabe ähnlich wie die nachfolgende sehen:

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```
## <a name="osm-controller-configuration"></a>Die OSM-Controllerkonfiguration
OSM stellt eine MeshConfig-Ressource `osm-mesh-config` als Teil der Steuerungsebene im Namespace arc-osm-system bereit. Diese Ressource bietet dem Meshbesitzer/-operator die Möglichkeit, einige der Meshkonfigurationen basierend auf seinen spezifischen Anforderungen zu aktualisieren. Verwenden Sie den folgenden Befehl, um die Standardwerte anzuzeigen.

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
In der Ausgabe werden die folgenden Standardwerte angezeigt:

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
Weitere Informationen erhalten Sie in der [Config-API-Referenz](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/). Beachten Sie, dass **spec.traffic.enablePermissiveTrafficPolicyMode** auf **true** festgelegt ist. Der Modus „Permissive traffic policy“ in OSM ist ein Modus, in dem die Durchsetzung der [SMI](https://smi-spec.io/)-Verkehrsrichtlinie umgangen wird. In diesem Modus erkennt OSM automatisch Dienste, die Teil des Dienstnetzes sind, und programmiert Regeln für Datenverkehrsrichtlinien auf jedem Envoy-Proxy-Sidecar, um mit diesen Diensten kommunizieren zu können.

### <a name="making-changes-to-osm-controller-configuration"></a>Änderungen an der OSM-Controllerkonfiguration

> [!NOTE]
> Die Werte in der MeshConfig-Ressource `osm-mesh-config` werden upgradeübergreifend beibehalten.

Änderungen an `osm-mesh-config` können mithilfe des kubectl-Patchbefehls vorgenommen werden. Im folgenden Beispiel wird der „permissive traffic policy mode“ auf diese Weise in FALSE geändert.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Wenn ein falscher Wert verwendet wird, verhindert die Überprüfung der MeshConfig-CRD die Übernahme der Änderung und es erscheint eine Fehlermeldung, in der erläutert wird, warum der Wert ungültig ist. Der folgende Befehl zeigt z. B., was geschieht, wenn enableEgress auf einen nicht booleschen Wert festgelegt wird.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>Die OSM-Controllerkonfiguration (Version v0.8.4)

Zurzeit können Sie über die Konfigurationszuordnung auf die OSM-Controllerkonfiguration zugreifen und diese konfigurieren. Um die Konfigurationseinstellungen des OSM-Controllers anzuzeigen, fragen Sie die `osm-config`-Konfigurationszuordnung über `kubectl` ab, um ihre Konfigurationseinstellungen anzuzeigen.

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

Ausgabe:

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

Lesen Sie die [OSM Konfigurationszuordnungsdokumentation](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/) um jede der verfügbaren Konfigurationen zu verstehen. 

Verwenden Sie die folgende Anleitung, um Änderungen an der OSM-Konfigurationszuordnung in Version v0.8.4 vorzunehmen:

1. Kopieren und speichern Sie die Änderungen, die Sie in einer JSON-Datei vornehmen möchten. In diesem Beispiel ändern wir den permissive_traffic_policy_mode von „true“ in „false“. Jedes Mal, wenn Sie eine Änderung an `osm-config` vornehmen, müssen Sie die vollständige Liste der Änderungen (im Vergleich zum Standard `osm-config`) in einer JSON-Datei bereitstellen.
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    Legen Sie den Dateipfad als Umgebungsvariable fest:
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. Führen Sie den gleichen `az k8s-extension create`-Befehl aus, der zum Erstellen der Erweiterung verwendet wurde, aber übergeben Sie nun die Konfigurationseinstellungsdatei: 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > Um sicherzustellen, dass die ConfigMap-Änderungen nicht auf die Standardeinstellung zurückgesetzt werden, übergeben Sie dieselben Konfigurationseinstellungen an alle nachfolgenden az k8s-Erweiterungserstellungsbefehle.

## <a name="using-the-arc-enabled-open-service-mesh"></a>Verwenden des Azure Arc-fähigen Open Service Mesh

Um mit der Verwendung von den OSM-Funktionen zu beginnen, müssen Sie zunächst die Anwendungs-Namespaces in das Dienst-Mesh integrieren. Laden Sie die OSM-CLI von der [OSM-GitHub-Release-Seite](https://github.com/openservicemesh/osm/releases/) herunter. Nachdem die Namespaces dem Mesh hinzugefügt wurden, können Sie die SMI-Richtlinien konfigurieren, um die gewünschte OSM-Funktion zu erreichen.

### <a name="onboard-namespaces-to-the-service-mesh"></a>So integrieren Sie die Namespaces in das Dienst-Mesh

Fügen Sie dem Mesh Namespaces hinzu, indem Sie den folgenden Befehl ausführen:

```azurecli-interactive
osm namespace add <namespace_name>
```

Weitere Informationen zur den Integrationsdiensten finden Sie [hier](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services).

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>Konfigurieren Sie das OSM mit den SMI-Richtlinien (Service Mesh Interface)

Sie können mit einer [Demoanwendung](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications) beginnen oder Ihre Testumgebung verwenden, um die SMI-Richtlinien auszuprobieren.

> [!NOTE] 
> Stellen Sie sicher, dass die Version der Bookstore-Anwendung, die Sie ausführen, mit der Version der OSM-Erweiterung übereinstimmt, die auf Ihrem Cluster installiert ist. Wenn Sie beispielsweise die Version 0.8.4 der OSM-Erweiterung verwenden, benötigen Sie die Bookstore-Demo aus der Release V 0.8-Verzweigung des OSM-Upstream-Repository.

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>Konfigurieren Sie Ihre eigenen Jaeger-, Prometheus- und Grafana-Instanzen

Die OSM-Erweiterung installiert keine Add-Ons wie [Jaeger](https://www.jaegertracing.io/docs/getting-started/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) und [Grafana](https://grafana.com/docs/grafana/latest/installation/). Die Benutzer können das OSM stattdessen in ihre eigenen ausgeführten Instanzen dieser Tools integrieren. Informationen zur der Integration in Ihre eigenen Instanzen finden Sie in der folgenden Dokumentation:

> [!NOTE]
> Gehen Sie sehr sorgfältig vor, wenn Sie die in der OSM-GitHub-Dokumentation bereitgestellten Befehle verwenden. Achten Sie darauf, dass Sie den richtigen Namespacenamen „arc-osm-system“ verwenden, wenn Sie Änderungen an `osm-mesh-config` vornehmen.

- [BYO-Jaeger-Instanz](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own)
- [BYO-Prometheus-Instanz](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus)
- [BYO-Grafana Dashboard](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Überwachen Sie Ihre Anwendungen mit Azure Monitor und Anwendungs-Insights

Sowohl Azure Monitor als auch Azure Application Insights helfen Ihnen, die Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste zu maximieren, indem sie eine umfassende Lösung zum Sammeln, Analysieren und Verarbeiten von Telemetriedaten aus Ihren Cloud- und On-Premises-Umgebungen bereitstellen.

Das Arc-fähige Open Service Mesh wird über tiefe Integrationen in diese beiden Azure-Dienste verfügen. Sie werden eine nahtlose Azure-Erfahrung für die Anzeige und Reaktion auf die kritischen KPIs bieten, die von den OSM-Metriken bereitgestellt werden. Führen Sie die folgenden Schritte aus, damit Azure Monitor die Prometheus-Endpunkte zum Sammeln von Anwendungsmetriken erfassen kann. 

1. Stellen Sie sicher, dass „prometheus_scraping“ in der `osm-mesh-config` auf TRUE festgelegt ist.

2. Stellen Sie sicher, dass die Anwendungs-Namespaces, die überwacht werden sollen, in das Mesh integriert sind. Befolgen Sie die hier [verfügbare Anleitung](#onboard-namespaces-to-the-service-mesh).

3. Machen Sie die Prometheus-Endpunkte für die Anwendungs-Namespaces verfügbar.
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```

4. Installieren Sie die Azure Monitor-Erweiterung mithilfe der [hier](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) verfügbaren Anleitung.

5. Fügen Sie die Namespaces hinzu, die Sie in der „container-azm-ms-osmconfig“ Konfigurationszuordnung überwachen möchten. Laden Sie die Konfigurationszuordnung [hier](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml) herunter.
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

6. Führen Sie den folgenden kubectl-Befehl aus
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

Es kann bis zu 15 Minuten lang dauern, bis die Metriken in Log Analytics angezeigt werden. Sie können versuchen, die Tabelle InsightsMetrics abzufragen.

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
Weitere Informationen zur Integration in Azure Monitor finden Sie [hier](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md).

### <a name="navigating-the-osm-dashboard"></a>Navigieren Sie im OSM-Dashboard

1. Greifen Sie über diesen [Link](https://aka.ms/azmon/osmarcux) auf Ihren mit Arc-verbundenen Kubernetes-Cluster zu.
2. Wechseln Sie zu „Azure Monitor“, und navigieren Sie zur Registerkarte „Berichte“, um auf die OSM-Arbeitsmappe zuzugreifen.
3. Wählen Sie den Zeitbereich und den Namespace aus, um den Bereich Ihrer Dienste zu erweitern.

![OSM-Arbeitsmappe](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>Registerkarte „Anforderungen“

- Diese Registerkarte enthält eine Zusammenfassung aller HTTP-Anforderungen, die über die Funktion „Dienst zu Dienst“ im OSM gesendet werden.
- Sie können alle Dienste sowie alle Dienste, mit denen sie kommunizieren, anzeigen, indem Sie den Dienst im Raster auswählen.
- Sie können die Gesamtanzahl der Anforderungen, die Anforderungsfehlerrate und die P90-Latenz anzeigen.
- Sie können sich das Ziel detaillierter anzeigen lassen und Trends für den HTTP-Fehler-/Erfolgscode, die Erfolgsrate, die Pod-Ressourcenauslastung und die Latenzen in verschiedenen Perzentilen anzeigen.

#### <a name="connections-tab"></a>Registerkarte „Verbindungen“

- Diese Registerkarte enthält eine Zusammenfassung aller Verbindungen zwischen Ihren Diensten im Open Service Mesh.
- Die ausgehenden Verbindungen: Die Gesamtanzahl der Verbindungen zwischen den Quell- und Zieldiensten.
- Die ausgehenden aktiven Verbindungen: Die letzte Anzahl der aktiven Verbindungen zwischen der Quelle und dem Ziel im ausgewählten Zeitbereich.
- Die Fehler bei ausgehenden Verbindungen: Die Gesamtanzahl der fehlerhaften Verbindungen zwischen den Quell- und Zieldiensten

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>Aktualisieren Sie die OSM-Erweiterungsinstanz auf eine bestimmte Version

Während der Aktualisierungen kann es zu Ausfallzeiten der Steuerungsebene kommen. Die Datenebene ist nur während den CRD-Aktualisierungen betroffen.

### <a name="supported-upgrades"></a>Unterstützte Aktualisierungen

Die OSM-Erweiterung kann auf die nächste Nebenversion aktualisiert werden. Die Herabstufungen und Aktualisierungen von Hauptversionen werden derzeit nicht unterstützt.

### <a name="crd-upgrades"></a>CRD-Aktualisierungen

Die OSM-Erweiterung kann nicht auf eine neue Version aktualisiert werden, wenn diese Version CRD-Versionsupdates enthält. Es müssen zuerst die vorhandenen CRDs gelöscht werden. Sie können überprüfen, ob eine OSM-Aktualisierung auch CRD-Versionsupdates enthält, indem Sie im Abschnitt CRD-Updates der [OSM-Versionshinweise](https://github.com/openservicemesh/osm/releases) nachsehen.

Stellen Sie sicher, dass Sie Ihre benutzerdefinierten Ressourcen sichern, bevor Sie die CRDs löschen, damit sie nach der Aktualisierung problemlos neu erstellt werden können. Befolgen Sie anschließend die unten aufgeführten Upgradeanweisungen.

> [!NOTE] 
> Eine Aktualisierung der CRDs wirkt sich auf die Datenebene aus, da die SMI-Richtlinien zwischen dem Zeitpunkt, zu dem sie gelöscht werden, und dem Zeitpunkt, zu dem sie erneut erstellt werden, nicht vorhanden sind.

### <a name="upgrade-instructions"></a>Aktualisierungsanweisungen

1. Löschen Sie die alten CRDs und benutzerdefinierten Ressourcen (Ausführung aus dem Stammverzeichnis des [OSM-Repositorys](https://github.com/openservicemesh/osm)). Stellen Sie sicher, dass das Tag der [OSM-CRDs](https://github.com/openservicemesh/osm/tree/main/charts/osm/crds) der neuen Diagrammversion entspricht.
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. Legen Sie die neue Diagrammversion als Umgebungsvariable fest:
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. Führen Sie die az k8s-Erweiterungserstellung mit der neuen Chart-Version aus
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. Erstellen Sie benutzerdefinierte Ressourcen mit neuen CRDs

## <a name="uninstall-arc-enabled-open-service-mesh"></a>Deinstallieren Sie das Arc-fähige Open Service Mesh

Verwenden Sie den folgenden Befehl:

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

Überprüfen Sie, ob die Erweiterungsinstanz gelöscht wurde:

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

Diese Ausgabe sollte kein OSM enthalten. Wenn Sie keine anderen Erweiterungen in Ihrem Cluster installiert haben, ist dies nur ein leeres Array.

Wenn Sie den Befehl „az k8s-Erweiterung“ verwenden, um die OSM-Erweiterung zu löschen, wird der Namespace „arc-osm-system“ nicht entfernt. Das Löschen der tatsächlichen Ressourcen im Namespace (z. B. das Ändern der Webhook-Konfiguration und des OSM-Controller-Pods) dauert ca. 10 Minuten.

> [!NOTE] 
> Verwenden Sie die CLI „az k8s-Erweiterung“ um von Arc verwaltete OSM-Komponenten zu deinstallieren. Die Verwendung der OSM-CLI zur Deinstallation wird von Arc nicht unterstützt und kann zu unerwünschtem Verhalten führen.

## <a name="troubleshooting"></a>Problembehandlung

Lesen Sie den [hier verfügbaren](troubleshooting.md#arc-enabled-open-service-mesh) Leitfaden zur Problembehandlung.

## <a name="next-steps"></a>Nächste Schritte

> **Möchten Sie es selbst ausprobieren?**  
> Beginnen Sie schnell mit einem [Azure Arc-Jumpstart](https://aka.ms/arc-jumpstart-osm)-Szenario mithilfe der Cluster-API.
