---
title: Verknüpfte Dienste
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie mehr über verknüpfte Dienste in Azure Data Factory und Azure Synapse Analytics. Verknüpfte Dienste verknüpfen Compute- und Datenspeicher mit dem Dienst.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ce62577dd73d6c6318c5358e4cff3ada4f4bc0a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068861"
---
# <a name="linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Verknüpfte Dienste in Azure Data Factory und Azure Synapse Analytics

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuelle Version](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, was verknüpfte Dienste sind, wie sie im JSON-Format definiert und in Azure Data Factory und Azure Synapse Analytics verwendet werden.

Weitere Informationen finden Sie im Einführungsartikel zu [Azure Data Factory](introduction.md) oder [Azure Synapse](../synapse-analytics/overview-what-is.md).

## <a name="overview"></a>Übersicht

Azure Data Factory und Azure Synapse Analytics können eine oder mehrere Pipelines umfassen. Bei einer **Pipeline** handelt es sich um eine logische Gruppierung von **Aktivitäten**, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Sie können beispielsweise mit einer Kopieraktivität Daten aus SQL Server in Azure Blob Storage kopieren. Anschließend könnten Sie eine Hive-Aktivität verwenden, die ein Hive-Skript für einen Azure HDInsight-Cluster ausführt, um Daten aus dem Blob Storage zu verarbeiten, um Ausgabedaten zu produzieren. Schließlich könnten Sie die Ausgabedaten mit einer zweiten Kopieraktivität in Azure Synapse Analytics kopieren, auf dessen Basis Business Intelligence-Berichtslösungen (BI) erstellt werden. Weitere Informationen zu Pipelines und Aktivitäten finden Sie unter [Pipelines und Aktivitäten](concepts-pipelines-activities.md).

Ein **Datensatz** ist eine benannte Ansicht von Daten, die einfach auf die Daten verweist, die Sie in Ihren **Aktivitäten** als Ein- und Ausgabe verwenden möchten.

Bevor Sie ein Dataset erstellen, müssen Sie einen **verknüpften Dienst** erstellen, um Ihren Datenspeicher mit der Data Factory oder einem Synapse-Arbeitsbereich zu verknüpfen. Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die der Dienst zum Herstellen einer Verbindung mit externen Ressourcen benötigt. Sie können sich dies wie folgt vorstellen: Das Dataset stellt die Struktur der Daten innerhalb des verknüpften Datenspeichers dar, und der verknüpfte Dienst definiert die Verbindung mit der Datenquelle. Ein mit Azure Storage verknüpfter Dienst verbindet z. B. ein Speicherkonto mit dem Dienst. Ein Azure-Blob-Dataset repräsentiert den Blobcontainer und den Ordner innerhalb des Azure Storage-Kontos, das die zu verarbeitenden Eingabeblobs enthält.

Hier ist ein Beispielszenario. Um Daten aus dem Blobspeicher in eine SQL-Datenbank zu kopieren, erstellen Sie zwei verknüpfte Dienste: Azure Storage und Azure SQL-Datenbank. Erstellen Sie anschließend zwei Datasets: Azure-Blobdataset (das sich auf den mit Azure Storage verknüpften Dienst bezieht) und Azure SQL-Tabellendataset (das sich auf den mit Azure SQL-Datenbank verknüpften Dienst bezieht). Die mit Azure Storage und Azure SQL-Datenbank verknüpften Dienste enthalten Verbindungszeichenfolgen, die der Dienst zur Laufzeit nutzt, um eine Verbindung mit Ihrer Instanz von Azure Storage bzw. Azure SQL-Datenbank herzustellen. Das Azure-Blobdataset gibt den Blobcontainer und Blobordner an, der die Eingabeblobs in Ihrer Blob Storage-Instanz enthält. Das Azure SQL-Tabellendataset gibt die SQL-Tabelle in Ihrer SQL-Datenbank an, in die die Daten kopiert werden sollen.

Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpftem Dienst im Dienst:

:::image type="content" source="media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png" alt-text="Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpften Diensten":::

## <a name="linked-service-json"></a>JSON-Text für verknüpfte Dienste

Ein verknüpfter Dienst wird wie folgt im JSON-Format definiert:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

Eigenschaft | BESCHREIBUNG | Erforderlich |
-------- | ----------- | -------- |
name | Name des verknüpften Diensts. Siehe [Benennungsregeln](naming-rules.md). |  Ja |
type | Typ des verknüpften Diensts. Beispiel: AzureBlobStorage (Datenspeicher) oder AzureBatch (Compute). Siehe die Beschreibung von „typeProperties“. | Ja |
typeProperties | Die Typeigenschaften unterscheiden sich für jeden Datenspeicher- oder Computetyp. <br/><br/> Informationen zu den unterstützten Datenspeichertypen und ihren Typeigenschaften finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats). Navigieren Sie zum Artikel über den Datenspeicherconnector, um mehr über die für einen Datenspeicher spezifischen Typeigenschaften zu erfahren. <br/><br/> Informationen zu den unterstützten Computetypen und ihren Typeigenschaften finden Sie unter [Verknüpfte Computedienste](compute-linked-services.md). | Ja |
connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein

## <a name="linked-service-example"></a>Beispiel für einen verknüpften Dienst

Der folgende verknüpfte Dienst ist ein mit Azure Blob Storage verknüpfter Dienst. Beachten Sie, dass der Typ auf „Azure Blob Storage“ festgelegt ist. Die Typeigenschaften für den verknüpften Azure Blob Storage-Dienst umfassen eine Verbindungszeichenfolge. Der Dienst verwendet diese Verbindungszeichenfolge, um zur Laufzeit eine Verbindung mit dem Datenspeicher herzustellen.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

Verknüpfte Dienste sowie sämtliche Aktivitäten, Datasets oder Datenflüsse, die auf diese verweisen, können auf der Benutzeroberfläche von Azure Data Factory über den [Verwaltungshub](author-management-hub.md) erstellt werden.

Sie können verknüpfte Dienste mit einem dieser Tools oder SDKs erstellen: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), [Azure Resource Manager Template](quickstart-create-data-factory-resource-manager-template.md) und [Azure Portal](quickstart-create-data-factory-portal.md).


## <a name="data-store-linked-services"></a>Verknüpfte Dienste von Datenspeichern

Die Liste der unterstützten Datenspeicher finden Sie im Artikel [Übersicht über Connectors](copy-activity-overview.md#supported-data-stores-and-formats). Klicken Sie auf einen Datenspeicher, um mehr über die unterstützten Verbindungseigenschaften zu erfahren.

## <a name="compute-linked-services"></a>Verknüpfte Computedienste

Weitere Informationen zu den verschiedenen Compute-Umgebungen, mit denen Sie sich von Ihrem Dienst aus verbinden können, sowie zu den verschiedenen Konfigurationen finden Sie unter [Unterstützte Compute-Umgebungen](compute-linked-services.md).

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich darüber, wie Sie Anmeldeinformationen für eine benutzerseitig zugewiesene verwaltete Identität in einem verknüpften Dienst verwenden.](credentials.md)

In den folgenden Tutorials finden Sie schrittweise Anleitungen zum Erstellen von Pipelines und Datasets mit einem dieser Tools oder SDKs.

- [Schnellstart: Erstellen einer Data Factory mithilfe von .NET](quickstart-create-data-factory-dot-net.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe der REST-API](quickstart-create-data-factory-rest-api.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe des Azure-Portals](quickstart-create-data-factory-portal.md)
