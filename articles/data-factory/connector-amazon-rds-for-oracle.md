---
title: Kopieren von Daten aus Amazon RDS für Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Lernen Sie, wie Sie Daten aus Amazon RDS für Oracle mit Hilfe von Data Factory oder Azure Synapse Analytics Pipelines in unterstützte Sink Stores kopieren können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: jianleishen
ms.openlocfilehash: c6f14b2bb5129907aa5efac042f23217791d84d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598456"
---
# <a name="copy-data-from-amazon-rds-for-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Kopieren von Daten aus Amazon RDS für Oracle mithilfe von Azure Data Factory oder Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt, wie die Kopieraktivität in Azure Data Factory verwendet wird, um Daten aus einer Amazon RDS für Oracle-Datenbank zu kopieren. Der Artikel baut auf der [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon RDS für Oracle Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer Amazon RDS für Oracle-Datenbank in jeden unterstützten Senken-Datenspeicher kopieren. Eine Liste der Datenspeicher, die von der Kopieraktivität als Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Insbesondere unterstützt dieser Amazon RDS für Oracle Connector:

- Die folgenden Versionen einer Amazon RDS für Oracle-Datenbank:
    - Amazon RDS für Oracle 19c R1 (19.1) und höher
    - Amazon RDS für Oracle 18c R1 (18.1) und höher
    - Amazon RDS für Oracle 12c R1 (12.1) und höher
    - Amazon RDS für Oracle 11g R1 (11.1) und höher
- Paralleles Kopieren aus einer Amazon RDS für Oracle-Quelle. Einzelheiten finden Sie im Abschnitt [ Parallele Kopie von Amazon RDS für Oracle](#parallel-copy-from-amazon-rds-for-oracle).

> [!Note]
> Amazon RDS für Oracle Proxy-Server wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

Die Integrationslaufzeit bietet einen integrierten Amazon RDS für Oracle-Treiber. Daher müssen Sie keinen Treiber manuell installieren, wenn Sie Daten von Amazon RDS für Oracle kopieren.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-rds-for-oracle-using-ui"></a>Erstellen Sie einen verknüpften Service mit Amazon RDS für Oracle über die Benutzeroberfläche

Gehen Sie wie folgt vor, um einen mit Amazon RDS für Oracle verknüpften Service in der Benutzeroberfläche des Azure-Portals zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“. Wählen Sie die Option „Verknüpfte Dienste“ aus, und klicken Sie anschließend auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Synapse-Benutzeroberfläche":::

1. Suchen Sie nach Amazon RDS für Oracle und wählen Sie den Amazon RDS für Oracle Connector aus.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/amazon-rds-for-oracle-connector.png" alt-text="Screenshot des Amazon RDS für Oracle Connectors.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung, und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/configure-amazon-rds-for-oracle-linked-service.png" alt-text="Screenshot von der Konfiguration des verknüpften Diensts für Amazon RDS für Oracle.":::

## <a name="connector-configuration-details"></a>Details zur Connectorkonfiguration

Die folgenden Abschnitte geben Details an zu Eigenschaften, die zum Definieren von Entitäten speziell für den Amazon RDS für Oracle Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der verknüpfte Dienst Amazon RDS für Oracle unterstützt folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „Typ“ muss auf **AmazonRdsForOracle** gesetzt werden. | Ja |
| connectionString | Gibt die Informationen an, die zur Verbindung mit der Amazon RDS für Oracle Database-Instanz erforderlich sind. <br/>Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). <br><br>**Unterstützter Verbindungstyp**: Sie können **Amazon RDS für Oracle SID** oder  **Amazon RDS für Oracle Service Name** verwenden, um Ihre Datenbank zu identifizieren:<br>– Wenn Sie die SID verwenden: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Wenn Sie den Dienstnamen verwenden: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Für fortgeschrittene Amazon RDS für Oracle native Verbindungsoptionen können Sie einen Eintrag in der Datei [JTNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) auf dem Amazon RDS für Oracle-Server hinzufügen und im verknüpften Dienst Amazon RDS für Oracle entscheiden, den Verbindungstyp „Amazon RDS für Oracle Dienstname“ zu verwenden und den korrespondierenden Dienstnamen zu konfigurieren. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Wenn Sie mehrere Amazon RDS für Oracle-Instanzen für ein Failover-Szenario haben, können Sie einen mit Amazon RDS für Oracle verknüpften Dienst erstellen und den Benutzernamen des primären Host-Ports, das Kennwort usw. eingeben und neue „**Zusätzliche Verbindungseigenschaften**“ mit dem Eigenschaftsnamen `AlternateServers` und dem Wert `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` hinzufügen – vergessen Sie die Klammern nicht und achten Sie auf die Doppelpunkte (`:`) als Trennzeichen. Beispielsweise definiert der folgende Wert von alternativen Servern zwei alternative Datenbankserver für das Verbindungsfailover: `(HostName=AccountingAmazonRdsForOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`.

Weitere Verbindungseigenschaften, die Sie abhängig von Ihrem Anwendungsfall in der Verbindungszeichenfolge festlegen können:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte |
|:--- |:--- |:--- |
| ArraySize |Die Anzahl von Bytes, die der Connector in einem einzelnen Netzwerkroundtrip abrufen kann. Beispiel: `ArraySize=‭10485760‬`.<br/><br/>Größere Werte erhöhen den Durchsatz, indem seltener Daten über das Netzwerk abgerufen werden. Kleinere Werte erhöhen die Reaktionszeit, da weniger lange auf die Übertragung von Daten durch den Server gewartet werden muss. | Eine ganze Zahl zwischen 1 und 4.294.967.296 (4 GB). Der Standardwert ist `60000`. Der Wert 1 definiert nicht die Anzahl von Bytes, sondern gibt die Zuordnung von Speicherplatz für genau eine Datenzeile an. |

Um die Verschlüsselung bei der Verbindung mit Amazon RDS für Oracle zu aktivieren, haben Sie zwei Möglichkeiten:

-   Um **Triple-DES-Verschlüsselung (3DES) und Advanced Encryption Standard (AES)** zu verwenden, gehen Sie auf der Amazon RDS für Oracle-Serverseite zu Oracle Advanced Security (OAS) und konfigurieren die Verschlüsselungseinstellungen. Ausführliche Informationen finden Sie in der [Oracle-Dokumentation](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Der Amazon RDS für Oracle Application Development Framework(ADF)-Connector handelt automatisch die Verschlüsselungsmethode aus und verwendet die von Ihnen in OAS konfigurierte Methode, wenn eine Verbindung zu Amazon RDS für Oracle hergestellt wird.

-   So verwenden Sie **TLS**:

    1.  Rufen Sie die TLS/SSL-Zertifikatinformationen ab. Rufen Sie die mit Distinguished Encoding Rules (DER) codierten Zertifikatinformationen Ihres TLS/SSL-Zertifikats ab, und speichern Sie die Ausgabe (----- Begin Certificate … End Certificate -----) als Textdatei.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Beispiel:** Extrahieren Sie Zertifikatinformationen aus „DERcert.cer“, und speichern Sie die Ausgabe in „cert.txt“.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Erstellen Sie `keystore` oder `truststore`. Mit dem folgenden Befehl wird die Datei `truststore` (mit oder ohne Kennwort) im PKCS-12-Format erstellt.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Beispiel:** Erstellen Sie eine PKCS12-Datei vom Typ `truststore` namens „MyTrustStoreFile“ mit einem Kennwort.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Platzieren Sie die Datei `truststore` auf dem Computer mit der selbstgehosteten Integration Runtime. Platzieren Sie die Datei beispielsweise unter „C:\MyTrustStoreFile“.
    4.  Konfigurieren Sie in dem Dienst die Amazon RDS für Oracle-Verbindungszeichenfolge mit `EncryptionMethod=1` und dem entsprechenden Wert für `TrustStore`/`TrustStorePassword`. Beispiel: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Beispiel:**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kennworts in Azure Key Vault**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Dataset-Eigenschaften

Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Amazon RDS für Oracle-Dataset unterstützt werden. Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

Um Daten von Amazon RDS für Oracle zu kopieren, setzen Sie die Eigenschaft „Typ“ des Datasets auf `AmazonRdsForOracleTable`. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ des Datasets muss auf `AmazonRdsForOracleTable` festgelegt werden. | Ja |
| schema | Name des Schemas. | Nein |
| table | Name der Tabelle/Ansicht. | Nein |
| tableName | Name der Tabelle/Ansicht mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie für eine neue Workload `schema` und `table`. | Nein |

**Beispiel:**

```json
{
    "name": "AmazonRdsForOracleDataset",
    "properties":
    {
        "type": "AmazonRdsForOracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Amazon RDS für Oracle-Quelle unterstützt werden. Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="amazon-rds-for-oracle-as-source"></a>Amazon RDS für Oracle als Quelle

>[!TIP]
>Mehr über das effiziente Laden von Daten aus Amazon RDS für Oracle unter Verwendung von Datenpartitionierung erfahren Sie unter [Paralleles Kopieren aus Amazon RDS für Oracle](#parallel-copy-from-amazon-rds-for-oracle).

Um Daten von Amazon RDS für Oracle zu kopieren, setzen Sie den Quellentyp in der Kopieraktivität auf `AmazonRdsForOracleSource`. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ der Quelle der Kopieraktivität muss auf `AmazonRdsForOracleSource` festgelegt werden. | Ja |
| oracleReaderQuery | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. z. B. `"SELECT * FROM MyTable"`.<br>Wenn Sie partitioniertes Laden aktivieren, müssen Sie die entsprechenden integrierten Partitionsparameter in Ihre Abfrage integrieren. Beispiele finden Sie im Abschnitt [Paralleles Kopieren aus Amazon RDS für Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Nein |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, die zum Laden von Daten aus Amazon RDS für Oracle verwendet werden. <br>Zulässige Werte sind: **None** (Standardwert), **PhysicalPartitionsOfTable** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (d. h. nicht `None`), wird der Grad der Parallelität zum gleichzeitigen Laden von Daten aus einer Amazon RDS für Oracle-Datenbank durch die [`parallelCopies`](copy-activity-performance-features.md#parallel-copy)-Einstellung der Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| partitionNames | Die Liste der physischen Partitionen, die kopiert werden müssen. <br>Verwenden Sie diese Option, wenn die Partitionsoption `PhysicalPartitionsOfTable` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfTabularPartitionName` in die WHERE-Klausel. Ein Beispiel finden Sie im [Abschnitt Paralleles Kopieren aus Amazon RDS für Oracle.](#parallel-copy-from-amazon-rds-for-oracle) | Nein |
| partitionColumnName | Geben Sie den Namen der Quellspalte als **Integer** an, der von der Bereichspartitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionColumnName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Amazon RDS für Oracle.](#parallel-copy-from-amazon-rds-for-oracle) | Nein |
| partitionUpperBound | Der Höchstwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionUpbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Amazon RDS für Oracle.](#parallel-copy-from-amazon-rds-for-oracle) | Nein |
| partitionLowerBound | Der Mindestwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionLowbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Amazon RDS für Oracle.](#parallel-copy-from-amazon-rds-for-oracle) | Nein |

**Beispiel: Kopieren von Daten mit einer einfachen Abfrage ohne Partition**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRdsForOracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-amazon-rds-for-oracle"></a>Paralleles Kopieren aus Amazon RDS für Oracle

Der Amazon RDS für Oracle Connector stellt eine integrierte Datenpartitionierung bereit, um Daten von Amazon RDS für Oracle parallel zu kopieren. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

:::image type="content" source="./media/connector-amazon-rds-for-oracle/connector-amazon-rds-for-oracle-partition-options.png" alt-text="Screenshot von den Partitionierungsoptionen.":::

Wenn Sie partitioniertes Kopieren aktivieren, führt der Dienst parallele Abfragen gegen Ihre Amazon RDS für Oracle-Quelle aus, um Daten nach Partitionen zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie z. B. `parallelCopies` auf vier einstellen, generiert der Dienst gleichzeitig vier Abfragen, die auf der von Ihnen angegebenen Partitionsoption und den Einstellungen basieren, und führt sie aus, wobei jede Abfrage einen Teil der Daten aus Ihrer Amazon RDS für Oracle-Datenbank abruft.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung vor allem dann zu aktivieren, wenn Sie große Datenmengen aus Ihrer Amazon RDS für Oracle-Datenbank laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben. (Geben Sie nur den Ordnernamen an.) In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen          | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Während der Ausführung erkennt der Dienst automatisch die physischen Partitionen und kopiert Daten nach Partitionen. |
| Vollständiges Laden aus einer großen Tabelle ohne physische Partitionen, aber mit einer Integerspalte für die Datenpartitionierung | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Ohne Angabe wird die Primärschlüsselspalte verwendet. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage mit physischen Partitionen | **Partitionsoption:** Physische Partitionen der Tabelle.<br>**Abfrage**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`<br>**Partitionsname:** Geben Sie den Namen der Partitionen an, aus denen Daten kopiert werden sollen. Wenn nicht angegeben: Der Dienst erkennt automatisch die physischen Partitionen in der Tabelle, die Sie im Amazon RDS für Oracle-Datensatz angegeben haben.<br><br>Während der Ausführung ersetzt der Dienst `?AdfTabularPartitionName` durch den tatsächlichen Partitionsnamen und sendet diesen zu Amazon RDS für Oracle. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage ohne physische Partitionen, aber mit einer Integerspalte für die Datenpartitionierung | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Die Partitionierung kann auf der Grundlage der Spalte mit dem Datentyp „Integer“ erfolgen.<br>**Obergrenze der Partition** und **Untergrenze der Partition**: Geben Sie an, ob Sie anhand der Partitionsspalte filtern möchten, um nur Daten zwischen der Ober- und der Untergrenze zu erhalten.<br><br>Während der Ausführung ersetzt der Dienst `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` und `?AdfRangePartitionLowbound` durch die tatsächlichen Spaltennamen und Wertebereiche für jede Partition und sendet sie zu Amazon RDS für Oracle. <br>Wenn z. B. für Ihre Partitionsspalte „ID“ die untere Grenze auf 1 und die obere Grenze auf 80 festgelegt ist und die Parallelkopie auf 4 eingestellt ist, ruft der Dienst Daten nach 4 Partitionen ab. Die ID-Bereiche sehen dann wie folgt aus: [1–20], [21–40], [41–60] und [61–80]. |

> [!TIP]
> Beim Kopieren von Daten aus einer nicht partitionierten Tabelle können Sie die Partitionsoption „Dynamischer Bereich“ verwenden, um eine Partitionierung auf Grundlage einer ganzzahlige Spalte durchzuführen. Wenn die Quelldaten keinen solchen Spaltentyp enthalten, können Sie mithilfe der [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm)-Funktion in der Quellabfrage eine Spalte generieren und diese als Partitionsspalte verwenden.

**Beispiel: Abfrage mit physischer Partition**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
