---
title: Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB mithilfe nativer MongoDB-Tools
description: Hier erfahren Sie, wie Sie kleine Datasets mithilfe nativer MongoDB-Tools von MongoDB-Instanzen zu Azure Cosmos DB migrieren.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 05/19/2021
ms.reviewer: sngun
ms.openlocfilehash: 6c7aedfa7b0a9c174e9205cb744f470574888be6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783508"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Tutorial: Offlinemigration von MongoDB zur Azure Cosmos DB-API für MongoDB mithilfe nativer MongoDB-Tools
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Lesen Sie den gesamten Leitfaden, bevor Sie die Migrationsschritte ausführen.
>

Dieser MongoDB-Migrationsleitfaden ist Teil der Reihe zur MongoDB-Migration. Die wichtigen MongoDB-Migrationsschritte umfassen die [Migrationsvorbereitung](pre-migration-steps.md), die Migration selbst und die [Schritte nach der Migration](post-migration-optimization.md), wie nachfolgend dargestellt.

![Diagramm der Migrationsschritte](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-data-migration-using-mongodb-native-tools"></a>Übersicht über die Datenmigration mithilfe nativer MongoDB-Tools

Sie können native MongoDB-Tools verwenden, um eine (einmalige) Offlinemigration von Datenbanken aus einer lokalen oder cloudbasierten MongoDB-Instanz zur Azure Cosmos DB-API für MongoDB durchzuführen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Auswählen des passenden nativen MongoDB-Tools für Ihren Anwendungsfall
> * Ausführen der Migration
> * Überwachen der Migration
> * Vergewissern, dass die Migration erfolgreich ausgeführt wurde

In diesem Tutorial wird ein Dataset in MongoDB, das auf einem virtuellen Azure-Computer gehostet wird, mithilfe nativer MongoDB-Tools zur Azure Cosmos DB-API für MongoDB migriert. Bei den nativen MongoDB-Tools handelt es sich um Binärdateien zur Vereinfachung der Datenbearbeitung für eine vorhandene MongoDB-Instanz. Da von Azure Cosmos DB eine Mongo-API verfügbar gemacht wird, können über die nativen MongoDB-Tools Daten in Azure Cosmos DB eingefügt werden. Der Schwerpunkt dieses Dokuments liegt auf der Migration von Daten aus einer MongoDB-Instanz mithilfe von *mongoexport/mongoimport* oder *mongodump/mongorestore*. Da die nativen Tools mithilfe von Verbindungszeichenfolgen mit MongoDB verbunden werden, können Sie die Tools überall ausführen. Zur Vermeidung von Firewallproblemen empfiehlt es sich jedoch, die Tools innerhalb des gleichen Netzwerks auszuführen wie die MongoDB-Instanz. 

Die Geschwindigkeit, mit der Daten von den nativen MongoDB-Tools verschoben werden können, hängt von der Hosthardware ab. Für kleine Datasets, bei denen die Gesamtmigrationszeit keine Rolle spielt, können die nativen Tools die einfachste Lösung sein. Wenn Sie eine skalierbare Migrationspipeline benötigen, sind [MongoDB-Spark-Connector](https://docs.mongodb.com/spark-connector/current/), [Azure Database Migration Service (DMS)](../../dms/tutorial-mongodb-cosmos-db.md) und [Azure Data Factory (ADF)](../../data-factory/connector-azure-cosmos-db-mongodb-api.md) ggf. bessere Alternativen.

Wenn Sie noch keine MongoDB-Quelle eingerichtet haben, lesen Sie den Artikel [Installieren und Konfigurieren von MongoDB auf einem virtuellen Windows-Computer in Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Führen Sie die Schritte zur Migrationsvorbereitung](pre-migration-steps.md) aus. Schätzen Sie beispielsweise den Durchsatz, und wählen Sie einen Partitionsschlüssel und die Indizierungsrichtlinie aus.
* [Erstellen Sie ein Konto für die Azure Cosmos DB-API für MongoDB.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Melden Sie sich bei Ihrer MongoDB-Instanz an.
    * Laden Sie unter [diesem Link](https://www.mongodb.com/try/download/database-tools) die nativen MongoDB-Tools herunter, und installieren Sie sie.
        * **Vergewissern Sie sich, dass die Version Ihrer nativen MongoDB-Tools der Version Ihrer vorhandenen MongoDB-Instanz entspricht.**
        * Sollte sich die Version Ihrer MongoDB-Instanz von der Version der Mongo-API von Azure Cosmos DB unterscheiden, **installieren Sie beide Versionen der nativen MongoDB-Tools, und verwenden Sie die jeweilige Toolversion für MongoDB bzw. für die Mongo-API von Azure Cosmos DB.**
    * Fügen Sie einen Benutzer mit Berechtigungen vom Typ `readWrite` hinzu, sofern noch keiner vorhanden ist. Der Benutzername und das Kennwort müssen später in diesem Tutorial für die Tools *mongoexport* und *mongodump* angegeben werden.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Konfigurieren serverseitiger Wiederholungen für Azure Cosmos DB

Kunden, die von MongoDB zu Azure Cosmos DB migrieren, profitieren von Ressourcengovernancefunktionen, die die vollständige Nutzung Ihres bereitgestellten Durchsatzes (RU/s) garantieren. Von Azure Cosmos DB können im Laufe der Migration ggf. Anforderungen gedrosselt werden, wenn bei ihnen die vom Container bereitgestellte Menge an RU/s überschritten wird. In diesem Fall muss die Anforderung wiederholt werden. Die Roundtripzeit für den Netzwerkhop zwischen dem Migrationstool und Azure Cosmos DB wirkt sich nachteilig auf die Gesamtreaktionszeit der betroffenen Anforderung aus. Außerdem werden von den nativen MongoDB-Tools unter Umständen keine Wiederholungen abgewickelt. Das Feature *Serverseitige Wiederholung* von Azure Cosmos DB ermöglicht es dem Dienst, drosselungsbedingte Fehlercodes abzufangen und eine Wiederholung mit wesentlich kürzerer Roundtripzeit durchzuführen, wodurch sich die Antwortzeiten von Anforderungen erheblich verbessern. Aus Sicht der nativen MongoDB-Tools wird der Bedarf für die Wiederholungsabwicklung minimiert, was sich positiv auf Ihre Migrationserfahrung auswirkt.

Die Funktion „Serverseitige Wiederholung“ (Server Side Retry, SSR) finden Sie im Azure Cosmos DB-Portal auf dem Blatt *Features*.

![Screenshot: SSR-Feature von MongoDB](../../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Ist das Feature *deaktiviert*, wird empfohlen, es wie im Anschluss gezeigt zu aktivieren:

![Screenshot: Aktivieren von MongoDB-SSR](../../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Auswählen des passenden nativen MongoDB-Tools

![Diagramm: Auswählen des optimalen nativen MongoDB-Tools](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* ist die beste Kombination von Migrationstools, wenn Sie eine Teilmenge Ihrer MongoDB-Datenbank migrieren möchten.
    * Mit *mongoexport* können Sie Ihre vorhandenen Daten in eine lesbare JSON- oder CSV-Datei exportieren. Von *mongoexport* wird ein Argument zur Angabe der zu exportierenden Teilmenge Ihrer vorhandenen Daten akzeptiert. 
    * Von *mongoimport* wird eine JSON- oder CSV-Datei geöffnet und der Inhalt in die Zieldatenbankinstanz (in diesem Fall: Azure Cosmos DB) eingefügt. 
    * Beachten Sie, dass JSON und CSV keine kompakten Formate sind. Unter Umständen fallen zusätzliche Netzwerkgebühren an, wenn von *mongoimport* Daten an Azure Cosmos DB gesendet werden.
* *mongodump/mongorestore* ist die beste Kombination von Migrationstools, wenn Sie Ihre gesamte MongoDB-Datenbank migrieren möchten. Das kompakte BSON-Format ermöglicht eine effizientere Nutzung der Netzwerkressourcen, wenn die Daten in Azure Cosmos DB eingefügt werden.
    * Von *mongodump* werden Ihre vorhandenen Daten als BSON-Datei exportiert.
    * Von *mongorestore* wird die BSON-Dateisicherung in Azure Cosmos DB importiert.
* Übrigens: Wenn Sie einfach nur eine kleine JSON-Datei in die Mongo-API von Azure Cosmos DB importieren möchten, können Sie das Tool *mongoimport* zur schnellen Erfassung der Daten verwenden.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Erfassen der Anmeldeinformationen für die Mongo-API von Azure Cosmos DB

Die Mongo-API von Azure Cosmos DB bietet kompatible Zugriffsanmeldeinformationen, die von nativen MongoDB-Tools verwendet werden können. Diese Zugriffsanmeldeinformationen werden benötigt, um Daten zur Mongo-API von Azure Cosmos DB migrieren zu können. So finden Sie diese Anmeldeinformationen:

1. Öffnen Sie das Azure-Portal.
1. Navigieren Sie zu Ihrem Konto für die Mongo-API von Azure Cosmos DB.
1. Wählen Sie im linken Navigationsbereich das Blatt *Verbindungszeichenfolge* aus. Daraufhin sollte in etwa Folgendes angezeigt werden:

    ![Screenshot: Anmeldeinformationen für Azure Cosmos DB](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *HOST*: Der Azure Cosmos DB-Endpunkt fungiert als MongoDB-Hostname.
    * *PORT*: Wenn von nativen MongoDB-Tools eine Verbindung mit Azure Cosmos DB hergestellt wird, muss dieser Port explizit angegeben werden.
    * *BENUTZERNAME*: Das Präfix des Domänennamens des Azure Cosmos DB-Endpunkts fungiert als MongoDB-Benutzername.
    * *KENNWORT*: Der Azure Cosmos DB-Hauptschlüssel fungiert als MongoDB-Kennwort.
    * Beachten Sie außerdem das auf `true` festgelegte Feld *SSL*. SSL **muss** durch das native MongoDB-Tool aktiviert werden, wenn Daten in Azure Cosmos DB geschrieben werden.

## <a name="perform-the-migration"></a>Durchführen der Migration

1. Wählen Sie aus, welche Datenbanken und Sammlungen Sie migrieren möchten. In diesem Beispiel wird die Sammlung *query* in der Datenbank *edx* von MongoDB zu Azure Cosmos DB migriert.

Im weiteren Verlauf dieses Abschnitts erfahren Sie, wie Sie die im vorherigen Abschnitt ausgewählte Toolkombination verwenden.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Öffnen Sie auf dem Computer mit der MongoDB-Instanz ein Terminal, um die Daten aus der MongoDB-Quellinstanz zu exportieren. Wenn es sich dabei um einen Linux-Computer handelt, geben Sie Folgendes ein:

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Unter Windows heißt die ausführbare Datei `mongoexport.exe`. *HOST* (HOST), *PORT* (PORT), *USERNAME* (BENUTZERNAME) und *PASSWORD* (KENNWORT) müssen auf der Grundlage der Eigenschaften Ihrer vorhandenen MongoDB-Datenbankinstanz angegeben werden. 
    
    Es ist auch möglich, nur eine Teilmenge des MongoDB-Datasets zu exportieren. Hierzu können Sie beispielsweise ein zusätzliches Filterargument hinzufügen:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Es werden nur Dokumente exportiert, die dem Filter `{"field1":"value1"}` entsprechen.

    Wenn Sie den Aufruf ausführen, sollte eine Datei namens `edx.json` erstellt werden:

    ![Screenshot: Aufruf von „mongoexport“](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Sie können das gleiche Terminal verwenden, um `edx.json` in Azure Cosmos DB zu importieren. Wenn Sie `mongoimport` auf einem Linux-Computer ausführen, geben Sie Folgendes ein:

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Unter Windows heißt die ausführbare Datei `mongoimport.exe`. *HOST* (HOST), *PORT* (PORT), *USERNAME* (BENUTZERNAME) und *PASSWORD* (KENNWORT) müssen auf der Grundlage der zuvor erfassten Azure Cosmos DB-Anmeldeinformationen angegeben werden. 
1. **Überwachen** Sie die Terminalausgabe von *mongoimport*. Im Terminal sollten Textzeilen mit aktualisiertem Migrationsstatus ausgegeben werden:

    ![Screenshot: Aufruf von „mongoimport“](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Überprüfen Sie abschließend Azure Cosmos DB, um sich zu **vergewissern**, dass die Migration erfolgreich war. Öffnen Sie das Azure Cosmos DB-Portal, und navigieren Sie zum Daten-Explorer. Dort sollten Sie Folgendes sehen: 1. Es wurde eine Datenbank namens *edx* mit einer Sammlung namens *importedQuery* erstellt. 2. Falls Sie nur eine Teilmenge der Daten exportiert haben, sollten in *importedQuery* auch *nur* Dokumente enthalten sein, die der gewünschten Teilmenge der Daten entsprechen. Im folgenden Beispiel entsprach nur ein einzelnes Dokument dem Filter `{"field1":"value1"}`:

    ![Screenshot: Cosmos DB-Datenüberprüfung](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Öffnen Sie auf dem Computer mit der MongoDB-Instanz ein Terminal, um eine BSON-Datensicherung Ihrer MongoDB-Instanz zu erstellen. Wenn es sich dabei um einen Linux-Computer handelt, geben Sie Folgendes ein:

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *HOST* (HOST), *PORT* (PORT), *USERNAME* (BENUTZERNAME) und *PASSWORD* (KENNWORT) müssen auf der Grundlage der Eigenschaften Ihrer vorhandenen MongoDB-Datenbankinstanz angegeben werden. Sie sollten sehen, dass ein Verzeichnis namens `edx-dump` erstellt wurde und dass die Verzeichnisstruktur von `edx-dump` der Ressourcenhierarchie (Datenbank- und Sammlungsstruktur) Ihrer MongoDB-Quellinstanz entspricht. Jede Sammlung wird durch eine BSON-Datei dargestellt:

    ![Screenshot: Aufruf von „mongodump“](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Sie können das gleiche Terminal verwenden, um den Inhalt von `edx-dump` in Azure Cosmos DB wiederherzustellen. Wenn Sie `mongorestore` auf einem Linux-Computer ausführen, geben Sie Folgendes ein:

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Unter Windows heißt die ausführbare Datei `mongorestore.exe`. *HOST* (HOST), *PORT* (PORT), *USERNAME* (BENUTZERNAME) und *PASSWORD* (KENNWORT) müssen auf der Grundlage der zuvor erfassten Azure Cosmos DB-Anmeldeinformationen angegeben werden. 
1. **Überwachen** Sie die Terminalausgabe von *mongorestore*. Im Terminal sollten Zeilen mit aktualisiertem Migrationsstatus ausgegeben werden:

    ![Screenshot: Aufruf von „mongorestore“](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Überprüfen Sie abschließend Azure Cosmos DB, um sich zu **vergewissern**, dass die Migration erfolgreich war. Öffnen Sie das Azure Cosmos DB-Portal, und navigieren Sie zum Daten-Explorer. Dort sollten Sie Folgendes sehen: 1. Es wurde eine Datenbank namens *edx* mit einer Sammlung namens *importedQuery* erstellt. 2. *importedQuery* sollte das *gesamte* Dataset aus der Quellsammlung enthalten:

    ![Screenshot: Überprüfen der Cosmos DB-Daten von „mongorestore“](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optimierung nach der Migration

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Sie können nach der Migration auch andere Optimierungsschritte ausführen und beispielsweise die Indizierungsrichtlinie optimieren, die Standardkonsistenzebene aktualisieren oder die globale Verteilung für Ihr Azure Cosmos DB-Konto konfigurieren. Weitere Informationen finden Sie im Artikel zur [Optimierung nach der Migration](post-migration-optimization.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cosmos DB-Dienstinformationen](https://azure.microsoft.com/services/cosmos-db/)
* [Dokumentation zu MongoDB-Datenbanktools](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich in der Migrationsanleitung im [Leitfaden zur Datenbankmigration von Microsoft](https://datamigration.microsoft.com/) über weitere Szenarios.