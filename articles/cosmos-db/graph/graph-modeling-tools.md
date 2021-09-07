---
title: Datenmodellierungstools von Drittanbietern für Azure Cosmos DB-Graphdaten
description: In diesem Artikel werden verschiedene Tools zum Entwerfen des Graphdatenmodells beschrieben.
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: eacebb55cbf4d6254d6688a95dba8ae638838cf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339044"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>Datenmodellierungstools von Drittanbietern für Azure Cosmos DB-Graphdaten

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Das Datenmodell muss nicht nur entworfen, sondern auch gepflegt werden. Hier finden Sie einige visuelle Entwurfstools von Drittanbietern, die Sie beim Entwerfen und Pflegen des Graphdatenmodells unterstützen.

> [!IMPORTANT] 
> Die Lösungen in diesem Artikel wurden lediglich zu Informationszwecken zusammengestellt und gehören dem jeweiligen Besitzer. Benutzer sollten die Lösungen umfassend evaluieren und die am besten geeignete Lösung auswählen.

## <a name="hackolade"></a>Hackolade

Hackolade ist ein Datenmodellierungs- und Schemaentwurfstool für NoSQL-Datenbanken. Es verfügt über ein Datenmodellierungsstudio, das Sie bei der Verwaltung von Schemas für ruhende und übertragene Daten unterstützt.

### <a name="how-it-works"></a>Funktionsweise
Dieses Tool ermöglicht die Datenmodellierung von Vertices/Edges und deren Eigenschaften.  Es unterstützt mehrere Anwendungsfälle. Hierzu zählen beispielsweise folgende:
-   Beginnen Sie mit einer leeren Seite, und gehen Sie gedanklich verschiedene Optionen für die grafische Erstellung Ihres Cosmos DB-Gremlin-Modells durch.  Entwickeln Sie dann mittels Forward-Engineering das Modell für Ihre Azure-Instanz, um das Ergebnis zu bewerten und das Modell weiterzuentwickeln.  Und das alles, ohne eine einzelne Codezeile zu schreiben.
-   Untersuchen Sie mittels Reverse-Engineering die Struktur eines vorhandenen Graphen in Azure, damit Sie Ihren Graphen auch effektiv abfragen können.  Reichern Sie dann das Datenmodell mit Beschreibungen, Metadaten und Einschränkungen an, um eine Dokumentation zu erstellen. Es unterstützt das HTML-, Markdown- und PDF-Format sowie Feeds für Unternehmensdatengovernance oder Wörterbuchsysteme.
-   Migrieren Sie mittels Denormalisierung von Datenstrukturen von einer relationalen Datenbank zu NoSQL.
-   Implementieren Sie über eine Befehlszeilenschnittstelle eine CI/CD-Pipelineintegration.
-   Profitieren Sie von Zusammenarbeit und Versionsverwaltung mit Git.
-   Und vieles mehr...

### <a name="sample"></a>Beispiel

Die Animation in Abbildung 2 zeigt Reverse-Engineering und die Extraktion von Entitäten aus einem DBMS. Hackolade findet dann Beziehungen aus Fremdschlüsselbeziehungen, und anschließend folgen Änderungen.

Beispiel-DDL für eine Quelle als SQL Server finden Sie [hier](https://github.com/Azure-Samples/northwind-ddl-sample/nw.sql).   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="Graphdiagramm":::
**Abbildung 1:** Graphdiagramm (extrahiertes Graphdatenmodell)

Nach der Änderung des Datenmodells kann das Tool das Gremlin-Skript generieren. Dieses Skript kann ein benutzerdefiniertes Cosmos DB-Indexskript enthalten, um die Verwendung optimaler Indizes sicherzustellen. Der gesamte Ablauf wird in Abbildung 2 dargestellt.

Die folgende Abbildung zeigt das Reverse-Engineering von RDBMS und Hackolade in Aktion: :::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="Hackolade in Aktion":::

**Abbildung 2:** Hackolade in Aktion (Konvertierung des Datenmodells von SQL zu Gremlin)
### <a name="useful-links"></a>Nützliche Links 
-   [Kostenlose 14-Tage-Testversion herunterladen](https://hackolade.com/download.html)
-   [Demo planen](https://c.x.ai/pdesmarets)
-  [Weitere Datenmodelle erhalten](https://hackolade.com/samplemodels.html#cosmosdb)
-  [Dokumentation zu Hackolade](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>Nächste Schritte
- [Visualisieren der Daten](/graph-visualization)
