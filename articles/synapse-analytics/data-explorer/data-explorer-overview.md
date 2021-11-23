---
title: Was ist Azure Synapse Data Explorer? (Vorschau)
description: Dieser Artikel enthält eine Einführung in Data Explorer in Azure Synapse Analytics sowie in die verschiedenen Einsatzszenarien.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: 527bcb71f622cfecba778a496df68476c8d6be3b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342640"
---
# <a name="what-is-azure-synapse-data-explorer-preview"></a>Was ist Azure Synapse Data Explorer? (Vorschau)

Azure Synapse Data Explorer bietet interaktive Abfragen, um Erkenntnisse aus Protokoll- und Telemetriedaten zu gewinnen. Um vorhandene SQL- und Apache Spark-Analyseruntime-Engines zu ergänzen, ist die Analyseruntime von Data Explorer für effiziente Protokollanalysen mit leistungsstarker Indizierungstechnologie optimiert, um Freitextdaten und halbstrukturierte Daten automatisch zu indizieren, die häufig in Telemetriedaten zu finden sind.

:::image type="content" border="false" source="../media/overview-what-is/synapse-architecture.png" alt-text="Diagramm der Azure Synapse-Architektur.":::

## <a name="what-makes-azure-synapse-data-explorer-unique"></a>Weshalb ist Azure Synapse Data Explorer so besonders?

* **Einfache Erfassung**: Data Explorer bietet integrierte Integrationen ohne oder mit nur geringem Programmieraufwand, Datenerfassung mit hohem Durchsatz und Zwischenspeicherung von Daten aus Echtzeitquellen. Daten können aus Quellen wie Event Hub, Kafka, Azure Data Lake, Open-Source-Agents wie Fluentd/Fluent Bit und einer Vielzahl von Cloud- und lokalen Datenquellen erfasst werden.

* **Keine komplexe Datenmodellierung**: Mit Data Explorer müssen keine komplexen Datenmodelle erstellt werden, und es müssen auch keine komplexen Skripts erstellt werden, um Daten zu transformieren, bevor sie genutzt werden.
* **Keine Indexwartung**: Es sind keine Wartungsaufgaben zur Optimierung der Daten in Bezug auf die Abfrageleistung und keine Wartung von Indizes erforderlich. Mit Data Explorer sind alle Rohdaten sofort verfügbar, sodass Sie Hochleistungsabfragen und Abfragen mit hoher Parallelität auf Ihre Streaming- und persistenten Daten anwenden können. Sie können mithilfe dieser Abfragen Dashboards und Warnungen nahezu in Echtzeit erstellen und operative Analysedaten mit dem Rest der Datenanalyseplattform verbinden.
* **Demokratisierung der Datenanalyse**: Data Explorer demokratisiert Big-Data-Analysen im Self-Service-Verfahren mit der intuitiven Kusto Query Language (KQL), die die Ausdrucksmöglichkeiten und die Leistungsfähigkeit von SQL mit der Einfachheit von Excel kombiniert. KQL ist in hohem Maße für die Abfrage von Telemetrie- und Zeitreihendaten optimiert. Die Sprache nutzt die erstklassige Textindizierungstechnologie von Data Explorer für eine effiziente Freitext- und Regex-Suche sowie umfassende Analysemöglichkeiten für die Abfrage von Ablaufverfolgungs-, Text- und semistrukturierten JSON-Daten einschließlich Arrays und geschachtelter Strukturen. KQL bietet eine verbesserte Unterstützung für Zeitreihen zum Erstellen, Bearbeiten und Analysieren mehrerer Zeitreihen mit integrierter Python-Ausführungsunterstützung für die Bewertung von Modellen.
* **Bewährte Technologie im Petabyte-Maßstab**: Data Explorer ist ein verteiltes System mit Compute- und Speicherressourcen, die unabhängig voneinander skaliert werden können und Analysen von Daten im Gigabyte- oder Petabytebereich ermöglichen.
* **Integriert**: Azure Synapse Analytics bietet datenübergreifende Interoperabilität zwischen Data Explorer-, Apache Spark- und SQL-Engines und ermöglicht Datentechnikern, -wissenschaftlern und -analysten einen einfachen und sicheren Zugriff auf dieselben Daten im Data Lake und die Zusammenarbeit daran.

## <a name="when-to-use-azure-synapse-data-explorer"></a>Einsatzmöglichkeiten von Azure Synapse Data Explorer

Verwenden Sie Data Explorer als Datenplattform zum Erstellen von Protokollanalyse- und IoT-Analyselösungen nahezu in Echtzeit für Folgendes:

* Konsolidieren und Korrelieren von Protokoll- und Ereignisdaten aus lokalen, Cloud- und Drittanbieterdatenquellen

* Beschleunigen Ihrer KI-Ops-Journey (Mustererkennung, Anomalieerkennung, Vorhersagen usw.)
* Ersetzen infrastrukturbasierter Protokollsuchlösungen, um Kosten zu sparen und die Produktivität zu steigern
* Entwickeln von IoT-Analyselösungen für Ihre IoT-Daten
* Entwerfen von SaaS-Analyselösungen, um Ihrer internen und externen Kundschaft Dienste bereitzustellen

## <a name="data-explorer-pool-architecture"></a>Architektur von Data Explorer-Pools

Data Explorer-Pools implementieren eine aufskalierbare Architektur, in der die Compute- und Speicherressourcen getrennt sind. Auf diese Weise können Sie jede Ressource unabhängig skalieren und z. B. mehrere schreibgeschützte Computeressourcen auf dieselben Daten anwenden. Data Explorer-Pools bestehen aus einer Reihe von Computeressourcen, in denen die Engine ausgeführt wird, die für die automatische Indizierung, Komprimierung, Zwischenspeicherung und Bereitstellung verteilter Abfragen zuständig ist. Sie verfügen auch über eine zweite Gruppe von Computeressourcen, in der der Datenverwaltungsdienst ausgeführt wird, der für Hintergrundsystemaufträge sowie die verwaltete und in der Warteschlange gespeicherte Datenerfassung zuständig ist. Alle Daten werden in verwalteten Blobspeicherkonten mithilfe eines komprimierten Spaltenformats persistent gespeichert.

Data Explorer-Pools unterstützen ein umfangreiches Ökosystem für die Erfassung von Daten mithilfe von Connectors, SDKs, REST-APIs und anderen verwalteten Funktionen. Sie bieten verschiedene Möglichkeiten, Daten für Ad-hoc-Abfragen, Berichte, Dashboards, Warnungen, REST-APIs und SDKs zu nutzen.

:::image type="content" source="media/data-explorer-overview/data-explorer-architecture.png" alt-text="Architektur von Data Explorer-Pools":::

Es gibt viele einzigartige Funktionen, aufgrund derer Data Explorer die beste Analyse-Engine für Protokoll- und Zeitreihenanalysen in Azure ist. Wenn Sie mehr über die Funktionsweise von Data Explorer erfahren möchten, lesen Sie das [Whitepaper zu Azure Data Explorer](https://azure.microsoft.com/resources/azure-data-explorer/).

In den folgenden Abschnitten werden die wichtigsten Alleinstellungsmerkmale vorgestellt.

### <a name="free-text-and-semi-structured-data-indexing-enables-near-real-time-high-performance-and-high-concurrent-queries"></a>Die Indizierung von Freitext- und semistrukturierten Daten ermöglicht nahezu in Echtzeit eine hohe Leistung und viele gleichzeitige Abfragen.

Data Explorer indiziert semistrukturierte Daten (JSON) und unstrukturierte Daten (Freitext), wodurch sehr leistungsfähige Abfragen dieser Art von Daten möglich sind. Standardmäßig wird jedes Feld während der Datenerfassung indiziert, wobei die Option besteht, eine Richtlinie für eine Codierung auf untergeordneter Ebene zu verwenden, um den Index für bestimmte Felder zu optimieren oder zu deaktivieren. Der Indexumfang ist ein einzelner Shard.

Die Implementierung des Index hängt wie folgt vom Typ des Felds ab:

| Feldtyp | Implementierung der Indizierung |
| -- | -- |
| **String** | Die Engine erstellt für Werte in Zeichenfolgenspalten einen invertierten Ausdrucksindex. Jeder Zeichenfolgenwert wird analysiert und in normalisierte Begriffe aufgeteilt. Zu jedem Begriff wird eine geordnete Liste logischer Positionen aufgezeichnet, die Ordnungszahlen für Datensätze enthält. Die resultierende sortierte Liste mit Begriffen und ihrer zugeordneten Positionen werden als unveränderliche B-Struktur gespeichert. |
| **Numerisch**<br />**Datetime**<br />**TimeSpan** | Die Engine erstellt einen einfachen bereichsbasierten Vorwärtsindex. Der Index zeichnet die Minimal- und Maximalwerte für jeden Block, für eine Gruppe von Blöcken und für die gesamte Spalte innerhalb des Datenshards auf. |
| **Dynamisch** | Der Indizierungsprozess erfasst alle „atomischen“ Elemente innerhalb des dynamischen Werts, wie z. B. Eigenschaftsnamen, Werte und Arrayelemente, und leitet sie an den Index-Generator weiter. Dynamische Felder haben denselben invertierten Begriffsindex wie Zeichenfolgenfelder. |

Diese effizienten Indizierungsfunktionen ermöglichen Data Explorer, die Daten nahezu in Echtzeit für Abfragen mit hoher Leistung und Parallelität verfügbar zu machen. Das System optimiert automatisch Datenshards, um die Leistung weiter zu steigern.

### <a name="kusto-query-language"></a>Kusto-Abfragesprache

Für KQL gibt es eine große, wachsende Community, die Azure Monitor Log Analytics und Application Insights, Microsoft Sentinel, Azure Data Explorer und andere Microsoft-Angebote schnell eingeführt hat. Die Sprache bietet eine leicht verständliche Syntax und ermöglicht einen reibungslosen Übergang von einfachen Einzeilern zu komplexen Datenverarbeitungsabfragen. Dadurch bietet Data Explorer eine umfangreiche Intellisense-Unterstützung und Vielzahl von Sprachkonstrukten und integrierten Funktionen für Aggregationen, Zeitreihen und Benutzeranalysen, die in SQL nicht verfügbar sind, um Telemetriedaten schneller zu erkunden.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Data Explorer-Pools mit Synapse Studio](data-explorer-create-pool-studio.md)
* [Erstellen eines Data Explorer-Pools im Azure-Portal (Vorschau)](data-explorer-create-pool-portal.md)
