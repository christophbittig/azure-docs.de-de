---
title: Konfigurieren von Azure Red Hat OpenShift (Version 4.x) mit Container Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Überwachung für einen in Azure Red Hat OpenShift (Version 4 oder höher) gehosteten Kubernetes-Cluster mit Azure Monitor konfigurieren können.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 4f9bdaf07903294ede0b8530a28e716c27d9cd9e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231510"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Konfigurieren von Azure Red Hat OpenShift (Version 4.x) mit Container Insights

Container Insights bietet umfassende Überwachungsfunktionen für Azure Kubernetes Service- (AKS) und AKS-Engine-Cluster. In diesem Artikel wird beschrieben, wie Sie Überwachung für unter [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) (Version 4.x) gehostete Kubernetes-Cluster aktivieren, um eine ähnliche Überwachungsfunktion zu erzielen.

>[!NOTE]
>Die Unterstützung für Azure Red Hat OpenShift ist zurzeit als Feature in der öffentlichen Vorschauversion verfügbar.
>

Sie können Container Insights für mindestens eine vorhandene Bereitstellung von Azure Red Hat OpenShift (Version 4.x) mit den folgenden unterstützten Methoden aktivieren, die in diesem Artikel beschrieben werden.

Führen Sie für einen vorhandenen Cluster dieses [Bash-Skript in der Azure CLI](/cli/azure/openshift#az_openshift_create&preserve-view=true) aus.

## <a name="supported-and-unsupported-features"></a>Unterstützte und nicht unterstützte Funktionen

Container Insights unterstützt wie im Artikel [Übersicht zu Container Insights](container-insights-overview.md) beschrieben die Überwachung von Azure Red Hat OpenShift (Version 4.x) mit Ausnahme der folgenden Features:

- Livedaten (Vorschauversion)
- [Erfassen von Metriken](container-insights-update-metrics.md) von Clusterknoten und Pods sowie Speichern dieser Metriken in der Azure Monitor-Metrikdatenbank

## <a name="prerequisites"></a>Voraussetzungen

- Azure CLI mit Version 2.0.72 oder höher  

- CLI-Tool [Helm 3](https://helm.sh/docs/intro/install/)

- Aktuelle Version von [OpenShift CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash, Version 4](https://www.gnu.org/software/bash/)

- Befehlszeilentool [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Einen [Log Analytics-Arbeitsbereich](../logs/design-logs-deployment.md).

    Container Insights unterstützt einen Log Analytics-Arbeitsbereich in den unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) aufgeführten Regionen in Azure. Ihren eigenen Arbeitsbereich können Sie über [Azure Resource Manager](../logs/resource-manager-workspace.md), über [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json) oder im [Azure-Portal](../logs/quick-create-workspace.md) erstellen.

- Zum Aktivieren der Funktionen und Zugreifen auf die Features in Container Insights benötigen Sie mindestens eine Azure-Rolle *Mitwirkender* im Azure-Abonnement und eine Rolle [*Log Analytics-Mitwirkender*](../logs/manage-access.md#manage-access-using-azure-permissions) im Log Analytics-Arbeitsbereich, die mit Container Insights konfiguriert wurde.

- Sie müssen über die Rolle [*Log Analytics-Leser*](../logs/manage-access.md#manage-access-using-azure-permissions) im mit Container Insights konfigurierten Log Analytics-Arbeitsbereich verfügen, um die Überwachungsdaten anzeigen zu können.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Aktivieren der Überwachung für einen vorhandenen Cluster

Gehen Sie folgendermaßen vor, um die Überwachung eines in Azure bereitgestellten Clusters für Azure Red Hat OpenShift (Version 4 oder höher) mithilfe des bereitgestellten Bash-Skripts zu aktivieren:

1. Melden Sie sich mithilfe des folgenden Befehls bei Azure an:

    ```azurecli
    az login
    ```

1. Laden Sie das Skript herunter, und speichern Sie es in einem lokalen Ordner, das Ihren Cluster über die folgenden Befehle mit dem Überwachungs-Add-In konfiguriert:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Stellen Sie mithilfe der Anweisungen in [Tutorial: Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster](../../openshift/tutorial-connect-cluster.md) eine Verbindung mit dem ARO v4-Cluster her.


### <a name="integrate-with-an-existing-workspace"></a>Integrieren in einen vorhandenen Arbeitsbereich

In diesem Abschnitt aktivieren Sie die Überwachung Ihres Clusters mithilfe des zuvor heruntergeladenen Bash-Skripts. Wenn Sie die Integration in einen vorhandenen Log Analytics-Arbeitsbereich vornehmen möchten, ermitteln Sie zunächst die vollständige Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs, die für den `logAnalyticsWorkspaceResourceId`-Parameter erforderlich ist, und führen dann den Befehl zum Aktivieren des Überwachungs-Add-Ins für den angegebenen Arbeitsbereich aus.

Wenn Sie nicht über einen Arbeitsbereich verfügen, den Sie angeben, fahren Sie direkt mit [Integration in den Standardarbeitsbereich](#integrate-with-the-default-workspace) fort, und lassen Sie das Skript einen neuen Arbeitsbereich für Sie erstellen.

1. Listen Sie mit dem folgenden Befehl alle Abonnements auf, auf die Sie Zugriff besitzen:

    ```azurecli
    az account list --all -o table
    ```

    Die Ausgabe sieht dann wie folgt aus:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopieren Sie den Wert für **SubscriptionId**.

1. Wechseln Sie durch Ausführen des folgenden Befehls zu dem Abonnement, das den Log Analytics-Arbeitsbereich hostet:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Zeigen Sie die Liste der Arbeitsbereiche in Ihren Abonnements im JSON-Standardformat an, indem Sie den folgenden Befehl ausführen:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Suchen Sie in der Ausgabe nach dem Namen des Arbeitsbereichs, und kopieren Sie dann die vollständige Ressourcen-ID dieses Log Analytics-Arbeitsbereichs unter dem Feld **ID**.

1. Führen Sie den folgenden Befehl aus, um die Überwachung zu aktivieren. Ersetzen Sie die Werte für die Parameter `azureAroV4ClusterResourceId` und `logAnalyticsWorkspaceResourceId`.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Nachfolgend ist der Befehl angegeben, den Sie ausführen müssen, nachdem Sie die drei Variablen mit Exportbefehlen ausgefüllt haben:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Nach dem Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis Integritätsmetriken für den Cluster angezeigt werden.

### <a name="integrate-with-the-default-workspace"></a>Integrieren in den Standardarbeitsbereich

In diesem Abschnitt aktivieren Sie die Überwachung Ihres Azure Red Hat OpenShift-Clusters (Version 4.x) mithilfe des heruntergeladenen Bash-Skripts.

In diesem Beispiel müssen Sie keinen Arbeitsbereich vorab erstellen bzw. keinen vorhandenen Arbeitsbereich angeben. Dieser Befehl vereinfacht den Prozess durch Erstellen eines Standardarbeitsbereichs in der Standardressourcengruppe des Clusterabonnements, wenn noch keine Arbeitsbereich in der Region vorhanden ist.

Der erstellte Standardarbeitsbereich weist das Format *DefaultWorkspace-\<GUID>-\<Region>* auf.  

Ersetzen Sie den Wert für den `azureAroV4ClusterResourceId`-Parameter.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Beispiel:

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId 

Nach dem Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis Integritätsmetriken für den Cluster angezeigt werden.

### <a name="enable-monitoring-from-the-azure-portal"></a>Aktivieren der Überwachung über das Azure-Portal

In der Ansicht für mehrere Cluster in Container Insights werden Ihre Azure Red Hat OpenShift-Cluster auf der Registerkarte **Nicht überwachte Cluster** hervorgehoben, für die die Überwachung nicht aktiviert ist. Durch die Option **Aktivieren** neben Ihrem Cluster wird kein Onboarding für die Überwachung über das Portal durchgeführt. Sie werden zu diesem Artikel weitergeleitet, um die Überwachung manuell zu aktivieren. Befolgen Sie hierzu die weiter oben in diesem Artikel beschriebenen Schritte.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im linken Bereich oder auf der Homepage auf **Azure Monitor**.

1. Wählen Sie im Abschnitt **Insights** die Option **Container** aus.

1. Wählen Sie auf der Seite **Überwachung – Container** die Option **Nicht überwachte Cluster** aus.

1. Wählen Sie in der Liste der nicht überwachten Cluster den Cluster aus, und wählen Sie dann **Aktivieren** aus.

    Sie können die Ergebnisse in der Liste identifizieren, indem Sie in der Spalte **Clustertyp** nach dem Wert **ARO** suchen. Nachdem Sie auf **Aktivieren** geklickt haben, werden Sie zu diesem Artikel weitergeleitet.

## <a name="next-steps"></a>Nächste Schritte

- Da Sie nun die Überwachung zum Erfassen der Integrität und Ressourcennutzung Ihres RedHat OpenShift-Clusters (Version 4.x) und der darunter ausgeführten Workloads aktiviert haben, informieren Sie sich über die [Verwendung](container-insights-analyze.md) von Container Insights.

- Standardmäßig sammelt der Container-Agent die Containerprotokolle *stdout* und *stderr* aller Container, die in allen Namespaces mit Ausnahme von kube-system ausgeführt werden. Wenn Sie die Containerprotokollsammlung bestimmter Namespaces konfigurieren möchten, finden Sie weitere Informationen unter [Agent-Konfiguration für Container Insights](container-insights-agent-config.md). In diesem Artikel wird beschrieben, wie Sie die gewünschten Einstellungen für die Datensammlung für Ihre *ConfigMap*-Konfigurationsdatei konfigurieren.

- Weitere Informationen zum Auslesen und Analysieren von Prometheus-Metriken aus Ihrem Cluster finden Sie unter [Konfigurieren des Abrufs von Prometheus-Metriken](container-insights-prometheus-integration.md).

- Informationen zum Beenden der Überwachung Ihres Clusters mit Container Insights finden Sie unter [Beenden der Überwachung von Azure Red Hat OpenShift-Clustern](./container-insights-optout-openshift-v3.md).