---
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/28/2021
ms.author: eric-urban
ms.custom: ignite-fall-2021
ms.openlocfilehash: 197c186f87eacfb19412c12bc171b46fd59b0ef2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501980"
---
## <a name="download-and-install"></a>Herunterladen und Installieren

#### <a name="windows-install"></a>[Windows-Installation](#tab/windowsinstall)

Führen Sie diese Schritte aus, um die Speech-Befehlszeilenschnittstelle unter Windows zu installieren:

1. Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.
1. Installieren Sie das [.NET Core 3.1 SDK](/dotnet/core/install/windows).
2. Installieren Sie die Speech-Befehlszeilenschnittstelle mithilfe von NuGet durch Eingabe des folgenden Befehls:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI
   ```
Geben Sie `spx` ein, um die Hilfe für die Speech-Befehlszeilenschnittstelle anzuzeigen.

> [!NOTE]
> Als Alternative zu NuGet können Sie die Speech-Befehlszeilenschnittstelle für Windows als [ZIP-Datei](https://aka.ms/speech/spx-windows) herunterladen und extrahieren.

### <a name="font-limitations"></a>Schriftarteinschränkungen

Unter Windows können von der Speech-Befehlszeilenschnittstelle nur Schriftarten angezeigt werden, die für die Eingabeaufforderung auf dem lokalen Computer verfügbar sind.
Das [Windows-Terminal](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701) unterstützt alle Schriftarten, die von der Speech-Befehlszeilenschnittstelle interaktiv generiert werden.

Bei der Ausgabe einer Datei können in einem Text-Editor wie Notepad oder einem Webbrowser wie Microsoft Edge ebenfalls alle Schriftarten angezeigt werden.

#### <a name="linux-install"></a>[Linux-Installation](#tab/linuxinstall)

Die folgenden Linux-Distributionen werden für x64-Architekturen mit der Speech-CLI unterstützt:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04 (bis September), Ubuntu 18.04/20.04

> [!NOTE]
> Weitere Architekturen werden vom Speech SDK (nicht die Speech-CLI) unterstützt. Weitere Informationen finden Sie unter [Informationen zum Speech SDK](../speech-sdk.md).

Führen Sie diese Schritte aus, um die Speech-Befehlszeilenschnittstelle unter Linux auf einer x64-CPU zu installieren:

1. Installieren Sie das [.NET Core 3.1 SDK](/dotnet/core/install/linux).
2. Installieren Sie die Speech-Befehlszeilenschnittstelle mithilfe von NuGet durch Eingabe des folgenden Befehls:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI`

3. Konfigurieren Sie unter RHEL/CentOS Linux [OpenSSL für Linux](../how-to-configure-openssl-linux.md).
4. Installieren Sie unter Ubunutu 20.04 Linux [GStreamer](../how-to-use-codec-compressed-audio-input-streams.md).

Geben Sie `spx` ein, um die Hilfe für die Speech-Befehlszeilenschnittstelle anzuzeigen.

> [!NOTE]
> Als Alternative zu NuGet können Sie die Linux-Binärdateien als [ZIP-Datei](https://aka.ms/speech/spx-linux) herunterladen.
> Extrahieren Sie `spx-netcore-30-linux-x64.zip` in ein neues Verzeichnis (`~/spx`), geben Sie `sudo chmod +r+x spx` für die Binärdatei ein, und fügen Sie den Pfad `~/spx` Ihrer PATH-Systemvariablen hinzu.


#### <a name="docker-install-windows-linux-macos"></a>[Docker-Installation (Windows, Linux, macOS)](#tab/dockerinstall)

> [!IMPORTANT]
> Wenn Sie die Speech-CLI in einem Docker-Container ausführen, können Sie das Mikrofon Ihres Computers nicht verwenden. Sie können jedoch aus Ihrem lokal eingebundenen Verzeichnis lesen und Audiodateien darin speichern. 

> [!NOTE]
> Im folgenden Beispiel wird ein öffentliches Container-Image von Docker Hub abgerufen. Es wird empfohlen, dass Sie sich zuerst mit Ihrem Docker Hub Konto (`docker login`) authentifizieren, anstatt eine anonyme Abrufanforderung zu erstellen. Um die Zuverlässigkeit bei der Verwendung öffentlicher Inhalte zu verbessern, sollten Sie das Image in einer privaten Azure-Containerregistrierung importieren und verwalten. Weitere Informationen zur Verwendung öffentlicher Images finden Sie [hier](../../../container-registry/buffer-gate-public-content.md).

Führen Sie die folgenden Schritte aus, um die Speech-CLI in einem Docker-Container zu installieren:

1. <a href="https://www.docker.com/get-started" target="_blank">Installieren Sie Docker Desktop</a> für Ihre Plattform (sofern noch nicht installiert).
2. Geben Sie an einer neuen Eingabeaufforderung oder in einem Terminal den folgenden Befehl ein: 
   ```console   
   docker pull msftspeech/spx
   ```
3. Geben Sie diesen Befehl ein. Es sollten Hilfeinformationen für die Speech-CLI angezeigt werden: 
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Einbinden eines Verzeichnisses in den Container

Mit dem Tool „Speech-CLI“ werden Konfigurationseinstellungen als Dateien gespeichert, die beim Ausführen eines Befehls dann geladen werden (mit Ausnahme von Hilfebefehlen).
Bei Verwendung der Speech-CLI in einem Docker-Container müssen Sie ein lokales Verzeichnis aus dem Container einbinden. Das Tool kann so die Konfigurationseinstellungen speichern bzw. finden und darüber hinaus alle Dateien lesen oder schreiben, die der Befehl benötigt, z. B. Audiodateien mit Sprache.

Geben Sie diesen Befehl unter Windows ein, um ein lokales Verzeichnis zu erstellen, das von der Speech-CLI aus dem Container verwendet werden kann:

`mkdir c:\spx-data`

Geben Sie unter Linux oder macOS den folgenden Befehl in einem Terminal ein, um ein Verzeichnis zu erstellen und dessen absoluten Pfad anzuzeigen:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Sie verwenden den absoluten Pfad beim Aufrufen der Speech-CLI.

### <a name="run-speech-cli-in-the-container"></a>Ausführen der Speech-CLI im Container

In dieser Dokumentation wird der Befehl `spx` der Speech-CLI angezeigt, der in nicht zu Docker gehörenden Installationen verwendet wird.
Beim Aufrufen des Befehls `spx` in einem Docker-Container müssen Sie ein Verzeichnis in den Container für Ihr Dateisystem einbinden, in dem die Speech-CLI Konfigurationswerte speichern und finden und Dateien lesen und schreiben kann.

Unter Windows werden Ihre Befehle wie folgt gestartet:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Unter Linux und macOS sehen Ihre Befehle wie im folgenden Beispiel aus. Ersetzen Sie `ABSOLUTE_PATH` durch den absoluten Pfad für Ihr eingebundenes Verzeichnis. Dieser Pfad wurde durch den Befehl `pwd` im vorherigen Abschnitt zurückgegeben. 

Wenn Sie diesen Befehl ausführen, bevor Sie den Schlüssel und die Region festgelegt haben, erhalten Sie eine Fehlermeldung mit dem Hinweis, dass Sie Ihren Schlüssel und Ihre Region festlegen müssen:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Geben Sie zum Verwenden des in einem Container installierten Befehls `spx` immer den vollständigen Befehl (wie oben gezeigt) ein, gefolgt von den Parametern Ihrer Anforderung.
Unter Windows wird Ihr Schlüssel mit diesem Befehl beispielsweise wie folgt festgelegt:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

Wenn Sie eine ausführlichere Befehlszeile nutzen möchten, können Sie einen Container mit einer interaktiven Bash-Shell starten, indem Sie einen entrypoint-Parameter hinzufügen.
Geben Sie unter Windows diesen Befehl ein, um einen Container zu starten, der eine interaktive Befehlszeile bereitstellt, in die Sie mehrere `spx`-Befehle eingeben können:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Erstellen der Abonnementkonfiguration

Um mit der Verwendung der Speech-Befehlszeilenschnittstelle zu beginnen, müssen Sie Ihren Speech-Abonnementschlüssel und den Regionsbezeichner eingeben. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../overview.md#try-the-speech-service-for-free) abrufen.
Nachdem Sie Ihren Abonnementschlüssel und den Regionsbezeichner (z. B. `eastus`, `westus`) erhalten haben, führen Sie die folgenden Befehle aus.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Ihre Abonnementauthentifizierung wird jetzt für zukünftige SPX-Anforderungen gespeichert. Wenn Sie einen dieser gespeicherten Werte entfernen müssen, führen Sie `spx config @region --clear` oder `spx config @key --clear` aus.
