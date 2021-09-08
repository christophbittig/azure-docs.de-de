---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/10/2021
ms.openlocfilehash: 20a9b85c2d68e1c9b3c33362f393eaec0e255ce7
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350843"
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
| Azure File Storage | Ja | 
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

> [!Note]
> Das Herkunftsfeature verursacht einen gewissen Leistungsoverhead in der Copy-Aktivität. Wenn Sie Verbindungen in Purview einrichten, werden Sie möglicherweise feststellen, dass bestimmte Kopieraufträge länger dauern. Die Auswirkungen sind in den meisten Fällen zu vernachlässigen. Wenn Ihre Kopieraufträge deutlich länger dauern als üblich, wenden Sie sich mit einem Zeitvergleich an den Support.

#### <a name="known-limitations-on-copy-activity-lineage"></a>Bekannte Einschränkungen der Herkunftserfassung für die Kopieraktivität

Wenn Sie die folgenden Funktionen der Kopieraktivität verwenden, wird die Erfassung der Herkunft derzeit noch nicht unterstützt:

- Kopieren von Daten in Azure Data Lake Storage Gen1 mithilfe des Binärformats
- Kopieren von Daten in Azure Synapse Analytics mithilfe von PolyBase oder der COPY-Anweisung
- Komprimierungseinstellung für Binärdateien, durch Trennzeichen getrennte Textdateien, Excel-, JSON- und XML-Dateien
- Quellpartitionsoptionen für Azure SQL-Datenbank, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server und SAP-Tabelle
- Option zur Quellpartitionsermittlung für dateibasierte Speicher
- Kopieren von Daten in eine dateibasierte Senke mit Einstellung für maximale Zeilenanzahl pro Datei
- Hinzufügen zusätzlicher Spalten während des Kopiervorgangs

Zusätzlich zur Datenherkunft wird das Datenobjektschema (siehe Registerkarte Objekte -> Schema) für die folgenden Konnektoren gemeldet:

- CSV-und Parquet-Dateien in Azure BLOB, Azure File Storage, ADLS Gen1, ADLS Gen2 und Amazon S3
- Azure Daten-Explorer, Azure SQL-Datenbank, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata
