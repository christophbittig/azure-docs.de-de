---
title: 'Registrieren eines neuen Geräts: Azure IoT Edge | Microsoft-Dokumentation'
description: Registrieren eines einzelnen IoT Edge-Geräts in IoT Hub für die manuelle Bereitstellung mit symmetrischen Schlüsseln oder X.509-Zertifikaten
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 82aa2eb24905a4166de474fbaf0baa1213abce53
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659334"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrieren eines IoT Edge-Geräts in IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In diesem Artikel werden die Schritte zum Registrieren eines neuen IoT Edge-Geräts in IoT Hub beschrieben.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird in Produktionsszenarien empfohlen.

In diesem Artikel werden beide Authentifizierungsmethoden behandelt.

Wenn Sie über sehr viele Geräte verfügen, die Sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel über die Integration von IoT Edge in IoT Hub Device Provisioning Service:

* [Erstellen und Bereitstellen eines IoT Edge-Geräts mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-x509.md)
* [Erstellen und Bereitstellen eines IoT Edge-Geräts mit einem TPM unter Linux](how-to-provision-devices-at-scale-linux-tpm.md)
* [Erstellen und Bereitstellen eines IoT Edge-Geräts mithilfe des Nachweises symmetrischer Schlüssel](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.
* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 oder höher lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.

---

## <a name="option-1-register-with-symmetric-keys"></a>Option 1: Registrieren mit symmetrischen Schlüsseln

Sie können verschiedene Tools verwenden, um ein neues IoT Edge-Gerät in IoT Hub zu registrieren und die Verbindungszeichenfolge abzurufen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID.
   * Wählen Sie als Authentifizierungstyp **Symmetrischer Schlüssel** aus.
   * Verwenden Sie die Standardeinstellungen, um Authentifizierungsschlüssel automatisch zu generieren und das neue Gerät mit Ihrem Hub zu verbinden.

1. Wählen Sie **Speichern**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihren Hub auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.
1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

   ![Erweitern des Abschnitts „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte)](./media/how-to-register-device/azure-iot-hub-devices.png)

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
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Dieser Befehl weist drei Parameter auf:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der in Ihrem IoT-Hub eindeutig ist.
* `--hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Nachdem Sie nun ein Gerät in IoT Hub registriert haben, rufen Sie die Verbindungszeichenfolge ab, mit der Sie die Installation und Bereitstellung der IoT Edge-Runtime abschließen. Führen Sie die weiter unten in diesem Artikel beschriebenen Schritte zum [Anzeigen registrierter Geräte und Abrufen von Verbindungszeichenfolgen](#view-registered-devices-and-retrieve-connection-strings) aus.

## <a name="option-2-register-with-x509-certificates"></a>Option 2: Registrieren mit X.509-Zertifikaten

Die manuelle Bereitstellung mit X.509-Zertifikaten erfordert die IoT Edge-Version 1.0.10 oder höher.

Bei der Authentifizierung mit einem X.509-Zertifikat werden die Informationen zur Authentifizierung der einzelnen Geräte in Form eines *Fingerabdrucks* bereitgestellt, der den Geräteidentitätszertifikaten entnommen wurde. Diese Fingerabdrücke werden IoT Hub zum Zeitpunkt der Geräteregistrierung bereitgestellt, damit der Dienst das Gerät erkennen kann, wenn es eine Verbindung herstellt.

### <a name="create-certificates-and-thumbprints"></a>Erstellen von Zertifikaten und Fingerabdrücken

Wenn Sie ein IoT Edge-Gerät mit X.509-Zertifikaten bereitstellen, verwenden Sie das sogenannte *Geräteidentitätszertifikat*. Dieses Zertifikat wird nur für die Bereitstellung eines IoT Edge-Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Es handelt sich dabei um ein Blattzertifikat, mit dem keine anderen Zertifikate signiert werden können. Das Geräteidentitätszertifikat hängt nicht mit den Zertifikaten der Zertifizierungsstelle zusammen, die das IoT Edge-Gerät Modulen oder Downstreamgeräten zur Überprüfung präsentiert. Weitere Informationen zur Aufgabe der verschiedenen Zertifikate der Zertifizierungsstelle auf IoT Edge-Geräten finden Sie unter [Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge](iot-edge-certs.md).

Für die manuelle Bereitstellung mit X.509 ist Folgendes erforderlich:

* Zwei Geräteidentitätszertifikate mit ihren entsprechenden Zertifikaten mit privaten Schlüsseln im CER- oder PEM-Format.

  Eine Gruppe von Zertifikat-/Schlüsseldateien wird für die IoT Edge-Runtime bereitgestellt. Wenn Sie Geräteidentitätszertifikate erstellen, legen Sie den allgemeinen Namen (Common Name, CN) des Zertifikats auf die Geräte-ID fest, die das Gerät in Ihrem IoT-Hub haben soll.

* Fingerabdrücke von beiden Geräteidentitätszertifikaten.

  Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes. Beide Fingerabdrücke werden zum Zeitpunkt der Geräteregistrierung für IoT Hub bereitgestellt.

Sollten Sie über keine Zertifikate verfügen, können Sie [Demozertifikate zum Testen von IoT Edge-Gerätefeatures erstellen](how-to-create-test-certificates.md). Gehen Sie wie in dem Artikel beschrieben vor, um Zertifikaterstellungsskripts einzurichten und ein Zertifikat der Stammzertifizierungsstelle sowie zwei IoT Edge-Geräteidentitätszertifikate zu erstellen.

Der Fingerabdruck kann beispielsweise mithilfe des folgenden OpenSSL-Befehls aus einem Zertifikat abgerufen werden:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrieren eines neuen Geräts

Sie können verschiedene Tools verwenden, um ein neues IoT Edge-Gerät in IoT Hub zu registrieren und seine Zertifikatfingerabdrücke hochzuladen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID. Notieren Sie sich die Geräte-ID, da Sie diese im nächsten Abschnitt verwenden.
   * Wählen Sie als Authentifizierungstyp **X.509, selbstsigniert** aus.
   * Geben Sie die primären und sekundären Identitätszertifikatfingerabdrücke an. Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes.

1. Wählen Sie **Speichern**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Derzeit unterstützt die Azure IoT-Erweiterung für Visual Studio Code die Geräteregistrierung mit X.509-Zertifikaten nicht.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity create](/cli/azure/iot/hub/device-identity), um eine neue Geräteidentität in Ihrem IoT-Hub zu erstellen. Beispiel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Dieser Befehl umfasse mehrere Parameter:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der für Ihren IoT Hub eindeutig ist. Notieren Sie sich die Geräte-ID, da Sie diese im nächsten Abschnitt verwenden.
* `hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.
* `--auth-method` oder `--am`: Deklarieren Sie den vom Gerät verwendeten Autorisierungstyp. In diesem Fall verwenden Sie X.509-Zertifikatfingerabdrücke.
* `--primary-thumbprint` oder `--ptp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Primärschlüssel verwendet werden soll.
* `--secondary-thumbprint` oder `--stp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Sekundärschlüssel verwendet werden soll.

---

Nachdem Sie ein Gerät in IoT Hub registriert haben, können Sie die IoT Edge-Runtime auf Ihrem Gerät installieren und bereitstellen. IoT Edge-Geräte, die sich mit X.509-Zertifikaten authentifizieren, verwenden keine Verbindungszeichenfolgen. Sie können daher mit dem nächsten Schritt fortfahren:

* [Installieren oder Deinstallieren von Azure IoT Edge für Linux](how-to-install-iot-edge.md)
* [Installieren oder Deinstallieren von Azure IoT Edge für Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Anzeigen registrierter Geräte und Abrufen von Verbindungszeichenfolgen

Für Geräte, die die Authentifizierung mit symmetrischen Schlüsseln verwenden, wird eine eigene Verbindungszeichenfolge benötigt, um die Installation und Bereitstellung der IoT Edge-Runtime abzuschließen.

Bei Geräten mit Authentifizierung per X.509-Zertifikat sind keine Verbindungszeichenfolgen erforderlich. Stattdessen werden für diese Geräte der IoT-Hub-Name, der Gerätename und die zugehörigen Zertifikatsdateien benötigt, damit die Installation und Bereitstellung der IoT Edge-Runtime abgeschlossen werden kann.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit dem Azure-Portal](./media/how-to-register-device/portal-view-devices.png)

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

Sie können die Verbindungszeichenfolgen von Geräten, die sich mit symmetrischen Schlüsseln authentifizieren, im Portal kopieren.

1. Klicken Sie im Portal auf der Seite **IoT Edge** in der Liste der IoT Edge-Geräte auf die Geräte-ID.
2. Kopieren Sie entweder den Wert der **primären Verbindungszeichenfolge** oder der **sekundären Verbindungszeichenfolge**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Anzeigen von IoT Edge-Geräten mit Visual Studio Code

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit VS Code](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Abrufen der Verbindungszeichenfolge mit Visual Studio Code

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

1. Klicken Sie mit der rechten Maustaste auf die ID des Geräts im Abschnitt **Azure IoT Hub**.
1. Wählen Sie **Copy Device Connection String** (Verbindungszeichenfolge des Geräts kopieren) aus.

   Die Verbindungszeichenfolge wird in die Zwischenablage kopiert.

Sie können auch im Kontextmenü **Get Device Info** (Geräteinformationen abrufen) auswählen, um alle Geräteinformationen einschließlich der Verbindungszeichenfolge im Ausgabefenster anzuzeigen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Anzeigen von IoT Edge-Geräten mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/iot/hub/device-identity), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge**-Eigenschaft mit dem Wert **true** auf.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Abrufen der Verbindungszeichenfolge mit der Azure-Befehlszeilenschnittstelle

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft. Verwenden Sie den Befehl [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string), um die Verbindungszeichenfolge für ein einzelnes Gerät zurückzugeben:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>Der Befehl `connection-string show` wurde in Version 0.9.8 der Azure IoT-Erweiterung eingeführt und ersetzt den veralteten Befehl `show-connection-string`. Wenn Sie beim Ausführen dieses Befehls einen Fehler erhalten, stellen Sie sicher, dass die Erweiterung mindestens auf Version 0.9.8 aktualisiert wurde. Weitere Informationen und die neuesten Updates finden Sie unter der [Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension).

Beim Wert für den Parameter `device-id` muss die Groß-/Kleinschreibung beachtet werden.

Achten Sie darauf, beim Kopieren der Verbindungszeichenfolge für die Verwendung auf einem Gerät nicht die Anführungszeichen um die Verbindungszeichenfolge herum mit zu kopieren.

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie ein Gerät in IoT Hub registriert haben, können Sie die IoT Edge-Runtime auf Ihrem Gerät installieren und bereitstellen.

* [Installieren oder Deinstallieren von Azure IoT Edge für Linux](how-to-install-iot-edge.md)
* [Installieren oder Deinstallieren von Azure IoT Edge für Windows](how-to-install-iot-edge-windows-on-windows.md)