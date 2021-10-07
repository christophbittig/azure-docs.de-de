---
title: Features für die Leistungsoptimierung bei Kopieraktivitäten
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie mehr über die wichtigsten Funktionen, mit denen Sie die Leistung bei Kopieraktivitäten in Azure Data Factory- und Azure Synapse Analytics-Pipelines optimieren können.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c9cedace5f4755e22c4f08ecdde0d3f6fb8fa52f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663210"
---
# <a name="copy-activity-performance-optimization-features"></a>Features für die Leistungsoptimierung bei Kopieraktivitäten

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden die Features für die Leistungsoptimierung bei Kopieraktivitäten beschrieben, die Sie in Azure Data Factory- und Synapse-Pipelines nutzen können.

## <a name="data-integration-units"></a>Datenintegrationseinheiten

Eine Datenintegrationseinheit ist eine Messgröße für die Leistungsfähigkeit (Kombination aus zugeteilten CPU-, Speicher- und Netzwerkressourcen) einer einzelnen Einheit innerhalb des Diensts. Die Datenintegrationseinheit gilt nur für die [Azure-Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), aber nicht für [selbstgehostete Integration Runtimes](concepts-integration-runtime.md#self-hosted-integration-runtime).

Die zulässige Anzahl von DIUs für die Ausführung einer Kopieraktivität liegt zwischen **2 und 256**. Wenn diese Option nicht angegeben ist oder Sie auf der Benutzeroberfläche „Auto“ auswählen, wendet der Dienst dynamisch die optimale DIU-Einstellung auf dem Quelle-Senke-Paar und dem Datenmuster basierend an. In der nachstehenden Tabelle sind die unterstützten DIU-Bereiche und das Standardverhalten in verschiedenen Kopierszenarien aufgeführt:

| Kopierszenario | Unterstützter DIU-Bereich | Vom Dienst bestimmte Standard-DIUs |
|:--- |:--- |---- |
| Zwischen Dateispeichern |- **Kopieren aus einer einzelnen oder in eine einzelne Datei**: 2–4 <br>- **Kopieren aus mehreren oder in mehrere Dateien**: 2–256, je nach Anzahl und Größe der Dateien <br><br>Wenn Sie beispielsweise Daten aus einem Ordner mit 4 großen Dateien kopieren und die Hierarchie beibehalten möchten, ist die maximale effektive DIU gleich 16. Wenn Sie sich für das Zusammenführen der Datei entscheiden, ist die maximale effektive DIU gleich 4. |Zwischen 4 und 32, je nach Anzahl und Größe der Dateien |
| Aus Dateispeicher in Nicht-Dateispeicher |- **Kopieren aus einer einzelnen Datei**: 2–4 <br/>- **Kopieren aus mehreren Dateien**: 2–256, je nach Anzahl und Größe der Dateien <br/><br/>Wenn Sie beispielsweise Daten aus einem Ordner mit 4 großen Dateien kopieren, ist die maximale effektive DIU gleich 16. |- **Kopieren in Azure SQL-Datenbank oder Azure Cosmos DB**: zwischen 4 und 16, je nach der Senkenebene (DTUs/RUs) und dem Quelldateimuster<br>- **Kopieren in Azure Synapse Analytics** mithilfe von PolyBase oder der COPY-Anweisung: 2<br>– Anderes Szenario: 4 |
| Aus Nicht-Dateispeicher in Dateispeicher |- **Kopieren aus einem Datenspeicher mit aktivierter Partitionsoption** (einschließlich [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md#azure-database-for-postgresql-as-source), [Azure SQL-Datenbank](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source) und [Teradata](connector-teradata.md#teradata-as-source)): 2–256 beim Schreiben in einen Ordner und 2–4 beim Schreiben in eine einzelne Datei. Beachten Sie, dass pro Quelldatenpartition bis zu 4 DIUs verwendet werden können.<br>- **Andere Szenarien**: 2–4 |- **Kopieren aus REST oder HTTP**: 1<br/>- **Kopieren aus Amazon Redshift** mithilfe von UNLOAD: 2<br>- **Anderes Szenario**: 4 |
| Zwischen Nicht-Dateispeichern |- **Kopieren aus einem Datenspeicher mit aktivierter Partitionsoption** (einschließlich [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md#azure-database-for-postgresql-as-source), [Azure SQL-Datenbank](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source) und [Teradata](connector-teradata.md#teradata-as-source)): 2–256 beim Schreiben in einen Ordner und 2–4 beim Schreiben in eine einzelne Datei. Beachten Sie, dass pro Quelldatenpartition bis zu 4 DIUs verwendet werden können.<br/>- **Andere Szenarien**: 2–4 |- **Kopieren aus REST oder HTTP**: 1<br>- **Anderes Szenario**: 4 |

Sie können die für die einzelnen Kopiervorgänge verwendeten DIUs in der Überwachungsansicht der Kopieraktivität oder der Aktivitätsausgabe anzeigen. Weitere Informationen finden Sie unter [Überwachung der Kopieraktivität](copy-activity-monitoring.md). Wenn Sie diese Standardeinstellung überschreiben möchten, geben Sie einen Wert für die Eigenschaft `dataIntegrationUnits` wie folgt an. Die *tatsächliche Anzahl von DIUs*, die der Kopiervorgang zur Laufzeit verwendet, entspricht maximal dem konfigurierten Wert. Dies ist abhängig von Ihrem Datenmuster.

Ihnen wird die **Anzahl der verwendeten DIUs \* Kopierdauer \* Einheitenpreis/DIU-Stunde** in Rechnung gestellt. Sehen Sie sich [hier](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) die aktuellen Preise an. Je nach Abonnementtyp können lokale Währungen und separate Rabatte gelten.

**Beispiel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Skalierbarkeit der selbstgehosteten Integration Runtime (IR)

Wenn Sie einen höheren Durchsatz erreichen möchten, können Sie die selbstgehostete IR zentral oder horizontal hochskalieren:

- Wenn die CPU und der verfügbare Arbeitsspeicher auf dem Knoten der selbstgehosteten IR nicht vollständig genutzt werden, bei der Ausführung paralleler Aufträge aber das Limit erreicht wird, sollten Sie eine zentrale Hochskalierung durchführen, indem Sie die Anzahl der parallelen Aufträge vergrößern, die auf einem Knoten ausgeführt werden können.  Anweisungen dazu finden Sie [hier](create-self-hosted-integration-runtime.md#scale-up).
- Ist jedoch die CPU-Auslastung auf dem Knoten der selbstgehosteten IR hoch oder der verfügbare Arbeitsspeicher gering, können Sie einen neuen Knoten hinzufügen, um die Last über mehrere Knoten horizontal zu skalieren.  Anweisungen dazu finden Sie [hier](create-self-hosted-integration-runtime.md#high-availability-and-scalability).

Beachten Sie in den folgenden Szenarien, dass bei der Ausführung einer einzelnen Kopieraktivität mehrere Knoten einer selbstgehosteten IR genutzt werden können:

- Kopieren Sie Daten aus dateibasierten Speichern, je nach Anzahl und Größe der Dateien.
- Kopieren von Daten aus einem Datenspeicher mit aktivierter Partitionsoption (einschließlich [Azure SQL-Datenbank](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source) und [Teradata](connector-teradata.md#teradata-as-source)), abhängig von der Anzahl der Datenpartitionen.

## <a name="parallel-copy"></a>Parallele Kopie

Sie können eine parallele Kopie (Eigenschaft `parallelCopies`) für die Kopieraktivität festlegen, um die Parallelität anzugeben, die von der Kopieraktivität verwendet werden soll. Diese Eigenschaft können Sie sich als die maximale Anzahl von Threads in der Kopieraktivität vorstellen, die parallel aus Ihrer Datenquelle lesen oder in Ihre Senkendatenspeicher schreiben.

Die parallele Kopie ist orthogonal zu [Datenintegrationseinheiten](#data-integration-units) oder [Knoten der selbstgehosteten IR](#self-hosted-integration-runtime-scalability). Sie wird für alle DIUs oder Knoten der selbstgehosteten IR gezählt.

Bei jeder Ausführung einer Kopieraktivität wendet der Dienst standardmäßig die optimale Einstellung für parallele Kopien basierend auf Ihrem Quell-Senke-Paar und Datenmuster dynamisch an. 

> [!TIP]
> Das Standardverhalten von parallelen Kopien bietet Ihnen normalerweise den besten Durchsatz, der vom Dienst automatisch festgelegt wird. Grundlage dafür ist das Quell-/Senkenpaar, das Datenmuster sowie die Anzahl der DIUs oder die CPU/der Arbeitsspeicher/die Anzahl der Knoten der selbstgehosteten IR. Weitere Informationen zur Optimierung des parallelen Kopierens finden Sie unter [Problembehandlung bei der Leistung der Kopieraktivität](copy-activity-performance-troubleshooting.md).

In der folgenden Tabelle wird das parallele Kopierverhalten aufgeführt:

| Kopierszenario | Paralleles Kopierverhalten |
| --- | --- |
| Zwischen Dateispeichern | `parallelCopies` bestimmt die Parallelität **auf Dateiebene**. Die Blockerstellung („Chunking“) innerhalb jeder Datei erfolgt darunter automatisch und transparent. Sie wurde so konzipiert, dass die am besten geeignete Blockgröße für einen bestimmten Datenspeichertyp zum parallelen Laden von Daten verwendet wird. <br/><br/>Die tatsächliche Anzahl paralleler Kopien, die von der Kopieraktivität zur Laufzeit verwendet werden, ist nicht mehr als die Anzahl Ihrer verfügbaren Dateien. Bei Verwendung des Kopierverhaltens **mergeFile** in die Dateisenke kann die Kopieraktivität die Parallelität auf Dateiebene nicht nutzen. |
| Aus Dateispeicher in Nicht-Dateispeicher | – Beim Kopieren von Daten in Azure SQL-Datenbank oder Azure Cosmos DB ist die standardmäßige parallele Kopie auch abhängig von der Senkenebene (Anzahl von DTUs/RUs).<br>– Beim Kopieren von Daten in Azure Table ist die standardmäßige parallele Kopie 4. |
| Aus Nicht-Dateispeicher in Dateispeicher | - Beim Kopieren von Daten aus einem Datenspeicher mit aktivierter Partitionsoption (einschließlich [Azure SQL-Datenbank](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source) und [Teradata](connector-teradata.md#teradata-as-source)), standardmäßige parallele Kopie = 4. Die tatsächliche Anzahl paralleler Kopien, die von der Kopieraktivität zur Laufzeit verwendet werden, ist nicht mehr als die Anzahl Ihrer verfügbaren Datenpartitionen. Wenn Sie eine selbstgehostete Integration Runtime verwenden und in Azure-Blob/ADLS Gen2 kopieren, beachten Sie, dass die maximale effektive parallele Kopie 4 oder 5 pro IR-Knoten ist.<br>– Bei anderen Szenarien wird die parallele Kopie nicht wirksam. Selbst wenn Parallelität angegeben wurde, wird sie nicht angewendet. |
| Zwischen Nicht-Dateispeichern | – Beim Kopieren von Daten in Azure SQL-Datenbank oder Azure Cosmos DB ist die standardmäßige parallele Kopie auch abhängig von der Senkenebene (Anzahl von DTUs/RUs).<br/>– Beim Kopieren von Daten aus einem Datenspeicher mit aktivierter Partitionsoption (einschließlich [Azure SQL-Datenbank](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source) und [Teradata](connector-teradata.md#teradata-as-source)), standardmäßige parallele Kopie = 4.<br>– Beim Kopieren von Daten in Azure Table ist die standardmäßige parallele Kopie 4. |

Sie können den Standardwert jedoch überschreiben und einen Wert für die Eigenschaft `parallelCopies` angeben, um die Last für die Computer zu steuern, auf denen Ihre Datenspeicher gehostet werden, oder um die Kopierleistung zu optimieren. Der Wert muss eine ganze Zahl größer als oder gleich 1 sein. Zur Laufzeit verwendet die Kopieraktivität maximal den von Ihnen festgelegten Wert, um eine optimale Leistung zu erzielen.

Berücksichtigen Sie bei der Angabe eines Werts für die Eigenschaft `parallelCopies` die höhere Auslastung Ihrer Quell- und Senkendatenspeicher. Berücksichtigen Sie auch den Lastanstieg zur selbstgehosteten Integration Runtime, wenn die Kopieraktivität davon abhängt. Dieser Lastanstieg tritt insbesondere auf, wenn Sie über mehrere Aktivitäten oder gleichzeitige Ausführungen der gleichen Aktivitäten verfügen, die für den gleichen Datenspeicher ausgeführt werden. Wenn Sie eine Überlastung des Datenspeichers oder der selbstgehosteten Integration Runtime feststellen, verringern Sie den Wert `parallelCopies`, um die Last zu verringern.

**Beispiel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>gestaffeltem Kopieren

Beim Kopieren von Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher können Sie ggf. Azure Blob Storage oder Azure Data Lake Storage Gen2 als Stagingzwischenspeicher verwenden. Staging ist besonders in folgenden Fällen hilfreich:

- **Sie möchten Daten aus verschiedenen Datenspeichern in Azure Synapse Analytics über PolyBase erfassen, Daten aus/in Snowflake kopieren oder Daten aus Amazon Redshift/HDFS leistungsstark erfassen.** Ausführliche Informationen finden Sie unter:
  - [Verwenden von PolyBase zum Laden von Daten in Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
  - [Snowflake-Connector](connector-snowflake.md)
  - [Amazon Redshift-Connector](connector-amazon-redshift.md)
  - [HDFS-Connector](connector-hdfs.md)
- **Aufgrund der IT-Richtlinien des Unternehmens sollten Sie mit Ausnahme der Ports 80 und 443 keine weiteren Ports in der Firewall öffnen.** Ein Beispiel: Beim Kopieren von Daten aus einem lokalen Datenspeicher in eine Azure SQL-Datenbank oder Azure Synapse Analytics müssen Sie die ausgehende TCP-Kommunikation über den Port 1433 sowohl für die Windows-Firewall als auch für Ihre Unternehmensfirewall aktivieren. In diesem Szenario kann das gestaffelte Kopieren die selbstgehostete Integration Runtime nutzen, um Daten zunächst per HTTP oder HTTPS über den Port 443 in einen Stagingspeicher zu kopieren und anschließend aus dem Stagingspeicher in SQL-Datenbank oder Azure Synapse Analytics zu laden. Dadurch muss der Port 1433 nicht geöffnet werden.
- **Hybriddatenverschiebungen (also das Kopieren aus einem lokalen Datenspeicher in einen Clouddatenspeicher) können bei einer langsamen Netzwerkverbindung eine Weile dauern.** Zur Verbesserung der Leistung können Sie gestaffeltes Kopieren verwenden, um die Daten lokal zu komprimieren und dadurch die Verschiebung in den Stagingdatenspeicher in der Cloud zu beschleunigen. Anschließend können Sie die Daten im Stagingspeicher wieder dekomprimieren, bevor Sie sie in den Zieldatenspeicher laden.

### <a name="how-staged-copy-works"></a>Funktionsweise des gestaffelten Kopierens

Bei aktivierter Stagingfunktion werden die Daten zunächst aus dem Quelldatenspeicher in den Stagingspeicher kopiert (von Ihnen bereitgestelltes Azure-Blob oder Azure Data Lake Storage Gen2). Danach werden sie aus dem Staging- in den Senkendatenspeicher kopiert. Die Kopieraktivität verwaltet den zweistufigen Ablauf automatisch und löscht außerdem nach Abschluss der Datenverschiebung temporäre Daten aus dem Stagingspeicher.

:::image type="content" source="media/copy-activity-performance/staged-copy.png" alt-text="Gestaffeltes Kopieren":::

Wenn Sie die Datenverschiebung unter Verwendung eines Stagingspeichers aktivieren, können Sie angeben, ob die Daten vor dem Verschieben aus dem Quelldatenspeicher in den Stagingspeicher komprimiert und dann vor dem Verschieben aus einem Zwischen- oder Stagingdatenspeicher in den Senkendatenspeicher dekomprimiert werden sollen.

Derzeit können Sie keine Daten zwischen zwei Datenspeichern kopieren, die über verschiedene selbstgehostete IRs verbunden sind, weder mit noch ohne gestaffeltes Kopieren. Für ein solches Szenario können Sie zwei explizit verkettete Kopiervorgänge konfigurieren, um von der Quelle zum Stagingspeicher und aus dem Stagingspeicher zur Senke zu kopieren.

### <a name="configuration"></a>Konfiguration

Konfigurieren Sie für die Kopieraktivität die Einstellung **enableStaging**, um anzugeben, ob die Daten vor dem Laden in einen Zielspeicher im Speicher bereitgestellt werden sollen. Wenn Sie **enableStaging** auf `TRUE` festlegen, müssen Sie zusätzliche Eigenschaften angeben. Diese sind in der folgenden Tabelle aufgeführt. 

| Eigenschaft | Beschreibung | Standardwert | Erforderlich |
| --- | --- | --- | --- |
| enableStaging |Geben Sie an, ob Sie Daten über einen Stagingzwischenspeicher kopieren möchten. |False |Nein |
| linkedServiceName |Geben Sie den Namen eines verknüpften Diensts vom Typ [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) an, der auf die Storage-Instanz verweist, die Sie als Stagingzwischenspeicher verwenden. |– |Ja, wenn **enableStaging** auf „TRUE“ festgelegt ist. |
| path |Geben Sie den gewünschten Pfad für die bereitgestellten Daten an. Wenn Sie keinen Pfad angeben, erstellt der Dienst einen Container zum Speichern der temporären Daten. |– |Nein |
| enableCompression |Gibt an, ob die Daten komprimiert werden sollen, bevor sie an das Ziel kopiert werden. Durch diese Einstellung wird die Menge der übertragenen Daten reduziert. |False |Nein |

>[!NOTE]
> Wenn Sie das gestaffelte Kopieren mit aktivierter Komprimierung verwenden, wird die Dienstprinzipal- oder MSI-Authentifizierung für das Staging eines verknüpften Blobdiensts nicht unterstützt.

Hier sehen Sie eine Beispieldefinition der Kopieraktivität mit den Eigenschaften aus der obigen Tabelle:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Auswirkungen auf die Abrechnung von gestaffeltem Kopieren

Die Abrechnung basiert auf zwei Aspekten: Dauer des Kopiervorgangs und Art der Kopie.

* Wenn Sie einen Stagingspeicher während eines Cloudkopiervorgangs (d. h. Kopieren von Daten aus einem Clouddatenspeicher in einen anderen Clouddatenspeicher. Beide Phasen werden von Azure-Integration Runtime unterstützt) verwenden, gilt folgende Formel: [Gesamtkopierdauer für Schritt 1 und 2] [Einzelpreis für Cloudkopien].
* Wenn Sie Staging während eines Hybridkopiervorgangs (d. h. Kopieren von Daten aus einem lokalen Datenspeicher in einen Clouddatenspeicher. Eine Phase wird von der selbstgehosteten Integration Runtime-Infrastruktur unterstützt) verwenden, gilt folgende Formel: [Dauer des Hybridkopiervorgangs] [Einzelpreis für Hybridkopien] + [Dauer des Cloudkopiervorgangs] [Einzelpreis für Cloudkopien].

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Handbuch zur Leistung und Skalierbarkeit der Kopieraktivität](copy-activity-performance.md)
- [Troubleshoot copy activity performance](copy-activity-performance-troubleshooting.md) (Problembehandlung bei der Leistung der Kopieraktivität)
- [Verwenden von Azure Data Factory zum Migrieren von Daten aus einem Data Lake oder Data Warehouse zu Azure](data-migration-guidance-overview.md)
- [Migrieren von Daten aus Amazon S3 zu Azure Storage](data-migration-guidance-s3-azure-storage.md)