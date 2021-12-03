---
title: Erstellen und Verwalten benutzerdefinierter Speicherorte in Azure Arc-fähigen Kubernetes-Clustern
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Verwenden Sie benutzerdefinierte Speicherorte zum Bereitstellen von Azure-PaaS-Diensten in Azure Arc-fähigen Kubernetes-Clustern.
ms.openlocfilehash: f241ec384fc9ed7ee96d7415074e009cea486811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257024"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Erstellen und Verwalten benutzerdefinierter Speicherorte in Azure Arc-fähigen Kubernetes-Clustern

 Mit *benutzerdefinierten Speicherorten* können Mandanten- oder Clusteradministrator*innen ihre Azure Arc-fähigen Kubernetes-Cluster als Zielspeicherorte für die Bereitstellung von Instanzen von Azure-Diensten verwenden, z. B.  Ressourcen wie SQL Managed Instance mit Azure Arc-Unterstützung und PostgreSQL Hyperscale mit Azure Arc-Unterstützung. Bei Azure Arc-fähigen Kubernetes-Clustern stellt der benutzerdefinierte Speicherort eine Abstraktion eines Namespace im Cluster dar. Mandanten- oder Clusteradministrator*innen können Anwendungsentwickler*innen oder Datenbankadministrator*innen über die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) Berechtigungen für die Bereitstellung von Ressourcen wie SQL Managed Instance mit Azure Arc-Unterstützung und Instanzen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung sowie Azure-Web-Apps am benutzerdefinierten Speicherort erteilen. 
 
Eine konzeptionelle Übersicht über dieses Feature finden Sie im Artikel [Benutzerdefinierte Speicherorte auf Basis von Azure Arc-fähigen Kubernetes-Clustern](conceptual-custom-locations.md). 

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Aktivieren benutzerdefinierter Speicherorte in Ihrem Azure Arc-fähigen Kubernetes-Cluster
> * Erstellen eines benutzerdefinierten Standorts.


## <a name="prerequisites"></a>Voraussetzungen

- [Installieren oder aktualisieren Sie Azure CLI](/cli/azure/install-azure-cli) auf eine Version >= 2.16.0.

- Installieren Sie die folgenden Azure CLI-Erweiterungen:
    - `connectedk8s` (Version 1.1.0 oder höher)
    - `k8s-extension` (Version 0.2.0 oder höher)
    - `customlocation` (Version 0.1.0 oder höher) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Wenn Sie zuvor die Erweiterungen `connectedk8s`, `k8s-extension` und `customlocation` installiert haben, aktualisieren Sie die neueste Version über den folgenden Befehl:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```
    >[!NOTE]
    >Es wird empfohlen, die neueste Version der CLI-Erweiterungen zu verwenden, um die neuesten Features zu erhalten.  

- Überprüfen Sie die abgeschlossene Anbieterregistrierung für `Microsoft.ExtendedLocation`.
    1. Geben Sie die folgenden Befehle ein:
    
        ```azurecli
        az provider register --namespace Microsoft.ExtendedLocation
        ```

    2. Überwachen Sie den Registrierungsprozess. Die Registrierung kann bis zu 10 Minuten dauern.
    
        ```azurecli
        az provider show -n Microsoft.ExtendedLocation -o table
        ```

        Nach der Registrierung weist der Status `RegistrationState` den Wert `Registered` auf.

- Stellen Sie sicher, dass Sie bereits über einen [verbundenen, Azure Arc-fähigen Kubernetes-Cluster](quickstart-connect-cluster.md) verfügen.
    - [Führen Sie für Ihre Agents ein Upgrade](agent-upgrade.md#manually-upgrade-agents) auf Version 1.1.1.0 oder höher aus.

## <a name="enable-custom-locations-on-cluster"></a>Aktivieren benutzerdefinierter Speicherorte im Cluster

Wenn Sie bei Azure CLI als Azure AD-Benutzer angemeldet sind, führen Sie den folgenden Befehl aus, um dieses Feature in Ihrem Cluster zu aktivieren:

```azurecli
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

Wenn Sie bei Azure CLI über einen Dienstprinzipal angemeldet sind, führen Sie den folgenden Befehl aus, um dieses Feature in Ihrem Cluster zu aktivieren:

1. Rufen Sie die Objekt-ID der Azure AD-Anwendung ab, die vom Azure Arc-Dienst verwendet wird:

    ```azurecli
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Verwenden Sie den `<objectId>`-Wert aus dem obigen Schritt, um das Feature „Benutzerdefinierte Speicherorte“ im Cluster zu aktivieren:

    ```azurecli
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. Das Feature „Benutzerdefinierte Speicherorte“ ist vom Cluster Connect-Feature abhängig. Daher müssen beide Features aktiviert werden, damit „Benutzerdefinierte Speicherorte“ funktioniert.
> 2. `az connectedk8s enable-features` muss auf einem Computer ausgeführt werden, auf dem die `kubeconfig`-Datei auf den Cluster verweist, auf dem die Features aktiviert werden sollen.

## <a name="create-custom-location"></a>Erstellen eines benutzerdefinierten Speicherorts

1. Stellen Sie die Azure-Dienstclustererweiterung der Azure-Dienstinstanz bereit, die Sie in Ihrem Cluster installieren möchten:

    * [Azure Arc-fähige Datendienste](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > Der Proxy für ausgehenden Datenverkehr ohne Authentifizierung und der Proxy für ausgehenden Datenverkehr mit Standardauthentifizierung werden von der Clustererweiterung „Azure Arc-fähige Datendienste“ unterstützt. Proxys für ausgehenden Datenverkehr, von denen Zertifikate erwartet werden, werden aktuell nicht unterstützt.


    * [Azure App Service in Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid in Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

2. Rufen Sie den Azure Resource Manager-Bezeichner des Azure Arc-fähigen Kubernetes-Clusters ab, auf den in späteren Schritten mit `connectedClusterId` verwiesen wird:

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

3. Rufen Sie den Azure Resource Manager-Bezeichner der im Azure Arc-fähigen Kubernetes-Cluster bereitgestellten Clustererweiterung ab, auf den in späteren Schritten mit `extensionId` verwiesen wird:

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

4. Erstellen Sie einen benutzerdefinierten Speicherort, indem Sie auf den Azure Arc-fähigen Kubernetes-Cluster und die Erweiterung verweisen:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
    ```

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name, --n` | Namen des benutzerdefinierten Standorts |
| `--resource-group, --g` | Dies ist die Ressourcengruppe des benutzerdefinierten Speicherorts.  | 
| `--namespace` | Dies ist der Namespace im Cluster, der an den benutzerdefinierten Speicherort gebunden ist, der erstellt wird. |
| `--host-resource-id` | Dies ist der Azure Resource Manager-Bezeichner des Azure Arc-fähigen Kubernetes-Clusters (verbundener Cluster). |
| `--cluster-extension-ids` | Dies sind die Azure Resource Manager-Bezeichner der Clustererweiterungsinstanzen, die in dem verbundenen Cluster installiert sind. Geben Sie eine Liste der Clustererweiterungs-IDs mit Leerzeichen als Trennzeichen an.  |

**Optionale Parameter**

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--assign-identity` | Der Standardwert ist `None`. Wenn der Parameter auf „SystemAssigned“ festgelegt ist, wird eine [systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erstellt. |
| `--location, --l` | Dies ist der benutzerdefinierte Speicherort für die Azure Resource Manager-Ressource in Azure. Standardmäßig ist dieser Parameter auf den Speicherort (oder die Azure-Region) des verbundenen Clusters festgelegt. |
| `--tags` | Dies ist eine Liste von Tags mit Leerzeichen als Trennzeichen: Schlüssel[=Wert] [Schlüssel[=Wert] ...]. Verwenden Sie „''“, um vorhandene Tags zu löschen. |
| `--kubeconfig` | kubeconfig des Administrators bzw. der Administratorin des Clusters. Dies muss als Datei übergeben werden, wenn der Cluster nicht AAD-fähig ist. |


## <a name="show-details-of-a-custom-location"></a>Anzeigen von Details für einen benutzerdefinierten Speicherort

So zeigen Sie Details für einen benutzerdefinierten Speicherort an:

```azurecli
    az customlocation show -n <customLocationName> -g <resourceGroupName> 
```

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name, --n` | Namen des benutzerdefinierten Standorts |
| `--resource-group, --g` | Dies ist die Ressourcengruppe des benutzerdefinierten Speicherorts.  | 

## <a name="list-custom-locations"></a>Auflisten benutzerdefinierter Speicherorte

Listen Sie alle benutzerdefinierten Speicherorte in einer Ressourcengruppe auf.

```azurecli
    az customlocation show -g <resourceGroupName> 
```

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--resource-group, --g` | Dies ist die Ressourcengruppe des benutzerdefinierten Speicherorts.  | 


## <a name="update-a-custom-location"></a>Aktualisieren eines benutzerdefinierten Speicherorts

Verwenden Sie den Befehl `update`, wenn Sie neue Tags hinzufügen oder dem benutzerdefinierten Speicherort neue Clustererweiterungs-IDs zuordnen und dabei vorhandene Tags und zugeordnete Clustererweiterungen beibehalten möchten. `--cluster-extension-ids`, `--tags` und `assign-identity` können aktualisiert werden. 

```azurecli
    az customlocation update -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```
**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name, --n` | Namen des benutzerdefinierten Standorts |
| `--resource-group, --g` | Dies ist die Ressourcengruppe des benutzerdefinierten Speicherorts.  | 
| `--namespace` | Dies ist der Namespace im Cluster, der an den benutzerdefinierten Speicherort gebunden ist, der erstellt wird. |
| `--host-resource-id` | Dies ist der Azure Resource Manager-Bezeichner des Azure Arc-fähigen Kubernetes-Clusters (verbundener Cluster). |

**Optionale Parameter**

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--assign-identity` | Dieser Parameter kann entweder auf `None` oder auf `"SystemAssigned` aktualisiert werden, wenn Sie dem benutzerdefinierten Speicherort eine [systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zuweisen möchten. |
| `--cluster-extension-ids` | Ordnen Sie diesem benutzerdefinierten Speicherort neue Clustererweiterungen zu, indem Sie die Azure Resource Manager-Bezeichner der Clustererweiterungsinstanzen bereitstellen, die in dem verbundenen Cluster installiert sind. Geben Sie eine Liste der Clustererweiterungs-IDs mit Leerzeichen als Trennzeichen an. |
| `--tags` | Mit diesem Parameter werden neue Tags zu den bereits vorhandenen hinzugefügt. Verwenden Sie hierbei eine Liste von Tags mit Leerzeichen als Trennzeichen: Schlüssel[=Wert] [Schlüssel[=Wert] ...]. |

## <a name="patch-a-custom-location"></a>Patchen eines benutzerdefinierten Speicherorts

Verwenden Sie den Befehl `patch`, wenn Sie vorhandene Tags und Clustererweiterungs-IDs durch neue Tags und Clustererweiterungs-IDs ersetzen möchten. `--cluster-extension-ids`, `assign-identity` und `--tags` können gepatcht werden. 

```azurecli
    az customlocation patch -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```

**Erforderliche Parameter**

| Parametername | BESCHREIBUNG |
|----------------|------------|
| `--name, --n` | Namen des benutzerdefinierten Standorts |
| `--resource-group, --g` | Dies ist die Ressourcengruppe des benutzerdefinierten Speicherorts.  | 

**Optionale Parameter**

| Parametername | BESCHREIBUNG |
|--------------|------------|
| `--assign-identity` | Dieser Parameter kann entweder auf `None` oder auf `"SystemAssigned` aktualisiert werden, wenn Sie dem benutzerdefinierten Speicherort eine [systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) zuweisen möchten. |
| `--cluster-extension-ids` | Ordnen Sie diesem benutzerdefinierten Speicherort neue Clustererweiterungen zu, indem Sie die Azure Resource Manager-Bezeichner der Clustererweiterungsinstanzen bereitstellen, die in dem verbundenen Cluster installiert sind. Geben Sie eine Liste der Clustererweiterungs-IDs mit Leerzeichen als Trennzeichen an. |
| `--tags` | Mit diesem Parameter werden neue Tags zu den bereits vorhandenen hinzugefügt. Verwenden Sie hierbei eine Liste von Tags mit Leerzeichen als Trennzeichen: Schlüssel[=Wert] [Schlüssel[=Wert] ...]. |

## <a name="delete-a-custom-location"></a>Löschen eines benutzerdefinierten Speicherorts

 ```azurecli
    az customlocation delete -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
   ```

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie eine sichere Verbindung mit dem Cluster über [Cluster Connect](cluster-connect.md) her.
- Fahren Sie mit [Azure App Service auf Azure Arc](../../app-service/overview-arc-integration.md) fort, um umfassende Anweisungen zum Installieren von Erweiterungen, Erstellen benutzerdefinierter Standorte und Erstellen der App Service-Kubernetes-Umgebung abzurufen. 
- Erstellen Sie ein Event Grid-Thema und ein Ereignisabonnement für [Event Grid in Kubernetes](../../event-grid/kubernetes/overview.md).
- Informieren Sie sich genauer über derzeit verfügbare [Erweiterungen für Azure Arc-fähige Kubernetes-Cluster](extensions.md#currently-available-extensions).
