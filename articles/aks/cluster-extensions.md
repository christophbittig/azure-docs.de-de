---
title: Cluster-Erweiterungen für Azure Kubernetes Service (AKS) (Vorschau)
description: Erfahren Sie, wie Sie den Lebenszyklus von Erweiterungen auf Azure Kubernetes Service (AKS) bereitstellen und verwalten können.
ms.service: container-service
ms.date: 10/13/2021
ms.topic: article
author: nickomang
ms.author: nickoman
ms.openlocfilehash: 80d6eb34e1b1e0bbce6a8a1f1d2de58dbec51b4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447422"
---
# <a name="deploy-and-manage-cluster-extensions-for-azure-kubernetes-service-aks-preview"></a>Bereitstellung und Verwaltung von Cluster-Erweiterungen für Azure Kubernetes Service (AKS) (Vorschau)

Cluster Extensions bietet eine vom Azure Resource Manager gesteuerte Erfahrung für die Installation und das Lebenszyklusmanagement von Diensten wie Azure Machine Learning (ML) auf einem AKS-Cluster. Diese Funktion ermöglicht Folgendes:

* Azure Resource Manager-basierte Bereitstellung von Erweiterungen, einschließlich At-Scale-Bereitstellungen über AKS-Cluster.
* Lebenszyklusverwaltung der Erweiterung (Aktualisieren, Löschen) über Azure Resource Manager.

In diesem Artikel erfahren Sie mehr darüber:
> [!div class="checklist"]

> * Wie man eine Erweiterungsinstanz erstellt.
> * Verfügbare Cluster-Erweiterungen auf AKS.
> * Anzeigen, Auflisten, Aktualisieren und Löschen von Erweiterungsinstanzen

Einen konzeptionellen Überblick über diese Funktion finden Sie im Artikel [Cluster-Erweiterungen - Azure Arc-fähiges Kubernetes][arc-k8s-extensions].

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* [Azure CLI](/cli/azure/install-azure-cli) Version >= 2.16.0 installiert.

### <a name="register-provider-for-cluster-extensions"></a>Anbieter für Cluster-Erweiterungen registrieren

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie die folgenden Befehle ein:

    ```azurecli-interactive
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ContainerService
    ```

2. Überwachen Sie den Registrierungsprozess. Die Registrierung kann bis zu 10 Minuten dauern.

    ```azurecli-interactive
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ContainerService -o table
    ```

    Nach der Registrierung sollte der `RegistrationState`-Status für diese Namespaces in `Registered` geändert werden.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Geben Sie die folgenden Befehle ein:

    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

1. Überwachen Sie den Registrierungsprozess. Die Registrierung kann bis zu 10 Minuten dauern.

    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

    Nach der Registrierung sollte der `RegistrationState`-Status für diese Namespaces in `Registered` geändert werden.

---

### <a name="register-the-aks-extensionmanager-preview-features"></a>Registrieren Sie die `AKS-ExtensionManager` Vorschaufunktionen

Um einen AKS-Cluster zu erstellen, der Cluster-Erweiterungen verwenden kann, müssen Sie das Funktionskennzeichen `AKS-ExtensionManager` in Ihrem Abonnement aktivieren.

Registrieren Sie das Featureflag `AKS-ExtensionManager` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie die Registrierung der *Microsoft.KubernetesConfiguration* und *Microsoft.ContainerService* Ressourcenanbieter mit dem Befehl [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Einrichten der Azure CLI-Erweiterung für Clustererweiterungen

> [!NOTE]
> Die minimal unterstützte Version für die `k8s-extension` Azure CLI-Erweiterung ist `1.0.0`. Wenn Sie sich nicht sicher sind, welche Version Sie installiert haben, führen Sie `az extension show --name k8s-extension` aus und suchen Sie das Feld `version`.

Sie benötigen auch die `k8s-extension`Azure CLI-Erweiterung. Führen Sie zur Installation die folgenden Befehle aus:
  
```azurecli-interactive
az extension add --name k8s-extension
```

Wenn die Erweiterung `k8s-extension` bereits installiert ist, können Sie sie mit dem folgenden Befehl auf die neueste Version aktualisieren:

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="currently-available-extensions"></a>Derzeit verfügbare Erweiterungen

>[!NOTE]
> Cluster-Erweiterungen bieten eine Plattform für verschiedene Erweiterungen, die auf einem AKS-Cluster installiert und verwaltet werden können. Wenn Sie bei der Verwendung einer dieser Erweiterungen auf Probleme stoßen, eröffnen Sie bitte ein Support-Ticket bei dem entsprechenden Dienst.

| Durchwahl | BESCHREIBUNG |
| --------- | ----------- |
| [Dapr][dapr-overview] | Dapr ist eine portable, ereignisgesteuerte Runtime, die es jedem Entwickler einfach macht, robuste, zustandslose und zustandsbehaftete Anwendungen zu erstellen, die in der Cloud und in Edge ausgeführt werden. |
| [Azure ML][azure-ml-overview] | Verwenden Sie Azure Kubernetes Service-Cluster zum Trainieren, für Rückschlüsse und zum Verwalten von Machine Learning-Modellen in Azure Machine Learning. |

## <a name="supported-regions-and-kubernetes-versions"></a>Unterstützte Regionen und Kubernetes-Versionen

Cluster-Erweiterungen können auf AKS-Clustern in den Regionen verwendet werden, die in [Azure Arc enabled Kubernetes region support][arc-k8s-regions] aufgeführt sind.

Die unterstützten Kubernetes-Versionen finden Sie in der entsprechenden Dokumentation für jede Erweiterung.

## <a name="usage-of-cluster-extensions"></a>Verwendung von Clustererweiterungen

> [!NOTE]
> Die in diesem Artikel enthaltenen Beispiele sind nicht vollständig und dienen nur zur Veranschaulichung der Funktionalität. Eine umfassende Liste der Befehle und ihrer Parameter finden Sie in der [az k8s-extension CLI-Referenz][k8s-extension-reference].

### <a name="create-extensions-instance"></a>Erstellen von Erweiterungsinstanzen

Erstellen Sie eine neue Erweiterungsinstanz mit `k8s-extension create`, und übergeben Sie Werte für die obligatorischen Parameter. Der folgende Befehl erstellt eine Azure Machine Learning-Erweiterungsinstanz auf Ihrem AKS-Cluster:

```azurecli
az k8s-extension create --name aml-compute --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters --configuration-settings enableInference=True allowInsecureConnections=True
```

> [!NOTE]
> Der Dienst Cluster Extensions ist nicht in der Lage, sensible Informationen länger als 48 Stunden aufzubewahren. Wenn die Agenten der Clustererweiterung länger als 48 Stunden keine Netzverbindung haben und nicht feststellen können, ob eine Erweiterung auf dem Cluster erstellt werden soll, geht die Erweiterung in den Zustand `Failed` über. Sobald Sie sich im Zustand `Failed` befinden, müssen Sie `k8s-extension create` erneut ausführen, um eine neue Erweiterungsinstanz zu erstellen.

#### <a name="required-parameters"></a>Erforderliche Parameter

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name` | Name der Erweiterungsinstanz |
| `--extension-type` | Der Typ der Erweiterung, die Sie auf dem Cluster installieren möchten. Beispiel: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Name des AKS-Clusters, auf dem die Erweiterungsinstanz angelegt werden soll |
| `--resource-group` | Die Ressourcengruppe, die den AKS-Cluster enthält |
| `--cluster-type` | Der Clustertyp, auf dem die Erweiterungsinstanz erstellt werden soll. Geben Sie `managedClusters` an, da es auf AKS-Cluster abgebildet wird|

#### <a name="optional-parameters"></a>Optionale Parameter

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--auto-upgrade-minor-version` | Boolesche Eigenschaft, die angibt, ob die Nebenversion der Erweiterung automatisch aktualisiert wird. Standardwert: `true`.  Wenn dieser Parameter auf „true“ festgelegt ist, können Sie den `version`-Parameter nicht festlegen, da die Version dynamisch aktualisiert wird. Wenn der Wert `false` festgelegt ist, wird die Erweiterung auch für Patchversionen nicht automatisch aktualisiert. |
| `--version` | Version der zu installierenden Erweiterung (bestimmte Version, an die die Erweiterungsinstanz angeheftet wird). Muss nicht angegeben werden, wenn „auto-upgrade-minor-version“ auf `true` festgelegt ist. |
| `--configuration-settings` | Einstellungen, die an die Erweiterung übergeben werden können, um deren Funktionalität zu steuern. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um Konfigurationseinstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings` | Diese Einstellungen können nicht mit `GET`-API-Aufrufen oder `az k8s-extension show`-Befehlen abgerufen werden, und werden daher verwendet, um sensible Einstellungen zu übergeben. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um sensible Einstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings` nicht im selben Befehl verwendet werden. |
| `--scope` | Umfang der Installation für die Erweiterung – `cluster` oder `namespace` |
| `--release-namespace` | Dieser Parameter gibt den Namespace an, in dem die Freigabe erstellt werden soll. Dieser Parameter ist nur relevant, wenn der `scope`-Parameter auf `cluster` festgelegt ist. |
| `--release-train` |  Erweiterungsautoren können Versionen in unterschiedlichen Release Trains wie `Stable`, `Preview` usw. veröffentlichen. Wenn dieser Parameter nicht explizit festgelegt ist, wird `Stable` als Standard verwendet. Dieser Parameter kann nicht verwendet werden, wenn der `autoUpgradeMinorVersion`-Parameter auf `false` festgelegt ist. |
| `--target-namespace` | Dieser Parameter gibt den Namespace an, in dem die Freigabe erstellt wird. Die Berechtigung des für diese Erweiterungsinstanz erstellten Systemkontos wird auf diesen Namespace beschränkt. Dieser Parameter ist nur relevant, wenn der `scope`-Parameter auf `namespace` festgelegt ist. |

### <a name="show-details-of-an-extension-instance"></a>Anzeigen von Details einer Erweiterungsinstanz

Zeigen Sie Details einer aktuell installierten Erweiterungsinstanz mit `k8s-extension show` an, wobei Sie Werte für die obligatorischen Parameter übergeben:

```azurecli
az k8s-extension show --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Alle auf dem Cluster installierten Erweiterungen auflisten

Auflisten aller auf dem Cluster installierten Erweiterungen mit `k8s-extension list`, wobei Werte für die obligatorischen Parameter übergeben werden.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="update-extension-instance"></a>Erweiterungsinstanz aktualisieren

> [!NOTE]
> In der Dokumentation des Erweiterungstyps (z. B. Azure ML) finden Sie Informationen zu den spezifischen Einstellungen unter ConfigurationSetting und ConfigurationProtectedSettings, die aktualisiert werden dürfen. Bei ConfigurationProtectedSettings wird erwartet, dass alle Einstellungen bei der Aktualisierung einer einzelnen Einstellung bereitgestellt werden. Wenn einige Einstellungen weggelassen werden, werden diese Einstellungen als veraltet betrachtet und gelöscht.

Aktualisieren Sie eine vorhandene Erweiterungsinstanz mit `k8s-extension update`, indem Sie Werte für die obligatorischen Parameter übergeben. Der folgende Befehl aktualisiert die Auto-Upgrade-Einstellung für eine Azure Machine Learning-Erweiterungsinstanz:

```azurecli
az k8s-extension update --name azureml --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name` | Name der Erweiterungsinstanz |
| `--extension-type` | Der Typ der Erweiterung, die Sie auf dem Cluster installieren möchten. Beispiel: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Name des AKS-Clusters, auf dem die Erweiterungsinstanz angelegt werden soll |
| `--resource-group` | Die Ressourcengruppe, die den AKS-Cluster enthält |
| `--cluster-type` | Der Clustertyp, auf dem die Erweiterungsinstanz erstellt werden soll. Geben Sie `managedClusters` an, da es auf AKS-Cluster abgebildet wird|

**Optionale Parameter**

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--auto-upgrade-minor-version` | Boolesche Eigenschaft, die angibt, ob die Nebenversion der Erweiterung automatisch aktualisiert wird. Standardwert: `true`.  Wenn dieser Parameter auf „true“ festgelegt ist, können Sie den `version`-Parameter nicht festlegen, da die Version dynamisch aktualisiert wird. Wenn der Wert `false` festgelegt ist, wird die Erweiterung auch für Patchversionen nicht automatisch aktualisiert. |
| `--version` | Version der zu installierenden Erweiterung (bestimmte Version, an die die Erweiterungsinstanz angeheftet wird). Muss nicht angegeben werden, wenn „auto-upgrade-minor-version“ auf `true` festgelegt ist. |
| `--configuration-settings` | Einstellungen, die an die Erweiterung übergeben werden können, um deren Funktionalität zu steuern. Nur die Einstellungen, die eine Aktualisierung erfordern, müssen angegeben werden. Die angegebenen Einstellungen würden durch die angegebenen Werte ersetzt werden. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um Konfigurationseinstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-settings` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings` | Diese Einstellungen können nicht mit `GET`-API-Aufrufen oder `az k8s-extension show`-Befehlen abgerufen werden, und werden daher verwendet, um sensible Einstellungen zu übergeben. Wenn eine Einstellung aktualisiert wird, wird erwartet, dass alle Einstellungen angegeben werden. Wenn einige Einstellungen weggelassen werden, werden diese Einstellungen als veraltet betrachtet und gelöscht. Sie müssen als leerzeichengetrennte `key=value`-Paare nach dem Parameternamen übergeben werden. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings-file` nicht im selben Befehl verwendet werden. |
| `--configuration-protected-settings-file` | Der Pfad zur JSON-Datei mit Schlüsselwertpaaren, die verwendet werden sollen, um sensible Einstellungen an die Erweiterung zu übergeben. Wenn dieser Parameter im Befehl verwendet wird, kann `--configuration-protected-settings` nicht im selben Befehl verwendet werden. |
| `--scope` | Umfang der Installation für die Erweiterung – `cluster` oder `namespace` |
| `--release-train` |  Erweiterungsautoren können Versionen in unterschiedlichen Release Trains wie `Stable`, `Preview` usw. veröffentlichen. Wenn dieser Parameter nicht explizit festgelegt ist, wird `Stable` als Standard verwendet. Dieser Parameter kann nicht verwendet werden, wenn der `autoUpgradeMinorVersion`-Parameter auf `false` festgelegt ist. |

### <a name="delete-extension-instance"></a>Löschen der Erweiterungsinstanz

>[!NOTE]
> Die Azure-Ressource, die diese Erweiterung darstellt, wird sofort gelöscht. Die Helm-Freigabe auf dem Cluster, die dieser Erweiterung zugeordnet ist, wird nur gelöscht, wenn die auf dem Kubernetes-Cluster ausgeführten Agents über Netzwerkkonnektivität verfügen und wieder auf Azure-Dienste zugreifen können, um den gewünschten Status abzurufen.

Löschen einer Erweiterungsinstanz auf einem Cluster mit `k8s-extension delete`, wobei Werte für die obligatorischen Parameter übergeben werden.

```azurecli
az k8s-extension delete --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

<!-- LINKS -->
<!-- INTERNAL -->
[arc-k8s-extensions]: ../azure-arc/kubernetes/conceptual-extensions.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-ml-overview]: ../machine-learning/how-to-attach-arc-kubernetes.md
[dapr-overview]: ./dapr.md
[k8s-extension-reference]: /cli/azure/k8s-extension

<!-- EXTERNAL -->
[arc-k8s-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc&regions=all