---
title: Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe eines X.509-Zertifikats – Azure IoT Edge | Microsoft-Dokumentation
description: Erstellen und Bereitstellen eines einzelnen IoT Edge-Geräts in IoT Hub für die manuelle Bereitstellung mit X.509-Zertifikaten
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: v-tcassi
ms.openlocfilehash: 5116774d0ea68f4d78dd0721a3546d9d82cbfdbd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270432"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel enthält End-to-End-Anweisungen zum Registrieren und Bereitstellen eines IoT Edge-Geräts unter Linux, einschließlich Anweisungen zur Installation von IoT Edge.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird für Produktionsszenarios empfohlen.

In diesem Artikel wird die Verwendung von X.509-Zertifikaten als Authentifizierungsmethode behandelt. Wenn Sie symmetrische Schlüssel verwenden möchten, finden Sie weitere Informationen unter [Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe von symmetrischen Schlüsseln](how-to-provision-single-device-linux-symmetric.md).

> [!NOTE]
> Wenn Sie über sehr viele Geräte verfügen, die Sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel über die Integration von IoT Edge in IoT Hub Device Provisioning Service:
>
> * [Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-x509.md)
> * [Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mit einem TPM](how-to-auto-provision-simulated-device-linux.md)
> * [Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mithilfe von symmetrischen Schlüsseln](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie Ihr IoT Edge-Gerät registrieren und darauf IoT Edge installieren. Für diese Aufgaben gibt es unterschiedliche Voraussetzungen und Hilfsprogramme zu deren Erledigung. Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie fortfahren.

### <a name="device-registration"></a>Geräteregistrierung

Sie können die Schritte zum Registrieren Ihres Geräts über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** ausführen. Bei jedem Hilfsprogramm gibt es eigene Voraussetzungen:

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code

> [!NOTE]
> Derzeit unterstützt die Azure IoT-Erweiterung für Visual Studio Code die Geräteregistrierung mit X.509-Zertifikaten nicht.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.
* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 oder höher lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.

---

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein X64-, ARM32- oder ARM64-Linux-Gerät.

Microsoft bietet Installationspakete für die Betriebssysteme Ubuntu Server 18.04 und Raspberry Pi OS Stretch.

Neueste Informationen dazu, welche Betriebssysteme zurzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems).

>[!NOTE]
>Die Unterstützung für ARM64-Geräte befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="generate-device-identity-certificates"></a>Generieren von Geräteidentitätszertifikaten

Die manuelle Bereitstellung mit X.509-Zertifikaten erfordert die IoT Edge-Version 1.0.10 oder höher.

Wenn Sie ein IoT Edge-Gerät mit X.509-Zertifikaten bereitstellen, verwenden Sie das sogenannte *Geräteidentitätszertifikat*. Dieses Zertifikat wird nur für die Bereitstellung eines IoT Edge-Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Es handelt sich dabei um ein Blattzertifikat, mit dem keine anderen Zertifikate signiert werden können. Das Geräteidentitätszertifikat hängt nicht mit den Zertifikaten der Zertifizierungsstelle zusammen, die das IoT Edge-Gerät Modulen oder Downstreamgeräten zur Überprüfung präsentiert.

Bei der Authentifizierung mit einem X.509-Zertifikat werden die Informationen zur Authentifizierung der einzelnen Geräte in Form eines *Fingerabdrucks* bereitgestellt, der den Geräteidentitätszertifikaten entnommen wurde. Diese Fingerabdrücke werden IoT Hub zum Zeitpunkt der Geräteregistrierung bereitgestellt, damit der Dienst das Gerät erkennen kann, wenn es eine Verbindung herstellt.

Weitere Informationen zur Aufgabe der verschiedenen Zertifikate der Zertifizierungsstelle auf IoT Edge-Geräten finden Sie unter [Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge](iot-edge-certs.md).

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

## <a name="register-your-device"></a>Registrieren Ihres Geräts

Je nach Präferenz können Sie Ihr Gerät über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** registrieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/how-to-provision-single-device-linux-x509/portal-add-iot-edge-device.png)

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

Nachdem Sie nun ein Gerät in IoT Hub registriert haben, rufen Sie die Informationen ab, mit denen Sie die Installation und Bereitstellung der IoT Edge-Runtime abschließen.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Anzeigen registrierter Geräte und Abrufen von Bereitstellungsinformationen

Geräte, die die X.509-Zertifikatauthentifizierung verwenden, benötigen ihren IoT-Hub-Namen, ihren Gerätenamen und ihre Zertifikatdateien, um die Installation und Bereitstellung der Iot Edge-Runtime abzuschließen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit dem Azure-Portal](./media/how-to-provision-single-device-linux-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Obwohl die Geräteregistrierung mit X.509-Zertifikaten nicht über Visual Studio Code unterstützt wird, können Sie Ihre IoT Edge-Geräte bei Bedarf weiterhin anzeigen.

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit VS Code](./media/how-to-provision-single-device-linux-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/iot/hub/device-identity), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge**-Eigenschaft mit dem Wert **true** auf.

---

## <a name="install-iot-edge"></a>Installieren von IoT Edge

In diesem Abschnitt bereiten Sie Ihren virtuellen Linux Computer oder Ihr physisches Gerät für loT Edge vor. Anschließend installieren Sie IoT Edge.

Es gibt zwei Schritte, die Sie auf Ihrem Gerät durchführen müssen, bevor es für die Installation der loT Edge-Runtime bereit ist. Ihr Gerät benötigt Zugang zu den Microsoft-Installationspaketen, und es muss eine Container-Engine installiert werden.

### <a name="prepare-your-device-to-access-the-microsoft-installation-packages"></a>Vorbereiten des Geräts für den Zugriff auf die Microsoft-Installationspakete

1. Installieren Sie die zum Betriebssystem Ihres Geräts passende Repositorykonfiguration.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Kopieren Sie die generierte Liste in das Verzeichnis „sources.list.d“.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Für Azure IoT Edge-Softwarepakete gelten die in jedem Paket (im Verzeichnis `usr/share/doc/{package-name}` oder `LICENSE`) enthaltenen Lizenzbedingungen. Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

### <a name="install-a-container-engine-on-your-device"></a>Installieren einer Container-Engine auf Ihrem Gerät

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. Für Produktionsszenarien empfehlen wir die Verwendung der Moby-Engine. Die Moby-Engine ist die einzige Container-Engine, das bei Azure IoT Edge offiziell unterstützt wird. Docker CE/EE-Containerimages sind mit der Moby-Runtime kompatibel.

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

Wenn beim Installieren der Moby-Containerengine Fehler angezeigt werden, überprüfen Sie Ihren Linux-Kernel auf Moby-Kompatibilität. Einige Hersteller von eingebetteten Geräten stellen Geräteimages mit benutzerdefinierten Linux-Kernels bereit, denen die Funktionen für die Containerengine-Kompatibilität fehlen. Führen Sie den folgenden Befehl aus, der das von Moby bereitgestellte [check-config-Skript](https://github.com/moby/moby/blob/master/contrib/check-config.sh) verwendet, um die Kernelkonfiguration zu überprüfen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Überprüfen Sie in der Ausgabe des Skripts, ob alle Elemente unter `Generally Necessary` und `Network Drivers` aktiviert sind. Wenn Funktionen fehlen, aktivieren Sie diese, indem Sie Ihren Kernel aus der Quelle neu erstellen und die zugehörigen Module zum Einbinden in die entsprechende Konfigurationsdatei für den Kernel auswählen. Gleiches gilt, wenn Sie einen Kernelkonfigurationsgenerator wie `defconfig` oder `menuconfig` verwenden: Suchen und aktivieren Sie die entsprechenden Funktionen, und erstellen Sie dann den Kernel dementsprechend neu. Sobald Sie Ihren bearbeiteten Kernel bereitgestellt haben, führen Sie das check-config-Skript erneut aus, um zu überprüfen, ob alle erforderlichen Funktionen erfolgreich aktiviert wurden.

### <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Der Daemon für IoT Edge-Sicherheit dient zum Bereitstellen und Einhalten von Sicherheitsstandards auf dem IoT Edge-Gerät. Der Daemon wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version, z. B eine Vorabversion, installieren oder eine Offlineinstallation durchführen müssen, führen Sie die Schritte unter [Offlineinstallation oder Installation einer bestimmten Version](#offline-or-specific-version-installation-optional) weiter unten in diesem Artikel aus.

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

Installieren Sie IoT Edge, Version 1.1.*, zusammen mit dem Paket **libiothsm-std**:

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge, Version 1.1, ist der Branch für langfristigen Support von IoT Edge. Wenn Sie eine ältere Version ausführen, empfehlen wir, den neuesten Patch zu installieren oder darauf zu aktualisieren, da ältere Versionen nicht mehr unterstützt werden.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Der IoT Edge-Dienst stellt Sicherheitsstandards auf dem IoT Edge-Gerät bereit und sorgt für deren Einhaltung. Der Dienst wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Der IoT-Identitätsdienst wurde zusammen mit Version 1.2 von IoT Edge eingeführt. Dieser Dienst übernimmt die Identitätsbereitstellung und -verwaltung für IoT Edge und andere Gerätekomponenten, die mit IoT Hub kommunizieren müssen.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version, z. B eine Vorabversion, installieren oder eine Offlineinstallation durchführen müssen, führen Sie die Schritte unter [Offlineinstallation oder Installation einer bestimmten Version](#offline-or-specific-version-installation-optional) weiter unten in diesem Artikel aus.

>[!NOTE]
>In diesem Abschnitt werden die Schritte zum Installieren der IoT Edge-Version 1.2 gezeigt.
>
>Wenn Sie bereits ein IoT Edge-Gerät haben, auf dem eine ältere Version ausgeführt wird, und ein Upgrade auf 1.2 durchführen möchten, führen Sie die Schritte unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md) aus. Version 1.2 unterscheidet sich ausreichend von früheren IoT Edge-Versionen, sodass bestimmte Schritte für das Upgrade erforderlich sind.

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

Überprüfen Sie, welche Versionen von IoT Edge und des IoT-Identitätsdiensts zur Verfügung stehen.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Installieren Sie die neueste Version von IoT Edge und des IoT-Identitätsdienstpakets mit dem folgenden Befehl:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie nun für den nächsten Schritt bereit, nämlich das Einrichten des Geräts mit seinen Cloudidentitäts- und Authentifizierungsinformationen.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Suchen Sie in der Datei nach dem Abschnitt mit den Bereitstellungskonfigurationen, und heben Sie die Auskommentierung des Abschnitts **Manual provisioning configuration using an X.509 identity certificate** (Manuelle Bereitstellungskonfiguration mithilfe eines X.509-Identitätszertifikats) auf. Stellen Sie sicher, dass alle anderen Bereitstellungsabschnitte auskommentiert sind. Stellen Sie sicher, dass der Zeile **provisioning:** kein Leerzeichen vorangestellt ist und dass geschachtelte Elemente jeweils um zwei Leerzeichen eingerückt sind.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Aktualisieren Sie die folgenden Felder:

* **iothub_hostname**: Dies ist der Hostname des IoT-Hubs, mit dem das Gerät eine Verbindung herstellt. Beispiel: `{IoT hub name}.azure-devices.net`.
* **device_id**: Dies ist die ID, die Sie bei der Registrierung des Geräts angegeben haben.
* **identity_cert**: Dies ist der URI für ein Identitätszertifikat auf dem Gerät. Beispiel: `file:///path/identity_certificate.pem`.
* **identity_pk**: Dies ist der URI für die Datei mit dem privaten Schlüssel für das bereitgestellte Identitätszertifikat. Beispiel: `file:///path/identity_key.pem`.

Speichern und schließen Sie die Datei.

   `CTRL + X`, `Y`, `Enter`

Starten Sie nach Eingabe der Bereitstellungsinformationen in der Konfigurationsdatei den Daemon neu:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Erstellen Sie die Konfigurationsdatei für Ihr Gerät basierend auf einer Vorlagendatei, die im Rahmen der IoT Edge-Installation bereitgestellt wird.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Suchen Sie den Abschnitt **Bereitstellung** der Datei, und heben Sie die Auskommentierung der Zeilen für manuelle Bereitstellung mit dem X.509-Identitätszertifikat auf. Stellen Sie sicher, dass alle anderen Bereitstellungsabschnitte auskommentiert sind.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Aktualisieren Sie die folgenden Felder:

* **iothub_hostname**: Dies ist der Hostname des IoT-Hubs, mit dem das Gerät eine Verbindung herstellt. Beispiel: `{IoT hub name}.azure-devices.net`.
* **device_id**: Dies ist die ID, die Sie bei der Registrierung des Geräts angegeben haben.
* **identity_cert**: Dies ist der URI für ein Identitätszertifikat auf dem Gerät, z. B.: `file:///path/identity_certificate.pem`. Alternativ können Sie das Zertifikat mithilfe von EST oder einer lokalen Zertifizierungsstelle dynamisch ausstellen.
* **identity_pk**: Dies ist der URI für die Datei mit dem privaten Schlüssel für das bereitgestellte Identitätszertifikat, z. B.: `file:///path/identity_key.pem`. Oder geben Sie einen PKCS#11-URI und dann Ihre Konfigurationsinformationen im Abschnitt **PKCS#11** weiter unten in der Konfigurationsdatei an.

Speichern und schließen Sie die Datei.

   `CTRL + X`, `Y`, `Enter`

Nachdem Sie die Bereitstellungsinformationen in der Konfigurationsdatei eingegeben haben, übernehmen Sie Ihre Änderungen:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass die Runtime erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

>[!TIP]
>Sie benötigen erhöhte Rechte zum Ausführen von `iotedge`-Befehlen. Nachdem Sie sich bei Ihrem Computer abgemeldet und sich nach der Installation der IoT Edge-Runtime zum ersten Mal erneut angemeldet haben, werden Ihre Berechtigungen automatisch aktualisiert. Verwenden Sie bis dahin `sudo` vor den Befehlen.

Überprüfen Sie, ob der IoT Edge-Systemdienst ausgeführt wird.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Eine erfolgreiche Statusantwort ist `Ok`.

::: moniker-end

Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Überprüfen Sie die Konfiguration und den Verbindungsstatus des Geräts mithilfe des Tools `check`.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Verwenden Sie zur Ausführung des Tools „check“ immer `sudo`, auch nachdem Ihre Berechtigungen aktualisiert wurden. Das Tool benötigt erhöhte Rechte für den Zugriff auf die Konfigurationsdatei, um den Konfigurationsstatus überprüfen zu können.

Zeigen Sie alle Module an, die auf Ihrem IoT Edge-Gerät ausgeführt werden. Wenn der Dienst zum ersten Mal gestartet wird, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.

   ```bash
   sudo iotedge list
   ```

Wenn Sie ein neues IoT Edge Gerät erstellen, wird es im Azure-Portal mit dem Statuscode `417 -- The device's deployment configuration is not set` angezeigt. Dieser Status ist normal und bedeutet, dass das Gerät bereit ist, eine Modulbereitstellung zu empfangen.

## <a name="offline-or-specific-version-installation-optional"></a>Offlineinstallation oder Installation einer bestimmten Version (optional)

Die in diesem Abschnitt aufgeführten Schritte sind für Szenarien vorgesehen, die von den Schritten der Standardinstallation nicht abgedeckt werden. Das können beispielsweise sein:

* Die Offlineinstallation von IoT Edge
* Die Installation einer Release Candidate-Version

Mithilfe der in diesem Abschnitt beschriebenen Schritte können Sie eine bestimmte Version der Azure IoT Edge-Runtime installieren, die über `apt-get install` nicht zur Verfügung steht. Die Microsoft-Paketliste enthält nur eine begrenzte Reihe aktueller Versionen und deren Unterversionen. Diese Schritte sind also für diejenigen gedacht, die eine ältere Version oder eine Release Candidate-Version installieren möchten.

Mithilfe von curl-Befehlen können Sie die Komponentendateien direkt aus dem IoT Edge-GitHub-Repository als Ziel verwenden.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Navigieren Sie zu den [Veröffentlichungen von Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases), und suchen Sie die Version, die Sie verwenden möchten.

2. Erweitern Sie den Abschnitt **Assets** für diese Version.

3. Jedes Release sollte neue Dateien für den IoT Edge-Sicherheits-Daemon und hsmlib enthalten. Wenn Sie IoT Edge auf einem Offlinegerät installieren möchten, laden Sie diese Dateien im Voraus herunter. Alternativ können Sie diese Komponenten mit den folgenden Befehlen aktualisieren.

   1. Suchen Sie die **libiothsm-std**-Datei, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   2. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version von hsmlib zu installieren:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Suchen Sie die **iotedge**-Datei, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   4. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version des IoT Edge-Sicherheits-Daemons zu installieren.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Wenn auf Ihrem Gerät zurzeit IoT Edge, Version 1.1 oder älter, ausgeführt wird, deinstallieren Sie die Pakete **iotedge** und **libiothsm-std**, bevor Sie die Schritte in diesem Abschnitt ausführen. Weitere Informationen finden Sie unter [Update von 1.0 oder 1.1 auf 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Navigieren Sie zu den [Veröffentlichungen von Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases), und suchen Sie die Version, die Sie verwenden möchten.

2. Erweitern Sie den Abschnitt **Assets** für diese Version.

3. Jedes Release sollte neue Dateien für IoT Edge und den Identitätsdienst enthalten. Wenn Sie IoT Edge auf einem Offlinegerät installieren möchten, laden Sie diese Dateien im Voraus herunter. Alternativ können Sie diese Komponenten mit den folgenden Befehlen aktualisieren.

   1. Suchen Sie die Datei **aziot-identity-service**, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   2. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version des Identitätsdiensts zu installieren:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Suchen Sie die Datei **aziot-edge**, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   4. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version von IoT Edge zu installieren.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Nachdem die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie nun für den nächsten Schritt bereit, nämlich das [Bereitstellen des Geräts mit seiner Cloudidentität](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Deinstallieren von IoT Edge

Verwenden Sie die folgenden Befehle, wenn Sie die IoT Edge-Installationen von Ihrem Gerät entfernen möchten.

Entfernen Sie die IoT Edge-Runtime.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Verwenden Sie das Flag `--purge`, wenn Sie alle IoT Edge zugeordneten Dateien, einschließlich Ihrer Konfigurationsdateien, löschen möchten. Lassen Sie dieses Flag weg, wenn Sie IoT Edge erneut installieren und künftig dieselben Konfigurationsinformationen verwenden möchten.

Wenn die IoT Edge-Runtime entfernt wird, werden alle von ihr erstellten Container angehalten. Auf Ihrem Gerät sind sie jedoch weiterhin vorhanden. Zeigen Sie alle Container an, um die verbliebenen zu sehen.

```bash
sudo docker ps -a
```

Löschen Sie die Container von Ihrem Gerät, einschließlich der zwei Laufzeitcontainer.

```bash
sudo docker rm -f <container name>
```

Entfernen Sie schließlich die Containerruntime von Ihrem Gerät.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
