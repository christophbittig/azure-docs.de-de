---
title: Purview-Connector – Übersicht
description: In diesem Artikel sind die verschiedenen in Purview unterstützten Datenspeicher und Funktionen skizziert.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59ba1b5bba00eb84d9a4e5dd20cb9f81771097f1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026385"
---
# <a name="supported-data-stores"></a>Unterstützte Datenspeicher

Purview unterstützt die folgenden Datenspeicher. Wählen Sie die einzelnen Datenspeicher aus, um mehr über die unterstützten Funktionen und die entsprechende Konfiguration zu erfahren.

## <a name="purview-data-sources"></a>Purview-Datenquellen

|**Kategorie**|  **Datenspeicher**  |**Technische Metadaten** |**Klassifizierung** |**Herkunft** | **Zugriffsrichtlinie** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [Ja](register-scan-azure-blob-storage-source.md#register) | [Ja](register-scan-azure-blob-storage-source.md#scan)| Eingeschränkt* | [Ja](how-to-access-policies-storage.md) |
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Ja](register-scan-azure-cosmos-database.md#register) | [Ja](register-scan-azure-cosmos-database.md#scan)|Nein*|Nein|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [Ja](register-scan-azure-data-explorer.md#register) | [Ja](register-scan-azure-data-explorer.md#scan)| Nein* | Nein |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Ja](register-scan-adls-gen1.md#register) | [Ja](register-scan-adls-gen1.md#scan)| Eingeschränkt* | Nein |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Ja](register-scan-adls-gen2.md#register) | [Ja](register-scan-adls-gen2.md#scan)| Eingeschränkt* | [Ja](how-to-access-policies-storage.md) |
|| [Azure Database for MySQL](register-scan-azure-mysql-database.md) | [Ja](register-scan-azure-mysql-database.md#register) | [Ja](register-scan-azure-mysql-database.md#scan) | Nein* | Nein |
|| [Azure-Datenbank für PostgreSQL](register-scan-azure-postgresql.md) | [Ja](register-scan-azure-postgresql.md#register) | [Ja](register-scan-azure-postgresql.md#scan) | Nein* | Nein |
||    [Dedizierter Azure-SQL-Pool (vormals SQL DW)](register-scan-azure-synapse-analytics.md)| [Ja](register-scan-azure-synapse-analytics.md#register) | [Ja](register-scan-azure-synapse-analytics.md#scan)| Nein* | Nein |
||    [Azure Files](register-scan-azure-files-storage-source.md)|[Ja](register-scan-azure-files-storage-source.md#register) | [Ja](register-scan-azure-files-storage-source.md#scan) | Eingeschränkt* |  Nein |
||    [Azure SQL-Datenbank](register-scan-azure-sql-database.md)| [Ja](register-scan-azure-sql-database.md#register) |[Ja](register-scan-azure-sql-database.md#scan)| Nein* | Nein |
||    [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md)|  [Ja](register-scan-azure-sql-database-managed-instance.md#scan) | [Ja](register-scan-azure-sql-database-managed-instance.md#scan) | Nein* | Nein |
||    [Azure Synapse Analytics (Arbeitsbereich)](register-scan-synapse-workspace.md)| [Ja](register-scan-synapse-workspace.md#register) | [Ja](register-scan-synapse-workspace.md#scan)| [Ja – Synapse-Pipelines](how-to-lineage-azure-synapse-analytics.md)| Nein|
|Datenbank| [Amazon RDS](register-scan-amazon-rds.md) | [Ja](register-scan-amazon-rds.md#register-an-amazon-rds-data-source) | [Ja](register-scan-amazon-rds.md#scan-an-amazon-rds-database) | Nein | Nein |
||    [Cassandra](register-scan-cassandra-source.md)|[Ja](register-scan-cassandra-source.md#register) | Nein | [Ja](how-to-lineage-cassandra.md)| Nein|
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [Ja](register-scan-google-bigquery-source.md#register)| Nein | [Ja](how-to-lineage-google-bigquery.md)| Nein|
|| [Hive-Metastore-Datenbank](register-scan-hive-metastore-source.md) | [Ja](register-scan-hive-metastore-source.md#register) | Nein | Ja* | Nein|
|| [MySQL](register-scan-mysql.md) | [Ja](register-scan-mysql.md#register) | Nein | [Ja](register-scan-mysql.md#scan) | Nein |
|| [Oracle](register-scan-oracle-source.md) | [Ja](register-scan-oracle-source.md#register)|  Nein | [Ja*](how-to-lineage-oracle.md) | Nein|
|| [PostgreSQL](register-scan-postgresql.md) | [Ja](register-scan-postgresql.md#register) | Nein | [Ja](register-scan-postgresql.md#scan) | Nein |
||    [SQL Server](register-scan-on-premises-sql-server.md)| [Ja](register-scan-on-premises-sql-server.md#register) |[Ja](register-scan-on-premises-sql-server.md#scan) | Nein* | Nein|
||    [Teradata](register-scan-teradata-source.md)| [Ja](register-scan-teradata-source.md#register)|  Nein | [Ja*](how-to-lineage-teradata.md) | Nein|
|Datei|[Amazon S3](register-scan-amazon-s3.md)|[Ja](register-scan-amazon-s3.md)| [Ja](register-scan-amazon-s3.md)| Eingeschränkt* | Nein|
|Dienste und Apps|    [Erwin](register-scan-erwin-source.md)| [Ja](register-scan-erwin-source.md#register)| Nein | [Ja](how-to-lineage-erwin.md)| Nein|
||    [Looker](register-scan-looker-source.md)| [Ja](register-scan-looker-source.md#register)| Nein | [Ja](how-to-lineage-looker.md)| Nein|
||    [Power BI](register-scan-power-bi-tenant.md)| [Ja](register-scan-power-bi-tenant.md#register)| Nein | [Ja](how-to-lineage-powerbi.md)| Nein|
|| [Salesforce](register-scan-salesforce.md) | [Ja](register-scan-salesforce.md#register) | Nein | Nein | Nein |
||    [SAP ECC](register-scan-sapecc-source.md)| [Ja](register-scan-sapecc-source.md#register) | Nein | [Ja*](how-to-lineage-sapecc.md) | Nein|
|| [SAP S/4HANA](register-scan-saps4hana-source.md) | [Ja](register-scan-saps4hana-source.md#register)| Nein | [Ja*](how-to-lineage-sapecc.md) | Nein|

\* Neben der Herkunft von Ressourcen innerhalb der Datenquelle wird die Herkunft auch unterstützt, wenn das Dataset in [Data Factory](how-to-link-azure-data-factory.md) oder in der [Synapse-Pipeline](how-to-lineage-azure-synapse-analytics.md) als Quelle/Senke verwendet wird.

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