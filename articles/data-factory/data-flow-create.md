---
title: Erstellen eines Zuordnungsdatenflusses
description: Erfahren Sie, wie Sie einen Datenfluss mit Azure Data Factory Mapping Data Flow erstellen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614509"
---
# <a name="create-azure-data-factory-data-flows"></a>Erstellen von Azure Data Factory-Datenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Mit Zuordnungsdatenflüssen können Sie Daten im großen Stil transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag im Datenfluss-Designer entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie dann Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen.

## <a name="steps-to-create-a-new-data-flow"></a>Schritte zum Erstellen eines neuen Datenflusses

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

Erstellen Sie zuerst im Azure-Portal eine [neue Data Factory (V2)](quickstart-create-data-factory-portal.md). Wählen Sie nach dem Erstellen der neuen Factory im Portal die Kachel **Azure Data Factory Studio öffnen** aus, um Data Factory Studio zu starten.

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="Screenshot: Öffnen von Data Factory Studio im Azure-Portal.":::

Sie können Beispieldatenflüsse aus dem Vorlagenkatalog hinzufügen. Um den Katalog zu durchsuchen, wählen Sie in Data Factory Studio die Registerkarte **Ersteller** aus, und klicken Sie auf das Pluszeichen, um **Pipeline** | **Vorlagenkatalog** auszuwählen.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="Screenshot: Öffnen des Vorlagenkatalogs in Data Factory.":::

Wählen Sie die Kategorie „Datenfluss“ aus, um aus den verfügbaren Vorlagen auszuwählen.

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="Screenshot: nach Datenflüssen gefilterter Vorlagenkatalog.":::

Sie können Datenflüsse auch direkt zu Ihrer Data Factory hinzufügen, ohne eine Vorlage zu verwenden. Wählen Sie in Data Factory Studio die Registerkarte **Ersteller** aus, und klicken Sie auf das Pluszeichen, um **Datenfluss** | **Datenfluss** auszuwählen.  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="Screenshot: direktes Erstellen eines leeren Datenflusses.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Zunächst [erstellen Sie einen neuen Synapse-Arbeitsbereich](../synapse-analytics/quickstart-create-workspace.md) im Azure-Portal. Nach dem Erstellen des Arbeitsbereichs wählen Sie die Kachel **Synapse Studio öffnen** aus, um die Data Factory-Benutzeroberfläche zu starten.
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="Screenshot: Öffnen von Synapse Studio im Azure-Portal.":::

Sie können Beispieldatenflüsse aus dem Vorlagenkatalog hinzufügen.  Wählen Sie zum Durchsuchen des Katalogs in Synapse Studio die Registerkarte **Integrieren** aus, und klicken Sie auf das Pluszeichen, um **Katalog durchsuchen** auszuwählen.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="Screenshot: Öffnen des Vorlagenkatalogs in Data Factory.":::

Filtern Sie nach „Kategorie: Datenfluss“, um aus den verfügbaren Vorlagen auszuwählen.

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="Screenshot: nach Datenflüssen gefilterter Vorlagenkatalog.":::

Sie können Datenflüsse auch direkt zu Ihrem Arbeitsbereich hinzufügen, ohne eine Vorlage zu verwenden. Wählen Sie in Synapse Studio die Registerkarte **Integrieren** aus, und klicken Sie auf das Pluszeichen, um **Pipeline** auszuwählen.  Erweitern Sie dann in Ihrer Pipeline den Aktivitätenabschnitt **Verschieben und umwandeln**, und ziehen Sie einen **Datenfluss** auf die Canvas für die Pipeline.

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="Screenshot: direktes Erstellen einer leeren Pipeline.":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="Screenshot: direktes Hinzufügen eines leeren Datenflusses zu einer Pipeline.":::

---

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Transformieren von Daten mit Zuordnungsdatenflüssen](tutorial-data-flow.md)
* Beginnen Sie die Erstellung Ihrer Datentransformation mit einer [Quelltransformation](data-flow-source.md).
