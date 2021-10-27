---
title: Infosys-Lösung für die Rückverfolgbarkeit von Lieferketten mithilfe der Azure Cosmos DB-Gremllin-API
description: Die von Infosys implementierte Graphlösung für die Rückverfolgbarkeit von Lieferketten verwendet die Azure Cosmos DB-Gremlin-API und weitere Azure-Dienste. Sie bietet eine Nach- und Rückverfolgungsfunktion für die globale Lieferkette von Endprodukten.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 3dacf188e0a3fbf901a97ff2125788f080d6532e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003892"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-gremlin-api"></a>Lösung für die Rückverfolgbarkeit von Lieferketten mithilfe der Azure Cosmos DB-Gremllin-API

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Dieser Artikel bietet eine Übersicht über [von Infosys implementierte Graphlösungen für die Rückverfolgbarkeit](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview). Diese Lösungen verwenden die Azure Cosmos DB-Gremlin-API und weitere Azure-Funktionen, um eine Nach- und Rückverfolgungsfunktion für die globale Lieferkette von Endprodukten bereitzustellen.

In diesem Artikel lernen Sie Folgendes:

* Was bedeutet Rückverfolgbarkeit im Kontext einer Lieferkette?
* Architektur einer globalen Rückverfolgbarkeitslösung, die mithilfe von Azure-Funktionen bereitgestellt wird.  
* Wie hilft die Azure Cosmos DB-Graphdatenbank bei komplexen Beziehungen zwischen Rohstoffen und Endprodukten in einer globalen Lieferkette?
* Wie helfen Ihnen die Azure-Integrationsplattformdienste wie API Management und Event Hub bei der Integration unterschiedlicher Anwendungsökosysteme für Lieferketten?
* Wie können Sie Hilfe von Infosys erhalten, um diese Lösung für Ihre Anforderungen an die Rückverfolgbarkeit zu verwenden?

## <a name="overview"></a>Übersicht

In Lieferketten der Lebensmittelindustrie bedeutet die Rückverfolgbarkeit von Produkten die Fähigkeit, Produkte in der gesamten Lieferkette und während des gesamten Produktlebenszyklus nach- und rückverfolgen zu können. Die Lieferkette umfasst Beschaffung, Herstellung und Auslieferung. Die Rückverfolgbarkeit ist von entscheidender Bedeutung für die Lebensmittelsicherheit, die Marke und die Einhaltung gesetzlicher Vorschriften. In der Vergangenheit konnten einige Organisationen Produkte in ihrer Lieferkette nicht effektiv nach- und rückverfolgen, was zu kostspieligen Rückrufen, Strafzahlungen und sogar zur Gefährdung der Gesundheit von Verbrauchern führte. Die Lösungen für die Rückverfolgbarkeit mussten die Anforderungen der Datenharmonisierung und der Datenerfassung mit unterschiedlicher Geschwindigkeit und Richtigkeit erfüllen und vor allem dem Inventurzyklus folgen – Zielsetzungen, die mit herkömmlichen Plattformen nicht zu erreichen waren.

Die Rückverfolgungslösung von Infosys, die mit Azure-Funktionen wie Anwendungs-, Integrations- und Datenbankdiensten entwickelt wurde, bietet wesentliche Funktionen für Folgendes:

* Herstellen einer Verbindung mit Fabriken, Warenlagern und Verteilzentren.
* Erfassen/Verarbeiten paralleler Lagerbewegungsereignisse.
* Ein Wissensdiagramm, das Verbindungen zwischen Rohstoffen, Chargen, Paletten mit Endprodukten, der Überordnungs-/Unterordnungsbeziehung zwischen Paletten auf mehreren Ebenen und der Warenbewegung zeigt.
* Benutzerportal mit einer Suchfunktion, die von der Platzhaltersuche bis zur Suche nach bestimmten Schlüsselwörtern reicht.
* Identifizieren der Auswirkungen eines Qualitätsvorfalls, z. B. betroffene Rohstoffchargen, betroffene Paletten, Standort der Paletten.
* Möglichkeit, den Verlauf von Ereignissen übergreifend für mehrere Märkte zu erfassen, einschließlich Informationen für den Produktrückruf.

## <a name="solution-architecture"></a>Lösungsarchitektur

Die Rückverfolgbarkeit der Lieferkette weist in aller Regel Muster bei der Erfassung von Palettenbewegungen, der Weitergabe von Qualitätsvorfällen und der Rückverfolgbarkeit/Analyse von Lagerdaten auf. Zunächst müssen diese Systeme sehr große Datenmengen aus Verwaltungssystemen von Fabriken und Warenlagern erfassen, die sich über mehrere geografische Regionen erstrecken. Als Nächstes verarbeiten und analysieren diese Systeme Streamingdaten, um komplexe Beziehungen zwischen Rohstoffen, Produktionschargen und Paletten mit Endprodukten sowie komplexe Beziehungen zwischen übergeordneten und untergeordneten Paletten (Umpacken/Neupacken) abzuleiten. Anschließend muss das System Informationen zu den komplexen Beziehungen zwischen Rohstoffen, Endprodukten und Paletten speichern, die für die Rückverfolgbarkeit erforderlich sind. Ein Benutzerportal mit Suchfunktion ermöglicht Benutzern die Rück- und Nachverfolgung von Produkten im Lieferkettennetzwerk. Diese Dienste ermöglichen eine End-to-End-Lösung für die Rückverfolgbarkeit, die cloudnative, API-First- und datenbasierte Funktionen unterstützt.

Microsoft Azure bietet umfangreiche Dienste, die bei Anwendungsfällen der Nachverfolgbarkeit helfen können, z. B. Azure Cosmos DB, Azure Event Hubs, Azure API Management, Azure App Service, Azure SignalR, Azure Synapse Analytics und Power BI.

Die Rückverfolgungslösung von Infosys bietet eine vorgefertigte Lösung, mit der Sie Ihre Rück- und Nachverfolgungsfunktionen verbessern können. Die folgende Abbildung zeigt die Architektur, die für diese Lösung zur Rückverfolgbarkeit verwendet wird:

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="Lösungsarchitektur für die Rückverfolgbarkeit in Lieferketten" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

Die verschiedenen in dieser Architektur verwendeten Azure-Dienste helfen bei folgenden Aufgaben:

* Mit Azure Cosmos DB können Sie die Leistung elastisch hoch- oder herunterskalieren. Mit der Gremlin-API können Sie komplexe Beziehungen zwischen Rohstoffen, Endprodukten und Warenlagern erstellen und abfragen.
* Azure API Management stellt externen Logistikdienstleistern und Lagerverwaltungssystemen APIs für Lagerbewegungsereignisse bereit.  
* Azure Event Hub bietet die Möglichkeit, eine große Anzahl gleichzeitiger Ereignisse aus Lagerverwaltungssystemen und von externen Logistikdienstleistern zur weiteren Verarbeitung zu erfassen.
* Azure Functions-Apps verarbeiten Ereignisse und erfassen Daten mithilfe der Gremlin-API in Azure Cosmos DB.
* Mit Azure Search können Benutzer komplexe Such- und Filtervorgänge für Paletteninformationen durchführen.
* Azure Databricks liest den Änderungsfeed und erstellt Modelle in Synapse Analytics für die Self-Service-Berichterstellung für Benutzer in Power BI.
* Azure-Web-App und der App Service Plan ermöglichen Ihnen die Bereitstellung des Benutzerportals.
* Ein Azure Storage-Konto speichert archivierte Daten für langfristige gesetzliche Anforderungen.

## <a name="graph-db-and-its-data-design"></a>Graphdatenbank und zugehöriger Datenentwurf

Die Produktion und Verteilung von Waren erfordert die Verwaltung komplexer und dynamischer Beziehungen.  Ein adaptives Datenmodell des Rückverfolgbarkeitsgraphen ermöglicht das Speichern solcher Beziehungen, beginnend mit dem Eingang von Rohstoffen, der Herstellung der Endprodukte in einer Fabrik, dem Transport in verschiedene Warenlager in der Lieferkette und schließlich der Auslieferung an die Warenlager der Kunden. Eine allgemeine Visualisierung des Prozesses sieht wie die folgende Abbildung aus:

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="Visualisierung des Datenentwurfs für eine Lieferkette" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

Das obige Diagramm zeigt eine allgemeine, vereinfachte Ansicht eines komplexen Lieferkettenprozesses. Wenn Sie jedoch wichtige Informationen zu Lagerbewegungen aus Fabriken und Warenlagern in Echtzeit abrufen können, können Sie einen komplexeren Graphen erstellen, der all diese getrennt vorliegenden Informationen miteinander verknüpft.

1. Der Prozess der Rückverfolgbarkeit beginnt, wenn ein Anbieter Rohstoffe an die Fabriken sendet und die Anfangsknoten (Ecken) des Graphen und die Beziehungen (Kanten) erstellt werden.

1. Die Endprodukte (Artikel) werden aus Rohstoffen hergestellt und auf Paletten gepackt.

1. Die Paletten werden dann gemäß Bedarf bzw. Bestellungen von Kunden in Fabrik- oder Marktwarenlager transportiert.

1. Die Warenlager können dem Produktionsunternehmen oder einem externen Logistikdienstleister gehören. Die Paletten werden dann gemäß Kundenbestellungen an verschiedene andere Warenlager geliefert. Je nach Kundenanforderungen werden Teilpaletten oder untergeordnete Paletten entsprechend der Bestellmenge zusammengestellt. Manchmal wird durch Kombination verschiedener Artikel ein ganz neuer Artikel erstellt. Beispielsweise kann in einem Szenario zur Zusammenstellung von Sortenpaketen ein und derselbe Artikel im Rahmen einer Kundenbestellung in kleinere oder größere Mengen auf andere Paletten umgepackt werden.

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="Palettenbeziehung in der Lösung für die Rückverfolgbarkeit von Lieferketten" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. Paletten durchlaufen dann das Lieferkettennetzwerk und erreichen schließlich das Kundenlager. Während dieses Prozesses können die Paletten weiter unterteilt oder mit anderen Paletten kombiniert werden, um neue Paletten zur Erfüllung von Kundenbestellungen zu erzeugen.

1. Schließlich erstellt das System einen komplexen Graphen, der wichtige Beziehungsinformationen für das Management von Qualitätsvorfällen enthält (diese werden in Kürze besprochen).

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="Vollständige Architektur der Objektbeziehung in einer Lieferkette" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. Diese komplexen Beziehungen sind von grundlegender Bedeutung bei Qualitätsvorfällen, da das System die Paletten in der gesamten Lieferkette rück- und nachverfolgen kann. Der Graph und die entsprechenden Verknüpfungen stellen die dafür erforderlichen Informationen bereit. Wenn beispielsweise ein Problem mit einem Rohstoff vorliegt, kann der Graph sämtliche betroffenen Paletten und deren aktuellen Standort anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* [Infosys Traceability Knowledge Graph](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* [Infosys Integrate+ for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-integrate-for-azure)
* Informationen zum Visualisieren von Graphdaten finden Sie unter [Visualisieren der in der Gremlin-API von Azure Cosmos DB gespeicherten Diagrammdaten mit Datenvisualisierungslösungen](graph-visualization-partners.md).
* Informationen zum Modellieren Ihrer Graphdaten finden Sie unter [Datenmodellierungstools von Drittanbietern für Azure Cosmos DB-Graphdaten](graph-modeling-tools.md).
