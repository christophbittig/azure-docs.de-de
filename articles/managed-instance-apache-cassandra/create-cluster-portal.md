---
title: 'Schnellstart: Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals eine Azure Managed Instance-Instanz für einen Apache Cassandra-Cluster erstellen.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8c266f1fc338ca35b05730d9f737a836bdf8949d
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893137"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Managed Instance for Apache Cassandra-Clusters im Azure-Portal

Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-Rechenzentren in Apache Cassandra und ermöglicht so schnellere Hybridszenarios und einen geringeren kontinuierlichen Wartungsaufwand.

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals eine Azure Managed Instance-Instanz für einen Apache Cassandra-Cluster erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Erstellen eines Managed Instance-Clusters

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie in der Suchleiste nach **Managed Instance for Apache Cassandra**, und wählen Sie das Ergebnis aus.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Suchen nach „Managed Instance for Apache Cassandra“." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Wählen Sie die Schaltfläche **Managed Instance for Apache Cassandra-Cluster erstellen** aus.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Erstellen des Clusters." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Geben Sie im Bereich **Managed Instance for Apache Cassandra-Cluster erstellen** folgende Details ein:

   * Wählen Sie in der Dropdownliste unter **Abonnement** Ihr Azure-Abonnement aus.
   * Geben Sie für **Ressourcengruppe** an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden möchten. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Weitere Informationen finden Sie im Übersichtsartikel [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md).
   * **Clustername**: Geben Sie einen Namen für den Cluster ein.
   * **Speicherort**: Der Speicherort, an dem der Cluster bereitgestellt wird.
   * **Ursprüngliches Cassandra-Administratorkennwort**: Das Kennwort, das zum Erstellen des Clusters verwendet wird.
   * **Cassandra-Administratorkennwort bestätigen**: Geben Sie Ihr Kennwort erneut ein.
   * **Virtuelles Netzwerk**: Wählen Sie ein vorhandenes virtuelles Netzwerk und ein Subnetz aus, oder erstellen Sie ein neues. 
   * **Rollen zuweisen**: Für virtuelle Netzwerke sind besondere Berechtigungen erforderlich, damit verwaltete Cassandra-Cluster bereitgestellt werden können. Lassen Sie dieses Kontrollkästchen aktiviert, wenn Sie ein neues virtuelles Netzwerk erstellen oder ein vorhandenes virtuelles Netzwerk ohne angewendete Berechtigungen verwenden. Wenn Sie ein virtuelles Netzwerk verwenden, in dem Sie bereits Managed Instance-Cassandra-Cluster bereitgestellt haben, deaktivieren Sie diese Option.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Ausfüllen des Formulars zum Erstellen des Clusters." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

   > [!NOTE]
   > Für die Bereitstellung einer Instanz von Azure Managed Instance for Apache Cassandra ist Internetzugriff erforderlich. In Umgebungen, in denen der Internetzugriff eingeschränkt ist, tritt ein Fehler bei der Bereitstellung auf. Stellen Sie sicher, dass der Zugriff im VNet auf die folgenden wichtigen Azure-Dienste, die für die richtige Funktionsweise von Managed Cassandra erforderlich sind, nicht blockiert ist. Ausführlichere Informationen finden Sie unter [Erforderliche Netzwerkregeln für ausgehenden Datenverkehr](network-rules.md).
   > - Azure Storage
   > - Azure Key Vault
   > - Skalierungsgruppen für virtuelle Azure-Computer
   > - Azure-Überwachung
   > - Azure Active Directory
   > - Azure Security

1. Wählen Sie als Nächstes die Registerkarte **Rechenzentrum** aus.

1. Geben Sie die folgenden Details ein:

   * **Datacenter name** (Name des Rechenzentrums): Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.
   * **Verfügbarkeitszone**: Aktivieren Sie dieses Kontrollkästchen, wenn Verfügbarkeitszonen aktiviert werden sollen.
   * **SKU-Größe**: Wählen Sie eine der verfügbaren VM-SKU-Größen aus.
   * **No. of disks** (Anzahl von Datenträgern):Wählen Sie die Anzahl der P30-Datenträger aus, die an jeden Cassandra-Knoten angefügt werden sollen.
   * **Anzahl der Knoten**: Wählen Sie die Anzahl der Cassandra-Knoten aus, die in diesem Rechenzentrum bereitgestellt werden.

   :::image type="content" source="./media/create-cluster-portal/create-datacenter-page.png" alt-text="Überprüfen der Zusammenfassung, um das Rechenzentrum zu erstellen" lightbox="./media/create-cluster-portal/create-datacenter-page.png" border="true":::

   > [!WARNING]
   > Verfügbarkeitszonen werden nicht in allen Regionen unterstützt. Bereitstellungen sind nicht erfolgreich, wenn Sie eine Region auswählen, in der Verfügbarkeitszonen nicht unterstützt werden. Informationen zu den unterstützten Regionen finden Sie [hier](/azure/availability-zones/az-overview#azure-regions-with-availability-zones). Die erfolgreiche Bereitstellung von Verfügbarkeitszonen unterliegt auch der Verfügbarkeit von Computeressourcen in allen Zonen in der angegebenen Region. Bei Bereitstellungen kann ein Fehler auftreten, wenn die von Ihnen ausgewählte SKU oder Kapazität nicht in allen Zonen verfügbar ist. 

1. Klicken Sie als Nächstes auf **Überprüfen und erstellen** > **Erstellen**.

   > [!NOTE]
   > Es kann bis zu 15 Minuten dauern, bis der Cluster erstellt ist.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Überprüfen der Zusammenfassung, um den Cluster zu erstellen." lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. Nachdem die Bereitstellung abgeschlossen ist, aktivieren Sie Ihre Ressourcengruppe, um den neu erstellten Managed Instance-Cluster anzuzeigen:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Übersichtsseite nach dem Erstellen des Clusters." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Navigieren Sie zum Durchsuchen der Clusterknoten zur Clusterressource, und öffnen Sie den Bereich **Rechenzentrum**, um sie anzuzeigen:

   :::image type="content" source="./media/create-cluster-portal/datacenter-1.png" alt-text="Anzeigen von Rechenzentrumsknoten" lightbox="./media/create-cluster-portal/datacenter-1.png" border="true":::

<!-- ## <a id="create-account"></a>Add a datacenter

1. To add another datacenter, click the add button in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/add-datacenter.png" alt-text="Click on add datacenter." lightbox="./media/create-cluster-portal/add-datacenter.png" border="true":::

   > [!WARNING]
   > If you are adding a datacenter in a different region, you will need to select a different virtual network. You will also need to ensure that this virtual network has connectivity to the primary region's virtual network created above (and any other virtual networks that are hosting datacenters within the managed instance cluster). Take a look at [this article](/azure/virtual-network/tutorial-connect-virtual-networks-portal#peer-virtual-networks) to learn how to peer virtual networks using Azure portal. You also need to make sure you have applied the appropriate role to your virtual network before attempting to deploy a managed instance cluster, using the below CLI command.
   >
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Fill in the appropriate fields:

   * **Datacenter name** - From the drop-down, select your Azure subscription.
   * **Availability zone** - Check this box if you want availability zones to be enabled in this datacenter.
   * **Location** - Location where your datacenter will be deployed to.
   * **SKU Size** - Choose from the available Virtual Machine SKU sizes.
   * **No. of disks** - Choose the number of p30 disks to be attached to each Cassandra node.
   * **SKU Size** - Choose the number of Cassandra nodes that will be deployed to this datacenter.
   * **Virtual Network** - Select an Exiting Virtual Network and Subnet.  

   :::image type="content" source="./media/create-cluster-portal/add-datacenter-2.png" alt-text="Add Datacenter." lightbox="./media/create-cluster-portal/add-datacenter-2.png" border="true":::

   > [!WARNING]
   > Notice that we do not allow creation of a new virtual network when adding a datacenter. You need to choose an existing virtual network, and as mentioned above, you need to ensure there is connectivity between the target subnets where datacenters will be deployed. You also need to apply the appropriate role to the VNet to allow deployment (see above).

1. When the datacenter is deployed, you should be able to view all datacenter information in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/multi-datacenter.png" alt-text="View the cluster resources." lightbox="./media/create-cluster-portal/multi-datacenter.png" border="true":::

## Troubleshooting

If you encounter an error when applying permissions to your Virtual Network using Azure CLI, such as *Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, you can apply the same permission manually from the Azure portal. Learn how to do this [here](add-service-principal.md).

> [!NOTE] 
> The Azure Cosmos DB role assignment is used for deployment purposes only. Azure Managed Instanced for Apache Cassandra has no backend dependencies on Azure Cosmos DB.   -->

## <a name="connecting-to-your-cluster"></a>Herstellen einer Verbindung mit Ihrem Cluster

Azure Managed Instance for Apache Cassandra erstellt keine Knoten mit öffentlichen IP-Adressen. Um also eine Verbindung mit dem neu erstellten Cassandra-Cluster herzustellen, müssen Sie eine weitere Ressource im VNET erstellen. Dabei kann es sich um eine Anwendung handeln oder einen virtuellen Computer, auf dem das Open-Source-Abfragetool [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) von Apache installiert ist. Sie können eine [Vorlage](https://azure.microsoft.com/resources/templates/vm-simple-linux/) verwenden, um einen virtuellen Ubuntu-Computer bereitzustellen. Stellen Sie nach der Bereitstellung mithilfe von SSH eine Verbindung mit dem Computer her, und installieren Sie CQLSH mithilfe der folgenden Befehle:

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
initial_admin_password="Password provided when creating the cluster"
cqlsh $host 9042 -u cassandra -p $initial_admin_password --ssl
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diesen Managed Instance-Cluster nicht mehr benötigen, löschen Sie ihn wie folgt:

1. Wählen Sie im linken Menü des Azure-Portals die Option **Ressourcengruppen** aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie für diesen Schnellstart erstellt haben.
1. Wählen Sie im Ressourcengruppenbereich **Übersicht** die Option **Ressourcengruppe löschen** aus.
1. Geben Sie in dem nächsten Fenster den Namen der zu löschenden Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mithilfe des Azure-Portals eine Azure Managed Instance-Instanz für einen Apache Cassandra-Cluster erstellen. Sie können jetzt beginnen, mit dem Cluster zu arbeiten.

> [!div class="nextstepaction"]
> [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
