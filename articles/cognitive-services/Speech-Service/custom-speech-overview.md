---
title: 'Übersicht über Custom Speech: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Bei Custom Speech handelt es sich um eine Reihe von Onlinetools, mit denen Sie die Genauigkeit der Spracherkennung von Microsoft für Ihre Anwendungen, Tools und Produkte bewerten und verbessern können.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: eur
ms.custom: contperf-fy21q2, references_regions
ms.openlocfilehash: 579c6d8b6a5bb506586f8f09feefff51743be80c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508965"
---
# <a name="what-is-custom-speech"></a>Was ist Custom Speech?

Custom Speech ermöglicht Ihnen das Bewerten und Verbessern der Genauigkeit der Spracherkennung von Microsoft für Ihre Anwendungen und Produkte. Nutzen Sie die in diesem Artikel angegebenen Links, um mit der Erstellung einer benutzerdefinierten Spracherkennung zu beginnen.

## <a name="whats-in-custom-speech"></a>Was ist in Custom Speech enthalten?

Bevor Sie Custom Speech verwenden können, benötigen Sie ein Azure-Konto und ein Abonnement für Speech-Dienste. Nachdem Sie ein Konto eingerichtet haben, können Sie Daten vorbereiten, Modelle trainieren und testen, die Erkennungsqualität überprüfen, die Genauigkeit bewerten und das benutzerdefinierte Spracherkennungsmodell schließlich bereitstellen und verwenden.

Die folgende Abbildung zeigt die Teile, aus denen sich der [Custom Speech-Bereich in Speech Studio](https://aka.ms/speechstudio/customspeech) zusammensetzt. Verwenden Sie die unten angegebenen Links, um mehr über die einzelnen Schritte zu erfahren.

![Abbildung der Komponenten, aus denen sich der Custom Speech-Bereich in Speech Studio zusammensetzt](./media/custom-speech/custom-speech-overview.png)

1. [Abonnieren und Erstellen eines Projekts:](#set-up-your-azure-account) Erstellen Sie ein Azure-Konto, und abonnieren Sie die Speech-Dienste. Durch dieses einheitliche Abonnement erhalten Sie Zugriff auf die Spracherkennung, Sprachsynthese, Sprachübersetzung und auf [Speech Studio](https://speech.microsoft.com/customspeech). Erstellen Sie dann mit Ihrem Abonnement für Speech-Dienste Ihr erstes Custom Speech-Projekt.

1. [Hochladen von Testdaten:](./how-to-custom-speech-test-and-train.md) Laden Sie Testdaten (Audiodateien) hoch, um das Spracherkennungsangebot von Microsoft für Ihre Anwendungen, Tools und Produkte zu bewerten.

1. [Überprüfen der Erkennungsqualität:](how-to-custom-speech-inspect-data.md) Verwenden Sie [Speech Studio](https://speech.microsoft.com/customspeech) zur Wiedergabe hochgeladener Audiodateien, und überprüfen Sie die Qualität der Spracherkennung Ihrer Testdaten. Informationen zu quantitativen Messungen finden Sie unter [Überprüfen von Daten](how-to-custom-speech-inspect-data.md).

1. [Bewerten und Verbessern der Genauigkeit:](how-to-custom-speech-evaluate-data.md) Bewerten und verbessern Sie die Genauigkeit des Spracherkennungsmodells. In [Speech Studio](https://speech.microsoft.com/customspeech) finden Sie eine *Wort-Fehler-Rate*, mit der Sie bestimmen können, ob weiteres Training erforderlich ist. Wenn Sie mit der Genauigkeit zufrieden sind, können Sie die Speech-Dienst-APIs direkt verwenden. Wenn Sie die Genauigkeit um durchschnittlich 5 – 20 % verbessern möchten, verwenden Sie die Registerkarte **Training** im Portal, um zusätzliche Trainingsdaten hochzuladen, z. B. Humantranskripte und zugehörigen Text.

1. [Trainieren und Bereitstellen eines Modells:](how-to-custom-speech-train-model.md) Verbessern Sie die Genauigkeit Ihres Spracherkennungsmodells, indem Sie schriftliche Transkripte (10 – 1.000 Stunden) und zugehörigen Text (< 200 MB) zusammen mit Ihren Audiotestdaten bereitstellen. Mithilfe dieser Daten kann das Spracherkennungsmodell trainiert werden. Testen Sie das Modell nach dem Training erneut. Wenn Sie mit dem Ergebnis zufrieden sind, können Sie das Modell an einem benutzerdefinierten Endpunkt bereitstellen.

## <a name="set-up-your-azure-account"></a>Einrichten Ihres Azure-Kontos

Sie benötigen ein Azure-Konto und ein Abonnement für die Speech-Dienste, um [Speech Studio](https://speech.microsoft.com/customspeech) zum Erstellen eines benutzerdefinierten Modells verwenden zu können. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](overview.md#try-the-speech-service-for-free).

Wenn Sie ein benutzerdefiniertes Modell mit **Audiodaten** trainieren möchten, wählen Sie eine der folgenden Regionen aus, in der dedizierte Hardware für das Training verfügbar ist. Dies reduziert die für das Training eines Modells erforderliche Zeit und ermöglicht es Ihnen, mehr Audiodaten für das Training zu verwenden. In diesen Regionen verwendet der Speech-Dienst bis zu 20 Stunden Audiodaten für das Training. In anderen Regionen werden nur bis zu 8 Stunden verwendet.

* Australien (Osten)
* Kanada, Mitte
* Indien, Mitte
* East US
* USA (Ost) 2
* USA Nord Mitte
* Nordeuropa
* USA Süd Mitte
* Asien, Südosten
* UK, Süden
* US Gov Arizona
* US Government, Virginia
* Europa, Westen
* USA, Westen 2

Nachdem Sie ein Azure-Konto und ein Abonnement für Speech-Dienste erstellt haben, müssen Sie sich bei [Speech Studio](https://speech.microsoft.com/customspeech) anmelden und Ihr Abonnement verknüpfen.

1. Melden Sie sich in [Speech Studio](https://aka.ms/speechstudio/customspeech) an.
1. Wählen Sie das Abonnement aus, das Sie bearbeiten möchten, und erstellen Sie ein Speech-Projekt.
1. Wenn Sie Ihr Abonnement ändern möchten, klicken Sie im oberen Menü auf die Zahnradschaltfläche.

## <a name="how-to-create-a-project"></a>So erstellen Sie ein Projekt

Inhalte wie Daten, Modelle, Tests und Endpunkte sind in [Speech Studio](https://speech.microsoft.com/customspeech) in *Projekten* organisiert. Jedes Projekt ist für eine Domäne und das Land/die Sprache spezifisch. Sie können beispielsweise ein Projekt für Callcenter erstellen, die Englisch in den USA verwenden.

Wählen Sie zum Erstellen Ihres ersten Projekts die **Spracherkennung/Custom Speech** aus, und klicken Sie dann auf **Neues Projekt**. Folgen Sie den Anweisungen des Assistenten, um Ihr Projekt zu erstellen. Nachdem Sie ein Projekt erstellt haben, sollten vier Registerkarten angezeigt werden: **Daten**, **Test**, **Training** und **Bereitstellung**. Verwenden Sie die Links unter [Nächste Schritte](#next-steps), um mehr über die Verwendung der einzelnen Registerkarten zu erfahren.

> [!IMPORTANT]
> [Speech Studio](https://aka.ms/speechstudio), ehemals als „Custom Speech-Portal“ bezeichnet, wurde vor Kurzem aktualisiert. Wenn Sie vorherige Daten, Modelle, Tests und veröffentlichte Endpunkte im CRIS.ai-Portal oder mit APIs erstellt haben, müssen Sie im neuen Portal ein neues Projekt erstellen, um eine Verbindung mit diesen alten Entitäten herzustellen.

## <a name="model-and-endpoint-lifecycle"></a>Lebenszyklus von Modell und Endpunkt

Ältere Modelle werden in der Regel mit der Zeit weniger nützlich, da das neueste Modell in der Regel eine höhere Genauigkeit aufweist. Daher laufen Basismodelle sowie benutzerdefinierte Modelle und Endpunkte, die über das Portal erstellt wurden, nach einem Jahr für die Anpassung und nach zwei Jahren für die Decodierung ab. Eine ausführliche Beschreibung finden Sie im Artikel [Lebenszyklus des Modells und Endpunkt](./how-to-custom-speech-model-and-endpoint-lifecycle.md).

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md)
* [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
* [Bewerten und Verbessern der Modellgenauigkeit](how-to-custom-speech-evaluate-data.md)
* [Trainieren und Bereitstellen eines Modells](how-to-custom-speech-train-model.md)
