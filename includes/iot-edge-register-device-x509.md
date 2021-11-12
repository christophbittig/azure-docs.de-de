---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 34aa41ae6762790bfa41cb612aac04418cfea212
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576809"
---
## <a name="register-your-device"></a>Registrieren Ihres Geräts

Je nach Vorlieben können Sie Ihr Gerät über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** registrieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/iot-edge-register-device-x509/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID. Notieren Sie sich die Geräte-ID, da Sie sie später verwenden werden.
   * Wählen Sie als Authentifizierungstyp **X.509, selbstsigniert** aus.
   * Geben Sie die primären und sekundären Identitätszertifikatfingerabdrücke an. Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes.

1. Wählen Sie **Speichern**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Derzeit unterstützt die Azure IoT-Erweiterung für Visual Studio Code die Geräteregistrierung mit X.509-Zertifikaten nicht.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity create](/cli/azure/iot/hub/device-identity), um eine neue Geräteidentität in Ihrem IoT-Hub zu erstellen. Beispiel:

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA_thumbprint] --secondary-thumbprint [SHA_thumbprint]
   ```

Dieser Befehl umfasse mehrere Parameter:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der für Ihren IoT Hub eindeutig ist. Notieren Sie sich die Geräte-ID, da Sie diese im nächsten Abschnitt verwenden.
* `hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.
* `--auth-method` oder `--am`: Deklarieren Sie den vom Gerät verwendeten Autorisierungstyp. In diesem Fall verwenden Sie X.509-Zertifikatfingerabdrücke.
* `--primary-thumbprint` oder `--ptp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Primärschlüssel verwendet werden soll.
* `--secondary-thumbprint` oder `--stp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Sekundärschlüssel verwendet werden soll.

---

Nachdem Sie nun ein Gerät in IoT Hub registriert haben, rufen Sie die Informationen ab, die Sie zum Abschließen der Installation und Bereitstellung der IoT Edge-Runtime benötigen.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Anzeigen registrierter Geräte und Abrufen von Informationen für die Bereitstellung

Geräte mit Verwendung der X.509-Zertifikat-Authentifizierung benötigen den zugehörigen IoT-Hub-Namen, den Gerätenamen und die Zertifikatsdateien, um die Installation und Bereitstellung der IoT Edge-Runtime abzuschließen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit dem Azure-Portal](./media/iot-edge-register-device-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Obwohl die Geräteregistrierung mit X.509-Zertifikaten über Visual Studio Code nicht unterstützt wird, können Sie Ihre IoT Edge-Geräte bei Bedarf dennoch anzeigen.

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit VS Code](./media/iot-edge-register-device-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/iot/hub/device-identity), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge**-Eigenschaft mit dem Wert **true** auf.

---
