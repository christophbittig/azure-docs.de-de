---
title: Metadaten und Datenherkunft aus Erwin
description: In diesem Artikel wird die Extraktion der Datenherkunft aus der Erwin-Quelle beschrieben.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356082"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>Abrufen der Datenherkunft aus Erwin in Azure Purview

In diesem Artikel werden die Datenherkunftsaspekte der Erwin-Quelle in Azure Purview beschrieben. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für Erwin ist die [Überprüfung Ihrer Erwin-Artefakte](../purview/register-scan-erwin-source.md). 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Herkunft von Erwin-Artefakten in Azure Purview

Benutzer können anhand des Namens, der Beschreibung oder anderer Details nach Erwin-Artefakten suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und den Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen Erwin-Tabellen und -Sichten angezeigt. Aus diesem Grund verfügen Erwin-Sichten über Informationen zur Datenherkunft aus Tabellen. 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="Screenshot: Rendering von Herkunftsdaten für Erwin" lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)
