---
title: Einrichten von Azure Arc für App Service, Funktionen und Logic Apps
description: Erfahren Sie für Ihre Kubernetes-Cluster mit Azure Arc-Unterstützung, wie Sie App Service-Apps, Funktions-Apps und Logik-Apps aktivieren.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: a330d68ed556a60261ca91e6bfb32fdddf52dc14
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435584"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>Einrichten eines Kubernetes-Clusters mit Azure Arc-Unterstützung zum Ausführen von App Service, Funktionen und Logic Apps (Vorschau)

Wenn Sie über einen [Kubernetes-Cluster](../azure-arc/kubernetes/overview.md) mit Azure Arc-Unterstützung verfügen, können Sie damit einen [App Service-fähigen benutzerdefinierten Standort](overview-arc-integration.md) erstellen und dort Web-Apps, Funktions-Apps und Logik-Apps bereitstellen.

Kubernetes mit Azure Arc-Unterstützung erlaubt es Ihnen, Ihren lokalen oder Cloud-Kubernetes-Cluster für App Service, Functions und Logic Apps in Azure sichtbar zu machen. Sie können eine App erstellen und sie dort wie in jeder anderen Azure-Region bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie noch kein Azure-Konto besitzen, [registrieren Sie sich heute](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) für ein kostenloses Konto.

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>Hinzufügen von Azure CLI-Erweiterungen

Starten Sie die Bash-Umgebung in [Azure Cloud Shell](../cloud-shell/quickstart.md).

[![Starten von Cloud Shell in einem neuen Fenster](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Da diese CLI-Befehle noch nicht Teil des CLI-Kernsets sind, fügen Sie sie mit den folgenden Befehlen hinzu.

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```

## <a name="create-a-connected-cluster"></a>Erstellen eines verbundenen Clusters

> [!NOTE]
> In diesem Tutorial wird [Azure Kubernetes Service (AKS)](../aks/index.yml) verwendet, um konkrete Anweisungen zum Einrichten einer Umgebung von Grund auf bereitzustellen. Für eine Produktionsworkload sollten Sie jedoch Azure Arc wahrscheinlich nicht in einem AKS-Cluster aktivieren, da es bereits in Azure verwaltet wird. Die folgenden Schritte helfen Ihnen beim Einstieg in das Verständnis des Diensts. Für Produktionsbereitstellungen sollten sie die Schritte jedoch nur als illustrativ und nicht als verbindlich betrachten. Allgemeine Anweisungen zum Erstellen eines Kubernetes-Clusters mit Azure Arc-Unterstützung finden Sie unter [Schnellstart: Verbinden eines vorhandenen Kubernetes-Clusters mit Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Erstellen Sie einen Cluster in Azure Kubernetes Service mit einer öffentlichen IP-Adresse. Ersetzen Sie `<group-name>` durch den Namen der gewünschten Ressourcengruppe.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    ```

    ---
    
2. Rufen Sie die Datei [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) ab, und testen Sie ihre Verbindung mit dem Cluster. Standardmäßig wird die Datei „kubeconfig“ in `~/.kube/config` gespeichert.

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. Erstellen Sie eine Ressourcengruppe für Ihre Azure Arc-Ressourcen. Ersetzen Sie `<group-name>` durch den Namen der gewünschten Ressourcengruppe.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. Verbinden Sie den erstellten Cluster mit Azure Arc.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. Überprüfen Sie die Verbindung mit dem folgenden Befehl. Die `provisioningState`-Eigenschaft sollte als `Succeeded` angezeigt werden. Falls nicht, führen Sie den Befehl nach einer Minute erneut aus.

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Während ein [Log Analytics-Arbeitsbereich](../azure-monitor/logs/quick-create-workspace.md) nicht erforderlich ist, um App Service in Azure Arc auszuführen, können Entwickler jedoch auf diese Weise Anwendungsprotokolle für ihre Apps erhalten, die im Kubernetes-Cluster mit Azure Arc-Unterstützung ausgeführt werden. 

1. Erstellen Sie der Einfachheit halber jetzt den Arbeitsbereich.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. Führen Sie die folgenden Befehle aus, um die codierte Arbeitsbereichs-ID und den gemeinsam genutzten Schlüssel für einen vorhandenen Log Analytics-Arbeitsbereich abzurufen. Sie benötigen diese Angaben im nächsten Schritt.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64 -w0) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEnc=$(printf %s $logAnalyticsKey | base64 -w0) # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>Installieren der App Service-Erweiterung

1. Legen Sie die folgenden Umgebungsvariablen für den gewünschten Namen der [App Service-Erweiterung](overview-arc-integration.md), den Clusternamespace, in dem Ressourcen bereitgestellt werden sollen, und den Namen für die App Service Kubernetes-Umgebung fest. Wählen Sie einen eindeutigen Namen für `<kube-environment-name>` aus, weil er Teil des Domänennamens für die App ist, die in der App Service Kubernetes-Umgebung erstellt wurde.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. Installieren Sie die App Service-Erweiterung in Ihrem mit Azure Arc verbundenen Cluster mit aktiviertem Log Analytics. Auch wenn Log Analytics hier ebenfalls nicht erforderlich ist, können Sie es der Erweiterung später nicht mehr hinzufügen, sodass dies jetzt einfacher ist.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > Um die Erweiterung ohne Log Analytics-Integration zu installieren, entfernen Sie die letzten drei `--configuration-settings`-Parameter aus dem Befehl.
    >

    In der folgenden Tabelle sind die verschiedenen `--configuration-settings`-Parameter bei Ausführung des Befehls beschrieben:
    
    | Parameter | Beschreibung |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | Das Dienstkonto, das für den benutzerdefinierten Standort erstellt werden soll, der erstellt wird. Es wird empfohlen, dies auf den Wert `default` festzulegen. |
    | `appsNamespace` | Der Namespace zum Bereitstellen der App-Definitionen und Pods. **Muss** mit dem Releasenamespace der Erweiterung übereinstimmen. |
    | `clusterName` | Der Name der App Service Kubernetes-Umgebung, die für diese Erweiterung erstellt wird. |
    | `keda.enabled` | Gibt an, ob [KEDA](https://keda.sh/) auf dem Kubernetes-Cluster werden soll. Für diesen Parameter kann `true` oder `false` angegeben werden. |
    | `buildService.storageClassName` | Der [Name der Speicherklasse](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class) für den Builddienst zum Speichern von Buildartefakten. Ein Wert wie `default` gibt eine Klasse namens `default` an, nicht [eine Klasse, die als Standard markiert ist](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).  Der Standardwert ist eine gültige Speicherklasse für AKS und AKS HCI, aber möglicherweise nicht für andere Distributionen/Plattformen. |
    | `buildService.storageAccessMode` | Der [Zugriffsmodus](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes), der mit der oben erwähnten, benannten Speicherklasse verwendet werden soll. Für diesen Parameter kann `ReadWriteOnce` oder `ReadWriteMany` angegeben werden. |
    | `customConfigMap` | Der Name der ConfigMap (Konfigurationszuordnung), die von der App Service Kubernetes-Umgebung festgelegt wird. Derzeit muss es `<namespace>/kube-environment-config` sein, wobei `<namespace>` durch den Wert von `appsNamespace` weiter oben ersetzt wird. |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Der Name der Ressourcengruppe, in der sich der Azure Kubernetes Service Cluster befindet. Nur gültig und erforderlich, wenn der zugrunde liegende Cluster Azure Kubernetes Service ist.  |
    | `logProcessor.appLogs.destination` | Optional. Akzeptiert `log-analytics` oder `none`, wenn Sie „none“ (keine) auswählen, werden Plattformprotokolle deaktiviert. |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | Nur erforderlich, wenn `logProcessor.appLogs.destination` auf `log-analytics` festgelegt ist. Die base64-codierte Log Analytics-Arbeitsbereichs-ID. Dieser Parameter sollte als geschützte Einstellung konfiguriert werden. |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | Nur erforderlich, wenn `logProcessor.appLogs.destination` auf `log-analytics` festgelegt ist. Der base64-codierte gemeinsam verwendete Schlüssel des Log Analytics-Arbeitsbereichs. Dieser Parameter sollte als geschützte Einstellung konfiguriert werden. |
    | | |
        
3. Speichern Sie die `id`-Eigenschaft der App Service-Erweiterung für später.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. Warten Sie, bis die Erweiterung vollständig installiert ist, bevor Sie fortfahren. Sie können ihre Terminalsitzung warten lassen, bis dies abgeschlossen ist, indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

Sie können `kubectl` verwenden, um die Pods anzuzeigen, die in Ihrem Kubernetes-Cluster erstellt wurden:

```bash
kubectl get pods -n $namespace
```

Weitere Informationen zu diesen Pods und ihrer Rolle im System finden Sie unter [Pods, die von der App Service-Erweiterung erstellt wurden](overview-arc-integration.md#pods-created-by-the-app-service-extension).

## <a name="create-a-custom-location"></a>Erstellen eines benutzerdefinierten Standorts

Der [benutzerdefinierte Standort](../azure-arc/kubernetes/custom-locations.md) in Azure wird verwendet, um die App Service Kubernetes-Umgebung zuzuweisen.

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. Legen Sie die folgenden Umgebungsvariablen für den gewünschten Namen des benutzerdefinierten Standorts und für die ID des verbundenen Azure Arc-Clusters fest.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. Erstellen des benutzerdefinierten Standorts:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. Überprüfen Sie mit dem folgenden Befehl, ob der benutzerdefinierte Standort erfolgreich erstellt wurde. In der Ausgabe sollte die `provisioningState`-Eigenschaft als `Succeeded` angezeigt werden. Falls nicht, führen Sie den Befehl nach einer Minute erneut aus.

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. Speichern Sie die benutzerdefinierte Standort-ID für den nächsten Schritt.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>Erstellen der App Service Kubernetes-Umgebung

Bevor Sie mit dem Erstellen von Apps am benutzerdefinierten Standort beginnen können, benötigen Sie eine [App Service Kubernetes-Umgebung](overview-arc-integration.md#app-service-kubernetes-environment).

1. Erstellen der App Service Kubernetes-Umgebung:
    
    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `      
    ```

    ---
    
2. Überprüfen Sie mit dem folgenden Befehl, ob die App Service Kubernetes-Umgebung erfolgreich erstellt wurde. In der Ausgabe sollte die `provisioningState`-Eigenschaft als `Succeeded` angezeigt werden. Falls nicht, führen Sie den Befehl nach einer Minute erneut aus.

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Web-App in Azure Arc](quickstart-arc.md)
- [Erstellen Ihrer ersten Funktion in Azure Arc](../azure-functions/create-first-function-arc-cli.md)
- [Erstellen Ihrer ersten Logik-App in Azure Arc](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
