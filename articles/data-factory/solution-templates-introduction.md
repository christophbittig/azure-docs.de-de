---
title: Übersicht über Vorlagen
description: Erfahren Sie, wie Sie mithilfe einer vordefinierten Vorlage schnell den Einstieg in Azure Data Factory finden.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: c384985ae178f22c15c4a408d7a2cd42d0fbf416
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743382"
---
# <a name="templates"></a>Vorlagen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Vorlagen sind vordefinierte Azure Data Factory-Pipelines, die Ihnen den schnellen Einstieg in Azure Data Factory ermöglichen. Vorlagen sind nützlich, wenn Sie noch nicht mit Data Factory vertraut sind und schnell die ersten Schritte ausführen möchten. Diese Vorlagen verringern den Entwicklungsaufwand für das Erstellen von Datenintegrationsprojekten und verbessern dadurch die Produktivität der Entwickler.

## <a name="create-data-factory-pipelines-from-templates"></a>Erstellen von Data Factory-Pipelines anhand von Vorlagen

Sie können mit dem Erstellen einer Data Factory-Pipeline aus einer Vorlage beginnen. Dabei haben Sie die folgenden zwei Möglichkeiten:

1.  Wählen Sie auf der Startseite von Data Factory im Abschnitt **Weitere Informationen** die Option **Pipelinevorlagen** aus, um den Vorlagenkatalog zu öffnen.

    :::image type="content" source="media/doc-common-process/home-page-pipeline-templates-tile.png" alt-text="Screenshot: Öffnen des Vorlagenkatalogs über die Data Factory-Startseite":::

1.  Wählen Sie im Ressourcen-Explorer auf der Registerkarte „Autor“ das Pluszeichen ( **+** ) aus, und klicken Sie dann **Pipeline aus Vorlage**, um den Vorlagenkatalog zu öffnen.

    :::image type="content" source="media/solution-templates-introduction/templates-introduction-image-2.png" alt-text="Screenshot: Öffnen des Vorlagenkatalogs über die Registerkarte „Autor“":::

## <a name="template-gallery"></a>Vorlagenkatalog

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-3.png" alt-text="Screenshot: Seite „Vorlagenkatalog“":::

### <a name="out-of-the-box-data-factory-templates"></a>Integrierte Data Factory-Vorlagen

Data Factory verwendet Azure Resource Manager-Vorlagen für das Speichern von Data Factory-Pipelinevorlagen. Im [offiziellen GitHub-Repository für Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates) werden alle Resource Manager-Vorlagen zusammen mit der für die integrierten Data Factory-Vorlagen verwendeten Manifestdatei angezeigt. Die von Microsoft bereitgestellten vordefinierten Vorlagen umfassen unter anderem die folgenden Elemente:

-   Kopiervorlagen:

    -   [Massenkopieren aus der Datenbank](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopieren neuer Dateien basierend auf LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopieren von Dateien aus mehreren Containern zwischen dateibasierten Speichern](solution-template-copy-files-multiple-containers.md)

    -   [Verschieben von Dateien](solution-template-move-files.md)

    -   [Deltakopie aus Datenbank](solution-template-delta-copy-with-control-table.md)

    -   Kopieren von \<source\> nach \<destination\>

        -   [Aus Amazon S3 in Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Aus Google Big Query in Azure Data Lake Store Gen 2

        -   Aus HDF in Azure Data Lake Store Gen 2

        -   Aus Netezza in Azure Data Lake Store Gen 1

        -   Aus einer lokalen SQL Server-Instanz in Azure SQL-Datenbank

        -   Aus einer lokalen SQL Server-Instanz in Azure Synapse Analytics

        -   Aus einer lokalen Oracle-Instanz in Azure Synapse Analytics

-   SSIS-Vorlagen

    -   Planen der Azure-SSIS Integration Runtime für die Ausführung von SSIS-Paketen

-   Transformationsvorlagen

    -   [ETL mit Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meine Vorlagen

Sie können auch eine Pipeline als Vorlage speichern, indem Sie auf der Registerkarte „Pipeline“ die Option **Als Vorlage speichern** auswählen.

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-4.png" alt-text="Screenshot: Speichern einer Pipeline als Vorlage":::

Nachdem Sie auf der Seite **Vorlagenkatalog** die Option **Meine Vorlagen** aktiviert haben, können Sie die als Vorlagen gespeicherten Pipelines im rechten Seitenbereich dieser Seite anzeigen. 

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-5.png" alt-text="Screenshot: Bereich „Meine Vorlagen“":::

> [!NOTE]
> Um das Feature „Meine Vorlagen“ verwenden zu können, müssen Sie die Git-Integration aktivieren. Azure DevOps-Git- und GitHub werden unterstützt.
