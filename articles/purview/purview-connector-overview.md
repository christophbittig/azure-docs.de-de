---
title: Purview-Connector – Übersicht
description: In diesem Artikel sind die verschiedenen in Purview unterstützten Datenspeicher und Funktionen skizziert.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 58b8ef4959c54e8c597e3f54864caae77dfd1fa8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061293"
---
# <a name="supported-data-stores"></a>Unterstützte Datenspeicher

Purview unterstützt die folgenden Datenspeicher. Wählen Sie die einzelnen Datenspeicher aus, um mehr über die unterstützten Funktionen und die entsprechende Konfiguration zu erfahren.

## <a name="purview-data-sources"></a>Purview-Datenquellen

|**Kategorie**|  **Datenspeicher**  |**Metadatenextrahierung**|**Vollständige Überprüfung**|**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [Ja](register-scan-azure-blob-storage-source.md#register) | [Ja](register-scan-azure-blob-storage-source.md#scan)|[Ja](register-scan-azure-blob-storage-source.md#scan) | [Ja](register-scan-azure-blob-storage-source.md#scan)|[Ja](register-scan-azure-blob-storage-source.md#scan)| Ja | Nein|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Ja](register-scan-azure-cosmos-database.md#register) | [Ja](register-scan-azure-cosmos-database.md#scan)|[Ja](register-scan-azure-cosmos-database.md#scan) | [Ja](register-scan-azure-cosmos-database.md#scan)|[Ja](register-scan-azure-cosmos-database.md#scan)|Nein|Nein|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)| [Ja](register-scan-azure-data-explorer.md#register) | [Ja](register-scan-azure-data-explorer.md#scan) | [Ja](register-scan-azure-data-explorer.md#scan) | [Ja](register-scan-azure-data-explorer.md#scan)| [Ja](register-scan-azure-data-explorer.md#scan)| Nein | Nein |
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Ja](register-scan-adls-gen1.md#register) | [Ja](register-scan-adls-gen1.md#scan)|[Ja](register-scan-adls-gen1.md#scan) | [Ja](register-scan-adls-gen1.md#scan)|[Ja](register-scan-adls-gen1.md#scan)| Nein |[Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Ja](register-scan-adls-gen2.md#register) | [Ja](register-scan-adls-gen2.md#scan)|[Ja](register-scan-adls-gen2.md#scan) | [Ja](register-scan-adls-gen2.md#scan)|[Ja](register-scan-adls-gen2.md#scan)| Ja | [Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
||[Dedizierter Azure-SQL-Pool (vormals SQL DW)](register-scan-azure-synapse-analytics.md)| [Ja](register-scan-azure-synapse-analytics.md#register) | [Ja](register-scan-azure-synapse-analytics.md#scan)| [Ja](register-scan-azure-synapse-analytics.md#scan)| [Ja](register-scan-azure-synapse-analytics.md#scan)| [Ja](register-scan-azure-synapse-analytics.md#scan)| Nein | Nein|
||[Azure Files](register-scan-azure-files-storage-source.md)|[Ja](register-scan-azure-files-storage-source.md#register) | [Ja](register-scan-azure-files-storage-source.md#scan) | [Ja](register-scan-azure-files-storage-source.md#scan) | [Ja](register-scan-azure-files-storage-source.md#scan) | [Ja](register-scan-azure-files-storage-source.md#scan) | Nein | Nein |
||[Azure SQL-Datenbank](register-scan-azure-sql-database.md)| [Ja](register-scan-azure-sql-database.md#register) | [Ja](register-scan-azure-sql-database.md#scan)|[Ja](register-scan-azure-sql-database.md#scan) | [Ja](register-scan-azure-sql-database.md#scan)|[Ja](register-scan-azure-sql-database.md#scan)| Nein |[Data Factory-Datenherkunft](how-to-link-azure-data-factory.md)|
||[Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md)| [Ja](register-scan-azure-sql-database-managed-instance.md#register) | [Ja](register-scan-azure-sql-database-managed-instance.md#scan)| [Ja](register-scan-azure-sql-database-managed-instance.md#scan) | [Ja](register-scan-azure-sql-database-managed-instance.md#scan) | [Ja](register-scan-azure-sql-database-managed-instance.md#scan) | Nein | [Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
||[Azure Synapse Analytics (Arbeitsbereich)](register-scan-synapse-workspace.md)| [Ja](register-scan-synapse-workspace.md#register) | [Ja](register-scan-synapse-workspace.md#scan)| [Ja](register-scan-synapse-workspace.md#scan) | [Ja](register-scan-synapse-workspace.md#scan)| [Ja](register-scan-synapse-workspace.md#scan)| Nein| [Ja](how-to-lineage-azure-synapse-analytics.md)|
||[Azure Database for MySQL](register-scan-azure-mysql-database.md)| [Ja](register-scan-azure-mysql-database.md#register) | [Ja](register-scan-azure-mysql-database.md#scan)| [Ja*](register-scan-azure-mysql-database.md#scan) | [Ja](register-scan-azure-mysql-database.md#scan) | [Ja](register-scan-azure-mysql-database.md#scan) | Nein | [Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
||[Azure-Datenbank für PostgreSQL](register-scan-azure-postgresql.md)| [Ja](register-scan-azure-postgresql.md#register) | [Ja](register-scan-azure-postgresql.md#scan)| [Ja](register-scan-azure-postgresql.md#scan) | [Ja](register-scan-azure-postgresql.md#scan) | [Ja](register-scan-azure-postgresql.md#scan) | Nein | [Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
|Datenbank|[Cassandra](register-scan-cassandra-source.md)|[Ja](register-scan-cassandra-source.md#register) | [Ja](register-scan-cassandra-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-cassandra.md)|
||[Google BigQuery](register-scan-google-bigquery-source.md)| [Ja](register-scan-google-bigquery-source.md#register)| [Ja](register-scan-google-bigquery-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-google-bigquery.md)|
||[Hive Metastore DB](register-scan-hive-metastore-source.md)| [Ja](register-scan-hive-metastore-source.md#register)| [Ja](register-scan-hive-metastore-source.md#scan)| Nein | Nein | Nein | Nein| Ja |
||[Oracle Database](register-scan-oracle-source.md)| [Ja](register-scan-oracle-source.md#register)| [Ja](register-scan-oracle-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-oracle.md)|
||[SQL Server](register-scan-on-premises-sql-server.md)| [Ja](register-scan-on-premises-sql-server.md#register) | [Ja](register-scan-on-premises-sql-server.md#scan) | [Ja](register-scan-on-premises-sql-server.md#scan) | [Ja](register-scan-on-premises-sql-server.md#scan) | [Ja](register-scan-on-premises-sql-server.md#scan) | Nein| [Data Factory-Datenherkunft](how-to-link-azure-data-factory.md) |
||[Teradata](register-scan-teradata-source.md)| [Ja](register-scan-teradata-source.md#register)| [Ja](register-scan-teradata-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-teradata.md)|
|Datei|[Amazon S3](register-scan-amazon-s3.md)|[Ja](register-scan-amazon-s3.md)| [Ja](register-scan-amazon-s3.md)| [Ja](register-scan-amazon-s3.md)| [Ja](register-scan-amazon-s3.md)| [Ja](register-scan-amazon-s3.md)| Nein| Ja|
|Dienste und Apps|[Erwin](register-scan-erwin-source.md)| [Ja](register-scan-erwin-source.md#register)| [Ja](register-scan-erwin-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-erwin.md)|
||[Looker](register-scan-looker-source.md)| [Ja](register-scan-looker-source.md#register)| [Ja](register-scan-looker-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-looker.md)|
||[Power BI](register-scan-power-bi-tenant.md)| [Ja](register-scan-power-bi-tenant.md#register)| [Ja](register-scan-power-bi-tenant.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-powerbi.md)|
||[SAP ECC](register-scan-sapecc-source.md)| [Ja](register-scan-sapecc-source.md#register)| [Ja](register-scan-sapecc-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-sapecc.md)|
||[SAP S4HANA](register-scan-saps4hana-source.md)| [Ja](register-scan-saps4hana-source.md#register)| [Ja](register-scan-saps4hana-source.md#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-sapecc.md)|

\* Purview basiert auf UPDATE_TIME Metadaten aus der Azure Database für MySQL für inkrementelle Überprüfungen. In einigen Fällen wird dieses Feld möglicherweise nicht in der Datenbank beibehalten, und es wird eine vollständige Überprüfung durchgeführt. Weitere Informationen finden Sie in der Tabelle [INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) für MySQL. 

> [!NOTE]
> Derzeit kann Purview ein Objekt mit `/`, `\` oder `#` im Namen nicht überprüfen. Verwenden Sie das Beispiel in [Registrieren und scannen Sie eine Azure SQL-Datenbank](register-scan-azure-sql-database.md#creating-the-scan), um den Umfang ihrer Überprüfung zu überprüfen und das Scannen von Ressourcen mit solchen Zeichen im Objektnamen zu vermeiden.

\* Purview basiert auf UPDATE_TIME Metadaten aus der Azure Database für MySQL für inkrementelle Überprüfungen. In einigen Fällen wird dieses Feld möglicherweise nicht in der Datenbank beibehalten, und es wird eine vollständige Überprüfung durchgeführt. Weitere Informationen finden Sie in der Tabelle [INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) für MySQL. 

> [!NOTE]
> Derzeit kann Purview ein Objekt mit `/`, `\` oder `#` im Namen nicht überprüfen. Verwenden Sie das Beispiel in [Registrieren und scannen Sie eine Azure SQL-Datenbank](register-scan-azure-sql-database.md#creating-the-scan), um den Umfang ihrer Überprüfung zu überprüfen und das Scannen von Ressourcen mit solchen Zeichen im Objektnamen zu vermeiden.

## <a name="scan-regions"></a>Überprüfungsregionen
Im Folgenden finden Sie eine Liste aller Azure-Datenquellregionen (Rechenzentren), in denen die Purview-Überprüfung ausgeführt wird. Wenn sich Ihre Azure-Datenquelle in einer Region befindet, die nicht in der Liste aufgeführt ist, erfolgt die Überprüfung in der Region Ihrer Purview-Instanz.
 
### <a name="purview-scanner-regions"></a>Regionen der Purview-Überprüfung

- EastUs
- EastUs2 
- USA, Süden-Mitte
- WestUs
- WestUs2
- SoutheastAsia
- Europa, Westen
- Europa, Norden
- UkSouth
- Australien, Osten
- CanadaCentral
- Brasilien, Süden
- CentralIndia
- JapanEast
- Südafrika, Nord
- Frankreich, Mitte
- KoreaCentral
- USA, Mitte
- NorthCentralUS
- Asien, Osten
- WestCentralUS
- AustraliaSoutheast

## <a name="next-steps"></a>Nächste Schritte

- [Registrieren und Überprüfen von Azure Blob Storage](register-scan-azure-blob-storage-source.md)
