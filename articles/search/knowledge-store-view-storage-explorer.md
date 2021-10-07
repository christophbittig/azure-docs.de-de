---
title: Anzeigen eines Wissensspeichers mit Storage-Explorer
titleSuffix: Azure Cognitive Search
description: Anzeigen und Analysieren eines Azure Cognitive Search-Wissensspeichers mit dem Storage-Explorer des Azure-Portals
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: d87ec3fa05e1740b5cba7b4230894f1df822ac1f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784625"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Anzeigen eines Wissensspeichers mit Storage-Explorer

Bei einem [Wissensspeicher](knowledge-store-concept-intro.md) handelt es sich um Inhalte, die von einem Azure Cognitive Search-Skillset erstellt und in Azure Storage gespeichert werden. In diesem Artikel wird erläutert, wie Sie mit dem Storage-Explorer im Azure-Portal den Inhalt eines Wissensspeichers anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

Beginnen Sie mit einem vorhandenen Wissensspeicher, der im [Azure-Portal](knowledge-store-create-portal.md) oder mithilfe der [REST-APIs](knowledge-store-create-rest.md) erstellt wurde. Sowohl mit den exemplarischen Vorgehensweisen für das Portal als auch für REST können Sie Wissensspeicher in Azure Table Storage erstellen.

## <a name="start-storage-explorer"></a>Starten Sie den Storage-Explorer.

1. [Öffnen Sie das Speicherkonto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), das Sie zum Erstellen des Wissensspeichers verwendet haben, im Azure-Portal.

1. Klicken Sie im linken Navigationsbereich des Speicherkontos auf **Storage-Explorer**.

## <a name="edit-and-query-tables"></a>Bearbeiten und Abfragen von Tabellen

1. Erweitern Sie die Liste **TABELLEN**, um eine Liste der Azure-Tabellenprojektionen anzuzeigen, die beim Erstellen des Wissensspeichers erstellt wurden. Wenn Sie den Wissensspeicher mithilfe des Schnellstarts oder REST-Artikels erstellt haben, enthalten die Tabellen Inhalte im Zusammenhang mit Kundenbewertungen eines europäischen Hotels.

1. Wählen Sie in der Liste eine Tabelle aus.

   ![Anzeigen von Tabellen im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Anzeigen von Tabellen im Storage-Explorer")

1. Klicken Sie auf **Bearbeiten**, um den Datentyp, Eigenschaftenname oder einzelne Datenwerte in der Tabelle zu ändern.

   ![Bearbeiten einer Tabelle im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Bearbeiten einer Tabelle im Storage-Explorer")

1. Klicken Sie zum Ausführen von Abfragen auf der Befehlsleiste auf **Abfrage**, und geben Sie Ihre Bedingungen ein.

   ![Abfragen einer Tabelle im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Abfragen einer Tabelle im Storage-Explorer")

Im Storage-Explorer können Sie mithilfe der [unterstützten Abfragesyntax](/rest/api/storageservices/Querying-Tables-and-Entities) nur eine Tabelle gleichzeitig abfragen. Wenn Sie tabellenübergreifende Abfragen ausführen möchten, sollten Sie stattdessen Power BI verwenden.

## <a name="next-steps"></a>Nächste Schritte

Verbinden Sie diesen Wissensspeicher mit Power BI, um Visualisierungen zu erstellen, die mehrere Tabellen enthalten.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Power BI](knowledge-store-connect-power-bi.md)
