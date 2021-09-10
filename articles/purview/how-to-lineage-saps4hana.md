---
title: Metadaten und Datenherkunft aus SAP S/4HANA
description: In diesem Artikel wird die Extraktion der Datenherkunft aus der Quelle „SAP S/4HANA“ beschrieben.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 52ae0f629bc863f604133e3c0f0e3c6256556a89
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340444"
---
# <a name="how-to-get-lineage-from-sap-s4hana-into-azure-purview"></a>Abrufen der Datenherkunft aus SAP S/4HANA in Azure Purview

Dieser Artikel erläutert die Datenherkunftsaspekte für die Quelle „SAP S/4HANA“ in Azure Purview. Die Voraussetzung zum Anzeigen der Datenherkunft in Purview für SAP S/4HANA ist die [Überprüfung Ihrer SAP S/4HANA-Artefakte](../purview/register-scan-saps4hana-source.md). 

## <a name="lineage-of-sap-s4hana-artifacts-in-azure-purview"></a>Datenherkunft von SAP S/4HANA-Artefakten in Azure Purview

Benutzer können SAP S/4HANA-Artefakte nach Namen, Beschreibung oder anderen Details suchen, um relevante Ergebnisse anzuzeigen. Auf der Registerkarte mit der Ressourcenübersicht und Eigenschaften werden die grundlegenden Details wie Beschreibung, Eigenschaften und andere Informationen angezeigt. Auf der Registerkarte „Datenherkunft“ werden Ressourcenbeziehungen zwischen SAP S/4HANA-Tabellen und -Sichten angezeigt. Aus diesem Grund verfügen SAP S/4HANA-Sichten über Tabellendaten zur Datenherkunft. 

Die abgeleitete Datenherkunft ist auch auf Spaltenebene verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Data Lineage in Azure Purview](catalog-lineage-user-guide.md)
- Wenn Sie Daten aus SAP S/4HANA mithilfe von ADF in Azure verschieben, können wir die Datenherkunft im Rahmen der Laufzeit der Datenverschiebung nachverfolgen – [Verbinden von Azure Data Factory und Azure Purview](how-to-link-azure-data-factory.md).
