---
title: 'Tutorial: Onlinemigration von RDS PostgreSQL zu Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von RDS PostgreSQL zu Azure Database for PostgreSQL durchführen.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 6c42c7df67baeba56213000175e0f5c2a50054d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638486"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Tutorial: Onlinemigration von RDS PostgreSQL mit DMS zu Azure Database for PostgreSQL

Sie können Azure Database Migration Service verwenden, um Datenbanken von einer RDS-PostgreSQL-Instanz zu [Azure Database for PostgreSQL](../postgresql/index.yml) zu migrieren, während die Quelldatenbank während der Migration online bleibt. Somit kommt es bei der Migration nur zu einer geringen Ausfallzeit für die Anwendung. In diesem Tutorial migrieren Sie die Beispieldatenbank **DVD Rental** von einer Instanz von RDS PostgreSQL 9.6 zu Azure Database for PostgreSQL. Zu diesem Zweck verwenden Sie die Onlinemigrationsaktivität in Azure Database Migration Service.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Migrieren des Beispielschemas mit dem Hilfsprogramm „pg_dump“
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration
> * Durchführen der Migrationsübernahme

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Weitere Informationen finden Sie auf der Seite [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/). Wir verschlüsseln den Datenträger, um Datendiebstahl während der Migration zu verhindern.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dieser Artikel beschreibt, wie Sie eine Onlinemigration von einer lokalen Instanz von PostgreSQL zu Azure Database for PostgreSQL durchführen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Laden Sie [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 oder 10 herunter, und installieren Sie die Edition. Die PostgreSQL-Version des Quellservers muss 9.5.11, 9.6.7, 10 oder höher sein. Weitere Informationen finden Sie im Artikel [Unterstützte PostgreSQL-Datenbankversionen](../postgresql/concepts-supported-versions.md).

   Beachten Sie außerdem, dass die Azure Database for PostgreSQL-Zielversion höher als die RDS PostgreSQL-Version sein oder dieser entsprechen muss. Beispiel: RDS PostgreSQL 9.6 kann nur zu Azure Database for PostgreSQL 9.6, 10 oder 11 migriert werden, nicht aber zu Azure Database for PostgreSQL 9.5.

* Erstellen Sie eine Instanz von [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) oder [Azure Database for PostgreSQL – Hyperscale (Citus)](../postgresql/quickstart-create-hyperscale-portal.md). Lesen Sie diesen [Abschnitt](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) des Dokuments, um zu erfahren, wie Sie mit pgAdmin eine Verbindung mit dem PostgreSQL-Server herstellen können.
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.
* Stellen Sie sicher, dass die NSG-Regeln (Netzwerksicherheitsgruppen) des virtuellen Netzwerks nicht den ausgehenden Port 443 von ServiceTag für ServiceBus, Storage und AzureMonitor blockieren. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die PostgreSQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 5432).
* Wenn Sie eine Firewall-Appliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, damit Azure Database Migration Service auf die Quelldatenbanken für die Migration zugreifen kann.
* Erstellen Sie für den Azure Database for PostgreSQL-Server eine [Firewallregel](../postgresql/concepts-firewall-rules.md) auf Serverebene, um den Zugriff auf die Zieldatenbanken durch Azure Database Migration Service zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Einrichten von AWS RDS PostgreSQL für die Replikation

1. Um eine neue Parametergruppe zu erstellen, befolgen Sie die von AWS im Artikel [Arbeiten mit DB-Parametergruppen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html) bereitgestellten Anweisungen.
2. Verwenden Sie den Masterbenutzernamen, um aus Azure Database Migration Service eine Verbindung mit der Quelle herzustellen. Wenn Sie ein anderes Konto als das Masterbenutzerkonto verwenden, muss das Konto über die Rollen rds_superuser und rds_replication verfügen. Die Rolle rds_replication berechtigt zur Verwaltung logischer Slots sowie zum Streamen von Daten mit logischen Slots.
3. Erstellen Sie eine neue Parametergruppe mit der folgenden Konfiguration:

    a. Legen Sie den rds.logical_replication-Parameter in Ihrer DB-Parametergruppe auf 1 fest.

    b. max_wal_senders = [Anzahl gleichzeitiger Aufgaben] – Der max_wal_senders-Parameter legt die Anzahl von Aufgaben fest, die gleichzeitig ausgeführt werden können, 10 Aufgaben empfohlen.

    c. max_replication_slots – = [Anzahl von Slots], empfohlene Einstellung: fünf Slots.

4. Ordnen Sie die Parametergruppe, die Sie erstellt haben, der RDS PostgreSQL-Instanz zu.

## <a name="migrate-the-schema"></a>Migrieren des Schemas

1. Extrahieren Sie das Schema aus der Quelldatenbank, und wenden Sie es auf die Zieldatenbank an, um die Migration aller Datenbankobjekte wie Tabellenschemas, Indizes und gespeicherten Prozeduren abzuschließen.

    Die einfachste Möglichkeit zum Migrieren nur des Schemas ist die Verwendung von pg_dump mit der Option „-s“. Weitere Informationen finden Sie in den [Beispielen](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) im Postgres-Tutorial zu pg_dump.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Zum Sichern einer Schemadatei für die Datenbank **dvdrental** verwenden Sie z.B. den folgenden Befehl:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Erstellen Sie eine leere Datenbank im Zieldienst Azure Database for PostgreSQL. Informationen zum Herstellen einer Verbindung und zum Erstellen einer Datenbank finden Sie in einem der folgenden Artikel:

    * [Erstellen eines Azure Database for PostgreSQL-Servers mithilfe des Azure-Portals](../postgresql/quickstart-create-server-database-portal.md)
    * [Erstellen eines Azure Database for PostgreSQL-Servers – Hyperscale (Citus) über das Azure-Portal](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importieren Sie das Schema in den Zieldienst Azure Database for PostgreSQL. Um die Schemasicherungsdatei wiederherzustellen, führen Sie den folgenden Befehl aus:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Beispiel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

  > [!NOTE]
   > Der Migrationsdienst verwaltet intern die Aktivierung/Deaktivierung von Fremdschlüsseln und Triggern, um eine zuverlässige und stabile Datenmigration zu gewährleisten. Daher müssen Sie sich keine Gedanken über Änderungen am Schema der Zieldatenbank machen.

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-instance-of-azure-database-migration-service"></a>Erstellen einer Instanz von Azure Database Migration Service

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk bietet Azure Database Migration Service Zugriff auf die PostgreSQL-Quellinstanz und die Azure Database for PostgreSQL-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif aus. Für diese Onlinemigration müssen Sie Premium auswählen: 4vCores-Tarif.

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, wählen Sie die Instanz aus, und klicken Sie dann auf **Neues Migrationsprojekt**.
3. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **AWS RDS for PostgreSQL** (AWS RDS für SQL Server), und wählen Sie dann im Textfeld **Typ des Zielservers** die Option **Azure Database for PostgreSQL** aus.
4. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    > [!IMPORTANT]
    > Wählen Sie unbedingt **Onlinedatenmigration** aus; Offlinemigrationen werden für dieses Szenario nicht unterstützt.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

5. Wählen Sie **Speichern** aus.

6. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

    > [!NOTE]
    > Notieren Sie sich die Voraussetzungen für die Einrichtung der Onlinemigration auf dem Projekterstellungsblatt.

## <a name="specify-source-details"></a>Angeben von Quelldetails

* Geben Sie auf dem Bildschirm **Quelldetails hinzufügen** die Verbindungsinformationen für die PostgreSQL-Quellinstanz an.

   ![Quellendetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern** aus, und geben Sie dann auf dem Bildschirm **Zieldetails** die Verbindungsdetails für den Azure Database for PostgreSQL-Zielserver an, der vorab bereitgestellt wird und das Schema **DVD Rentals** über pg_dump implementiert.

    ![Zieldetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Ausgeführt** lautet.

    ![Aktivitätsstatus: Wird ausgeführt](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Wählen Sie unter **DATENBANKNAME** eine bestimmte Datenbank aus, um den Migrationsstatus für die Vorgänge **Vollständiger Datenladevorgang** und **Inkrementelle Datensynchronisierung** abzurufen.

    Unter **Vollständiger Datenladevorgang** wird der Migrationsstatus des ersten Ladevorgangs und unter **Inkrementelle Datensynchronisierung** der CDC-Status (Change Data Capture) angezeigt.

    ![Inventarbildschirm: Vollständiger Datenladevorgang](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Inventarbildschirm: Inkrementelle Datensynchronisierung](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

2. Warten Sie, bis der Zähler **Ausstehende Änderungen** den Wert **0** anzeigt, um sicherzustellen, dass alle eingehenden Transaktionen für die Datenbank angehalten werden, aktivieren Sie das Kontrollkästchen **Bestätigen**, und klicken Sie auf **Anwenden**.

    ![Bildschirm „Umstellung abschließen“](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure Database for PostgreSQL-Zieldatenbank her.

Die Onlinemigration von einer lokalen Instanz von RDS PostgreSQL zu Azure Database for PostgreSQL ist jetzt abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
* Weitere Informationen zu Azure Database for PostgreSQL finden Sie im Artikel [Was ist Azure Database for PostgreSQL?](../postgresql/overview.md).
* Wenn Sie weitere Fragen haben, senden Sie eine E-Mail an den Alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
