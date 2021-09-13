---
title: Verwenden eines einfachen Sprachmusterabgleichs mit dem Speech SDK für C++
titleSuffix: Azure Cognitive Services
description: In diesem Leitfaden erfahren Sie, wie Sie Absichten und Entitäten mit einfachen Mustern erkennen.
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713357"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>Verwenden eines einfachen Sprachmusterabgleichs mit dem Speech SDK für C++

Das Cognitive Services [Speech SDK](speech-sdk.md) verfügt über ein integriertes Feature, das die **Absichtserkennung** mit einem **einfachen Sprachmusterabgleich** ermöglicht. Eine Absicht ist etwas, das der Benutzer tun möchte: ein Fenster schließen, ein Kontrollkästchen aktivieren, Text einfügen usw.

In diesem Leitfaden verwenden Sie das Speech SDK, um eine C++-Konsolenanwendung zu entwickeln, die Absichten aus Benutzeräußerungen über das Mikrofon Ihres Geräts ableitet. Sie lernen Folgendes:

> [!div class="checklist"]
>
> - Erstellen eines Visual Studio-Projekts, das auf das Speech SDK NuGet-Paket verweist
> - Erstellen einer Sprachkonfiguration und Abrufen einer Absichtserkennung
> - Hinzufügen von Absichten und Mustern über die Speech SDK-API
> - Erkennen von Sprache aus einer Datei
> - Verwenden von asynchroner, ereignisgesteuerter kontinuierlicher Erkennung

## <a name="when-should-you-use-this"></a>Wann sollten Sie den einfachen Sprachmusterabgleich mit dem Speech SDK verwenden?

Verwenden Sie diesen Beispielcode in folgenden Fällen: 
* Sie sind nur daran interessiert, die Äußerungen des Benutzers sehr genau abzugleichen. Diese Muster führen einen aggressiveren Abgleich durch als LUIS.
* Sie haben keinen Zugriff auf eine LUIS-App, möchten aber Absichten verwenden. Da die Absichtserkennung in das SDK eingebettet ist, kann es in solchen Fällen hilfreich sein.
* Sie können oder möchten keine LUIS-App erstellen, möchten aber eine Sprachsteuerungsfunktion verwenden.

Wenn Sie keinen Zugriff auf eine LUIS-App haben, aber dennoch Absichten verwenden möchten, kann das SDK durch die eingebettete Spracherkennung hilfreich sein.


## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Elemente vorhanden sind, bevor Sie mit diesem Leitfaden beginnen:

- Eine [Azure Cognitive Services-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) oder eine [Unified Speech-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (beliebige Edition)

## <a name="speech-and-simple-patterns"></a>Speech und einfache Muster

Die einfachen Muster sind ein Feature des Speech SDK und erfordern eine Cognitive Services- oder Unified Speech-Ressource.

Ein Muster ist ein Ausdruck, der eine Entität enthält. Eine Entität wird definiert, indem ein Wort in geschweifte Klammern eingeschlossen wird. Zum Beispiel:

```
    Take me to the {floorName}
```

Dadurch wird eine Entität mit der ID „floorName“ definiert, bei der die Groß-/Kleinschreibung beachtet wird.

Alle anderen Sonderzeichen und Satzzeichen werden ignoriert.

Absichten werden mithilfe von Aufrufen der API „IntentRecognizer->AddIntent()“ hinzugefügt.

## <a name="create-a-speech-project-in-visual-studio"></a>Erstellen eines Speech-Projekts in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

Öffnen Sie Ihr Projekt in Visual Studio.

Starten Sie Visual Studio 2019.
Laden Sie Ihr Projekt, und öffnen Sie „helloworld.cpp“.
Beginnen Sie mit einigen Codebausteinen. Fügen Sie Code hinzu, der als Gerüst für das Projekt dient. Notieren Sie sich, dass Sie eine asynchrone Methode namens „recognizeIntent()“ erstellt haben.

## <a name="open-your-project-in-visual-studio"></a>Öffnen Sie Ihr Projekt in Visual Studio.

Öffnen Sie als Nächstes Ihr Projekt in Visual Studio.

1. Starten Sie Visual Studio 2019.
2. Laden Sie das Projekt, und öffnen Sie `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen

Fügen Sie Code hinzu, der als Gerüst für das Projekt fungiert.

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Speech-Konfiguration

Bevor Sie ein `IntentRecognizer`-Objekt initialisieren können, müssen Sie eine Konfiguration erstellen, die den Schlüssel und Speicherort Ihrer Cognitive Services-Vorhersageressource verwendet.

* Ersetzen Sie `"YOUR_SUBSCRIPTION_KEY"` durch Ihren Cognitive Services-Vorhersageschlüssel.
* Ersetzen Sie `"YOUR_SUBSCRIPTION_REGION"` durch die Region Ihrer Cognitive Services-Ressource.

In diesem Beispiel wird die Methode `FromSubscription()` zum Erstellen von `SpeechConfig` verwendet. Eine vollständige Liste der verfügbaren Methoden finden Sie unter [SpeechConfig-Klasse](/cpp/cognitive-services/speech/speechconfig).

## <a name="initialize-an-intentrecognizer"></a>Initialisieren eines IntentRecognizer-Objekts

Erstellen Sie nun eine Spracherkennung (`IntentRecognizer`). Fügen Sie den folgenden Code direkt unterhalb Ihrer Speech-Konfiguration ein:

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>Hinzufügen von Absichten

Sie müssen einige Muster mit der `IntentRecognizer` verknüpfen, indem Sie `AddIntent()` aufrufen.
Wir fügen zwei Absichten mit der gleichen ID zum Wechseln der Etagen und eine weitere Absicht mit einer separaten ID zum Öffnen und Schließen von Türen hinzu.

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> Sie können eine beliebige Anzahl von Entitäten deklarieren, sie werden jedoch grob abgeglichen. Wenn Sie einen Ausdruck wie „{action} door“ hinzufügen, wird er jedes Mal als Übereinstimmung ermittelt, wenn Text vor dem Wort „door“ vorhanden ist. Absichten werden basierend auf ihrer Anzahl von Entitäten ausgewertet. Wenn zwei Muster übereinstimmen, wird die Absicht zurückgegeben, für die mehr Entitäten definiert sind.

## <a name="recognize-an-intent"></a>Erkennen einer Absicht

Rufen Sie die Methode `RecognizeOnceAsync()` über das Objekt `IntentRecognizer` auf. Diese Methode fordert den Speech-Dienst auf, die Sprache in einem einzelnen Ausdruck zu erkennen und die Spracherkennung zu beenden, sobald der Ausdruck identifiziert wurde. Der Einfachheit halber warten wir auf die kommende Rückgabe, um fortzufahren.

Fügen Sie diesen Code unterhalb Ihrer Absichten ein:

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>Anzeigen der Erkennungsergebnisse (oder Fehler)

Wenn das Erkennungsergebnis vom Speech-Dienst zurückgegeben wird, geben Sie es aus.

Fügen Sie diesen Code unterhalb von `auto result = intentRecognizer->RecognizeOnceAsync().get();` ein:

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>Überprüfen des Codes

Ihr Code sollte nun wie folgt aussehen:

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>Erstellen und Ausführen der App

Sie können nun Ihre App erstellen und die Spracherkennung mit dem Speech-Dienst testen.

1. **Kompilieren des Codes**: Wählen Sie auf der Menüleiste von Visual Studio **Build** > **Projektmappe erstellen** aus.
2. **Starten der App**: Wählen Sie auf der Menüleiste **Debuggen** > **Debuggen starten** aus, oder drücken Sie <kbd>F5</kbd>.
3. **Starten der Erkennung**: Sie werden aufgefordert, etwas zu sagen. Die Standardsprache ist Deutsch. Ihre Spracheingabe wird an den Speech-Dienst gesendet, in Text transkribiert und in der Konsole ausgegeben.

Wenn Sie z. B. „Führe mich zu Etage 7“ sagen, sollte die Ausgabe wie folgt aussehen:

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
