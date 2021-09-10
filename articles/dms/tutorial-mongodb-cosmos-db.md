---
title: 'Tutorial: Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Offlinemigration von einer lokalen MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: 87f211976f74fef5ed2c0cef992d82099e141a29
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038169"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Tutorial: Migration von MongoDB zur Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Lesen Sie den gesamten Leitfaden, bevor Sie die Migrationsschritte ausführen.
>

Dieser MongoDB-Migrationsleitfaden ist Teil der Reihe zur MongoDB-Migration. Die wichtigen MongoDB-Migrationsschritte umfassen die [Migrationsvorbereitung](../cosmos-db/mongodb-pre-migration.md), die Migration selbst und die [Schritte nach der Migration](../cosmos-db/mongodb-post-migration.md), wie nachfolgend dargestellt.

![Diagramm der Migrationsschritte](../cosmos-db/mongodb/media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-offline-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>Übersicht über die Offlinedatenmigration von MongoDB zu Azure Cosmos DB mit DMS

Sie können Azure Database Migration Service zum Durchführen einer Offlinemigration (einmalig) von Datenbanken aus einer lokalen oder cloudbasierten MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB verwenden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration

In diesem Tutorial migrieren Sie ein Dataset in MongoDB, das auf einem virtuellen Azure-Computer gehostet wird. Wenn Sie Azure Database Migration Service verwenden, migrieren Sie das DataSet zur Azure Cosmos DB-API für MongoDB. Wenn Sie noch keine MongoDB-Quelle eingerichtet haben, lesen Sie [Installieren und Konfigurieren von MongoDB auf einem virtuellen Windows-Computer in Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Führen Sie die Schritte zur Migrationsvorbereitung](../cosmos-db/mongodb-pre-migration.md) aus. Schätzen Sie beispielsweise den Durchsatz, und wählen Sie einen Partitionsschlüssel aus.
* [Erstellen Sie ein Konto für die Azure Cosmos DB-API für MongoDB.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie den Azure Resource Manager verwenden. Dieses Bereitstellungsmodell bietet Site-to-Site-Konnektivität zu Ihren lokalen Quellservern, indem es entweder [Azure ExpressRoute](../expressroute/expressroute-introduction.md) oder [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) verwendet. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Azure Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, oder Azure Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Vergewissern Sie sich, dass die folgenden Kommunikationsports nicht durch Regeln für Netzwerksicherheitsgruppen (NSGs) der Netzwerksicherheitsgruppe blockiert werden: 53, 443, 445, 9354 und 10000-20000. Weitere Informationen finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den MongoDB-Quellserver zugreifen kann (standardmäßig TCP-Port 27017).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.

## <a name="configure-the-server-side-retry-feature"></a>Server seitiges Wiederholungs Feature konfigurieren

Wenn Sie von MongoDB zu Azure Cosmos DB migrieren, können Sie von den Ressourcen Steuerungsfunktionen profitieren. Mit diesen Funktionen können Sie die bereitgestellten Anforderungs Einheiten (Request Units, ru/s) des Durchsatzes vollständig nutzen. Von Azure Cosmos DB können im Laufe der Migration ggf. bestimmte Data Migration Service-Anforderungen gedrosselt werden, wenn bei ihnen die vom Container bereitgestellte Menge an RU/s überschritten wird. In diesem Fall muss die Anforderung wiederholt werden. Anschließend muss die Anforderung wiederholt werden.

Database Migration Service ist in der Lage, Wiederholungs Versuche auszuführen. Es ist wichtig zu verstehen, dass die Roundtrip-Zeit, die im Netzwerkhop zwischen Database Migration Service und Azure Cosmos DB beteiligt ist, sich auf die Gesamtantwort Zeit dieser Anforderung auswirkt. Durch die Verbesserung der Antwortzeit für gedrosselte Anforderungen kann die Gesamtdauer der Migration verkürzt werden.

Das Feature Serverseitige Wiederholung von Azure Cosmos DB ermöglicht es dem Dienst, drosselungsbedingte Fehlercodes abzufangen und eine Wiederholung mit wesentlich kürzerer Roundtripzeit durchzuführen, wodurch sich die Antwortzeiten von Anforderungen erheblich verbessern.

Um die serverseitige Wiederholung zu verwenden, wählen Sie im Azure Cosmos DB-Portal **Features**  >  **serverseitiges Wiederholungsversuch** aus.

![Screenshot, der zeigt, wo Sie die Server seitige Wiederholungsfunktion finden.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Wenn die Funktion deaktiviert ist, wählen Sie **aktivieren** aus.

![Screenshot, der zeigt, wie Sie die Server seitige Wiederholung aktivieren.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]  

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Screenshot des Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Screenshot, der zeigt, wie eine Instanz von Azure Database Migration Service erstellt wird.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Geben Sie unter **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten. 

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die MongoDB-Quellinstanz und auf das Azure Cosmos DB-Zielkonto.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Screenshot, der Konfigurationseinstellungen für die Instanz von Azure Database Migration Service anzeigt.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem sie den Dienst erstellt haben, suchen Sie im Azure-Portal, und öffnen Sie es. Erstellen Sie dann ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Screenshot, der zeigt, wie eine Instanz von Azure Database Migration Service gesucht wird.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

3. Wählen Sie **+ New Migration Project** (Neues Migrationsprojekt) aus.

4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **MongoDB**. In der **Zielservertyp** text box, wählen Sie **CosmosDB (MongoDB API)** , und dann für **Aktivitätstyp auswählen**, wählen Sie **Offlinedatenmigration**. 

    ![Screenshot das Projektoptionen zeigt.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Source details** (Quelldetails) die Verbindungsdetails für die MongoDB-Server-Quellinstanz an.

   > [!IMPORTANT]
   > Azure Cosmos DB als Quelle wird von Azure Database Migration Service nicht unterstützt.

    Es gibt drei Modi, um eine Verbindung mit einer Quelle herzustellen:
   * **Standardmodus**, in dem ein vollqualifizierter Domänenname oder eine IP-Adresse, eine Portnummer und die Anmeldeinformationen für die Verbindung akzeptiert werden.
   * **Verbindungszeichenfolgen-Modus**, in dem eine MongoDB-Verbindungszeichenfolge akzeptiert wird. Dies ist im  [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-Format von Verbindungszeichenfolgen) beschrieben.
   * **Daten aus Azure-Speicher**, wobei eine Blobcontainer-SAS-URL akzeptiert wird. Wählen Sie **Blob enthält BSON Speicherabbilder** wenn der Blob- container BSON Speicherabbilder vom MongoDB [bsondump tool](https://docs.mongodb.com/manual/reference/program/bsondump/)erzeugt hat. Wählen Sie diese Option nicht aus, wenn der Container JSON-Dateien enthält.

     Bei Verwendung dieser Option muss die Verbindungszeichenfolge für das Speicherkonto folgendes Format haben:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Diese SAS-Verbindungszeichenfolge für den Blobcontainer finden Sie im Azure Storage-Explorer. Bei der Erstellung der SAS für den betreffenden Container erhalten Sie die URL im angeforderten Format.
     
     Beachten Sie außerdem den folgenden Aspekt (abhängig von der Art der Sicherungsinformationen in Azure Storage):

     * Bei BSON-Sicherungen müssen die Daten im Blobcontainer im bsondump-Format vorliegen. Platzieren Sie Datendateien in Ordnern, die nach den enthaltenden Datenbanken im Format *Collection. bson* benannt werden. Benennen Sie alle Metadatendateien, indem Sie das Format *collection.metadata.js*.

     * Bei JSON-Sicherungen müssen die Dateien im Blobcontainer in Ordnern platziert werden, die nach den enthaltenden Datenbanken benannt sind. Innerhalb der einzelnen Datenbankordner müssen Datenbankdateien in eine Unterordner namens *data*, plaziert und im Format *Ammlung.json* benannt werden. Vorhandene Metadaten müssen in einem Unterordner namens *metadata* platziert und ebenfalls im Format *Sammlung.json*. benannt werden. Die Metadatendateien müssen in dem Format vorliegen, das vom MongoDB-Tool „bsondump“ generiert wird.

    > [!IMPORTANT]
    > Es wird nicht empfohlen, ein selbst signiertes Zertifikat auf dem MongoDB-Server zu verwenden. Wenn Sie einen verwenden müssen, stellen Sie eine Verbindung mit dem Server her, indem Sie den Verbindungs Zeichen folgen Modus verwenden, und stellen Sie sicher, dass Ihre Verbindungs Zeichenfolge Anführungszeichen ("")
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Sie können auch die IP-Adresse für Situationen verwenden, in denen DNS-Namensauflösung nicht möglich ist.

   ![Screenshot, der das Angeben von Quell Details anzeigt.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wählen Sie **Speichern** aus.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie im Bildschirm **Migrationszieldetail** die Verbindungsdetails für das Ziel Azure Cosmos DB Konto an. Bei diesem Konto handelt es sich um die vorab bereitgestellte Azure Cosmos DB API für das MongoDB-Konto, für das Sie Ihre MongoDB-Daten migrieren.

    ![Screenshot, der das Angeben von Quell Details anzeigt.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wählen Sie **Speichern** aus.

## <a name="map-to-target-databases"></a>Zuordnen zu Zieldatenbanken

1. Ordnen Sie im Bildschirm **Map to target databases** (Zu Zieldatenbanken zuordnen) die Quell- und die Zieldatenbank für die Migration einander zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    Wenn die Zeichenfolge **Erstellen** neben dem Datenbanknamen angezeigt wird, hat Azure Database Migration Service die Zieldatenbank nicht gefunden, und der Dienst erstellt die Datenbank für Sie.

    An diesem Punkt der Migration können Sie [Durchsatz bereitstellen](../cosmos-db/set-throughput.md). In Azure Cosmos DB können Sie den Durchsatz entweder auf Datenbankebene oder für jede Sammlung individuell bereitstellen. Der Durchsatz wird in [Anforderungseinheiten](../cosmos-db/request-units.md) gemessen. Weitere Informationen: [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Screenshot zeigt den Bereich Zieldatenbanken zuordnen.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wählen Sie **Speichern** aus.
3. Erweitern Sie auf dem Einstellungsbildschirm **Sammlung** die Auflistung der Sammlungen, und überprüfen Sie dann die Liste der Sammlungen, die migriert werden.

    Azure Database Migration Service wählt automatisch alle Sammlungen aus, die in der MongoDB-Quellinstanz vorhanden sind, jedoch nicht im Azure Cosmos DB-Zielkonto. Wenn Sie Sammlungen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Sammlungen in diesem Bereich explizit auswählen.

    Sie können die Anzahl von RUs angeben, die die Sammlungen verwenden sollen. Azure Database Migration Service schlägt intelligente Standardwerte basierend auf der Sammlungsgröße vor.

    > [!NOTE]
    > Führen Sie die Datenbankmigration und-Sammlung parallel aus. Bei Bedarf können Sie mehrere Instanzen von Azure Database Migration Service verwenden, um die Ausführung zu beschleunigen.

    Sie können auch einen Shardschlüssel angeben, um die [Partitionierung in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) für eine optimale Skalierbarkeit zu nutzen. Überprüfen Sie unbedingt die [bewährten Methoden für die Auswahl eines Shard-/Partitionsschlüssels](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Screenshot, der das Auswählen von Auflistungs Tabellen anzeigt.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wählen Sie **Speichern** aus.

5. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität an.

    ![Screenshot, der die Zusammenfassung der nigration anzeigt.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

Wählen Sie **Migration ausführen** aus. Das Fenster „Migrationsaktivität“ wird angezeigt, und der Status der Aktivität lautet  **Nicht gestartet**.

![Screenshot, der den Aktivitätsstatus zeigt.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der Status der Migration **Abgeschlossen** lautet.

> [!NOTE]
> Sie können die Aktivität zum Abrufen von Details der Migrationsmetriken auf Datenbank- und Sammlungsebene auswählen.

![Screenshot, der den abgeschlossenen Aktivitätsstatus zeigt.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Überprüfen der Daten in Azure Cosmos DB

Nachdem die Migration abgeschlossen ist, können Sie Ihr Azure Cosmos DB-Konto überprüfen, um sicherzustellen, dass alle Sammlungen erfolgreich migriert wurden.

![Der Screenshot zeigt, wo Sie Ihr Azure Cosmos DB-Konto überprüfen können, um sicherzustellen, dass alle Sammlungen erfolgreich migriert wurden.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimierung nach der Migration

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Sie können auch andere Optimierungsschritte nach der Migration ausführen. Hierzu zählen u. a. die Optimierung der Indizierungs Richtlinie, die Aktualisierung der Standard Konsistenz Ebene oder die Konfiguration der globalen Verteilung für Ihr Azure Cosmos DB Konto. Weitere Informationen finden Sie unter [Optimierung nach der Migration](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../cosmos-db/convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](../cosmos-db/mongodb/estimate-ru-capacity-planner.md)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich in der Migrationsanleitung im [Leitfaden zur Datenbankmigration von Azure](https://datamigration.microsoft.com/) über weitere Szenarios.



