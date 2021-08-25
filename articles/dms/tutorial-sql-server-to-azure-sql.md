---
title: 'Tutorial: Offlinemigration von SQL Server zu Azure SQL-Datenbank'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Offlinemigration von SQL Server zu Azure SQL-Datenbank durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 990dfd3a2cf86f77310e51cb29fa65e181b37a9f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638470"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Tutorial: Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von DMS

Mit Azure Database Migration Service können Sie die Datenbanken von einer SQL Server-Instanz zu [Azure SQL-Datenbank](/azure/sql-database/) migrieren. In diesem Tutorial migrieren Sie die Datenbank [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files), die auf einer lokalen Instanz von SQL Server 2016 (oder höher) wiederhergestellt wurde, mithilfe von Azure Database Migration Service zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank.

Sie lernen Folgendes:
> [!div class="checklist"]
>
> - Bewerten und Evaluieren Ihrer lokalen Datenbank in Bezug auf blockierende Probleme per Datenmigrations-Assistent
> - Verwenden des Datenmigrations-Assistenten zum Migrieren des Datenbankbeispielschemas 
> - Registrieren des Ressourcenanbieters für die Azure-Datenmigration
> - Erstellen einer Instanz von Azure Database Migration Service
> - Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> - Ausführen der Migration
> - Überwachen der Migration


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Laden Sie [SQL Server 2016 oder höher](https://www.microsoft.com/sql-server/sql-server-downloads) herunter, und installieren Sie es.
- Aktivieren Sie das TCP/IP-Protokoll (dieses wird während der SQL Server Express-Installation standardmäßig deaktiviert), indem Sie die Anweisungen im Artikel [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) befolgen.
- Erstellen Sie gemäß der Anleitung im Artikel [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](../azure-sql/database/single-database-create-quickstart.md) eine Datenbank in Azure SQL-Datenbank. Zum Zweck dieses Tutorials lautet der Name der Azure SQL-Datenbank **AdventureWorksAzure**. Sie können sie jedoch nach Bedarf umbenennen.

    > [!NOTE]
    > Falls Sie SQL Server Integration Services (SSIS) verwenden und die Katalogdatenbank für Ihre SSIS-Projekte/-Pakete (SSISDB) von SQL Server zu Azure SQL-Datenbank migrieren möchten, wird die Ziel-SSISDB automatisch für Sie erstellt und verwaltet, wenn Sie SSIS in Azure Data Factory (ADF) bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).
  
- Laden Sie die aktuelle Version des [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595) herunter, und installieren Sie sie.
- Erstellen Sie ein virtuelles Microsoft Azure-Netzwerk für Azure Database Migration Service mithilfe des Azure Resource Manager-Bereitstellungsmodells, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > - Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > - Speicherendpunkt
    > - Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.
    >
    >Wenn Sie keine Site-to-Site-Konnektivität zwischen dem lokalen Netzwerk und Azure haben oder wenn die Bandbreite der Site-to-Site-Konnektivität begrenzt ist, sollten Sie die Verwendung von Azure Database Migration Service im Hybridmodus (Vorschauversion) in Betracht ziehen. Der Hybridmodus nutzt einen lokalen Migrationsworker zusammen mit einer Instanz von Azure Database Migration Service, die in der Cloud ausgeführt wird. Informationen zum Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus finden Sie im Artikel [Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus mit dem Azure-Portal](./quickstart-create-data-migration-service-hybrid-portal.md).

- Stellen Sie sicher, dass die NSG-Regeln (Netzwerksicherheitsgruppen) für ausgehende Sicherheit des virtuellen Netzwerks nicht den ausgehenden Port 443 von ServiceTag für ServiceBus, Storage und AzureMonitor blockieren. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung für virtuelle Azure-Netzwerke finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433). Wenn die Standardinstanz an einem anderen Port lauscht, fügen Sie diesen der Firewall hinzu.
- Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
- Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
- Erstellen Sie für Azure SQL-Datenbank eine IP-[Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
- Stellen Sie sicher, dass die für die Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldeinformationen über [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-Berechtigungen verfügen.
- Stellen Sie sicher, dass die für die Verbindungsherstellung mit der Azure SQL-Datenbank-Zielinstanz verwendeten Anmeldeinformationen über die Berechtigung [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) für die Zieldatenbanken verfügen.

    > [!IMPORTANT]
    > Bei der Erstellung einer Instanz von Azure Database Migration Service muss auf Einstellungen für das virtuelle Netzwerk zugegriffen werden, die sich normalerweise nicht in der gleichen Ressourcengruppe befinden. Benutzer, die eine DMS-Instanz erstellen möchten, müssen daher über eine entsprechende Berechtigung auf Abonnementebene verfügen. Führen Sie zum Erstellen der erforderlichen Rollen das folgende Skript aus. Die Rollen können dann nach Bedarf zugewiesen werden.
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="assess-your-on-premises-database"></a>Bewerten Ihrer lokalen Datenbank

Damit Sie Daten von einer SQL Server-Instanz zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank migrieren können, müssen Sie die SQL Server-Datenbank auf Probleme untersuchen, die eine Migration verhindern könnten. Führen Sie im Datenmigrations-Assistenten die im Artikel [Durchführen einer SQL Server-Migrationsbewertung](/sql/dma/dma-assesssqlonprem) beschriebenen Schritte aus, um die Bewertung der lokalen Datenbank abzuschließen. Hier sehen Sie eine Zusammenfassung der erforderlichen Schritte:

1. Wählen Sie im Data Migration Assistant das Symbol „New (+)“ (Neu), und wählen Sie dann den Projekttyp **Assessment** (Bewertung) aus.
2. Geben Sie einen Projektnamen an. Wählen Sie in der Dropdownliste **Bewertungstyp** die Option **Datenbank-Engine** und dann im Textfeld **Typ des Quellservers** die Option **SQL Server** aus. Wählen Sie im Textfeld **Typ des Zielservers** die Option **Azure SQL-Datenbank** und dann **Erstellen** aus, um das Projekt zu erstellen.

    Bei der Bewertung der SQL Server-Quelldatenbank für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank können Sie einen oder beide der folgenden Bewertungsberichtstypen auswählen:

   - Check database compatibility (Datenbankkompatibilität prüfen)
   - Check feature parity (Featureparität prüfen)

    Beide Berichtsarten sind standardmäßig ausgewählt.

3. Wählen Sie im Data Migration Assistant im Bildschirm **Options** (Optionen) die Schaltfläche **Weiter**.
4. Geben Sie im Bildschirm **Select sources** (Quellen auswählen) im Dialogfeld **Connect to a server** (Verbindung mit einem Server herstellen) die Verbindungsdetails für SQL Server an, und wählen Sie dann **Connect** (Verbinden).
5. Wählen Sie im Dialogfeld **Quellen hinzufügen** den Eintrag **Adventureworks2016** und dann **Hinzufügen** > **Bewertung starten** aus.

    > [!NOTE]
    > Bei Verwendung von SSIS wird die Bewertung der Quell-SSISDB derzeit von DMA nicht unterstützt. SSIS-Projekte/-Pakete werden jedoch bewertet/überprüft, wenn sie in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitgestellt werden. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

    Nach Abschluss der Bewertung werden die Ergebnisse wie in der folgenden Abbildung angezeigt:

    ![Bewerten der Datenmigration](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Für Datenbanken in Azure SQL-Datenbank ermitteln die Bewertungen sowohl Probleme mit der Featureparität als auch Probleme, die eine Migration verhindern können, wenn eine Bereitstellung in einer Einzel- oder Pooldatenbank vorgenommen wird.

    - Die Kategorie **SQL Server-Featureparität** bietet eine umfassende Reihe von Empfehlungen, in Azure verfügbaren alternativen Herangehensweisen sowie Schritte zur Risikominimierung, sodass Sie den Aufwand bei Ihren Migrationsprojekten einplanen können.
    - In der Kategorie **Kompatibilitätsprobleme** sind teilweise oder nicht unterstützte Features angegeben, die zu Kompatibilitätsproblemen führen können, durch die eine Migration der SQL Server-Datenbank(en) zu Azure SQL-Datenbank ggf. verhindert wird. Zudem werden Empfehlungen bereitgestellt, mit deren Hilfe Sie diese Probleme behandeln können.

6. Prüfen Sie die Bewertungsergebnisse auf die oben genannten Probleme, indem Sie die jeweiligen Optionen auswählen.

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas

Nachdem Sie sich mit der Bewertung vertraut gemacht und sich davon überzeugt haben, dass die ausgewählte Datenbank ein guter Kandidat für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank ist, verwenden Sie DMA zum Migrieren des Schemas zu Azure SQL-Datenbank.

> [!NOTE]
> Bevor Sie ein Migrationsprojekt im Data Migration Assistant erstellen, vergewissern Sie sich, dass Sie bereits eine Datenbank in Azure bereitgestellt haben, wie in den Voraussetzungen erwähnt. 

> [!IMPORTANT]
> Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMA nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

Führen Sie zur Migration des **Adventureworks2016**-Schemas zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank die folgenden Schritte aus:

1. Wählen Sie im Data Migration Assistant das Symbol „Neu (+)“ aus, und wählen Sie dann unter **Projekttyp** die Option **Migration** aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, und wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank).
3. Wählen Sie unter **Migration Scope** (Migrationsumfang) die Option **Schema only** (Nur Schema).

    Nachdem Sie die vorherigen Schritte durchgeführt haben, wird die Data Migration Assistant-Oberfläche wie in der folgenden Abbildung angezeigt:

    ![Erstellen eines Data Migration Assistant-Projekts](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.
5. Geben Sie im Data Migration Assistant die Quellverbindungsdetails für Ihre SQL Server-Instanz an, und wählen Sie **Connect** (Verbinden), und dann die Datenbank **Adventureworks2016** aus.

    ![Data Migration Assistant-Quellverbindungsdetails](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Wählen Sie **Weiter** aus. Geben Sie unter **Verbindung mit Zielserver herstellen** die Zielverbindungsdetails für die Azure SQL-Datenbank an, wählen Sie **Verbinden**, und wählen Sie dann die Datenbank **AdventureWorksAzure** aus, die Sie zuvor in Azure SQL-Datenbank bereitgestellt hatten.

    ![Data Migration Assistant-Zielverbindungsdetails](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Wählen Sie **Weiter** aus, um zum Bildschirm **Select objects** (Objekte auswählen) zu gelangen. Hier können Sie die Schemaobjekte in der Datenbank **Adventureworks2016** angeben, die in Azure SQL-Datenbank bereitgestellt werden sollen.

    Standardmäßig sind alle Objekte ausgewählt.

    ![Generieren von SQL-Skripts](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Wählen Sie **Generate SQL script** (SQL-Skript generieren) aus, um die SQL-Skripts zu erstellen, und prüfen Sie die Skripts dann auf Fehler.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Wählen Sie **Deploy schema** (Schema bereitstellen) aus, um das Schema für Azure SQL-Datenbank bereitzustellen, und prüfen Sie den Zielserver anschließend auf Anomalien.

    ![Bereitstellen des Schemas](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]   

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Suchen Sie **Azure Database Migration Service**, und wählen Sie ihn aus.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. Auf dem Bildschirm **Create Migration Service** (Migrationsdienst erstellen) mit den Grundeinstellungen:

     - Wählen Sie das Abonnement aus.
     - Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
     - Geben Sie einen Namen für die Instanz von Azure Database Migration Service an.
     - Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.
     - Wählen Sie **Azure** als Dienstmodus aus.
     - Wählen Sie einen Tarif. Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Grundeinstellungen einer Azure Database Migration Service-Instanz](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - Klicken Sie auf **Weiter: Netzwerk**.

4. Auf dem Netzwerkbildschirm **Create Migration Service** (Migrationsdienst erstellen):

    - Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues. Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die Azure SQL-Datenbank-Zielinstanz. Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurieren der Netzwerkeinstellungen einer Azure Database Migration Service-Instanz](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Wählen Sie zum Erstellen des Diensts die Option **Überprüfen + erstellen** aus.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Menü des Azure-Portals **Alle Dienste** aus. Suchen Sie **Azure Database Migration Services**, und wählen Sie sie aus.

     ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Services** die von Ihnen erstellte Azure Database Migration Service-Instanz aus.

3. Wählen Sie **Neues Migrationsprojekt** aus.

     ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **SQL Server** und im Textfeld **Zielservertyp** die Option **Azure SQL-Datenbank** aus. Wählen Sie außerdem für **Aktivitätstyp für Migration auswählen** die Option **Datenmigration** aus.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie auf dem Bildschirm **Quelle auswählen** die Verbindungsdetails für die SQL Server-Quellinstanz an.

    Stellen Sie sicher, dass Sie einen vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den SQL Server-Quellinstanznamen verwenden. Sie können auch die IP-Adresse für Situationen verwenden, in denen DNS-Namensauflösung nicht möglich ist.

2. Wenn Sie auf Ihrem Quellserver kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn ein vertrauenswürdiges Zertifikat nicht installiert ist, erzeugt SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. In einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, sollten Sie sich nicht auf TLS mit Verwendung selbstsignierter Zertifikate verlassen.

    > [!IMPORTANT]
    > Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMS nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

   ![Quellendetails](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. Wählen Sie **Weiter: Datenbanken auswählen** aus.

## <a name="select-databases-for-migration"></a>Auswählen von Datenbanken für die Migration

Wählen Sie entweder alle Datenbanken oder bestimmte Datenbanken aus, die Sie zu Azure SQL-Datenbank migrieren möchten. DMS gibt die erwartete Migrationszeit für ausgewählte Datenbanken an. Ist die Downtime für die Migration akzeptabel, fahren Sie mit der Migration fort. Ist die Downtime für die Migration nicht akzeptabel, sollten Sie eine Migration zu [SQL Managed Instance nahezu ohne Downtime](tutorial-sql-server-managed-instance-online.md) erwägen oder sich an das [DMS-Team](mailto:DMSFeedback@microsoft.com) wenden, um Informationen zu weiteren Optionen zu erhalten. 

1. Wählen Sie in der Liste der verfügbaren Datenbanken die Datenbanken aus, die Sie migrieren möchten. 
1. Überprüfen Sie die erwartete Downtime. Ist diese akzeptabel, wählen Sie **Weiter: Ziel auswählen >>** aus.

   ![Quelldatenbanken](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Ziel auswählen** Authentifizierungseinstellungen für Ihre Azure SQL-Datenbank-Instanz an. 

   ![Ziel auswählen](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > Derzeit wird nur die SQL-Authentifizierung als Authentifizierungstyp unterstützt.

1. Klicken Sie auf **Weiter: Map to target databases** (Den Zieldatenbanken zuordnen), um die Quell- und die Zieldatenbank für die Migration zuzuordnen.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Klicken Sie auf **Weiter: Migrationseinstellungen konfigurieren**, erweitern Sie die Tabelle, und sehen Sie sich die Liste mit den betroffenen Feldern an.

    Azure Database Migration Service wählt automatisch alle leeren Quelltabellen aus, die in der Zielinstanz von Azure SQL-Datenbank vorhanden sind. Wenn Sie Tabellen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Tabellen auf diesem Blatt explizit auswählen.

    ![Tabellen auswählen](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Klicken Sie auf **Weiter: Zusammenfassung**, überprüfen Sie die Migrationskonfiguration, und geben Sie im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität an.

    ![Auswählen der Validierungsoption](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>Ausführen der Migration

- Wählen Sie **Migration starten** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Ausstehend**.

    ![Aktivitätsstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Abgeschlossen** lautet.

    ![Aktivitätsstatus „Abgeschlossen“](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Überprüfen Sie die Zieldatenbank(en) in der **Azure SQL-Zieldatenbank**.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

- Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
- Informationen zu Azure SQL-Datenbank finden Sie im Artikel [Der Azure SQL-Datenbank-Dienst](../azure-sql/database/sql-database-paas-overview.md).
