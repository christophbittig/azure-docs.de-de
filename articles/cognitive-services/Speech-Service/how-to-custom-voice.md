---
title: 'Erste Schritte mit „Benutzerdefinierte neuronale Stimme“: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Bei „Benutzerdefinierte neuronale Stimme“ handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: eur
ms.openlocfilehash: f521f1d08124575553c32c8b6fb4c3931ddcaa6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510261"
---
# <a name="get-started-with-custom-neural-voice"></a>Erste Schritte mit „Benutzerdefinierte neuronale Stimme“

Bei [Benutzerdefinierte neuronale Stimme](https://aka.ms/customvoice) handelt es sich um eine Reihe von Onlinetools, mit denen Sie eine wiedererkennbare, einzigartige Stimme für Ihre Marke erstellen können. Für den Einstieg benötigen Sie lediglich einige Audiodateien und die dazugehörigen Transkriptionen. Nutzen Sie die unten angegebenen Links, um mit dem Erstellen einer benutzerdefinierten Sprachsynthese zu beginnen. Hier finden Sie Informationen zu den unterstützten [Sprachen](language-support.md#customization) und [Regionen](regions.md#custom-voices) für „Benutzerdefinierte neuronale Stimme“.

> [!NOTE]
> Im Rahmen der Zusage von Microsoft zu einem verantwortungsbewussten Umgang mit KI haben wir die Verwendung des Features „Benutzerdefinierte neuronale Stimme“ beschränkt. Sie erhalten erst Zugriff auf die Technologie, nachdem Ihre Anwendungen überprüft wurden und Sie sich zur Einhaltung der Prinzipien eines verantwortungsbewussten Umgangs mit KI verpflichtet haben. [Hier](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) erhalten Sie weitere Informationen zu unserer Richtlinie zur Zugriffsbeschränkung. [Hier](https://aka.ms/customneural) können Sie den Zugriff anfordern. 
 
## <a name="set-up-your-azure-account"></a>Einrichten Ihres Azure-Kontos

Bevor Sie „Benutzerdefinierte neuronale Stimme“ verwenden können, ist ein Speech-Dienstabonnement erforderlich. Befolgen Sie diese Anweisungen, um in Azure ein Abonnement für den Speech-Dienst zu erstellen. Sollten Sie über kein Azure-Konto verfügen, können Sie sich für ein neues Konto registrieren.  

Nachdem Sie ein Azure-Konto und ein Abonnement für Speech-Dienste erstellt haben, müssen Sie sich bei Speech Studio anmelden und Ihr Abonnement verbinden.

1. Rufen Sie über das Azure-Portal Ihren Abonnementschlüssel für die Speech-Dienste ab.
2. Melden Sie sich bei [Speech Studio](https://speech.microsoft.com) an, und klicken Sie dann auf **Custom Voice**.
3. Wählen Sie Ihr Abonnement aus, und erstellen Sie ein Speech-Projekt.
4. Wenn Sie zu einem anderen Speech-Abonnement wechseln möchten, verwenden Sie das Zahnradsymbol auf der oberen Navigationsleiste.

> [!NOTE]
> Sie müssen über einen in Azure erstellten F0- oder S0-Schlüssel für den Speech-Dienst verfügen, um den Dienst nutzen zu können. Das Feature „Benutzerdefinierte neuronale Stimme“ unterstützt nur die S0-Ebene. 

## <a name="create-a-project"></a>Erstellen eines Projekts

Inhalte wie Daten, Modelle, Tests und Endpunkte sind in Speech Studio in **Projekten** organisiert. Jedes Projekt ist spezifisch für ein Land/eine Sprache und das Geschlecht der Stimme, die Sie erstellen möchten. So können Sie beispielsweise ein Projekt mit einer weiblichen Stimme für die Chatbots Ihres Callcenters erstellen, die US-amerikanisches Englisch (en-US) verwenden.

Wählen Sie zum Erstellen Ihres ersten Projekts die Registerkarte **Text-to-Speech/Custom Voice** (Sprachsynthese/Benutzerdefinierte Stimme) aus, und klicken Sie auf **Projekt erstellen**. Folgen Sie den Anweisungen des Assistenten, um Ihr Projekt zu erstellen. Nachdem Sie ein Projekt erstellt haben, werden vier Registerkarten angezeigt: **Sprecher einrichten**, **Trainingsdaten vorbereiten**, **Modell trainieren** und **Modell bereitstellen**. Verwenden Sie die Links unter [Nächste Schritte](#next-steps), um mehr über die Verwendung der einzelnen Registerkarten zu erfahren.

## <a name="tips-for-creating-a-custom-neural-voice"></a>Tipps zum Erstellen einer benutzerdefinierten neuronalen Stimme

Die Erstellung einer hervorragenden benutzerdefinierten Stimme erfordert eine sorgfältige Qualitätskontrolle in jedem Schritt, von Sprachentwurf und Datenaufbereitung bis hin zur Bereitstellung des Stimmmodells in Ihrem System. Im Folgenden finden Sie einige wichtige Schritte, die Sie beim Erstellen einer benutzerdefinierten neuronalen Stimme für Ihre Organisation ausführen müssen. 

### <a name="persona-design"></a>Entwurf der Persona

Entwerfen Sie zunächst eine Persona der Stimme, die Ihre Marke darstellt, indem Sie ein kurzes Personadokument verwenden, das Elemente wie die Merkmale der Stimme und den Charakter hinter der Stimme definiert. Dies hilft, den Prozess der Erstellung eines benutzerdefinierten Stimmmodells zu steuern, einschließlich der Definition der Skripts, der Auswahl Ihres Sprechers, des Trainings und der Stimmoptimierung.

### <a name="script-selection"></a>Skriptauswahl
 
Wählen Sie das Aufzeichnungsskript sorgfältig aus, um die Benutzerszenarien für Ihre Stimme darzustellen. Sie können z. B. die Ausdrücke aus Botkonversationen als Aufzeichnungsskript verwenden, wenn Sie einen Kundendienstbot erstellen. Fügen Sie verschiedene Satztypen in Ihre Skripts ein, einschließlich Aussagen, Fragen, Ausrufen usw.

### <a name="preparing-training-data"></a>Vorbereiten von Trainingsdaten

Es wird empfohlen, die Audioaufzeichnungen in einem professionellen Aufzeichnungsstudio mit entsprechender Qualität zu erfassen, um ein hohes Signal-Rausch-Verhältnis zu erzielen. Die Qualität des Stimmmodells hängt stark von Ihren Trainingsdaten ab. Konsistente Lautstärke, Sprechgeschwindigkeit, Tonhöhe und Konsistenz in den ausdrucksvollen Eigenheiten der Sprache sind erforderlich.

Sobald die Aufzeichnungen bereit sind, befolgen Sie [Vorbereiten der Trainingsdaten](how-to-custom-voice-prepare-data.md), um die Trainingsdaten im richtigen Format vorzubereiten.

### <a name="training"></a>Training

Nachdem Sie die Trainingsdaten vorbereitet haben, wechseln Sie zu [Speech Studio](https://aka.ms/custom-voice), um Ihre benutzerdefinierte neuronale Stimme zu erstellen. Sie müssen mindestens 300 Äußerungen auswählen, um eine benutzerdefinierte neuronale Stimme zu erstellen. Eine Reihe von Datenqualitätsprüfungen werden automatisch ausgeführt, wenn Sie sie hochladen. Um qualitativ hochwertige Stimmmodelle zu erstellen, sollten Sie die Fehler beheben und die Übermittlung erneut durchführen.

### <a name="testing"></a>Test

Bereiten Sie Testskripts für Ihr Sprachmodell vor, die die verschiedenen Anwendungsfälle für Ihre Apps abdecken. Es wird empfohlen, Skripts innerhalb und außerhalb des Trainingsdatasets zu verwenden, damit Sie die Qualität für verschiedene Inhalte umfassender testen können.

### <a name="tuning-and-adjustment"></a>Optimierung und Anpassung

Der Stil und die Merkmale des trainierten Stimmmodells hängen vom Stil und der Qualität der Aufzeichnungen des Sprechers ab, der für das Training verwendet wird. Es können jedoch mehrere Anpassungen mithilfe von [SSML (Speech Synthesis Markup Language)](./speech-synthesis-markup.md?tabs=csharp) vorgenommen werden, wenn Sie die API-Aufrufe für Ihr Sprachmodell vornehmen, um synthetische Sprache zu generieren. SSML ist die Markupsprache, die für die Kommunikation mit dem TTS-Dienst verwendet wird, um Text in Audio zu konvertieren. Die Anpassungen umfassen die Änderung von Tonhöhe, Geschwindigkeit, Intonation und Aussprachekorrektur.  Wenn das Stimmmodell mit mehreren Stilen erstellt wird, kann SSML auch verwendet werden, um die Stile zu wechseln.

## <a name="migrate-to-custom-neural-voice"></a>Migrieren zur benutzerdefinierten neuronalen Stimme

Die Unterstützung der Standardtrainingsebene/nicht neuronalen Trainingsebene (statistisch parametrisch, konkatenativ) von Custom Voice wird beendet. Die Ankündigung wurde an alle vor dem 28.02.2021 vorhandenen Speech-Abonnements gesendet. Während des Übergangszeitraums bis zum Ende der Unterstützung (01.03.2021–29.02.2024) können vorhandene Benutzer der Standardebene weiterhin ihre erstellten nicht neuronalen Modelle verwenden. Alle neuen Benutzer/neuen Sprachressourcen sollten zur neuronalen Ebene bzw. zu „Benutzerdefinierte neuronale Stimme“ wechseln. Nach dem 29.02.2024 werden standardmäßige/nicht neuronale benutzerdefinierte Stimmen nicht mehr unterstützt. 

Wenn Sie die nicht neuronale/Standardversion von Custom Voice verwenden, sollten Sie direkt nach dem Ausführen der folgenden Schritte zu „Benutzerdefinierte neuronale Stimme“ migrieren. Der Wechsel zur benutzerdefinierten neuronalen Stimme hilft Ihnen dabei, realistischere Stimmen für noch natürlichere Konversationsschnittstellen zu entwickeln. So können Sie es Ihren Kunden und Endbenutzern ermöglichen, die Vorteile der aktuellen Text-zu-Sprache-Technologie verantwortungsvoll zu nutzen. 

1. [Hier](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) erhalten Sie weitere Informationen zu unserer Richtlinie zur Zugriffsbeschränkung. [Hier](https://aka.ms/customneural) können Sie den Zugriff anfordern. Beachten Sie, dass der Zugriff auf den Dienst für die benutzerdefinierte neuronale Stimme im alleinigen Ermessen von Microsoft auf der Grundlage unserer Qualifizierungskriterien liegt. Kunden erhalten erst dann Zugriff auf die Technologie, wenn ihre Anwendung geprüft wurde und sie sich verpflichtet haben, sie in Übereinstimmung mit unseren [Prinzipien für verantwortungsbewusste KI](https://microsoft.com/ai/responsible-ai) und den [Verhaltensregeln](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) zu verwenden. 
2. Sobald Ihre Anwendung genehmigt ist, erhalten Sie Zugriff auf das „neuronale“ Trainingsfeature. Stellen Sie sicher, dass Sie sich mithilfe desselben Azure-Abonnements, das Sie in Ihrer Anwendung bereitstellen, bei [Speech Studio](https://speech.microsoft.com) anmelden. 
    > [!IMPORTANT]
    > Um Sprecher zu schützen und das Training von Stimmmodellen mit nicht autorisierten Aufzeichnungen oder ohne die Zustimmung des Sprechers zu verhindern, erfordern wir vom Kunden das Hochladen einer aufgezeichneten Erklärung des Sprechers, in der er seine Zustimmung gibt. Stellen Sie beim Vorbereiten des Aufzeichnungsskripts sicher, dass Sie den folgenden Satz einschließen. „Ich [Vor- und Nachname nennen] akzeptiere, dass die Aufzeichnungen meiner Stimme von [Name des Unternehmens nennen] verwendet werden, um eine synthetische Version meiner Stimme zu erstellen und diese zu verwenden.“
    > Dieser Satz muss als Datei mit mündlicher Einwilligung auf die Registerkarte **Sprecher einrichten** hochgeladen werden. Anhand dieses Satzes wird überprüft, ob die Aufzeichnungen in den Trainingsdatasets von der Person stammen, die die Zustimmung erteilt hat.
3. Nachdem das benutzerdefinierte neuronale Stimmmodell erstellt wurde, stellen Sie das Stimmmodell auf einem neuen Endpunkt bereit. Navigieren Sie zum Erstellen eines Custom Voice-Endpunkts mit Ihrem neuronalen Stimmmodell zu **Sprachsynthese > Custom Voice > Modell bereitstellen**. Wählen Sie **Modell bereitstellen** aus, und geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie dann das benutzerdefinierte neuronale Stimmmodell aus, das Sie diesem Endpunkt zuordnen möchten, und bestätigen Sie die Bereitstellung.  
4. Aktualisieren Sie Ihren Code in Ihren Apps, wenn Sie einen neuen Endpunkt mit einem neuen Modell erstellt haben. 

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten von Daten für „Benutzerdefinierte neuronale Stimme“](how-to-custom-voice-prepare-data.md)
- [Trainieren und Bereitstellen einer benutzerdefinierten neuronalen Stimme](how-to-custom-voice-create-voice.md)
- [Aufnehmen von Sprachbeispielen](record-custom-voice-samples.md)
