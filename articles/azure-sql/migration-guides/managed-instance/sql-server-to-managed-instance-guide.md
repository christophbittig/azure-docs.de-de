---
title: 'SQL Server zu Azure SQL Managed Instance: Anleitung zur Migration'
description: In diesem Leitfaden erfahren Sie, wie Sie Ihre SQL Server-Datenbanken zu Azure SQL Managed Instance migrieren.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 06/25/2021
ms.openlocfilehash: d00eb47a1e366d5ae9f3ba559a65e57f5c9c9baa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044000"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>SQL Server zu Azure SQL Managed Instance: Anleitung zur Migration
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Mithilfe dieses Leitfadens können Sie Ihre SQL Server-Instanzen zu Azure SQL Managed Instance zu migrieren. 

Sie können SQL Server-Instanzen migrieren, die lokal oder auf folgenden Plattformen ausgeführt werden: 

- SQL Server auf virtuellen Computern  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL für SQL Server (Google Cloud Platform – GCP) 

Weitere Informationen zur Migration finden Sie in [dieser Übersicht](sql-server-to-managed-instance-overview.md). Weitere Migrationsleitfäden finden Sie im [Leitfaden zur Azure-Datenbankmigration](/data-migration). 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Ablauf des Migrationsprozesses":::

## <a name="prerequisites"></a>Voraussetzungen 

Zum Migrieren Ihrer SQL Server-Datenbank-Instanzen zu Azure SQL Managed Instance ist Folgendes erforderlich: 

- Sie haben eine [Migrationsmethode](sql-server-to-managed-instance-overview.md#compare-migration-options) und die entsprechenden Tools für Ihre Methode ausgewählt.
- Sie haben den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) auf einem Computer installiert, der eine Verbindung mit Ihrer SQL Server-Quelldatenbank herstellen kann.
- Sie haben eine Zielinstanz von [Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md) erstellt.
- Sie haben Konnektivität und geeignete Berechtigungen für den Zugriff auf die Quelle und das Ziel konfiguriert. 
- Sie haben die [in Azure SQL Managed Instance verfügbaren Features](../../database/features-comparison.md) der SQL Server-Datenbank-Engine überprüft. 


## <a name="pre-migration"></a>Vor der Migration

Sobald Sie sich davon überzeugt haben, dass Ihre Quellumgebung unterstützt wird, können Sie mit der Prämigrationsphase beginnen. Ermitteln Sie alle vorhandenen Datenquellen, bewerten Sie die Umsetzbarkeit der Migration, und identifizieren Sie Probleme, die der Migration möglicherweise im Weg stehen.  

### <a name="discover"></a>Entdecken

Scannen Sie das Netzwerk in der Ermittlungsphase nach SQL Server-Instanzen und -Features, die von Ihrer Organisation verwendet werden. 

Nutzen Sie [Azure Migrate](../../../migrate/migrate-services-overview.md), um die Umsetzbarkeit der Migration auf lokalen Servern zu bewerten, führen Sie eine leistungsbasierte Größenanpassung durch, und erstellen Sie eine Kostenschätzungen für die Ausführung in Azure. 

Alternativ können Sie das  [Microsoft Assessment and Planning Toolkit (MAP Toolkit)](https://www.microsoft.com/download/details.aspx?id=7826) zur Bewertung Ihrer aktuellen IT-Infrastruktur verwenden. Das Toolkit bietet leistungsfähige Optionen zur Bestandserfassung, Bewertung und Berichterstellung, die den Planungsprozess für die Migration erleichtern. 

Weitere Informationen zu den für die Ermittlungsphase verfügbaren Tools finden Sie unter [Dienste und Tools für Datenmigrationsszenarios](../../../dms/dms-tools-matrix.md). 

Nachdem die Datenquellen ermittelt wurden, sollten Sie alle lokalen SQL Server-Instanzen bewerten, die zu Azure SQL Managed Instance migriert werden können, um Hindernisse oder Kompatibilitätsprobleme zu identifizieren.
Fahren Sie mit den folgenden Schritten fort, um Datenbanken zu bewerten und zu Azure SQL Managed Instance zu migrieren:

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-sql-managed-instance-steps.png" alt-text="Schritte für die Migration zu Azure SQL Managed Instance":::

- [Bewerten Sie die Azure SQL Managed Instance-Kompatibilität](#assess), wobei Sie sicherstellen sollten, dass es keine blockierenden Probleme gibt, die Ihre Migrationen verhindern können.
  Dieser Schritt umfasst auch das Erstellen [einer Leistungsbaseline](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline), um die Ressourcennutzung Ihrer SQL Server-Quellinstanz zu bestimmen. Er ist erforderlich, wenn Sie eine ordnungsgemäß dimensionierte verwaltete Instanz bereitstellen und sicherstellen möchten, dass die Leistung nach der Migration nicht beeinträchtigt wird.
- [Wählen von App-Konnektivitätsoptionen](../../managed-instance/connect-application-instance.md)
- [Stellen Sie eine verwaltete Instanz mit optimaler Größe bereit](#deploy-to-an-optimally-sized-managed-instance), in der Sie technische Merkmale (Anzahl virtueller Kerne, verfügbarer Arbeitsspeicher) und Leistungsstufe („Unternehmenskritisch“, „Universell“) für Ihre verwaltete Instanz auswählen.
- [Wählen Sie eine Migrationsmethode aus und migrieren](sql-server-to-managed-instance-overview.md#compare-migration-options) Sie Ihre Datenbanken mithilfe der Optionen für Offline- oder Onlinemigration.
- [Überwachen und korrigieren Sie Anwendungen](#monitor-and-remediate-applications), um die erwartete Leistung zu gewährleisten.


### <a name="assess"></a>Bewerten 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Bestimmen Sie, ob SQL Managed Instance mit den Datenbankanforderungen Ihrer Anwendung kompatibel ist. SQL Managed Instance wurde konzipiert, um eine einfache Migration per Lift & Shift für die meisten bestehenden Anwendungen zu ermöglichen, die SQL Server verwenden. Es kann jedoch vorkommen, dass Sie Features oder Funktionen benötigen, die noch nicht unterstützt werden, und die Kosten für die Implementierung eines Workarounds zu hoch sind. 

Wenn Sie Datenbanken mit dem Datenmigrations-Assistenten (Version 4.1 und höher) bewerten, erhalten Sie folgende Informationen: 

- [Azure-Zielempfehlungen](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure-SKU-Empfehlungen](/sql/dma/dma-sku-recommend-sql-db)

Befolgen Sie diese Schritte, um Ihre Umgebung mit dem Datenmigrations-Assistenten zu bewerten: 

1. Öffnen Sie den [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Klicken Sie auf **Datei** und dann auf **Neues Projekt**. 
1. Wählen Sie einen Projektnamen, SQL Server als Quellserver und Azure SQL Managed Instance als Zielserver aus. 
1. Legen Sie fest, welche Bewertungsberichte generiert werden sollen – zum Beispiel für die Datenbankkompatibilität oder die Featureparität. Die Berechtigungen für den SQL Server-Quellserver unterscheiden sich je nach Art der Bewertung möglicherweise.  DMA zeigt vor der Bewertung an, welche Berechtigungen für den ausgewählten Advisor benötigt werden.
    - Die Kategorie **Featureparität** enthält ausführliche Empfehlungen, in Azure verfügbare Alternativen und Schritte zur Risikominimierung, die bei der Planung Ihres Migrationsprojekts nützlich sind. Hierfür sind sysadmin-Berechtigungen erforderlich.
    - In der Kategorie **Kompatibilitätsprobleme** werden teilweise oder nicht unterstützte Features aufgeführt, die die Migration möglicherweise verhindern, sowie Empfehlungen, um diese Probleme zu beheben. Die Berechtigungen `CONNECT SQL`, `VIEW SERVER STATE` und `VIEW ANY DEFINITION` sind erforderlich.
1. Geben Sie die Quellverbindungsdetails für Ihre SQL Server-Instanz an, und stellen Sie eine Verbindung mit der Quelldatenbank her.
1. Klicken Sie auf **Bewertung starten**. 
1. Lesen Sie sich nach Abschluss des Vorgangs die Bewertungsberichte durch, um beispielsweise Probleme mit der Featureparität zu ermitteln, die die Migration verhindern könnten. Der Bewertungsbericht kann auch in eine Datei exportiert und für andere Teams und Mitarbeiter in Ihrer Organisation freigegeben werden. 
1. Bestimmen Sie den Datenbank-Kompatibilitätsgrad, mit dem der Aufwand vor der Migration am geringsten ist.  
1. Ermitteln Sie die am besten geeignete Azure SQL Managed Instance-SKU für Ihre lokale Workload. 

Weitere Informationen finden Sie unter [Durchführen einer SQL Server-Migrationsbewertung mit dem Datenmigrations-Assistenten](/sql/dma/dma-assesssqlonprem).

Wenn SQL Managed Instance für Ihre Workload nicht als Ziel geeignet ist, kann SQL Server auf Azure-VMs für Ihr Unternehmen eine alternative Möglichkeit darstellen.

#### <a name="scaled-assessments-and-analysis"></a>Skalierte Bewertungen und Analysen

Der Datenmigrations-Assistent unterstützt skalierte Bewertungen und die Konsolidierung der Bewertungsberichte, damit diese analysiert werden können. Wenn mehrere Server und Datenbanken bewertet und analysiert werden müssen, um einen umfassenderen Überblick über den Datenbestand zu erstellen, finden Sie unter den folgenden Links weitere Informationen.

- [Skalierte Bewertungen mithilfe von PowerShell](/sql/dma/dma-consolidatereports)
- [Analysieren von Bewertungsberichten mithilfe von Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Die Ausführung skalierter Bewertungen für mehrere Datenbanken kann mithilfe des [Befehlszeilen-Hilfsprogramms von DMA](/sql/dma/dma-commandline) automatisiert werden. Dadurch können die Ergebnisse auch in [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) hochgeladen werden, um weitere Analysen durchzuführen und die Bereitschaft der Zielumgebung zu bewerten.

### <a name="deploy-to-an-optimally-sized-managed-instance"></a>Bereitstellen für eine verwaltete Instanz mit optimaler Größe

Erstellen Sie auf Grundlage der Informationen aus der Ermittlungs- und Bewertungsphase eine entsprechend große SQL Managed Instance-Zielinstanz. Hierfür können Sie das [Azure-Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md) oder eine [Azure Resource Manager-Vorlage (ARM-Vorlage)](../../managed-instance/create-template-quickstart.md) verwenden.

SQL Managed Instance ist auf lokale Workloads zugeschnitten, die für den Umzug in die Cloud vorgesehen sind. Es wird ein [Kaufmodell](../../database/service-tiers-vcore.md) eingeführt, das eine größere Flexibilität bei der Auswahl der richtigen Ressourcen für Ihre Workloads bietet. In der lokalen Umgebung sind Sie wahrscheinlich daran gewöhnt, diese Workloads mit physischen Kernen und E/A-Bandbreite zu dimensionieren. Das Kaufmodell für verwaltete Instanzen basiert auf virtuellen Kernen oder „V-Kernen“, wobei zusätzlicher Speicher und EA separat erhältlich sind. Das V-Kern-Modell ist eine einfachere Methode, um die Computeanforderungen in der Cloud mit dem, was Sie heute lokal verwenden, zu vergleichen. Mit diesem Kaufmodell können Sie die Größe Ihrer Zielumgebung in der Cloud optimal anpassen. Einige allgemeine Richtlinien, anhand denen Sie die richtige Dienstebene und die richtigen Merkmale auswählen können, sind hier beschrieben:

- Basierend auf der CPU-Nutzung der Baseline können Sie eine verwaltete Instanz mit derselben Anzahl von Kernen bereitstellen, die Sie in SQL Server verwenden. Dabei sollten Sie aber bedenken, dass CPU-Merkmale möglicherweise skaliert werden müssen, damit sie den [Merkmalen des virtuellen Computers entsprechen, auf dem die verwaltete Instanz installiert ist](../../managed-instance/resource-limits.md#hardware-generation-characteristics).
- Wählen Sie basierend auf der Baseline-Speicherauslastung [die Dienstebene mit übereinstimmendem Speicher](../../managed-instance/resource-limits.md#hardware-generation-characteristics) aus. Weil die Menge an Arbeitsspeicher nicht direkt ausgewählt werden kann, müssten Sie die verwaltete Instanz mit der Menge an virtuellen Kernen auswählen, die einen übereinstimmenden Arbeitsspeicher aufweisen (z. B. 5,1 GB/vCore in Gen5).
- Wählen Sie basierend auf der Baseline-E/A-Latenz des Dateisubsystems zwischen den Dienstebenen „Universell“ (Latenz größer als 5 ms) und „Unternehmenskritisch“ (Latenz kleiner als 3 ms) aus.
- Ordnen Sie basierend auf dem Baselinedurchsatz die Größe von Daten- oder Protokolldateien vorab zu, um die erwartete E/A-Leistung zu erhalten.

Im [Azure-Portal](../../database/scale-resources.md) haben Sie die Möglichkeit, Compute- und Speicherressourcen zum Zeitpunkt der Bereitstellung auszuwählen und anschließend zu ändern, ohne dass es zu Downtime für Ihre Anwendung kommt:

:::image type="content" source="media/sql-server-to-managed-instance-overview/managed-instance-sizing.png" alt-text="Dimensionierung einer verwalteten Instanz":::

Informationen zum Erstellen der VNET-Infrastruktur und einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Instanz](../../managed-instance/instance-create-quickstart.md).

> [!IMPORTANT]
> Es ist wichtig, dass Sie Ihr Ziel-VNET und -subnetz in Übereinstimmung mit den [VNET-Anforderungen für verwaltete Instanzen](../../managed-instance/connectivity-architecture-overview.md#network-requirements) halten. Jede Inkompatibilität kann Sie daran hindern, neue Instanzen anzulegen oder bereits erstellte Instanzen zu verwenden. Erfahren Sie mehr über das [Erstellen neuer](../../managed-instance/virtual-network-subnet-create-arm-template.md) und [Konfigurieren vorhandener](../../managed-instance/vnet-existing-add-subnet.md) Netzwerke.

## <a name="migrate"></a>Migrieren

Nachdem Sie die Schritte der Prämigrationsphase abgeschlossen haben, können Sie sich der Schema- und Datenmigration widmen. 

Migrieren Sie Ihre Daten mithilfe der gewählten [Migrationsmethode](sql-server-to-managed-instance-overview.md#compare-migration-options).

SQL Managed Instance zielt auf Benutzerszenarien ab, die eine Massenmigration der Datenbank von lokalen oder Azure VM-Datenbankimplementierungen erfordern. Sie sind die optimale Wahl, wenn es darum geht, das Back-End der Anwendungen, die regelmäßig Funktionalitäten auf Instanzebene und/oder über Datenbanken hinweg nutzen, per Lift & Shift zu migrieren. Wenn dies Ihr Szenario ist, können Sie eine ganze Instanz in eine entsprechende Umgebung in Azure verschieben, ohne dass Sie Ihre Anwendungen neu strukturieren müssen.

Zum Verschieben von SQL-Instanzen müssen Sie Folgendes sorgfältig planen:

- Die Migration aller Datenbanken, die zusammengeführt werden müssen (diejenigen, die auf derselben Instanz ausgeführt werden).
- Die Migration von Objekten auf Instanzebene, von denen Ihre Anwendung abhängt, wie Anmeldeinformationen, SQL-Agent-Aufträge und -Operatoren sowie Trigger auf Serverebene.

SQL Managed Instance ist ein verwalteter Dienst, der es Ihnen ermöglicht, einige der regulären DBA-Aktivitäten während deren Einbindung an die Plattform zu delegieren. Daher müssen einige Daten auf Instanzebene nicht migriert werden, wie z.B. Wartungsaufträge für regelmäßige Sicherungen oder Always On-Konfigurationen, da [Hochverfügbarkeit](../../database/high-availability-sla.md) integriert ist.

SQL Managed Instance unterstützt die folgenden Datenbankmigrationsoptionen (derzeit sind dies die einzigen unterstützten Migrationsmethoden):

- Azure Database Migration Service: Migrationen nahezu ohne Ausfallzeiten
- Native `RESTORE DATABASE FROM URL`: nutzt native Sicherungen von SQL Server und ist mit einer gewissen Ausfallzeit verbunden

In diesem Leitfaden werden die beiden beliebtesten Optionen behandelt: Azure Database Migration Service (DMS) und die native Sicherung und Wiederherstellung.

### <a name="database-migration-service"></a>Database Migration Service

Befolgen Sie diese Schritte, um Migrationen mit DMS durchzuführen:

1. [Registrieren Sie den Ressourcenanbieter **Microsoft.DataMigration**](../../../dms/quickstart-create-data-migration-service-portal.md#register-the-resource-provider) in Ihrem Abonnement, wenn Sie diesen Vorgang zum ersten Mal durchführen.
1. Erstellen Sie eine Azure Database Migration Service-Instanz an einem Standort Ihrer Wahl (vorzugsweise in der Region der Azure SQL Managed Instance-Zielinstanz). Wählen Sie dann ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues, um Ihre DMS-Instanz zu hosten.
1. Wenn Sie Ihre DMS-Instanz erstellt haben, können Sie ein neues Migrationsprojekt erstellen. Geben Sie **SQL Server** als Quellserver und **Azure SQL Managed Instance** als Zielserver an. Wählen Sie auf dem Blatt für die Projekterstellung eine Aktivität (Online- oder Offlinedatenmigration) aus. 
1.  Geben Sie auf der Seite **Migrationsquelle** die Details zur SQL Server-Quellinstanz und auf der Seite **Migrationsziel** die Details zur Azure SQL Managed Instance-Zielinstanz ein. Wählen Sie **Weiter** aus.
1. Wählen Sie die Datenbank aus, die Sie migrieren möchten. 
1. Geben Sie in den Konfigurationseinstellungen die **SMB-Netzwerkfreigabe** an, die die Sicherungsdateien für Ihre Datenbank enthält. Verwenden Sie in DMS die Windows-Benutzeranmeldeinformationen, die auf die Netzwerkfreigabe zugreifen können. Geben Sie die **Details zu Ihrem Azure Storage-Konto** an. 
1. Überprüfen Sie die Migrationszusammenfassung, und klicken Sie auf **Migration ausführen**. Anschließend können Sie die Migrationsaktivität überwachen und den Fortschritt der Datenbankmigration verfolgen.
1. Klicken Sie nach der Wiederherstellung der Datenbank auf **Übernahme starten**. Beim Migrationsprozess wird die Sicherung des Protokollfragments kopiert, sobald Sie es in der SMB-Netzwerkfreigabe verfügbar machen, und im Ziel wiederhergestellt. 
1. Halten Sie in der Quelldatenbank sämtlichen eingehenden Datenverkehr an, und aktualisieren Sie die Verbindungszeichenfolge auf die neue Azure SQL Managed Instance-Datenbank. 

Ein ausführliches Tutorial für diese Migrationsoption finden Sie unter [Tutorial: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   

### <a name="backup-and-restore"></a>Sichern und Wiederherstellen 

Eine der wichtigsten Funktionen von Azure SQL Managed Instance für eine schnelle und einfache Datenbankmigration besteht in der nativen Wiederherstellung von Datenbanksicherungsdateien (`.bak`), die in [Azure Storage](https://azure.microsoft.com/services/storage/) gespeichert sind. Die Sicherung und Wiederherstellung erfolgen asynchron, und die Dauer richtet sich nach der Größe Ihrer Datenbank. 

Das folgende Diagramm bietet einen allgemeinen Überblick über den Prozess:

![Im Diagramm weist ein Pfeil mit der Beschriftung „SICHERN/zu URL hochladen“ zu Azure Storage. Ein weiterer Pfeil mit der Beschriftung „RESTORE FROM URL“ weist von Azure Storage zu einer verwalteten SQL-Instanz.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Je nach Größe der Datenbank dauert es unterschiedlich lange, die Sicherung in Azure Storage hochzuladen und einen nativen Wiederherstellungsvorgang zu Azure SQL Managed Instance durchzuführen. Bei großen Datenbanken müssen Sie eine ausreichende Downtime einberechnen, um den Vorgang abzuschließen. 

In der folgenden Tabelle finden Sie weitere Informationen über die Methoden, die Sie je nach Version der ausgeführten SQL Server-Quellinstanz verwenden können:

|Schritt|SQL-Engine und -Version|Sicherungs- und Wiederherstellungsmethode|
|---|---|---|
|Sicherung auf Azure Storage legen|Vor 2012 SP1 CU2|Direkter Upload der BAK-Datei in Azure Storage|
| |2012 SP1 CU2 – 2016|Direkte Sicherung mit veralteter [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql)-Syntax|
| |Ab 2016|Direkte Sicherung mit [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Wiederherstellung aus Azure Storage in eine verwaltete Instanz| |[RESTORE FROM URL mit SAS CREDENTIAL](../../managed-instance/restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Beim Migrieren einer Datenbank, die durch [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) geschützt ist, zu einer verwalteten Instanz mithilfe der nativen Wiederherstellungsoption muss das entsprechende Zertifikat von der lokalen oder der Azure VM-SQL Server-Instanz vor der Wiederherstellung der Datenbank migriert werden. Eine ausführliche Anleitung finden Sie unter [Migrieren des Zertifikats einer durch TDE geschützten Datenbank zu einer verwalteten Azure SQL-Instanz](../../managed-instance/tde-certificate-migrate.md).
> - Das Wiederherstellen von Systemdatenbanken wird nicht unterstützt. Um (in Master- oder msdb-Datenbanken gespeicherte) Objekte auf Instanzebene zu migrieren, wird empfohlen, für diese T-SQL-Skripts zu erstellen und in der Zielinstanz auszuführen.

Befolgen Sie diese Schritte, um die Migration mit dem Sicherungs- und Wiederherstellungsansatz durchzuführen: 

1. Sichern Sie Ihre Datenbank in Azure Blob Storage. Verwenden Sie beispielsweise die Option [Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) (Sicherung über URL) in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Verwenden Sie das entsprechende [Microsoft Azure-Tool](https://go.microsoft.com/fwlink/?LinkID=324399), wenn Datenbanken unterstützt werden müssen, die älter als SQL Server 2012 SP1 CU2 sind. 
1. Stellen Sie über SQL Server Management Studio eine Verbindung mit Ihrer Azure SQL Managed Instance-Instanz her. 
1. Erstellen Sie mithilfe einer SAS (Shared Access Signature) die Anmeldeinformationen für den Zugriff auf das Azure Blob Storage-Konto, das Ihre Datenbanksicherungen enthält. Zum Beispiel:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Stellen Sie die Sicherung aus dem Azure Blob Storage-Container wieder her. Zum Beispiel: 

   ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Rufen Sie die Datenbank nach Abschluss der Wiederherstellung im **Objekt-Explorer** in SQL Server Management Studio auf. 

Weitere Informationen zu dieser Migrationsoption finden Sie unter [Schnellstart: Wiederherstellen einer Datenbank in Azure SQL Managed Instance mit SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Der Vorgang der Datenbankwiederherstellung ist asynchron und wiederholbar. Möglicherweise erhalten Sie eine Fehlermeldung in SQL Server Management Studio, falls die Verbindung unterbrochen oder ein Timeout überschritten wird. Azure SQL-Datenbank versucht weiterhin, die Datenbank im Hintergrund wiederherzustellen, und Sie können den Wiederherstellungsfortschritt mithilfe der Ansichten [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) und [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) verfolgen.


## <a name="data-sync-and-cutover"></a>Datensynchronisierung und Übernahme

Wenn Migrationsoptionen angewendet werden, die Datenänderungen kontinuierlich von der Quelle zum Ziel replizieren und/oder synchronisieren, werden die Quelldaten und das Quellschema ggf. geändert und weichen vom Ziel ab. Sorgen Sie während der Datensynchronisierung dafür, dass alle Änderungen an der Quelle erfasst und während des Migrationsprozesses auf das Ziel angewendet werden. 

Nachdem Sie sich vergewissert haben, dass die Daten in der Quelle und im Ziel identisch sind, können Sie die Übernahme von der Quell- zur Zielumgebung durchführen. Sie sollten den Übernahmeprozess mit den Geschäfts- und Anwendungsteams planen, um Unterbrechungen so geringfügig wie möglich zu halten und die Geschäftskontinuität nicht zu beeinträchtigen. 

> [!IMPORTANT]
> Weitere Informationen zu den Schritten für eine Übernahmemigration mithilfe von DMS finden Sie unter [Durchführen der Migrationsübernahme](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Nach der Migration

Nach dem erfolgreichen Abschluss der Migrationsphase sind einige weitere Schritte erforderlich, um sicherzustellen, dass alles so reibungslos und effizient wie möglich funktioniert. 

Die Postmigrationsphase ist unerlässlich, um Datengenauigkeitsprobleme abzustimmen, die Vollständigkeit zu bestätigen und Leistungsprobleme der Workload zu beheben. 

### <a name="monitor-and-remediate-applications"></a>Überwachen und Korrigieren von Anwendungen 
Nachdem Sie die Migration zu einer verwalteten Instanz abgeschlossen haben, sollten Sie das Anwendungsverhalten und die Anwendungsleistung Ihrer Workload nachverfolgen. Dieser Prozess umfasst die folgenden Aktivitäten:

- [Vergleichen der Leistung der Workload, die in der verwalteten Instanz ausgeführt wird,](sql-server-to-managed-instance-performance-baseline.md#compare-performance) mit der [Leistungsbaseline, die Sie für die SQL Server-Quellinstanz erstellt haben](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline)
- Kontinuierliches [Überwachen der Leistung Ihrer Workload](sql-server-to-managed-instance-performance-baseline.md#monitor-performance), um mögliche Probleme und Verbesserungen zu erkennen

### <a name="perform-tests"></a>Durchführen von Tests

Das Testvorgehen für die Datenbankmigration umfasst die folgenden Aktivitäten:

1. **Entwickeln von Validierungstests**: Für das Testen der Datenbankmigration müssen Sie SQL-Abfragen verwenden. Sie müssen die Validierungsabfragen erstellen, die für die Quell- und die Zieldatenbank ausgeführt werden sollen. Ihre Validierungsabfragen sollten den von Ihnen definierten Bereich abdecken.
1. **Einrichten der Testumgebung**: Die Testumgebung sollte eine Kopie der Quelldatenbank und der Zieldatenbank enthalten. Stellen Sie sicher, dass Sie die Testumgebung isolieren.
1. **Ausführen der Validierungstests**: Führen Sie die Validierungstests für die Quelle und das Ziel aus, und analysieren Sie anschließend die Ergebnisse.
1. **Ausführen von Leistungstests**: Führen Sie Leistungstests für die Quelle und das Ziel aus, und analysieren und vergleichen Sie anschließend die Ergebnisse.


## <a name="leverage-advanced-features"></a>Nutzen der erweiterten Features 

Sie sollten die erweiterten cloudbasierten Features von SQL Managed Instance nutzen, z. B. die [integrierte Hochverfügbarkeit](../../database/high-availability-sla.md), die [Bedrohungserkennung](../../database/azure-defender-for-sql.md) und die [Überwachung und Optimierung Ihrer Workloads](../../database/monitor-tune-overview.md). 

Die [Azure SQL-Analyse](../../../azure-sql/database/monitor-tune-overview.md) bietet eine zentralisierte Überwachung für große Mengen verwalteter Instanzen.

Einige SQL Server-Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 


## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu Azure SQL Managed Instance finden Sie unter:
   - [Dienstebenen für Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Unterschiede zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).
