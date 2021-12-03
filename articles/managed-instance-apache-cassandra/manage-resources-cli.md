---
title: Verwalten von Ressourcen mit der Azure CLI – Azure Resource Manager | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die allgemeinen Befehle zum Automatisieren der Verwaltung von Azure SQL Managed Instance für Apache Cassandra mithilfe der Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: devx-track-azurecli, seo-azure-cli, ignite-fall-2021
keywords: Befehlszeilenschnittstelle von Azure Resource Manager
ms.openlocfilehash: 1cb28336c27a6f470a305917a267fc9c9320620a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892461"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli"></a>Verwalten von Azure SQL Managed Instance für Apache Cassandra-Ressourcen mithilfe der Azure CLI

In diesem Artikel werden allgemeine Befehle zum Automatisieren der Verwaltung von Azure SQL Managed Instance für Apache Cassandra-Cluster mithilfe der Azure CLI beschrieben.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Für diesen Artikel ist die Azure CLI-Version 2.30.0 oder höher erforderlich. Wenn Sie Azure Cloud Shell verwenden, ist die neueste Version bereits installiert.
>
> Ressourcen für die Verwaltung von Azure SQL Managed Instance für Apache Cassandra können nicht umbenannt werden, da dies gegen die Funktionsweise von Azure Resource Manager mit Ressourcen-URIs verstößt.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure SQL Managed Instance für Apache Cassandra-Cluster

In den folgenden Abschnitten wird die Verwaltung von Azure SQL Managed Instance für Apache Cluster veranschaulicht, einschließlich der folgenden Szenarios:

* [Erstellen eines Managed Instance-Clusters](#create-cluster)
* [Löschen eines Managed Instance-Clusters](#delete-cluster)
* [Abrufen der Clusterdetails](#get-cluster-details)
* [Abrufen des Clusterknotenstatus](#get-cluster-status)
* [Auflisten von Clustern nach Ressourcengruppe](#list-clusters-resource-group)
* [Auflisten von Clustern nach Abonnement-ID](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Erstellen eines Managed Instance-Clusters

Erstellen Sie einen Azure Managed Instance for Apache Cassandra-Cluster mithilfe des Befehls [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Löschen eines Managed Instance-Clusters

Löschen Sie einen Cluster mithilfe des Befehls [az managed-cassandra cluster delete](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Abrufen der Clusterdetails

Rufen Sie Clusterdetails mithilfe des Befehls [az managed-cassandra cluster show](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show) ab:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Abrufen des Clusterknotenstatus

Rufen Sie den Clusterknotenstatus mithilfe des Befehls [az managed-cassandra cluster node-status](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) ab:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Auflisten von Clustern nach Ressourcengruppe

Listen Sie Cluster mithilfe des Befehls [az managed-cassandra cluster list](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) nach Ressourcengruppe auf:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Auflisten von Clustern nach Abonnement-ID

Listen Sie Cluster mithilfe des Befehls [az managed-cassandra cluster list](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) nach Abonnement-ID auf:

```azurecli-interactive
# set your subscription id
az account set -s <subscriptionID>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Rechenzentren für Azure SQL Managed Instance

In den folgenden Abschnitten wird die Verwaltung von Azure SQL Managed Instance für Apache Cassandra-Rechenzentren veranschaulicht, einschließlich der folgenden Szenarios:

* [Erstellen eines Rechenzentrums](#create-datacenter)
* [Löschen eines Rechenzentrums](#delete-datacenter)
* [Abrufen von Details zu Rechenzentren](#get-datacenter-details)
* [Abrufen von Rechenzentren in einem Cluster](#get-datacenters-cluster)
* [Aktualisieren oder Skalieren eines Rechenzentrums](#update-datacenter)
* [Aktualisieren der Cassandra-Konfiguration](#update-yaml)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Erstellen eines Rechenzentrums

Erstellen Sie ein Rechenzentrum mithilfe des Befehls [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Löschen eines Rechenzentrums

Löschen Sie ein Rechenzentrum mithilfe des Befehls [az managed-cassandra datacenter delete](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

> [!WARNING]
> Wenn Sie in Ihrem Cluster über mehrere Rechenzentren verfügen, müssen Sie zuerst alle Verweise auf das Rechenzentrum entfernen, das Sie in allen [Einstellungen für die Keyspace-Replikationsstrategie](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/operations/opsChangeKSStrategy.html) löschen möchten. Bei diesem Befehl tritt ein Fehler auf, wenn es noch Verweise auf das Rechenzentrum in einem der Keyspaces innerhalb Ihres Clusters gibt. 

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Abrufen von Details zu Rechenzentren

Rufen Sie Details zu einem Rechenzentrum mithilfe des Befehls [az managed-cassandra datacenter show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) ab:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Aktualisieren oder Skalieren eines Rechenzentrums

Aktualisieren oder skalieren Sie ein Rechenzentrum (zum Skalieren des nodeCount-Werts für Änderungen) mithilfe des Befehls [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="update-cassandra-configuration"></a><a id="update-yaml"></a>Aktualisieren der Cassandra-Konfiguration

Ändern Sie die Cassandra-Konfiguration in einem Rechenzentrum mithilfe des Befehls [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update). Sie müssen eine Base64-Codierung für das YAML-Fragment mithilfe eines [Onlinetools](https://www.base64encode.org/) durchführen. Die folgenden YAML-Einstellungen werden unterstützt:

- column_index_size_in_kb
- compaction_throughput_mb_per_sec
- read_request_timeout_in_ms
- range_request_timeout_in_ms
- aggregated_request_timeout_in_ms
- write_request_timeout_in_ms
- internode_compression
- batchlog_replay_throttle_in_kb

Sehen Sie sich als Beispiel das folgende YAML-Fragment an:

```yaml
column_index_size_in_kb: 16
read_request_timeout_in_ms: 10000
```

Wenn die YAML-Datei codiert wird, wird sie in `Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA==` konvertiert. 

Siehe unten:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
yamlFragment='Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA=='

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --base64-encoded-cassandra-yaml-fragment $yamlFragment
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Abrufen der Rechenzentren in einem Cluster

Rufen Sie die Rechenzentren in einem Cluster mithilfe des Befehls [az managed-cassandra datacenter list](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list) ab:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure SQL Managed Instance-Clusters über das Azure-Portal](create-cluster-portal.md)
* [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
