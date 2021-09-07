---
title: Anzeigen eines Wissensspeichers mit Storage-Explorer
titleSuffix: Azure Cognitive Search
description: Anzeigen und Analysieren eines Azure Cognitive Search-Wissensspeichers mit dem Storage-Explorer des Azure-Portals
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346757"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Anzeigen eines Wissensspeichers mit Storage-Explorer

Ein [Wissensspeicher](knowledge-store-concept-intro.md) wird durch ein Skillset definiert und in Azure Storage gespeichert. In diesem Artikel erfahren Sie, wie Sie mit dem Storage-Explorer im Azure-Portal die Inhalte eines Wissensspeichers anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

+ Erstellen Sie einen Wissensspeicher im [Azure-Portal](knowledge-store-create-portal.md) oder in [Postman und den REST-APIs](knowledge-store-create-rest.md).

+ Sie benötigen außerdem den Namen des Azure Storage-Kontos, das Sie zum Erstellen des Wissensspeichers verwendet haben, sowie den Zugriffsschlüssel aus dem Azure-Portal.

## <a name="start-storage-explorer"></a>Starten Sie den Storage-Explorer.

1. [Öffnen Sie das Speicherkonto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), das Sie zum Erstellen des Wissensspeichers verwendet haben, im Azure-Portal.

1. Klicken Sie im linken Navigationsbereich des Speicherkontos auf **Storage-Explorer**.

## <a name="view-edit-and-query-tables"></a>Anzeigen, Bearbeiten und Abfragen von Tabellen

Sowohl das Portal als auch exemplarische Vorgehensweisen für REST erstellen einen Wissensspeicher in Table Storage.

1. Erweitern Sie die Liste **TABELLEN**, um eine Liste der Azure-Tabellenprojektionen anzuzeigen, die beim Erstellen des Wissensspeichers erstellt wurden. Die Tabellen sollten Inhalt im Zusammenhang mit Hotelrezensionen enthalten.

1. Wählen Sie eine beliebige Tabelle aus, um die angereicherten Daten, einschließlich Schlüsselbegriffen und Stimmungspunktzahlen, anzuzeigen.

   ![Anzeigen von Tabellen im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Anzeigen von Tabellen im Storage-Explorer")

1. Zum Ändern des Datentyps für einen beliebigen Tabellenwert oder zum Ändern einzelner Werte in der Tabelle klicken Sie auf **Bearbeiten**. Wenn Sie den Datentyp für eine Spalte in einer Tabellenzeile ändern, erfolgt die Änderung für alle Zeilen.

   ![Bearbeiten einer Tabelle im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Bearbeiten einer Tabelle im Storage-Explorer")

1. Klicken Sie zum Ausführen von Abfragen in der Befehlsleiste auf **Abfrage**, und geben Sie Ihre Bedingungen ein.  

   ![Abfragen einer Tabelle im Storage-Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Abfragen einer Tabelle im Storage-Explorer")

## <a name="next-steps"></a>Nächste Schritte

Verbinden Sie diesen Wissensspeicher zur detaillierteren Analyse mit Power BI, oder fahren Sie mit Code fort, und erstellen Sie mit der REST-API und Postman einen anderen Wissensspeicher.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Power BI](knowledge-store-connect-power-bi.md)
