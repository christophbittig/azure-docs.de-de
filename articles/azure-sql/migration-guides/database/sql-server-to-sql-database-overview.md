---
title: 'Migrationsleitfaden: SQL-Server zu Azure SQL-Datenbank'
description: In diesem Artikel lernen Sie die verschiedenen Tools und Optionen kennen, die zum Migrieren Ihrer SQL Server-Datenbanken zu Azure SQL-Datenbank verfügbar sind.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: f5e581e979a1c02d58b9ef9f89bc43b06117ddfb
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371921"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Migrationsübersicht: SQL Server zu Azure SQL-Datenbank
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Erfahren Sie mehr über die Optionen und Überlegungen zur Migration Ihrer SQL Server-Datenbanken auf Azure SQL Database. 

Sie können vorhandene SQL Server-Datenbanken migrieren, die auf folgenden Plattformen ausgeführt werden: 

- Lokaler SQL Server.  
- SQL Server auf Azure Virtual Machines
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2)
- AWS Relational Database Service (RDS)
- Compute Engine in Google Cloud Platform (GCP)  
- Cloud-SQL für SQL Server in GCP 

Weitere Migrationsleitfäden finden Sie im [Leitfaden zur Azure-Datenbankmigration](/data-migration). 

## <a name="overview"></a>Übersicht

[Azure SQL-Datenbank](../../database/sql-database-paas-overview.md) ist eine empfohlene Zieloption für SQL Server-Arbeitsauslastungen, die eine vollständig verwaltete Platform-as-a-Service-Lösung (PaaS) erfordern. SQL-Datenbanken führen die meisten Funktionen des Datenbankmanagements durch. In sie ist Hochverfügbarkeit, Skalierbarbeit und Leistungsfunktionen für viele Anwendungstypen integriert. 

In SQL-Datenbank sind zudem mehrere [Bereitstellungsmodelle](../../database/sql-database-paas-overview.md#deployment-models) und [Dienstebenen](../../database/service-tiers-vcore.md#service-tiers) verfügbar, die für Flexibilität sorgen und für unterschiedliche Typen von Anwendungen oder Arbeitsauslastungen geeignet sind.

Einer der wichtigsten Vorteile der Migration in SQL-Datenbank besteht darin, dass Sie Ihre Anwendung mithilfe der PaaS-Funktionen modernisieren können. Sie können dann jede Abhängigkeit von technischen Komponenten eliminieren, die auf die Instanzebene beschränkt sind, wie z. b. SQL-Agentaufträge.

Sie können auch Kosten sparen, indem Sie den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) für SQL Server verwenden, um Ihre lokalen Lizenzen von SQL Server zu Azure SQL-Datenbank zu migrieren. Diese Option ist verfügbar, wenn Sie das [v-Core-basierte Kaufmodell](../../database/service-tiers-vcore.md) auswählen.

Überprüfen Sie die in [Azure SQL-Datenbank verfügbaren Features](../../database/features-comparison.md) der SQL Server-Datenbank-Engine, um die Unterstützungsfähigkeit Ihres Migrationsziels sicherzustellen.  

## <a name="considerations"></a>Überlegungen 

Dies sind die wichtigsten Faktoren, die Sie bei der Bewertung von Migrationsoptionen berücksichtigen sollten: 

- Anzahl der Server und Datenbanken
- Größe der Datenbanken
- Akzeptable Downtime für das Geschäft während des Migrationsprozesses 

Die in diesem Leitfaden aufgeführten Migrationsoptionen berücksichtigen diese Faktoren. Bei der logischen Datenmigration zu Azure SQL-Datenbank kann die Migrationsdauer sowohl von der Anzahl der Objekte in einer Datenbank als auch von der Größe der Datenbank abhängen. 

Für verschiedene Arbeitsauslastungen und Benutzereinstellungen stehen unterschiedliche Tools zur Verfügung. Mit einigen Tools kann eine schnelle Migration einer einzelnen Datenbank über ein benutzeroberflächenbasiertes Tool durchgeführt werden. Mit anderen Tools können Sie die Migration mehrerer Datenbanken automatisieren, um Migrationen im großen Stil durchzuführen. 

## <a name="choose-an-appropriate-target"></a>Wählen Sie ein passendes Ziel aus

Bei der Auswahl des richtigen Bereitstellungsmodells und der richtigen Dienstebene für Azure SQL-Datenbank sollten Sie einige allgemeine Richtlinien berücksichtigen. Während der Bereitstellung können Sie Compute- und Speicherressourcen auswählen und anschließend über das [anschließend über das Azure-Portal ändern](../../database/scale-resources.md), ohne dass es zu Downtime Ihrer Anwendung kommt.

**Bereitstellungsmodelle:** Sie sollten die Arbeitsauslastung und das Nutzungsmuster Ihrer Anwendung verstehen, um die Wahl zwischen einem Singleton und einem Pool für elastische Datenbanken zu treffen. 

- Bei einem [Singleton](../../database/single-database-overview.md) handelt es sich um eine vollständig verwaltete Datenbank, die für die meisten modernen Cloudanwendungen und Microservices geeignet ist.
- Ein [Pool für elastische Datenbanken](../../database/elastic-pool-overview.md) ist eine Sammlung von Singletons mit gemeinsam genutzten Ressourcen wie CPU oder Arbeitsspeicher. Er ist geeignet für die Zusammenfassung von Datenbanken in einem Pool mit vorhersehbaren Nutzungsmustern, die verschiedene Ressourcen effektiv gemeinsam nutzen.

**Kaufmodelle**: Wählen Sie zwischen dem virtuellen Kern, der Datenbanktransaktionseinheit (DTU) oder dem serverlosen Kaufmodell. 

- Mit dem [vCore-basierten Modell](../../database/service-tiers-vcore.md) können Sie die Anzahl von virtuellen Kernen für Ihre Instanz von Azure SQL-Datenbank selbst festlegen. Dies ist bei der Migration von einer lokalen SQL Server-Instanz die einfachste Option. Es ist außerdem die einzige Option, die Einsparungen bei den Lizenzkosten mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) unterstützt. 
- Beim [DTU-basierten Modell](../../database/service-tiers-dtu.md) werden die zugrunde liegenden Compute-, Arbeitsspeicher- und Eingabe/Ausgaberessourcen für eine gemischte DTU abstrahiert. 
- Das [serverlose Modell](../../database/serverless-tier-overview.md) ist für Arbeitsauslastungen vorgesehen, die automatische bedarfsgesteuerte Skalierung mit Computeressourcen erfordern, die pro Nutzungssekunde abgerechnet werden. Im Tarif „Serverloses Computing“ werden Datenbanken während inaktiver Phasen (in denen nur Speicher in Rechnung gestellt wird) automatisch angehalten. Datenbanken werden automatisch fortgesetzt, wenn die Aktivität fortgesetzt wird. 

**Dienstebenen:** Sie haben die Wahl zwischen drei Dienstebenen, die für verschiedene Anwendungstypen entwickelt wurden.

- Die [Dienstebene „Universell/Standard“](../../database/service-tier-general-purpose.md) bietet eine ausgeglichene budgetorientierte Option mit COMPUTE-und Speicherkapazitäten, die für die Bereitstellung von Anwendungen in der mittleren und niedrigeren Ebene geeignet ist. Redundanz ist auf der Speicherebene integriert und kann nach Fehlern wiederhergestellt werden. Sie eignet sich für die meisten Arbeitsauslastungen von Datenbanken. 
- Die [Dienstebene „Unternehmenskritisch/Premium“](../../database/service-tier-business-critical.md) ist für Anwendungen auf hoher Ebene vorgesehen, die hohe Transaktionsraten, E/A mit niedriger Latenz und ein hohes Maß an Resilienz erfordern. Sekundäre Replikate sind für das Failover und das Auslagern von Lese-Arbeitsauslastungen verfügbar.
- Die [Dienstebene „Hyperscale“](../../database/service-tier-hyperscale.md) wurde für Datenbanken mit wachsenden Datenvolumen konzipiert, die automatisch auf bis zu 100 TB skaliert werden müssen. Diese Option ist für sehr große Datenbanken konzipiert. 

> [!IMPORTANT]
> [Die Transaktionsprotokollrate wird in Azure SQL-Datenbank geregelt](../../database/resource-limits-logical-server.md#transaction-log-rate-governance), um hohe Erfassungsraten zu drosseln. Daher kann es während der Migration notwendig sein, Zieldatenbankressourcen (vCores/DTUs) zu skalieren, um die Last auf die CPU oder den Durchsatz zu verringern. Wählen Sie die Zieldatenbank mit der passenden Größe aus, aber planen Sie ein, die Ressourcen für die Migration bei Bedarf zu skalieren. 


### <a name="sql-server-vm-alternative"></a>Alternative für SQL Server-VMs

Ihr Unternehmen weist möglicherweise Anforderungen auf, die [Azure Virtual Machines für SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) zu einem besseren Ziel als Azure SQL-Datenbank machen. 

Wenn eine der folgenden Bedingungen für Ihr Unternehmen zutrifft, erwägen Sie, stattdessen zu einer SQL Server virtuellen Maschine (VM) zu wechseln: 

- Sie benötigen direkten Zugriff auf das Betriebs- oder Dateisystem, z. B. um Drittanbieter- oder benutzerdefinierte Agents auf der gleichen VM wie SQL Server zu installieren. 
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z. B. FileStream/FileTable, PolyBase und instanzübergreifende Transaktionen. 
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z. B. 2012) bleiben müssen. 
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z. B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt. 


## <a name="migration-tools"></a>Migrationstools 

Wir empfehlen die folgenden Migrationstools: 

|Technologie | Beschreibung|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Dieser Azure-Dienst hilft Ihnen, Ihren SQL-Datenbestand auf VMware im Maßstab zu erkennen und zu bewerten. Er bietet Empfehlungen für die Azure SQL-Bereitstellung, Zielgrößen und monatliche Schätzungen. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|Der Data Migration Assistant ist ein Desktoptool, das nahtlose SQL Server-Bewertungen und Migrationen zu Azure SQL-Datenbank (sowohl Schema als auch Daten) bereitstellt. </br></br>Das Tool kann lokal auf einem Server oder auf Ihrem lokalen Computer installiert werden, der mit ihren Quelldatenbanken verbunden ist. Bei der Migration handelt es sich um eine logische Datenverschiebung zwischen Objekten in der Quell- und der Zieldatenbank.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Dieser Azure-Dienst kann SQL Server-Datenbanken über das Azure-Portal oder automatisch mit PowerShell zu Azure SQL-Datenbank migrieren. In Database Migration Service müssen Sie bei der Bereitstellung ein bevorzugtes virtuelles Azure-Netzwerk (VNET) auswählen, um eine Verbindung mit Ihren SQL Server-Quelldatenbanken zu gewährleisten. Migrieren von Singletons oder im großen Stil. |
| | |


In der folgenden Tabelle werden alternative Migrationstools aufgeführt: 

|Technologie |BESCHREIBUNG  |
|---------|---------|
|[Transaktionsreplikation](../../database/replication-to-sql-database.md)|Mit diesem Tool können Daten aus Tabellen der SQL Server-Quelldatenbank in Azure SQL-Datenbank repliziert werden, indem eine Migrationsoption vom Typ „Herausgeber/Abonnent“ angegeben und die Transaktionskonsistenz beibehalten wird. Inkrementelle Datenänderungen werden an Abonnenten weitergegeben, sobald sie auf den Verlegern auftreten.|
|[Import/Export-Dienst/BACPAC](../../database/database-import.md)|Eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei ist eine Windows-Datei mit der Erweiterung „.bacpac“, die das Schema und die Daten einer Datenbank kapselt. BACPAC-Dateien können zum Exportieren von Daten aus einer SQL Server-Quelle und zum Importieren der Daten in Azure SQL-Datenbank verwendet werden. Eine BACPAC-Datei kann über das Azure-Portal in eine neue Azure SQL-Datenbank-Instanz importiert werden. </br></br> Für Skalierbarkeit und Leistung in großen Datenbanken oder bei einer großen Anzahl von Datenbanken sollten Sie das Befehlszeilen-Tool [SqlPackage](../../database/database-import.md#using-sqlpackage) in Betracht ziehen, um Datenbanken zu exportieren und zu importieren.|
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|Mit dem [Tool zum Massenkopieren](/sql/tools/bcp-utility) werden Daten aus einer SQL Server-Instanz in eine Datendatei kopiert. Verwenden Sie das Tool zum Exportieren der Daten aus Ihrer Quelle und zum Importieren der Datendatei in die Zielinstanz von SQL-Datenbank. </br></br> Für Vorgänge mit hoher Geschwindigkeit bei Massenkopien zum Verschieben von Daten auf Azure SQL-Datenbank können Sie das [intelligente Tool zum Massenkopieren ](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)verwenden, um die Übertragungsgeschwindigkeit zu maximieren, indem Sie parallele Kopieraufgaben nutzen.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|Die [Kopieraktivität](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migriert Daten aus SQL Server-Quelldatenbanken mithilfe integrierter Connectors und einer [Integration Runtime](../../../data-factory/concepts-integration-runtime.md) zu Azure SQL-Datenbank.</br> </br> Data Factory unterstützt ein breites Spektrum von [Konnektoren](../../../data-factory/connector-overview.md), um Daten von SQL Server-Quellen in Azure SQL-Datenbank zu verschieben.|
|[SQL-Datensynchronisierung](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL-Datensynchronisierung ist ein Dienst, der auf Azure SQL-Datenbank basiert und mit dem Sie ausgewählte Daten bidirektional über mehrere Datenbanken hinweg synchronisieren können, sowohl lokal als auch in der Cloud.</br>Die Datensynchronisierung ist nützlich, wenn Daten über mehrere Datenbanken in Azure SQL-Datenbank oder SQL Server hinweg auf dem neuesten Stand gehalten werden müssen.|
| | |

## <a name="compare-migration-options"></a>Vergleich der Migrationsoptionen

Im Folgenden können Sie die Migrationsoptionen vergleichen, um die richtige Vorgehensweise für Ihre Geschäftsanforderungen zu wählen. 

In der folgenden Tabelle werden die von uns empfohlenen Migrationsoptionen verglichen: 

|Migrationsoption  |Verwendung  |Überlegungen  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrieren von Singletons (Schema und Daten)  </br> – Während der Datenmigration ist Downtime möglich. </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Bei der Migration werden Daten zwischen Datenbankobjekten (von der Quelle zum Ziel) verschoben. Daher wird empfohlen, diese außerhalb der Spitzenzeiten auszuführen. </br> - Data Migration Assistant meldet den Status der Migration pro Datenbankobjekt einschließlich der Anzahl der migrierten Zeilen.  </br> – Bei großen Migrationen (Anzahl der Datenbanken/Größe der Datenbank) sollten Sie den unten aufgeführten Dienst „Azure Database Migration Service“ verwenden.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrieren von Singletons oder im großen Stil. </br> - Kann Ausfallzeiten während des Migrationsprozesses einkalkulieren. </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM | – Migrationen im großen Stil können mit [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md) automatisiert werden. </br> – Die Zeit bis zum Abschluss der Migration hängt von der Datenbankgröße und der Anzahl der Datenbankobjekte ab. </br> – Die Quelldatenbank muss schreibgeschützt sein. |
| | | |

In der folgenden Tabelle werden die alternativen Migrationsoptionen verglichen: 

|Methode oder Technologie |Verwendung    |Überlegungen  |
|---------|---------|---------|
|[Transaktionsreplikation](../../database/replication-to-sql-database.md)| – Migrieren durch kontinuierliches Veröffentlichen von Änderungen der Quelldatenbanktabellen an die Zieltabellen in SQL-Datenbank </br> – Führen Sie eine vollständige oder teilweise Datenbankmigrationen ausgewählter Tabellen (Teilmenge der Datenbank) aus.  </br> </br> Unterstützte Quellen: </br> - [SQL Server (2016 to 2019) mit Einschränkungen](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> – GCP Compute mit SQL Server-VM  | – Das Setup ist im Vergleich zu anderen Migrationsoptionen relativ komplex.   </br> – Bietet eine kontinuierliche Replikationsoption zum Migrieren von Daten (ohne die Datenbanken offline zu schalten)  </br> - Bei der transaktionalen Replikation gibt es Einschränkungen zu beachten, wenn Sie den Verleger auf der SQL Server-Quellinstanz einrichten. Weitere Informationen finden Sie unter [Einschränkungen für das Veröffentlichen von Objekten](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects). </br>– Es ist möglich, [die Replikationsaktivität zu überwachen](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import/Export-Dienst/BACPAC](../../database/database-import.md)| – Migrieren individueller Branchenanwendungsdatenbanken </br>– Für kleinere Datenbanken geeignet  </br>  – Erfordert keinen separaten Migrationsdienst oder ein Tool </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM  |  – Erfordert Downtime, da Daten aus der Quelle exportiert und im Ziel importiert werden müssen.   </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen, um Kürzungen oder Fehler durch nicht übereinstimmende Datentypen zu vermeiden.  </br> – Die Zeit zum Exportieren einer Datenbank mit einer großen Anzahl von Objekten kann deutlich höher sein.       |
|[Massenkopieren](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Führen Sie vollständige oder teilweise Datenmigrationen durch. </br> – Downtime ist möglich </br> </br> Unterstützte Quellen: </br> - SQL Server (2005 bis 2019) lokal oder Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP Compute mit SQL Server-VM   | - Erfordert Downtime für das Exportieren von Daten aus der Quelle und das Importieren in das Ziel. </br> – Die für den Export/Import verwendeten Dateiformate und Datentypen müssen mit den Tabellenschemas übereinstimmen. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| – Migrieren und/oder Transformieren von Daten aus SQL Server-Quelldatenbanken </br> – Zusammenführen von Daten aus mehreren Datenquellen in Azure SQL-Datenbank (wird in der Regel für Business Intelligence-Arbeitsauslastungen verwendet)  |  – Erfordert das Erstellen von Datenverschiebungspipelines in Data Factory zum Verschieben von Daten aus der Quelle zum Ziel   </br> - [Kosten](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) sind ein wichtiger Aspekt, der auf Faktoren wie Pipelinetriggern, den Aktivitätsausführungen, der Dauer der Datenverschiebung und mehr basiert. |
|[SQL-Datensynchronisierung](../../database/sql-data-sync-data-sql-server-sql-database.md)| – Synchronisieren von Daten zwischen Quell- und Zieldatenbanken</br> – Eignet sich für eine fortlaufende Synchronisierung zwischen Azure SQL-Datenbank und der lokalen SQL Server-Instanz in einem bidirektionalen Flow | – Die Instanz von Azure SQL-Datenbank muss als Hub-Datenbank für die Synchronisierung mit der lokalen SQL Server-Datenbank als Mitgliedsdatenbank konfiguriert werden</br> – Im Gegensatz zur Transaktionsreplikation unterstützt die SQL-Datensynchronisierung die bidirektionale Datensynchronisierung zwischen der lokalen Datenbank und der Instanz von Azure SQL-Datenbank </br> – Kann je nach Arbeitsauslastung höhere Auswirkungen auf die Leistung haben|
| | | |

## <a name="feature-interoperability"></a>Featureinteroperabilität 

Beim Migrieren von Workloads, die andere SQL Server-Features nutzen, gibt es weitere Aspekte, die Sie berücksichtigen werden müssen.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrieren Sie SQL Server Integration Services-Pakete (SSIS) zu Azure, indem Sie die Pakete neu in der Azure-SSIS-Runtime in [Azure Data Factory](../../../data-factory/introduction.md) bereitstellen. Azure Data Factory [unterstützt die Migration von SSIS-Paketen](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) durch eine Runtime, in der SSIS-Pakete in Azure ausgeführt werden können. Alternativ können Sie die SSIS-ETL-Logik (extrahieren, transformieren, laden) mithilfe von [Datenflüssen](../../../data-factory/concepts-data-flow-overview.md) nativ in Azure Data Factory umschreiben.


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services-Berichte (SSRS) können zu paginierten Berichten in Power BI migriert werden. Verwenden Sie das  [RDL-Migrationstool](https://github.com/microsoft/RdlMigration), um die Vorbereitung und Migration Ihrer Berichte zu unterstützen. Microsoft hat dieses Tool entwickelt, um Kunden bei der Migration von Report Definition Language (RDL)-Berichten von ihren SSRS-Servern zu Power BI zu unterstützen. Es ist auf GitHub verfügbar und dokumentiert eine exemplarische End-to-End-Vorgehensweise des Migrationsszenarios. 

### <a name="high-availability"></a>Hochverfügbarkeit
Die Funktionen der SQL Server-Hochverfügbarkeit wie Always On Failover-Cluster-Instanzen und Always On-Verfügbarkeitsgruppen werden auf der verwalteten SQL-Zielinstanz obsolet. Hochverfügbarkeitsarchitektur ist bereits in die Dienstebenen [Universell (Standard-Verfügbarkeitsmodell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) und [Unternehmenskritisch (Premium Availability Model)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) für Azure SQL-Datenbank integriert. Die Dienstebene „Unternehmenskritisch“/„Premium“ stellt auch die horizontale Leseskalierung bereit, die das Herstellen einer Verbindung mit einem der sekundären Knoten für schreibgeschützte Zwecke ermöglicht. 

Über die Hochverfügbarkeitsarchitektur hinaus, die in Azure SQL-Datenbank enthalten ist, können Sie mit der Funktion für [automatische Failover-Gruppen](../../database/auto-failover-group-overview.md) die Replikation und das Failover von Datenbanken in einer verwalteten Instanz in eine andere Region verwalten. 

### <a name="logins-and-groups"></a>Anmeldungen und Gruppen

Windows-Anmeldungen werden in Azure SQL-Datenbank nicht unterstützt. Erstellen Sie stattdessen eine Azure Active Directory-Anmeldung. Erstellen Sie alle SQL-Anmeldungen manuell neu. 

### <a name="sql-agent-jobs"></a>SQL-Agent-Aufträge
SQL-Agent-Aufträge werden in Azure SQL-Datenbank nicht direkt unterstützt und müssen als [Aufträge für elastische Datenbanken (Vorschauversion)](../../database/job-automation-overview.md) bereitgestellt werden.

### <a name="system-databases"></a>Systemdatenbanken
Die einzigen in Azure SQL-Datenbank anwendbaren Systemdatenbanken sind die [Masterdatenbank](/sql/relational-databases/databases/master-database) und tempdb. Weitere Informationen finden Sie unter [tempdb in Azure SQL-Datenbank](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Erweiterte Funktionen 

Sie sollten unbedingt die Vorteile der erweiterten cloudbasierten Funktionen nutzen, die mit der SQL-Datenbank möglich sind. Zum Beispiel müssen Sie sich nicht um die Verwaltung der Sicherungen kümmern, da der Dienst dies für Sie übernimmt. Sie können [einen beliebigen Zeitpunkt im Aufbewahrungszeitraum](../../database/recovery-using-backups.md#point-in-time-restore) wiederherstellen. 

Sie sollten außerdem die  [Azure AD-Authentifizierung](../../database/authentication-aad-overview.md), die [Überwachung](../../database/auditing-overview.md), die  [Bedrohungserkennung](../../database/azure-defender-for-sql.md), die  [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und die  [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) verwenden, um die Sicherheit zu erhöhen.

Zusätzlich zu den erweiterten Verwaltungs- und Sicherheitsfunktionen stellt SQL-Datenbank eine Reihe von Tools bereit, die Sie beim [Überwachen und Optimieren Ihrer Arbeitsauslastung](../../database/monitor-tune-overview.md) unterstützen. Die [Azure SQL-Analyse (Vorschauversion)](../../../azure-monitor/insights/azure-sql.md) ist eine erweiterte Überwachungslösung, mit der Sie die Leistung Ihrer sämtlichen Datenbanken in Azure SQL-Datenbank im großen Stil und abonnementübergreifend in einer zentralen Ansicht überwachen können. Azure SQL-Analyse erfasst und visualisiert wichtige Leistungsmetriken und umfasst integrierte Logik für die Problembehandlung in Bezug auf die Leistung.

Mithilfe der [automatischen Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) kann die Leistung Ihrer SQL-Ausführungsplanstatistiken kontinuierlich überwacht werden. Erkannte Leistungsprobleme werden durch die Funktion automatisch korrigiert. 


## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Unterstützung finden Sie in den folgenden Ressourcen, die für reale Migrationsprojekte entwickelt wurden.

|Asset  |BESCHREIBUNG  |
|---------|---------|
|[Data Workload Assessment Model and Tool (Datenarbeitsauslastungs-Bewertungsmodell und -tool)](https://www.microsoft.com/download/details.aspx?id=103130)| Dieses Tool stellt für eine bestimmte Arbeitsauslastung Informationen zu empfohlenen „optimalen“ Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch einen automatisierten und einheitlichen Entscheidungsprozess für die Zielplattform dabei helfen, Bewertungen von umfangreichen Datenbeständen zu beschleunigen.|
|[Massenerstellung von Datenbanken mit PowerShell](https://www.microsoft.com/download/details.aspx?id=103107)|Diese Ressource enthält drei PowerShell-Skripts, mit denen eine Ressourcengruppe (create_rg.ps1), der [logische Server in Azure](../../database/logical-servers.md) (create_sqlserver.ps1) und eine SQL-Datenbank (create_sqldb.ps1) erstellt werden können. Die Skripts enthalten Schleifenfunktionen, sodass Sie Iterationen durchführen und beliebig viele Server und Datenbanken erstellen können.|
|[Massenbereitstellung von Schemas mit MSSQL-Scripter und PowerShell](https://www.microsoft.com/download/details.aspx?id=103032)|Mit dieser Ressource wird eine Ressourcengruppe sowie mindestens ein [logischer Server in Azure](../../database/logical-servers.md) zum Hosten der Azure SQL-Datenbank-Instanz erstellt. Außerdem exportiert sie jedes Schema aus einer lokalen SQL Server-Instanz (oder mehreren SQL Server-Instanzen (2005 und höher)) und importiert diese in Azure SQL-Datenbank.|
|[Konvertieren von SQL Server-Agent-Aufträgen in Aufträge für elastische Datenbanken](https://www.microsoft.com/download/details.aspx?id=103123)|Dieses Skript migriert die SQL Server-Agent-Aufträge Ihrer Quelldatenbank zu Aufträgen für elastische Datenbanken.|
|[Hilfsprogramm zum Verschieben lokaler SQL Server-Anmeldungen zu Azure SQL-Datenbank](https://www.microsoft.com/download/details.aspx?id=103111)|Hierbei handelt es sich um ein PowerShell-Skript, das ein T-SQL-Befehlsskript erstellt, um Anmeldungen und ausgewählte Datenbankbenutzer aus der lokalen SQL Server-Instanz in Azure SQL-Datenbank neu zu erstellen. Das Tool ermöglicht die automatische Zuordnung von Windows Server Active Directory-Konten zu Azure AD-Konten, zusammen mit der optionalen Migration nativer SQL Server-Logins.|
|[Automatisierung der PerfMon-Datensammlung mithilfe von Logman](https://www.microsoft.com/download/details.aspx?id=103114)|Sie können das Logman-Tool verwenden, um PerfMon-Daten zu erfassen (damit Sie die grundlegende Leistung besser verstehen können) und Empfehlungen für die Migration der virtuellen Maschine zu erhalten. Das Tool verwendet die ausführbare Datei „logman.exe“, um den Befehl zu erstellen, der die Leistungsindikatoren für eine SQL Server-Remoteinstanz erstellt, startet, beendet und löscht.|

Das Data SQL Engineering-Team hat diese Ressourcen entwickelt. Die Hauptanwendung dieses Teams besteht darin, die komplexe Modernisierung für Datenplattform-Migrationsprojekte auf der Azure-Datenplattform von Microsoft freizugeben und zu beschleunigen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Starten der Migration Ihrer SQL Server-Instanz zu Azure SQL-Datenbank finden Sie im [Leitfaden für die Migration von SQL Server zu SQL-Datenbank](sql-server-to-sql-database-guide.md).

- Eine Matrix von Diensten und Werkzeugen, die Sie bei Datenbank- und Datenmigrationsszenarien sowie bei speziellen Aufgaben unterstützen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu SQL-Datenbank finden Sie unter:
   - [Übersicht über Azure SQL-Datenbank](../../database/sql-database-paas-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 

- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für Kostenermittlung und Größenanpassung von Workloads, die zu Azure migriert werden](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Einzelheiten zur Durchführung von A/B-Tests auf der Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).
