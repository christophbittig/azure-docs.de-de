---
title: Metadaten und Datenherkunft aus BigQuery
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus BigQuery beschrieben.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 9373544ee3eab185438aafd7dbbdf661b385074e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340354"
---
# <a name="how-to-get-lineage-from-bigquery-into-azure-purview"></a>Abrufen der Datenherkunft aus BigQuery in Azure Purview

In diesem Artikel geht es um die Aspekte der Datenherkunft von BigQuery-Quellen in Azure Purview. Damit die Datenherkunft in Purview für BigQuery angezeigt werden kann, muss das [BigQuery-Projekt gescannt werden](../purview/register-scan-google-bigquery-source.md). 

## <a name="lineage-of-bigquery-artifacts-in-azure-purview"></a>Datenherkunft von Power BI-Artefakten in Azure Purview

Benutzer können BigQuery-Artefakte anhand des Namens, der Beschreibung oder anderer Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen BigQuery-Tabellen und -Sichten angezeigt. Daher verfügen BigQuery-Sichten über Informationen zur Datenherkunft aus Tabellen. 

Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

:::image type="content" source="./media/how-to-lineage-google-bigquery/lineage.png" alt-text="Screenshot, der anzeigt, wie die Datenherkunft für BigQuery dargestellt wird." lightbox="./media/how-to-lineage-google-bigquery/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)
