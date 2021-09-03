---
title: Datenherkunft aus SQL Server Integration Services
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus SQL Server Integration Services beschrieben.
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: aa4fb6b7870831d0f24b0a7071cbf0dfd2431c14
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294544"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>Abrufen der Datenherkunft aus SQL Server Integration Services (SSIS) in Azure Purview

In diesem Artikel werden die Aspekte der Datenherkunft von SQL Server Integration Services (SSIS) in Azure Purview erläutert.

## <a name="prerequisites"></a>Voraussetzungen

- [Migration von SQL Server Integration Services-Workloads in die Cloud per Lift &amp; Shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Der aktuelle Umfang der Unterstützung bezieht sich auf die Extrahierung der Datenherkunft aus SSIS-Paketen, die von der Integration Runtime von Azure Data Factory SSIS ausgeführt werden.

Die lokale Extrahierung der Datenherkunft von SSIS wird noch nicht unterstützt.

### <a name="supported-data-stores"></a>Unterstützte Datenspeicher

| Datenspeicher | Unterstützt |
| ------------------- | ------------------- |
| Azure Blob Storage | Ja |
| Azure Data Lake Storage Gen1 | Ja |
| Azure Data Lake Storage Gen2 | Ja |
| Azure File Storage | Ja |
| Azure SQL-Datenbank \* | Ja |
| Azure SQL Managed Instance \*| Ja |
| Azure Synapse Analytics \* | Ja |
| SQL Server \* | Ja |

*\* Azure Purview unterstützt derzeit keine Abfrage oder gespeicherte Prozedur für die Erfassung der Herkunft oder Überprüfung. Die Herkunft ist auf Tabellen- und Ansichtsquellen beschränkt.*


## <a name="how-to-bring-ssis-lineage-into-purview"></a>Integrieren der Datenherkunft von SSIS in Purview

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>Schritt 1: [Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>Schritt 2: Auslösen der Ausführung von SSIS-Aktivitäten in Azure Data Factory

Sie können [das SSIS-Paket mit der Aktivität „SSIS-Paket ausführen“](../data-factory/how-to-invoke-ssis-package-ssis-activity.md) oder das [SSIS-Paket mit Transact-SQL in ADF SSIS Integration Runtime ausführen](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md).  

Sobald die Aktivität „SSIS-Paket ausführen“ abgeschlossen ist, können Sie den Status des Datenherkunftsberichts in der Aktivitätsausgabe im [Data Factory-Aktivitätsmonitor](../data-factory/monitor-visually.md#monitor-activity-runs) überprüfen.
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="ssis-status":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>Schritt 3: Durchsuchen von Herkunftsinformationen in Ihrem Azure Purview-Konto

- Sie können die Data Catalog-Instanz durchsuchen, indem Sie den Ressourcentyp „SQL Server Integration Services“ auswählen.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="Browserressourcen" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="browser-assets-ssis":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text="browse-assets-ssis-package":::

- Sie können Data Catalog auch mit Stichwörtern durchsuchen.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="search-ssis" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- Sie können Herkunftsinformationen für eine SSIS Execute Package-Aktivität anzeigen und in Data Factory öffnen, um die Aktivitätseinstellungen anzuzeigen/zu bearbeiten.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="show-ssis-lineage" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- Sie können eine Datenquelle auswählen, um detailliert zu untersuchen, wie die Spalten in der Quelle den Spalten im Ziel zugeordnet sind.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="show-ssis-lineage-drill-in" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>Nächste Schritte

- [Migration von SQL Server Integration Services-Workloads in die Cloud per Lift &amp; Shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)