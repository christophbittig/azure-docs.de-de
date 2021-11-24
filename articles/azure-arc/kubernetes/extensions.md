---
title: Azure Arc-fähige Kubernetes-Clustererweiterungen
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Bereitstellen und Verwalten des Lebenszyklus von Erweiterungen für Azure Arc-fähiges Kubernetes
ms.openlocfilehash: 811bced5b0855ffdc44d851459b69a7b6aad6b19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312819"
---
# <a name="deploy-and-manage-azure-arc-enabled-kubernetes-cluster-extensions"></a>Bereitstellen und Verwalten Azure Arc-fähiger Kubernetes-Clustererweiterungen

Kubernetes-Erweiterungen ermöglichen in Azure Arc-fähigen Kubernetes-Clustern Folgendes:

* Bereitstellung von Clustererweiterungen auf Basis von Azure Resource Manager.
* Lebenszyklusverwaltung von Erweiterungs-Helm-Diagrammen.

In diesem Artikel wird Folgendes behandelt:
> [!div class="checklist"]
> * Derzeit verfügbare Azure Arc-fähige Kubernetes-Clustererweiterungen
> * Erstellen von Erweiterungsinstanzen
> * Erforderliche und optionale Parameter
> * Anzeigen, Auflisten, Aktualisieren und Löschen von Erweiterungsinstanzen 

Einen konzeptionellen Überblick über diese Funktion finden Sie im Artikel [Cluster-Erweiterungen - Azure Arc-fähiges Kubernetes](conceptual-extensions.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Installieren oder aktualisieren Sie Azure CLI](/cli/azure/install-azure-cli) auf eine Version >= 2.16.0.
- Azure CLI-Erweiterungen `connectedk8s` (Version >= 1.1.0) und `k8s-extension` (Version >= 0.2.0). Installieren Sie diese Azure CLI-Erweiterungen, indem Sie die folgenden Befehle ausführen:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Wenn die Erweiterungen `connectedk8s` und `k8s-extension` bereits installiert sind, können Sie sie mit dem folgenden Befehl auf die neueste Version aktualisieren:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Ein vorhandener Cluster, der mit Kubernetes mit Azure Arc-Unterstützung verbunden ist
    - Wenn Sie noch keine Verbindung mit einem Cluster hergestellt haben, verwenden Sie unseren [Schnellstart](quickstart-connect-cluster.md).
    - [Aktualisieren Sie Ihre Agents](agent-upgrade.md#manually-upgrade-agents) auf Version >= 1.1.0.

## <a name="currently-available-extensions"></a>Derzeit verfügbare Erweiterungen

| Durchwahl | BESCHREIBUNG |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Bietet Einblick in die Leistung von Workloads, die im Kubernetes-Cluster bereitgestellt werden. Sammelt Metriken zur Arbeitsspeicher- und CPU-Auslastung von Controllern, Knoten und Containern. |
| [Microsoft Defender für Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Erfasst Sicherheitsinformationen wie Überwachungsprotokolldaten aus dem Kubernetes-Cluster. Bietet Empfehlungen und Bedrohungswarnungen auf Grundlage der gesammelten Daten. |
| [Open Service Mesh mit Azure Arc-Unterstützung](tutorial-arc-enabled-open-service-mesh.md) | Stellt Open Service Mesh im Cluster bereit und ermöglicht Funktionen wie mTLS-Sicherheit, fein aufgelöste Zugriffssteuerung, Datenverkehrsverschiebung, Überwachung mit Azure Monitor oder mit Open Source-Add-Ons von Prometheus und Grafana, Ablaufverfolgung mit Jaeger, Integration in externe Zertifizierungsverwaltungslösung. |
| [Azure Arc-fähige Datendienste](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) | Ermöglicht das Ausführen von Azure-Datendiensten in der lokalen Umgebung, im Edge-Bereich und in öffentlichen Clouds mithilfe von Kubernetes und der Infrastruktur Ihrer Wahl. |
| [Azure App Service in Azure Arc](../../app-service/overview-arc-integration.md) | Ermöglicht die Bereitstellung einer App Service-Kubernetes-Umgebung auf Basis Azure Arc-fähiger Kubernetes-Cluster. |
| [Event Grid in Kubernetes](../../event-grid/kubernetes/overview.md) | Erstellen und Verwalten von Event Grid-Ressourcen wie Themen und Ereignisabonnements auf Basis Azure Arc-fähiger Kubernetes-Cluster. |
| [Azure API Management in Azure Arc](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md) | Bereitstellen und Verwalten von Azure API Management-Gateways in Azure Arc-fähigen Kubernetes-Clustern. |
| [Machine Learning mit Azure Arc-Unterstützung](../../machine-learning/how-to-attach-arc-kubernetes.md) | Stellen Sie Azure Machine Learning-Webdienste in Azure Arc-fähigen Kubernetes-Clustern bereit, und führen Sie sie aus. |

## <a name="usage-of-cluster-extensions"></a>Verwendung von Clustererweiterungen

### <a name="create-extensions-instance"></a>Erstellen von Erweiterungsinstanzen

Erstellen Sie eine neue Erweiterungsinstanz mit `k8s-extension create`, und übergeben Sie Werte für die obligatorischen Parameter. Der folgende Befehl erstellt eine Erweiterungsinstanz für Azure Monitor für Container in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Ausgabe:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Der Dienst kann vertrauliche Informationen nicht für mehr als 48 Stunden aufbewahren. Wenn Azure Arc-fähige Kubernetes-Agents für mehr als 48 Stunden nicht über Netzwerkkonnektivität verfügen und nicht bestimmen können, ob eine Erweiterung auf dem Cluster erstellt werden soll, geht die Erweiterung in den Status `Failed` über. Wenn sie sich im `Failed`-Status befindet, müssen Sie `k8s-extension create` erneut ausführen, um eine neue Erweiterungs-Azure-Ressource zu erstellen.
> * Azure Monitor für Container ist eine Singleton-Erweiterung (nur eine pro Cluster erforderlich). Sie müssen alle vorherigen Helm-Diagramminstallationen von Azure Monitor für Container (ohne Erweiterungen) bereinigen, bevor Sie die gleichen über Erweiterungen installieren. Befolgen Sie die Anweisungen zum [Löschen des Helm-Diagramms vor der Ausführung von `az k8s-extension create`](../../azure-monitor/containers/container-insights-optout-hybrid.md).

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name` | Name der Erweiterungsinstanz |
| `--extension-type` | Der Typ der Erweiterung, die Sie auf dem Cluster installieren möchten. Beispiel: Microsoft.AzureMonitor.Containers, microsoft.azuredefender.kubernetes | 
| `--scope` | Umfang der Installation für die Erweiterung – `cluster` oder `namespace` |
| `--cluster-name` | Name der Azure Arc-fähigen Kubernetes-Ressource, auf der die Erweiterungsinstanz erstellt werden soll |
| `--resource-group` | Die Ressourcengruppe, in der die Azure Arc-fähige Kubernetes-Ressource enthalten ist |
| `--cluster-type` | Der Clustertyp, auf dem die Erweiterungsinstanz erstellt werden soll. Derzeit ist nur `connectedClusters` ein akzeptierter Wert, was Azure Arc-fähigem Kubernetes entspricht. |

**Optionale Parameter**

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--auto-upgrade-minor-version` | Boolesche Eigenschaft, die angibt, ob die Nebenversion der Erweiterung automatisch aktualisiert wird. Standardwert: `true`.  Wenn dieser Parameter auf „true“ festgelegt ist, können Sie den `version`-Parameter nicht festlegen, da die Version dynamisch aktualisiert wird. Wenn der Wert `false` festgelegt ist, wird die Erweiterung auch für Patchversionen nicht automatisch aktualisiert. |
| `--version` | Version der zu installierenden Erweiterung (bestimmte Version, an die die Erweiterungsinstanz angeheftet wird). Muss nicht angegeben werden, wenn „auto-upgrade-minor-version“ auf `true` festgelegt ist. |
| `--configuration-settings` | Einstellungen, die an die Erweiterung übergeben werden können, um deren Funktionalität zu steuern. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um Konfigurationseinstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings` | Diese Einstellungen können nicht mit `GET`-API-Aufrufen oder `az k8s-extension show`-Befehlen abgerufen werden, und werden daher verwendet, um sensible Einstellungen zu übergeben. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um sensible Einstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings` nicht im selben Befehl verwendet werden. |
| `--release-namespace` | Dieser Parameter gibt den Namespace an, in dem die Freigabe erstellt werden soll. Dieser Parameter ist nur relevant, wenn der `scope`-Parameter auf `cluster` festgelegt ist. |
| `--release-train` |  Erweiterungsautoren können Versionen in unterschiedlichen Release Trains wie `Stable`, `Preview` usw. veröffentlichen. Wenn dieser Parameter nicht explizit festgelegt ist, wird `Stable` als Standard verwendet. Dieser Parameter kann nicht verwendet werden, wenn der `autoUpgradeMinorVersion`-Parameter auf `false` festgelegt ist. |
| `--target-namespace` | Dieser Parameter gibt den Namespace an, in dem die Freigabe erstellt wird. Die Berechtigung des für diese Erweiterungsinstanz erstellten Systemkontos wird auf diesen Namespace beschränkt. Dieser Parameter ist nur relevant, wenn der `scope`-Parameter auf `namespace` festgelegt ist. |

### <a name="show-details-of-an-extension-instance"></a>Anzeigen von Details einer Erweiterungsinstanz

Zeigen Sie Details einer aktuell installierten Erweiterungsinstanz mit `k8s-extension show` an, wobei Sie Werte für die obligatorischen Parameter übergeben:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Ausgabe:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Alle auf dem Cluster installierten Erweiterungen auflisten

Auflisten aller auf dem Cluster installierten Erweiterungen mit `k8s-extension list`, wobei Werte für die obligatorischen Parameter übergeben werden.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Ausgabe:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>Löschen der Erweiterungsinstanz

Löschen einer Erweiterungsinstanz auf einem Cluster mit `k8s-extension delete`, wobei Werte für die obligatorischen Parameter übergeben werden.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Die Azure-Ressource, die diese Erweiterung darstellt, wird sofort gelöscht. Die Helm-Freigabe auf dem Cluster, die dieser Erweiterung zugeordnet ist, wird nur gelöscht, wenn die auf dem Kubernetes-Cluster ausgeführten Agents über Netzwerkkonnektivität verfügen und wieder auf Azure-Dienste zugreifen können, um den gewünschten Status abzurufen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die derzeit für Azure Arc-fähiges Kubernetes verfügbaren Clustererweiterungen:

> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Microsoft Defender für Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Azure Arc-fähiges Open Service Mesh](tutorial-arc-enabled-open-service-mesh.md)
> 
> [!div class="nextstepaction"]
> [Microsoft Defender für Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> 
> [!div class="nextstepaction"]
> [Azure App Service in Azure Arc](../../app-service/overview-arc-integration.md)
> 
> [!div class="nextstepaction"]
> [Event Grid in Kubernetes](../../event-grid/kubernetes/overview.md)
> 
> [!div class="nextstepaction"]
> [Azure API Management in Azure Arc](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md)
