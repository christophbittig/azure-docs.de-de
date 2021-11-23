---
title: Übersicht zur Sprachübersetzung – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Mit der Sprachübersetzung können Sie Ihren Anwendungen, Tools und Geräten End-to-End- und Echtzeit-Sprachübersetzungen sowie mehrsprachige Übersetzungen hinzufügen. Die gleiche API kann für Speech-to-Speech- und für Speech-to-Text-Übersetzungen verwendet werden. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Sprachübersetzungsdiensts.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: eur
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: Sprachübersetzung
ms.openlocfilehash: 3c71682028f1fb54b55e9faddde5928883f44916
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485564"
---
# <a name="what-is-speech-translation"></a>Was ist Sprachübersetzung?

In dieser Übersicht lernen Sie die Vorteile und Funktionen des Sprachübersetzungsdiensts kennen, der eine [mehrsprachige Sprache-zu-Sprache-Übersetzung](language-support.md#speech-translation) und eine Sprache-zu-Text-Übersetzung von Audiostreams in Echtzeit ermöglicht. Mit dem Speech SDK haben Ihre Anwendungen, Tools und Geräte Zugriff auf Quelltranskriptionen und Übersetzungsausgaben für bereitgestelltes Audio. Die Zwischenergebnisse der Transkription und Übersetzung werden zurückgegeben, wenn Sprache erkannt wird, und die Endergebnisse können in synthetisierte Sprache konvertiert werden.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* **Schnellstarts** sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* **Anleitungen** enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die Artikel zu **Konzepten** enthalten ausführliche Erläuterungen der Dienstfunktionen und -features.
* **Tutorials** sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="core-features"></a>Wichtige Funktionen

* Sprache-zu-Text-Übersetzung mit Erkennungsergebnissen.
* Sprache-zu-Sprache-Übersetzung.
* Unterstützung des Übersetzens von Sprache in mehrere Zielsprachen.
* Zwischenergebnisse der Spracherkennung und Übersetzung.

## <a name="get-started"></a>Erste Schritte 

Verwenden Sie die [Schnellstartanleitung](get-started-speech-translation.md), um mit der Sprachübersetzung zu beginnen. Der Sprachübersetzungsdienst ist über das [Speech SDK](speech-sdk.md) und die [Speech CLI](spx-overview.md) verfügbar.

## <a name="sample-code"></a>Beispielcode

Beispielcode für das Speech SDK finden Sie auf GitHub. In den Beispielen werden gängige Szenarien wie etwa das Lesen von Audiodaten aus einer Datei oder einem Stream, die kontinuierliche und anfängliche Erkennung/Übersetzung oder die Verwendung benutzerdefinierter Modelle behandelt.

* [Beispiele für Spracherkennung und Übersetzung (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrationsleitfäden

Wenn Ihre Anwendungen, Tools oder Produkte die [Sprachübersetzungs-API](./how-to-migrate-from-translator-speech-api.md) verwenden, finden Sie in den nachfolgend aufgelisteten Leitfäden Informationen zur Migration zum Speech-Dienst.

* [Migrieren von der Sprachübersetzungs-API zum Speech-Dienst](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenz

* [Speech SDK](./speech-sdk.md)
* [Speech-Geräte-SDK](speech-devices-sdk.md)
* [REST-API: Spracherkennung](rest-speech-to-text.md)
* [REST-API: Sprachsynthese](rest-text-to-speech.md)
* [REST-API: Batchtranskription und Anpassung](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Nächste Schritte

* Ausführen des [Schnellstarts](get-started-speech-translation.md) zur Sprachübersetzung
* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Abrufen des Speech SDK](speech-sdk.md)
