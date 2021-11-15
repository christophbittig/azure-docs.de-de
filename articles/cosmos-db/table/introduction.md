---
title: Einführung in die Tabellen-API von Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Azure Cosmos DB verwenden können, um riesige Mengen von Schlüssel-Wert-Daten mit geringer Wartezeit mithilfe der Azure-Tabellen-APIs zu speichern und abzufragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/03/2021
ms.author: sngun
ms.openlocfilehash: aa065a4c5cd8100ee9e6cb235555f9aefc910643
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504776"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Einführung in Azure Cosmos DB: Tabelle-API
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Mit [Azure Cosmos DB](introduction.md) wird die Tabellen-API für Anwendungen bereitgestellt, die für Azure Table Storage geschrieben wurden und beispielsweise folgende Premium-Funktionen benötigen:

* [Globale, sofort einsatzbereite Verteilung](../distribute-data-globally.md).
* [Dedizierter Durchsatz](../partitioning-overview.md) weltweit (bei Verwendung von bereitgestelltem Durchsatz).
* Einstellige Latenzzeiten im Millisekundenbereich im 99. Perzentil.
* Garantierte Hochverfügbarkeit.
* Automatische sekundäre Indizierung

[Azure Tables SDKs](https://devblogs.microsoft.com/azure-sdk/announcing-the-new-azure-data-tables-libraries/) sind für .NET, Java, Python, Node.js und Go verfügbar. Diese SDKs können entweder für Table Storage oder Cosmos DB-Tabellen verwendet werden. Anwendungen, die mithilfe der Azure Tables SDKs für Azure Table Storage geschrieben wurden, können ohne Codeänderungen zur Azure Cosmos DB-Tabellen-API migriert werden, um die Premium-Funktionen zu nutzen.

> [!NOTE]
> Der [serverlose Kapazitätsmodus](../serverless.md) ist nun in der Tabellen-API von Azure Cosmos DB verfügbar.

> [!IMPORTANT]
> Das .NET Azure Tables SDK [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) bietet aktuelle Features, die von der Tabellen-API unterstützt werden. Die Azure Tables-Clientbibliothek kann nahtlos auf Azure Table Storage- oder Azure Cosmos DB-Tabellendienstendpunkte ohne Codeänderungen ausgerichtet werden.

## <a name="table-offerings"></a>Tabellenangebote

Wenn Sie derzeit Azure Table Storage verwenden, bietet Ihnen der Wechsel zur Azure Cosmos DB-Tabellen-API folgende Vorteile:

| Funktion | Azure-Tabellenspeicher | Azure Cosmos DB-Tabellen-API |
| --- | --- | --- |
| Latency | Schnell, aber keine Obergrenzen für die Wartezeit. | Wartezeit im einstelligen Millisekundenbereich für Lese- und Schreibvorgänge, unterstützt durch weniger als 10 ms Wartezeit bei Lese- und Schreibvorgängen im 99. Perzentil, bei beliebiger Skalierung weltweit |
| Throughput | Variables Durchsatzmodell. Tabellen verfügen über eine maximale Skalierbarkeit von 20.000 Vorgängen/s. | Hochgradig skalierbar mit einem [dedizierten reservierten Durchsatz pro Tabelle](../request-units.md), der durch SLAs abgedeckt ist. Konten haben keine Obergrenze für den Durchsatz und unterstützen pro Tabelle mehr als 10 Millionen Vorgänge/s. |
| Globale Verteilung | Einzelne Region mit einer optionalen sekundären Leseregion für Hochverfügbarkeit. | [Globale, sofort einsatzbereite Verteilung](../distribute-data-globally.md) für eine beliebige Anzahl von Regionen. Unterstützung von [automatischen und manuellen Failovern](../high-availability.md) zu jeder Zeit und an jedem Ort der Welt. Mehrere Schreibregionen, damit jede Region Schreibvorgänge akzeptieren kann. |
| Indizierung | Nur primärer Index für PartitionKey und RowKey. Keine sekundären Indizes. | Standardmäßig automatische und vollständige Indizierung für alle Eigenschaften, keine Indexverwaltung. |
| Abfrage | Abfrageausführung verwendet Index für Primärschlüssel, andernfalls die Suche. | Abfragen können die automatische Indizierung für Eigenschaften für schnelle Abfragezeiten nutzen. |
| Konsistenz | „Stark“ in der primären Region. „Möglich“ in der sekundären Region. | [Fünf klar definierte Konsistenzebenen](../consistency-levels.md) zur Abstimmung von Verfügbarkeit, Latenz, Durchsatz und Konsistenz basierend auf Ihren Anwendungsanforderungen. |
| Preise | Nutzungsbasiert. | Die verfügbaren Modi lauten [Nutzungsbasiert](../serverless.md) und [Bereitgestellte Kapazität](../set-throughput.md). |
| SLAs | Verfügbarkeit von 99,9 bis 99,99 %, abhängig von der Replikationsstrategie | 99,999 % Leseverfügbarkeit, 99,99 % Schreibverfügbarkeit für ein Konto in einer Region und 99,999 % Schreibverfügbarkeit für Konten in mehreren Regionen. Die Bereiche Verfügbarkeit, Latenz, Durchsatz und Konsistenz sind durch [umfassende SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) abgedeckt. |

## <a name="get-started"></a>Erste Schritte

Erstellen Sie im [Azure-Portal](https://portal.azure.com) ein Azure Cosmos DB-Konto. Fahren Sie anschließend mit dem [Schnellstart für die Table-API per .NET](create-table-dotnet.md) fort.

## <a name="next-steps"></a>Nächste Schritte

Hier einige Hinweise, um Ihnen den Einstieg zu erleichtern:
* [Erstellen einer .NET-Anwendung mit der Table-API](create-table-dotnet.md)
* [Entwickeln mit der Table-API in .NET](tutorial-develop-table-dotnet.md)
* [Abfragen von Tabellendaten mit der Table-API](tutorial-query-table.md)
* [Einrichten der globalen Verteilung von Azure Cosmos DB mithilfe der Table-API](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table .NET Standard SDK](dotnet-standard-sdk.md)
* [Azure Cosmos DB Table .NET SDK](dotnet-sdk.md)
* [Azure Cosmos DB Table Java SDK](java-sdk.md)
* [Azure Cosmos DB Table Node.js SDK](nodejs-sdk.md)
* [Azure Cosmos DB-Table-SDK für Python](python-sdk.md)
