---
title: Anpassen der Aussprache
description: Verwenden Sie Phoneme, um die Aussprache von Wörtern in Speech-to-Text anzupassen.
author: ut-karsh
ms.author: umaheshwari
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/19/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 6b8f2339668656ec067bae234d21ec4bf2d5741c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021515"
---
# <a name="customize-pronunciation"></a>Anpassen der Aussprache

Die benutzerdefinierte Sprachausgabe ermöglicht es Ihnen, mit dem Universal Phone Set verschiedene Aussprachen für bestimmte Wörter bereitzustellen. Der Universal Phone Set (UPS) ist ein maschinenlesbarer Telefonsatz, der auf dem International Phonetic Set Alphabet (IPA) basiert. Das IPA wird von Sprachwissenschaftlern in der ganzen Welt verwendet und ist als Standard anerkannt.

Die UPS-Aussprache besteht aus einer Reihe von UPS-Telefonen, die jeweils durch Leerzeichen getrennt sind. Beim Telefonset wird zwischen Groß- und Kleinschreibung unterschieden. UPS Telefonbeschriftungen werden alle mit ASCII-Zeichenketten definiert.

Für Schritte zur Implementierung von UPS siehe [Strukturierte Textdaten für Trainingstelefonsätze](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)

Diese strukturierten Textdaten sind nicht dasselbe wie die [Aussprachedateien](how-to-custom-speech-test-and-train.md#pronunciation-data-for-training), und sie können nicht zusammen verwendet werden.

## <a name="languages-supported"></a>Unterstützte Sprachen

Verwenden Sie die nachstehende Tabelle, um zum USV für die jeweilige Sprache zu navigieren.

| Sprache                | Gebietsschema  |
|-------------------------|---------|
| [Englisch (USA)](phone-sets.md) | `en-US` |


## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen der UPS-Aussprache für benutzerdefinierte Sprache](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)
