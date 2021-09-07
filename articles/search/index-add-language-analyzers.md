---
title: Hinzufügen von Sprachanalysetools zu Zeichenfolgenfeldern
titleSuffix: Azure Cognitive Search
description: Mehrsprachige lexikalische Analyse für nicht englischsprachige Abfragen und Indizes in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 3a8a235e204826c26f20cc146003e9290331fe07
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091535"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Hinzufügen von Sprachanalysetools zu Zeichenfolgenfeldern in einem Azure Cognitive Search-Index

Ein *Sprachanalysetool* ist eine bestimmte Art einer [Textanalyse](search-analyzers.md), die eine lexikalische Analyse mithilfe der linguistischen Regeln der Zielsprache durchführt. Jedes durchsuchbare Feld verfügt über die Eigenschaft **analyzer**. Wenn Ihr Inhalt aus übersetzten Zeichenfolgen besteht, wie z. B. separaten Feldern für englischen und chinesischen Text, können Sie für jedes Feld Sprachanalysetools angeben, um auf die umfangreichen linguistischen Funktionen dieser Sprachanalysetools zuzugreifen.

## <a name="when-to-use-a-language-analyzer"></a>Anwendungsfälle für Sprachanalysetools

Sie sollten ein Sprachanalysetool in Erwägung ziehen, wenn der Wert der Textanalyse durch Sensitivität für die Wort- oder Satzstruktur steigt. Ein häufiges Beispiel ist die Zuordnung unregelmäßiger Verbformen („bringt“ und „brachte“) oder Pluralformen („Sphinx“ und „Sphingen“). Ohne linguistische Sensitivität werden diese Zeichen nur nach ihren physischen Merkmalen analysiert, wodurch die Verbindung nicht erkannt wird. Da große Textabschnitte mit höherer Wahrscheinlichkeit solche Inhalte aufweisen, sind Felder, die Beschreibungen, Rezensionen oder Zusammenfassungen enthalten, gute Kandidaten für ein Sprachanalysetool.

Sie sollten Sprachanalysetools außerdem in Erwägung ziehen, wenn die Inhalte aus Zeichenfolgen in nicht westlichen Sprachen bestehen. Die [Standardanalyse](search-analyzers.md#default-analyzer) ist zwar nicht sprachsensitiv, das Konzept der Verwendung von Leerzeichen und Sonderzeichen (Bindestriche und Schrägstriche) zum Trennen von Zeichenfolgen lässt sich tendenziell aber eher auf westliche Sprachen als auch nicht westliche Sprachen anwenden. 

Beispielsweise ist im Chinesischen, Japanischen, Koreanischen (CJK) und anderen asiatischen Sprachen ein Leerzeichen nicht zwangsläufig ein Trennzeichen für Wörter. Sehen Sie sich die folgende japanische Zeichenfolge an. Da sie keine Leerzeichen aufweist, wird die gesamte Zeichenfolge von einem nicht sprachsensitiven Analysetool wahrscheinlich als ein Token analysiert, während es sich bei dieser Zeichenfolge tatsächlich um einen Satz handelt.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Für das Beispiel oben müsste eine erfolgreiche Abfrage das vollständige Token oder einen Teil des Tokens mit einem Suffix-Platzhalterzeichen enthalten, was zu einem unnatürlichen und eingeschränkten Suchverhalten führt.

Ein besseres Verhalten ergibt sich bei der Suche nach einzelnen Wörtern: 明るい (hell), 私たちの (unsere), 銀河系 (Galaxie). Durch die Verwendung eines der japanischen Analysetools, die in Cognitive Search zur Verfügung stehen, lässt sich dieses Verhalten leichter aufbrechen, da diese Analysetools besser dafür ausgerüstet sind, den Textabschnitt in aussagekräftige Wörter in der Zielsprache aufzuteilen.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Vergleich der Analysetools von Lucene und Microsoft

Azure Cognitive Search unterstützt 35 Sprachanalysetools auf Basis von Lucene und 50 Sprachanalysetools, die von Microsoft-Technologien zur Verarbeitung natürlicher Sprache unterstützt werden, die in Office und Bing zum Einsatz kommen.

Einige Entwickler bevorzugen möglicherweise die vertrautere, einfachere Open Source-Lösung von Lucene. Lucene-Sprachanalysetools sind schneller, während Microsoft-Sprachanalysetools erweiterte Funktionen bieten, wie z.B. Lemmatisierung, Wortzerlegung (in Sprachen wie Deutsch, Dänisch, Niederländisch, Schwedisch, Norwegisch, Estnisch, Finnisch, Ungarisch und Slowakisch) und Entitätserkennung (URLs, E-Mails, Datumsangaben und Zahlen). Vergleichen Sie nach Möglichkeit die Analyseprogramme von Microsoft und Lucene, um die für Ihre Anforderungen passendere Lösung zu ermitteln. Sie können die [Analyse-API](/rest/api/searchservice/test-analyzer) verwenden, um die Token, die auf der Grundlage eines bestimmten Texts generiert wurden, mithilfe eines bestimmten Analysetools anzuzeigen.

Die Indizierung mit Analyseprogrammen von Microsoft dauert je nach Sprache durchschnittlich zwei bis drei Mal länger als mit entsprechenden Analyseprogrammen von Lucene. Die Suchleistung sollte bei durchschnittlich großen Abfragen nicht wesentlich eingeschränkt sein. 

### <a name="english-analyzers"></a>Analysetools für Englisch

Standardmäßig wird das Lucene-Standardanalysetool verwendet, das für Englisch gut funktioniert, vielleicht aber nicht ganz so gut wie das Analysetool für Englisch von Lucene oder das Analysetool für Englisch von Microsoft.

+ Das Analysetool für Englisch von Lucene ist eine Erweiterung des Standardanalysetools. Es entfernt Possessivformen (nachgestelltes -s) bei Wörtern, wendet gemäß dem Wortstammerkennungsalgorithmus von Porter die Wortstammerkennung an und entfernt englische Stoppwörter.  

+ Das Analysetool für Englisch von Microsoft führt die Lemmatisierung anstelle der Wortstammerkennung durch. Dadurch können gebeugte und unregelmäßige Wortformen viel besser verarbeitet werden, was zu relevanteren Suchergebnissen führt. 

## <a name="how-to-specify-a-language-analyzer"></a>Festlegen einer Sprachanalyse

Legen Sie während der Felddefinition eine Sprachanalyse für searchable-Felder vom Typ „Edm.String“ fest.

Obwohl Felddefinitionen über mehrere Eigenschaften in Bezug auf das Analysetool verfügen, kann nur die Eigenschaft „analyzer“ für Sprachanalysetools verwendet werden. Der Wert von „analyzer“ muss einem der Sprachanalysetools aus der Liste unterstützter Analysetools entsprechen.

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Weitere Informationen über die Erstellung eines Index und das Festlegen von Feldeigenschaften finden Sie unter [Erstellen eines Index](/rest/api/searchservice/create-index). Weitere Informationen über die Textanalyse finden Sie unter [Analysetools in Azure Cognitive Search](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Unterstützte Sprachanalysetools

 In der folgenden Liste werden die unterstützten Sprachen und die Namen der entsprechenden Analysetools von Lucene und Microsoft aufgeführt.  

| Sprache | Name des Microsoft-Analysetools | Name des Lucene-Analysetools |
|----------|-------------------------|----------------------|
| Arabisch   | ar.microsoft | ar.lucene |
| Armenisch |           | hy.lucene |
| Bengalisch   | bn.microsoft |  |
| Baskisch   |  | eu.lucene |
| Bulgarisch | bg.microsoft | bg.lucene |
| Katalanisch  | ca.microsoft | ca.lucene |
| Chinesisch (vereinfacht) | zh-Hans.microsoft | zh-Hans.lucene |
| Chinesisch (traditionell) | zh-Hant.microsoft | zh-Hant.lucene |
| Kroatisch | hr.microsoft |  |
| Tschechisch | cs.microsoft | cs.lucene |
| Dänisch | da.microsoft | da.lucene |
| Niederländisch | nl.microsoft | nl.lucene |
| Englisch | en.microsoft | en.lucene |
| Estnisch | et.microsoft |  |
| Finnisch | fi.microsoft | fi.lucene |
| Französisch | fr.microsoft | fr.lucene |
| Galizisch |  | gl.lucene |
| Deutsch | de.microsoft | de.lucene |
| Griechisch | el.microsoft | el.lucene |
| Gujarati | gu.microsoft |  |
| Hebräisch | he.microsoft |  |
| Hindi | hi.microsoft | hi.lucene |
| Ungarisch | hu.microsoft | hu.lucene |
| Isländisch | is.microsoft |  |
| Indonesisch (Bahasa) | id.microsoft | id.lucene |
| Irisch |  | ga.lucene |
| Italienisch | it.microsoft | it.lucene |
| Japanisch | ja.microsoft | ja.lucene |
| Kannada | kn.microsoft |  |
| Koreanisch | ko.microsoft | ko.lucene |
| Lettisch | lv.microsoft | lv.lucene |
| Litauisch | lt.microsoft |  |
| Malayalam | ml.microsoft |  |
| Malaiisch (Lateinisch) | ms.microsoft |  |
| Marathi | mr.microsoft |  |
| Norwegisch | nb.microsoft | no.lucene |
| Persisch |  | fa.lucene |
| Polnisch | pl.microsoft | pl.lucene |
| Portugiesisch (Brasilien) | pt-Br.microsoft | pt-Br.lucene |
| Portugiesisch (Portugal) | pt-Pt.microsoft | pt-Pt.lucene |
| Pandschabi | pa.microsoft |  |
| Rumänisch | ro.microsoft | ro.lucene |
| Russisch | ru.microsoft | ru.lucene |
| Serbisch (Kyrillisch) | sr-cyrillic.microsoft |  |
| Serbisch (Lateinisch) | sr-latin.microsoft |  |
| Slowakisch | sk.microsoft |  |
| Slowenisch | sl.microsoft |  |
| Spanisch | es.microsoft | es.lucene |
| Schwedisch | sv.microsoft | sv.lucene |
| Tamilisch | ta.microsoft |  |
| Telugu | te.microsoft |  |
| Thailändisch | th.microsoft | th.lucene |
| Türkisch | tr.microsoft | tr.lucene |
| Ukrainisch | uk.microsoft |  |
| Urdu | ur.microsoft |  |
| Vietnamesisch | vi.microsoft |  |

 Alle Analysetools mit **Lucene** im Namen werden von den [Sprachanalysetools von Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ) unterstützt.

## <a name="see-also"></a>Weitere Informationen  

+ [Erstellen eines Index](search-what-is-an-index.md)
+ [Erstellen eines mehrsprachigen Index](search-language-support.md)
+ [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index)  
+ [LexicalAnalyzerName-Klasse (Azure SDK für .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)
