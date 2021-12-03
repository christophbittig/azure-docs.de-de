---
title: Mehrsprachige Unterstützung und Emoji-Unterstützung in Azure Cognitive Service für Language
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über Offsets, die durch mehrsprachige Codierungen und Emojicodierungen in Sprachdienstfeatures verursacht werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48bda7b1e5ab5eaa62c525997530acd9af31a39a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021371"
---
# <a name="multilingual-and-emoji-support-in-language-service-features"></a>Unterstützung für mehrere Sprachen und Emojis in Sprachdienstfeatures

Die Unterstützung für mehrere Sprachen und Emojis hat zu Unicode-Codierungen geführt, die ein einzelnes angezeigtes Zeichen (ein so genanntes Graphem) mit mehreren [Codepunkten](https://wikipedia.org/wiki/Code_point) darstellen. Für Emojis wie 🌷 und 👍 werden beispielsweise mehrere Zeichen zum Erstellen der Form und zusätzliche Zeichen für visuelle Attribute wie etwa die Hautfarbe verwendet. Ebenso wird das Hindi-Wort `अनुच्छेद` mit fünf Buchstaben und drei Verbindungszeichen codiert.

Aufgrund der unterschiedlichen Längen der möglichen mehrsprachigen Codierungen und Emoji-Codierungen geben die Sprachdienstfeatures in der Antwort möglicherweise Offsets zurück.

## <a name="offsets-in-the-api-response"></a>Versätze in der API-Antwort

Wenn Offsets in der API-Antwort zurückgegeben werden, denken Sie an Folgendes:

* Elemente in der Antwort können spezifisch für den aufgerufenen Endpunkt sein. 
* HTTP POST/GET-Nutzlasten werden in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp) codiert. Dies kann die Standardzeichencodierung Ihres clientseitigen Compilers oder Betriebssystems sein oder auch nicht.
* Versätze beziehen sich auf die auf dem [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)-Standard basierende Graphemanzahl, nicht auf die Zeichenanzahl.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahieren von Teilzeichenfolgen aus Text mit Versätzen

Versätze können Probleme verursachen, wenn zeichenbasierte substring-Methoden verwendet werden, z. B. die .NET-Methode [substring()](/dotnet/api/system.string.substring). Eines dieser Probleme ist, dass eine substring-Methode aufgrund eines Versatzes in der Mitte einer Graphemcodierung mit mehreren Zeichen beendet werden kann und nicht an deren Ende.

Verwenden Sie in .NET ggf. die [StringInfo](/dotnet/api/system.globalization.stringinfo)-Klasse. Sie ermöglicht es Ihnen, eine Zeichenfolge nicht als einzelne Zeichenobjekte, sondern als eine Reihe von Textelementen zu verarbeiten. Sie können auch nach Graphem-Splitter-Bibliotheken in Ihrer bevorzugten Softwareumgebung suchen. 

Die Sprachdienstfeatures geben diese Textelemente ebenfalls der Einfachheit halber zurück.

Endpunkte, die einen Offset zurückgeben, unterstützen den `stringIndexType`-Parameter. Mit diesem Parameter werden die `offset`- und `length`-Attribute in der API-Ausgabe an das angeforderte Zeichenfolgeniterationsschema angepasst. Derzeit werden drei Typen unterstützt:

- `textElement_v8` (Standardwert): Durchläuft Grapheme gemäß der Definition des [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)-Standards.
- `unicodeCodePoint`: Durchläuft [Unicode-Codepunkte](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), das Standardschema für Python 3.
- `utf16CodeUnit`: Durchläuft [UTF-16-Codeeinheiten](https://unicode.org/faq/utf_bom.html#UTF16), das Standardschema für JavaScript, Java und .NET.

Wenn der angeforderte `stringIndexType` der gewünschten Programmierumgebung entspricht, kann die Extraktion von Substrings mithilfe von Substringstandard- oder -slicemethoden erfolgen. 

## <a name="see-also"></a>Weitere Informationen

* [Sprachdienstübersicht](../overview.md)
