---
title: 'Schnellstart: Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals eine Azure Managed Instance-Instanz für einen Apache Cassandra-Cluster erstellen.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 09/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 276ac87c4d1f0c31d75c4dd653aaa5c67f96d6c0
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754518"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Schnellstart: Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal (Vorschau)

Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-Rechenzentren in Apache Cassandra und ermöglicht so schnellere Hybridszenarios und einen geringeren kontinuierlichen Wartungsaufwand.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra befindet sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
   * **SKU**: Der SKU-Typ für Ihren Cluster.
   * **Anz. der Knoten**: Anzahl der Knoten in einem Cluster. Diese Knoten fungieren als Replikate für Ihre Daten.
   * **Ursprüngliches Cassandra-Administratorkennwort**: Das Kennwort, das zum Erstellen des Clusters verwendet wird.
   * **Cassandra-Administratorkennwort bestätigen**: Geben Sie Ihr Kennwort erneut ein.

   > [!NOTE]
   > Während der öffentlichen Vorschau können Sie den Managed Instance-Cluster in den Regionen *„USA, Osten“, „USA, Westen“, „USA, Osten 2“, „USA, Westen 2“, „USA, Mitte“, „USA, Süden-Mitte“, „Europa, Norden“, „Europa, Westen“, „Asien, Südosten“, „Indien, Mitte“ und „Australien, Osten“* erstellen.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Ausfüllen des Formulars zum Erstellen des Clusters." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Wählen Sie nun die Registerkarte **Netzwerk** aus.

1. Wählen Sie im Bereich **Netzwerk** den Namen des **Virtuellen Netzwerks** und das **Subnetz** aus. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurieren der Netzwerkdetails" lightbox="./media/create-cluster-portal/networking.png" border="true":::.

   > [!NOTE]
   > Für die Bereitstellung einer Instanz von Azure Managed Instance for Apache Cassandra ist Internetzugriff erforderlich. In Umgebungen, in denen der Internetzugriff eingeschränkt ist, tritt ein Fehler bei der Bereitstellung auf. Stellen Sie sicher, dass der Zugriff im VNet auf die folgenden wichtigen Azure-Dienste, die für die richtige Funktionsweise von Managed Cassandra erforderlich sind, nicht blockiert ist:
   > - Azure Storage
   > - Azure Key Vault
   > - Skalierungsgruppen für virtuelle Azure-Computer
   > - Azure-Überwachung
   > - Azure Active Directory
   > - Azure Security

1. Wenn Sie im letzten Schritt ein neues VNet erstellt haben, fahren Sie mit Schritt 8 fort. Wenn Sie vor dem Erstellen des Clusters ein vorhandenes VNET ausgewählt haben, müssen Sie einige besondere Berechtigungen für das VNET und das Subnetz anwenden. Verwenden Sie hierzu den Befehl `az role assignment create`, und ersetzen Sie `<subscription ID>`, `<resource group name>` und `<VNet name>` durch die entsprechenden Werte:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Die Werte `assignee` und `role` im vorherigen Befehl sind feste Werte. Geben Sie diese Werte daher genau so ein, wie im Befehl angegeben. Andernfalls treten beim Erstellen des Clusters Fehler auf. Treten beim Ausführen dieses Befehls Fehler auf, verfügen Sie möglicherweise nicht über die Berechtigungen zum Ausführen des Befehls. Wenden Sie sich an Ihren Administrator, um die entsprechenden Berechtigungen zu erhalten.

1. Nachdem Sie nun die Schritte für das Netzwerk abgeschlossen haben, klicken Sie auf **Überprüfen und erstellen** > **Erstellen**.

   > [!NOTE]
   > Es kann bis zu 15 Minuten dauern, bis der Cluster erstellt ist.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Überprüfen der Zusammenfassung, um den Cluster zu erstellen." lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. Nachdem die Bereitstellung abgeschlossen ist, aktivieren Sie Ihre Ressourcengruppe, um den neu erstellten Managed Instance-Cluster anzuzeigen:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Übersichtsseite nach dem Erstellen des Clusters." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Navigieren Sie zum Durchsuchen der Clusterknoten zum Bereich „Virtuelles Netzwerk“, den Sie zum Erstellen des Clusters verwendet haben, und öffnen Sie den Bereich **Übersicht**, um sie anzuzeigen:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Anzeigen der Clusterressourcen." lightbox="./media/create-cluster-portal/resources.png" border="true":::

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

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Anwenden von Berechtigungen für Ihre Virtual Network-Instanz ein Fehler mit dem Hinweis auftritt, dass *der Benutzer oder Dienstprinzipal in der Graphdatenbank für „e5007d2c-4b13-4a74-9b6a-605d99f03501“ nicht gefunden werden kann*, können Sie die gleiche Berechtigung manuell über das Azure-Portal anwenden. Navigieren Sie zum Anwenden von Berechtigungen über das Portal zum Bereich **Zugriffssteuerung (IAM)** Ihres vorhandenen virtuellen Netzwerks, und fügen Sie der Rolle „Netzwerkadministrator“ eine Rollenzuweisung für „Azure Cosmos DB“ hinzu. Werden bei der Suche nach „Azure Cosmos DB“ zwei Einträge angezeigt, fügen Sie beide Einträge hinzu wie in der folgenden Abbildung gezeigt: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Anwenden von Berechtigungen" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Die Azure Cosmos DB-Rollenzuweisung wird nur für Bereitstellungszwecke verwendet. Azure Managed Instance for Apache Cassandra verfügt über keine Back-End-Abhängigkeiten von Azure Cosmos DB.   

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
