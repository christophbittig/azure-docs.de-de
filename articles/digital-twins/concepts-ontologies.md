---
title: Was ist eine Ontologie?
titleSuffix: Azure Digital Twins
description: Erfahren Sie mehr über Ontologien für digitale Zwillinge, wie sie in Azure Digital Twins verwendet werden und wie diese DTDL-Ontologien für die Modellierung im Kontext bestimmter Branchen verwendet werden können.
author: baanders
ms.author: baanders
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ce0815bdcb59e64f6a61cce4d4fb6172abea20
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501626"
---
# <a name="what-is-an-ontology"></a>Was ist eine Ontologie? 

Dieser Artikel beschreibt das Konzept von Branchen-Ontologien und wie sie im Kontext von Azure Digital Twins verwendet werden können.

Das Vokabular einer Azure Digital Twins-Lösung wird mit [Modellen](concepts-models.md) definiert, die die Arten von Entitäten in Ihrer Umgebung beschreiben.

Wenn es sich bei Ihrer Lösung um eine branchenspezifische Lösung handelt, kann es manchmal einfacher und effektiver sein, mit einer Gruppe bereits vorhandener Modelle für diese Branche zu arbeiten statt ein eigenes Modell von Grund auf neu zu erstellen. Diese Gruppen bereits vorhandener Modelle werden **Ontologien** genannt.

Allgemein ist eine Ontologie eine Gruppe von Modellen für eine bestimmte Domäne (z. B. eine Gebäudestruktur, ein IoT-System, eine Smart City, das Energienetz, Webinhalte usw.). Ontologien werden häufig als Schemas für Zwillingsgraphen verwendet, da sie Folgendes ermöglichen können:
* Harmonisierung von Softwarekomponenten, Dokumentation, Abfragebibliotheken usw.
* Geringerer Investitionsaufwand für konzeptionelle Modellierung und Systementwicklung
* Einfachere Interoperabilität von Daten auf Semantikebene
* Wiederverwendung unter Berücksichtigung bewährter Methoden statt von Grund auf neu zu beginnen oder das Rad neu zu erfinden

In diesem Artikel wird erläutert, warum Sie Ontologien für Ihre Azure Digital Twins-Modelle verwenden sollten und wie Sie dabei vorgehen können. Außerdem wird erläutert, welche Ontologien und Tools für sie derzeit verfügbar sind.

## <a name="using-ontologies-for-azure-digital-twins"></a>Verwenden von Ontologien für Azure Digital Twins

Ontologien sind ein hervorragender Ausgangspunkt für Lösungen mit digitalen Zwillingen. Sie umfassen eine Reihe domänenspezifischer Modelle und Beziehungen zwischen Entitäten zum Entwerfen, Erstellen und Analysieren von Graphen für digitale Zwillinge. Mit ihrer Hilfe können Lösungsentwickler bei Lösungen mit digitalen Zwillingen an einem bewährten Ausgangspunkt beginnen und sich auf das Lösen von Geschäftsproblemen konzentrieren. Die von Microsoft bereitgestellten Ontologien sind so gestaltet, dass sie leicht erweitert werden können, sodass Sie sie für Ihre Lösung anpassen können. 

Außerdem können diese Ontologien durch die Verwendung in Ihren Lösungen eine nahtlosere Integration zwischen verschiedenen Partnern und Herstellern ermöglichen, da sie ein gemeinsames Vokabular für verschiedene Lösungen bieten können.

Da Modelle in Azure Digital Twins in [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) dargestellt werden, sind die Ontologien für Azure Digital Twins ebenfalls in DTDL geschrieben. 

## <a name="strategies-for-integrating-ontologies"></a>Strategien für die Integration von Ontologien

Es gibt drei mögliche Strategien für das Integrieren von Branchenstandardontologien mit DTDL. Sie können die Strategie verwenden, die basierend auf Ihren Anforderungen am besten für Sie geeignet ist:

| Strategie | BESCHREIBUNG | Ressourcen |
| --- | --- | --- |
| **Übernehmen** | Sie können Ihre Lösung mit einer Open-Source-DTDL-Ontologie beginnen, die auf weit verbreiteten Branchenstandards basiert. Sie können diese Gruppen von Modellen entweder so verwenden, wie sie bereitgestellt werden, oder um eigene Ergänzungen für eine benutzerdefinierte Lösung erweitern. | [Übernehmen&nbsp;von&nbsp;Branchenstandardontologien](concepts-ontologies-adopt.md)<br><br>[Erweitern&nbsp;von Ontologien](concepts-ontologies-extend.md) |
| **Konvertieren** | Wenn Sie bereits über vorhandene Modelle in einem anderen Standardformat verfügen, können Sie diese für die Verwendung in Azure Digital Twins in DTDL konvertieren. | [Konvertieren&nbsp;von Ontologien](concepts-ontologies-convert.md)<br><br>[Erweitern&nbsp;von Ontologien](concepts-ontologies-extend.md) |
| **Autor** | Sie können sich immer von anwendbaren Branchenstandards inspirieren lassen und eigene benutzerdefinierte DTDL-Modelle von Grund auf neu entwickeln. | [DTDL-Modelle](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Verwenden von Ontologiestrategien in einem Modellentwicklungspfad

Unabhängig davon, für welche Strategie zur Integration einer Ontologie in Azure Digital Twins Sie sich entscheiden, kann der gesamte folgende Pfad Sie durch das Erstellen und Hochladen Ihrer Ontologie als DTDL-Modelle führen.

1. Beginnen Sie damit, sich über [DTDL-Modellierung in Azure Digital Twins](concepts-models.md) zu informieren und ein Verständnis dafür zu entwickeln.
1. Fahren Sie mit der von Ihnen gewählten Ontologie-Integrationsstrategie fort: [Übernehmen](concepts-ontologies-adopt.md), [Konvertieren](concepts-ontologies-convert.md) oder [Erstellen](concepts-models.md) Sie Ihre Modelle auf der Grundlage Ihrer Ontologie.
    1. [Erweitern](concepts-ontologies-extend.md) Sie ggf. Ihre Ontologie, um sie an Ihre Anforderungen anzupassen.
1. [Überprüfen](how-to-parse-models.md) Sie Ihre Modelle, um sicherzustellen, dass es sich um funktionierende DTDL-Dokumente handelt.
1. Laden Sie Ihre fertigen Modelle über die [APIs](how-to-manage-model.md#upload-models) oder eine Beispielanwendung wie [Azure Digital Twins Model Uploader](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels) in Azure Digital Twins hoch.

Nachdem Sie diese Folge von Artikeln gelesen haben, sollten Sie Ihre Modelle in Ihrer Azure Digital Twins-Instanz verwenden können. 

>[!TIP]
> Sie können die Modelle in Ihrer Ontologie mit dem [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md) oder dem [Azure Digital Twins-Modellvisualisierer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) visualisieren.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie weitere Artikel über die Strategien des Übernehmens, Konvertierens und Erstellens von Ontologien:
* [Einführung in Branchenstandard-Ontologien](concepts-ontologies-adopt.md)
* [Konvertieren von Ontologien](concepts-ontologies-convert.md)
* [Verwalten von DTDL-Modellen](how-to-manage-model.md)

Oder informieren Sie sich darüber, wie mit Modellen digitale Zwillinge erstellt werden: [Digitale Zwillinge und der Zwillingsgraph](concepts-twins-graph.md).