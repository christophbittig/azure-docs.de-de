---
title: 'Azure Cognitive Services: Textmining und -analyse mit der Textanalyse-API'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über Textmining mit der Textanalyse-API. Verwenden Sie es für Stimmungsanalysen, Spracherkennung und andere Arten der Verarbeitung natürlicher Sprache.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/14/2021
ms.author: aahi
keywords: Textmining, Stimmungsanalyse, Textanalyse
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 804af634c7202fdc3f822e32e7cd1fbf827bfab1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733965"
---
# <a name="what-is-the-text-analytics-api"></a>Worum handelt es sich bei der Textanalyse-API?

Die Textanalyse-API ist ein cloudbasierter Dienst für Features zur Verarbeitung natürlicher Sprache, z. B. Textmining und -analyse, einschließlich Stimmungsanalysen, Opinion Mining, Schlüsselbegriffserkennung, Spracherkennung und Erkennung benannter Entitäten.

Die API ist ein Teil von [Azure Cognitive Services](../index.yml), einer Sammlung von Algorithmen für maschinelles Lernen und künstliche Intelligenz (KI) in der Cloud, die Sie für Ihre Entwicklungsprojekte verwenden können. Sie können diese Funktionen mit [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/) oder [Version 3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1) der REST-API oder mit der [Clientbibliothek](quickstarts/client-libraries-rest-api.md) verwenden.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

Diese Dokumentation enthält die folgenden Arten von Artikeln:
* Die [Schnellstarts](./quickstarts/client-libraries-rest-api.md) sind Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten. 
* [Anleitungen](./how-tos/text-analytics-how-to-call-api.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* [Konzepte](text-analytics-user-scenarios.md) enthalten ausführliche Erläuterungen der Funktionen und Features des Diensts.
* [Tutorials](./tutorials/tutorial-power-bi-key-phrases.md) sind ausführlichere Leitfäden, in denen die Verwendung dieses Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="sentiment-analysis"></a>Stimmungsanalyse

Ermitteln Sie anhand der [Stimmungsanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) (SA), was Menschen von Ihrer Marke oder Ihrem Thema halten, indem Sie unformatierten Text auf Hinweise einer positiven oder negativen Stimmung analysieren. 

Bei diesem Feature werden Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) basierend auf der höchsten Zuverlässigkeitsbewertung angegeben, die vom Dienst auf Satz- und Dokumentebene gefunden wird. Darüber hinaus werden bei diesem Feature Zuverlässigkeitsbewertungen zwischen 0 und 1 für jedes Dokument und jeden darin enthaltenen Satz zurückgegeben (positive, neutrale und negative Stimmung). Sie können den Dienst auch lokal ausführen, indem Sie [einen Container verwenden](how-tos/text-analytics-how-to-install-containers.md).

Opinion Mining (OM) ist ab Version 3.1 ein Feature der Stimmungsanalyse. Dieses Feature wird in der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) auch als aspektbasierte Standpunktanalyse bezeichnet und bietet feiner abgestufte Informationen zu den Meinungen in Bezug auf Wörter (z. B. Attribute von Produkten oder Dienstleistungen) in Texten.

## <a name="key-phrase-extraction"></a>Schlüsselwortextraktion

Verwenden Sie die [Schlüsselbegriffserkennung](how-tos/text-analytics-how-to-keyword-extraction.md) (Key Phrase Extraction, KPE), um schnell die Hauptkonzepte eines Texts zu identifizieren. Im Text „Das Essen war köstlich, und es gab hervorragendes Personal“ gibt die Schlüsselbegriffserkennung beispielsweise die Kernpunkte „Essen“ und „hervorragendes Personal“ zurück.

## <a name="language-detection"></a>Spracherkennung

Die Spracherkennung kann eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen [die Sprache von Eingabetexten erkennen](how-tos/text-analytics-how-to-language-detection.md) und einen einzigen Sprachcode für jedes Dokument melden, das auf Anforderung gesendet wird. Der Sprachcode wird mit einer Zuverlässigkeitsbewertung bereitgestellt.

## <a name="named-entity-recognition"></a>Erkennung benannter Entitäten

Die Erkennung benannter Entitäten (NER) kann in Ihrem Text [Entitäten identifizieren und kategorisieren](how-tos/text-analytics-how-to-entity-linking.md), die als Personen, Orte, Organisationen und Mengen erkannt werden. Bekannte Entitäten werden ebenfalls erkannt, und es werden Links zu weiteren Informationen im Internet bereitgestellt.

## <a name="text-summarization"></a>Textzusammenfassung

Die [Zusammenfassung](how-tos/extractive-summarization.md) erzeugt eine Textzusammenfassung, indem Sätze extrahiert werden, die zusammen die wichtigsten oder relevantesten Informationen innerhalb des ursprünglichen Inhalts darstellen. Dieses Feature fasst Artikel, Abhandlungen oder Dokumente auf die wichtigsten Sätze zusammen.

## <a name="text-analytics-for-health"></a>Textanalyse für Gesundheit

Text Analytics for Health ist ein Feature des Textanalyse-API-Diensts, der relevante medizinische Informationen aus unstrukturierten Texten wie Arztbriefen, Entlassungszusammenfassungen, klinischen Dokumenten und elektronischen Gesundheitsakten extrahiert und bezeichnet. 

## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

[Verwenden Sie Container für die Textanalyse](how-tos/text-analytics-how-to-install-containers.md), um API-Features lokal bereitzustellen. Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden. Die Textanalyse bietet folgende Container:

* Stimmungsanalyse
* Schlüsselbegriffserkennung (Vorschauversion)
* Sprachenerkennung (Vorschauversion)
* Textanalyse für Gesundheit

## <a name="asynchronous-operations"></a>Asynchrone Vorgänge

Der Endpunkt `/analyze` ermöglicht die [asynchrone](how-tos/text-analytics-how-to-call-api.md) Verwendung mehrerer Features der Textanalyse-API. Die Erkennung benannter Entitäten (NER), die Schlüsselbegriffserkennung (KPE), die Stimmungsanalyse (SA) und Opinion Mining (OM) sind als Teil des `/analyze`-Endpunkts verfügbar. Er ermöglicht die gemeinsame Nutzung dieser Features in einem einzigen Aufruf. Er ermöglicht das Senden von bis zu 125.000 Zeichen pro Dokument. Die Preise entsprechen denen der regulären Textanalyse.

## <a name="typical-workflow"></a>Typischer Workflow

Der Workflow ist einfach: Sie übermitteln Daten für die Analyse und verarbeiten Ausgaben in Ihrem Code. Analysetool werden unverändert (d.h. ohne Konfigurationen oder Anpassungen) verwendet.

1. [Erstellen Sie eine Azure-Ressource](how-tos/text-analytics-how-to-call-api.md) für die Textanalyse. [Rufen Sie anschließend den Schlüssel ab](how-tos/text-analytics-how-to-call-api.md), der für Sie zum Authentifizieren Ihrer Anforderungen generiert wurde.

2. [Formulieren Sie eine Anforderung](how-tos/text-analytics-how-to-call-api.md#json-schema), die Ihre Daten als unformatierten, unstrukturierten Text im JSON-Format enthält.

3. Übermitteln Sie die Anforderung an den Endpunkt, der im Rahmen der Registrierung eingerichtet wurde, und fügen Sie die gewünschte Ressource an: Standpunktanalyse, Schlüsselbegriffserkennung, Sprachenerkennung oder Erkennung benannter Entitäten.

4. Streamen oder speichern Sie die Antwort lokal. Je nach Anforderung ist das Ergebnis entweder eine Standpunktbewertung, eine Sammlung von extrahierten Schlüsselbegriffen oder ein Sprachcode.

Die Ausgabe wird als einzelnes JSON-Dokument mit Ergebnissen für jedes bereitgestellte Textdokument zurückgegeben und basiert auf der ID. Sie können die Ergebnisse anschließend analysieren, visualisieren oder in verwertbare Erkenntnisse kategorisieren.

Daten werden nicht in Ihrem Konto gespeichert. Von der Textanalyse-API durchgeführte Vorgänge sind zustandslos. Dies bedeutet, dass der bereitgestellte Text verarbeitet wird und die Ergebnisse sofort zurückgegeben werden.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalyse für mehrere Stufen der Programmierungserfahrung

Sie können mit der Verwendung der Textanalyse-API in Ihren Prozessen beginnen, auch wenn Sie über wenig Erfahrung bei der Programmierung verfügen. Anhand dieser Tutorials erfahren Sie, wie Sie die API verwenden können, um Text gemäß Ihrem Erfahrungsstand auf verschiedene Weise zu analysieren. 

* Minimaler Programmieraufwand:
    * [Extrahieren von Informationen aus Excel mithilfe von Textanalyse und Power Automate](tutorials/extract-excel-information.md)
    * [Verwenden der Textanalyse-API und von MS Flow zum Ermitteln der Stimmung in Kommentaren in einer Yammer-Gruppe](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integrieren von Power BI mit der Textanalyse-API zur Analyse von Kundenfeedback](tutorials/tutorial-power-bi-key-phrases.md)
* Programmiererfahrung empfohlen:
    * [Standpunktanalyse für Streamingdaten mit Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Erstellen einer Flask-App zum Übersetzen von Text, Analysieren der Stimmung und Synthetisieren von Sprache](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Unterstützte Sprachen

Für eine bessere Auffindbarkeit wurde dieser Abschnitt in einen separaten Artikel verschoben. Diesen Inhalt finden Sie unter [Supported languages in the Text Analytics API](./language-support.md) (Unterstützte Sprachen in der Textanalyse-API).

<a name="data-limits"></a>

## <a name="data-limits"></a>Datengrenzwerte

Alle Endpunkte der Textanalyse-API akzeptieren unformatierte Textdaten. Weitere Informationen finden Sie im Artikel zu den [Datengrenzwerten](concepts/data-limits.md).

## <a name="unicode-encoding"></a>Unicode-Codierung

Die Textanalyse-API verwendet Unicode-Codierung für die Textdarstellung und die Berechnung der Zeichenanzahl. Anforderungen können in UTF-8 und UTF-16 übermittelt werden, dabei gibt es keine messbaren Unterschiede bei der Zeichenanzahl. Unicode-Codepunkte werden als Heuristik für die Zeichenlänge verwendet und werden für die Grenzwerte der Textanalysedaten als äquivalent betrachtet. Wenn Sie [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) verwenden, um die Zeichenanzahl abzurufen, verwenden Sie die gleiche Methode wie wir, um die Datengröße zu ermitteln.

## <a name="next-steps"></a>Nächste Schritte

+ [Erstellen Sie eine Azure-Ressource](../cognitive-services-apis-create-account.md) für die Textanalyse, um einen Schlüssel und einen Endpunkt für Ihre Anwendungen zu erhalten.

+ Verwenden Sie die [Schnellstartanleitung](quickstarts/client-libraries-rest-api.md), um mit dem Senden von API-Aufrufen zu beginnen. Erfahren Sie, wie Sie mit minimalem Code Text übermitteln, eine Analyse auswählen und Ergebnisse anzeigen.

+ Informationen zu neuen Releases und Features finden Sie unter [What's new in the Text Analytics API?](whats-new.md) (Neuigkeiten bei der Textanalyse-API).

+ In [diesem Tutorial zur Standpunktanalyse](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) mit Azure Databricks erhalten Sie ausführlichere Informationen.

+ Eine Liste von Blogbeiträgen und Videos, in denen die Verwendung der Textanalyse-API mit anderen Tools und Technologien veranschaulicht wird, finden Sie auf der Seite [Externe Inhalte und Community-Inhalte für die Textanalyse in Cognitive Service](text-analytics-resource-external-community.md).
