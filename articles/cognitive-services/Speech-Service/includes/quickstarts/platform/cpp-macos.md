---
title: 'Schnellstart: Speech SDK für C++ (macOS): Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform mit dem Speech Service SDK für C++ unter macOS einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 73a6c99b3f35fa43852981edb3343cf9644e9662
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252499"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für C++ unter macOS (ab 10.14) installieren.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemanforderungen

macOS (ab 10.14)

## <a name="install-speech-sdk"></a>Installieren des Speech SDK

1. Wählen Sie ein Verzeichnis aus, in das die Dateien des Speech SDK extrahiert werden sollen, und legen Sie die Umgebungsvariable `SPEECHSDK_ROOT` so fest, dass sie auf dieses Verzeichnis verweist. Diese Variable erleichtert es, in zukünftigen Befehlen auf das Verzeichnis zu verweisen. Wenn Sie z.B. das Verzeichnis `speechsdk` in Ihrem Basisverzeichnis verwenden möchten, verwenden Sie einen Befehl wie den folgenden:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Erstellen Sie das Verzeichnis, wenn es noch nicht vorhanden ist:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Laden Sie das Archiv `.zip` mit Speech SDK xcframework herunter, und extrahieren Sie es:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Überprüfen Sie den Inhalt des Verzeichnisses auf oberster Ebene des extrahierten Pakets:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Die Verzeichnisliste muss den Drittanbieterhinweis und die Lizenzdateien sowie ein Verzeichnis namens `MicrosoftCognitiveServicesSpeech.xcframework` enthalten.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
