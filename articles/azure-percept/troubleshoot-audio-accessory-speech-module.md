---
title: Problembehandlung für Azure Percept Audio und das Sprachmodul
description: Tipps zur Problembehandlung für Azure Percept Audio und azureearspeechclientmodule
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 3cd92c3158aec8dd63d58455641d4d42d02ff6e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059811"
---
# <a name="troubleshoot-azure-percept-audio-and-speech-module"></a>Problembehandlung für Azure Percept Audio und das Sprachmodul

Beheben Sie Probleme mit dem Sprach-Assistenten mithilfe der folgenden Anweisungen.

## <a name="checking-runtime-status-of-the-speech-module"></a>Überprüfen des Laufzeitstatus des Speech-Moduls

Überprüfen Sie, ob für **azureearspeechclientmodule** als Laufzeitstatus **Wird ausgeführt** angezeigt wird. Öffnen Sie zum Ermitteln des Laufzeitstatus Ihrer Gerätemodule das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu **Alle Ressourcen** ->  **[Ihr IoT Hub]**  -> **IoT Edge** ->  **[Ihre Geräte-ID]** . Wählen Sie die Registerkarte **Module** aus, um den Laufzeitstatus aller installierten Module anzuzeigen.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Seite „Edge-Gerät“ im Azure-Portal":::

Wird als Laufzeitstatus von **azureearspeechclientmodule** nicht **Wird ausgeführt** angezeigt, wählen Sie **Module festlegen** -> **azureearspeechclientmodule** aus. Legen Sie auf der Seite **Moduleinstellungen** die Option **Gewünschter Status** auf **Wird ausgeführt** fest, und wählen Sie **Aktualisieren** aus.

## <a name="voice-assistant-application-doesnt-load"></a>Sprach-Assistent-Anwendung wird nicht geladen
Versuchen Sie, [eine der verfügbaren Sprach-Assistentenvorlagen bereitzustellen](./tutorial-no-code-speech.md). Durch die Bereitstellung einer Vorlage wird sichergestellt, dass alle unterstützenden Ressourcen erstellt werden, die für Sprach-Assistentenanwendungen erforderlich sind.

## <a name="voice-assistant-template-doesnt-get-created"></a>Die Vorlage für den Sprach-Assistenten wird nicht erstellt.
Fehler beim Erstellen einer Sprach-Assistentenvorlage sind in der Regel ein Problem mit einer der unterstützenden Ressourcen.
1. [Löschen Sie alle zuvor erstellten Sprach-Assistentenressourcen](./delete-voice-assistant-application.md).
1. Stellen Sie eine neue [Sprachassistenten-Vorlage](./tutorial-no-code-speech.md) bereit.

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>Sprach-Assistent wurde erstellt, reagiert aber nicht auf Befehle
Befolgen Sie die Anweisungen im Leitfaden zum [LED-Verhalten und zur Problembehandlung](audio-button-led-behavior.md), um dieses Problem zu beheben.

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>Sprach-Assistent reagiert nicht auf ein benutzerdefiniertes Schlüsselwort, das in Speech Studio erstellt wurde
Dies kann passieren, wenn das Sprachmodul veraltet ist. Führen Sie die folgenden Schritte aus, um das Sprachmodul auf die neueste Version zu aktualisieren:

1. Wählen Sie auf der Startseite von Azure Percept Studio im Menü auf der linken Seite **Geräte** aus.
1. Suchen Sie nach Ihrem Gerät, und wählen Sie es aus.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Screenshot: Geräteliste in Azure Percept Studio":::
1. Wählen Sie im Gerätefenster die Registerkarte **Sprache** aus.
1. Überprüfen Sie die Version des Sprachmoduls. Wenn ein Update verfügbar ist, wird neben der Versionsnummer die Schaltfläche **Update** angezeigt.
1. Klicken Sie auf **Update**, um das Update für das Sprachmodul bereitzustellen. Es dauert normalerweise zwei bis drei Minuten, bis der Updatevorgang abgeschlossen ist.

## <a name="collecting-speech-module-logs"></a>Sammeln von Speech-Modulprotokollen
Um diese Befehle auszuführen, verbinden Sie sich per [SSH mit dem Dev-Kit](./how-to-ssh-into-percept-dk.md) und geben Sie die Befehle in die Eingabeaufforderung des SSH-Clients ein.

Sammeln Sie Speech-Modulprotokolle:

```console
sudo iotedge logs azureearspeechclientmodule
```

Verwenden Sie die folgende Syntax, um Ausgaben zur weiteren Analyse an eine TXT-Datei umzuleiten:

```console
sudo [command] > [file name].txt
```

Ändern Sie die Berechtigungen der txt-Datei, damit Sie kopiert werden kann:

```console
sudo chmod 666 [file name].txt
```

Nach dem Umleiten einer Ausgabe an eine TXT-Datei kopieren Sie die Datei über SCP auf Ihren Host-PC:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[local host file path] bezieht sich auf den Speicherort auf Ihrem Host-PC, an den Sie die TXT-Datei kopieren möchten. [remote username] ist der SSH-Benutzername, der während des [Setups](./quickstart-percept-dk-set-up.md) ausgewählt wurde.

## <a name="known-issues"></a>Bekannte Probleme
- Wenn Sie eine kostenlose Testversion verwenden, kann das Sprachmodell den Preisplan der kostenlosen Testversion überschreiten. In diesem Fall stellt das Modell die Funktion ohne Fehlermeldung ein.
- Wenn mehr als 5 IoT Edge-Geräte verbunden sind, wird der Bericht (der per Telemetrie an IoT Hub und Speech Studio gesendete Text) möglicherweise blockiert.
- Wenn sich das Gerät in einer anderen Region als die Ressourcen befindet, wird die Berichtsmeldung möglicherweise verzögert. 

## <a name="useful-links"></a>Nützliche Links
- [Einrichten von Azure Percept-Audio](./quickstart-percept-audio-setup.md)
- [Tasten- und LED-Verhalten von Azure Percept-Audio](./audio-button-led-behavior.md)
- [Erstellen eines Sprach-Assistenten mit Azure Percept DK und Azure Percept-Audio](./tutorial-no-code-speech.md)
- [Allgemeiner Problembehandlungsleitfaden für Azure Percept DK](./troubleshoot-dev-kit.md)
- [Zurückkehren zu einer zuvor beschriebenen Sprach-Assistenten-Anwendung](return-to-voice-assistant-application-window.md)
