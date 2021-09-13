---
title: 'Auflösen von T-SQL-Unterschieden: Migration'
description: T-SQL-Anweisungen, die in Azure SQL-Datenbank nicht vollständig unterstützt werden
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004590"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>T-SQL-Unterschiede zwischen SQL Server und Azure SQL-Datenbank

Beim [Migrieren Ihrer Datenbank](migrate-to-database-from-sql-server.md) von SQL Server zu Azure SQL-Datenbank stellen Sie möglicherweise fest, dass Ihre SQL Server-Datenbanken vor der Migration etwas umstrukturiert werden müssen. Dieser Artikel enthält Anleitungen, die Ihnen sowohl bei der Durchführung dieser Umstrukturierung als auch beim Verstehen der Gründe helfen, warum diese Umstrukturierung notwendig ist. Verwenden Sie den [Datenmigrations-Assistenten (DMA)](/sql/dma/dma-overview), um Inkompatibilitäten zu erkennen und Datenbanken zu Azure SQL-Datenbank zu migrieren.

## <a name="overview"></a>Übersicht

Die meisten T-SQL-Funktionen, die von Anwendungen verwendet werden, werden in Microsoft SQL Server und in Azure SQL-Datenbank unterstützt. Die zentralen SQL-Komponenten wie Datentypen, Operatoren sowie Zeichenfolgen-, Arithmetik-, logische und Cursorfunktionen funktionieren in SQL Server und in SQL-Datenbank gleich. Es gibt jedoch einige T-SQL-Unterschiede zwischen Elementen der Datendefinitionssprache (Data Definition Language, DDL) und der Datenbearbeitungssprache (Data Manipulation Language, DML), die dazu führen, dass T-SQL-Anweisungen und -Abfragen nur teilweise unterstützt werden (mehr dazu weiter unten in diesem Artikel).

Darüber hinaus werden einige Features und bestimmte Syntax überhaupt nicht unterstützt, da Azure SQL-Datenbank darauf ausgelegt ist, Features von Systemdatenbank- und Betriebssystemabhängigkeiten zu isolieren. Daher werden die meisten Features auf Instanzebene in SQL-Datenbank nicht unterstützt. T-SQL-Anweisungen und -Optionen sind nicht verfügbar, wenn sie Optionen auf Instanzebene oder Betriebssystemkomponenten konfigurieren oder Dateisystemkonfigurationen angeben. Wenn solche Funktionen erforderlich sind, dann ist häufig eine andere geeignete Alternative von der SQL-Datenbank oder aus einem anderen Azure-Feature oder -Dienst verfügbar.

Hochverfügbarkeit ist beispielsweise in Azure SQL-Datenbank integriert. T-SQL-Anweisungen, die sich auf Verfügbarkeitsgruppen beziehen, werden nicht von der SQL-Datenbank unterstützt, und die dynamischen Verwaltungssichten, die sich auf AlwaysOn-Verfügbarkeitsgruppen beziehen, werden auch nicht unterstützt.

Eine Liste der Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](features-comparison.md). Diese Seite ergänzt diesen Artikel und konzentriert sich auf T-SQL-Anweisungen.

## <a name="t-sql-syntax-statements-with-partial-differences"></a>T-SQL-Syntaxanweisungen mit partiellen Unterschieden

Die grundlegenden DDL-Anweisungen sind verfügbar, DDL-Anweisungserweiterungen im Zusammenhang mit nicht unterstützten Features (etwa Dateiplatzierung auf dem Datenträger) werden jedoch nicht unterstützt.

- In SQL Server stehen für Anweisungen vom Typ `CREATE DATABASE` und `ALTER DATABASE` mehr als drei Dutzend Optionen zur Verfügung. Die Anweisungen enthalten Ablage von Dateien, FILESTREAM und Service Broker-Optionen, die nur für SQL Server gelten. Wenn Sie vor der Migration Datenbanken in SQL Server erstellen, ist dies möglicherweise nicht relevant. Wenn Sie allerdings T-SQL-Code migrieren, der Datenbanken erstellt, sollten Sie [CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) mit der SQL Server-Syntax in [CREATE DATABASE (SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true) vergleichen, um sicherzustellen, dass alle verwendeten Optionen unterstützt werden. `CREATE DATABASE` für Azure SQL-Datenbank verfügt auch über Optionen für Dienstziele und für Pools für elastische Datenbanken, die nur für SQL-Datenbank gelten.
- Die Anweisungen `CREATE TABLE` und `ALTER TABLE` verfügen über Optionen vom Typ `FILETABLE` und `FILESTREAM`, die nicht mit SQL-Datenbank verwendet werden können, da diese Features nicht unterstützt werden.
- Anweisungen vom Typ `CREATE LOGIN` und `ALTER LOGIN` werden zwar unterstützt, bieten aber weniger Optionen als in SQL Server zur Verfügung stehen. Um die Portabilität der Datenbank zu verbessern, unterstützt die SQL-Datenbank nach Möglichkeit die Verwendung von eigenständigen Datenbankbenutzern anstelle von Anmeldungen. Weitere Informationen finden Sie unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true) und [ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true) sowie unter [Autorisieren des Datenbankzugriffs für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](logins-create-manage.md).

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>In Azure SQL-Datenbank nicht unterstützte T-SQL-Syntax

Zusätzlich zu T-SQL-Anweisungen, die sich auf die unter [Featurevergleich: Azure SQL-Datenbank und Azure SQL Managed Instance](features-comparison.md) beschriebenen nicht unterstützten Features beziehen, werden die folgenden Anweisungen und Gruppen von Anweisungen nicht unterstützt. Wenn die zu migrierende Datenbank eines der folgenden Features verwendet, muss Ihre Anwendung umstrukturiert werden, um die betroffenen T-SQL-Features und -Anweisungen zu entfernen.

- Sortierung von Systemobjekten.
- Verbindungsbezogen: Endpunktanweisungen. SQL-Datenbank unterstützt keine Windows-Authentifizierung. aber die Azure Active Directory-Authentifizierung. Dies schließt die Authentifizierung von Active Directory-Prinzipalen im Verbund mit Azure Active Directory ein. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank oder Azure Synapse Analytics unter Verwendung der Azure Active Directory-Authentifizierung](authentication-aad-overview.md).
- Datenbank- und instanzübergreifende Abfragen mit drei- oder vierteiligen Namen. Dreiteilige Namen, die auf die Datenbank `tempdb` und auf die aktuelle Datenbank verweisen, werden unterstützt. Von [elastischen Abfragen](elastic-query-overview.md) werden schreibgeschützte Verweise auf Tabellen in anderen MSSQL-Datenbanken unterstützt.
- Datenbankübergreifende Besitzketten und die Datenbankeigenschaft `TRUSTWORTHY`.
- `EXECUTE AS LOGIN`. Verwenden Sie stattdessen `EXECUTE AS USER`.
- Erweiterbare Schlüsselverwaltung (Extensible Key Management, EKM) für Verschlüsselungsschlüssel. [Kundenseitig verwaltete Schlüssel](transparent-data-encryption-byok-overview.md) für Transparent Data Encryption (TDE) sowie [Spaltenmasterschlüssel](always-encrypted-azure-key-vault-configure.md) für Always Encrypted können in Azure Key Vault gespeichert werden.
- Ereignisse: Ereignisbenachrichtigungen, Abfragebenachrichtigungen.
- Dateieigenschaften: Syntax im Zusammenhang mit Datenbankdateiname, Platzierung, Größe und anderen Dateieigenschaften, die automatisch von SQL-Datenbank verwaltet werden.
- Hochverfügbarkeit: Syntax im Zusammenhang mit Hochverfügbarkeit und Datenbankwiederherstellung (werden von SQL-Datenbank verwaltet). Dies schließt die Syntax für Sicherung, Wiederherstellung, Always On, Datenbankspiegelung, Protokollversand und Wiederherstellungsmodelle ein.
- Syntax im Zusammenhang mit der Momentaufnahme-, Transaktions- und Mergereplikation (in SQL-Datenbank nicht verfügbar). [Replikationsabonnements](replication-to-sql-database.md) werden unterstützt.
- Funktionen: `fn_get_sql`, `fn_virtualfilestats` und `fn_virtualservernodes`.
- Instanzkonfiguration: Syntax im Zusammenhang mit Serverarbeitsspeicher, Arbeitsthreads, CPU-Affinität und Ablaufverfolgungsflags. Verwenden Sie stattdessen Dienstebenen und Computegrößen.
- `KILL STATS JOB`.
- `OPENQUERY`, `OPENDATASOURCE` und vierteilige Namen.
- .NET Framework: CLR-Integration
- Semantische Suche
- Serveranmeldeinformationen: Verwenden Sie stattdessen [datenbankbezogene Anmeldeinformationen](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL).
- Berechtigungen auf Serverebene: `GRANT`, `REVOKE` und `DENY` für Berechtigungen auf Serverebene werden nicht unterstützt. Einige Berechtigungen auf Serverebene werden durch Berechtigungen auf Datenbankebene ersetzt oder implizit durch integrierte Serverrollen gewährt. Für einige DMVs und Katalogsichten auf Serverebene stehen ähnliche Sichten auf Datenbankebene zur Verfügung.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` und `RECONFIGURE`. [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) wird unterstützt.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server-Agent: Syntax, die auf dem SQL Server-Agent oder der MSDB-Datenbank beruht: Warnungen, Operatoren, zentrale Verwaltungsserver. Verwenden Sie stattdessen Skripts (beispielsweise PowerShell).
- SQL Server-Überwachung: Verwenden Sie stattdessen die [Überwachung](auditing-overview.md) von SQL-Datenbank.
- SQL Server-Ablaufverfolgung.
- Ablaufverfolgungskennzeichen.
- T-SQL-Debugging.
- Trigger im Gültigkeitsbereich des Servers oder Anmeldetrigger.
- Anweisung `USE`: Wenn Sie den Datenbankkontext in eine andere Datenbank ändern möchten, müssen Sie eine neue Verbindung mit dieser Datenbank erstellen.

## <a name="full-t-sql-reference"></a>Vollständige T-SQL-Referenz

Weitere Informationen zur Grammatik und Syntax von T-SQL sowie Beispiele finden Sie unter [Transact-SQL-Referenz (Datenbank-Engine)](/sql/t-sql/language-reference).

### <a name="about-the-applies-to-tags"></a>Informationen zu Tags vom Typ "Gilt für"

Die T-SQL-Referenz enthält Artikel zu allen aktuellen SQL Server-Versionen. Unter der Artikelüberschrift befindet sich eine Symbolleiste, die angibt, für welche MSSQL-Plattformen der jeweilige Artikel gilt. Beispielsweise wurden Verfügbarkeitsgruppen in SQL Server 2012 eingeführt. Der Artikel [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) gibt an, dass die Anweisung für **SQL Server gilt (ab SQL Server 2012)** . Die Anweisung gilt nicht für SQL Server 2008, SQL Server 2008 R2, Azure SQL-Datenbank, Azure Synapse Analytics oder Parallel Data Warehouse.

In einigen Fällen kann der allgemeine Gegenstand eines Artikels in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Artikel entsprechend angegeben. Der Artikel `CREATE TRIGGER` ist beispielsweise in SQL-Datenbank verfügbar. Die Option `ALL SERVER` für Trigger auf Serverebene signalisiert jedoch, dass Trigger auf Serverebene in SQL-Datenbank nicht verwendet werden können. Verwenden Sie stattdessen Trigger auf Datenbankebene.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](features-comparison.md).

Verwenden Sie den [Datenmigrations-Assistenten](/sql/dma/dma-overview), um Kompatibilitätsprobleme in Ihren SQL Server-Datenbanken vor der Migration zu Azure SQL-Datenbank zu erkennen und Ihre Datenbanken zu migrieren.