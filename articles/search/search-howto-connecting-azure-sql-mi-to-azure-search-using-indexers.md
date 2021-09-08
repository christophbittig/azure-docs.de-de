---
title: Indexerverbindung mit SQL Managed Instances
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie den öffentlichen Endpunkt, um Verbindungen mit verwalteten SQL-Datenbank-Instanzen von einem Indexer in der kognitiven Azure-Suche zuzulassen.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984892"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>Indexerverbindungen mit Azure SQL Managed Instance über einen öffentlichen Endpunkt

Wenn Sie einen Azure Cognitive Search-Indexer einrichten, der eine Verbindung mit einer verwalteten Azure SQL-Instanz herstellt, müssen Sie als Voraussetzung einen öffentlichen Endpunkt für die verwaltete Instanz aktivieren. Ein Indexer stellt über einen öffentlichen Endpunkt eine Verbindung mit einer verwalteten Instanz her.

Dieser Artikel enthält grundlegende Schritte, darunter das Sammeln von Informationen, die für die Konfiguration der Datenquelle erforderlich sind. Weitere Informationen und Methodiken finden Sie unter [Konfigurieren des öffentlichen Endpunkts in Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md).

## <a name="enable-a-public-endpoint"></a>Aktivieren des öffentlichen Endpunkts

Für eine neue SQL Managed Instance erstellen Sie die Ressource mit der ausgewählten Option **Öffentlichen Endpunkt aktivieren**.

   ![Öffentlichen Endpunkt aktivieren](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Aktivieren des öffentlichen Endpunkts")

Wenn die Instanz bereits vorhanden ist, können Sie den öffentlichen Endpunkt auch auf einer vorhandenen SQL Managed Instance unter **Sicherheit** > **Virtuelles Netzwerk** > **Öffentlicher Endpunkt**  > **Aktivieren** aktivieren.

   ![Aktivieren eines öffentlichen Endpunkts mit verwalteter Instanz – VNET](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Aktivieren des öffentlichen Endpunkts")

## <a name="verify-nsg-rules"></a>Überprüfen von NSG-Regeln

Überprüfen Sie, ob die Netzwerksicherheitsgruppe die richtigen **Eingangssicherheitsregeln** hat, die Verbindungen von Azure-Diensten erlauben.

   ![NSG-Eingangssicherheitsregel](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG-Eingangssicherheitsregel")

## <a name="restrict-inbound-access-to-the-endpoint"></a>Einschränken des Zugriffs auf den Endpunkt

Sie können den eingehenden Zugriff auf den öffentlichen Endpunkt einschränken, indem Sie die aktuelle Regel (`public_endpoint_inbound`) durch die folgenden zwei Regeln ersetzen:

* Eingehenden Zugriff über das `AzureCognitiveSearch`-[Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) zulassen ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)

* Eingehenden Zugriff von der IP-Adresse des Suchdienstanbieters zulassen; kann durch Pingen des vollqualifizierten Domänennamens abgerufen werden (z. B. `<your-search-service-name>.search.windows.net`). ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)

Legen Sie für jede dieser beiden Regeln "PORT" = `3342`, "PROTOCOL" = `TCP`, "DESTINATION" = `Any`, "ACTION" = `Allow` fest.

## <a name="get-public-endpoint-connection-string"></a>Abrufen der Verbindungszeichenfolge für den öffentlichen Endpunkt

Kopieren Sie die Verbindungszeichenfolge, die in der Datenquellenverbindung des Suchindexers verwendet werden soll. Achten Sie darauf, die Verbindungszeichenfolge für den **öffentlichen Endpunkt** zu kopieren (Port 3342, nicht Port 1433).

   ![Verbindungszeichenfolge für den öffentlichen Endpunkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Verbindungszeichenfolge für den öffentlichen Endpunkt")

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Konfiguration abgeschlossen ist, können Sie nun eine [SQL Managed Instance als Indexerdatenquelle](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) angeben.