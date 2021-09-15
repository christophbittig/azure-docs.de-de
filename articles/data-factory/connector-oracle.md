---
title: Kopieren von Daten in und aus Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Lernen Sie, wie Sie Daten aus unterstützten Quellspeichern in eine Oracle-Datenbank oder von Oracle in unterstützte Senkenspeicher kopieren können, indem Sie Data Factory- oder Azure Synapse Analytics-Pipelines verwenden.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: bf21a264e64fd43ba98f73f96afc6fe2f1bfd069
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123317448"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Kopieren von Daten von und nach Oracle mithilfe von Azure Data Factory oder Azure Synapse Analytics

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuelle Version](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer Oracle-Datenbank und in eine Oracle-Datenbank zu kopieren. Der Artikel baut auf der [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Oracle-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer Oracle-Datenbank in jeden unterstützten Senkendatenspeicher kopieren. Zudem können Sie Daten aus jedem unterstützten Quelldatenspeicher in eine Oracle-Datenbank kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Oracle-Connector unterstützt insbesondere Folgendes:

- Die folgenden Versionen einer Oracle-Datenbank:
    - Oracle 19c R1 (19.1) und höher
    - Oracle 18c R1 (18.1) und höher
    - Oracle 12c R1 (12.1) und höher
    - Oracle 11g R1 (11.1) und höher
    - Oracle 10g R1 (10.1) und höher
    - Oracle 9i R2 (9.2) und höher
    - Oracle 8i R3 (8.1.7) und höher
    - Oracle Database Cloud Exadata Service
- Paralleles Kopieren aus einer Oracle-Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle).

> [!Note]
> Der Oracle-Proxyserver wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

Die Integration Runtime stellt einen integrierten Oracle-Treiber bereit. Daher müssen Sie zum Kopieren von Daten aus und nach Oracle nicht manuell einen Treiber erstellen.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-oracle-using-ui"></a>Erstellen eines verknüpften Diensts zu Oracle mittels Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst zu Oracle im Azure-Portal zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Data Factory Benutzeroberfläche zeigt.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach Oracle, und wählen Sie den Oracle Connector aus.

    :::image type="content" source="media/connector-oracle/oracle-connector.png" alt-text="Ein Screenshot vom Oracle Connector":::.    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-oracle/configure-oracle-linked-service.png" alt-text="Ein Screenshot von der Konfiguration des verknüpften Diensts für Oracle.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte geben Details an von Eigenschaften, die zum Definieren von Entitäten speziell für den Salesforce-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der verknüpfte Oracle-Dienst unterstützt folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **Oracle** festgelegt werden. | Ja |
| connectionString | Gibt die Informationen an, die zum Herstellen einer Verbindung mit der Oracle-Datenbankinstanz erforderlich sind. <br/>Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). <br><br>**Unterstützter Verbindungstyp:** Sie können die **Oracle-SID** oder den **Oracle-Dienstnamen** zur Identifizierung Ihrer Datenbank verwenden:<br>– Wenn Sie die SID verwenden: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Wenn Sie den Dienstnamen verwenden: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Für erweiterte native Oracle-Verbindungsoptionen können Sie wählen, ob Sie einen Eintrag in der Datei [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) auf dem Oracle-Server hinzufügen möchten, und in Oracle Linked Service den Verbindungstyp Oracle Service Name verwenden und den korrespondierenden Dienstnamen konfigurieren möchten. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

>[!TIP]
>Falls der Fehler „ORA-01025: UPI parameter out of range“ (ORA-01025: URI-Parameter außerhalb des zulässigen Bereichs) angezeigt wird und Sie die Oracle-Version 8i verwenden, fügen Sie Ihrer Verbindungszeichenfolge Folgendes hinzu: `WireProtocolMode=1`. Versuchen Sie es anschließend noch mal.

Wenn Sie mehrere Oracle-Instanzen für ein Failoverszenario haben, können Sie den mit Oracle verknüpften Dienst erstellen und den primären Host, den Port, den Benutzernamen, das Kennwort usw. ausfüllen sowie ein neues „**Zusätzliche Verbindungseigenschaften**“ mit dem Eigenschaftsnamen `AlternateServers` und dem Wert `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` hinzufügen. Vergessen dabei nicht die eckigen Klammern, und achten Sie auf die Doppelpunkte (`:`) als Trennzeichen. Beispielsweise definiert der folgende Wert von alternativen Servern zwei alternative Datenbankserver für das Verbindungsfailover: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`.

Weitere Verbindungseigenschaften, die Sie abhängig von Ihrem Anwendungsfall in der Verbindungszeichenfolge festlegen können:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte |
|:--- |:--- |:--- |
| ArraySize |Die Anzahl von Bytes, die der Connector in einem einzelnen Netzwerkroundtrip abrufen kann. Beispiel: `ArraySize=‭10485760‬`.<br/><br/>Größere Werte erhöhen den Durchsatz, indem seltener Daten über das Netzwerk abgerufen werden. Kleinere Werte erhöhen die Reaktionszeit, da weniger lange auf die Übertragung von Daten durch den Server gewartet werden muss. | Eine ganze Zahl zwischen 1 und 4.294.967.296 (4 GB). Der Standardwert ist `60000`. Der Wert 1 definiert nicht die Anzahl von Bytes, sondern gibt die Zuordnung von Speicherplatz für genau eine Datenzeile an. |

Zum Aktivieren der Verschlüsselung bei einer Oracle-Verbindung haben Sie zwei Möglichkeiten:

-   Um die **Triple-DES-Verschlüsselung (3DES) und Advanced Encryption Standard (AES)** zu verwenden, wechseln Sie aufseiten des Oracle-Servers zu Oracle Advanced Security (OAS), und konfigurieren Sie die Verschlüsselungseinstellungen. Ausführliche Informationen finden Sie in der [Oracle-Dokumentation](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Der Oracle-ADF-Connector (Application Development Framework) handelt automatisch die zu verwendende Verschlüsselungsmethode als diejenige aus, die Sie in OAS bei der Verbindungsherstellung mit Oracle konfigurieren.

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
    4.  Konfigurieren Sie in dem Dienst die Oracle-Verbindungszeichenfolge mit `EncryptionMethod=1` und dem entsprechenden Wert für `TrustStore`/`TrustStorePassword`. Beispiel: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Beispiel:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Oracle-Dataset unterstützt werden. Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

Legen Sie zum Kopieren von Daten aus bzw. nach Oracle die Eigenschaft „type“ des Datasets auf `OracleTable` fest. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ des Datasets muss auf `OracleTable` festgelegt werden. | Ja |
| schema | Name des Schemas. |Quelle: Nein, Senke: Ja  |
| table | Name der Tabelle/Ansicht. |Quelle: Nein, Senke: Ja  |
| tableName | Name der Tabelle/Ansicht mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie für eine neue Workload `schema` und `table`. | Quelle: Nein, Senke: Ja |

**Beispiel:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Oracle-Quelle und -Senke unterstützt werden. Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Oracle mittels Datenpartitionierung finden Sie unter [Parallele Kopie von Oracle](#parallel-copy-from-oracle).

Legen Sie zum Kopieren von Daten aus Oracle den Quelltyp in der Kopieraktivität auf `OracleSource` fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ der Quelle der Kopieraktivität muss auf `OracleSource` festgelegt werden. | Ja |
| oracleReaderQuery | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. z. B. `"SELECT * FROM MyTable"`.<br>Wenn Sie partitioniertes Laden aktivieren, müssen Sie die entsprechenden integrierten Partitionsparameter in Ihre Abfrage integrieren. Beispiele finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle). | Nein |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten aus Oracle geladen werden. <br>Zulässige Werte sind: **None** (Standardwert), **PhysicalPartitionsOfTable** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (d.h. nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten aus einer Oracle-Datenbank durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| partitionNames | Die Liste der physischen Partitionen, die kopiert werden müssen. <br>Verwenden Sie diese Option, wenn die Partitionsoption `PhysicalPartitionsOfTable` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfTabularPartitionName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle). | Nein |
| partitionColumnName | Geben Sie den Namen der Quellspalte als **Integer** an, der von der Bereichspartitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionColumnName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle). | Nein |
| partitionUpperBound | Der Höchstwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionUpbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle). | Nein |
| partitionLowerBound | Der Mindestwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionLowbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Oracle](#parallel-copy-from-oracle). | Nein |

**Beispiel: Kopieren von Daten mit einer einfachen Abfrage ohne Partition**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle als Senke

Legen Sie zum Kopieren von Daten in Oracle den Senkentyp in der Kopieraktivität auf `OracleSink` fest. Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ der Senke der Kopieraktivität muss auf `OracleSink` festgelegt werden. | Ja |
| writeBatchSize | Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße `writeBatchSize` erreicht.<br/>Zulässige Werte sind Integer-Werte (Anzahl der Zeilen). |Nein (Standardwert ist 10.000) |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bevor ein Timeout auftritt.<br/>Zulässige Werte sind Timespan-Werte. Beispiel: 00:30:00 (30 Minuten). | Nein |
| preCopyScript | Geben Sie eine SQL-Abfrage an, die bei jeder Ausführung von der Kopieraktivität ausgeführt werden soll, bevor Daten in Oracle geschrieben werden. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. | Nein |
| maxConcurrentConnections |Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Parallele Kopie von Oracle

Der Oracle-Connector stellt eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten aus Oracle zur Verfügung. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

![Screenshot der Partitionierungsoptionen](./media/connector-oracle/connector-oracle-partition-options.png)

Wenn Sie partitioniertes Kopieren aktivieren, führt der Dienst parallele Abfragen gegenüber Ihrer Oracle-Quelle aus, um Daten anhand von Partitionen zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie z. B. `parallelCopies` auf vier einstellen, generiert der Dienst gleichzeitig vier Abfragen auf der Grundlage der von Ihnen angegebenen Partitionsoption und -einstellungen und führt sie aus, wobei jede Abfrage einen Teil der Daten aus Ihrer Oracle-Datenbank abruft.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren, vor allem, wenn Sie große Datenmengen aus Ihrer Oracle-Datenbank laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben. (Geben Sie nur den Ordnernamen an.) In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen          | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Während der Ausführung erkennt der Dienst automatisch die physischen Partitionen und kopiert Daten nach Partitionen. |
| Vollständiges Laden aus einer großen Tabelle ohne physische Partitionen, aber mit einer Integerspalte für die Datenpartitionierung | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Ohne Angabe wird die Primärschlüsselspalte verwendet. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage mit physischen Partitionen | **Partitionsoption:** Physische Partitionen der Tabelle.<br>**Abfrage**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`<br>**Partitionsname:** Geben Sie den Namen der Partitionen an, aus denen Daten kopiert werden sollen. Wenn nicht angegeben, erkennt der Dienst automatisch die physischen Partitionen der Tabelle, die Sie im Oracle-Datensatz angegeben haben.<br><br>Während der Ausführung ersetzt der Dienst `?AdfTabularPartitionName` durch den tatsächlichen Partitionsnamen und sendet ihn an Oracle. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage ohne physische Partitionen, aber mit einer Integerspalte für die Datenpartitionierung | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Die Partitionierung kann auf der Grundlage der Spalte mit dem Datentyp „Integer“ erfolgen.<br>**Obergrenze der Partition** und **Untergrenze der Partition**: Geben Sie an, ob Sie anhand der Partitionsspalte filtern möchten, um nur Daten zwischen der Ober- und der Untergrenze zu erhalten.<br><br>Während der Ausführung ersetzt der Dienst `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` und `?AdfRangePartitionLowbound` durch die tatsächlichen Spaltennamen und Wertebereiche für jede Partition und sendet sie an Oracle. <br>Wenn z. B. für Ihre Partitionsspalte „ID“ die untere Grenze auf 1 und die obere Grenze auf 80 festgelegt ist und die Parallelkopie auf 4 eingestellt ist, ruft der Dienst Daten nach 4 Partitionen ab. Die ID-Bereiche sehen dann wie folgt aus: [1–20], [21–40], [41–60] und [61–80]. |

> [!TIP]
> Beim Kopieren von Daten aus einer nicht partitionierten Tabelle können Sie die Partitionsoption „Dynamischer Bereich“ verwenden, um eine Partitionierung auf Grundlage einer ganzzahlige Spalte durchzuführen. Wenn die Quelldaten keinen solchen Spaltentyp enthalten, können Sie mithilfe der [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm)-Funktion in der Quellabfrage eine Spalte generieren und diese als Partitionsspalte verwenden.

**Beispiel: Abfrage mit physischer Partition**

```json
"source": {
    "type": "OracleSource",
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
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Datentypzuordnung für Oracle

Wenn Sie Daten von und nach Oracle kopieren, werden die folgenden vorläufigen Datentypzuordnungen innerhalb des Dienstes verwendet. Weitere Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| Oracle-Datentyp | Typ von vorläufigen Daten |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(nur für Oracle 10g und höher unterstützt) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| GLEITKOMMAZAHL |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| INTEGER |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER (p,s) |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| NUMBER ohne Genauigkeit und Dezimalstellen |Double |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Die Datentypen INTERVAL YEAR TO MONTH und INTERVAL DAY TO SECOND werden nicht unterstützt.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
