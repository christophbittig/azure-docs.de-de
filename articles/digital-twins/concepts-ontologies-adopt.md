---
title: Übernehmen von Branchenstandardontologien
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie mehr über bereits vorhandene branchenspezifische Ontologien, die für Azure Digital Twins übernommen werden können.
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e31ee4ed9b7baa074f59bc615b9044cbf314a47d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438512"
---
# <a name="adopting-an-industry-ontology"></a>Übernehmen einer branchenspezifischen Ontologie

Da es einfacher sein kann, mit einer Open-Source-DTDL-Ontologie zu beginnen, als von Grund auf neu zu entwickeln, arbeitet Microsoft mit Branchenexperten zusammen, um Ontologien zu veröffentlichen. Diese Ontologien stellt weitgehend akzeptierte Branchenkonventionen dar und unterstützen verschiedenste Anwendungsfälle von Kunden. 

Das Ergebnis dieser Zusammenarbeit ist eine Reihe von Open-Source-Ontologien auf Basis von DTDL, die aus Branchenstandards lernen, auf diesen aufbauen oder sie direkt verwenden. Diese Ontologien sind so konzipiert, dass sie die Anforderungen von Entwicklern, die damit weiterarbeiten, erfüllen und das Potenzial haben, in der entsprechenden Branche von vielen übernommen und erweitert zu werden.

Microsoft hat hierbei mit Partnern zusammengearbeitet, um Ontologien für [intelligente Gebäude](#realestatecore-smart-building-ontology), [intelligente Städte](#smart-cities-ontology) und [Stromnetze](#energy-grid-ontology) zu entwickeln. Sie dienen als gemeinsame Grundlage für die Modellierung anhand der Standards in diesen Branchen, um zu vermeiden, dass Aufwand für Neuerfindungen anfällt. 

Jede Ontologie legt den Schwerpunkt auf eine anfängliche Gruppe von Modellen. Die Ersteller der Ontologie laden Sie ein, zur Erweiterung der anfänglichen Gruppe mit Anwendungsfällen beizutragen und die vorhandenen Modelle zu verbessern. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore-Ontologie für intelligente Gebäude

*Die Ontologie können Sie über das folgende Repository beziehen:* [Auf der Digital Twins Definition Language (DTDL) basierende RealEstateCore-Ontologie für intelligente Gebäude](https://github.com/Azure/opendigitaltwins-building).

Microsoft hat in Zusammenarbeit mit [RealEstateCore](https://www.realestatecore.io/), einem schwedischen Zusammenschluss von Immobilieneigentümern, Softwareherstellern und Forschungseinrichtungen, diese quelloffene DTDL-Ontologie für die Immobilienbranche bereitgestellt.

Diese Ontologie für intelligente Gebäude bietet eine gemeinsame Grundlage für die Modellierung von intelligenten Gebäuden basierend auf Branchenstandards (z. B. dem  [Brick-Schema](https://brickschema.org/ontology/) oder der  [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html)), um ein Neuerfinden zu vermeiden. Außerdem werden bewährte Methoden für die Nutzung und richtige Erweiterung der Ontologie bereitgestellt. 

Weitere Informationen zu den Struktur- und Modellierungskonventionen für die Ontologie, ihrer Verwendung, ihrer Erweiterung sowie zur Mitwirkung daran finden Sie im Repository für die Ontologie auf GitHub: [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

Weitere Informationen zur Partnerschaft mit RealEstateCore und den Zielen für diese Initiative finden Sie im folgenden Blogbeitrag und dem zugehörigen Video: [RealEstateCore, eine Ontologie für intelligente Gebäude für Digital Twins, ist jetzt verfügbar](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologie für intelligente Städte

*Die Ontologie können Sie über das folgende Repository beziehen:* [Digital Twins Definition Language-Ontologie (DTDL) für Smart Cities.](https://github.com/Azure/opendigitaltwins-smartcities)

Microsoft hat mit [Open Agile Smart Cities (OASC)](https://oascities.org/) und [Sirus](https://sirus.be/) zusammengearbeitet, um eine DTDL-basierte Ontologie für intelligente Städte bereitzustellen. Hierbei wurde mit [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim) begonnen. Zusätzlich zu ETSI NGSI-LD haben wir auch Saref4City, CityGML, ISO und andere evaluiert.

Weitere Informationen zur Ontologie, zu ihrer Verwendung und zur Möglichkeit zum Leisten eines Beitrags finden Sie im Repository der Ontologie auf GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Weitere Informationen zu den Partnerschaften und zum Ansatz für intelligente Städte erhalten Sie auch im folgenden Blogbeitrag und dem zugehörigen Video: [Ontologie für intelligente Städte: Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="energy-grid-ontology"></a>Stromnetz-Ontologie

*Die Ontologie können Sie über das folgende Repository beziehen:* [Digital Twins Definition Language-Ontologie (DTDL) für Energy Grid.](https://github.com/Azure/opendigitaltwins-energygrid/)

Diese Ontologie wurde entwickelt, um Lösungsanbietern dabei zu helfen, die Entwicklung von Lösungen für digitale Zwillinge für Anwendungsfälle im Energiebereich (Überwachung von Netzressourcen, Analyse von Ausfällen und Auswirkungen, Simulation und Predictive Maintenance) zu beschleunigen und die digitale Transformation und Modernisierung des Stromnetzes zu ermöglichen. Sie ist vom [Common Information Model (CIM)](https://cimug.ucaiug.org/) abgeleitet, einem globalen Standard für die Verwaltung von Stromnetzressourcen, die Modellierung des Energiesystembetriebs und den physischen Energierohstoffmarkt.

Weitere Informationen zur Ontologie, zu ihrer Verwendung und zur Möglichkeit zum Leisten eines Beitrags finden Sie im Repository der Ontologie auf GitHub: [Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/). 

Weitere Informationen zu den Partnerschaften und dem Ansatz für Stromnetze finden Sie im folgenden Blogbeitrag: [Stromnetz-Ontologie für Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das Erweitern von Branchenstandardontologien zur Erfüllung Ihrer Spezifikationen: [Erweitern von Branchenontologien](concepts-ontologies-extend.md).

* Oder fahren Sie mit dem Pfad für das Entwickeln von Modellen basierend auf Ontologien fort: [Verwenden von Ontologiestrategien in einem Modellentwicklungspfad](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).