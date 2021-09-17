---
title: Erstellen und Verwalten benutzerdefinierter Standorte in Kubernetes mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Verwenden benutzerdefinierter Speicherorte zum Bereitstellen von Azure-PaaS-Diensten in Kubernetes-Clustern mit Azure Arc-Unterstützung
ms.openlocfilehash: a4586f6f527bd98f0f347e51c787f2bcda7c6d8d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768301"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Erstellen und Verwalten benutzerdefinierter Standorte in Kubernetes mit Azure Arc-Unterstützung

Als Azure-Speicherorterweiterung bietet *Benutzerdefinierte Speicherorte* Mandantenadministratoren eine Möglichkeit, ihre Kubernetes-Cluster mit Azure Arc-Unterstützung als Zielspeicherorte für die Bereitstellung von Instanzen von Azure-Diensten zu verwenden. Beispiele für Azure-Ressourcen sind u. a. SQL Managed Instance mit Azure Arc-Unterstützung und PostgreSQL Hyperscale mit Azure Arc-Unterstützung.

Ähnlich wie bei Azure-Speicherorten können Endbenutzer im Mandanten mit Zugriff auf „Benutzerdefinierte Speicherorte“ dort Ressourcen bereitstellen, indem sie das private Compute Ihres Unternehmens verwenden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Sie aktivieren benutzerdefinierte Standorte in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung.
> * Sie stellen die Azure-Dienstclustererweiterung der Azure-Dienstinstanz für Ihren Cluster bereit.
> * Erstellen Sie einen benutzerdefinierten Speicherort in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung.

Eine konzeptionelle Übersicht zu diesem Feature finden Sie im Artikel [„Benutzerdefinierte Speicherorte“ auf Basis von Kubernetes mit Azure Arc-Unterstützung](conceptual-custom-locations.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

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

- Überprüfen Sie, ob Sie über einen [verbundenen, Azure Arc-fähigen Kubernetes-Cluster](quickstart-connect-cluster.md) verfügen.
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

1. Stellen Sie die Azure-Dienstclusterinstanz der Azure-Dienstinstanz bereit, die letztendlich auf Ihrem Cluster vorhanden sein soll.

    * [Azure Arc-fähige Datendienste](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > Der Proxy für ausgehenden Datenverkehr ohne Authentifizierung und der Proxy für ausgehenden Datenverkehr mit Standardauthentifizierung werden von der Data Services-Clustererweiterung mit Arc-Unterstützung aktiviert. Proxys für ausgehenden Datenverkehr, von denen Zertifikate erwartet werden, werden aktuell nicht unterstützt.


    * [Azure App Service in Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid in Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

1. Rufen Sie den Azure Resource Manager-Bezeichner des Kubernetes-Clusters mit Azure Arc-Unterstützung ab, auf den in späteren Schritten als `connectedClusterId` verwiesen wird:

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Rufen Sie den Azure Resource Manager-Bezeichner der auf dem Kubernetes-Clusters mit Azure Arc-Unterstützung bereitgestellten Clustererweiterung ab, auf den in späteren Schritten als `extensionId` verwiesen wird:

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Erstellen Sie einen benutzerdefinierten Speicherort, indem Sie auf den Kubernetes-Clusters mit Azure Arc-Unterstützung und die Erweiterung verweisen:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie eine sichere Verbindung mit dem Cluster über [Cluster Connect](cluster-connect.md) her.
- Fahren Sie mit [Azure App Service auf Azure Arc](../../app-service/overview-arc-integration.md) fort, um umfassende Anweisungen zum Installieren von Erweiterungen, Erstellen benutzerdefinierter Standorte und Erstellen der App Service-Kubernetes-Umgebung abzurufen. 
- Erstellen Sie ein Event Grid-Thema und ein Ereignisabonnement für [Event Grid in Kubernetes](../../event-grid/kubernetes/overview.md).
- In den derzeit verfügbaren [Azure Arc-fähigen Kubernetes-Erweiterungen](extensions.md#currently-available-extensions) finden Sie weitere Details.