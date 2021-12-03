---
title: Überwachen von Azure Arc-fähigen Kubernetes-Clustern
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Erfassen von Metriken und Protokollen von Azure Arc-fähigen Kubernetes-Clustern mithilfe von Azure Monitor
ms.openlocfilehash: 90a648c5cb6ace4d1f055532d335a705220e2716
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465457"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor Container Insights für Azure Arc-fähige Kubernetes-Cluster

[Azure Monitor Container Insights](container-insights-overview.md) bietet umfassende Überwachungsfunktionen für Azure Arc-fähige Kubernetes-Cluster.


## <a name="supported-configurations"></a>Unterstützte Konfigurationen

- Azure Monitor Container Insights unterstützt die Überwachung von Azure Arc-fähigen Kubernetes-Clustern wie im [Übersichtsartikel](container-insights-overview.md) beschrieben, mit Ausnahme des Features für Livedaten. Außerdem müssen Benutzer zum [Aktivieren von Metriken](container-insights-update-metrics.md) nicht über Berechtigungen vom Typ [Besitzer](../../role-based-access-control/built-in-roles.md#owner) verfügen.
- `Docker`, `Moby` und CRI-kompatible Containerruntimes wie `CRI-O` und `containerd`
- Proxys für ausgehenden Datenverkehr ohne Authentifizierung und mit Standardauthentifizierung werden unterstützt. Proxys für ausgehenden Datenverkehr, von denen Zertifikate erwartet werden, werden aktuell nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Vergewissern Sie sich, dass die in der [Dokumentation zu generischen Clustererweiterungen](../../azure-arc/kubernetes/extensions.md#prerequisites) aufgeführten Voraussetzungen erfüllt sind.
- Log Analytics-Arbeitsbereich: Von Azure Monitor Container Insights wird ein Log Analytics-Arbeitsbereich in den auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) aufgeführten Regionen unterstützt. Sie können Ihren eigenen Arbeitsbereich über [Azure Resource Manager](../logs/resource-manager-workspace.md), mithilfe von [PowerShell](../logs/powershell-workspace-configuration.md) oder über das [Azure-Portal](../logs/quick-create-workspace.md) erstellen.
- Sie müssen für das Azure-Abonnement, das die Azure Arc-fähige Kubernetes-Ressource enthält, über die Rollenzuweisung [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) verfügen. Befindet sich der Log Analytics-Arbeitsbereich in einem anderen Abonnement, wird für den Log Analytics-Arbeitsbereich die Rolle [Log Analytics-Mitwirkender](../logs/manage-access.md#manage-access-using-azure-permissions) benötigt.
- Zum Anzeigen der Überwachungsdaten müssen Sie im Log Analytics-Arbeitsbereich über die Rollenzuweisung [Log Analytics-Leser](../logs/manage-access.md#manage-access-using-azure-permissions) verfügen.
- Für ausgehenden Zugriff müssen neben den unter [Erfüllen von Netzwerkanforderungen](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements) angegebenen Endpunkten die folgenden Endpunkte aktiviert werden:

    | Endpunkt | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Wenn sich Ihre Azure Arc-fähige Kubernetes-Ressource in einer Umgebung vom Typ „Azure US-Regierung“ befindet, müssen die folgenden Endpunkte für ausgehenden Zugriff aktiviert werden:

    | Endpunkt | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Wenn Sie Azure Monitor Container Insights bereits per Skript ohne Clustererweiterungen in diesem Cluster bereitgestellt haben, gehen Sie wie [hier](container-insights-optout-hybrid.md) beschrieben vor, um dieses Helm-Chart zu löschen. Anschließend können Sie mit dem Erstellen einer Clustererweiterungsinstanz für Azure Monitor Container Insights fortfahren.


### <a name="identify-workspace-resource-id"></a>Identifizieren der Arbeitsbereichsressourcen-ID

Führen Sie die folgenden Befehle aus, um den vollständigen Azure Resource Manager-Bezeichner des Log Analytics-Arbeitsbereichs zu ermitteln. 

1. Listen Sie mit dem folgenden Befehl alle Abonnements auf, auf die Sie Zugriff haben:

    ```azurecli
    az account list --all -o table
    ```

2. Wechseln Sie mit dem folgenden Befehl zu dem Abonnement, das den Log Analytics-Arbeitsbereich hostet:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Im folgenden Beispiel wird die Liste mit den Arbeitsbereichen Ihrer Abonnements im JSON-Standardformat angezeigt.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Suchen Sie in der Ausgabe den relevanten Arbeitsbereichsnamen. Das zugehörige Feld `id` stellt den Azure Resource Manager-Bezeichner dieses Log Analytics-Arbeitsbereichs dar.

    >[!TIP]
    > Diese ID (`id`) finden Sie auch im Azure-Portal auf dem Blatt *Übersicht* des Log Analytics-Arbeitsbereichs.

## <a name="create-extension-instance-using-azure-cli"></a>Erstellen einer Erweiterungsinstanz mithilfe der Azure CLI

### <a name="option-1---with-default-values"></a>Option 1: Mit Standardwerten

Bei dieser Option werden die folgenden Standardwerte verwendet:

- Erstellt oder verwendet einen standardmäßigen Log Analytics-Arbeitsbereich, der der Region des Clusters entspricht.
- Automatisches Upgrade ist für die Azure Monitor-Clustererweiterung aktiviert.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Option 2: Mit vorhandenem Azure Log Analytics-Arbeitsbereich

Sie können einen vorhandenen Azure Log Analytics-Arbeitsbereich in einem beliebigen Abonnement verwenden, für das Sie mindestens über die Rollenzuweisung *Mitwirkender* verfügen.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Option 3: Mit erweiterter Konfiguration

Wenn Sie die standardmäßigen Ressourcenanforderungen und -einschränkungen anpassen möchten, können Sie die erweiterten Konfigurationseinstellungen verwenden:

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Informationen zu den verfügbaren Konfigurationseinstellungen finden Sie im [Abschnitt zu Ressourcenanforderungen und -einschränkungen des Helm-Charts](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml).

### <a name="option-4---on-azure-stack-edge"></a>Option 4: In Azure Stack Edge

Wenn sich der Azure Arc-fähige Kubernetes-Cluster in Azure Stack Edge befindet, muss ein benutzerdefinierter Bereitstellungspfad (`/home/data/docker`) verwendet werden.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Wenn Sie im Erstellungsbefehl explizit die zu installierende Version der Erweiterung angeben, muss mindestens die Version 2.8.2 verwendet werden.

## <a name="create-extension-instance-using-azure-portal"></a>Erstellen einer Erweiterungsinstanz über das Azure-Portal

>[!IMPORTANT]
>  Wenn Sie Azure Monitor in einem Kubernetes-Cluster bereitstellen, der auf Azure Stack Edge basiert, muss anstelle des Azure-Portals die Azure CLI verwendet werden, da für Cluster dieser Art ein benutzerdefinierter Bereitstellungspfad festgelegt werden muss.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Durchführen des Onboardings über das Blatt der Azure Arc-fähigen Kubernetes-Ressource

1. Wählen Sie im Azure-Portal den Azure Arc-fähigen Kubernetes-Cluster aus, den Sie überwachen möchten.

2. Wählen Sie auf dem Blatt der Ressource im Abschnitt „Überwachung“ das Element „Insights“ aus.

3. Wählen Sie auf der Onboardingseite die Schaltfläche „Azure Monitor konfigurieren“ aus.

4. Nun können Sie den [Log Analytics-Arbeitsbereich](../logs/quick-create-workspace.md) auswählen, an den Ihre Metriken und Protokolle gesendet werden sollen.

5. Wählen Sie die Schaltfläche „Konfigurieren“ aus, um die Clustererweiterung „Azure Monitor Container Insights“ bereitzustellen.

### <a name="onboarding-from-azure-monitor-blade"></a>Durchführen des Onboardings über das Blatt „Azure Monitor“

1. Navigieren Sie im Azure-Portal zum Blatt „Monitor“, und wählen Sie im Menü „Insights“ die Option „Container“ aus.

2. Wählen Sie die Registerkarte „Nicht überwachte Cluster“ aus, um die Azure Arc-fähigen Kubernetes-Cluster anzuzeigen, für die Sie die Überwachung aktivieren können.

3. Klicken Sie neben dem Cluster, für den Sie die Überwachung aktivieren möchten, auf den Link „Aktivieren“.

4. Wählen Sie den Log Analytics-Arbeitsbereich und anschließend die Schaltfläche „Konfigurieren“ aus, um den Vorgang fortzusetzen.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Erstellen der Erweiterungsinstanz per Azure Resource Manager

1. Laden Sie die Azure Resource Manager-Vorlage und die zugehörigen Parameter herunter:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Aktualisieren Sie die Parameterwerte in der Datei „arc-k8s-azmon-extension-arm-template-params.json“. Für die öffentliche Azure-Cloud muss `opinsights.azure.com` als Wert für „workspaceDomain“ verwendet werden. Für „AzureUSGovernment“ muss `opinsights.azure.us` als Wert für „workspaceDomain“ verwendet werden.

3. Stellen Sie die Vorlage zum Erstellen der Erweiterung „Azure Monitor Container Insights“ bereit. 

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="verify-extension-installation-status"></a>Überprüfen des Installationsstatus der Erweiterung
Nachdem Sie die Azure Monitor-Erweiterung für Ihren Azure Arc-fähigen Kubernetes-Cluster erfolgreich erstellt haben, können Sie zusätzlich den Installationsstatus über das Azure-Portal oder mit der CLI überprüfen. Bei erfolgreichen Installationen sollte der Status „Installiert“ angezeigt werden. Wenn der Status „Fehler“ angezeigt wird oder die Installation für längere Zeit im Status „Ausstehend“ verbleibt, fahren Sie mit dem Abschnitt „Problembehandlung“ weiter unten fort.

### <a name="azure-portal"></a>Azure-Portal
1. Wählen Sie im Azure-Portal den Azure Arc-fähigen Kubernetes-Cluster aus, auf dem die Erweiterung installiert wird.
2. Wählen Sie auf dem Blatt der Ressource im Abschnitt „Einstellungen“ das Element „Erweiterungen“ aus.
3. Es sollte eine Erweiterung mit dem Namen „azuremonitor-containers“ angezeigt werden, deren Status in der Spalte „Installationsstatus“ angegeben ist.
### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Führen Sie den folgenden Befehl aus, um den aktuellen Status der Erweiterung `Microsoft.AzureMonitor.Containers` anzuzeigen.
```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters -n azuremonitor-containers
```

## <a name="delete-extension-instance"></a>Löschen der Erweiterungsinstanz

Mit dem folgenden Befehl wird nur die Erweiterungsinstanz gelöscht, nicht aber der Log Analytics-Arbeitsbereich. Die Daten in der Log Analytics-Ressource bleiben intakt.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Cluster ohne Verbindung
Wenn Ihr Cluster länger als 48 Stunden nicht mit Azure verbunden ist, verfügt Azure Resource Graph über keine Informationen zu Ihrem Cluster. Dadurch werden auf dem Blatt „Insights“ unter Umständen falsche Informationen zum Zustand Ihres Clusters angezeigt.

## <a name="troubleshooting"></a>Problembehandlung
Bei Schwierigkeiten mit dem Aktivieren der Überwachung steht ein [Skript zur Problembehandlung](https://aka.ms/azmon-ci-troubleshooting) bereit, das bei der Diagnose von Problemen hilft.

## <a name="next-steps"></a>Nächste Schritte

- Wenn die Überwachung aktiviert ist, um Integrität und Ressourcenverwendung Ihres Azure Arc-fähigen Kubernetes-Clusters und der darauf ausgeführten Workloads zu erfassen, informieren Sie sich über die [Verwendung](container-insights-analyze.md) von Container Insights.

- Standardmäßig sammelt der Container-Agent die Containerprotokolle stdout und stderr aller Container, die in allen Namespaces mit Ausnahme von kube-system ausgeführt werden. Wenn Sie die Containerprotokollsammlung bestimmter Namespaces konfigurieren möchten, finden Sie weitere Informationen unter [Agent-Konfiguration für Container Insights](container-insights-agent-config.md). In diesem Artikel wird beschrieben, wie Sie die gewünschten Einstellungen für die Datensammlung für Ihre ConfigMap-Konfigurationsdatei konfigurieren.

- Weitere Informationen zum Auslesen und Analysieren von Prometheus-Metriken aus Ihrem Cluster finden Sie unter [Konfigurieren des Abrufs von Prometheus-Metriken](container-insights-prometheus-integration.md).
