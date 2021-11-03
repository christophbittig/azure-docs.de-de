---
title: Bewertungsregeln für die Migration von SQL Server zu Azure SQL Managed Instance
description: Bewertungsregeln zum Identifizieren von Problemen mit der SQL Server-Quellinstanz, die behoben werden müssen, bevor die Migration zu Azure SQL Managed Instance erfolgt.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: 92fee6cde8c7a98806db6084c413202eb2f153f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069480"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>Bewertungsregeln für die Migration von SQL Server zu Azure SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Mit Migrationstools wird Ihre SQL Server-Quellinstanz überprüft, indem einige Bewertungsregeln angewendet werden. Hiermit sollen Probleme identifiziert werden, die behoben werden müssen, bevor für Ihre SQL Server-Datenbank die Migration zu Azure SQL Managed Instance durchgeführt wird. 

Dieser Artikel enthält eine Liste mit den Regeln, die zum Bewerten der Durchführbarkeit einer Migration Ihrer SQL Server-Datenbank zu Azure SQL Managed Instance verwendet werden. 

## <a name="analysiscommand-job"></a>AnalysisCommand-Auftrag<a id="AnalysisCommandJob"></a>

**Titel: AnalysisCommand-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   


**Beschreibung**   
Bei diesem Auftragsschritt wird ein Analysis Services-Befehl ausgeführt. Der AnalysisCommand-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.

**Empfehlung**     
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Es werden alle Aufträge angezeigt, für die der Auftragsschritt mit dem Analysis Services-Befehl verwendet wird, und Sie können ermitteln, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery-Auftrag<a id="AnalysisQueryJob"></a>

**Titel: AnalysisQuery-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Bei diesem Auftragsschritt wird eine Analysis Services-Abfrage durchgeführt. AnalysisQuery-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.


**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Es werden alle Aufträge angezeigt, für die der Auftragsschritt mit der Analysis Services-Abfrage verwendet wird, und Sie können ermitteln, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Assembly aus Datei<a id="AssemblyFromFile"></a>

**Titel: CREATE ASSEMBLY und ALTER ASSEMBLY mit einem Dateiparameter werden in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Azure SQL Managed Instance unterstützt „CREATE ASSEMBLY“ oder „ALTER ASSEMBLY“ nicht mit einem Dateiparameter. Ein binärer Parameter wird unterstützt. Das spezifische Objekt, in dem der Dateiparameter verwendet wird, finden Sie im Abschnitt Betroffene Objekte.

**Empfehlung**   
Überprüfen Sie Objekte mit „CREATE ASSEMBLY“ oder „ALTER ASSEMBLY“ mit einem Dateiparameter. Wenn solche Objekte erforderlich sind, konvertieren Sie den Dateiparameter in einen binären Parameter. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance: CLR](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Masseneinfügung (BULK INSERT)<a id="BulkInsert"></a>

**Titel: BULK INSERT bei anderer Datenquelle als Azure-Blob wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Azure SQL Managed Instance kann nicht auf Dateifreigaben oder Windows-Ordner zugreifen. Der Abschnitt „Betroffene Objekte“ enthält Informationen zu den spezifischen Verwendungsmöglichkeiten für BULK INSERT-Anweisungen, für die nicht auf ein Azure-Blob verwiesen wird. Objekte mit „BULK INSERT“, bei denen die Quelle kein Azure-Blobspeicher ist, funktionieren nach der Migration zu Azure SQL Managed Instance nicht mehr.


**Empfehlung**   
Sie müssen BULK INSERT-Anweisungen, für die lokale Dateien oder Dateifreigaben verwendet werden, auf die Verwendung von Dateien aus Azure Blob Storage umstellen, wenn Sie eine Migration zu Azure SQL Managed Instance durchführen.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance: Bulk insert/OPENROWSET](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR-Sicherheit<a id="ClrStrictSecurity"></a>

**Titel: Als SAFE oder EXTERNAL_ACCESS markierte CLR-Assemblys werden als UNSAFE eingestuft.**    
**Kategorie**: Warnung   

**Beschreibung**   
In Azure SQL Managed Instance wird der strenge CLR-Sicherheitsmodus erzwungen. Dieser Modus ist standardmäßig aktiviert und umfasst Breaking Changes für Datenbanken mit benutzerdefinierten CLR-Assemblys, die entweder als SAFE oder EXTERNAL_ACCESS markiert sind.


**Empfehlung**   
CLR verwendet die Codezugriffssicherheit (Code Access Security, CAS) im .NET Framework, die nicht länger als Sicherheitsbegrenzung unterstützt wird. Ab der Datenbank-Engine SQL Server 2017 (14.x) wurde eine Option vom Typ `sp_configure` mit dem Namen „clr strict security“ (strenge CLR-Sicherheit) eingeführt, um die Sicherheit von CLR-Assemblys zu erhöhen. Die Option „clr strict security“ ist standardmäßig aktiviert und bewirkt, dass CLR-Assemblys vom Typ SAFE und EXTERNAL_ACCESS so behandelt werden, als ob sie als UNSAFE gekennzeichnet wären. Wenn „clr strict security“ deaktiviert ist, kann eine CLR-Assembly, die mit PERMISSION_SET = SAFE erstellt wurde, unter Umständen auf externe Systemressourcen zugreifen, nicht verwalteten Code aufrufen und SysAdmin-Privilegien erhalten. Nachdem Sie Strict Security aktiviert haben, können Assemblys, die nicht signiert sind, nicht geladen werden. Wenn eine Datenbank über Assemblys vom Typ SAFE oder EXTERNAL_ACCESS verfügt, können RESTORE- oder ATTACH DATABASE-Anweisungen durchgeführt werden, aber die Assemblys werden ggf. nicht geladen. Um die Assemblys zu laden, müssen Sie jede Assembly entweder bearbeiten, ablegen oder neu erstellen, damit sie mit einem Zertifikat oder asymmetrischen Schlüssel signiert ist, der über einen entsprechenden Anmeldenamen mit der UNSAFE ASSEMBLY-Berechtigung auf dem Server verfügt.

Weitere Informationen: [CLR Strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>COMPUTE-Klausel<a id="ComputeClause"></a>

**Titel: COMPUTE-Klausel wird nicht mehr unterstützt und wurde entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die COMPUTE-Klausel generiert Gesamtsummen, die als zusätzliche Summenspalten am Ende des Resultsets aufgeführt werden. Diese Klausel wird in Azure SQL Managed Instance aber nicht mehr unterstützt.



**Empfehlung**   
Das T-SQL-Modul muss stattdessen mit dem ROLLUP-Operator umgeschrieben werden. Der folgende Code veranschaulicht, wie COMPUTE durch ROLLUP ersetzt werden kann: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Kryptografieanbieter<a id="CryptographicProvider"></a>

**Titel: Es wurde eine Verwendung von CREATE CRYPTOGRAPHIC PROVIDER oder ALTER CRYPTOGRAPHIC PROVIDER gefunden. Dies wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Für Azure SQL Managed Instance werden CRYPTOGRAPHIC PROVIDER-Anweisungen nicht unterstützt, weil der Zugriff auf Dateien nicht möglich ist. Der Abschnitt „Betroffene Objekte“ enthält Informationen zu den spezifischen Verwendungsmöglichkeiten für CRYPTOGRAPHIC PROVIDER-Anweisungen. Objekte mit „CREATE CRYPTOGRAPHIC PROVIDER“ oder „ALTER CRYPTOGRAPHIC PROVIDER“ funktionieren nach der Migration zu Azure SQL Managed Instance nicht mehr richtig.


**Empfehlung**   
Überprüfen Sie Objekte mit „CREATE CRYPTOGRAPHIC PROVIDER“ oder „ALTER CRYPTOGRAPHIC PROVIDER“. Entfernen Sie in allen erforderlichen Objekten die Verwendungsmöglichkeiten dieser Funktionen. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

Weitere Informationen: [Unterschiede bei Kryptografieanbietern in Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Datenbankkompatibilität<a id="DbCompatLevelLowerThan100"></a>

**Titel: Datenbank-Kompatibilitätsgrad von unter 100 wird nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Der Datenbank-Kompatibilitätsgrad ist ein wichtiges Tool zur Unterstützung der Datenbankmodernisierung. Hiermit können Upgrades für die SQL Server-Datenbank-Engine durchgeführt werden, während der funktionale Status von Anwendungen, die Verbindungen herstellen, erhalten bleibt, indem der vor einem Upgrade wirksame Kompatibilitätsgrad beibehalten wird. Für Azure SQL Managed Instance werden Kompatibilitätsgrade von unter 100 nicht unterstützt. Wenn die Datenbank mit einem Kompatibilitätsgrad von unter 100 in Azure SQL Managed Instance wiederhergestellt wird, wird der Kompatibilitätsgrad auf 100 aktualisiert. 


**Empfehlung**... Ermitteln Sie, ob die Anwendungsfunktionalität intakt ist, wenn der Datenbank-Kompatibilitätsgrad in Azure SQL Managed Instance auf 100 aktualisiert wird. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterstützte Kompatibilitätsgrade in Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Datenbankprinzipal-Alias<a id="DatabasePrincipalAlias"></a>

**Titel: SYS.DATABASE_PRINCIPAL_ALIASES wird eingestellt und wurde entfernt.**    
**Kategorie**: Problem   

**Beschreibung**   
SYS.DATABASE_PRINCIPAL_ALIASES wird eingestellt und wurde in Azure SQL Managed Instance entfernt.


**Empfehlung**   
Verwenden Sie anstelle von Aliasen Rollen.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Option „DISABLE_DEF_CNST_CHK“<a id="DisableDefCNSTCHK"></a>

**Titel: SET-Option „DISABLE_DEF_CNST_CHK“ wird eingestellt und wurde entfernt.**    
**Kategorie**: Problem   

**Beschreibung**   
Die SET-Option „DISABLE_DEF_CNST_CHK“ wird eingestellt und wurde in Azure SQL Managed Instance entfernt.


Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW-Hinweis<a id="FastFirstRowHint"></a>

**Titel: FASTFIRSTROW-Abfragehinweis wird eingestellt und wurde entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Der FASTFIRSTROW-Abfragehinweis wird eingestellt und wurde in Azure SQL Managed Instance entfernt.


**Empfehlung**   
Verwenden Sie OPTION (FAST n) anstelle des FASTFIRSTROW-Abfragehinweises.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Filestream<a id="FileStream"></a>

**Titel: Filestream und Dateitabelle werden in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Das Feature „Filestream“, mit dem Sie unstrukturierte Daten (z. B. Textdokumente, Bilder und Videos) im NTFS-Dateisystem speichern können, wird in Azure SQL Managed Instance nicht unterstützt. **Diese Datenbank kann nicht migriert werden, weil eine Sicherung, die Filestream-Dateigruppen enthält, in Azure SQL Managed Instance nicht wiederhergestellt werden kann.**


**Empfehlung**   
Laden Sie die unstrukturierten Dateien in Azure-Blobspeicher hoch, und speichern Sie die Metadaten zu diesen Dateien (Name, Typ, URL-Speicherort, Speicherschlüssel usw.) in Azure SQL Managed Instance. Sie müssen für Ihre Anwendung ggf. einen Reengineering-Vorgang durchführen, um das Streamen von Blobs für Azure SQL Managed Instance zu ermöglichen. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Streamen von Blobs für SQL Azure: Blog](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogener MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Titel: BEGIN DISTRIBUTED TRANSACTION mit einem anderen Remoteserver als einem SQL Server-Remoteserver wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Eine verteilte Transaktion, die mit „Transact SQL BEGIN DISTRIBUTED TRANSACTION“ gestartet und mit Microsoft Distributed Transaction Coordinator (MS DTC) verwaltet wird, wird in Azure SQL Managed Instance nicht unterstützt, wenn kein SQL Server-Remoteserver genutzt wird. 


**Empfehlung**   
Navigieren Sie in Azure Migrate zum Abschnitt „Betroffene Objekte“, um alle Objekte anzuzeigen, für die BEGIN DISTRIBUTED TRANSACTION verwendet wird. Erwägen Sie, die Teilnehmerdatenbanken zu Azure SQL Managed Instance zu migrieren, damit verteilte Transaktionen instanzübergreifend unterstützt werden (derzeit in der Vorschauphase). Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Transaktionen über mehrere Server für Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-for-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Homogener MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Titel: BEGIN DISTRIBUTED TRANSACTION wird für Azure SQL Managed Instance für mehrere Server unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Eine verteilte Transaktion, die mit „Transact SQL BEGIN DISTRIBUTED TRANSACTION“ gestartet und mit Microsoft Distributed Transaction Coordinator (MS DTC) verwaltet wird, wird für Azure SQL Managed Instance auf mehreren Servern unterstützt.


**Empfehlung**   
Navigieren Sie in Azure Migrate zum Abschnitt „Betroffene Objekte“, um alle Objekte anzuzeigen, für die BEGIN DISTRIBUTED TRANSACTION verwendet wird. Erwägen Sie, die Teilnehmerdatenbanken zu Azure SQL Managed Instance zu migrieren, damit verteilte Transaktionen instanzübergreifend unterstützt werden (derzeit in der Vorschauphase). Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren. 

Weitere Informationen: [Transaktionen über mehrere Server für Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-for-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Verbindungsserver (kein SQL-Anbieter)<a id="LinkedServerWithNonSQLProvider"></a>

**Titel: Verbindungsserver (kein SQL Server-Anbieter) wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Mit Verbindungsservern kann die SQL Server-Datenbank-Engine Befehle für OLE DB-Datenquellen ausführen, die sich außerhalb der Instanz von SQL Server befinden. Ein Verbindungsserver (kein SQL Server-Anbieter) wird in Azure SQL Managed Instance nicht unterstützt. 


**Empfehlung**   
Für Azure SQL Managed Instance wird die Funktionalität für Verbindungsserver nicht unterstützt, wenn der Remoteserveranbieter kein SQL Server-Anbieter ist, z. B. Oracle, Sybase usw. 

Die folgenden Aktionen werden empfohlen, um zu vermeiden, dass Verbindungsserver verwendet werden müssen: 
- Identifizieren Sie die abhängigen Datenbanken für die Remoteserver, bei denen es sich nicht um SQL-Server handelt, und verschieben Sie diese in die zu migrierende Datenbank. 
- Migrieren Sie die abhängigen Datenbanken zu unterstützten Zielen, z. B. SQL Managed Instance, SQL-Datenbank, Azure Synapse SQL und SQL Server-Instanzen. 
- Erwägen Sie, einen Verbindungsserver zwischen Azure SQL Managed Instance und SQL Server auf einem virtuellen Azure-Computer (SQL-VM) zu erstellen.  Erstellen Sie anschließend auf der SQL-VM einen Verbindungsserver für Oracle, Sybase usw. Für diesen Ansatz sind zwei Hops erforderlich, aber er kann als vorübergehende Problemumgehung genutzt werden.  
- Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance: Verbindungsserver](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Auftrag „Zusammenführen“<a id="MergeJob"></a>

**Titel: Auftragsschritt „Zusammenführen“ wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Hierbei handelt es sich um einen Auftragsschritt, mit dem der Replikationsmerge-Agent aktiviert wird. Der Replikationsmerge-Agent ist ein Hilfsprogramm in Form einer ausführbaren Datei, die die in den Datenbanktabellen enthaltene Anfangsmomentaufnahme auf die Abonnenten anwendet. Er führt außerdem inkrementelle Datenänderungen zusammen, die nach der Erstellung der Anfangsmomentaufnahme auf dem Verleger ausgeführt wurden, und löst Konflikte entweder entsprechend den von Ihnen konfigurierten Regeln oder mithilfe eines von Ihnen erstellten benutzerdefinierten Konfliktlösers. Der Auftragsschritt „Zusammenführen“ (Merge) wird in Azure SQL Managed Instance nicht unterstützt.


**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Es werden alle Aufträge angezeigt, für die der Auftragsschritt „Zusammenführen“ verwendet wird, und Sie können ermitteln, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI-Datenbankgröße<a id="MIDatabaseSize<"></a>

**Titel: Für Azure SQL Managed Instance wird eine Datenbankgröße von über 8 TB nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Die Datenbankgröße übersteigt die maximale Größe des reservierten Instanzspeichers. **Diese Datenbank kann nicht für die Migration ausgewählt werden, da der zulässige Grenzwert für die Größe überschritten wird.**


**Empfehlung**   
Ermitteln Sie, ob die Daten archiviert, komprimiert oder per Sharding in mehreren Datenbanken angeordnet werden können. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Merkmale der Hardwaregeneration von Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI-Instanzgröße<a id="MIInstanceSize<"></a>

**Titel: Maximale Instanzspeichergröße in Azure SQL Managed Instance darf nicht größer als 8 TB sein.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die Größe aller Datenbanken übersteigt die maximale Größe des reservierten Instanzspeichers.  


**Empfehlung**   
Erwägen Sie, die Datenbanken zu anderen Azure SQL Managed Instances oder zu SQL Server auf einem virtuellen Azure-Computer zu migrieren, falls alle Datenbanken auf derselben Instanz vorhanden sein müssen. 

Weitere Informationen: [Merkmale der Hardwaregeneration von Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Mehrere Protokolldateien<a id="MultipleLogFiles<"></a>

**Titel: Für Azure SQL Managed Instance werden nicht mehrere Protokolldateien unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Unter SQL Server ist es möglich, dass für eine Datenbank Protokolldaten in mehrere Dateien geschrieben werden. Diese Datenbank verfügt über mehrere Protokolldateien, und dies wird in Azure SQL Managed Instance nicht unterstützt. ** Diese Datenbank kann nicht migriert werden, da die Sicherung unter Azure SQL Managed Instance nicht wiederhergestellt werden kann. 
**

**Empfehlung**   
Für Azure SQL Managed Instance wird nur ein Protokoll pro Datenbank unterstützt. Sie müssen alle Protokolldateien bis auf eine löschen, bevor Sie diese Datenbank zu Azure migrieren: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Weitere Informationen: [Nicht unterstützte Datenbankoptionen in Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Nächste Spalte<a id="NextColumn"></a>

**Titel: Tabellen und Spalten mit dem Namen NEXT führen in Azure SQL Managed Instance zu einem Fehler.**    
**Kategorie**: Problem   

**Beschreibung**   
Es wurden Tabellen oder Spalten mit dem Namen NEXT erkannt. In Microsoft SQL Server eingeführte Sequenzen verwenden die ANSI-Standardfunktion NEXT VALUE FOR. Wenn der Name einer Tabelle oder Spalte NEXT lautet, für die Spalte als Alias VALUE verwendet wird und das ANSI-Standardschlüsselwort AS weggelassen wird, kann die resultierende Anweisung einen Fehler verursachen.


**Empfehlung**   
Schreiben Sie Anweisungen so um, dass sie das ANSI-Standardschlüsselwort AS enthalten, wenn für eine Tabelle oder Spalte ein Alias verwendet wird. Wenn eine Spalte beispielsweise den Namen NEXT hat und über den Alias VALUE verfügt, führt die Abfrage SELECT NEXT VALUE FROM TABLE zu einem Fehler und sollte wie folgt umgeschrieben werden: SELECT NEXT AS VALUE FROM TABLE. Entsprechend gilt auch Folgendes: Wenn eine Tabelle den Namen NEXT hat und über den Alias VALUE verfügt, führt die Abfrage SELECT Col1 FROM NEXT VALUE zu einem Fehler und sollte wie folgt umgeschrieben werden: SELECT Col1 FROM NEXT AS VALUE.



## <a name="non-ansi-style-left-outer-join"></a>Linke äußere Verknüpfung (kein ANSI-Format)<a id="NonANSILeftOuterJoinSyntax"></a>

**Titel: Linke äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Linke äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und in Azure SQL Managed Instance entfernt. 


**Empfehlung**   
Verwenden Sie die ANSI-Joinsyntax.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Rechte äußere Verknüpfung (kein ANSI-Format)<a id="NonANSIRightOuterJoinSyntax"></a>

**Titel: Rechte äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und entfernt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Rechte äußere Verknüpfung (kein ANSI-Format) wurde eingestellt und in Azure SQL Managed Instance entfernt. 



Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Empfehlung**   
Verwenden Sie die ANSI-Joinsyntax.

## <a name="databases-exceed-100"></a>Datenbankanzahl überschreitet 100 <a id="NumDbExceeds100"></a>

**Titel: Für Azure SQL Managed Instance werden maximal 100 Datenbanken pro Instanz unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die maximale Anzahl von Datenbanken, die in Azure SQL Managed Instance unterstützt wird, ist 100, sofern nicht die Instanzspeichergröße erreicht wurde.



**Empfehlung**   
Erwägen Sie, die Datenbanken zu anderen Azure SQL Managed Instances oder zu SQL Server auf einem virtuellen Azure-Computer zu migrieren, falls alle Datenbanken auf derselben Instanz vorhanden sein müssen. 

Weitere Informationen: [Ressourcenlimits für Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (Datenquelle (kein Blob))<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titel: OpenRowSet bei einem Massenvorgang bei einer Datenquelle, die kein Azure-Blobspeicher ist, wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Für OPENROWSET werden Massenvorgänge über einen integrierten BULK-Anbieter unterstützt, mit dem Daten aus einer Datei gelesen und als Rowset zurückgegeben werden können. OPENROWSET bei einer Datenquelle, die kein Azure-Blobspeicher ist, wird in Azure SQL Managed Instance nicht unterstützt. 

**Empfehlung**   
Eine Azure SQL Managed Instance kann nicht auf Dateifreigaben und Windows-Ordner zugreifen. Daher müssen die Dateien aus Azure Blob Storage importiert werden: Aus diesem Grund wird für die OPENROWSET-Funktion nur der Blobtyp DATASOURCE unterstützt. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance: Bulk insert/OPENROWSET](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (kein SQL-Anbieter)<a id="OpenRowsetWithNonSQLProvider"></a>

**Titel: OpenRowSet mit einem anderen Anbieter als SQL wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   

**Beschreibung**   
Diese Methode ist eine Alternative zum Zugriff auf Tabellen eines Verbindungsservers. Sie ist eine einmalig verwendete Ad-hoc-Methode zum Verbinden und Zugreifen auf Remotedaten mithilfe von OLE DB. OpenRowSet mit einem anderen Anbieter als SQL wird in Azure SQL Managed Instance nicht unterstützt. 



**Empfehlung**   
Die Funktion OPENROWSET kann nur auf SQL Server-Instanzen (entweder verwaltet, lokal oder auf virtuellen Computern) für die Ausführung von Abfragen verwendet werden. Nur die Werte SQLNCLI, SQLNCLI11 und SQLOLEDB werden als Anbieter unterstützt. Daher besteht die empfohlene Aktion darin, die abhängigen Datenbanken für die Remoteserver zu identifizieren, bei denen es sich nicht um SQL-Server handelt, und diese in die zu migrierende Datenbank zu verschieben.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance: Bulk insert/OPENROWSET](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell-Auftrag<a id="PowerShellJob"></a>

**Titel: PowerShell-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Hierbei handelt es sich um einen Auftragsschritt, bei dem ein PowerShell-Skript ausgeführt wird. PowerShell-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt. 



**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Es werden alle Aufträge angezeigt, für die der PowerShell-Auftragsschritt verwendet wird, und Sie können ermitteln, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann.  Ermitteln Sie, ob Azure Automation verwendet werden kann. Alternativ können Sie zu SQL Server auf dem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Warteschlangenleser-Auftrag<a id="QueueReaderJob"></a>

**Titel: Warteschlangenleser-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   

**Beschreibung**   
Hierbei handelt es sich um einen Auftragsschritt, bei dem der Warteschlangenlese-Agent für die Replikation aktiviert wird. Der Warteschlangenlese-Agent für die Replikation ist eine ausführbare Datei, die in einer Microsoft SQL Server-Warteschlange oder einer Microsoft Message Queue gespeicherte Nachrichten liest und diese dann auf den Herausgeber anwendet. Der Warteschlangenlese-Agent wird bei Momentaufnahme- und Transaktionsveröffentlichungen verwendet, die das verzögerte Update über eine Warteschlange gestatten. Warteschlangenleser-Auftragsschritt wird in Azure SQL Managed Instance nicht unterstützt.


**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Es werden alle Aufträge angezeigt, für die der Warteschlangenleser-Auftragsschritt verwendet wird, und Sie können ermitteln, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titel: RAISERROR-Aufrufe im Legacystil sollten durch moderne Äquivalente ersetzt werden.**    
**Kategorie**: Warnung   

**Beschreibung**   
RAISERROR-Aufrufe wie im folgenden Beispiel werden als Aufrufe im Legacystil bezeichnet, da sie keine Kommas und Klammern enthalten. RAISERROR 50001 „Dies ist ein Test“. Diese Methode zum Aufrufen von RAISERROR wurde eingestellt und in Azure SQL Managed Instance entfernt.



**Empfehlung**   
Schreiben Sie die Anweisung um, indem Sie die aktuelle RAISERROR-Syntax verwenden, oder ermitteln Sie, ob der moderne Ansatz mit `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` möglich ist.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Titel: SQL Mail wird nicht mehr unterstützt.**    
**Kategorie**: Warnung   


**Beschreibung**   
SQL Mail wurde eingestellt und in Azure SQL Managed Instance entfernt.



**Empfehlung**   
Verwenden Sie Datenbank-E-Mail.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Titel: Es wurden Anweisungen erkannt, in denen auf entfernte gespeicherte Systemprozeduren verwiesen wird, die in Azure SQL Managed Instance nicht verfügbar sind.**    
**Kategorie**: Warnung   

**Beschreibung**   
Die folgenden nicht unterstützten gespeicherten Systemprozeduren und erweiterten gespeicherten Prozeduren können in Azure SQL Managed Instance nicht verwendet werden: `sp_dboption`, `sp_addserver`, `sp_dropalias`, `sp_activedirectory_obj`, `sp_activedirectory_scp` und `sp_activedirectory_start`. 




**Empfehlung**   
Entfernen Sie Verweise auf nicht unterstützte Systemprozeduren, die in Azure SQL Managed Instance entfernt wurden.

Weitere Informationen: [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL-Auftrag<a id="TransactSqlJob"></a>

**Titel: T-SQL-Auftragsschritt enthält nicht unterstützte Befehle in Azure SQL Managed Instance.**    
**Kategorie**: Warnung   


**Beschreibung**   
Bei diesem Auftragsschritt werden T-SQL-Skripts zu geplanten Zeiten ausgeführt. Der T-SQL-Auftragsschritt enthält nicht unterstützte Befehle, die in Azure SQL Managed Instance nicht unterstützt werden.



**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Darin sind alle Aufträge mit Befehlen aufgeführt, die in Azure SQL Managed Instance nicht unterstützt werden. Ermitteln Sie, ob der Auftragsschritt oder das betroffene Objekt entfernt werden kann. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Ablaufverfolgungsflags<a id="TraceFlags"></a>

**Titel: Ablaufverfolgungsflags gefunden, die in Azure SQL Managed Instance nicht unterstützt werden.**    
**Kategorie**: Warnung   


**Beschreibung**   
Für Azure SQL Managed Instance wird nur eine begrenzte Zahl von globalen Ablaufverfolgungsflags unterstützt. Ablaufverfolgungsflags für Sitzungen werden nicht unterstützt.



**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Darin sind alle Ablaufverfolgungsflags aufgeführt, die in Azure SQL Managed Instance nicht unterstützt werden, und Sie können ermitteln, ob sie entfernt werden können. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Ablaufverfolgungsflags](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows-Authentifizierung<a id="WindowsAuthentication"></a>

**Titel: Datenbankbenutzer mit Zuordnung der Windows-Authentifizierung (integrierte Sicherheit) werden in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Warnung   


**Beschreibung**   
Azure SQL Managed Instance unterstützt zwei Arten der Authentifizierung: 
- SQL-Authentifizierung, bei der ein Benutzername und ein Kennwort verwendet werden
- Azure Active Directory-Authentifizierung, bei der von Azure Active Directory verwaltete Identitäten verwendet werden und die für verwaltete und integrierte Domänen unterstützt wird 

Datenbankbenutzer mit Zuordnung der Windows-Authentifizierung (integrierte Sicherheit) werden in Azure SQL Managed Instance nicht unterstützt. 


**Empfehlung**   
Stellen Sie für die lokale Active Directory-Instanz einen Verbund mit Azure Active Directory her. Die Windows-Identität kann dann durch die äquivalenten Azure Active Directory-Identitäten ersetzt werden. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Sicherheitsfunktionen für SQL Managed Instance](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>xp_cmdshell<a id="XpCmdshell"></a>

**Titel: „xp_cmdshell“ wird in Azure SQL Managed Instance nicht unterstützt.**    
**Kategorie**: Problem   


**Beschreibung**   
„xp_cmdshell“ für die Erstellung einer Windows-Befehlsshell und die Übergabe einer Zeichenfolge für die Ausführung wird in Azure SQL Managed Instance nicht unterstützt. 



**Empfehlung**   
Sehen Sie sich in Azure Migrate den Abschnitt mit den betroffenen Objekten an. Darin sind alle Objekte aufgeführt, für die „xp_cmdshell“ verwendet wird, und Sie können ermitteln, ob der Verweis auf „xp_cmdshell“ oder das betroffene Objekt entfernt werden kann. Erwägen Sie, sich mit Azure Automation vertraut zu machen, um einen cloudbasierten Dienst für die Automatisierung und Konfiguration nutzen zu können. Alternativ können Sie zu SQL Server auf einem virtuellen Azure-Computer migrieren.

Weitere Informationen: [Gespeicherte Prozeduren: Unterschiede in Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Beginn der Migration Ihrer SQL Server-Instanz zu Azure SQL Managed Instance finden Sie im [Leitfaden für die Migration von SQL Server zu SQL Managed Instance](sql-server-to-managed-instance-guide.md).

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
