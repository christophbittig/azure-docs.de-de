---
title: Transformieren von Daten mit der Aktivität „Gespeicherte Prozedur“
titleSuffix: Azure Data Factory & Azure Synapse
description: Informationen, wie Sie die SQL Server Stored Procedure-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline zum Aufrufen einer gespeicherten Prozedur in einer Azure SQL-Datenbank-/Data Warehouse-Instanz verwenden können.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 30e40c20aa8e11add35b270baf867a5fd5e46058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016566"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformieren von Daten mit der SQL Server Stored Procedure-Aktivität in Azure Data Factory oder Synapse Analytics
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuelle Version](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie verwenden Transformationsaktivitäten in einer Data Factory- oder Synapse-[Pipeline](concepts-pipelines-activities.md), um Rohdaten in Vorhersagen und Erkenntnisse zu transformieren und zu verarbeiten. Die Stored Procedure-Aktivität ist eine der Transformationsaktivitäten, die Pipelines unterstützen. Dieser Artikel baut auf dem Artikel [Transformieren von Daten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

> [!NOTE]
> Wenn Sie noch nicht mit Azure Data Factory vertraut sind, sollten Sie zunächst den Artikel [Einführung in Azure Data Factory](introduction.md) lesen und anschließend das Tutorial zum Thema [Tutorial: Transformieren von Daten](tutorial-transform-data-spark-powershell.md) durcharbeiten.  Weitere Informationen zu Synapse Analytics finden Sie unter [Was ist Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

Sie können die Aktivität „Gespeicherte Prozedur“ verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer (VM) aufzurufen: 

- Azure SQL-Datenbank
- Azure Synapse Analytics
- SQL Server-Datenbank.  Wenn Sie SQL Server verwenden, müssen Sie die selbstgehostete Integration Runtime auf dem Computer installieren, der die Datenbank hostet, oder auf einem separaten Computer, der Zugriff auf die Datenbank hat. Die selbstgehostete Integration Runtime ist eine Komponente, die lokale Datenquellen bzw. Datenquellen auf virtuellen Azure Computern mit Clouddiensten auf sichere und verwaltete Weise verbindet. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.

> [!IMPORTANT]
> Beim Kopieren von Daten nach Azure SQL-Datenbank oder SQL Server können Sie **SqlSink** in der Kopieraktivität für das Aufrufen einer gespeicherten Prozedur mit der **sqlWriterStoredProcedureName**-Eigenschaft konfigurieren. Ausführliche Informationen zur Eigenschaft finden Sie in folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Das Aufrufen einer gespeicherten Prozedur im Rahmen des Kopierens von Daten in Azure Synapse Analytics mithilfe einer Kopieraktivität wird nicht unterstützt. Sie können jedoch mithilfe der Aktivität „Gespeicherte Prozedur“ eine gespeicherte Prozedur in Azure Synapse Analytics aufrufen. 
>
> Beim Kopieren von Daten aus Azure SQL-Datenbank, SQL Server oder Azure Synapse Analytics können Sie **SqlSource** in der Kopieraktivität mit der Eigenschaft **sqlReaderStoredProcedureName** konfigurieren, um eine gespeicherte Prozedur zum Lesen von Daten aus der Quelldatenbank aufzurufen. Weitere Informationen finden Sie in den folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Syntaxdetails
So sieht das JSON-Format zum Definieren der Aktivität „Gespeicherte Prozedur“ aus:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

In der folgenden Tabelle werden diese JSON-Eigenschaften beschrieben:

| Eigenschaft                  | BESCHREIBUNG                              | Erforderlich |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Der Name der Aktivität                     | Ja      |
| description               | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Nein       |
| type                      | Für die Aktivität „Gespeicherte Prozedur“ lautet der Aktivitätstyp **SqlServerStoredProcedure**. | Ja      |
| linkedServiceName         | Verweis auf die **Azure SQL-Datenbank**-, **Azure Synapse Analytics**- oder **SQL Server**-Instanz, die in Data Factory als verknüpfter Dienst registriert ist. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md). | Ja      |
| storedProcedureName       | Geben Sie den Namen der gespeicherten Prozedur an, die aufgerufen werden soll. | Ja      |
| storedProcedureParameters | Geben Sie die Werte für Parameter der gespeicherten Prozedur an. Verwenden Sie `"param1": { "value": "param1Value","type":"param1Type" }` zum Übergeben von Parameterwerten und deren Typ, der von der Datenquelle unterstützt wird. Wenn Sie für einen Parameter „null“ übergeben müssen, verwenden Sie die folgende Syntax: `"param1": { "value": null }` (nur Kleinbuchstaben). | Nein       |

## <a name="parameter-data-type-mapping"></a>Zuordnen des Parameterdatentyps
Der Datentyp, den Sie für den Parameter angeben, ist der interne Diensttyp, der dem Datentyp in der verwendeten Datenquelle zugeordnet ist. Sie finden die Datentypzuordnungen für Ihre Datenquelle in der Dokumentation zu Connectors. Zum Beispiel:

- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#data-type-mapping-for-azure-synapse-analytics)
- [Zuordnung von Azure SQL-Datenbank-Datentypen](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)
- [Oracle-Datentypzuordnung](connector-oracle.md#data-type-mapping-for-oracle)
- [SQL Server-Datentypzuordnung](connector-sql-server.md#data-type-mapping-for-sql-server)

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden: 

* [U-SQL-Aktivität](transform-data-using-data-lake-analytics.md)
* [Hive-Aktivität](transform-data-using-hadoop-hive.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-Streamingaktivität](transform-data-using-hadoop-streaming.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)
