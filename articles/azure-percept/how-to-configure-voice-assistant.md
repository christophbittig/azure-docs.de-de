---
title: Konfigurieren Ihrer Sprach-Assistent-Anwendung für Azure Percept
description: Konfigurieren Ihrer Sprach-Assistenten-Anwendung mithilfe von Azure IoT Hub
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 82be52611d12b69e146fdaa477614a3dbc25242d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006561"
---
# <a name="configure-your-azure-percept-voice-assistant-application"></a>Konfigurieren Ihrer Sprach-Assistent-Anwendung für Azure Percept

In diesem Artikel wird beschrieben, wie Sie Ihre Sprach-Assistenten-Anwendung mithilfe von IoT Hub konfigurieren. Ein schrittweises Tutorial, das Sie durch den Prozess der Erstellung eines Sprach-Assistenten führt, finden Sie unter [Erstellen eines Sprach-Assistenten ohne Code mit Azure Percept Studio und Azure Percept Audio](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Aktualisieren Ihrer Sprach-Assistenten-Konfiguration

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und geben Sie **IoT Hub** in die Suchleiste ein. Klicken Sie auf das Symbol, um die Seite „IoT Hub“ zu öffnen.

1. Wählen Sie auf der Seite „IoT Hub“ den IOT Hub aus, in dem Ihr Gerät bereitgestellt wurde.

1. Wählen Sie im linken Navigationsmenü unter **Automatische Geräteverwaltung** den Eintrag **IoT Edge** aus, um alle mit Ihrem IoT Hub verbundenen Geräte anzuzeigen.

1. Wählen Sie das Gerät aus, auf dem Ihre Sprach-Assistenten-Anwendung bereitgestellt wurde.

1. Wählen Sie **Module festlegen** aus.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Screenshot: Geräteseite mit Hervorhebung von „Module festlegen“":::

1. Vergewissern Sie sich, dass der folgende Eintrag im Abschnitt **Anmeldeinformationen für Container Registry** vorhanden ist. Fügen Sie Anmeldeinformationen hinzu, falls dies erforderlich ist.

    |Name|Adresse|Username|Kennwort|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. Wählen Sie im Abschnitt **IoT Edge-Module** das Modul **azureearspeechclientmodule** aus.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Screenshot: Liste aller IoT Edge-Module des Geräts":::

1. Wählen Sie die Registerkarte **Moduleinstellungen** aus. Überprüfen Sie die folgende Konfiguration:

    Image-URI|Neustartrichtlinie|Gewünschter Status
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule: preload-devkit|immer|„Wird ausgeführt“

    Wenn Ihre Einstellungen nicht übereinstimmen, bearbeiten Sie sie, und wählen Sie dann **Aktualisieren** aus.

1. Wählen Sie die Registerkarte **Umgebungsvariablen** aus. Stellen Sie sicher, dass keine Umgebungsvariablen definiert sind.

1. Wählen Sie die Registerkarte **Einstellungen für Modulzwilling** aus. Aktualisieren Sie den Abschnitt **speechConfigs** wie folgt:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Das oben verwendete Schlüsselwort ist ein standardmäßig öffentlich verfügbares Schlüsselwort. Wenn Sie Ihr eigenes verwenden möchten, können Sie Ihr eigenes benutzerdefiniertes Schlüsselwort hinzufügen, indem Sie eine erstellte Tabellendatei in den Blob Storage hochladen. Blob Storage muss entweder mit anonymem Containerzugriff oder mit anonymem Blobzugriff konfiguriert werden.

## <a name="how-to-find-out-appid-key-and-region"></a>Herausfinden von App-ID (appId), Schlüssel (key) und Region (region)

Wechseln Sie zu [Speech Studio](https://speech.microsoft.com/), um Ihre **appID**, den **key** und die **region** herauszufinden:

1. Melden Sie sich an, und wählen Sie die entsprechende Sprachressource aus.
1. Wählen Sie auf der Homepage von **Speech Studio** unter **Sprach-Assistenten** die Option **Benutzerdefinierte Befehle** aus.
1. Wählen Sie Ihr Zielprojekt aus.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Screenshot: Projektseite in Speech Studio":::

1. Wählen Sie im linken Menübereich die Option **Einstellungen** aus.
1. **appID** und **key** befinden sich auf der Registerkarte **Allgemein** e Einstellungen.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Screenshot: Allgemeine Einstellungen für Speech-Projekt":::

1. Um Ihre **region** zu finden, öffnen Sie die Registerkarte **LUIS-Ressourcen** in den Einstellungen. Der Abschnitt **Erstellungsressource** enthält Regionsinformationen.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Screenshot: LUIS-Ressourcen für Speech-Projekt":::

1. Nachdem Sie Ihre **speechConfigs**-Informationen eingegeben haben, wählen Sie **Aktualisieren** aus.

1. Wählen Sie oben auf der Seite **Module festlegen** die Registerkarte **Routen** aus. Stellen Sie sicher, dass Sie eine Route mit folgendem Wert besitzen:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Fügen Sie die Route hinzu, wenn sie nicht vorhanden ist.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Klicken Sie auf **Erstellen**.


## <a name="next-steps"></a>Nächste Schritte

Kehren Sie nach dem Aktualisieren Ihrer Sprach-Assistenten-Konfiguration zur Demo in Azure Percept Studio zurück, um mit der Anwendung zu interagieren.
