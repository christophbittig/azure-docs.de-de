---
title: Verschieben von Daten in Azure API for FHIR in Azure Synapse Analytics
description: In diesem Artikel wird das Verschieben von FHIR-Daten in Synapse beschrieben.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: e548361f6cf8d638609dd9daa83a175fa49f3679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355524"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>Verschieben von Daten aus Azure API for FHIR in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie Daten aus Azure API for FHIR in Azure Synapse Analytics verschieben können. Dabei handelt es sich um einen [unbegrenzten](https://azure.microsoft.com/services/synapse-analytics/)Analysedienst, der Datenintegration, Data Warehousing für Unternehmen und Big Data-Analysen zusammenführt. 

Das Verschieben von Daten vom FHIR-Server nach Synapse umfasst das Exportieren der Daten mithilfe des `$export` FHIR-Vorgangs, gefolgt von einer Reihe von Schritten zum Transformieren und Laden der Daten in Synapse. Dieser Artikel führt Sie durch zwei der verschiedenen Ansätze, die beide zeigen, wie Sie FHIR-Ressourcen in tabellarische Formate konvertieren, während Sie sie in Synapse verschieben.

* **Laden sie exportierte Daten mithilfe von T-SQL in Synapse:** Verwenden Sie `$export` den Vorgang, um FHIR-Ressourcen im Format in einen **Azure Data Lake Gen 2-Blobspeicher (ADL Gen 2)** zu `NDJSON` verschieben. Laden Sie die Daten aus dem Speicher mithilfe von T-SQL in **serverlose oder dedizierte SQL-Pools** in Synapse. Konvertieren Sie diese Schritte mithilfe von [Synapse-Pipelines](../../synapse-analytics/get-started-pipelines.md)in eine stabile Datenverlagerungspipeline.
* **Verwenden Sie die Tools aus dem FHIR Analytics Pipelines OSS-Repository:** Das [Repository der FHIR-Analysepipeline](https://github.com/microsoft/FHIR-Analytics-Pipelines) enthält Tools, mit denen eine **Azure Data Factory-Pipeline (ADF)** erstellt werden kann, um FHIR-Daten in einen **Common Data Model-Ordner (CDM)** und aus dem CDM-Ordner in Synapse zu verschieben.

## <a name="load-exported-data-to-synapse-using-t-sql"></a>Laden exportierter Daten in Synapse mithilfe von T-SQL

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` zum Verschieben von FHIR-Daten in Azure Data Lake Gen2-Speicher

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="Azure Storage in Synapse mithilfe von $export":::

#### <a name="configure-your-fhir-server-to-support-export"></a>Konfigurieren Des FHIR-Servers zur Unterstützung `$export`

Azure API for FHIR implementiert den `$export` von der FHIR-Spezifikation definierten Vorgang, um alle oder eine gefilterte Teilmenge der FHIR-Daten im Format zu `NDJSON` exportieren. Darüber hinaus wird der [de-identified-Export](./de-identified-export.md) unterstützt, um FHIR-Daten während des Exports zu anonymisieren. Wenn Sie verwenden, erhalten Sie standardmäßig das Feature zur `$export` Deidentifikation, dessen Funktion bereits in integriert `$export` ist.

Um FHIR-Daten in Azure Blob Storage zu exportieren, müssen Sie zunächst Ihren FHIR-Server so konfigurieren, dass Daten in das Speicherkonto exportiert werden. Sie müssen (1) die verwaltete Identität aktivieren, (2) zu Access Control im Speicherkonto wechseln und die Rollenzuweisung hinzufügen. (3) Wählen Sie Ihr Speicherkonto für `$export` aus. Weitere Schritt-für-Schritt-Anleitungen finden Sie [hier.](./configure-export-data.md)

Sie können den Server so konfigurieren, dass die Daten in eine beliebige Art von Azure-Speicherkonto exportiert werden. Es wird jedoch empfohlen, die Daten nach ADL Gen 2 zu exportieren, um eine optimale Abstimmung mit Synapse zu erzielen.

#### <a name="using-export-command"></a>Verwenden des `$export` Befehls

Nachdem Sie Ihren FHIR-Server konfiguriert haben, können Sie die [Dokumentation](./export-data.md#using-export-command) befolgen, um Ihre FHIR-Ressourcen auf System-, Patienten- oder Gruppenebene zu exportieren. Beispielsweise können Sie alle Ihre FHIR-Daten, die mit den Patienten in einem verknüpft `Group` sind, mit dem folgenden `$export` Befehl exportieren, in dem Sie ihren ADL Gen 2-Blobspeichernamen im Feld `{{BlobContainer}}` angeben:

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

Sie können auch `_type` den Parameter im `$export` obigen Aufruf verwenden, um die Ressourcen einzuschränken, die Sie exportieren möchten. Mit dem folgenden Aufruf werden beispielsweise nur `Patient` die Ressourcen , und `MedicationRequest` `Observation` exportiert:

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

Weitere Informationen zu den verschiedenen unterstützten Parametern finden Sie in unserem `$export` Seitenabschnitt zu den [Abfrageparametern.](./export-data.md#settings-and-parameters)

### <a name="create-a-synapse-workspace"></a>Erstellen eines Synapse-Arbeitsbereichs

Vor der Verwendung von Synapse benötigen Sie einen Synapse-Arbeitsbereich. Sie erstellen einen Azure Synapse Analytics-Dienst auf Azure-Portal. Eine ausführlichere Anleitung finden Sie [hier.](../../synapse-analytics/get-started-create-workspace.md) Sie benötigen ein `ADLSGEN2` Konto, um einen Arbeitsbereich zu erstellen. Ihr Azure Synapse Arbeitsbereich verwendet dieses Speicherkonto, um Ihre Synapse-Arbeitsbereichsdaten zu speichern.

Nachdem Sie einen Arbeitsbereich erstellt haben, können Sie Ihren Arbeitsbereich auf Synapse Studio anzeigen, indem Sie sich unter bei Ihrem Arbeitsbereich anmelden https://web.azuresynapse.net oder Synapse Studio im Azure-Portal starten.

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>Erstellen eines verknüpften Diensts zwischen Azure Storage und Synapse

Um Ihre Daten in Synapse zu verschieben, müssen Sie einen verknüpften Dienst erstellen, der Ihr Azure Storage-Konto mit Synapse verbindet. Weitere Schritt-für-Schritt-Anleitungen finden Sie [hier.](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)

1. Navigieren Sie auf Synapse Studio zur Registerkarte **Verwalten,** und wählen Sie unter Externe Verbindungen die Option **Verknüpfte** **Dienste** aus.
2. Wählen Sie **Neu** aus, um einen neuen verknüpften Dienst hinzuzufügen.
3. Wählen Sie in der Liste **Azure Data Lake Storage Gen2** und dann **Weiter** aus.
4. Geben Sie Ihre Anmeldeinformationen für die Authentifizierung ein. Wenn Sie fertig sind, klicken Sie auf **Erstellen**.

Nachdem Sie nun über einen verknüpften Dienst zwischen Ihrem ADL Gen 2-Speicher und Synapse verfügen, können Sie Synapse SQL Pools verwenden, um Ihre FHIR-Daten zu laden und zu analysieren.

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>Entscheiden zwischen serverlosem und dediziertem SQL Pool

Azure Synapse Analytics bietet zwei verschiedene SQL Pools: einen serverlosen SQL pool und einen dedizierten pool SQL. Serverloser SQL-Pool bietet die Flexibilität, Daten direkt im Blobspeicher mithilfe des serverlosen SQL Endpunkts ohne Ressourcenbereitstellung abzufragen. Dedizierter SQL-Pool verfügt über die Verarbeitungsleistung für hohe Leistung und Parallelität und wird für Data Warehousing-Funktionen auf Unternehmen-Ebene empfohlen. Weitere Informationen zu den beiden SQL Pools finden Sie auf der [Synapse-Dokumentationsseite](../../synapse-analytics/sql/overview-architecture.md) zu SQL-Architektur.

#### <a name="using-serverless-sql-pool"></a>Verwenden eines serverlosen SQL Pools

Da es serverlos ist, gibt es keine Infrastruktur zum Einrichten oder Verwalten von Clustern. Sie können mit dem Abfragen von Daten aus Synapse Studio beginnen, sobald der Arbeitsbereich erstellt wurde.

Beispielsweise kann die folgende Abfrage verwendet werden, um ausgewählte Felder von `Patient.ndjson` in eine tabellarische Struktur zu transformieren:

```sql
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson', 
FORMAT = 'csv', 
FIELDTERMINATOR ='0x0b', 
FIELDQUOTE = '0x0b')  
WITH (doc NVARCHAR(MAX)) AS rows     
CROSS APPLY OPENJSON(doc)     
WITH ( 
    ResourceId VARCHAR(64) '$.id', 
    Active VARCHAR(10) '$.active', 
    FullName VARCHAR(100) '$.name[0].text', 
    Gender VARCHAR(20) '$.gender', 
       ...
) 
```

In der obigen Abfrage greift die `OPENROWSET` Funktion auf Dateien in Azure Storage zu, analysiert `OPENJSON` JSON-Text und gibt die JSON-Eingabeeigenschaften als Zeilen und Spalten zurück. Bei jeder Ausführung dieser Abfrage liest der serverlose SQL Pool die Datei aus dem Blobspeicher, analysiert den JSON-Code und extrahiert die Felder.

Sie können die Ergebnisse auch im Parquet-Format in einer [externen Tabelle](../../synapse-analytics/sql/develop-tables-external-tables.md) materialisieren, um eine bessere Abfrageleistung zu erzielen, wie unten dargestellt:

```sql
-- Create External data source where the parquet file will be written 
CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH ( 
    LOCATION = 'https://{{youraccount}}.blob.core.windows.net/{{exttblcontainer}}' 
); 
GO 

-- Create External File Format 
CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH ( 
    FORMAT_TYPE = PARQUET, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec' 
); 
GO 

CREATE EXTERNAL TABLE [dbo].[Patient] WITH ( 
        LOCATION = 'PatientParquet/', 
        DATA_SOURCE = [MyDataSource], 
        FILE_FORMAT = [ParquetFF] 
) AS 
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
-- Use rest of the SQL statement from the previous example --
```

#### <a name="using-dedicated-sql-pool"></a>Verwenden eines dedizierten SQL Pools

Ein dedizierter SQL-Pool unterstützt verwaltete Tabellen und einen hierarchischen Cache für die Leistung im Arbeitsspeicher. Sie können Big Data mit einfachen T-SQL-Abfragen importieren und dann die Leistungsfähigkeit der verteilten Abfrage-Engine nutzen, um Hochleistungsanalysen auszuführen.

Die einfachste und schnellste Möglichkeit zum Laden von Daten aus Ihrem Speicher in einen dedizierten SQL Pool ist die Verwendung des **`COPY`** Befehls in T-SQL, der CSV-, Parquet- und ORC-Dateien lesen kann. Verwenden Sie wie in der folgenden Beispielabfrage den Befehl , `COPY` um die Zeilen in eine `NDJSON` tabellarische Struktur zu laden. 

```sql
-- Create table with HEAP, which is not indexed and does not have a column width limitation of NVARCHAR(4000) 
CREATE TABLE StagingPatient ( 
Resource NVARCHAR(MAX) 
) WITH (HEAP) 
COPY INTO StagingPatient 
FROM 'https://{{yourblobaccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
WITH ( 
FILE_TYPE = 'CSV', 
ROWTERMINATOR='0x0a', 
FIELDQUOTE = '', 
FIELDTERMINATOR = '0x00' 
) 
GO
```

Sobald die JSON-Zeilen in der obigen Tabelle vorhanden `StagingPatient` sind, können Sie mithilfe der -Funktion unterschiedliche Tabellenformate der Daten erstellen `OPENJSON` und die Ergebnisse in Tabellen speichern. Im Folgenden finden Sie ein Beispiel SQL Abfrage zum Erstellen einer `Patient` Tabelle durch Extrahieren einiger Felder aus der `Patient` Ressource:

```sql
SELECT RES.* 
INTO Patient 
FROM StagingPatient
CROSS APPLY OPENJSON(Resource)   
WITH (
    ResourceId VARCHAR(64) '$.id',
    FullName VARCHAR(100) '$.name[0].text',
    FamilyName VARCHAR(50) '$.name[0].family',
    GivenName VARCHAR(50) '$.name[0].given[0]',
    Gender VARCHAR(20) '$.gender',
    DOB DATETIME2 '$.birthDate',
    MaritalStatus VARCHAR(20) '$.maritalStatus.coding[0].display',
    LanguageOfCommunication VARCHAR(20) '$.communication[0].language.text'
) AS RES 
GO

```

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>Verwenden von FHIR Analytics Pipelines OSS-Tools

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="Verwenden der ADF-Pipeline zum Verschieben von Daten in den CDM-Ordner und dann in Synapse":::

> [!Note]
> [Die FHIR Analytics-Pipeline](https://github.com/microsoft/FHIR-Analytics-Pipelines) ist ein Open-Source-Tool, das unter der MIT-Lizenz veröffentlicht wurde und nicht durch die Microsoft-SLA für Azure-Dienste abgedeckt ist.

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>ADF-Pipeline zum Verschieben von FHIR-Daten in den CDM-Ordner

Common Data Model Ordner (CDM) ist ein Ordner in einem Data Lake, der klar definierten und standardisierten Metadatenstrukturen und selbstbeschreibenden Daten entspricht. Diese Ordner erleichtern die Metadateninteroperabilität zwischen Datenerstellern und Datenverbrauchern. Bevor Sie FHIR-Daten in den CDM-Ordner verschieben, können Sie Ihre Daten in eine Tabellenkonfiguration transformieren.

### <a name="generating-table-configuration"></a>Generieren der Tabellenkonfiguration

Klonen Sie das Repository, um alle Skripts und den Quellcode abzurufen. Verwenden Sie `npm install` , um die Abhängigkeiten zu installieren. Führen Sie den folgenden Befehl aus dem `Configuration-Generator` Ordner aus, um einen Tabellenkonfigurationsordner mithilfe von Anweisungen im YAML-Format zu generieren:

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

Sie können die Beispieldateien verwenden `YAML` und im Repository bereitgestellt `resourcesConfig.yml` `propertiesGroupConfig.yml` werden.

### <a name="generating-adf-pipeline"></a>Generieren einer ADF-Pipeline

Nun können Sie den Inhalt der generierten Tabellenkonfiguration und einige andere Konfigurationen verwenden, um eine ADF-Pipeline zu generieren. Diese ADF-Pipeline exportiert beim Auslösen die Daten vom FHIR-Server mithilfe der `$export` API und schreibt zusammen mit den zugehörigen CDM-Metadaten in einen CDM-Ordner.

1. Erstellen Sie eine Azure Active Directory (AD)-Anwendung und einen Dienstprinzipal. Die ADF-Pipeline verwendet einen Azure Batch-Dienst für die Transformation und benötigt eine Azure AD Anwendung für den Batchdienst. Befolgen Sie [Azure AD Dokumentation.](../../active-directory/develop/howto-create-service-principal-portal.md)
2. Gewähren Sie dem Dienstprinzipal Zugriff für den Exportspeicherort. Gewähren `Access Control` Sie im des Exportspeichers `Storage Blob Data Contributor` der Azure AD Anwendung die Rolle .
3. Stellen Sie die Ausgangspipeline bereit. Verwenden Sie die Vorlage `fhirServiceToCdm.json` für eine benutzerdefinierte Bereitstellung in Azure. In diesem Schritt werden die folgenden Azure-Ressourcen erstellt:
    - Eine ADF-Pipeline mit dem Namen `{pipelinename}-df` .
    - Ein Schlüsseltresor mit dem Namen `{pipelinename}-kv` zum Speichern des geheimen Clientschlüssels.
    - Ein Batchkonto mit dem Namen `{pipelinename}batch` zum Ausführen der Transformation.
    - Ein Speicherkonto mit dem Namen `{pipelinename}storage` .
4. Gewähren Sie Zugriff auf die Azure Data Factory. Gewähren Sie der Data Factory in der Zugriffssteuerung Ihres FHIR-Diensts `FHIR data exporter` die Rollen und `FHIR data reader` `{pipelinename}-df` .
5. Hochladen den Inhalt des Tabellenkonfigurationsordners an den Konfigurationscontainer.
6. Wechseln Sie zu `{pipelinename}-df` , und lösen Sie die Pipeline aus. Die exportierten Daten sollten im CDM-Ordner des Speicherkontos angezeigt `{pipelinename}storage` werden. Es sollte ein Ordner für jede Tabelle mit einer CSV-Datei angezeigt werden.

### <a name="from-cdm-folder-to-synapse"></a>Vom CDM-Ordner zu Synapse

Nachdem Sie die Daten in einem CDM-Format exportiert und in Ihrem ADL Gen 2-Speicher gespeichert haben, können Sie ihre Daten jetzt im CDM-Ordner nach Synapse verschieben.

Sie können eine CDM-zu-Synapse-Pipeline mithilfe einer Konfigurationsdatei erstellen, die in etwa wie folgt aussieht:

```json
{
  "ResourceGroup": "",
  "TemplateFilePath": "../Templates/cdmToSynapse.json",
  "TemplateParameters": {
    "DataFactoryName": "",
    "SynapseWorkspace": "",
    "DedicatedSqlPool": "",
    "AdlsAccountForCdm": "",
    "CdmRootLocation": "cdm",
    "StagingContainer": "adfstaging",
    "Entities": ["LocalPatient", "LocalPatientAddress"]
  }
}
```

Führen Sie dieses Skript mit der obigen Konfigurationsdatei aus:

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

Fügen Sie die verwaltete ADF-Identität als SQL Benutzer in SQL Datenbank hinzu. Hier sehen Sie ein Beispiel SQL Skripts zum Erstellen eines Benutzers und einer Zuweisungsrolle:

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie zwei verschiedene Möglichkeiten kennengelernt, Ihre FHIR-Daten in Synapse zu verschieben: (1) mithilfe von zum Verschieben von `$export` Daten in den Blobspeicher von ADL Gen 2 und anschließendes Laden der Daten in Synapse SQL Pools und (2) Mithilfe der ADF-Pipeline zum Verschieben von FHIR-Daten in den CDM-Ordner und dann in Synapse.

Als Nächstes erfahren Sie mehr über die Anonymisierung Ihrer FHIR-Daten beim Verschieben von Daten in Synapse, um sicherzustellen, dass Ihre Gesundheitsinformationen geschützt sind:
 
>[!div class="nextstepaction"]
>[Exportieren von nicht identifizierten Daten](./de-identified-export.md)








