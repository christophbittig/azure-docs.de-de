---
title: Pushen von Data Factory-Herkunftsdaten an Azure Purview
description: Erfahren Sie, wie Sie Data Factory-Herkunftsdaten an Azure Purview pushen
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: ff6c53efea27bc2bb4e6273180aa64178d6a0295
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424256"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Pushen von Data Factory-Herkunftsdaten an Azure Purview (Vorschau)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie auf der Data Factory-Benutzeroberfläche eine Pipeline, die Aktivitäten ausführt und Herkunftsdaten an das Azure Purview-Konto meldet. Anschließend können Sie alle Herkunftsinformationen in Ihrem Azure Purview-Konto anzeigen. 

Die Datenherkunft wird derzeit für Copy-, Datenfluss- und Execute SSIS-Aktivitäten unterstützt. Unter [Unterstützte Azure Data Factory-Aktivitäten](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities) erhalten Sie mehr Informationen zu den unterstützten Funktionen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Data Factory** Wenn Sie nicht über eine Azure Data Factory-Instanz verfügen, finden Sie weitere Informationen unter [Erstellen einer Azure Data Factory-Instanz](./quickstart-create-data-factory-portal.md).
* **Azure Purview-Konto.** Das Purview-Konto erfasst alle von der Data Factory generierten Herkunftsdaten. Falls Sie noch nicht über ein Azure Purview-Konto verfügen, lesen Sie den Schnellstart [Erstellen eines Azure Purview-Kontos im Azure-Portal](../purview/create-catalog-portal.md).

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>Ausführen von Pipelines und Verschieben von Herkunftsdaten nach Azure Purview

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>Schritt 1: Herstellen einer Verbindung zwischen Data Factory und Ihrem Purview-Konto

Sie können eine Verbindung zwischen Data Factory und dem Purview-Konto herstellen, indem Sie die Schritte unter [Herstellen einer Verbindung zwischen Data Factory und Azure Purview](connect-data-factory-to-azure-purview.md) befolgen.

### <a name="step-2-run-pipeline-in-data-factory"></a>Schritt 2: Ausführen der Pipeline in Data Factory

Sie können in Data Factory Pipelines erstellen sowie Copy- und Dataflow-Aktivitäten ausführen. Sie müssen für das Aufzeichnen der Herkunftsdaten keine zusätzliche Konfiguration durchführen. Die Herkunftsdaten werden während der Ausführung der Aktivitäten automatisch aufgezeichnet.

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="Screenshot: Copy- und Dataflow-Aktivität":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="Screenshot der Aktivität „SSIS-Paket ausführen“":::

Weitere Informationen darüber, wie Sie Copy-, Dataflow- und Execute SSIS-Aktivitäten erstellen, finden Sie unter [Kopieren von Daten aus Azure Blob Storage in eine Datenbank in Azure SQL-Datenbank mithilfe von Azure Data Factory](./tutorial-copy-data-portal.md), [Transformieren von Daten mithilfe von Mapping Data Flow](./tutorial-data-flow.md) und [Ausführen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-3-monitor-lineage-reporting-status"></a>Schritt 3: Überwachen Sie den Status der Datenherkunftsberichte

Nachdem Sie die Pipeline ausgeführt haben, können Sie in der [Überwachungsansicht der Synapse-Pipeline](monitor-visually.md#monitor-pipeline-runs) den Berichterstellungsstatus für die Datenherkunft überprüfen. Klicken Sie dazu auf die folgende Schaltfläche **Datenherkunftsstatus**. Dieselben Informationen sind ebenfalls in der JSON-Aktivitätsausgabe zu finden -> Abschnitt `reportLineageToPurvew`.

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Überwachen Sie den Status der Datenherkunftsberichte in der Pipeline-Überwachungsansicht.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Schritt 4: Anzeigen von Herkunftsinformationen in Ihrem Purview-Konto

In der Purview-Benutzeroberfläche können Sie Ressourcen durchsuchen und den Typ „Azure Data Factory“ auswählen. Sie können Data Catalog auch mit Stichwörtern durchsuchen.

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="Screenshot: Ressourcen in Purview durchsuchen" lightbox="./media/data-factory-purview/view-dataset.png":::

Wenn Sie in der Aktivitätsressource auf die Registerkarte „Datenherkunft“klicken, werden alle Herkunftsinformationen angezeigt.

- Kopieraktivität:

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Screenshot: Datenherkunft der Copy-Aktivität in Purview" lightbox="./media/data-factory-purview/copy-lineage.png":::

- Datenflowaktivität

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Screenshot: Dataflow-Herkunft in Purview" lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > Bei den Herkunftsdaten der Dataflow-Aktivität werden nur Quelle und Senke unterstützt. Herkunftsdaten für die Datenflusstransformation werden noch nicht unterstützt.

- Aktivität „SSIS-Paket ausführen“:

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Screenshot: Datenherkunft für die Aktivität „SSIS-Paket ausführen“ in Purview" lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > Bei den Herkunftsdaten der Aktivität „SSIS-Paket ausführen“ werden nur Quelle und Ziel unterstützt. Herkunftsdaten der Transformation werden noch nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Benutzerhandbuch zur Katalogherkunft](../purview/catalog-lineage-user-guide.md)

[Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview](connect-data-factory-to-azure-purview.md)