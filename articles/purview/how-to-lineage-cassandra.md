---
title: Metadaten und Datenherkunft aus Cassandra
description: In diesem Artikel wird die Extrahierung der Datenherkunft aus der Quelle „Cassandra“ beschrieben.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: a46649187403c5825f580da167f9fe77dc9ba210
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453324"
---
# <a name="how-to-get-lineage-from-cassandra-into-azure-purview"></a>Abrufen der Datenherkunft aus Cassandra in Azure Purview

Dieser Artikel erläutert die Aspekte der Datenherkunft der Quelle „Cassandra“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für Cassandra ist die [Überprüfung Ihres Cassandra-Servers](../purview/register-scan-cassandra-source.md). 

## <a name="lineage-of-cassandra-artifacts-in-azure-purview"></a>Herkunft von Cassandra-Artefakten in Azure Purview

Benutzer können Cassandra-Artefakte nach Name, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen Cassandra-Tabellen und materialisierten Sichten gezeigt. Aus diesem Grund verfügen materialisierte Cassandra-Sichten über Informationen zur Datenherkunft aus Tabellen. 

Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

:::image type="content" source="./media/how-to-lineage-cassandra/lineage.png" alt-text="Screenshot, der zeigt, wie Datenherkunft für Cassandra gerendert wird." lightbox="./media/how-to-lineage-cassandra/lineage.png":::


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Verknüpfen Sie Azure Data Factory, um die automatisierte Lineage zu pushen](how-to-link-azure-data-factory.md)
