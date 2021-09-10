---
title: Metadaten und Datenherkunft aus Looker
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus der Quelle „Looker“ beschrieben.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: bed8395940643e56d586369fd00341b4c28b27f9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356100"
---
# <a name="how-to-get-lineage-from-looker-into-azure-purview"></a>Abrufen der Datenherkunft aus Looker in Azure Purview

Dieser Artikel erläutert die Aspekte der Datenherkunft der Quelle „Looker“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für Looker ist die [Überprüfung Ihrer Looker-Artefakte](../purview/register-scan-looker-source.md). 

## <a name="lineage-of-looker-artifacts-in-azure-purview"></a>Herkunft von Looker-Artefakten in Azure Purview

Benutzer können Looker-Artefakte nach Name, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und den Eigenschaften werden grundlegende Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen Looker-Layouts und -Sichten angezeigt. Deshalb umfassen Looker-Sichten Informationen zur Datenherkunft aus Tabellen. 

:::image type="content" source="./media/how-to-lineage-looker/lineage.png" alt-text="Screenshot: Rendering von Herkunftsdaten für Looker" lightbox="./media/how-to-lineage-looker/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)
