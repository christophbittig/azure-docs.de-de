---
title: Anzeigen von Wissensspeichern
titleSuffix: Azure Cognitive Search
description: Zeigen Sie einen Wissensspeicher mithilfe des Speicherbrowsers im Azure-Portal an.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: d3a66bacfe746b73d269cd8a36fedf0c6b821cdd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563661"
---
# <a name="view-a-knowledge-store-with-storage-browser"></a>Anzeigen eines Wissensspeichers mit dem Speicherbrowser

Bei einem [Wissensspeicher](knowledge-store-concept-intro.md) handelt es sich um Inhalte, die von einem Azure Cognitive Search-Skillset erstellt und in Azure Storage gespeichert werden. In diesem Artikel wird erläutert, wie Sie mit dem Speicherbrowser im Azure-Portal den Inhalt eines Wissensspeichers anzeigen.

Beginnen Sie mit einem vorhandenen Wissensspeicher, der im [Azure-Portal](knowledge-store-create-portal.md) oder mithilfe der [REST-APIs](knowledge-store-create-rest.md) erstellt wurde. Sowohl mit den exemplarischen Vorgehensweisen für das Portal als auch für REST können Sie Wissensspeicher in Azure Table Storage erstellen.

## <a name="start-storage-browser"></a>Starten des Speicherbrowsers

1. [Öffnen Sie das Speicherkonto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), das Sie zum Erstellen des Wissensspeichers verwendet haben, im Azure-Portal.

1. Wählen Sie im linken Navigationsbereich des Speicherkontos **Speicherbrowser** aus.

## <a name="view-and-edit-tables"></a>Anzeigen und Bearbeiten von Tabellen

1. Erweitern Sie **Tabellen**, um die Tabellenprojektionen Ihres Wissensspeichers zu suchen. Wenn Sie den Wissensspeicher mithilfe des Schnellstarts oder REST-Artikels erstellt haben, enthalten die Tabellen Inhalte im Zusammenhang mit Kundenbewertungen eines europäischen Hotels.

   :::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Screenshot des Speicherbrowsers" border="true":::

1. Wählen Sie eine Tabelle aus der Liste aus, um deren Inhalt anzuzeigen.

1. Wählen Sie oben auf der Seite **Spalten bearbeiten** aus, um die Spaltenreihenfolge neu anzuordnen oder eine Spalte zu löschen.

Im Speicherbrowser können Sie mithilfe der [unterstützten Abfragesyntax](/rest/api/storageservices/Querying-Tables-and-Entities) nur eine Tabelle gleichzeitig abfragen. Wenn Sie tabellenübergreifende Abfragen ausführen möchten, sollten Sie stattdessen Power BI verwenden.

## <a name="next-steps"></a>Nächste Schritte

Verbinden Sie diesen Wissensspeicher mit Power BI, um Visualisierungen zu erstellen, die mehrere Tabellen enthalten.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Power BI](knowledge-store-connect-power-bi.md)
