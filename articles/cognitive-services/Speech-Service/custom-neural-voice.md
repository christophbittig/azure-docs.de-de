---
title: 'Übersicht über benutzerdefinierte neuronale Stimme: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Die benutzerdefinierte neuronale Stimme ist ein Sprachsynthesefeature, mit dem Sie eine einzigartige, benutzerdefinierte synthetische Stimme für Ihre Anwendungen erstellen können, indem Sie Ihre eigenen Audiodaten als Beispiel bereitstellen.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: eur
ms.openlocfilehash: a4893f4f6205c79c22c0edf08059529bb7e00fcc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509193"
---
# <a name="what-is-custom-neural-voice"></a>Worum handelt es sich bei „Benutzerdefinierte neuronale Stimme“?

„Benutzerdefinierte neuronale Stimme“ ist ein TTS-Feature (Sprachsynthese), mit dem Sie eine einzigartige, benutzerdefinierte synthetische Stimme für Ihre Anwendungen erstellen können. Mit „Benutzerdefinierte neuronale Stimme“ können Sie eine äußerst natürlich klingende Stimme erstellen, indem Sie Ihre Audiobeispiele als Trainingsdaten bereitstellen. Basierend auf der neuronalen TTS-Technologie und dem mehrsprachigen universellen Modell mit mehreren Sprechern können Sie mit „Benutzerdefinierte neuronale Stimme“ synthetische Stimmen erstellen, die eine Vielzahl von Sprechstilen aufweisen oder sprachübergreifend angepasst werden können. Die realistische und natürlich klingende Stimme der benutzerdefinierten neuronalen Stimme kann Marken repräsentieren, Computer personifizieren und es Benutzern ermöglichen, mit Anwendungen zu interagieren. Informationen zu „Benutzerdefinierte neuronale Stimme“ und sprachübergreifende Features finden Sie unter den unterstützten [Sprachen](language-support.md#customization).

> [!NOTE]
> Das Feature „Benutzerdefinierte neuronale Stimme“ erfordert eine Registrierung, und der Zugriff darauf ist auf der Grundlage der Berechtigungs- und Verwendungskriterien von Microsoft eingeschränkt. Kunden, die dieses Feature verwenden möchten, müssen ihre Anwendungsfälle über das [Aufnahmeformular](https://aka.ms/customneural) registrieren.

## <a name="the-basics-of-custom-neural-voice"></a>Grundlagen der benutzerdefinierten neuronalen Stimme

Die zugrunde liegende neuronale TTS-Technologie (Sprachsynthese), die für die benutzerdefinierte neuronale Stimme verwendet wird, besteht aus drei Hauptkomponenten: Textanalyse, neuronales Akustikmodell und neuronaler Vocoder. Um eine natürliche synthetische Stimme aus Text zu erzeugen, wird der Text zunächst in die Textanalyse eingegeben, die eine Ausgabe in Form einer Phonemfolge liefert. Ein Phonem ist eine grundlegende Lauteinheit, die ein Wort in einer bestimmten Sprache von einem anderen unterscheidet. Eine Folge von Phonemen definiert die Aussprache der im Text vorgesehenen Wörter. 

Als nächstes wechselt die Phonemfolge in das neuronale Akustikmodell, um akustische Features vorherzusagen, die Sprachsignale definieren, z. B. die Klangfarbe, die Sprechweise, die Geschwindigkeit, Intonationen und Betonungsmuster. Schließlich wandelt der neuronale Vocoder die akustischen Features in hörbare Wellen um, sodass synthetische Sprache erzeugt wird.

![Einführendes Bild für benutzerdefinierte neuronale Stimme.](./media/custom-voice/cnv-intro.png)

Neuronale TTS-Sprachmodelle werden mithilfe von Deep Neural Networks trainiert, die auf den Aufnahmebeispielen menschlicher Stimmen basieren. In diesem [Blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911) wird beschrieben, wie die neuronale Sprachsynthese mit modernsten neuronalen Sprachsynthesemodellen funktioniert. Im Blog wird auch erläutert, wie ein universelles Basismodell an die Stimme eines Sprechers mit weniger als zwei Stunden Sprachdaten (oder weniger als 2.000 aufgezeichneten Äußerungen) angepasst und zusätzlich die Stimme in eine andere Sprache oder einen anderen Stil übertragen werden kann. Wie ein neuronaler Vocoder trainiert wird, erfahren Sie im [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Mit „Benutzerdefinierte neuronale Stimme“ können Sie die Engine für die neuronale Sprachsynthese an Ihre Szenarien anpassen. Um eine benutzerdefinierte neuronale Stimme zu erstellen, verwenden Sie [Speech Studio](https://speech.microsoft.com/customvoice), um die aufgenommenen Audiodaten und die entsprechenden Skripts hochzuladen, das Modell zu trainieren und die Stimme an einem benutzerdefinierten Endpunkt bereitzustellen. „Benutzerdefinierte neuronale Stimme“ kann vom Benutzer bereitgestellten Text verwenden, um Text in Echtzeit in Sprache zu konvertieren oder Audioinhalte offline mit Texteingaben zu generieren. Dies wird über die [REST-API](./rest-text-to-speech.md), das [Speech SDK](./get-started-text-to-speech.md) oder das [Webportal](https://speech.microsoft.com/audiocontentcreation) zur Verfügung gestellt.

## <a name="get-started"></a>Erste Schritte

* Informationen zu den ersten Schritten mit „Benutzerdefinierte neuronale Stimme“ und zum Erstellen eines Projekts finden Sie unter [Erste Schritte mit „Benutzerdefinierte neuronale Stimme“](how-to-custom-voice.md).
* Informationen zum Vorbereiten und Hochladen Ihrer Audiodaten finden Sie unter [Vorbereiten von Trainingsdaten](how-to-custom-voice-prepare-data.md).
* Informationen zum Trainieren und Bereitstellen Ihrer Modelle finden Sie unter [Erstellen und Verwenden Ihres Sprachmodells](how-to-custom-voice-create-voice.md).

## <a name="terms-and-definitions"></a>Begriffe und Definitionen

| **Begriff**      | **Definition**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stimmmodell   | Ein Sprachsynthesemodell, das die eindeutigen stimmlichen Merkmale eines Zielsprechers imitieren kann. Ein *Stimmmodell* wird auch als *Voicefont* oder *synthetische Stimme* bezeichnet. Ein Stimmmodell ist ein Satz von Parametern im Binärformat, der nicht für Menschen lesbar ist und keine Audioaufnahmen enthält. Eine Rückentwicklung (Reverse Engineering) ist nicht möglich, um den Klang einer menschlichen Stimme abzuleiten oder zu konstruieren. |
| Sprecher  | Einzelpersonen oder Zielsprecher, deren Stimmen aufgenommen und verwendet werden, um Stimmmodelle zu erstellen, die wie die Stimme des Sprechers klingen sollen.                                                                                                                                                                                                                                                   |
| Standard-TTS  | Die Standard- oder „traditionelle“ TTS-Methode, bei der gesprochene Sprache in phonetische Codeausschnitte unterteilt wird, sodass sie mithilfe klassischer Programmierung oder statistischer Methoden neu gemischt und angepasst werden können.                                                                                                                                                                                                    |
| Neuronales Text-to-Speech    | Die neuronale Sprachsynthese synthetisiert Sprache mithilfe von Deep Neural Networks, die „gelernt“ haben, wie die Phonetik in der natürlichen menschlichen Sprache kombiniert wird, anstatt prozedurale Programmierung oder statistische Methoden zu verwenden. Zusätzlich zu den Aufzeichnungen eines Zielsprechers verwendet die neuronale Sprachsynthese eine Quellbibliothek bzw. ein Basismodell, die/das mithilfe von Stimmaufzeichnungen von vielen verschiedenen Sprechern erstellt wurde.          |
| Trainingsdaten | Ein benutzerdefiniertes neuronales Dataset für das Stimmtraining, das die Audioaufnahmen des Sprechers und die zugehörigen Texttranskriptionen enthält.                                                                                                                                                                                                                                                               |
| Persona       | Eine Persona beschreibt, wer diese Stimme sein soll. Ein guter Persona-Entwurf ist die Grundlage für die gesamte Stimmerstellung, unabhängig davon, ob Sie ein bereits erstelltes Stimmmodell auswählen oder mit der Umwandlung und Aufnahme eines neuen Sprechers von Grund auf beginnen.                                                                                                |
| Skript        | Ein Skript ist eine Textdatei, die die Äußerungen enthält, die von Ihrem Sprecher gesprochen werden sollen. (Der Begriff „*Äußerungen*“ umfasst sowohl vollständige Sätze als auch kürzere Wendungen).                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Verantwortungsvolle Verwendung von KI

Wie Sie die benutzerdefinierte neuronale Stimme verantwortungsvoll verwenden können, erfahren Sie im [Transparenzhinweis](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Die Transparenzhinweise von Microsoft sollen Ihnen helfen zu verstehen, wie unsere KI-Technologie funktioniert, welche Entscheidungen Systembesitzer treffen können, die die Systemleistung und das Systemverhalten beeinflussen, und wie wichtig es ist, das gesamte System zu betrachten, einschließlich der Technologie, der Menschen und der Umgebung.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit „Benutzerdefinierte neuronale Stimme“](how-to-custom-voice.md)
