---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 6a33020c686686d40529b8188ccfcf7fcb412331
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252315"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Sprach-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für Python installieren. Führen Sie `pip install azure-cognitiveservices-speech` aus, wenn Sie nur den Paketnamen benötigen und selbständig einsteigen möchten.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- Das Python Speech SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
  - Windows: x64 und x86
  - Mac: macOS X Version 10.14 oder höher (Die Python-Mindestversion unter macOS ist 3.7.)
  - Linux: Zeigen Sie die Liste mit den [unterstützten Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) an.

## <a name="prerequisites"></a>Voraussetzungen

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.

- Sehen Sie sich unter Linux die [Systemanforderungen und Setupanweisungen](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk) an.

- Und schließlich benötigen Sie [Python 3.6 bis 3.9](https://www.python.org/downloads/). Um die Installation zu überprüfen, öffnen Sie eine Eingabeaufforderung, geben den Befehl `python --version` ein und überprüfen das Ergebnis. Bei ordnungsgemäßer Installation erhalten Sie die Antwort „Python 3.8.8“ oder ähnliches.

## <a name="install-the-speech-sdk-from-pypi"></a>Installieren des Speech SDK über PyPI

Wenn Sie Ihre eigene Umgebung oder Ihre eigenen Buildtools verwenden, führen Sie den folgenden Befehl aus, um das Speech SDK über [PyPI](https://pypi.org/) zu installieren. Fahren Sie als Benutzer von Visual Studio Code mit dem nächsten Unterabschnitt zur geführten Installation fort.

```sh
pip install azure-cognitiveservices-speech
```

Auf macOS müssen Sie möglicherweise den folgenden Befehl ausführen, damit der obige `pip`-Befehl funktioniert:

```sh
python3 -m pip install --upgrade pip
```

Nachdem Sie `pip` erfolgreich für die Installation von `azure-cognitiveservices-speech` verwendet haben, können Sie das Sprach-SDK verwenden, indem Sie den Namespace in Ihre Python-Projekte importieren.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installieren des Sprach-SDK unter Verwendung von Visual Studio Code

1. Laden Sie die neueste unterstützte Version von [Python](https://www.python.org/downloads/) für Ihre Plattform (3.6 bis 3.9) herunter, und installieren Sie sie.
   - Als Windows-Benutzer stellen Sie sicher, dass Sie während des Installationsvorgangs „Add Python to your PATH“ (Fügen Sie Python Ihrem PATH hinzu) auswählen.
1. Laden Sie [Visual Studio Code](https://code.visualstudio.com/Download) herunter, und installieren Sie die Software.
1. Öffnen Sie Visual Studio Code, und installieren Sie die Python-Erweiterung. Wählen Sie im Menü **Datei** > **Einstellungen** > **Erweiterungen**. Suchen Sie nach **Python**, und klicken Sie auf **Installieren**.

   ![Installieren der Python-Erweiterung](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Installieren Sie das Sprach-SDK-Paket für Python auch von Visual Studio Code aus über die integrierte Befehlszeile:
   1. Öffnen Sie ein Terminal (in den Dropdownmenüs **Terminal** > **Neues Terminal**).
   1. Geben Sie im geöffneten Terminal den Befehl `python -m pip install azure-cognitiveservices-speech` ein.

Wenn Sie noch nicht mit Visual Studio Code vertraut sind, lesen Sie die ausführlichere [Visual Studio Code-Dokumentation](https://code.visualstudio.com/docs). Weitere Informationen zu Visual Studio Code und Python finden Sie im [Visual Studio Code-Python-Tutorial](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Support und Updates

Updates für das Speech SDK-Paket für Python werden über PyPI verteilt und in den [Versionshinweisen](~/articles/cognitive-services/speech-service/releasenotes.md) angekündigt.
Wenn eine neue Version verfügbar ist, können Sie mit dem Befehl `pip install --upgrade azure-cognitiveservices-speech` auf diese Version aktualisieren.
Die derzeit installierte Version können Sie der Variablen `azure.cognitiveservices.speech.__version__` entnehmen.

Falls Probleme auftreten oder ein Feature fehlt, sehen Sie sich die [Support- und Hilfeoptionen](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) an.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
