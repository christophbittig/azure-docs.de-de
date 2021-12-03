---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11a89e1333ddcf3afd3594c3bd2ce46eda6513f1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131577586"
---
## <a name="register-your-device"></a>Registrieren Ihres Geräts

Je nach Bedarf können Sie Ihr Gerät über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** registrieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/iot-edge-register-device-symmetric/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID. Notieren Sie sich die Geräte-ID, da Sie sie später verwenden werden.
   * Wählen Sie als Authentifizierungstyp **Symmetrischer Schlüssel** aus.
   * Verwenden Sie die Standardeinstellungen, um Authentifizierungsschlüssel automatisch zu generieren und das neue Gerät mit Ihrem Hub zu verbinden.

1. Wählen Sie **Speichern**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihren Hub auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.
1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

   ![Erweitern des Abschnitts „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte)](./media/iot-edge-register-device-symmetric/azure-iot-hub-devices.png)

1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.
1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.
1. Wenn Sie nicht bei Ihrem Azure-Konto angemeldet sind, befolgen Sie die angezeigten Aufforderungen.
1. Wählen Sie Ihr Azure-Abonnement.
1. Wählen Sie Ihren IoT Hub aus.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrieren eines neuen Geräts mit Visual Studio Code

1. Erweitern Sie im Visual Studio Code-Explorer den Abschnitt **Azure IoT Hub**.
1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.
1. Wählen Sie **Create IoT Edge Device** (IoT Edge-Gerät erstellen) aus.
1. Geben Sie im geöffneten Textfeld eine ID für Ihr Gerät an.

Auf dem Ausgabebildschirm wird das Ergebnis des Befehls angezeigt. Die Geräteinformationen werden ausgegeben, einschließlich der **deviceID**, die Sie angegeben haben, und der Zeichenfolge **connectionString**, mit der Sie Ihr physisches Gerät mit Ihrem IoT Hub verbinden können.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity create](/cli/azure/iot/hub/device-identity), um eine neue Geräteidentität in Ihrem IoT-Hub zu erstellen. Beispiel:

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled
   ```

Dieser Befehl weist drei Parameter auf:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der in Ihrem IoT-Hub eindeutig ist.
* `--hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.

   ![az iot hub device-identity create output](./media/iot-edge-register-device-symmetric/create-edge-device-cli.png)

---

Nachdem Sie ein Gerät in IoT Hub registriert haben, rufen Sie jetzt die Verbindungszeichenfolge ab, mit der Sie die Installation und Bereitstellung der IoT Edge-Runtime abschließen.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Anzeigen registrierter Geräte und Abrufen von Bereitstellungsinformationen

Für Geräte, die die Authentifizierung mit symmetrischen Schlüsseln verwenden, wird eine eigene Verbindungszeichenfolge benötigt, um die Installation und Bereitstellung der IoT Edge-Runtime abzuschließen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit dem Azure-Portal](./media/iot-edge-register-device-symmetric/portal-view-devices.png)

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

Sie können die Verbindungszeichenfolgen von Geräten, die sich mit symmetrischen Schlüsseln authentifizieren, im Portal kopieren.

1. Klicken Sie im Portal auf der Seite **IoT Edge** in der Liste der IoT Edge-Geräte auf die Geräte-ID.
2. Kopieren Sie entweder den Wert der **primären Verbindungszeichenfolge** oder der **sekundären Verbindungszeichenfolge**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit VS Code](./media/iot-edge-register-device-symmetric/view-devices.png)

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

1. Klicken Sie mit der rechten Maustaste auf die ID des Geräts im Abschnitt **Azure IoT Hub**.
1. Wählen Sie **Copy Device Connection String** (Verbindungszeichenfolge des Geräts kopieren) aus.

   Die Verbindungszeichenfolge wird in die Zwischenablage kopiert.

Sie können auch im Kontextmenü **Get Device Info** (Geräteinformationen abrufen) auswählen, um alle Geräteinformationen einschließlich der Verbindungszeichenfolge im Ausgabefenster anzuzeigen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/iot/hub/device-identity), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge**-Eigenschaft mit dem Wert **true** auf.

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft. Verwenden Sie den Befehl [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string), um die Verbindungszeichenfolge für ein einzelnes Gerät zurückzugeben:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>Der Befehl `connection-string show` wurde in Version 0.9.8 der Azure IoT-Erweiterung eingeführt und ersetzt den veralteten Befehl `show-connection-string`. Wenn Sie beim Ausführen dieses Befehls einen Fehler erhalten, stellen Sie sicher, dass die Erweiterung mindestens auf Version 0.9.8 aktualisiert wurde. Weitere Informationen und die neuesten Updates finden Sie unter der [Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension).

Beim Wert für den Parameter `device-id` muss die Groß-/Kleinschreibung beachtet werden.

Achten Sie darauf, beim Kopieren der Verbindungszeichenfolge für die Verwendung auf einem Gerät nicht die Anführungszeichen um die Verbindungszeichenfolge herum mit zu kopieren.

---
