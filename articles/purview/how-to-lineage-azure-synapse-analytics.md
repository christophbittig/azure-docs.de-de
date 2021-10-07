---
title: Metadaten und Datenherkunft in Azure Synapse Analytics
description: In diesem Artikel wird beschrieben, wie Sie Azure Synapse Analytics und Azure Purview verbinden, um die Datenherkunft nachzuverfolgen.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 02ea61356157c7e2a421c736a1af5bd87a1bcb5c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212337"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>Abrufen der Datenherkunft aus Azure Synapse Analytics in Azure Purview

In diesem Dokument werden die Schritte erläutert, die zum Verbinden eines Azure Synapse-Arbeitsbereichs mit einem Azure Purview-Konto erforderlich sind, um die Datenherkunft nachzuverfolgen. Das Dokument enthält zudem Details zum Abdeckungsbereich und zu den unterstützten Datenherkunftsfunktionen.

## <a name="supported-azure-synapse-capabilities"></a>Unterstützte Azure Synapse-Funktionen

Derzeit erfasst Azure Purview die Runtimeherkunft aus den folgenden Azure Synapse-Pipelineaktivitäten:

- [Daten kopieren](../data-factory/copy-activity-overview.md?context=/azure/synapse-analytics/context/context)
- [Datenfluss](../data-factory/concepts-data-flow-overview.md?context=/azure/synapse-analytics/context/context)

> [!IMPORTANT]
> Azure Purview verwirft die Herkunft, wenn die Quelle oder das Ziel ein nicht unterstütztes Datenspeichersystem verwendet.

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="access-secured-azure-purview-account"></a>Zugreifen auf ein geschütztes Azure Purview-Konto
      
Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, erfahren Sie, wie Sie Azure Synapse über private Purview-Endpunkte den [Zugriff auf ein geschütztes Purview-Konto](../synapse-analytics/catalog-and-governance/how-to-access-secured-purview-account.md) ermöglichen können.

## <a name="bring-azure-synapse-lineage-into-purview"></a>Integrieren der Datenherkunft von Azure Synapse in Purview

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>Schritt 1: Verbinden Sie den Azure Synapse-Arbeitsbereich mit Ihrem Purview-Konto

Sie können einen Azure Synapse-Arbeitsbereich mit Purview verbinden. Durch diese Verbindung ist es Azure Synapse möglich, Datenherkunftsinformationen zu Purview zu übertragen. Befolgen Sie die Schritte in [Verbinden eines Synapse-Arbeitsbereichs mit Azure Purview](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md). Es können auch mehrere Azure Synapse-Arbeitsbereiche mit einem einzelnen Azure Purview-Konto verbunden werden, damit eine ganzheitliche Nachverfolgung der Datenherkunft möglich ist.

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>Schritt 2: Führen Sie eine Pipeline im Azure Synapse-Arbeitsbereich aus

Sie können Pipelines mit Copy-Aktivität im Azure Synapse-Arbeitsbereich erstellen. Sie müssen für das Aufzeichnen der Herkunftsdaten keine zusätzliche Konfiguration durchführen. Die Herkunftsdaten werden während der Ausführung der Aktivitäten automatisch aufgezeichnet.

### <a name="step-3-monitor-lineage-reporting-status"></a>Schritt 3: Überwachen Sie den Status der Datenherkunftsberichte

Nachdem Sie die Azure Synapse-Pipeline ausgeführt haben, können Sie in der Überwachungsansicht der Synapse-Pipeline den Status der Datenherkunftsberichte überprüfen. Klicken Sie dazu auf die unten gezeigte Schaltfläche für den **Datenherkunftsstatus**. Dieselben Informationen sind ebenfalls in der JSON-Aktivitätsausgabe zu finden -> Abschnitt `reportLineageToPurvew`.

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Überwachen Sie den Status der Datenherkunftsberichte in der Pipeline-Überwachungsansicht.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Schritt 4: Anzeigen von Herkunftsinformationen in Ihrem Purview-Konto

In Ihrem Purview-Konto können Sie Ressourcen durchsuchen und den Typ „Azure Synapse Analytics“ auswählen. Sie können Data Catalog auch mit Stichwörtern durchsuchen.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Browse the Azure Synapse assets in Purview."

Wählen Sie Synapse-Konto -> Pipeline -> Aktivität aus, um die Herkunftsinformationen anzuzeigen.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="Durchsuchen der Datenherkunft von Azure Synapse-Pipelines in Purview" lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::.

## <a name="next-steps"></a>Nächste Schritte

[Benutzerhandbuch zur Katalogherkunft](catalog-lineage-user-guide.md)

[Azure Data Share-Datenherkunft](how-to-link-azure-data-share.md)
