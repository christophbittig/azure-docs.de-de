---
title: Sprachunterstützung bei der benutzerdefinierten Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, welche Sprachen bei der Extraktion benutzerdefinierter Entitäten unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 13e031c502538fa2126e6d4ab4e9e4eb49616142
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509079"
---
# <a name="language-support"></a>Sprachunterstützung

In diesem Artikel erfahren Sie, welche Sprachen bei der benutzerdefinierten Textklassifizierung unterstützt werden.

## <a name="multiple-language-support"></a>Unterstützung für mehrere Sprachen

Mit benutzerdefinierter Textklassifizierung können Sie ein Modell in einer Sprache trainieren und in einer anderen Sprache testen. Dieses Feature ist sehr leistungsfähig, da es Ihnen hilft, Zeit und Aufwand zu sparen: Anstatt separate Projekte für jede Sprache zu erstellen, können Sie mehrsprachige Datasets in einem Projekt verarbeiten.  Ihr Dataset muss nicht zur Gänze in derselben Sprache vorliegen, aber Sie müssen diese Option bei der Projekterstellung angeben. Wenn Sie während des Auswertungsprozesses feststellen, dass Ihr Modell in bestimmten Sprachen eine schlechte Leistung aufweist, sollten Sie ihrem Trainingssatz weitere Daten in den betreffenden Sprachen hinzufügen.

> [!NOTE]
> Zum Aktivieren der Unterstützung für mehrere Sprachen müssen Sie diese Option beim [Erstellen Ihres Projekts](how-to/create-project.md) oder später über die Seite mit den Projekteinstellungen aktivieren.

## <a name="languages-supported-by-custom-text-classification"></a>Von der benutzerdefinierten Textklassifizierung unterstützte Sprachen

Die benutzerdefinierte Textklassifizierung unterstützt `.txt`-Dateien in den folgenden Sprachen:

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

* [Grenzwerte für die benutzerdefinierte Textklassifizierung – Übersicht](overview.md)
* [Datengrenzwerte](service-limits.md)
