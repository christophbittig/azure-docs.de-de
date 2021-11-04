---
title: Metadaten und Datenherkunft aus SAP ECC
description: In diesem Artikel wird die Extraktion der Datenherkunft aus der Quelle „SAP ECC“ beschrieben.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: fc7c612384d46ed4983e634ec665927cf0bf1002
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463977"
---
# <a name="how-to-get-lineage-from-sap-ecc-into-azure-purview"></a>Abrufen der Datenherkunft aus SAP ECC in Azure Purview

Dieser Artikel erläutert die Datenherkunftsaspekte für die Quelle „SAP ECC“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für SAP ECC ist die [Überprüfung Ihrer SAP ECC-Artefakte](../purview/register-scan-sapecc-source.md). 

## <a name="lineage-of-sap-ecc-artifacts-in-azure-purview"></a>Datenherkunft von SAP ECC-Artefakten in Azure Purview

Benutzer können SAP ECC-Artefakte nach Name, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen SAP ECC-Tabellen und -Sichten gezeigt. Aus diesem Grund verfügen SAP ECC-Sichten über Informationen zur Datenherkunft aus Tabellen. 

Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

:::image type="content" source="./media/how-to-lineage-sapecc/lineage.png" alt-text="Screenshot, der zeigt, wie Datenherkunft für SAP ECC gerendert wird." lightbox="./media/how-to-lineage-sapecc/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- Wenn Sie Daten aus SAP ECC mithilfe von ADF in Azure verschieben, können wir die Datenherkunft als Teil der Laufzeit der Datenverschiebung nachverfolgen – [Verbinden von Azure Data Factory und Azure Purview](how-to-link-azure-data-factory.md)
