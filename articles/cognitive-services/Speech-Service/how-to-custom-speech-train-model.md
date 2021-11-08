---
title: Trainieren und Bereitstellen eines Custom Speech-Modells – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Custom Speech-Modelle trainieren und bereitstellen können. Das Training einer Spracherkennung kann die Erkennungsgenauigkeit sowohl für das Microsoft-Basismodell als auch für ein benutzerdefiniertes Modell verbessern.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: eur
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7f0e4f45659d77400f21d6ff056c1b8d5131fca
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509174"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Trainieren und Bereitstellen eines Custom Speech-Modells

In diesem Artikel erfahren Sie, wie Sie Custom Speech-Modelle trainieren und bereitstellen können. Das Training einer Spracherkennung kann die Erkennungsgenauigkeit für das Microsoft-Basismodell verbessern. Sie verwenden die Humantranskription sowie zugehörigen Text,um ein Modell zu trainieren. Diese Datasets werden zusammen mit zuvor hochgeladenen Audiodaten verwendet, um das Spracherkennungsmodell zu verfeinern und zu trainieren.

## <a name="use-training-to-resolve-accuracy-problems"></a>Lösen von Genauigkeitsproblemen durch Training

Wenn Sie bei einem Basismodell auf Erkennungsprobleme stoßen, kann die Verwendung von Humantranskripten und zugehörigen Daten zum Training eines benutzerdefinierten Modells dazu beitragen, die Genauigkeit zu verbessern. Bestimmen Sie anhand von dieser Tabelle, welches Dataset zum Beheben Ihrer Probleme verwendet werden soll:

| Anwendungsfall | Datentyp |
| -------- | --------- |
| Verbessern der Erkennungsgenauigkeit für branchenspezifisches Vokabular und entsprechende Grammatik (z. B. aus der Medizin- oder IT-Branche) | Nur-Text oder strukturierte Textdaten |
| Definieren der phonetischen und angezeigten Form eines Worts oder Begriffs mit nicht standardmäßiger Aussprache (beispielsweise Produktnamen oder Akronyme) | Aussprache-Daten oder phonetische Aussprache in strukturiertem Text |
| Verbessern der Erkennungsgenauigkeit für Sprechweisen, Akzente oder bestimmte Hintergrundgeräusche | Audio + menschenmarkierte Transkripte |

## <a name="train-and-evaluate-a-model"></a>Trainieren und Bewerten eines Modells

Der erste Schritt beim Trainieren eines Modells ist das Hochladen von Trainingsdaten. Unter [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md) finden Sie ausführliche Anweisungen zum Vorbereiten von Humantranskriptionen und zugehörigem Text (Äußerungen und Aussprache). Nachdem Sie Trainingsdaten hochgeladen haben, folgen Sie diesen Anweisungen, um mit dem Trainieren Ihres Modells zu beginnen:

1. Melden Sie sich beim [Custom Speech-Portal](https://speech.microsoft.com/customspeech) an. Wenn Sie ein Modell mit Audiodatasets und mit aus Humantranskriptionen bestehenden Datasets trainieren möchten, wählen Sie ein Speech-Abonnement in einer [Region mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account) für das Training aus.
2. Navigieren Sie zu **Spracherkennung** > **Custom Speech** >  **[Projektname]**  > **Training**.
3. Wählen Sie **Modell trainieren** aus.
4. Geben Sie einen **Namen** und eine **Beschreibung** für Ihr Training ein.
5. Wählen Sie aus der Liste für **Szenario und Basismodell** das für Ihre Domäne am besten geeignete Szenario aus. Wenn Sie sich nicht sicher sind, welches Szenario Sie wählen sollen, wählen Sie **Allgemein** aus. Das Basismodell stellt den Ausgangspunkt für das Training dar. Das neueste Modell ist in der Regel die beste Wahl.
6. Wählen Sie auf der Seite **Trainingsdaten auswählen** ein oder mehrere Datasets mit zugehörigem Text oder Audiodatasets und Humantranskriptionsdatasets aus, die Sie für das Training verwenden möchten.

> [!NOTE]
> Wenn Sie ein neues Modell trainieren, starten Sie mit zugehörigem Text. Das Training mit Audiodaten und Humantranskriptionen kann deutlich länger dauern **(bis zu [mehreren Tagen](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)** ).

> [!NOTE]
> Nicht alle Basismodelle unterstützen das Training mit Audiodaten. Wenn es von einem Basismodell nicht unterstützt wird, verwendet der Speech-Dienst nur den Text aus den Transkriptionen und ignoriert die Audiodaten. Eine Liste mit Basismodellen, die das Training mit Audiodaten unterstützen, finden Sie unter [Sprachunterstützung](language-support.md#speech-to-text).

> [!NOTE]
> Wenn Sie das für das Training verwendete Basismodell ändern und das Trainingsdataset Audiodaten enthält, überprüfen Sie *immer*, ob das neue ausgewählte Basismodell [das Training mit Audiodaten unterstützt](language-support.md#speech-to-text). Wenn das zuvor verwendete Basismodell kein Training mit Audiodaten unterstützt hat und das Trainingsdataset Audiodaten enthält, verlängert sich die Trainingsdauer mit dem neuen Basismodell **deutlich** und kann schnell einige Stunden oder sogar mehrere Tage und mehr betragen. Dies ist insbesondere dann der Fall, wenn es sich bei der Region Ihres Abonnements für den Speech-Dienst **nicht** um eine [Region mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account) für Training handelt.
>
> Wenn das im obigen Abschnitt beschriebene Problem auftritt, können Sie die Trainingsdauer schnell verkürzen, indem Sie die Menge der Audiodaten im Dataset verringern oder die Audiodaten ganz daraus entfernen, sodass das Dataset nur noch Text enthält. Letzteres ist sehr empfehlenswert, wenn es sich bei der Region Ihres Abonnements für den Speech-Dienst **nicht** um eine [Region mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account) für Training handelt.

7. Nachdem das Training abgeschlossen ist, können Sie Genauigkeitsprüfungen für das neu trainierte Modell ausführen. Dieser Schritt ist optional.
8. Wählen Sie **Erstellen** aus, um ein benutzerdefiniertes Modell zu erstellen.

In der **Trainingstabelle** wird ein neuer Eintrag angezeigt, der diesem neuen Modell entspricht. Außerdem zeigt die Tabelle den Status an: **Verarbeitung**, **Erfolgreich** oder **Fehler**.

Weitere Informationen zur Bewertung und Verbesserung der Genauigkeit des Custom Speech-Modells finden Sie in dieser [Schrittanleitung](how-to-custom-speech-evaluate-data.md). Wenn Sie sich für einen Genauigkeitstest entscheiden, ist es wichtig, ein anderes akustisches Dataset auszuwählen, als Sie für Ihr Modell verwendet haben, um ein realistisches Bild von der Leistung des Modells zu erhalten.

> [!NOTE]
> Sowohl Basismodelle als auch benutzerdefinierte Modelle können nur bis zu einem bestimmten Datum verwendet werden (siehe [Lebenszyklus von Modellen und Endpunkten](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). In Speech Studio wird dieses Datum in der Spalte **Ablaufdatum** für jedes Modell und jeden Endpunkt angezeigt. Nach diesem Datum treten für Anforderungen an einen Endpunkt oder für eine Batchtranskription möglicherweise Fehler auf, oder es wird ein Fallback zu einem Basismodell durchgeführt.
>
> Trainieren Sie Ihr Modell mithilfe des aktuellen Basismodells noch mal, um die Vorteile der Verbesserungen der Genauigkeit zu nutzen und zu vermeiden, dass Ihr Modell sein Ablaufdatum erreicht.

## <a name="deploy-a-custom-model"></a>Bereitstellen eines benutzerdefinierten Modells

Nachdem Sie Daten hochgeladen und überprüft, die Genauigkeit ausgewertet und ein benutzerdefiniertes Modell trainiert haben, können Sie einen benutzerdefinierten Endpunkt zur Verwendung mit Ihren Apps, Tools und Produkten bereitstellen. 

Melden Sie sich zum Erstellen eines benutzerdefinierten Endpunkts beim [Custom Speech-Portal](https://speech.microsoft.com/customspeech) an. Wählen Sie **Bereitstellung** im Menü **Custom Speech** am oberen Rand der Seite aus. Wenn dies Ihre erste Ausführung ist, werden Sie feststellen, dass in der Tabelle keine Endpunkte aufgeführt sind. Nachdem Sie einen Endpunkt erstellt haben, verwenden Sie diese Seite zum Nachverfolgen der einzelnen bereitgestellten Endpunkte.

Wählen Sie als Nächstes **Endpunkt hinzufügen** aus, und geben Sie einen **Namen** und eine **Beschreibung** für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie dann das benutzerdefinierte Modell aus, das Sie diesem Endpunkt zuordnen möchten.  Auf dieser Seite können Sie auch die Protokollierung aktivieren. Die Protokollierung ermöglicht es Ihnen, den Datenverkehr des Endpunkts zu überwachen. Wenn die Protokollierung deaktiviert ist, wird Datenverkehr nicht gespeichert.

![Screenshot, der die neue Endpunktseite anzeigt](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Denken Sie daran, die Nutzungsbedingungen und Preise zu akzeptieren.

Wählen Sie als Nächstes die Option **Erstellen**. Durch diese Aktion kehren Sie zur Seite **Bereitstellung** zurück. Die Tabelle enthält jetzt einen Eintrag für Ihren benutzerdefinierten Endpunkt. Der Endpunktstatus zeigt den aktuellen Zustand. Es kann bis zu 30 Minuten dauern, bis ein neuer Endpunkt mit Ihren benutzerdefinierten Modellen instanziiert wurde. Sobald sich der Bereitstellungsstatus in **Abgeschlossen** ändert, kann der Endpunkt verwendet werden.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode). Notieren Sie sich das Ablaufdatum, und aktualisieren Sie das Modell des Endpunkts vor diesem Datum, damit der Dienst unterbrechungsfrei ausgeführt werden kann.

## <a name="view-logging-data"></a>Anzeigen von Protokolldaten

Sie können die Protokolldaten exportieren, indem Sie unter **Bereitstellungen** zur Seite des Endpunkts navigieren.
> [!NOTE]
>Protokolldaten stehen 30 Tage lang in Microsoft-eigenem Speicher zur Verfügung. Nach diesem Zeitraum werden sie entfernt. Falls ein kundeneigenes Speicherkonto mit dem Cognitive Services-Abonnement verknüpft ist, werden die Protokolldaten nicht automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Ihr benutzerdefiniertes Modell verwenden](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md)
- [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
- [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
