---
title: 'Schnellstart: Konfigurieren eines Hybridclusters mit Azure Managed Instance for Apache Cassandra'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit Azure Managed Instance for Apache Cassandra einen Hybridcluster konfigurieren.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 31b6f0459f2fb9b04646667abfda039760e5fcc0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892436"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra"></a>Schnellstart: Konfigurieren eines Hybridclusters mit Azure Managed Instance for Apache Cassandra

Azure Managed Instance for Apache Cassandra verfügt über automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-basierte Apache Cassandra-Rechenzentren. Dieser Dienst unterstützt Sie dabei, Hybridszenarien zu beschleunigen und die laufende Wartung zu verringern.

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie die Azure CLI-Befehle zum Konfigurieren eines Hybridclusters verwenden. Wenn Sie über vorhandene Rechenzentren in einer lokalen oder selbstgehosteten Umgebung verfügen, können Sie diesem Cluster mit Azure Managed Instance for Apache Cassandra weitere Rechenzentren hinzufügen und diese verwalten.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Für diesen Artikel ist die Azure CLI-Version 2.30.0 oder höher erforderlich. Wenn Sie Azure Cloud Shell verwenden, ist die neueste Version bereits installiert.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) mit einer Verbindung mit Ihrer selbstgehosteten bzw. lokalen Umgebung. Weitere Informationen zum Herstellen einer Verbindung für lokale Umgebungen mit Azure finden Sie im Artikel [Verbinden eines lokalen Netzwerks mit Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurieren eines Hybridclusters

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrer VNET-Ressource.

1. Öffnen Sie die Registerkarte **Subnetze**, und erstellen Sie ein neues Subnetz. Weitere Informationen zu den Feldern des Formulars **Subnetz hinzufügen** finden Sie im Artikel zu [virtuellen Netzwerken](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet):

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Hinzufügen eines neuen Subnetzes zu Ihrem virtuellen Netzwerk" lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

   > [!NOTE]
   > Für die Bereitstellung einer Instanz von Azure Managed Instance for Apache Cassandra ist Internetzugriff erforderlich. In Umgebungen, in denen der Internetzugriff eingeschränkt ist, tritt ein Fehler bei der Bereitstellung auf. Stellen Sie sicher, dass der Zugriff im VNet auf die folgenden wichtigen Azure-Dienste, die für die richtige Funktionsweise von Managed Cassandra erforderlich sind, nicht blockiert ist. Eine umfassende Liste der IP-Adressen und Portabhängigkeiten finden Sie [hier](network-rules.md). 
   > - Azure Storage
   > - Azure Key Vault
   > - Skalierungsgruppen für virtuelle Azure-Computer
   > - Azure-Überwachung
   > - Azure Active Directory
   > - Azure Security

1. Als Nächstes wenden wir über die Azure CLI einige spezielle Berechtigungen auf das VNET und das Subnetz an, die für die Cassandra Managed Instance benötigt werden. Verwenden Sie den Befehl `az role assignment create`, und ersetzen Sie `<subscriptionID>`, `<resourceGroupName>` und `<vnetName>` durch die entsprechenden Werte:

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > Die Werte `assignee` und `role` im vorherigen Befehl sind feste Dienstprinzipal- bzw. Rollenbezeichner.

1. Als Nächstes konfigurieren wir Ressourcen für unseren Hybridcluster. Da Sie bereits über einen Cluster verfügen, dient der Clustername hier nur als logische Ressource zum Identifizieren des Namens Ihres vorhandenen Clusters. Achten Sie darauf, dass Sie den Namen Ihres vorhandenen Clusters verwenden, wenn Sie die Variablen `clusterName` und `clusterNameOverride` im folgenden Skript definieren. 
 
   Außerdem benötigen Sie mindestens die Startknoten aus Ihrem vorhandenen Rechenzentrum und die Gossip-Zertifikate, die für die Knoten-zu-Knoten-Verschlüsselung erforderlich sind. Azure Managed Instance for Apache Cassandra erfordert eine Knoten-zu-Knoten-Verschlüsselung für die Kommunikation zwischen Rechenzentren. Wenn Sie in Ihrem vorhandenen Cluster keine Knoten-zu-Knoten-Verschlüsselung implementiert haben, müssen Sie sie implementieren. Weitere Informationen finden Sie in [dieser Dokumentation](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLNodeToNode.html). Sie müssen den Pfad zum Speicherort der Zertifikate bereitstellen. Jedes Zertifikat sollte im PEM-Format vorliegen, z. B. `-----BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----`. Im Allgemeinen gibt es zwei Möglichkeiten zum Implementieren von Zertifikaten:

   1. Selbstsignierte Zertifikate. Dies bedeutet, dass für jeden Knoten ein privates und ein öffentliches Zertifikat (kein Zertifizierungsstellenzertifikat) vorhanden sind. In diesem Fall benötigen wir alle öffentlichen Zertifikate.

   1. Zertifikate, die von einer Zertifizierungsstelle signiert wurden. Hierbei kann es sich um eine selbstsignierte Zertifizierungsstelle oder sogar um eine öffentliche Zertifizierungsstelle handelt. In diesem Fall benötigen wir das Stammzertifizierungsstellenzertifikat (siehe Anweisungen zum [Vorbereiten von SSL-Zertifikaten für die Produktion](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLCertWithCA.html)) und alle Zwischenstufen (falls zutreffend).

   Wenn Sie auch Client-zu-Knoten-Zertifikate implementiert haben (siehe [hier](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLClientToNode.html)), müssen Sie diese beim Erstellen des Hybridclusters ebenfalls im gleichen Format bereitstellen. Siehe Beispiel unten.

   > [!NOTE]
   > Der Wert der von Ihnen unten angegebenen Variablen `delegatedManagementSubnetId` stimmt exakt mit dem Wert von `--scope` überein, den Sie im obigen Befehl angegeben haben:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --external-seed-nodes 10.52.221.2 10.52.221.3 10.52.221.4 \
     --external-gossip-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/gossipKeyStore.crt_signed
     # optional - add your existing datacenter's client-to-node certificates (if implemented):
     # --client-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/nodeKeyStore.crt_signed 
   ```

   > [!NOTE]
   > Wenn Ihr Cluster bereits über Knoten-zu-Knoten- und Client-zu-Knoten-Verschlüsselung verfügt, müssen Sie wissen, wo Ihre vorhandenen Client- und/oder Gossip-SSL-Zertifikate gespeichert werden. Falls Sie sich nicht sicher sind, sollten Sie `keytool -list -keystore <keystore-path> -rfc -storepass <password>` ausführen können, um die Zertifikate auszugeben. 

1. Führen Sie nach dem Erstellen der Clusterressource den folgenden Befehl aus, um die Details zur Clustereinrichtung anzuzeigen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
   ```

1. Mit dem obigen Befehl werden Informationen zur Umgebung der verwalteten Instanz zurückgegeben. Sie benötigen die Gossip-Zertifikate für die Installation im Vertrauensspeicher für die Knoten in Ihrem vorhandenen Rechenzentrum. Im folgenden Screenshot ist die Ausgabe des vorherigen Befehls und das Format der Zertifikate dargestellt:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Abrufen der Zertifikatdetails aus dem Cluster" lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

   > [!NOTE]
   > Die vom obigen Befehl zurückgegebenen Zertifikate enthalten Zeilenumbrüche, die als Text dargestellt werden, z. B. `\r\n`. Sie sollten jedes Zertifikat in eine Datei kopieren und formatieren, bevor Sie versuchen, es in den Vertrauensspeicher Ihres vorhandenen Rechenzentrums zu importieren.

   > [!TIP]
   > Kopieren Sie den im obigen Screenshot gezeigten Arraywert `gossipCertificates` in eine Datei, und verwenden Sie das folgende Bash-Skript (Sie müssen [jq für Ihre Plattform herunterladen und installieren](https://stedolan.github.io/jq/download/)), um die Zertifikate zu formatieren und jeweils separate PEM-Dateien zu erstellen.
   >
   > ```bash
   > readarray -t cert_array < <(jq -c '.[]' gossipCertificates.txt)
   > # iterate through the certs array, format each cert, write to a numbered file.
   > num=0
   > filename=""
   > for item in "${cert_array[@]}"; do
   >   let num=num+1
   >   filename="cert$num.pem"
   >   cert=$(jq '.pem' <<< $item)
   >   echo -e $cert >> $filename
   >   sed -e '1d' -e '$d' -i $filename
   > done
   > ```

1. Erstellen Sie als Nächstes im Hybridcluster ein neues Rechenzentrum. Achten Sie darauf, dass Sie die Variablenwerte durch die Details für Ihren Cluster ersetzen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 9 
   ```

1. Führen Sie nach Abschluss der Erstellung des neuen Rechenzentrums den Befehl „datacenter show“ aus, um die zugehörigen Details anzuzeigen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName 
   ```

1. Mit dem obigen Befehl werden die Startknoten des neuen Rechenzentrums ausgegeben: 

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Abrufen der Details zum Rechenzentrum" lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

1. Fügen Sie die Startknoten des neuen Rechenzentrums in der Datei [cassandra.yaml](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html) der [Startknotenkonfiguration](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html#configCassandra_yaml__seed_provider) Ihres vorhandenen Rechenzentrums hinzu. Installieren Sie außerdem die Gossip-Zertifikate der verwalteten Instanz, die Sie zuvor im Vertrauensspeicher für alle Knoten in Ihrem vorhandenen Cluster erfasst haben, mit dem `keytool`-Befehl für jedes Zertifikat:

   ```bash
   keytool -importcert -keystore generic-server-truststore.jks -alias CassandraMI -file cert1.pem -noprompt -keypass myPass -storepass truststorePass
   ```

   > [!NOTE]
   > Falls Sie weitere Rechenzentren hinzufügen möchten, können Sie die obigen Schritte wiederholen, aber Sie benötigen hierfür nur die Startknoten. 

1. Verwenden Sie abschließend die folgende CQL-Abfrage, um die Replikationsstrategie in den einzelnen Keyspaces so zu aktualisieren, dass alle Rechenzentren des gesamten Clusters eingebunden sind:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3};
   ```

   Darüber hinaus müssen Sie auch die Kennworttabellen aktualisieren:

   ```bash
   ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3}
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

In dieser Schnellstartanleitung wurde beschrieben, wie Sie mit der Azure CLI und Azure Managed Instance for Apache Cassandra einen Hybridcluster erstellen. Sie können nun mit der Nutzung des Clusters beginnen.

> [!div class="nextstepaction"]
> [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
