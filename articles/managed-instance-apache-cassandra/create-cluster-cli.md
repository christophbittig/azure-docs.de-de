---
title: 'Schnellstart: Erstellen eines Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ mithilfe der CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI einen Cluster vom Typ „Azure Managed Instance for Apache Cassandra“ erstellen.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 795ad31b83f3a6f1acdd8bcc7561e6d75556b311
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892417"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli"></a>Schnellstart: Erstellen eines Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ mithilfe der Azure CLI

Azure Managed Instance for Apache Cassandra verfügt über automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-basierte Apache Cassandra-Rechenzentren. Dieser Dienst unterstützt Sie dabei, Hybridszenarien zu beschleunigen und die laufende Wartung zu verringern.

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI einen Cluster mit Azure Managed Instance for Apache Cassandra erstellen. Außerdem wird gezeigt, wie Sie ein Rechenzentrum erstellen und Knoten im Rechenzentrum hoch- oder herunterskalieren.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) mit einer Verbindung mit Ihrer selbstgehosteten bzw. lokalen Umgebung. Weitere Informationen zum Herstellen einer Verbindung für lokale Umgebungen mit Azure finden Sie im Artikel [Verbinden eines lokalen Netzwerks mit Azure](/azure/architecture/reference-architectures/hybrid-networking/).

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Für diesen Artikel ist die Azure CLI-Version 2.30.0 oder höher erforderlich. Wenn Sie Azure Cloud Shell verwenden, ist die neueste Version bereits installiert.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Erstellen eines Managed Instance-Clusters

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

1. Legen Sie Ihre Abonnement-ID über die Azure CLI fest:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Erstellen Sie als Nächstes ein virtuelles Netzwerk mit einem dedizierten Subnetz in Ihrer Ressourcengruppe:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > Für die Bereitstellung einer Instanz von Azure Managed Instance for Apache Cassandra ist Internetzugriff erforderlich. In Umgebungen, in denen der Internetzugriff eingeschränkt ist, tritt ein Fehler bei der Bereitstellung auf. Stellen Sie sicher, dass der Zugriff im VNet auf die folgenden wichtigen Azure-Dienste, die für die richtige Funktionsweise von Managed Cassandra erforderlich sind, nicht blockiert ist:
   > - Azure Storage
   > - Azure Key Vault
   > - Skalierungsgruppen für virtuelle Azure-Computer
   > - Azure-Überwachung
   > - Azure Active Directory
   > - Azure Security

1. Wenden Sie einige spezielle Berechtigungen auf das virtuelle Netzwerk an, die für die verwaltete Instanz erforderlich sind. Verwenden Sie den Befehl `az role assignment create`, und ersetzen Sie `<subscriptionID>`, `<resourceGroupName>` und `<vnetName>` durch die entsprechenden Werte:

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > Die Werte `assignee` und `role` im vorherigen Befehl sind feste Werte. Geben Sie diese Werte daher genau so ein, wie im Befehl angegeben. Andernfalls treten beim Erstellen des Clusters Fehler auf. Treten beim Ausführen dieses Befehls Fehler auf, verfügen Sie möglicherweise nicht über die Berechtigungen zum Ausführen des Befehls. Wenden Sie sich an Ihren Administrator, um die entsprechenden Berechtigungen zu erhalten.

1. Erstellen Sie als Nächstes mithilfe des Befehls [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) den Cluster in dem neu erstellten virtuellen Netzwerk. Führen Sie den folgenden Befehl mit dem Wert der Variablen `delegatedManagementSubnetId` aus:

   > [!NOTE]
   > Der Wert der von Ihnen unten angegebenen Variablen `delegatedManagementSubnetId` stimmt exakt mit dem Wert von `--scope` überein, den Sie im obigen Befehl angegeben haben:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. Erstellen Sie schließlich ein Rechenzentrum für den Cluster mit drei Knoten, der VM-SKU „Standard D8s v4“ und vier angefügten P30-Datenträgern für jeden Knoten. Verwenden Sie dazu den Befehl [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create):

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   virtualMachineSKU='Standard_D8s_v4'
   noOfDisksPerNode=4
     
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 \
     --sku $virtualMachineSKU \
     --disk-capacity $noOfDisksPerNode \
     --availability-zone false
   ```

   > [!NOTE]
   > Der Wert für `--sku` kann aus den folgenden verfügbaren SKUs ausgewählt werden:
   >
   > - Standard_E8s_v4
   > - Standard_E16s_v4 
   > - Standard_E20s_v4
   > - Standard_E32s_v4 
   > - Standard_DS13_v2
   > - Standard_DS14_v2
   > - Standard_D8s_v4
   > - Standard_D16s_v4
   > - Standard_D32s_v4 
   > 
   > Beachten Sie auch, dass `--availability-zone` auf `false` festgelegt ist. Legen Sie diesen Wert auf `true` fest, um Verfügbarkeitszonen zu aktivieren. 

   > [!WARNING]
   > Verfügbarkeitszonen werden nicht in allen Regionen unterstützt. Bereitstellungen sind nicht erfolgreich, wenn Sie eine Region auswählen, in der Verfügbarkeitszonen nicht unterstützt werden. Informationen zu den unterstützten Regionen finden Sie [hier](/azure/availability-zones/az-overview#azure-regions-with-availability-zones). Die erfolgreiche Bereitstellung von Verfügbarkeitszonen unterliegt auch der Verfügbarkeit von Computeressourcen in allen Zonen in der angegebenen Region. Bei Bereitstellungen kann ein Fehler auftreten, wenn die von Ihnen ausgewählte SKU oder Kapazität nicht in allen Zonen verfügbar ist. 

1. Die Knoten des erstellten Rechenzentrums können bei Bedarf mithilfe des Befehls [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) hoch- oder herunterskaliert werden. Ändern Sie den Wert des Parameters `node-count` in den gewünschten Wert:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Herstellen einer Clusterverbindung

Von Azure Managed Instance for Apache Cassandra werden keine Knoten mit öffentlicher IP-Adresse erstellt. Wenn Sie eine Verbindung mit dem neu erstellten Cassandra-Cluster herstellen möchten, müssen Sie eine weitere Ressource innerhalb des virtuellen Netzwerks erstellen. Bei dieser Ressource kann es sich um eine Anwendung handeln oder um einen virtuellen Computer, auf dem das Open-Source-Abfragetool [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) von Apache installiert ist. Sie können eine [Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/vm-simple-linux/) verwenden, um einen virtuellen Ubuntu-Computer bereitzustellen. Stellen Sie nach dessen Bereitstellung eine SSH-Verbindung mit dem Computer her, und installieren Sie CQLSH, wie in den folgenden Befehlen gezeigt:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Anwenden von Berechtigungen für Ihre Virtual Network-Instanz mithilfe der Azure CLI ein Fehler mit dem Hinweis auftritt, dass *der Benutzer oder Dienstprinzipal in der Graphdatenbank für „e5007d2c-4b13-4a74-9b6a-605d99f03501“ nicht gefunden werden kann*, können Sie die gleiche Berechtigung manuell über das Azure-Portal anwenden. Informationen zur Vorgehensweise finden Sie [hier](add-service-principal.md).

> [!NOTE] 
> Die Azure Cosmos DB-Rollenzuweisung wird nur für Bereitstellungszwecke verwendet. Azure Managed Instance for Apache Cassandra verfügt über keine Back-End-Abhängigkeiten von Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl `az group delete` aus, um die Ressourcengruppe, die verwaltete Instanz und alle zugehörigen Ressourcen zu löschen:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe der Azure CLI einen Cluster vom Typ „Azure Managed Instance for Apache Cassandra“ erstellen. Nun können Sie mit der Nutzung des Clusters beginnen:

> [!div class="nextstepaction"]
> [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
