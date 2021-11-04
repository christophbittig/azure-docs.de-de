---
title: Metadaten und Datenherkunft aus Teradata
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus der Quelle „Teradata“ beschrieben.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 047405fea177cb8ba1365a1329dd6a682f1662fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428765"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>Abrufen der Datenherkunft aus Teradata in Azure Purview

Dieser Artikel erläutert die Aspekte der Datenherkunft der Quelle „Teradata“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für Teradata ist die [Überprüfung Ihrer Teradata-Artefakte](../purview/register-scan-teradata-source.md). 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Herkunft von Teradata-Artefakten in Azure Purview

Benutzer können Teradata-Artefakte nach Name, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen Teradata-Tabellen und gespeicherten Prozeduren sowie zwischen Teradata-Tabellen und -Sichten gezeigt. 

Daher unterstützen wir Herkunft für Datentransformationen, die in gespeicherten Prozeduren und Transformationen in Sichten von Teradata-Tabellen stattfinden. Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="Screenshot, der zeigt, wie Datenherkunft für Teradata gerendert wird." lightbox="./media/how-to-lineage-teradata/lineage.png":::

Im obigen Screenshot ist **customerView** eine Teradata-Sicht, die anhand der Teradata-Tabelle **test_customer** erstellt wurde. Außerdem ist **Return_DataSet** eine gespeicherte Prozedur, die die Teradata-Tabelle **test_customer** verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- Wenn Sie Daten aus Teradata mithilfe von ADF in Azure verschieben, können wir die Datenherkunft als Teil der Laufzeit der Datenverschiebung nachverfolgen – [Verbinden von Azure Data Factory und Azure Purview](how-to-link-azure-data-factory.md)
