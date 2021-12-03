---
title: T-SQL-Feature im Synapse SQL-Pool
description: Liste der Transact-SQL-Funktionen, die in Synapse SQL verfügbar sind.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 074f6dffae5067676e073916018126c5c2f07d85
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850563"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>In Azure Synapse SQL unterstützte Transact-SQL-Funktionen

Azure Synapse SQL ist ein Big Data-Analysedienst, mit dem Sie Ihre Daten per T-SQL-Sprache abfragen und analysieren können. Sie können einen ANSI-kompatiblen Standarddialekt der SQL-Sprache verwenden, die in SQL Server und Azure SQL-Datenbank für die Datenanalyse verwendet wird. 

Die Transact-SQL-Sprache wird im serverlosen SQL-Pool verwendet. Das dedizierte Modell kann auf verschiedene Objekte verweisen, und bei den unterstützten Funktionen gibt es gewisse Unterschiede. Auf dieser Seite finden Sie allgemeine Transact-SQL-Sprachunterschiede zwischen den Verbrauchsmodellen von Synapse SQL.

## <a name="database-objects"></a>Datenbankobjekte

Verbrauchsmodelle in Synapse SQL ermöglichen die Verwendung verschiedener Datenbankobjekte. Die folgende Tabelle gibt Aufschluss über die unterstützten Objekttypen:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Tabellen** | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Nein. Im Rahmen des serverlosen Modells können nur externe Daten in [Azure Storage](#storage-options) abgefragt werden. |
| **Ansichten** | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Von Sichten können [Abfragesprachelemente](#query-language) verwendet werden, die im dedizierten Modell verfügbar sind. | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Von Sichten können [Abfragesprachelemente](#query-language) verwendet werden, die im serverlosen Modell verfügbar sind. |
| **Schemas** | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Temporäre Tabellen** | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?context=/azure/synapse-analytics/context/context) | Nein |
| **Vorgehensweisen** | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja |
| **Funktionen** | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Ja, nur Inline-Tabellenwertfunktionen |
| **Trigger** | Nein | Nein |
| **Externe Tabellen** | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Weitere Informationen finden Sie in den unterstützten [Datenformaten](#data-formats). | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Weitere Informationen finden Sie in den unterstützten [Datenformaten](#data-formats). |
| **Zwischenspeichern von Abfragen** | Ja, mehrere Varianten (SSD-basierte Zwischenspeicherung, In-Memory, Resultset-Zwischenspeicherung). Außerdem wird die materialisierte Sicht unterstützt. | Nein |
| **Tabellenvariablen** | [Nein](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Verwenden Sie temporäre Tabellen. | Nein |
| **[Tabellenverteilung](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)**               | Ja | Nein |
| **[Tabellenindizes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)**                           | Ja | Nein |
| **[Tabellenpartitionen](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)**                     | Ja | Nein |
| **[Statistik](develop-tables-statistics.md)**            | Ja | Ja |
| **[Workloadverwaltung, Ressourcenklassen und Gleichzeitigkeitssteuerung](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context)** | Ja    | Nein |
| **Kostenkontrolle** | Ja, mithilfe von Aktionen zum Hochskalieren und Herunterskalieren | Ja, mithilfe des [Azure-Portals oder der T-SQL-Prozedur](./data-processed.md#cost-control) |

## <a name="query-language"></a>Abfragesprache

Die unterstützten Funktionen der in Synapse SQL verwendeten Abfragesprachen können sich abhängig vom Verbrauchsmodell unterscheiden. In der folgenden Tabelle sind die wichtigsten Unterschiede bei der Abfragesprache in Transact-SQL-Dialekten aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **SELECT-Anweisung** | Ja. Die Transact-SQL-Abfrageklauseln [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true) und OFFSET/FETCH werden nicht unterstützt. | Ja. Die Transact-SQL-Abfrageklauseln [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), [PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true), GROUPING SETS und Abfragehinweise werden nicht unterstützt. |
| **INSERT-Anweisung** | Ja | Nein |
| **UPDATE-Anweisung** | Ja | Nein |
| **DELETE-Anweisung** | Ja | Nein |
| **MERGE-Anweisung** | Ja ([Vorschau](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | Nein |
| **[Transaktionen](develop-transactions.md)** | Ja | Ja, gültig für Metadatenobjekte |
| **[Bezeichnungen](develop-label.md)** | Ja | Nein |
| **Ladevorgänge für Daten** | Ja. Das bevorzugte Hilfsprogramm ist zwar die [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)-Anweisung, vom System werden jedoch auch Massenladen (BCP) sowie [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) zum Laden von Daten unterstützt. | Nein |
| **Datenexport** | Ja. Unter Verwendung von [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Unter Verwendung von [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Typen** | Ja. Alle Transact-SQL-Typen außer [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text und image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [räumliche Typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Alle Transact-SQL-Typen außer [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text und image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [räumliche Typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) und Tabellentyp. |
| **Datenbankübergreifende Abfragen** | Nein | Ja, einschließlich [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true)-Anweisung. |
| **Integrierte Funktionen (Analyse)** | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Analyse](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), Konvertierung, [Datum und Uhrzeit](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), Logik und [Mathematik](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [PARSE](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Analyse](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), Konvertierung, [Datum und Uhrzeit](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), Logik und [Mathematik](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Integrierte Funktionen ([Zeichenfolge](/sql/t-sql/functions/string-functions-transact-sql))** | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Zeichenfolgen](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) und Sortierung mit Ausnahme von [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Zeichenfolgen](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) und Sortierung. |
| **Integrierte Funktionen ([Kryptografisch](/sql/t-sql/functions/cryptographic-functions-transact-sql))** | Einige | Nein |
| **Integrierte Tabellenwertfunktionen** | Ja. [Transact-SQL-Rowsetfunktionen](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions) mit Ausnahme von [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. [Transact-SQL-Rowsetfunktionen](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions) mit Ausnahme von [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true).  |
| **Aggregate** |  Integrierte Transact-SQL-Aggregate mit Ausnahme von [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Integrierte Transact-SQL-Aggregate |
| **Operatoren** | Ja. Alle [Transact-SQL-Operatoren](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Alle [Transact-SQL-Operatoren](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true).  |
| **Ablaufsteuerung** | Ja. Alle [Transact-SQL-Ablaufsteuerungsanweisungen](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Alle [Transact-SQL-Ablaufsteuerungsanweisungen](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) und SELECT-Abfrage in der Bedingung `WHILE (...)` |
| **DDL-Anweisungen (CREATE, ALTER, DROP)** | Ja. Alle auf die unterstützten Objekttypen anwendbaren Transact-SQL-DDL-Anweisungen. | Ja. Alle auf die unterstützten Objekttypen anwendbaren Transact-SQL-DDL-Anweisungen. |

## <a name="security"></a>Sicherheit

Synapse SQL-Pools ermöglichen die Verwendung integrierter Sicherheitsfeatures, um Ihre Daten zu schützen und den Zugriff zu steuern. In der folgenden Tabelle werden allgemeine Unterschiede zwischen den Synapse-SQL-Verbrauchsmodellen gegenübergestellt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Anmeldungen** | Nicht zutreffend (In Datenbanken werden nur enthaltene Benutzer unterstützt.) | Ja |
| **Benutzer** |  Nicht zutreffend (In Datenbanken werden nur enthaltene Benutzer unterstützt.) | Ja |
| **[Eigenständige Benutzer](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Ja. **Hinweis:** Es kann immer nur ein einzelner Azure AD-Benutzer uneingeschränkter Administrator sein. | Nein  |
| **Authentifizierung mit SQL-Benutzername/Kennwort**| Ja | Ja |
| **Azure Active Directory-Authentifizierung (Azure AD)**| Ja, Azure AD-Benutzer | Ja, Azure AD-Anmeldungen und -Benutzer |
| **Passthrough-Authentifizierung für Azure Active Directory-Speicher (Azure AD)** | Ja | Ja |
| **SAS-Tokenauthentifizierung für den Speicher** | Nein | Ja, unter Verwendung von [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) oder [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) (auf Instanzebene). |
| **Authentifizierung per Speicherzugriffsschlüssel** | Ja, unter Verwendung von [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Nein |
| **Authentifizierung für den Speicher mittels [verwalteter Identität](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)** | Ja, unter Verwendung von [Anmeldeinformationen für eine verwaltete Dienstidentität](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true). | Ja, unter Verwendung von Anmeldeinformationen vom Typ `Managed Identity`. |
| **Authentifizierung für den Speicher mittels Anwendungsidentität** | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nein |
| **Rollen auf Serverebene** | Nein | Ja. Systemadministrator, öffentliche und andere Serverrollen werden unterstützt. |
| **SERVER SCOPED CREDENTIAL** | Nein | Ja |
| **Berechtigungen – [Serverebene](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nein | Ja |
| **Datenbankweite Rollen** | Ja | Ja |
| **DATABASE SCOPED CREDENTIAL** | Ja | Ja |
| **Berechtigungen – [Datenbankebene](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Ja | Ja |
| **Berechtigungen – Schemaebene** | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für das Schema. | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für das Schema. |
| **Berechtigungen – Objektebene** | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer. | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für die unterstützten Systemobjekte. |
| **Berechtigungen – [Sicherheit auf Spaltenebene](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Ja | Ja |
| **Sicherheit&amp; Identitätsfunktionen** | Einige Transact-SQL-Sicherheitsfunktionen und -operatoren: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Einige Transact-SQL-Sicherheitsfunktionen und -operatoren: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SESSION_CONTEXT`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` und `REVERT`. Sicherheitsfunktionen können nicht zum Abfragen externer Daten verwendet werden. (Speichern Sie das Ergebnis in einer Variablen, die in der Abfrage verwendet werden kann.)  |
| **Sicherheit auf Zeilenebene** | [Ja](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nein |
| **Transparente Datenverschlüsselung (TDE)** | [Ja](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | Nein | 
| **Datenermittlung und -klassifizierung** | [Ja](../../azure-sql/database/data-discovery-and-classification-overview.md) | Nein |
| **Sicherheitsrisikobewertung** | [Ja](../../azure-sql/database/sql-vulnerability-assessment.md) | Nein |
| **Advanced Threat Protection für Azure SQL-Datenbank** | [Ja](../../azure-sql/database/threat-detection-overview.md)
| **Überwachung** | [Ja](../../azure-sql/database/auditing-overview.md) | Nein |
| **[Firewallregeln](../security/synapse-workspace-ip-firewall.md)**| Ja | Ja |
| **[Privater Endpunkt](../security/synapse-workspace-managed-private-endpoints.md)**| Ja | Ja |

Ein dedizierter SQL-Pool und ein serverloser SQL-Pool verwenden die Transact-SQL-Standardsprache, um Daten abzufragen. Ausführliche Informationen zu den Unterschieden finden Sie in der [Transact-SQL-Sprachreferenz](/sql/t-sql/language-reference).

## <a name="tools"></a>Tools

Sie können verschiedene Tools verwenden, um eine Verbindung mit Synapse SQL herzustellen und Daten abzufragen.

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Synapse Studio** | Ja, SQL-Skripts. | Ja, SQL-Skripts. |
| **Power BI** | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Ja | Ja |
| **Azure Data Studio** | Ja | Ja, ab Version 1.18.0. SQL-Skripts und -Notebooks werden unterstützt. |
| **SQL Server Management Studio** | Ja | Ja, ab Version 18.5 |

> [!NOTE]
> Sie können mithilfe von SSMS eine Verbindung mit einem serverlosen SQL-Pool herstellen und Abfragen durchführen. Es wird ab Version 18.5 teilweise unterstützt, kann aber nur zum Herstellen einer Verbindung und für Abfragen verwendet werden.

Die meisten Anwendungen verwenden die Transact-SQL-Standardsprache und können sowohl dedizierte als auch serverlose Verbrauchsmodelle von Synapse SQL abfragen.

## <a name="storage-options"></a>Speicheroptionen

Die zu analysierenden Daten können in verschiedenen Arten von Speicher gespeichert sein. In der folgenden Tabelle sind alle verfügbaren Speicheroptionen aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Interner Speicher** | Ja | Nein |
| **Azure Data Lake v2** | Ja | Ja |
| **Azure Blob Storage** | Ja | Ja |
| **Azure SQL (Remote)** | Nein  | Nein  |
| **Transaktionaler Azure CosmosDB-Speicher** | Nein  | Nein  |
| **Analytischer Speicher für Azure CosmosDB** | Nein | Ja, unter Verwendung von [Synapse Link](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) |
| **Apache Spark-Tabellen (im Arbeitsbereich)** | Nein  | PARQUET-Tabellen, die nur [Metadatensynchronisierung](develop-storage-files-spark-tables.md) verwenden |
| **Apache Spark-Tabellen (Remote)** | Nein  | Nein  |
| **Databricks-Tabellen (Remote)** | Nein | Nein |

## <a name="data-formats"></a>Datenformate

Zu analysierende Daten können in verschiedenen Speicherformaten gespeichert sein. In der folgenden Tabelle sind alle für die Analyse verfügbaren Datenformate aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Mit Trennzeichen** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-single-csv-file.md) |
| **CSV** | Ja. (Trennzeichen mit mehreren Zeichen werden nicht unterstützt.) | [Ja](query-single-csv-file.md) |
| **Parquet** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-parquet-files.md), einschließlich Dateien mit [geschachtelten Typen](query-parquet-nested-types.md). |
| **Hive ORC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nein |
| **Hive RC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nein |
| **JSON** | Ja | [Ja](query-json-files.md) |
| **Avro** | Nein | Nein |
| **[Delta-Lake](https://delta.io/)** | Nein | [Ja](query-delta-lake-format.md) |
| **[CDM](/common-data-model/)** | Nein | Nein |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu bewährten Methoden für dedizierte SQL-Pools und serverlose SQL-Pools finden Sie in den folgenden Artikeln:

- [Best Practices für dedizierte SQL-Pools](best-practices-dedicated-sql-pool.md)
- [Best Practices für serverlose SQL-Pools](best-practices-serverless-sql-pool.md)
