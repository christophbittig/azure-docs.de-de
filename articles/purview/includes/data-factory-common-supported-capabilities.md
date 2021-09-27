---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/15/2021
ms.openlocfilehash: cb10d36bd426655db6a059dad3bda70cf21cf123
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908804"
---
### <a name="copy-activity-support"></a>Unterstützung der Copy-Aktivität

| Datenspeicher | Unterstützt | 
| ------------------- | ------------------- | 
| Azure Blob Storage | Ja |
| Azure Cognitive Search | Ja | 
| Azure Cosmos DB (SQL-API) \* | Ja | 
| Azure Cosmos DB-API für MongoDB \* | Ja |
| Azure Data Explorer \* | Ja | 
| Azure Data Lake Storage Gen1 | Ja | 
| Azure Data Lake Storage Gen2 | Ja | 
| Azure Database for MariaDB \* | Ja | 
| Azure Database for MySQL \* | Ja | 
| Azure Database for PostgreSQL \* | Ja |
| Azure Files | Ja | 
| Azure SQL-Datenbank \* | Ja | 
| Azure SQL Managed Instance \* | Ja | 
| Azure Synapse Analytics \* | Ja | 
| Azure Table Storage | Ja |
| Amazon S3 | Ja | 
| Hive \* | Ja | 
| SAP-Tabelle *(beim Herstellen einer Verbindung mit SAP ECC oder SAP S/4HANA)* | Ja |
| SQL Server \* | Ja | 
| Teradata \* | Ja |

*\* Azure Purview unterstützt derzeit keine Abfrage oder gespeicherte Prozedur für die Erfassung der Herkunft oder Überprüfung. Die Herkunft ist auf Tabellen- und Ansichtsquellen beschränkt.*

#### <a name="known-limitations-on-copy-activity-lineage"></a>Bekannte Einschränkungen der Herkunftserfassung für die Kopieraktivität

Wenn Sie die folgenden Funktionen der Kopieraktivität verwenden, wird die Erfassung der Herkunft derzeit noch nicht unterstützt:

- Kopieren von Daten in Azure Data Lake Storage Gen1 mithilfe des Binärformats
- Kopieren von Daten in Azure Synapse Analytics mithilfe von PolyBase oder der COPY-Anweisung
- Komprimierungseinstellung für Binärdateien, durch Trennzeichen getrennte Textdateien, Excel-, JSON- und XML-Dateien
- Quellpartitionsoptionen für Azure SQL-Datenbank, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server und SAP-Tabelle
- Kopieren von Daten in eine dateibasierte Senke mit Einstellung für maximale Zeilenanzahl pro Datei

Zusätzlich zur Datenherkunft wird das Datenobjektschema (siehe Registerkarte Objekte -> Schema) für die folgenden Konnektoren gemeldet:

- CSV-und Parquet-Dateien in Azure Blob, Azure Files, ADLS Gen1, ADLS Gen2 und Amazon S3
- Azure Daten-Explorer, Azure SQL-Datenbank, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-flow-support"></a>Datenflussunterstützung

| Datenspeicher | Unterstützt |
| ------------------- | ------------------- | 
| Azure Blob Storage | Ja |
| Azure Cosmos DB (SQL-API) \* | Ja | 
| Azure Data Lake Storage Gen1 | Ja |
| Azure Data Lake Storage Gen2 | Ja |
| Azure Database for MySQL \* | Ja | 
| Azure Database for PostgreSQL \* | Ja |
| Azure SQL-Datenbank \* | Ja |
| Azure SQL Managed Instance \* | Ja | 
| Azure Synapse Analytics \* | Ja |

*\* Azure Purview unterstützt derzeit keine Abfrage oder gespeicherte Prozedur für die Erfassung der Herkunft oder Überprüfung. Die Herkunft ist auf Tabellen- und Ansichtsquellen beschränkt.*