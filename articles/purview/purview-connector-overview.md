---
title: Purview-Connector – Übersicht
description: In diesem Artikel sind die verschiedenen in Purview unterstützten Datenspeicher und Funktionen skizziert.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 96a51e4ca3ab1f145b5d2820b43290f9a5f98d48
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214820"
---
# <a name="supported-data-stores"></a>Unterstützte Datenspeicher

Purview unterstützt die folgenden Datenspeicher. Wählen Sie die einzelnen Datenspeicher aus, um mehr über die unterstützten Funktionen und die entsprechende Konfiguration zu erfahren.

## <a name="purview-data-sources"></a>Purview-Datenquellen

|**Kategorie**|  **Datenspeicher**  |**Metadatenextrahierung**|**Vollständige Überprüfung**|**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Herkunft**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Dedizierter Azure-SQL-Pool (vormals SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Azure SQL-Datenbank](register-scan-azure-sql-database.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Azure Synapse Analytics (Arbeitsbereich)](register-scan-synapse-workspace.md)|Ja| Ja| Nein| Ja| Ja| Ja|
|Datenbank|[Cassandra](register-scan-cassandra-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[Google BigQuery](register-scan-google-bigquery-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[Hive Metastore DB](register-scan-oracle-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[Oracle Database](register-scan-oracle-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Teradata](register-scan-teradata-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
|Datei|[Amazon S3](register-scan-amazon-s3.md)|Ja| Ja| Ja| Ja| Ja| Ja|
|Dienste und Apps|[Erwin](register-scan-erwin-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[Looker](register-scan-looker-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[SAP ECC](register-scan-sapecc-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|

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
