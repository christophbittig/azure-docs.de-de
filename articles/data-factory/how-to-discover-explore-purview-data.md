---
title: Ermitteln und Erkunden von Daten in ADF mithilfe von Purview
description: Hier erfahren Sie, wie Sie Daten in Azure Data Factory mithilfe von Purview ermitteln und erkunden können.
ms.service: data-factory
ms.topic: conceptual
author: linda33wj
ms.author: jingwang
ms.custom: seo-lt-2019
ms.date: 08/10/2021
ms.openlocfilehash: 037e7fadd84d3b3b7405507a6116ed1e8c0ef59d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427046"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Ermitteln und Erkunden von Daten in ADF mithilfe von Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel registrieren Sie ein Azure Purview-Konto bei einer Data Factory. Dank dieser Verbindung können Sie Azure Purview-Ressourcen ermitteln und über ADF-Funktionen damit interagieren. 

Sie können in ADF die folgenden Aufgaben ausführen: 
- Suchen von Purview-Objekten anhand von Schlüsselwörtern mithilfe des Suchfelds im oberen Bereich 
- Verstehen der Daten auf der Grundlage von Metadaten, Herkunft und Anmerkungen 
- Verbinden dieser Daten mit Ihrer Data Factory mit verknüpften Diensten oder Datasets 

## <a name="prerequisites"></a>Voraussetzungen 

- [Azure Purview-Konto](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Verbinden eines Azure Purview-Kontos in Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Verwenden von Azure Purview in Data Factory 

Für die Verwendung von Azure Purview in Data Factory müssen Sie Zugriff auf dieses Purview-Konto haben. Data Factory übergibt Ihre Purview-Berechtigung. Wenn Sie z. B. über eine Rolle mit Kuratorberechtigung verfügen, können Sie von Azure Purview gescannte Metadaten bearbeiten. 

### <a name="data-discovery-search-datasets"></a>Datenermittlung: Durchsuchen von Datasets 

Zum Ermitteln von Daten, die von Azure Purview registriert und überprüft wurden, können Sie die Suchleiste oben in der Mitte des Data Factory-Portals verwenden. Stellen Sie sicher, dass Sie Azure Purview auswählen, um nach allen ihren Organisationsdaten zu suchen. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Screenshot zur Ausführung für Datasets.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Aktionen, die Sie für Datasets mit Data Factory-Ressourcen ausführen können 
Sie können einen verknüpften Dienst, ein Dataset oder einen Datenfluss für die Daten, die Sie nach Azure Purview durchsuchen, direkt erstellen.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Screenshot, der zeigt, wie Sie einen verknüpften Dienst, ein Dataset oder einen Datenfluss für die Daten, die Sie nach Azure Purview durchsuchen, direkt erstellen können.":::

##  <a name="nextsteps"></a>Nächste Schritte 

[Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](turorial-push-lineage-to-purview.md)

[Verbinden eines Azure Purview-Kontos in Data Factory](connect-data-factory-to-azure-purview.md) 

[Suchen im Azure Purview Data Catalog](../purview/how-to-search-catalog.md)