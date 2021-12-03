---
title: 'Schnellstart: Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen verwalteten Apache Spark-Cluster mit Azure Databricks bereitstellen.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fb33196d95f21041cf2657bdd27f203ad3f860ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084935"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-with-azure-databricks"></a>Schnellstart: Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks

Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-Rechenzentren in Apache Cassandra und ermöglicht so schnellere Hybridszenarios und einen geringeren kontinuierlichen Wartungsaufwand.

In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen vollständig verwalteten Apache Spark-Cluster innerhalb des virtuellen Azure-Netzwerks Ihres Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ erstellen. Der Spark-Cluster wird in Azure Databricks erstellt. Später können Sie Notebooks erstellen oder an den Cluster anfügen, Daten aus verschiedenen Datenquellen lesen und Erkenntnisse analysieren.

Weitere Informationen sowie eine ausführliche Anleitung finden Sie unter [Bereitstellen von Azure Databricks in Ihrem virtuellen Azure-Netzwerk (VNET-Einschleusung)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Erstellen eines Azure Databricks-Clusters

Gehen Sie wie folgt vor, um einen Azure Databricks-Cluster in einem virtuellen Netzwerk zu erstellen, in dem sich Azure Managed Instance for Apache Cassandra befindet:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie im linken Navigationsbereich nach **Ressourcengruppen**, und navigieren Sie zu der Ressourcengruppe, die das virtuelle Netzwerk mit Ihrer bereitgestellten verwalteten Instanz enthält.

1. Öffnen Sie die Ressource **Virtual Network**, und notieren Sie sich den **Adressraum**:

   :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Ermitteln des Adressraums Ihres virtuellen Netzwerks" border="true":::

1. Wählen Sie in der Ressourcengruppe die Option **Hinzufügen** aus, und suchen Sie im Suchfeld nach **Azure Databricks**:

   :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Suchen nach Azure Databricks" border="true":::

1. Wählen Sie **Erstellen** aus, um ein Azure Databricks-Konto zu erstellen:

   :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Erstellen eines Azure Databricks-Kontos" border="true":::

1. Geben Sie die folgenden Werte an:

   * **Arbeitsbereichsname**: Geben Sie einen Namen für Ihren Databricks-Arbeitsbereich an.
   * **Region**: Wählen Sie die Region aus, in der sich Ihr virtuelles Netzwerk befindet.
   * **Tarif**: Wählen Sie zwischen „Standard“, „Premium“ und „Testversion“. Weitere Informationen zu diesen Tarifen, finden Sie unter [Azure Databricks – Preise](https://azure.microsoft.com/pricing/details/databricks/).

   :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Angeben von Arbeitsbereichsname, Region und Tarif für das Databricks-Konto" border="true":::

1. Wählen Sie als Nächstes die Registerkarte **Netzwerk** aus, und geben Sie Folgendes an:

   * **Azure Databricks-Arbeitsbereich in Ihrem eigenen virtuellen Netzwerk bereitstellen**: Wählen Sie **Ja** aus.
   * **Virtuelles Netzwerk**: Wählen Sie in der Dropdownliste das virtuelle Netzwerk aus, in dem sich Ihre verwaltete Instanz befindet.
   * **Name des öffentlichen Subnetzes**: Geben Sie einen Namen für das öffentliche Subnetz ein.
   * **CIDR-Bereich des öffentlichen Subnetzes**: Geben Sie einen IP-Adressbereich für das öffentliche Subnetz ein.
   * **Name des privaten Subnetzes**: Geben Sie einen Namen für das private Subnetz ein.
   * **CIDR-Bereich für privates Subnetz**: Geben Sie einen IP-Adressbereich für das private Subnetz ein.

   Wählen Sie höhere Bereiche aus, um Bereichskonflikte zu vermeiden. Verwenden Sie bei Bedarf einen [visuellen Subnetzrechner](https://www.fryguy.net/wp-content/tools/subnets.html), um die Bereiche zu unterteilen:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Verwenden des Subnetzrechners für das virtuelle Netzwerk" border="true":::

   Der folgende Screenshot zeigt den Bereich „Netzwerk“ mit Beispielangaben:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Angeben von Namen für das öffentliche und das private Subnetz" border="true":::

1. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus, um den Arbeitsbereich bereitzustellen.

1. Wählen Sie nach Abschluss der Erstellung die Option **Arbeitsbereich starten** aus.

1. Sie werden zum Azure Databricks-Portal weitergeleitet. Wählen Sie im Portal **Neuer Cluster** aus.

1. Passen Sie im Bereich **Neuer Cluster** nur die folgenden Felder an, und übernehmen Sie ansonsten die Standardwerte:

   * **Clustername**: Geben Sie einen Namen für den Cluster ein.
   * **Databricks-Runtimeversion**: Es wird empfohlen, mindestens die Databricks-Runtimeversion 7.5 auszuwählen, um Spark 3.x-Unterstützung zu erhalten. 

   :::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Auswählen der Databricks-Runtimeversion und des Spark-Clusters" border="true":::

1. Erweitern Sie **Erweiterte Optionen**, und fügen Sie die folgende Konfiguration hinzu. Ersetzen Sie dabei die IP-Adressen der Knoten sowie die Anmeldeinformationen:

   ```java
   spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
   spark.cassandra.auth.password cassandra
   spark.cassandra.connection.port 9042
   spark.cassandra.auth.username cassandra
   spark.cassandra.connection.ssl.enabled true
   ```

1. Fügen Sie dem Cluster die Apache Spark-Cassandra-Connectorbibliothek hinzu, um eine Verbindung mit nativen Endpunkten sowie mit Azure Cosmos DB-Cassandra-Endpunkten herzustellen. Wählen Sie in Ihrem Cluster **Bibliotheken**  >  **Neue**  >  **Maven** installieren und fügen Sie dann `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` in Maven-Koordinaten hinzu.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Screenshot, der zeigt, wie Maven-Pakete in Databricks gesucht werden.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diesen Managed Instance-Cluster nicht mehr benötigen, löschen Sie ihn wie folgt:

1. Wählen Sie im linken Menü des Azure-Portals die Option **Ressourcengruppen** aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie für diesen Schnellstart erstellt haben.
1. Wählen Sie im Ressourcengruppenbereich **Übersicht** die Option **Ressourcengruppe löschen** aus.
1. Geben Sie in dem nächsten Fenster den Namen der zu löschenden Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie einen vollständig verwalteten Apache Spark-Cluster innerhalb des virtuellen Netzwerks Ihres Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ erstellen. Im nächsten Artikel erfahren Sie, wie Sie die Cluster- und Rechenzentrumsressourcen verwalten:

> [!div class="nextstepaction"]
> [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
