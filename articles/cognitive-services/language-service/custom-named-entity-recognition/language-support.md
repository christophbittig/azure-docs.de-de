---
title: Sprach- und Regionsunterstützung für die benutzerdefinierte Erkennung benannter Entitäten (Custom Named Entity Recognition, NER)
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Sprachen und Regionen, die von der benutzerdefinierten Erkennung benannter Entitäten unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: references_regions, language-service-custom-ner, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 499fa25a09699e21610fb2c1812612616fb818da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095342"
---
# <a name="language-support-for-custom-named-entity-recognition-ner"></a>Sprachunterstützung für die benutzerdefinierte Erkennung benannter Entitäten

In diesem Artikel erfahren Sie mehr über die Sprachen und Regionen, die derzeit von der benutzerdefinierten Erkennung benannter Entitäten unterstützt werden.

## <a name="multiple-language-support"></a>Unterstützung für mehrere Sprachen

Mit der benutzerdefinierten Erkennung benannter Entitäten können Sie ein Modell in einer Sprache trainieren und in einer anderen Sprache testen. Dieses Feature ist sehr leistungsfähig, da es Ihnen hilft, Zeit und Aufwand zu sparen: Anstatt separate Projekte für jede Sprache zu erstellen, können Sie mehrsprachige Datasets in einem Projekt verarbeiten. Ihr Dataset muss nicht zur Gänze in derselben Sprache vorliegen, aber Sie müssen diese Option bei der Projekterstellung angeben. Wenn Sie während des Auswertungsprozesses feststellen, dass Ihr Modell in bestimmten Sprachen eine schlechte Leistung aufweist, sollten Sie ihrem Trainingssatz weitere Daten in den betreffenden Sprachen hinzufügen.

> [!NOTE]
> Zum Aktivieren der Unterstützung für mehrere Sprachen müssen Sie diese Option beim [Erstellen Ihres Projekts](how-to/create-project.md) aktivieren.

## <a name="language-support"></a>Sprachunterstützung

Die benutzerdefinierte Erkennung benannter Entitäten unterstützt `.txt`-Dateien in den folgenden Sprachen:

| Sprache | Gebietsschema |  
|--|--|
| Englisch (USA) |`en-US` |
| Französisch (Frankreich) |`fr-FR` |
| Deutsch |`de-DE` |
| Italienisch |`it-IT` |
| Spanisch (Spanien) |`es-ES` |
| Portugiesisch (Portugal) | `pt-PT` |
| Portugiesisch (Brasilien) | `pt-BR` |

## <a name="next-steps"></a>Nächste Schritte

[Benutzerdefinierte NER-Übersicht](overview.md)
