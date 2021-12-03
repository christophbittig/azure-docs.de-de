---
title: Was ist Azure Cognitive Service for Language?
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie KI in Ihre Anwendungen integrieren, die Informationen extrahieren und geschriebene Sprache verstehen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0eb1b3a1248e4bccf8f726724b57cacb352b88e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485367"
---
# <a name="what-is-azure-cognitive-service-for-language"></a>Was ist Azure Cognitive Service for Language?

Azure Cognitive Service for Lange ist ein cloudbasierter Dienst, der Features zur Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) bietet, um Text zu verstehen und zu analysieren. Verwenden Sie diesen Dienst, um mithilfe der webbasierten Language Studio-Anwendung, von REST-APIs und Clientbibliotheken intelligente Anwendungen zu erstellen.  

Dieser Sprachdienst vereinheitlicht Textanalyse, QnA Maker und LUIS und bietet zudem mehrere neue Features. Diese Features können folgende Eigenschaften haben:
* Sie sind vorkonfiguriert. Das bedeutet, dass die vom Feature verwendeten KI-Modelle nicht anpassbar sind. Sie senden nur Ihre Daten und verwenden die Ausgabe des Features in Ihren Anwendungen.
* Sie sind anpassbar. Das bedeutet, Sie trainieren ein KI-Modell mit unseren Tools, die speziell an Ihre Daten angepasst werden.

## <a name="migrate-from-text-analytics-qna-maker-or-language-understanding"></a>Sie führen eine Migration von Textanalyse, QnA Maker oder Language Understanding aus?

Azure Cognitive Services for Language vereint drei einzelne Sprachdienste in Cognitive Services : Textanalyse, QnA Maker und Language Understanding (LUIS). Wenn Sie bisher diese drei Dienste verwendet haben, können Sie problemlos zum neuen Dienst „Azure Cognitive Services for Language“ migrieren. Entsprechende Anweisungen finden Sie unter [Migrieren zu Azure Cognitive Services for Language](concepts/migrate.md).  

## <a name="available-features"></a>Verfügbare Features

Azure Cognitive Service for Language bietet die folgenden Features:

> [!div class="mx-tdCol2BreakAll"]
> |Funktion  |BESCHREIBUNG  | Bereitstellungsoptionen| 
> |---------|---------|---------|
> | [Erkennung benannter Entitäten (NER)](named-entity-recognition/overview.md)     | Dieses vorkonfigurierte Feature identifiziert Entitäten im Text in mehreren vordefinierten Kategorien.        | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](named-entity-recognition/quickstart.md) |
> | [Erkennung personenbezogener Informationen (Personally Identifiable Information, PII)](personally-identifiable-information/overview.md)     | Dieses vorkonfigurierte Feature identifiziert Entitäten im Text in mehreren vordefinierten Kategorien vertraulicher Informationen, z. B. Kontoinformationen.        | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](named-entity-recognition/quickstart.md) |
> | [Schlüsselbegriffserkennung](key-phrase-extraction/overview.md)     | Dieses vorkonfigurierte Feature wertet unstrukturierten Text aus und gibt für jedes Eingabedokument eine Liste mit Schlüsselbegriffen und Hauptpunkten im Text zurück. | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](key-phrase-extraction/quickstart.md) <br> • [Docker-Container](key-phrase-extraction/how-to/use-containers.md)  |
> |[Entitätsverknüpfung](entity-linking/overview.md)    | Dieses vorkonfigurierte Feature bestimmt die Identität einer im Text gefundenen Entität eindeutig und stellt Links zur Entität auf Wikipedia bereit.        | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](entity-linking/quickstart.md) |
> | [Text Analytics for Health](text-analytics-for-health/overview.md)    | Dieses vorkonfigurierte Feature extrahiert Informationen aus unstrukturierten medizinischen Texten, z. B. aus klinischen Notizen und Arztbriefen.  | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](text-analytics-for-health/quickstart.md) <br> • [Docker-Container](text-analytics-for-health/how-to/use-containers.md) |
> | [Benutzerdefinierte Erkennung benannter Entitäten (Named Entity Recognition, NER)](custom-named-entity-recognition/overview.md)    | Erstellen Sie ein KI-Modell, um benutzerdefinierte Entitätskategorien mithilfe von unstrukturiertem Text zu extrahieren, den Sie bereitstellen. |  • [Language Studio](custom-named-entity-recognition/quickstart.md?pivots=language-studio) <br> • [REST-API](custom-named-entity-recognition/quickstart.md?pivots=rest-api) |
> | [Analysieren von Stimmung und Meinungen](sentiment-opinion-mining/overview.md)     | Dieses vorkonfigurierte Feature stellt Stimmungsbezeichnungen (z. B.*negativ*, *neutral* und *positiv*) für Sätze und Dokumente bereit. Dieses Feature kann darüber hinaus präzise Informationen zu den Meinungen im Zusammenhang mit Wörtern bereitstellen, die im Text vorkommen, z. B. die Attribute von Produkten oder Diensten. |  • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](sentiment-opinion-mining/quickstart.md) <br> • [Docker-Container](sentiment-opinion-mining/how-to/use-containers.md)
> |[Sprachenerkennung](language-detection/overview.md)    | Dieses vorkonfigurierte Feature wertet Text aus und bestimmt die Sprache, in der er geschrieben wurde. Es gibt einen Sprachbezeichner und eine Bewertung zurück, die die Stärke der Analyse angibt.        | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](language-detection/quickstart.md) <br> • [Docker-Container](language-detection/how-to/use-containers.md) |
> |[Benutzerdefinierte Textklassifizierung (Vorschau)](custom-classification/overview.md)    | Erstellen Sie ein KI-Modell, um unstrukturierten Text in benutzerdefinierte Klassen einzuteilen, die Sie definieren.         | • [Language Studio](custom-classification/quickstart.md?pivots=language-studio)<br> • [REST-API](language-detection/quickstart.md?pivots=rest-api) |
> | [Textzusammenfassung (Vorschau)](text-summarization/overview.md)     | Dieses vorkonfigurierte Feature extrahiert Schlüsselsätze, die zusammen das Wesentliche eines Dokuments vermitteln. | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](text-summarization/quickstart.md) |
> | [Unterhaltungssprachverständnis (Vorschau)](conversational-language-understanding/overview.md)   | Erstellen Sie ein KI-Modell, um die Fähigkeit, natürliche Sprache zu verstehen, in Apps, Bots und IoT-Geräte zu integrieren. | • [Language Studio](conversational-language-understanding/quickstart.md)
> | [Fragen und Antworten](question-answering/overview.md)     | Dieses vorkonfigurierte Feature bietet Antworten auf Fragen, die aus Texteingaben extrahiert werden. Dazu werden teilweise strukturierte Inhalte wie häufig gestellte Fragen, Handbücher und Dokumente verwendet. | • [Language Studio](language-studio.md) <br> • [REST-API und Clientbibliothek](question-answering/quickstart/sdk.md) |

## <a name="tutorials"></a>Lernprogramme

Nachdem Sie Gelegenheit hatten, erste Schritte mit dem Language-Dienst auszuführen, erfahren Sie in unseren Tutorials mehr zu verschiedenen Szenarien.

* [Extrahieren von Schlüsselwörtern aus Text, der in Power BI gespeichert ist](key-phrase-extraction/tutorials/integrate-power-bi.md)
* [Verwenden von Power Automate zum Sortieren von Informationen in Microsoft Excel](named-entity-recognition/tutorials/extract-excel-information.md) 
* [Übersetzen von Text, Analysieren der Stimmung und Synthetisieren von Sprache mithilfe von Flask](/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?context=/azure/cognitive-services/language-service/context/context)
* [Verwenden von Cognitive Services in Canvas-Apps](/powerapps/maker/canvas-apps/cognitive-services-api?context=/azure/cognitive-services/language-service/context/context)
* [Erstellen eines Bots für häufig gestellte Fragen](question-answering/tutorials/bot-service.md)

## <a name="additional-code-samples"></a>Weitere Codebeispiele

Weitere Codebeispiele für die folgenden Sprachen finden Sie auf GitHub:

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern 
Verwenden Sie Container für den Sprachdienst, um API-Features lokal bereitzustellen. Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden. Der Sprachdienst bietet folgende Container:

* [Standpunktanalyse](sentiment-opinion-mining/how-to/use-containers.md)
* [Sprachenerkennung](language-detection/how-to/use-containers.md)
* [Schlüsselbegriffserkennung](key-phrase-extraction/how-to/use-containers.md) 
* [Textanalyse für Gesundheit](text-analytics-for-health/how-to/use-containers.md)


## <a name="responsible-ai"></a>Verantwortungsbewusste künstliche Intelligenz 

Ein KI-System umfasst nicht nur die Technologie, sondern auch die Personen, die das System verwenden, die davon betroffenen Personen und die Umgebung, in der es bereitgestellt wird. Lesen Sie die folgenden Artikel, um mehr über verantwortungsvolle KI-Nutzung und -Bereitstellung in Ihren Systemen zu erfahren:

* [Transparenzhinweis für den Sprachdienst](/legal/cognitive-services/text-analytics/transparency-note)
* [Integration und verantwortungsvolle Verwendung](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [Daten, Datenschutz und Sicherheit](/legal/cognitive-services/text-analytics/data-privacy)
