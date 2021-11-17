---
title: include file
description: include file
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 830c636dfe3f8179a459fd8bfe7aedd51ff11902
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860488"
---
## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs
In diesem Abschnitt verwenden Sie die Azure CLI zum Erstellen eines IoT-Hubs und einer Ressourcengruppe.  Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Ein IoT-Hub fungiert als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und Geräten.

So erstellen Sie einen IoT-Hub und eine Ressourcengruppe:

1. Start Azure CLI: 
    - Wählen Sie bei Verwendung von Cloud Shell die Schaltfläche **Ausprobieren** in den CLI-Befehlen aus, um Cloud Shell in einem geteilten Browserfenster zu starten. Alternativ können Sie [Cloud Shell](https://shell.azure.com/bash) auch in einem separaten Browsertab öffnen.
    - Wenn Sie Azure CLI lokal verwenden, öffnen Sie eine Konsole wie Windows CMD, PowerShell oder Bash und [melden Sie sich bei Azure CLI an](/cli/azure/authenticate-azure-cli).
    
    Um die CLI-Befehle im Rest dieses Schnellstarts auszuführen, kopieren Sie die Befehlssyntax, fügen Sie sie in Ihr Cloud Shell-Fenster oder Ihre CLI-Konsole ein, bearbeiten Sie die Variablenwerte und drücken Sie die Eingabetaste.

1. Führen Sie [az extension add](/cli/azure/extension#az_extension_add) aus, um die Erweiterung *azure-iot* zu installieren oder auf die aktuelle Version zu aktualisieren.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen. Mit dem folgenden Befehl wird eine Ressourcengruppe mit dem Namen *MyResourceGroup* am Standort *eastus* erstellt. 
    >[!NOTE]
    > Optional können Sie auch einen alternativen Standort festlegen. Führen Sie zum Anzeigen der verfügbaren Standorte `az account list-locations` aus. In diesem Tutorial wird *eastus* verwendet, wie im Beispielbefehl gezeigt. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. Führen Sie den Befehl [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) aus, um einen IoT-Hub zu erstellen. Es kann einige Minuten dauern, bis ein IoT-Hub erstellt wurde. 

    *YourIotHubName*: Ersetzen Sie diesen Platzhalter und die umgebenden geschweiften Klammern im folgenden Befehl durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben. Der Name eines IoT-Hubs muss in Azure global eindeutig sein. Verwenden Sie im weiteren Verlauf dieser Schnellstartanleitung den Namen des IoT-Hubs für alle Vorkommen dieses Platzhalters.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > Nach dem Erstellen eines IoT-Hubs verwenden Sie im weiteren Verlauf dieser Schnellstartanleitung Azure IoT Explorer für die Interaktion mit Ihrem IoT-Hub. IoT Explorer ist eine GUI-Anwendung, mit der Sie eine Verbindung mit einer vorhandenen IoT Hub sowie Geräte hinzufügen, verwalten und überwachen können. Weitere Informationen finden Sie unter [Installieren und Verwenden des Azure IoT-Explorers](../articles/iot-fundamentals/howto-use-iot-explorer.md). Optional können Sie weiterhin CLI-Befehle verwenden.

### <a name="configure-iot-explorer"></a>Konfigurieren von IoT Explorer

Im weiteren Verlauf dieses Schnellstarts verwenden Sie IoT Explorer zum Registrieren eines Geräts bei Ihrem IoT-Hub und zum Anzeigen der Gerätetelemetriedaten. In diesem Abschnitt konfigurieren Sie IoT Explorer, um eine Verbindung mit dem soeben erstellten IoT-Hub herzustellen und Plug & Play-Modelle aus dem öffentlichen Modellrepository zu lesen. 

> [!NOTE]
> Sie können auch die Azure CLI verwenden, um ein Gerät zu registrieren. Verwenden Sie den Befehl *[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) -device-id mydevice -hub-name {YourloTHubName}* aus, um ein neues Gerät zu registrieren, und den Befehl *[ az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) -device-id mydevice -hub-name {YourloTHubName}* , um die primäre Verbindungszeichenfolge für das Gerät zu erhalten. Nachdem Sie sich die Verbindungszeichenfolge des Geräts notiert haben, können Sie mit [Ausführen des Gerätebeispiels](#run-the-device-sample) fortfahren.

So fügen Sie Ihrem IoT-Hub eine Verbindung hinzu:

1. Führen Sie den Befehl [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) aus, um die Verbindungszeichenfolge für Ihren loT-Hub zu erhalten.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. Kopieren Sie die Verbindungszeichenfolge ohne die umgebenden Anführungszeichen.
1. Wählen Sie in Azure IoT Explorer im linken Menü **IoT-Hubs** und dann **+ Verbindung hinzufügen** aus.
1. Fügen Sie die Verbindungszeichenfolge im Feld **Verbindungszeichenfolge** ein.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="Screenshot: Hinzufügen einer Verbindung in IoT Explorer":::

1. Wenn die Verbindung erfolgreich hergestellt wurde, wechselt IoT Explorer zur Ansicht **Geräte**.

So fügen Sie das öffentliche Modellrepository hinzu:

1. Wählen Sie in IoT Explorer **Startseite** aus, um zur Ansicht „Startseite“ zurückzukehren.
1. Wählen Sie im linken Menü die Option **IoT Plug & Play-Einstellungen**, anschließend **+Hinzufügen** und dann im Dropdownmenü die Option **Öffentliches Repository** aus.
1. Unter `https://devicemodels.azure.com` wird ein Eintrag für das öffentliche Modellrepository angezeigt.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="Screenshot: Hinzufügen des öffentlichen Modellrepositorys in IoT Explorer":::

1. Wählen Sie **Speichern** aus.

### <a name="register-a-device"></a>Registrieren eines Geräts

In diesem Abschnitt erstellen Sie eine neue Geräteinstanz und registrieren sie bei dem von Ihnen erstellten IoT-Hub. In einem Abschnitt weiter unten verwenden Sie die Verbindungsinformationen für das neu registrierte Gerät zum Herstellen einer sicheren Verbindung für Ihr Gerät.

So registrieren Sie ein Gerät:

1. Wählen Sie in IoT Explorer auf der Startseite die Option **IoT-Hubs** aus.
1. Die zuvor hinzugefügte Verbindung sollte angezeigt werden. Wählen Sie unter den Verbindungseigenschaften die Option **Geräte in diesem Hub anzeigen** aus.
1. Wählen Sie **+ Neu** aus, und geben Sie eine Geräte-ID für Ihr Gerät ein. Beispiel: *mydevice*. Behalten Sie alle anderen Eigenschaften unverändert bei.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Screenshot: Azure IoT Explorer-Geräteidentität":::

1. Verwenden Sie die Schaltflächen zum Kopieren, um das Feld **Primäre Verbindungszeichenfolge** zu kopieren und sich die Zeichenfolge zu notieren. Sie benötigen diese Verbindungszeichenfolge später.
