---
title: 'Schnellstart: Konfigurieren eines regionsübergreifenden Clusters mit Azure Managed Instance for Apache Cassandra'
description: In diesem Schnellstart wird veranschaulicht, wie Sie mit Azure Managed Instance for Apache Cassandra einen regionsübergreifenden Cluster konfigurieren.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fe2ab4a780e07f8d8325c2881d562a379ec16b97
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894391"
---
# <a name="quickstart-create-a-multi-region-cluster-with-azure-managed-instance-for-apache-cassandra"></a>Schnellstart: Erstellen eines regionsübergreifenden Clusters mit Azure Managed Instance for Apache Cassandra

Azure Managed Instance for Apache Cassandra verfügt über automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-basierte Apache Cassandra-Rechenzentren. Dieser Dienst unterstützt Sie dabei, Hybridszenarien zu beschleunigen und die laufende Wartung zu verringern.

In diesem Schnellstart wird veranschaulicht, wie Sie die Azure CLI-Befehle zum Konfigurieren eines regionsübergreifenden Clusters verwenden.  

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Für diesen Artikel ist die Azure CLI-Version 2.30.0 oder höher erforderlich. Wenn Sie Azure Cloud Shell verwenden, ist die neueste Version bereits installiert.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) mit einer Verbindung mit Ihrer selbstgehosteten bzw. lokalen Umgebung. Weitere Informationen zum Herstellen einer Verbindung für lokale Umgebungen mit Azure finden Sie im Artikel [Verbinden eines lokalen Netzwerks mit Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="setting-up-the-network-environment"></a><a id="create-account"></a>Einrichten der Netzwerkumgebung

Da alle mit diesem Dienst bereitgestellten Rechenzentren mithilfe der VNet-Einschleusung in dedizierten Subnetzen bereitgestellt werden müssen, muss vor der Bereitstellung ein geeignetes Netzwerkpeering konfiguriert werden, um sicherzustellen, dass Ihr regionsübergreifender Cluster ordnungsgemäß funktioniert. Sie erstellen einen Cluster mit zwei Rechenzentren in separaten Regionen: „USA, Osten“ und „USA, Osten 2“. Zunächst müssen Sie die Virtual Network-Instanzen für jede Region erstellen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Erstellen Sie eine Ressourcengruppe namens „cassandra-mi-multi-region“:

   ```azurecli-interactive
   az group create -l eastus2 -n cassandra-mi-multi-region
   ```

1. Erstellen Sie das erste VNet in der Region „USA, Osten 2“ mit einem dedizierten Subnetz:

   ```azurecli-interactive
   az network vnet create \
     -n vnetEastUs2 \
     -l eastus2 \
     -g cassandra-mi-multi-region \
     --address-prefix 10.0.0.0/16 \
     --subnet-name dedicated-subnet
   ```

1. Erstellen Sie nun das zweite VNet in „USA, Osten“, ebenfalls mit einem dedizierten Subnetz:

   ```azurecli-interactive
    az network vnet create \
      -n vnetEastUs \
      -l eastus \
      -g cassandra-mi-multi-region \
      --address-prefix 192.168.0.0/16 \
      --subnet-name dedicated-subnet
   ```

   > [!NOTE]
   > Fügen Sie explizit verschiedene IP-Adressbereiche hinzu, um sicherzustellen, dass beim Peering keine Fehler auftreten. 

1. Nun müssen Sie ein Peering zwischen dem ersten VNet und dem zweiten VNet erstellen:

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet1ToMyVnet2 \
     --vnet-name vnetEastUs2 \
     --remote-vnet vnetEastUs \
     --allow-vnet-access \
     --allow-forwarded-traffic
   ```

1. Erstellen Sie ein weiteres Peering zwischen dem zweiten und dem ersten VNet, um die beiden VNets zu verbinden:

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet2ToMyVnet1 \
     --vnet-name vnetEastUs \
     --remote-vnet vnetEastUs2 \
     --allow-vnet-access \
     --allow-forwarded-traffic  
   ```

   > [!NOTE]
   > Wenn weitere Regionen hinzugefügt werden, erfordert jedes VNet Peering von ihm zu allen anderen VNets und von allen anderen VNets zu diesem VNet. 

1. Überprüfen Sie die Ausgabe des vorherigen Befehls, und stellen Sie sicher, dass der Wert von „peeringState“ jetzt „Verbunden“ lautet. Dies können Sie auch durch Ausführen des folgenden Befehls überprüfen:

   ```azurecli-interactive
   az network vnet peering show \
     --name MyVnet1ToMyVnet2 \
     --resource-group cassandra-mi-multi-region \
     --vnet-name vnetEastUs2 \
     --query peeringState
   ``` 

1. Wenden Sie als Nächstes einige spezielle Berechtigungen auf beide Virtual Network-Instanzen an, die für Azure Managed Instance for Apache Cassandra erforderlich sind. Führen Sie Folgendes aus, und ersetzen Sie `<SubscriptionID>` durch Ihre Abonnement-ID:

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2

   az role assignment create     \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs
    ```
   > [!NOTE]
   > Die Werte `assignee` und `role` im vorherigen Befehl sind feste Werte. Geben Sie diese Werte daher genau so ein, wie im Befehl angegeben. Andernfalls treten beim Erstellen des Clusters Fehler auf. Treten beim Ausführen dieses Befehls Fehler auf, verfügen Sie möglicherweise nicht über die Berechtigungen zum Ausführen des Befehls. Wenden Sie sich an Ihren Administrator, um die entsprechenden Berechtigungen zu erhalten.

## <a name="create-a-multi-region-cluster"></a><a id="create-account"></a>Erstellen eines regionsübergreifenden Clusters

1. Nachdem Sie nun über das entsprechende Netzwerk verfügen, können Sie die Clusterressource bereitstellen. (Ersetzen Sie `<Subscription ID>` durch Ihre Abonnement-ID.) Dies kann 5 bis 10 Minuten dauern:

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
   initialCassandraAdminPassword='myPassword'
        
    az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Wenn die Clusterressource erstellt wurde, können Sie ein Rechenzentrum erstellen. Erstellen Sie zunächst ein Rechenzentrum in „USA, Osten 2“. (Ersetzen Sie `<SubscriptionID>` durch Ihre Abonnement-ID.) Der Vorgang kann bis zu zehn Minuten dauern:

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus2'
   dataCenterLocation='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 3
   ```

1. Erstellen Sie dann ein Rechenzentrum in „USA, Osten“. (Ersetzen Sie `<SubscriptionID>` durch Ihre Abonnement-ID.)

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus'
   dataCenterLocation='eastus'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Nachdem das zweite Rechenzentrum erstellt wurde, rufen Sie den Knotenstatus ab, um zu überprüfen, ob alle Cassandra-Knoten erfolgreich eingerichtet wurden:

    ```azurecli-interactive
    resourceGroupName='cassandra-mi-multi-region'
    clusterName='test-multi-region'
    
    az managed-cassandra cluster node-status \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName
    ```

1. Abschließend [stellen Sie eine Clusterverbindung her](create-cluster-cli.md#connect-to-your-cluster). Verwenden Sie dazu CQLSH und die folgende CQL-Abfrage, um die Replikationsstrategie in den einzelnen Keyspaces so zu aktualisieren, dass alle Rechenzentren des gesamten Clusters eingebunden sind:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3};
   ```
   Darüber hinaus müssen Sie auch die Kennworttabellen aktualisieren:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3}
   ```

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Anwenden von Berechtigungen für Ihre Virtual Network-Instanz mithilfe der Azure CLI ein Fehler mit dem Hinweis auftritt, dass *der Benutzer oder Dienstprinzipal in der Graphdatenbank für „e5007d2c-4b13-4a74-9b6a-605d99f03501“ nicht gefunden werden kann*, können Sie die gleiche Berechtigung manuell über das Azure-Portal anwenden. Informationen zur Vorgehensweise finden Sie [hier](add-service-principal.md).

> [!NOTE] 
> Die Azure Cosmos DB-Rollenzuweisung wird nur für Bereitstellungszwecke verwendet. Azure Managed Instance for Apache Cassandra verfügt über keine Back-End-Abhängigkeiten von Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diesen Managed Instance-Cluster nicht mehr benötigen, löschen Sie ihn wie folgt:

1. Wählen Sie im linken Menü des Azure-Portals die Option **Ressourcengruppen** aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie für diesen Schnellstart erstellt haben.
1. Wählen Sie im Ressourcengruppenbereich **Übersicht** die Option **Ressourcengruppe löschen** aus.
1. Geben Sie in dem nächsten Fenster den Namen der zu löschenden Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart wurde beschrieben, wie Sie mit der Azure CLI und Azure Managed Instance for Apache Cassandra einen regionsübergreifenden Cluster erstellen. Sie können nun mit der Nutzung des Clusters beginnen.

> [!div class="nextstepaction"]
> [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
