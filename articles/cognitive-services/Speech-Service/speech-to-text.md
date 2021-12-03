---
title: 'Übersicht über die Spracherkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Die Spracherkennungssoftware (Sprache-zu-Text) ermöglicht die Echtzeittranskription von Audiostreams in Text. Diese Texteingaben können von Ihren Anwendungen, Tools oder Geräten verwendet, angezeigt und verarbeitet werden. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Spracherkennungsdiensts.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: eur
ms.custom: cog-serv-seo-aug-2020
keywords: Spracherkennung, Spracherkennungssoftware
ms.openlocfilehash: bf0d8c7ffdff3c936804583f03b42dbe1c5e2392
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488390"
---
# <a name="what-is-speech-to-text"></a>Was ist die Spracherkennung?

In dieser Übersicht lernen Sie die Vorteile und Funktionen des Spracherkennungsdiensts kennen.
Die Spracherkennung (Sprache zu Text) ermöglicht die Echtzeittranskription von Audiostreams in Text. Diese Texteingaben können von Ihren Anwendungen, Tools oder Geräten verwendet, angezeigt und als Befehlseingabe verarbeitet werden. Dieser Dienst nutzt dieselbe Erkennungstechnologie, die Microsoft auch bei Cortana und Office-Produkten einsetzt. Er funktioniert nahtlos mit den Dienstangeboten für <a href="./speech-translation.md" target="_blank">Übersetzung</a> und <a href="./text-to-speech.md" target="_blank">Sprachsynthese</a>. Eine vollständige Liste der Spracherkennungssprachen finden Sie unter [Unterstützte Sprachen](language-support.md#speech-to-text).

Der Spracherkennungsdienst verwendet standardmäßig das sogenannte Universal Language Model. Dieses Modell wurde mit Microsoft-Daten trainiert, und es wird in der Cloud bereitgestellt. Es eignet sich besonders für Gesprächs- oder Diktatszenarios. Wenn Sie die Spracherkennung für die Erkennung und Transkription in einer individuellen Umgebung verwenden, können Sie benutzerdefinierte Akustik-, Sprach- und Aussprachemodelle erstellen und trainieren. Anpassungen sind hilfreich für das Kompensieren von Umgebungsgeräuschen oder bei branchenspezifischem Vokabular.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* **Schnellstarts** sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* **Anleitungen** enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die Artikel zu **Konzepten** enthalten ausführliche Erläuterungen der Dienstfunktionen und -features.
* **Tutorials** sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

> [!NOTE]
> Die Bing-Spracheingabe wurde am 15. Oktober 2019 eingestellt. Wenn Ihre Anwendungen, Tools oder Produkte die Bing-Spracheingabe-APIs verwenden, finden Sie in den nachfolgend aufgelisteten Leitfäden Informationen zur Migration zum Speech-Dienst:
> - [Migrieren von der Bing-Spracheingabe zum Speech-Dienst](how-to-migrate-from-bing-speech.md)

## <a name="get-started"></a>Erste Schritte

Verwenden Sie den [Schnellstart](get-started-speech-to-text.md), um mit der Spracherkennung zu beginnen. Der Dienst ist über das [Speech SDK](speech-sdk.md), die [REST-API](rest-speech-to-text.md#pronunciation-assessment-parameters) und die [Speech CLI](spx-overview.md) verfügbar.

## <a name="sample-code"></a>Beispielcode

Beispielcode für das Speech SDK finden Sie auf GitHub. In den Beispielen werden gängige Szenarien wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und anfängliche Erkennung oder die Verwendung benutzerdefinierter Modelle behandelt.

- [Speech-to-text samples (SDK) (Spracherkennungsbeispiele (SDK))](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST) (Batchtranskriptionsbeispiele (REST))](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Beispiele für die Aussprachebewertung (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Anpassung

Zusätzlich zum Standardmodell des Speech-Diensts können Sie auch benutzerdefinierte Modelle erstellen. Mithilfe von Anpassungen überwinden Sie Grenzen der Spracherkennung wie z. B. Sprachstil, Vokabular und Hintergrundgeräusche. Weitere Informationen finden Sie unter [Custom Speech](./custom-speech-overview.md). Die Anpassungsoptionen variieren je nach Sprache/Gebietsschema. Weitere Informationen zur Unterstützung erhalten Sie unter [Unterstützte Sprachen](./language-support.md).

## <a name="batch-transcription"></a>Batch-Transkription

Bei der Batchtranskription handelt es sich um eine Reihe von Rest-API-Vorgängen, mit denen Sie große Mengen von Audiodaten im Speicher transkribieren können. Sie können per SAS-URI (Shared Access Signature) auf Audiodateien verweisen und Transkriptionsergebnisse asynchron empfangen. Weitere Informationen zur Verwendung der Batchtranskriptions-API finden Sie in [diesem Artikel](batch-transcription.md).

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
- [Abrufen des Speech SDK](speech-sdk.md)
