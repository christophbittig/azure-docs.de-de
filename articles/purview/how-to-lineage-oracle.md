---
title: Metadaten und Datenherkunft aus Oracle
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus der Quelle „Oracle“ beschrieben.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: 4f790487e910c279723c38e725e6b0ee1549e0bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425062"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>Abrufen der Datenherkunft aus Oracle in Azure Purview

Dieser Artikel erläutert die Aspekte der Datenherkunft der Quelle „Oracle“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für Oracle ist die [Überprüfung Ihrer Oracle-Artefakte](../purview/register-scan-oracle-source.md). 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Herkunft von Oracle-Artefakten in Azure Purview

Benutzer können Oracle-Artefakte nach Name, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte „Asset-Übersicht & Eigenschaften" werden die grundlegenden Details wie Beschreibung, Klassifizierung und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen Oracle-Tabellen und gespeicherten Prozeduren, Sichten und Funktionen gezeigt. 

Daher enthalten Oracle-Sichten derzeit Herkunftsinformationen aus Tabellen, während Funktionen und gespeicherte Prozeduren die Herkunft zwischen Tabelle/Sicht mithilfe von parameter_dataset und stored_procedure_result_set angeben. Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="Screenshot, der zeigt, wie Datenherkunft für Oracle gerendert wird." lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)
