---
title: Übernehmen von Branchenstandardontologien
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie mehr über bereits vorhandene branchenspezifische Ontologien, die für Azure Digital Twins übernommen werden können.
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 936df82dc8c7c5babcb3b1844f2d53f1396dbd8b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500657"
---
# <a name="adopting-an-industry-ontology"></a>Übernehmen einer branchenspezifischen Ontologie

In diesem Artikel werden verschiedene Branchenstandard-Ontologien behandelt, die zur Vereinfachung von Lösungen übernommen werden können.

Da es einfacher sein kann, mit einer Open-Source-DTDL-Ontologie (Digital Twins Definition Language) zu beginnen, als von Grund auf neu zu entwickeln, arbeitet Microsoft mit Branchenexperten zusammen, um Ontologien zu veröffentlichen. Diese Ontologien stellt weitgehend akzeptierte Branchenkonventionen dar und unterstützen verschiedenste Anwendungsfälle von Kunden. 

Das Ergebnis dieser Zusammenarbeit ist eine Reihe von Open-Source-Ontologien auf Basis von DTDL, die aus Branchenstandards lernen, auf diesen aufbauen oder sie direkt verwenden. Diese Ontologien sind so konzipiert, dass sie die Anforderungen von Entwicklern, die damit weiterarbeiten, erfüllen und das Potenzial haben, in der entsprechenden Branche von vielen übernommen und erweitert zu werden.

Zurzeit erstellt Microsoft mit Partnern Ontologien für [intelligente Gebäude](#realestatecore-smart-building-ontology), [intelligente Städte](#smart-cities-ontology) und [Stromnetze](#energy-grid-ontology). Diese Ontologien bieten eine gemeinsame Grundlage für die Modellierung auf der Grundlage von Standards in diesen Branchen, damit sie nicht neu erfunden werden müssen. 

Jede Ontologie legt den Schwerpunkt auf eine anfängliche Gruppe von Modellen. Die Ersteller der Ontologie laden Sie ein, zur Erweiterung der anfänglichen Gruppe mit Anwendungsfällen beizutragen und die vorhandenen Modelle zu verbessern. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore-Ontologie für intelligente Gebäude

*Die Ontologie können Sie über das folgende Repository beziehen:* [Auf der Digital Twins Definition Language (DTDL) basierende RealEstateCore-Ontologie für intelligente Gebäude](https://github.com/Azure/opendigitaltwins-building).

Microsoft ist eine Partnerschaft mit [RealEstateCore](https://www.realestatecore.io/) eingegangen, um diese Open-Source-DTDL-Ontologie für die Immobilienbranche bereitzustellen. [RealEstateCore](https://www.realestatecore.io/) ist ein schwedisches Konsortium aus Immobilienbesitzern, Softwareanbietern und Forschungseinrichtungen.

Diese Ontologie für intelligente Gebäude bietet eine gemeinsame Grundlage für die Modellierung von intelligenten Gebäuden basierend auf Branchenstandards (z. B. dem  [Brick-Schema](https://brickschema.org/ontology/) oder der  [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html)), um ein Neuerfinden zu vermeiden. Außerdem werden bewährte Methoden für die Nutzung und richtige Erweiterung der Ontologie bereitgestellt. 

Weitere Informationen zu den Struktur- und Modellierungskonventionen für die Ontologie, ihrer Verwendung, ihrer Erweiterung sowie zur Mitwirkung daran finden Sie im Repository für die Ontologie auf GitHub: [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

Weitere Informationen zur Partnerschaft mit RealEstateCore und den Zielen für diese Initiative finden Sie im folgenden Blogbeitrag und dem eingebetteten Video: [RealEstateCore, eine Ontologie für intelligente Gebäude für Digital Twins, ist jetzt verfügbar](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologie für intelligente Städte

*Die Ontologie können Sie über das folgende Repository beziehen:* [Digital Twins Definition Language-Ontologie (DTDL) für Smart Cities.](https://github.com/Azure/opendigitaltwins-smartcities)

Microsoft hat mit [Open Agile Smart Cities (OASC)](https://oascities.org/) und [Sirus](https://sirus.be/) zusammengearbeitet, um eine DTDL-basierte Ontologie für intelligente Städte bereitzustellen. Hierbei wurde mit [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim) begonnen. Neben ETSI NGSI-LD haben wir auch Saref4City, CityGML, ISO und andere evaluiert.

Weitere Informationen zur Ontologie, zu ihrer Verwendung und zur Möglichkeit zum Leisten eines Beitrags finden Sie im Repository der Ontologie auf GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Weitere Informationen zu den Partnerschaften und zum Ansatz für intelligente Städte erhalten Sie auch im folgenden Blogbeitrag und dem eingebetteten Video: [Ontologie für intelligente Städte: Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="energy-grid-ontology"></a>Stromnetz-Ontologie

*Die Ontologie können Sie über das folgende Repository beziehen:* [Digital Twins Definition Language-Ontologie (DTDL) für Energy Grid.](https://github.com/Azure/opendigitaltwins-energygrid/)

Diese Ontologie wurde entwickelt, um Lösungsanbietern dabei zu helfen, die Entwicklung von Lösungen für digitale Zwillinge für Anwendungsfälle im Energiebereich wie die Überwachung von Netzressourcen, Analyse von Ausfällen und Auswirkungen, Simulation und Predictive Maintenance. Darüber hinaus kann die Ontologie verwendet werden, um die digitale Transformation und Modernisierung des Stromnetzes zu ermöglichen. Sie ist vom [Common Information Model (CIM)](https://cimug.ucaiug.org/) abgeleitet, einem globalen Standard für die Verwaltung von Stromnetzressourcen, die Modellierung des Energiesystembetriebs und den physischen Energierohstoffmarkt.

Weitere Informationen zur Ontologie, zu ihrer Verwendung und zur Möglichkeit zum Leisten eines Beitrags finden Sie im Repository der Ontologie auf GitHub: [Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/). 

Weitere Informationen zu den Partnerschaften und dem Ansatz für Stromnetze finden Sie im folgenden Blogbeitrag: [Stromnetz-Ontologie für Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das Erweitern von Branchenstandardontologien zur Erfüllung Ihrer Spezifikationen: [Erweitern von Branchenontologien](concepts-ontologies-extend.md).

* Oder fahren Sie mit dem Pfad für das Entwickeln von Modellen basierend auf Ontologien fort: [Verwenden von Ontologiestrategien in einem Modellentwicklungspfad](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).