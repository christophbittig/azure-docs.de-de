---
title: Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe symmetrischer Schlüssel – -Azure IoT Edge | Microsoft-Dokumentation
description: Erstellen und Bereitstellen eines einzelnen IoT Edge-Geräts in IoT Hub für die manuelle Bereitstellung mit symmetrischen Schlüsseln
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: kgremban
ms.openlocfilehash: 8d0db2b4aa516be4da48a6d80904cfd485e8462e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490561"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-symmetric-keys"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe von symmetrischen Schlüsseln

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In diesem Artikel finden Sie umfassende Anweisungen zum Registrieren und Bereitstellen eines IoT Edge-Geräts unter Linux, darunter auch zum Installieren von IoT Edge.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird für Produktionsszenarien empfohlen.

In diesem Artikel wird die Verwendung von symmetrischen Schlüsseln als Authentifizierungsmethode beschrieben. Wenn Sie X.509-Zertifikate verwenden möchten, lesen Sie [Erstellen und Bereitstellen eines IoT Edge-Geräts unter Linux mithilfe von X.509-Zertifikaten](how-to-provision-single-device-linux-x509.md).

> [!NOTE]
> Wenn Sie viele Geräte einrichten müssen und sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel, wie IoT Edge beim IoT Hub Device Provisioning Service (Gerätebereitstellungsdienst) funktioniert:
>
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-x509.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM](how-to-provision-devices-at-scale-linux-tpm.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von symmetrischen Schlüsseln](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie Ihr IoT Edge-Gerät registrieren und darauf IoT Edge installieren. Bei diesen Aufgaben gibt es unterschiedliche Voraussetzungen und Hilfsprogramme zu deren Erledigung. Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie den Vorgang fortsetzen.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein X64-, ARM32- oder ARM64-Linux-Gerät.

Microsoft bietet Installationspakete für die Betriebssysteme Ubuntu Server 18.04 und Raspberry Pi OS Stretch.

Neueste Informationen dazu, welche Betriebssysteme zurzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems).

>[!NOTE]
>Die Unterstützung für ARM64-Geräte befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie nun für den nächsten Schritt bereit, nämlich das Einrichten des Geräts mit seinen Cloudidentitäts- und Authentifizierungsinformationen.

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
     device_connection_string: "ADD_DEVICE_CONNECTION_STRING_HERE"
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

Mithilfe des folgenden Befehls können Sie Ihr IoT Edge-Gerät schnell mit der Authentifizierung über symmetrische Schlüssel konfigurieren:

   ```bash
   sudo iotedge config mp --connection-string 'PASTE_DEVICE_CONNECTION_STRING_HERE'
   ```

   Der Befehl `iotedge config mp` erstellt eine Konfigurationsdatei auf dem Gerät und trägt Ihre Verbindungszeichenfolge in die Datei ein.

Übernehmen Sie die Konfigurationsänderungen.

   ```bash
   sudo iotedge config apply
   ```

Wenn Sie die Konfigurationsdatei anzeigen möchten, können Sie sie öffnen:

   ```bash
   sudo nano /etc/aziot/config.toml
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
      curl -L libiothsm-std_link_here -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Suchen Sie die **iotedge**-Datei, die der Architektur Ihres IoT Edge-Geräts entspricht. Klicken Sie mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse.

   4. Verwenden Sie den kopierten Link im folgenden Befehl, um diese Version des IoT Edge-Sicherheits-Daemons zu installieren.

      ```bash
      curl -L iotedge_link_here -o iotedge.deb && sudo apt-get install ./iotedge.deb
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
