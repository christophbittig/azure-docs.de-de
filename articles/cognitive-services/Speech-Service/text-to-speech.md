---
title: 'Übersicht über die Sprachsynthese: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Das Feature Text-zu-Sprache in Speech-Dienst ermöglicht es Ihren Anwendungen, Tools oder Geräten, Text in natürliche, menschenähnliche synthetisierte Sprache umzuwandeln. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Sprachsynthesediensts.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: eur
ms.custom: cog-serv-seo-aug-2020
keywords: Sprachsynthese
ms.openlocfilehash: c3a4caa638debfa32fa6d571b473b2bf5bd0cf09
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990374"
---
# <a name="what-is-neural-text-to-speech"></a>Was ist die neuronale Sprachsynthese?

Die neuronale Sprachsynthese von Microsoft verwenden Deep Neural Network-Instanzen, um die Stimmen von Computern so zu gestalten, dass sie von Aufzeichnungen von Personen nahezu nicht zu unterscheiden sind. Durch natürliche, menschenähnliche Intonation und klare Aussprache von Wörtern kann die neuronale Sprachsynthese die Hörermüdung bei der Interaktion mit KI-Systemen erheblich verringern.

Die Betonungs- und Intonationsmuster in gesprochener Sprache werden als _Satzrhythmus_ bezeichnet. Die traditionellen Sprachsynthesesysteme untergliedern den Satzrhythmus in separate linguistische Analyse- und akustische Vorhersageschritte, die von unabhängigen Modellen gesteuert werden. Dies kann zu einer dumpfen, dröhnenden Sprachsynthese führen. Die neuronale Sprachsynthesefunktion von Microsoft ermöglicht gleichzeitig die Satzrhythmusvorhersage und Sprachsynthese, verwendet Deep Neural Network-Instanzen, um die Grenzen herkömmlicher Sprachsynthesesysteme bei der Anpassung von Betonungs- und Intonationsmustern in gesprochener Sprache zu überwinden, und synthetisiert die Spracheinheiten in eine Computerstimme. Das Ergebnis ist eine flüssigere und natürlicher klingende Stimme.

In dieser Übersicht erfahren Sie mehr über die Vorteile und Funktionen des Sprachsynthesediensts, der Ihren Anwendungen, Tools oder Geräten das Konvertieren von Text in menschenähnliche synthetisierte Sprache ermöglicht. Verwenden Sie menschenähnliche neuronale Stimmen, oder erstellen Sie eine benutzerdefinierte Stimme, die für Ihr Produkt oder Ihre Marke exklusiv ist. Eine vollständige Liste der unterstützten Stimmen, Sprachen und Gebietsschemas finden Sie unter [unterstützte Sprachen](language-support.md#text-to-speech).

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* **Schnellstarts** sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* **Anleitungen** enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die Artikel zu **Konzepten** enthalten ausführliche Erläuterungen der Dienstfunktionen und -features.
* **Tutorials** sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

> [!NOTE]
>
> Die Bing-Spracheingabe wurde am 15. Oktober 2019 eingestellt. Wenn Ihre Anwendungen, Tools oder Produkte die Bing-Spracheingabe-APIs oder Custom Speech verwenden, finden Sie in den nachfolgend aufgelisteten Leitfäden Informationen zur Migration zum Speech-Dienst.
>
> * [Migrieren von der Bing-Spracheingabe zum Speech-Dienst](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Wichtige Funktionen

* Sprachsynthese: Verwenden Sie das [Speech SDK](./get-started-text-to-speech.md) oder die [REST-API](rest-text-to-speech.md), um mithilfe von [neuronalen Stimmen der Plattform](language-support.md#text-to-speech) oder [benutzerdefinierten neuronalen Stimmen](custom-neural-voice.md) Text in Sprache zu konvertieren.

* Asynchrone Synthese von langen Audioclips: Verwenden Sie die [API für lange Audioinhalte-](long-audio-api.md), um Text-to-Speech-Dateien von mehr als 10 Minuten Länge (z. B. Audiobücher oder Vorlesungen) asynchron zu synthetisieren. Anders als bei der Synthese mithilfe des Speech SDK oder der Sprache-zu-Text-REST API werden keine Antworten in Echtzeit zurückgegeben. Es wird davon ausgegangen, dass Anforderungen asynchron gesendet werden, Antworten abgerufen werden und dass das synthetisierte Audiosignal heruntergeladen wird, wenn es durch den Dienst zur Verfügung gestellt wird.

* Neuronale Stimmen der Plattform: Tiefe neuronale Netzwerke werden verwendet, um die Grenzen der herkömmlichen Sprachsynthese in Bezug auf Betonung und Intonation in gesprochener Sprache zu überwinden. Die Vorhersage der Satzgliederung und die Stimmensynthese werden gleichzeitig ausgeführt, sodass eine flüssigere und natürlicher klingende Ausgabe entsteht. Gestalten Sie mit neuronalen Stimmen Interaktionen mit Chatbots und Sprachassistenten noch natürlicher und einladender, wandeln Sie digitale Texte wie E-Books in Audiobooks um, und verpassen Sie Ihrem Navigationssystem im Auto ein Upgrade. Durch natürliche, menschenähnliche Intonation und klare Aussprache von Wörtern können neuronale Stimmen die Hörermüdung bei der Interaktion mit KI-Systemen erheblich verringern. Eine vollständige Liste der neuronalen Stimmen der Plattform finden Sie unter [Unterstützte Sprachen](language-support.md#text-to-speech).

* Optimieren der TTS-Ausgabe mit SSML: Die Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML) ist eine XML-basierte Markupsprache, mit der Ausgaben der Sprachsynthese angepasst werden. Mit SSML können Sie nicht nur die Tonhöhe anpassen, Pausen hinzufügen, die Aussprache verbessern, die Sprechgeschwindigkeit ändern, die Lautstärke anpassen und mehrere Stimmen einem einzelnen Dokument zuordnen, sondern auch eigene Lexika definieren oder zu verschiedenen Sprechstilen wechseln. Mit den [mehrsprachigen Stimmen](https://techcommunity.microsoft.com/t5/azure-ai/azure-text-to-speech-updates-at-build-2021/ba-p/2382981) können Sie auch die gesprochenen Sprachen über SSML anpassen. Informationen zum Optimieren der Sprachausgabe für Ihr Szenario finden Sie unter [Verwenden von SSML](speech-synthesis-markup.md).

* Viseme: [Viseme](how-to-speech-synthesis-viseme.md) (Mundbilder) sind die wichtigsten Gesichtsausdrücke beim Sprechen und umfassen u. a. die Position der Lippen, des Kiefers und der Zunge beim Erzeugen eines bestimmten Phonems. Viseme sind stark mit Stimmen und Phonemen korreliert. Sie können mit Visemereignissen im Speech SDK Gesichtsanimationsdaten generieren. Mit diesen lassen sich Gesichter für die Kommunikation durch Lippenlesen, für Unterricht, Unterhaltung oder Kundendienst animieren. Viseme werden derzeit nur für die [neuronalen Stimmen](language-support.md#text-to-speech) für `en-US` Englisch (USA) unterstützt.

## <a name="get-started"></a>Erste Schritte

Verwenden Sie die [Schnellstartanleitung](get-started-text-to-speech.md), um mit der Sprachsynthese (Text-zu-Sprache) loszulegen. Der Sprachsynthesedienst ist über das [Speech SDK](speech-sdk.md), die [REST-API](rest-text-to-speech.md) und die [Speech CLI](spx-overview.md) verfügbar.

## <a name="sample-code"></a>Beispielcode

Beispielcode für Text-zu-Sprache finden Sie auf GitHub. Diese Beispiele umfassen die Text-zu-Sprache-Konvertierung in den gängigsten Programmiersprachen.

* [Beispiele für Text-zu-Sprache (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Beispiele für Text-zu-Sprache (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Anpassung

Über die neuronalen Stimmen hinaus können Sie benutzerdefinierte Stimmen erstellen und optimieren, die für Ihr Produkt oder Ihre Marke exklusiv sind. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Weitere Informationen finden Sie unter [Erste Schritte mit „Benutzerdefinierte neuronale Stimme“](how-to-custom-voice.md).

## <a name="pricing-note"></a>Preishinweis

Bei der Nutzung des Sprachsynthesediensts wird Ihnen einschließlich Interpunktion jedes Zeichen in Rechnung gestellt, das in Sprache umgewandelt wird. Während das SSML-Dokument selbst nicht abrechenbar ist, werden optionale Elemente wie Phoneme und Tonhöhe, mit denen eingestellt wird, wie der Text in Sprache umgewandelt wird, als abrechenbare Zeichen gezählt. Folgendes wird abgerechnet:

* Text, der dem Sprachsynthesedienst im SSML-Text der Anforderung übergeben wird
* Alle Markups im Textfeld des Anforderungstexts im SSML-Format, mit Ausnahme von `<speak>`- und `<voice>`-Tags
* Buchstaben, Satzzeichen, Leerzeichen, Tabulatoren, Markups und alle sonstigen Leerzeichen
* Jeder in Unicode definierte Codepunkt

Ausführliche Informationen finden Sie unter [Cognitive Services-Preise – Sprachdienste](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Chinesische, japanische und koreanische Zeichen zählen bei der Abrechnung jeweils als zwei Zeichen.

## <a name="migrate-to-neural-voice"></a>Migrieren in eine neuronale Stimme

Wir stellen die Standardstimmen am **31. August 2024** ein und sie werden nach diesem Datum nicht mehr unterstützt.Die Ankündigung wurde an alle vor dem **31. August 2021** vorhandenen Speech-Abonnements gesendet. Während des Außerbetriebnahmezeitraums (**31. August 2021 bis 31. August 2024)** können vorhandene Standardstimmenbenutzer*innen weiterhin ihre Standardstimmen verwenden. Alle neuen Benutzer*innen bzw. neuen Sprachressourcen sollten zu den neuronalen Stimmen wechseln.

**Erforderliche Aktion**

1. Überprüfen Sie die [Preisstruktur](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/), und hören Sie sich die [Beispiele](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#overview) für neuronale Stimmen am Ende der Seite an, um die richtige Stimme für Ihre Geschäftsanforderungen zu finden.
1. Um die Änderung vorzunehmen, [folgen Sie dem Beispielcode](speech-synthesis-markup.md#choose-a-voice-for-text-to-speech), um den Stimmennamen in Ihrer Sprachsyntheseanforderung bis zum 31. August 2024 auf die unterstützten neuronalen Stimmennamen in den ausgewählten Sprachen zu aktualisieren. **Ab dem 1. September 2024** werden Standardstimmen nicht mehr unterstützt. Verwenden Sie neuronale Stimmen für Ihre Sprachsyntheseanforderungen (in der Cloud oder lokal). Für lokale Container verwenden Sie die [neuronalen Sprachcontainer](../containers/container-image-tags.md), und befolgen Sie die [Anweisungen](speech-container-howto.md).

## <a name="reference-docs"></a>Referenz

* [Speech SDK](speech-sdk.md)
* [REST-API: Sprachsynthese](rest-text-to-speech.md)

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen eines kostenlosen Speech-Dienstabonnements](overview.md#try-the-speech-service-for-free)
* [Abrufen des Speech SDK](speech-sdk.md)
