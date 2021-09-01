---
title: Installieren von Azure IoT Edge | Microsoft-Dokumentation
description: Anweisungen zur Azure IoT Edge-Installation auf Windows- oder Linux-Geräten
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kgremban
ms.openlocfilehash: 0f0cf8f4ce8f82964a69635debacea772c4310da
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340242"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Installieren oder Deinstallieren von Azure IoT Edge für Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen. Weitere Informationen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

In diesem Artikel werden die Schritte zum Installieren der Azure IoT Edge-Runtime auf Linux-Geräten aufgeführt.

## <a name="prerequisites"></a>Voraussetzungen

* Eine [registrierte Geräte-ID](how-to-register-device.md)

  Wenn Sie Ihr Gerät mithilfe einer Authentifizierung mit symmetrischen Schlüsseln registriert haben, halten Sie die Verbindungszeichenfolge für das Gerät bereit.

  Wenn Sie Ihr Gerät mithilfe einer Authentifizierung mit selbst signiertem X.509-Zertifikat registriert haben, muss mindestens eines der Zertifikate, das Sie zum Registrieren des Geräts verwendet haben, und dessen übereinstimmender privater Schlüssel auf Ihrem Gerät zur Verfügung stehen.

* Ein Linux-Gerät

  Sie müssen über ein x64-, ARM32- oder ARM64 Linux-Gerät verfügen. Microsoft bietet Installationspakete für die Betriebssysteme Ubuntu Server 18.04 und Raspberry Pi OS Stretch.

  Neueste Informationen dazu, welche Betriebssysteme zurzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems).

  >[!NOTE]
  >Die Unterstützung für ARM64-Geräte befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Vorbereiten Ihres Geräts für den Zugriff auf die Microsoft-Installationspakete.

  Installieren Sie die zum Betriebssystem Ihres Geräts passende Repositorykonfiguration.

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Kopieren Sie die generierte Liste in das Verzeichnis „sources.list.d“.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Für Azure IoT Edge-Softwarepakete gelten die in jedem Paket (im Verzeichnis `usr/share/doc/{package-name}` oder `LICENSE`) enthaltenen Lizenzbedingungen. Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

## <a name="install-a-container-engine"></a>Installieren einer Containerengine

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

## <a name="install-iot-edge"></a>Installieren von IoT Edge

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

Wenn Sie aber lieber eine andere IoT Edge-Version als die neueste installieren möchten, müssen Sie für den `aziot-edge`- und den `aziot-identity-service`-Dienst unbedingt dieselbe Version installieren.

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie nun für den nächsten Schritt bereit, nämlich das Einrichten des Geräts mit seinen Cloudidentitäts- und Authentifizierungsinformationen.

Wählen Sie den nächsten Abschnitt auf der Grundlage des gewünschten Authentifizierungstyps aus:

* [Option 1: Authentifizieren mit symmetrischen Schlüsseln](#option-1-authenticate-with-symmetric-keys)
* [Option 2: Authentifizieren mit X.509-Zertifikaten](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Option 1: Authentifizieren mit symmetrischen Schlüsseln

Zu diesem Zeitpunkt wird die IoT Edge-Runtime auf Ihrem Linux-Gerät installiert, und Sie müssen das Gerät mit seiner Cloud-Identität und seinen Authentifizierungsinformationen bereitstellen.

In diesem Abschnitt werden die Schritte zum Bereitstellen eines Geräts mit einer Authentifizierung mit symmetrischem Schlüssel erläutert. Sie sollten Ihr Gerät in IoT Hub registriert und die Verbindungszeichenfolge aus den Geräteinformationen abgerufen haben. Führen Sie andernfalls die Schritte in [Registrieren eines IoT Edge Geräts in IoT Hub](how-to-register-device.md) aus.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Suchen Sie die Bereitstellungskonfigurationen der Datei, und heben Sie die Auskommentierung des Abschnitts **Manual provisioning configuration using a connection string** (Manuelle Bereitstellungskonfiguration mit einer Verbindungszeichenfolge) auf, wenn dies nicht bereits geschehen ist.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Aktualisieren Sie den Wert von **device_connection_string** durch die Verbindungszeichenfolge Ihres IoT Edge-Geräts. Stellen Sie sicher, dass alle anderen Bereitstellungsabschnitte auskommentiert sind. Stellen Sie sicher, dass der Zeile **provisioning:** kein Leerzeichen vorangestellt ist und dass geschachtelte Elemente jeweils um zwei Leerzeichen eingerückt sind.

Verwenden Sie zum Einfügen des Inhalts der Zwischenablage in Nano `Shift+Right Click`, oder drücken Sie `Shift+Insert`.

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

Suchen Sie den Abschnitt **Bereitstellung** der Datei, und heben Sie die Auskommentierung der Zeilen für manuelle Bereitstellung mit Verbindungszeichenfolge auf.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Aktualisieren Sie den Wert von **connection_string** mit der Verbindungszeichenfolge Ihres IoT Edge-Geräts.

Verwenden Sie zum Einfügen des Inhalts der Zwischenablage in Nano `Shift+Right Click`, oder drücken Sie `Shift+Insert`.

Speichern und schließen Sie die Datei.

   `CTRL + X`, `Y`, `Enter`

Nachdem Sie die Bereitstellungsinformationen in der Konfigurationsdatei eingegeben haben, übernehmen Sie Ihre Änderungen:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Option 2: Authentifizieren mit X.509-Zertifikaten

Zu diesem Zeitpunkt wird die IoT Edge-Runtime auf Ihrem Linux-Gerät installiert, und Sie müssen das Gerät mit seiner Cloud-Identität und seinen Authentifizierungsinformationen bereitstellen.

In diesem Abschnitt werden die Schritte zum Bereitstellen eines Geräts mit einer Authentifizierung mit X.509-Zertifikat erläutert. Sie sollten Ihr Gerät in IoT Hub registriert und dabei Fingerabdrücke bereitgestellt haben, die mit dem Zertifikat und dem privaten Schlüssel auf Ihrem IoT Edge-Gerät übereinstimmen. Führen Sie andernfalls die Schritte in [Registrieren eines IoT Edge Geräts in IoT Hub](how-to-register-device.md) aus.

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
