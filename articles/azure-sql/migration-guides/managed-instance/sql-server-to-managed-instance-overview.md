---
title: 'SQL Server zu SQL Managed Instance: Übersicht zur Migration'
description: In diesem Artikel lernen Sie die Tools und Optionen kennen, die zum Migrieren Ihrer SQL Server-Datenbanken zu Azure SQL Managed Instance verfügbar sind.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 09/07/2021
ms.openlocfilehash: 8c44d6e92f2943f3c565e80d42d9d0c474fddd4f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542249"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Migrationsübersicht: SQL Server zu Azure SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Erfahren Sie mehr über die Optionen und Überlegungen zur Migration Ihrer SQL Server-Datenbanken auf Azure SQL Managed Instance. 

Sie können SQL Server-Datenbanken migrieren, die vor Ort oder auf dem Server laufen: 

- SQL Server auf Azure Virtual Machines  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2) 
- AWS Relational Database Service (RDS) 
- Compute Engine in Google Cloud Platform (GCP)  
- Cloud-SQL für SQL Server in GCP 

Weitere Migrationsleitfäden finden Sie im [Leitfaden zur Azure-Datenbankmigration](/data-migration). 

## <a name="overview"></a>Übersicht

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) ist eine empfohlene Zieloption für SQL Server-Workloads, die einen vollständig verwalteten Dienst erfordern, ohne dass Sie VMs (virtuelle Computer) oder ihre Betriebssysteme verwalten müssen. Mit SQL Managed Instance können Sie Ihre On-Premises-Anwendungen mit minimalen Anwendungs- oder Datenbankänderungen nach Azure verlagern. Sie bietet eine umfassende Isolation ihrer Instanzen mit nativer Unterstützung für virtuelle Netzwerke. 

Überprüfen Sie die in [Azure SQL Managed Instance verfügbaren Features](../../database/features-comparison.md) der SQL Server-Datenbank-Engine, um die Unterstützungsfähigkeit Ihres Migrationsziels sicherzustellen.  

## <a name="considerations"></a>Überlegungen 

Dies sind die wichtigsten Faktoren, die Sie bei der Bewertung von Migrationsoptionen berücksichtigen sollten: 
- Anzahl der Server und Datenbanken
- Größe der Datenbanken
- Akzeptable Downtime für das Geschäft während des Migrationsprozesses 

Einer der Hauptvorteile der Migration Ihrer SQL Server-Datenbanken auf SQL Managed Instance ist, dass Sie wählen können, ob Sie die gesamte Instanz oder nur eine Teilmenge einzelner Datenbanken migrieren möchten. Planen Sie sorgfältig, Folgendes in den Migrationsprozess miteinzubeziehen: 
- Alle Datenbanken, die in derselben Instanz zusammengestellt werden müssen 
- Objekte auf Instanzebene, die für Ihre Anwendung erforderlich sind, einschließlich Anmeldungen, Anmeldeinformationen, SQL-Agent-Aufträge und -Operatoren sowie Trigger auf Serverebene. 

> [!NOTE]
> Azure SQL Managed Instance gewährleistet auch bei kritischen Szenarien eine Verfügbarkeit von 99,99 Prozent. Der durch einige Features in SQL Managed Instance verursachte mehr Aufwand kann nicht deaktiviert werden. Weitere Informationen finden Sie in den [Hauptgründen der Leistungsunterschiede zwischen SQL Managed Instance und SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) Blogeintrag. 


## <a name="choose-an-appropriate-target"></a>Wählen Sie ein passendes Ziel aus.

Im Folgenden finden Sie einige allgemeine Richtlinien, die Ihnen bei der Auswahl der richtigen Dienstebene und Merkmale von SQL Managed Instance helfen, die Ihrer [Leistungsbaseline](sql-server-to-managed-instance-performance-baseline.md) entsprechen: 

- Verwenden Sie die Baseline für die CPU-Auslastung, um eine verwaltete Instanz bereitzustellen, die der Anzahl der Kerne entspricht, die Ihre Instanz von SQL Server verwendet. Es kann erforderlich sein, dass Ressourcen entsprechend der [Merkmale der Hardwaregeneration](../../managed-instance/resource-limits.md#hardware-generation-characteristics) skaliert werden müssen. 
- Verwenden Sie die Baseline für die Arbeitsspeicherauslastung, um eine [Option für die virtuellen Kerne](../../managed-instance/resource-limits.md#service-tier-characteristics) auszuwählen, die Ihrer Arbeitsspeicherbelegung entspricht. 
- Verwenden Sie die Baseline für die E/A-Latenz des Dateisubsystems, um zwischen den Dienstebenen „Universell“ (Latenz höher als 5 ms) und „Unternehmenskritisch“ (Latenz geringer als 3 ms) auszuwählen. 
- Verwenden Sie die Baseline für den Durchsatz, um die Größe der Daten und Protokolldateien im Voraus zuzuweisen, um die erwartete E/A-Leistung zu erzielen. 

Während der Bereitstellung können Sie Compute- und Speicherressourcen auswählen und anschließend über das [Azure-Portal](../../database/scale-resources.md) ändern, ohne dass es zu Downtime Ihrer Anwendung kommt. 

> [!IMPORTANT]
> Jede Abweichung in den [Anforderungen an virtuelle Netzwerke für verwaltete Instanzen](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kann Sie am Erstellen neuer Instanzen oder Verwenden vorhandener Instanzen hindern. Erfahren Sie mehr über das  [Erstellen neuer](../../managed-instance/virtual-network-subnet-create-arm-template.md) und [Konfigurieren vorhandener](../../managed-instance/vnet-existing-add-subnet.md) Netzwerke. 

Eine weitere wichtige Überlegung bei der Auswahl des Zieldienst-Tiers in Azure SQL Managed Instance (Universell versus Unternehmenskritisch) ist die Verfügbarkeit bestimmter Funktionen, wie In-Memory OLTP, die nur in einer unternehmenskritischen Ebene verfügbar sind. 

### <a name="sql-server-vm-alternative"></a>Alternative für SQL Server-VMs

Ihr Unternehmen weist möglicherweise Anforderungen auf, die [Azure Virtual Machines zu einem besseren Ziel für SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) als Azure SQL Managed Instance machen. 

Wenn eine der folgenden Bedingungen für Ihr Unternehmen zutrifft, erwägen Sie, stattdessen zu einer SQL Server virtuellen Maschine (VM) zu wechseln: 

- Sie benötigen direkten Zugriff auf das Betriebs- oder Dateisystem, z. B. um Drittanbieter- oder benutzerdefinierte Agents auf der gleichen VM wie SQL Server zu installieren. 
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z. B. FileStream/FileTable, PolyBase und instanzübergreifende Transaktionen. 
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z. B. 2012) bleiben müssen. 
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z. B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt. 

## <a name="migration-tools"></a>Migrationstools

Wir empfehlen die folgenden Migrationstools: 

|Technologie | BESCHREIBUNG|
|---------|---------|
|[Azure SQL-Migrationserweiterung für Azure Data Studio](../../../dms/migration-using-azure-data-studio.md)  | Durch die Azure SQL-Migrationserweiterung für Azure Data Studio werden Ihnen SQL Server-Bewertungs- und -Migrationsfunktionen in Azure Data Studio zur Verfügung gestellt. Migrationen können entweder im Onlinemodus (für Migrationen, die eine minimale Downtime erfordern) oder Offlinemodus (für Migrationen, bei denen die Downtime über die gesamte Dauer der Migration besteht) ausgeführt werden. |
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Dieser Azure-Dienst hilft Ihnen, Ihren SQL-Datenbestand auf VMware im Maßstab zu erkennen und zu bewerten. Er bietet Empfehlungen für die Azure SQL-Bereitstellung, Zielgrößen und monatliche Schätzungen. | 
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Dieser Azure-Dienst unterstützt die Migration im Offline-Modus für Anwendungen, die sich eine Ausfallzeit während des Migrationsprozesses leisten können. Im Gegensatz zur kontinuierlichen Migration im Onlinemodus führt die Migration im Offlinemodus eine einmalige Wiederherstellung einer vollständigen Datenbanksicherung von der Quelle zum Ziel aus. | 
|[Native Sicherung und Wiederherstellung](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance die Wiederherstellung nativer SQL Server Datenbanksicherungen (BAK-Dateien). Es ist die einfachste Migrationsoption für Kunden, die vollständige Datenbank-Backups in Azure Storage bereitstellen können. Vollständige und differenzielle Sicherungen werden ebenfalls unterstützt und später in diesem Artikel im Abschnitt [Migrationsressourcen](#migration-assets) dokumentiert.| 
|[Protokollwiedergabedienst](../../managed-instance/log-replay-service-migrate.md) | Dieser Cloud-Dienst ist für SQL Managed Instance auf der Grundlage der SQL Server-Protokollversandtechnologie aktiviert. Es ist eine Migrationsoption für Kunden, die Voll-, Differenz- und Protokolldatenbank-Backups auf Azure Storage bereitstellen können. Log Replay Service wird zum Wiederherstellen von Sicherungsdateien aus Azure Blob Storage in SQL Managed Instance verwendet.| 
| | |

In der folgenden Tabelle werden alternative Migrationstools aufgeführt: 

|**Technologie** |**Beschreibung**  |
|---------|---------|
|[Transaktionsreplikation](../../managed-instance/replication-transactional-overview.md) | Mit diesem Tool können Daten aus Datenbanktabellen der SQL Server-Quelle in SQL Managed Instance repliziert werden, indem eine Migrationsoption vom Typ „Verleger/Abonnent“ angegeben und die Transaktionskonsistenz beibehalten wird. | 
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| Mit dem [Tool zum Massenkopieren](/sql/tools/bcp-utility) werden Daten aus einer SQL Server-Instanz in eine Datendatei kopiert. Verwenden Sie das Tool zum Massenkopieren, um die Daten aus Ihrer Quelle zu exportieren und die Datendatei in das SQL Managed Instance-Ziel zu importieren. </br></br> Für Vorgänge mit hoher Geschwindigkeit bei Massenkopien zum Verschieben von Daten auf Azure SQL Managed Instance können Sie das [Tool Smart Bulk Copy ](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)verwenden, um die Übertragungsgeschwindigkeit zu maximieren, indem Sie parallele Kopieraufgaben nutzen. | 
|[Assistent zum Importieren/Exportieren von BACPAC-Dateien](../../database/database-import.md?tabs=azure-powershell)| Eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei ist eine Windows-Datei mit der Erweiterung „.bacpac“, die das Schema und die Daten einer Datenbank kapselt. BACPAC-Dateien können zum Exportieren von Daten aus einer SQL Server-Quelle und zum Importieren der Datei in Azure SQL Managed Instance verwendet werden. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  Die [Kopieraktivität](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migriert Daten aus SQL Server-Datenbank-Quellinstanzen in SQL Managed Instance mithilfe integrierter Connectors und einer [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory unterstützt ein breites Spektrum von [Connectors](../../../data-factory/connector-overview.md), um Daten von SQL Server-Quellen in SQL Managed Instance zu verschieben. |

## <a name="compare-migration-options"></a>Vergleich der Migrationsoptionen

Im Folgenden können Sie die Migrationsoptionen vergleichen, um die richtige Vorgehensweise für Ihre Geschäftsanforderungen zu wählen. 

In der folgenden Tabelle werden die von uns empfohlenen Migrationsoptionen verglichen: 

|Migrationsoption  |Verwendung  |Überlegungen  |
|---------|---------|---------|
|[Azure SQL-Migrationserweiterung für Azure Data Studio](../../../dms/migration-using-azure-data-studio.md) | – Migrieren einzelner oder mehrerer Datenbanken nach Maß </br> – Kann sowohl im Onlinemodus (minimale Downtime) als auch im Offlinemodus (akzeptable Downtime) ausgeführt werden </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM |  – Einfache Einrichtung und einfacher Einstieg </br> – Erfordert die Einrichtung der selbstgehosteten Integration Runtime für den Zugriff auf lokale SQL Server und Sicherungen </br> – Erweiterte Bewertungs- und Migrationsfunktionen |
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrieren einzelner oder mehrerer Datenbanken nach Maß </br> - Kann Ausfallzeiten während des Migrationsprozesses einkalkulieren. </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM |  – Migrationen im großen Stil können mit [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) automatisiert werden. </br> - Die Zeit bis zum Abschluss der Migration hängt von der Datenbankgröße ab und wird von der Sicherungs- und Wiederherstellungszeit beeinflusst. </br> - Es kann eine ausreichende Ausfallzeit erforderlich sein. |
|[Native Sicherung und Wiederherstellung](../../managed-instance/restore-sample-database-quickstart.md) | – Migrieren individueller Branchenanwendungsdatenbanken  </br> – Schnelle und einfache Migration ohne separaten Migrationsdienst oder ein Tool  </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | - Die Datenbanksicherung verwendet mehrere Threads, um die Datenübertragung zu Azure Blob Storage zu optimieren, aber die Bandbreite des Partners und die Datenbankgröße können die Übertragungsrate beeinflussen. </br> – Die Downtime sollte den Zeitaufwand zum Durchführen einer vollständigen Sicherung und Wiederherstellung berücksichtigen (dieser Vorgang ist von der Datengröße abhängig).| 
|[Protokollwiedergabedienst](../../managed-instance/log-replay-service-migrate.md) | – Migrieren individueller Branchenanwendungsdatenbanken  </br> – Für Datenbankmigrationen ist mehr Kontrolle erforderlich.  </br> </br> Unterstützte Quellen: </br> - SQL Server (2008 to 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Die Migration umfasst das Erstellen vollständiger Datenbanksicherungen auf SQL Server und das Kopieren von Sicherungsdateien zu Azure Blob Storage. Log Replay Service wird zum Wiederherstellen von Sicherungsdateien aus Azure Blob Storage in SQL Managed Instance verwendet. </br> - Datenbanken, die während des Migrationsprozesses wiederhergestellt werden, befinden sich in einem Wiederherstellungsmodus und können nicht zum Lesen oder Schreiben verwendet werden, bis der Prozess abgeschlossen ist.| 
| | | |

In der folgenden Tabelle werden die alternativen Migrationsoptionen verglichen: 

|Methode oder Technologie |Verwendung |Überlegungen  |
|---------|---------|---------|
|[Transaktionsreplikation](../../managed-instance/replication-transactional-overview.md) | – Migrieren durch kontinuierliches Veröffentlichen von Änderungen an Quelldatenbanktabellen an die Zieldatenbanktabellen von SQL Managed Instance </br> – Führen Sie eine vollständige oder teilweise Datenbankmigrationen ausgewählter Tabellen (Teilmenge der Datenbank) aus.  </br> </br> Unterstützte Quellen: </br> - SQL Server (2012 bis 2019) mit einigen Einschränkungen </br> – AWS EC2  </br> – GCP Compute mit SQL Server-VM | </br> – Das Setup ist im Vergleich zu anderen Migrationsoptionen relativ komplex.   </br> – Bietet eine kontinuierliche Replikationsoption zum Migrieren von Daten (ohne die Datenbanken offline zu schalten)</br> - Bei der transaktionalen Replikation gibt es Einschränkungen zu beachten, wenn Sie den Verleger auf der SQL Server-Quellinstanz einrichten. Weitere Informationen finden Sie unter [Einschränkungen für das Veröffentlichen von Objekten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects).  </br> – Eine Funktion zum [Überwachen der Replikationsaktivität](/sql/relational-databases/replication/monitor/monitoring-replication) ist verfügbar.    |
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Führen Sie vollständige oder teilweise Datenmigrationen durch. </br> – Downtime ist möglich </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM   | - Erfordert Downtime für das Exportieren von Daten aus der Quelle und das Importieren in das Ziel. </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen. |
|[Assistent zum Importieren/Exportieren von BACPAC-Dateien](../../database/database-import.md)| – Migrieren individueller Branchenanwendungsdatenbanken </br>– Für kleinere Datenbanken geeignet  </br>  – Erfordert keinen separaten Migrationsdienst oder ein Tool </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM  |   </br> – Erfordert Downtime, da Daten aus der Quelle exportiert und im Ziel importiert werden müssen.   </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen, um Kürzungen oder Fehler durch nicht übereinstimmende Datentypen zu vermeiden. </br> – Die Zeit zum Exportieren einer Datenbank mit einer großen Anzahl von Objekten kann deutlich höher sein. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| – Migrieren und/oder Transformieren von Daten aus SQL Server-Quelldatenbanken.</br> – Zusammenführen von Daten aus mehreren Datenquellen in Azure SQL Managed Instance (wird in der Regel für Business Intelligence-Workloads verwendet).   </br> – Erfordert das Erstellen von Datenverschiebungspipelines in Data Factory zum Verschieben von Daten aus der Quelle zum Ziel.   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) sind ein wichtiger Aspekt, der auf den Faktoren der Pipelinetriggern, den Aktivitätsausführungen, der Dauer der Datenverschiebung und mehr basiert. |
| | | |

## <a name="feature-interoperability"></a>Featureinteroperabilität 

Beim Migrieren von Workloads, die andere SQL Server-Features nutzen, gibt es weitere Aspekte, die Sie berücksichtigen werden müssen. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrieren Sie SSIS-Pakete (SQL Server Integration Services) und -Projekte in SSISDB mithilfe von [Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md) zu Azure SQL Managed Instance. 

Nur SSIS-Pakete in SSISDB ab SQL Server 2012 werden für die Migration unterstützt. Konvertieren Sie ältere SSIS-Pakete vor der Migration. Weitere Informationen finden Sie unter [Tutorial für die Konvertierung von Projekten](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model). 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Sie können SQL Server Reporting Services-Berichte (SSRS)zu paginierten Berichten in Power BI migrieren. Verwenden Sie das  [RDL-Migrationstool](https://github.com/microsoft/RdlMigration), um die Vorbereitung und Migration Ihrer Berichte zu unterstützen. Microsoft hat dieses Tool entwickelt, um Kunden bei der Migration von Report Definition Language (RDL)-Berichten von ihren SSRS-Servern zu Power BI zu unterstützen. Es ist auf GitHub verfügbar und dokumentiert eine exemplarische End-to-End-Vorgehensweise des Migrationsszenarios. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services-Tabellenmodelle von SQL Server 2012 und höher können zu Azure Analysis Services migriert werden, das ein Plattform-as-a-Service (PaaS)-Bereitstellungsmodell für das Analysis Services-Tabellenmodell in Azure ist. Weitere Informationen über die Migration lokaler Modelle zu Azure Analysis Services finden Sie in diesem [Videotutorial](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Alternativ können Sie die Migration Ihrer lokalen tabellarischen Analysis Services-Modelle zu [Power BI Premium mithilfe der neuen XMLA-Endpunkte für Lese- und Schreibvorgänge](/power-bi/admin/service-premium-connect-tools) in Betracht ziehen. 

### <a name="high-availability"></a>Hochverfügbarkeit

Die Funktionen der SQL Server-Hochverfügbarkeit Always On Failover-Cluster-Instanzen und Always On-Verfügbarkeitsgruppen werden auf der verwalteten SQL-Zielinstanz obsolet. Hoch Verfügbarkeitsarchitektur ist bereits in [universell (Standard-Verfügbarkeitsmodell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) und [unternehmenskritisch (Premium Availability Model)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) - Dienstebenen für SQL Managed Instance. Das Premium-Verfügbarkeitsmodell stellt auch die horizontale Leseskalierung bereit, die das Herstellen einer Verbindung mit einem der sekundären Knoten für schreibgeschützte Zwecke ermöglicht.     

Über die Hochverfügbarkeitsarchitektur hinaus, die in SQL Managed Instance enthalten ist, können Sie mit der Funktion für [automatische Failover-Gruppen](../../database/auto-failover-group-overview.md) die Replikation und das Failover von Datenbanken in einer verwalteten Instanz in eine andere Region verwalten. 

### <a name="sql-agent-jobs"></a>SQL-Agent-Aufträge

Verwenden Sie die Azure Database Migration Service-Offlineoption zum Migrieren von [SQL-Agent-Aufträgen](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Andernfalls müssen Sie die Aufträge mithilfe von SSMS (SQL Server Management Studio) in Transact-SQL (T-SQL) schreiben und dann im SQL Managed Instance-Ziel manuell neu erstellen. 

> [!IMPORTANT]
> Derzeit unterstützt der Azure Database Migration Service nur Aufträge mit T-SQL-Subsystemschritten. Aufträge mit SSIS-Paketschritten müssen manuell migriert werden. 

### <a name="logins-and-groups"></a>Anmeldungen und Gruppen

Verschieben Sie SQL-Logins von der SQL Server-Quelle zu Azure SQL Managed Instance, indem Sie den Datenbankmigrationsdienst im Offline-Modus verwenden.  Verwenden Sie das Blatt [Anmeldungen auswählen](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) im Migrations-Assistent, um Anmeldungen in Ihr SQL Managed Instance-Ziel zu migrieren. 

Standardmäßig unterstützt Azure Database Migration Service nur das Migrieren von SQL-Anmeldungen. Sie können allerdings die Migration von Windows-Anmeldungen wie folgt aktivieren:

- Stellen Sie sicher, dass die verwaltete Zielinstanz von SQL Azure Active Directory (Azure AD) Lesezugriff hat. Ein Benutzer, der über die Rolle „globaler Administrator“ verfügt, kann diesen Zugriff über den Azure-Portal konfigurieren.
- Konfigurieren von Azure Database Migration Service, um Windows-Benutzer-oder Gruppe-Anmeldemigrationen zu aktivieren. Sie richten dies über den Azure-Portal auf der Seite **Konfiguration** ein. Nachdem Sie diese Einstellung aktiviert haben, starten Sie den Dienst neu, damit die Änderungen übernommen werden.

Nach dem Neustart des Diensts werden die Windows-Anmeldungen der Benutzer/Gruppen in der Liste der für die Migration verfügbaren Anmeldungen angezeigt. Für alle Windows-Anmeldungen von Benutzern/Gruppen, die Sie migrieren, werden Sie aufgefordert, den zugeordneten Domänennamen anzugeben. Dienstbenutzerkonten (Konten mit dem Domänennamen NT AUTHORITY) und virtuelle Benutzerkonten (Konten mit dem Domänennamen NT SERVICE) werden nicht unterstützt. Weitere Informationen finden Sie unter [Migrieren von Windows-Benutzern und -Gruppen in einer SQL Server-Instanz zu Azure SQL Managed Instance mithilfe von T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternativ können Sie das [PowerShell-Hilfstool](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) verwenden, das speziell von den Datenmigrationsarchitekten von Microsoft entworfen wurde. Das Hilfsprogramm verwendet PowerShell, um ein T-SQL-Skript zum Neuerstellen von Anmeldungen und ausgewählten Datenbankbenutzern aus der Quelle im Ziel zu erstellen. 

Das PowerShell-Dienstprogramm ordnet Windows Server Active Directory-Konten automatisch Azure AD-Konten zu und kann für jede Anmeldung einen UPN-Abgleich mit der Active Directory-Quellinstanz durchführen. Das Tool erstellt benutzerdefinierte Server- und Datenbankrollen sowie Rollenmitgliedschaften, Datenbankrollen und Benutzerberechtigungen. Eigenständige Datenbanken werden noch nicht unterstützt, und nur eine Teilmenge der möglichen SQL Server-Berechtigungen werden erstellt. 

### <a name="encryption"></a>Verschlüsselung

Wenn Sie Datenbanken migrieren, die mit  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) geschützt werden, zu einer verwalteten Instanz mithilfe der nativen Wiederherstellungsoption [müssen Sie das entsprechende Zertifikat aus der SQL Server-Quelle zum SQL Managed Instance-Ziel migrieren](../../managed-instance/tde-certificate-migrate.md), *bevor* die Datenbankwiederherstellung durchgeführt wird. 

### <a name="system-databases"></a>Systemdatenbanken

Das Wiederherstellen von Systemdatenbanken wird nicht unterstützt. Um Objekte auf Instanzebene zu migrieren (die in den Datenbanken „master“ und „msdb“ gespeichert sind), erstellen Sie ein Skript mit T-SQL und legen Sie sie dann auf der verwalteten Zielinstanz neu an. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory-OLTP (speicheroptimierte Tabellen)

SQL Server stellt eine In-Memory OLTP-Funktion bereit. Sie ermöglicht die Verwendung von speicheroptimierten Tabellen, speicheroptimierten Tabellentypen und nativ kompilierten SQL-Modulen, um Arbeitslasten auszuführen, die einen hohen Durchsatz und eine niedrige Latenz für die transaktionale Verarbeitung erfordern. 

> [!IMPORTANT]
> In-Memory OLTP wird nur im unternehmenskritische Ebene in Azure SQL Managed Instance unterstützt. Es wird in der Dienstebene „Universell“ nicht unterstützt.

Wenn Sie speicheroptimierte Tabellen oder speicheroptimierte Tabellentypen in Ihrer lokalen SQL Server-Instanz haben und Sie zu Azure SQL Managed Instance migrieren möchten, sollten Sie entweder:

- Wählen Sie die Dienstebene „Unternehmenskritisch“ für Ihre SQL Managed Instance-Zielinstanz, die In-Memory-OLTP unterstützt.
- Wenn Sie zur Dienstebene „Universell“ für Azure SQL Managed Instance migrieren möchten, entfernen Sie die speicheroptimierten Tabellen, speicheroptimierten Tabellentypen und die nativ kompilierten SQL-Module, die mit speicheroptimierten Objekten interagieren, bevor Sie Ihre Datenbank(en) migrieren. Sie können die folgende T-SQL-Abfrage verwenden, um alle Objekte zu identifizieren, die vor der Migration zur Dienstebene „Universell“ entfernt werden müssen:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Weitere Informationen zu In-Memory-Technologien finden Sie unter [Optimieren der Leistung mithilfe von In-Memory-Technologien in Azure SQL-Datenbank und Azure SQL Managed Instance](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Erweiterte Funktionen 

Nutzen Sie unbedingt die erweiterten Cloud-basierten Funktionen von SQL Managed Instance. Zum Beispiel müssen Sie sich nicht um die Verwaltung von Backups kümmern, da der Dienst dies für Sie übernimmt. Sie können [einen beliebigen Zeitpunkt im Aufbewahrungszeitraum](../../database/recovery-using-backups.md#point-in-time-restore) wiederherstellen. Darüber hinaus müssen Sie sich nicht um die Einrichtung von Hochverfügbarkeit kümmern, denn  [Hochverfügbarkeit ist bereits integriert in](../../database/high-availability-sla.md). 

Sie sollten außerdem die  [Azure AD-Authentifizierung](../../database/authentication-aad-overview.md), die [Überwachung](../../managed-instance/auditing-configure.md), die  [Bedrohungserkennung](../../database/azure-defender-for-sql.md), die  [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und die  [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) verwenden, um die Sicherheit zu erhöhen.

Zusätzlich zu erweiterten Verwaltungs- und Sicherheitsfeatures stellt SQL Managed Instance erweiterte Tools bereit, die Sie beim [Überwachen und Optimieren Ihrer Workload](../../database/monitor-tune-overview.md) unterstützen. Mit der [Azure SQL-Analyse](../../../azure-monitor/insights/azure-sql.md) können Sie eine große Gruppe verwalteter Instanzen zentral überwachen. Die [automatische Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in verwalteten Instanzen überwacht kontinuierlich die Leistung der Ausführung Ihres SQL-Tarifs und behebt identifizierte Leistungsprobleme automatisch. 

Einige Features sind nur verfügbar, wenn der [Datenbank-Kompatibilitätsgrad](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) in den neuesten Kompatibilitätsgrad (150) geändert wurde. 

## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Unterstützung finden Sie in den folgenden Ressourcen, die für reale Migrationsprojekte entwickelt wurden.

|Asset  |BESCHREIBUNG  |
|---------|---------|
|[Data Workload Assessment Model and Tool (Datenarbeitsauslastungs-Bewertungsmodell und -tool)](https://www.microsoft.com/download/details.aspx?id=103130)| Dieses Tool stellt für eine bestimmte Arbeitsauslastung Informationen zu empfohlenen „optimalen“ Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch einen automatisierten und einheitlichen Entscheidungsprozess für die Zielplattform dabei helfen, Bewertungen von umfangreichen Datenbeständen zu beschleunigen.|
|[Hilfsprogramm zum Verschieben lokaler SQL Server-Anmeldungen zu Azure SQL Managed Instance](https://www.microsoft.com/download/details.aspx?id=103111)|Hierbei handelt es sich um ein PowerShell-Skript kann ein T-SQL-Befehlsskript erstellen, um Anmeldungen und ausgewählte Datenbankbenutzer aus der lokalen SQL Server-Instanz in Azure SQL Managed Instance neu zu erstellen. Das Tool ermöglicht die automatische Zuordnung von Windows Server Active Directory-Konten zu Azure AD-Konten, zusammen mit der optionalen Migration nativer SQL Server-Logins.|
|[Automatisierung der PerfMon-Datensammlung mithilfe von Logman](https://www.microsoft.com/download/details.aspx?id=103114)|Sie können das Logman-Tool verwenden, um PerfMon-Daten zu erfassen (damit Sie die grundlegende Leistung besser verstehen können) und Empfehlungen für die Migration der virtuellen Maschine zu erhalten. Das Tool verwendet die ausführbare Datei „logman.exe“, um den Befehl zu erstellen, der die Leistungsindikatoren für eine SQL Server-Remoteinstanz erstellt, startet, beendet und löscht.|

Das Data SQL Engineering-Team hat diese Ressourcen entwickelt. Die Hauptanwendung dieses Teams besteht darin, die komplexe Modernisierung für Datenplattform-Migrationsprojekte auf der Azure-Datenplattform von Microsoft freizugeben und zu beschleunigen.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Beginn der Migration Ihrer SQL Server-Dankenbanken zu Azure SQL Managed Instance finden Sie im [Leitfaden für die Migration von SQL Server zu SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Eine Matrix von Diensten und Werkzeugen, die Sie bei Datenbank- und Datenmigrationsszenarien sowie bei speziellen Aufgaben unterstützen können, finden Sie [unter Dienste und Werkzeuge für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu Azure SQL Managed Instance finden Sie unter:
   - [Dienstebenen für Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Unterschiede zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 

- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für Kostenermittlung und Größenanpassung von Workloads, die zu Azure migriert werden](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Einzelheiten zur Durchführung von A/B-Tests auf der Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).
