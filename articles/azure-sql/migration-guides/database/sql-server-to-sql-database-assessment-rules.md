---
title: Bewertungsregeln für die Migration von Azure SQL Server zu SQL-Datenbank
description: Bewertungsregeln zum Identifizieren von Problemen mit der SQL Server-Quellinstanz, die behoben werden müssen, bevor die Migration zu Azure SQL-Datenbank erfolgt.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: 88c90896cd8c480f828116c479a4dc5d0a3ee99f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069499"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Bewertungsregeln für die Migration von Azure SQL Server zu SQL-Datenbank
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Mit Migrationstools wird Ihre SQL Server-Quellinstanz überprüft, indem einige Bewertungsregeln angewendet werden. Hiermit sollen Probleme identifiziert werden, die behoben werden müssen, bevor für Ihre SQL Server-Datenbank die Migration zu Azure SQL-Datenbank durchgeführt wird. 

Dieser Artikel enthält eine Liste mit den Regeln, die zum Bewerten der Durchführbarkeit einer Migration Ihrer SQL Server-Datenbank zu Azure SQL-Datenbank verwendet werden. 


## <a name="bulk-insert"></a>Masseneinfügung (BULK INSERT)<a id="BulkInsert"></a>

**Titel: BULK INSERT bei anderer Datenquelle als Azure-Blob wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Azure SQL-Datenbank kann nicht auf Dateifreigaben oder Windows-Ordner zugreifen. Der Abschnitt „Betroffene Objekte“ enthält Informationen zu den spezifischen Verwendungsmöglichkeiten für BULK INSERT-Anweisungen, für die nicht auf ein Azure-Blob verwiesen wird. Objekte mit „BULK INSERT“, bei denen die Quelle kein Azure-Blobspeicher ist, funktionieren nach der Migration zu Azure SQL-Datenbank nicht mehr. 


**Empfehlung**   
Sie müssen die BULK INSERT-Anweisungen, die lokale Dateien oder Dateifreigaben verwenden, auf die Verwendung von Dateien aus Azure Blob Storage umstellen, wenn Sie eine Migration zu Azure SQL-Datenbank durchführen. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

## <a name="compute-clause"></a>COMPUTE-Klausel<a id="ComputeClause"></a>

**Titel: COMPUTE-Klausel wird nicht mehr unterstützt und wurde entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die COMPUTE-Klausel generiert Gesamtsummen, die als zusätzliche Summenspalten am Ende des Resultsets aufgeführt werden. Diese Klausel wird in Azure SQL-Datenbank aber nicht mehr unterstützt. 


**Empfehlung**   
Das T-SQL-Modul muss stattdessen mit dem ROLLUP-Operator umgeschrieben werden. Der folgende Code veranschaulicht, wie COMPUTE durch ROLLUP ersetzt werden kann: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="clr-assemblies"></a>CLR-Assemblys<a id="ClrAssemblies"></a>

**Titel: SQL-CLR-Assemblys werden in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   


**Beschreibung**   
Azure SQL-Datenbank unterstützt keine SQL-CLR-Assemblys. 


**Empfehlung**   
Derzeit gibt es keine Möglichkeit, dies in Azure SQL-Datenbank zu erreichen. Für die empfohlenen Alternativlösungen dürfen für Anwendungscode und Datenbankänderungen nur Assemblys genutzt werden, die von Azure SQL-Datenbank unterstützt werden. Alternativ können Sie die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchführen.

Weitere Informationen: [Fehlende Unterstützung von Transact-SQL: Unterschiede in SQL-Datenbank](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Kryptografieanbieter<a id="CryptographicProvider"></a>

**Titel: Es wurde eine Verwendung von CREATE CRYPTOGRAPHIC PROVIDER oder ALTER CRYPTOGRAPHIC PROVIDER gefunden. Dies wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Für Azure SQL-Datenbank werden CRYPTOGRAPHIC PROVIDER-Anweisungen nicht unterstützt, weil der Zugriff auf Dateien nicht möglich ist. Der Abschnitt „Betroffene Objekte“ enthält Informationen zu den spezifischen Verwendungsmöglichkeiten für CRYPTOGRAPHIC PROVIDER-Anweisungen. Objekte mit `CREATE CRYPTOGRAPHIC PROVIDER` oder `ALTER CRYPTOGRAPHIC PROVIDER` funktionieren nach der Migration zu Azure SQL-Datenbank nicht richtig.  


**Empfehlung**   
Überprüfen Sie die Objekte mit `CREATE CRYPTOGRAPHIC PROVIDER` oder `ALTER CRYPTOGRAPHIC PROVIDER`. Entfernen Sie in allen erforderlichen Objekten die Verwendungsmöglichkeiten dieser Funktionen. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

## <a name="cross-database-references"></a>Datenbankübergreifende Verweise<a id="CrossDataseReferences"></a>

**Titel: Datenbankübergreifende Abfragen werden in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Für Datenbanken auf diesem Server werden datenbankübergreifende Abfragen verwendet, die in Azure SQL-Datenbank nicht unterstützt werden. 


**Empfehlung**   
Für Azure SQL-Datenbank werden datenbankübergreifende Abfragen nicht unterstützt. Die folgenden Aktionen werden empfohlen: 
- Migrieren Sie die abhängigen Datenbanken zu Azure SQL-Datenbank, und verwenden Sie das Feature „Abfrage für elastische Datenbanken“ (derzeit in der Vorschauphase), um Abfragen übergreifend für mehrere Azure SQL-Datenbank-Instanzen durchzuführen. 
- Verschieben Sie die abhängigen Datasets aus anderen Datenbanken in die zu migrierende Datenbank. 
- Führen Sie die Migration zu Azure SQL Managed Instance durch.
- Führen Sie die Migration zu SQL Server auf einem virtuellen Azure-Computer durch. 

Weitere Informationen: [Übersicht über elastische Abfragen in Azure SQL-Datenbank (Vorschau)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Datenbankkompatibilität<a id="DbCompatLevelLowerThan100"></a>

**Titel: Für Azure SQL-Datenbank werden Kompatibilitätsgrade von unter 100 nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Der Datenbank-Kompatibilitätsgrad ist ein wichtiges Tool zur Unterstützung der Datenbankmodernisierung. Hiermit können Upgrades für die SQL Server-Datenbank-Engine durchgeführt werden, während der funktionale Status von Anwendungen, die Verbindungen herstellen, erhalten bleibt, indem der vor einem Upgrade wirksame Kompatibilitätsgrad beibehalten wird. Für Azure SQL-Datenbank werden Kompatibilitätsgrade von unter 100 nicht unterstützt. 


**Empfehlung**   
Ermitteln Sie, ob die Anwendungsfunktionalität intakt ist, wenn der Datenbank-Kompatibilitätsgrad in Azure SQL Managed Instance auf 100 aktualisiert wird. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

## <a name="database-mail"></a>Datenbank-E-Mail<a id="DatabaseMail"></a>

**Titel: Datenbank-E-Mail wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Für diesen Server wird das Feature „Datenbank-E-Mail“ verwendet, das in Azure SQL-Datenbank nicht unterstützt wird.


**Empfehlung**   
Erwägen Sie, die Migration zu Azure SQL Managed Instance mit Unterstützung von Datenbank-E-Mail durchzuführen.  Alternativ können Sie Azure Functions und SendGrid nutzen, um E-Mail-Funktionen für Azure SQL-Datenbank bereitzustellen.

## <a name="database-principal-alias"></a>Datenbankprinzipal-Alias<a id="DatabasePrincipalAlias"></a>

**Titel: SYS.DATABASE_PRINCIPAL_ALIASES wird eingestellt und wurde entfernt.**    
**Kategorie**: Problem   

**Beschreibung**   
SYS.DATABASE_PRINCIPAL_ALIASES wird eingestellt und wurde in Azure SQL-Datenbank entfernt.  


**Empfehlung**   
Verwenden Sie anstelle von Aliasen Rollen.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Option „DISABLE_DEF_CNST_CHK“<a id="DisableDefCNSTCHK"></a>

**Titel: SET-Option „DISABLE_DEF_CNST_CHK“ wird eingestellt und wurde entfernt.**    
**Kategorie**: Problem   

**Beschreibung**   
Die SET-Option „DISABLE_DEF_CNST_CHK“ wird eingestellt und wurde in Azure SQL-Datenbank entfernt.  


Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW-Hinweis<a id="FastFirstRowHint"></a>

**Titel: FASTFIRSTROW-Abfragehinweis wird eingestellt und wurde entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Der FASTFIRSTROW-Abfragehinweis wird eingestellt und wurde in Azure SQL-Datenbank entfernt.  


**Empfehlung**   
Verwenden Sie OPTION (FAST n) anstelle des FASTFIRSTROW-Abfragehinweises.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Filestream<a id="FileStream"></a>

**Titel: Filestream wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Das Feature „Filestream“, mit dem Sie unstrukturierte Daten (z. B. Textdokumente, Bilder und Videos) im NTFS-Dateisystem speichern können, wird in Azure SQL-Datenbank nicht unterstützt. 


**Empfehlung**   
Laden Sie die unstrukturierten Dateien in Azure-Blobspeicher hoch, und speichern Sie die Metadaten zu diesen Dateien (Name, Typ, URL-Speicherort, Speicherschlüssel usw.) in Azure SQL-Datenbank. Sie müssen für Ihre Anwendung ggf. das Reengineering durchführen, um das Streamen von Blobs für Azure SQL-Datenbank zu ermöglichen. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Streamen von Blobs für Azure SQL: Blog](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Verbindungsserver<a id="LinkedServer"></a>

**Titel: Funktionalität für Verbindungsserver wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Mit Verbindungsservern kann die SQL Server-Datenbank-Engine Befehle für OLE DB-Datenquellen ausführen, die sich außerhalb der Instanz von SQL Server befinden. 


**Empfehlung**   
Für Azure SQL-Datenbank wird die Funktionalität für Verbindungsserver nicht unterstützt. Die folgenden Aktionen werden empfohlen, um zu vermeiden, dass Verbindungsserver verwendet werden müssen: 
- Identifizieren Sie die abhängigen Datasets für die SQL-Remoteserver, und erwägen Sie, diese in die zu migrierende Datenbank zu verschieben. 
- Migrieren Sie die abhängigen Datenbanken zu Azure, und verwenden Sie das Feature „Abfrage für elastische Datenbanken“ (Vorschauversion), um in Azure SQL-Datenbank datenbankübergreifende Abfragen durchzuführen. 

Weitere Informationen: [Übersicht über elastische Abfragen in Azure SQL-Datenbank (Vorschau)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Titel: BEGIN DISTRIBUTED TRANSACTION wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Eine verteilte Transaktion, die mit „Transact SQL BEGIN DISTRIBUTED TRANSACTION“ gestartet und mit Microsoft Distributed Transaction Coordinator (MS DTC) verwaltet wird, wird in Azure SQL-Datenbank nicht unterstützt.  


**Empfehlung**   
Navigieren Sie in Azure Migrate zum Abschnitt „Betroffene Objekte“, um alle Objekte anzuzeigen, für die BEGIN DISTRIBUTED TRANSACTION verwendet wird. Erwägen Sie, die Teilnehmerdatenbanken zu Azure SQL Managed Instance zu migrieren, damit verteilte Transaktionen instanzübergreifend unterstützt werden (derzeit in der Vorschauphase). Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Transaktionen über mehrere Server für Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-for-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (Massenvorgang)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titel: OpenRowSet bei einem Massenvorgang bei einer Datenquelle, die kein Azure-Blobspeicher ist, wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung** Für OPENROWSET werden Massenvorgänge über einen integrierten BULK-Anbieter unterstützt, mit dem Daten aus einer Datei gelesen und als Rowset zurückgegeben werden können. OPENROWSET bei einer Datenquelle, die kein Azure-Blobspeicher ist, wird in Azure SQL-Datenbank nicht unterstützt.


**Empfehlung**   
Azure SQL-Datenbank kann nicht auf Dateifreigaben und Windows-Ordner zugreifen. Daher müssen die Dateien aus Azure Blob Storage importiert werden. Aus diesem Grund wird für die OPENROWSET-Funktion nur der Blobtyp DATASOURCE unterstützt. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (Anbieter)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Titel: OpenRowSet mit einem SQL- oder anderen Anbieter wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
OpenRowSet mit einem SQL- oder anderen Anbieter ist eine Alternative zum Zugriff auf Tabellen eines Verbindungsservers. Dies ist eine einmalig verwendete Ad-hoc-Methode zum Verbinden und Zugreifen auf Remotedaten mithilfe von OLE DB. OpenRowSet mit einem SQL- oder anderen Anbieter wird in Azure SQL-Datenbank nicht unterstützt.


**Empfehlung**   
Azure SQL-Datenbank unterstützt OPENROWSET nur für den Import aus Azure Blob Storage. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Linke äußere Verknüpfung (kein ANSI)<a id="NonANSILeftOuterJoinSyntax"></a>

**Titel: Linke äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Linke äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und in Azure SQL-Datenbank entfernt. 


**Empfehlung**   
Verwenden Sie die ANSI-Joinsyntax.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Rechte äußere Verknüpfung (kein ANSI)<a id="NonANSIRightOuterJoinSyntax"></a>

**Titel: Rechte äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Rechte äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und in Azure SQL-Datenbank entfernt. 


**Empfehlung**   
Verwenden Sie die ANSI-Joinsyntax.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Nächste Spalte<a id="NextColumn"></a>

**Titel: Tabellen und Spalten mit dem Namen NEXT führen in Azure SQL-Datenbank zu einem Fehler.**    
**Kategorie**: Problem   

**Beschreibung**   
Es wurden Tabellen oder Spalten mit dem Namen NEXT erkannt. In Microsoft SQL Server eingeführte Sequenzen verwenden die ANSI-Standardfunktion NEXT VALUE FOR. Wenn der Name einer Tabelle oder Spalte NEXT lautet, für die Spalte als Alias VALUE verwendet wird und das ANSI-Standardschlüsselwort AS weggelassen wird, kann die resultierende Anweisung einen Fehler verursachen.  


**Empfehlung**   
Schreiben Sie Anweisungen so um, dass sie das ANSI-Standardschlüsselwort AS enthalten, wenn für eine Tabelle oder Spalte ein Alias verwendet wird. Wenn eine Spalte beispielsweise den Namen NEXT hat und über den Alias VALUE verfügt, führt die Abfrage `SELECT NEXT VALUE FROM TABLE` zu einem Fehler und sollte wie folgt umgeschrieben werden: SELECT NEXT AS VALUE FROM TABLE. Ebenso verursacht die Abfrage `SELECT Col1 FROM NEXT VALUE` einen Fehler, wenn der Name einer Tabelle NEXT lautet und für diese Tabelle der Alias VALUE verwendet wird. Diese Abfrage sollte wie folgt umgeschrieben werden: `SELECT Col1 FROM NEXT AS VALUE`.

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titel: RAISERROR-Aufrufe im Legacystil sollten durch moderne Äquivalente ersetzt werden.**    
**Kategorie**: Warnung   

**Beschreibung**   
RAISERROR-Aufrufe wie im folgenden Beispiel werden als Aufrufe im Legacystil bezeichnet, da sie keine Kommas und Klammern enthalten. `RAISERROR 50001 'this is a test'`. Diese Methode zum Aufrufen von RAISERROR wurde eingestellt und in Azure SQL-Datenbank entfernt. 


**Empfehlung**   
Schreiben Sie die Anweisung um, indem Sie die aktuelle RAISERROR-Syntax verwenden, oder ermitteln Sie, ob der moderne Ansatz mit `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` möglich ist.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Serverüberwachungen<a id="ServerAudits"></a>

**Titel: Verwenden der Überwachungsfunktionen von Azure SQL-Datenbank als Ersatz für Serverüberwachungen.**    
**Kategorie**: Warnung   

**Beschreibung**   
Serverüberwachungen werden in Azure SQL-Datenbank nicht unterstützt.


**Empfehlung**   
Erwägen Sie, die Überwachungsfunktionen von Azure SQL-Datenbank als Ersatz für Serverüberwachungen zu verwenden.  Die Überwachung wird von Azure SQL unterstützt, und die Features sind umfangreicher als bei SQL Server. Mit Azure SQL-Datenbank können verschiedene Datenbankaktionen und -ereignisse überwacht werden, z. B.: Zugriff auf Daten, Schemaänderungen (DDL), Datenänderungen (DML), Konten, Rollen und Berechtigungen (DCL) sowie Sicherheitsausnahmen. Mit den Überwachungsfunktionen von Azure SQL-Datenbank können sich Organisationen eingehender über die Ereignisse und Änderungen in ihrer Datenbank informieren (z. B. über Datenaktualisierungen und -abfragen). Alternativ können Sie die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchführen.

Weitere Informationen: [Überwachen von Azure SQL-Datenbank und Azure Synapse Analytics](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Serveranmeldeinformationen<a id="ServerCredentials"></a>

**Titel: Serverbezogene Anmeldeinformationen werden in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Anmeldeinformationen sind in einem Datensatz gespeichert, in dem die Authentifizierungsinformationen (Anmeldeinformationen) enthalten sind, die zum Herstellen einer Verbindung mit einer Ressource außerhalb von SQL Server erforderlich sind. Für Azure SQL-Datenbank werden Datenbank-Anmeldeinformationen unterstützt, aber dies gilt nicht für die Anmeldeinformationen, die für den SQL Server-Bereich erstellt wurden.   


**Empfehlung**   
Für Azure SQL-Datenbank werden datenbankbezogene Anmeldeinformationen unterstützt. Konvertieren Sie serverbezogene Anmeldeinformationen in datenbankbezogene Anmeldeinformationen. Alternativ können Sie die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchführen.

Weitere Informationen: [Erstellen von datenbankbezogenen Anmeldeinformationen](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Titel: Service Broker-Feature wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
SQL Server Service Broker verfügt über native Unterstützung für Messaging- und Warteschlangenanwendungen in der SQL Server-Datenbank-Engine. Service Broker-Feature wird in Azure SQL-Datenbank nicht unterstützt.


**Empfehlung**   
Service Broker-Feature wird in Azure SQL-Datenbank nicht unterstützt. Erwägen Sie, die Migration zum Dienst Azure SQL Managed Instance durchzuführen, für den die Nutzung von Service Broker innerhalb derselben Instanz unterstützt wird. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

## <a name="server-scoped-triggers"></a>Trigger im Gültigkeitsbereich des Servers<a id="ServerScopedTriggers"></a>

**Titel: Trigger im Gültigkeitsbereich des Servers werden in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Ein Trigger ist eine besondere Art von gespeicherter Prozedur, die als Reaktion auf eine bestimmte Tabellenaktion, z. B. das Einfügen, Löschen oder Aktualisieren von Daten, ausgeführt wird. Trigger im Gültigkeitsbereich des Servers werden in Azure SQL-Datenbank nicht unterstützt. Für Azure SQL-Datenbank werden die folgenden Optionen für Trigger nicht unterstützt: FOR LOGON, ENCRYPTION, WITH APPEND, NOT FOR REPLICATION, Option EXTERNAL NAME (keine Unterstützung einer externen Methode), Option ALL SERVER (DDL-Trigger), Trigger für ein LOGON-Ereignis (LOGON-Trigger). Für Azure SQL-Datenbank werden auch keine CLR-Trigger unterstützt.


**Empfehlung**   
Verwenden Sie stattdessen einen Trigger auf Datenbankebene. Alternativ können Sie die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchführen.

Weitere Informationen: [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL-Agent-Aufträge<a id="AgentJobs"></a>

**Titel: In Azure SQL-Datenbank sind keine SQL Server-Agent-Aufträge verfügbar.**    
**Kategorie**: Warnung   

**Beschreibung**   
Der SQL Server-Agent ist ein Microsoft Windows-Dienst, der geplante administrative Aufgaben ausführt, die in SQL Server als Aufträge bezeichnet werden. In Azure SQL-Datenbank sind keine SQL Server-Agent-Aufträge verfügbar. 


**Empfehlung** Verwenden Sie elastische Aufträge (Vorschauversion), die in Azure SQL-Datenbank als Ersatz für SQL Server-Agent-Aufträge dienen. Das Feature „Aufträge für die elastische Datenbank“ für Azure SQL-Datenbank ermöglicht die zuverlässige Ausführung von T-SQL-Skripts, die mehrere Datenbanken abdecken, und stellt Funktionen für die automatische Wiederholung und ggf. Abschlussgarantien bereit. Alternativ können Sie erwägen, die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchzuführen.

Weitere Informationen: [Erste Schritte mit „Aufträge für die elastische Datenbank“ (Vorschauversion) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL-Datenbankgröße<a id="SQLDBDatabaseSize"></a>

**Titel: Für Azure SQL-Datenbank wird eine Datenbankgröße von über 100 TB nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Die Datenbankgröße übersteigt die maximal unterstützte Größe von 100 TB. 


**Empfehlung**   
Ermitteln Sie, ob die Daten archiviert oder komprimiert oder per Sharding in mehreren Datenbanken angeordnet werden können. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

Weitere Informationen: [Ressourcenlimits für virtuelle Kerne](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Titel: SQL Mail wird nicht mehr unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
SQL Mail wurde eingestellt und in Azure SQL-Datenbank entfernt.


**Empfehlung**   
Erwägen Sie, die Migration zu Azure SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer durchzuführen und Datenbank-E-Mail zu nutzen.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Titel: Es wurden Anweisungen erkannt, in denen auf entfernte gespeicherte Systemprozeduren verwiesen wird, die in Azure SQL-Datenbank nicht verfügbar sind.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die folgenden nicht unterstützten Systemprozeduren und erweiterten gespeicherten Prozeduren können in Azure SQL-Datenbank nicht verwendet werden: `sp_dboption`, `sp_addserver`, `sp_dropalias`,`sp_activedirectory_obj`, `sp_activedirectory_scp`, `sp_activedirectory_start`.


**Empfehlung**    
Entfernen Sie Verweise auf nicht unterstützte Systemprozeduren, die in Azure SQL-Datenbank entfernt wurden.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Ablaufverfolgungsflags<a id="TraceFlags"></a>

**Titel: Für Azure SQL-Datenbank werden keine Ablaufverfolgungsflags unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Ablaufverfolgungsflags werden zur temporären Festlegung bestimmter Servereigenschaften oder zum Ausschalten eines bestimmten Verhaltens verwendet. Häufig werden Ablaufverfolgungsflags zur Diagnose der Systemleistung und zum Debuggen von gespeicherten Prozeduren oder komplexen Computersystemen verwendet. Für Azure SQL-Datenbank werden keine Ablaufverfolgungsflags unterstützt. 


**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Darin sind alle Ablaufverfolgungsflags aufgeführt, die in Azure SQL-Datenbank nicht unterstützt werden, und Sie können ermitteln, ob sie entfernt werden können. Alternativ können Sie die Migration zum Dienst Azure SQL Managed Instance, für den eine begrenzte Anzahl von globalen Ablaufverfolgungsflags unterstützt wird, oder zu SQL Server auf einem virtuellen Azure-Computer durchführen.

Weitere Informationen: [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows-Authentifizierung<a id="WindowsAuthentication"></a>

**Titel: Datenbankbenutzer mit Zuordnung der Windows-Authentifizierung (integrierte Sicherheit) werden in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Azure SQL-Datenbank unterstützt zwei Arten der Authentifizierung: 
- SQL-Authentifizierung: Verwendet einen Benutzernamen und ein Kennwort. 
- Azure Active Directory-Authentifizierung: Verwendet von Azure Active Directory verwaltete Identitäten und wird für verwaltete und integrierte Domänen unterstützt. 

Datenbankbenutzer mit Zuordnung der Windows-Authentifizierung (integrierte Sicherheit) werden in Azure SQL-Datenbank nicht unterstützt. 



**Empfehlung**   
Stellen Sie für die lokale Active Directory-Instanz einen Verbund mit Azure Active Directory her. Die Windows-Identität kann dann durch die äquivalenten Azure Active Directory-Identitäten ersetzt werden. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Sicherheitsfunktionen von SQL-Datenbank](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>xp_cmdshell<a id="XpCmdshell"></a>

**Titel: „xp_cmdshell“ wird in Azure SQL-Datenbank nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
„xp_cmdshell“ für die Erstellung einer Windows-Befehlsshell und die Übergabe einer Zeichenfolge für die Ausführung wird in Azure SQL-Datenbank nicht unterstützt. 


**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Darin sind alle Objekte aufgeführt, für die „xp_cmdshell“ verwendet wird, und Sie können ermitteln, ob der Verweis auf „xp_cmdshell“ oder das betroffene Objekt entfernt werden kann. Erwägen Sie auch, sich mit Azure Automation vertraut zu machen, um einen cloudbasierten Dienst für die Automatisierung und Konfiguration nutzen zu können. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Beginn der Migration Ihrer SQL Server-Instanz zu Azure SQL-Datenbank finden Sie im [Leitfaden für die Migration von SQL Server zu SQL-Datenbank](sql-server-to-sql-database-guide.md).

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios und Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Weitere Informationen zu SQL-Datenbank finden Sie unter:
   - [Übersicht über Azure SQL-Datenbank](../../database/sql-database-paas-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) 

- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für die Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Informationen zum Bewerten der Anwendungszugriffsebene finden Sie unter [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).
