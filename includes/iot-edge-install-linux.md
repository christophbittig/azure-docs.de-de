---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e4c78fe2037beb23f69700ae7a340a4d5c6cc160
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490560"
---
## <a name="install-iot-edge"></a>Installieren von IoT Edge

In diesem Abschnitt bereiten Sie Ihre Linux-VM oder Ihr physisches Gerät für IoT Edge vor. Anschließend installieren Sie IoT Edge.

Es gibt zwei Schritte, die Sie auf Ihrem Gerät ausführen müssen, bevor es für die Installation der IoT Edge-Runtime bereit ist. Ihr Gerät benötigt Zugang zu den Microsoft-Installationspaketen, und es muss eine Container-Engine installiert werden.

### <a name="access-the-microsoft-installation-packages"></a>Zugriff auf die Microsoft-Installationspakete

1. Laden Sie das zum Betriebssystem Ihres Geräts passende Repositorykonfigurationspaket herunter.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

1. Installieren Sie das Konfigurationspaket, um das Paketrepository und den öffentlichen GPG-Schlüssel von Microsoft hinzuzufügen.

   ```bash
   sudo apt install ./packages-microsoft-prod.deb
   ```

> [!NOTE]
> Für Azure IoT Edge-Softwarepakete gelten die in jedem Paket (im Verzeichnis `usr/share/doc/{package-name}` oder `LICENSE`) enthaltenen Lizenzbedingungen. Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

### <a name="install-a-container-engine"></a>Installieren einer Containerengine

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. Für Produktionsszenarien empfehlen wir die Verwendung der Moby-Engine. Die Moby-Engine ist die einzige Container-Engine, die offiziell von IoT Edge unterstützt wird. Docker CE/EE-Containerimages sind mit der Moby-Runtime kompatibel.

1. Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

1. Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Wenn beim Installieren der Moby-Container-Engine Fehler angezeigt werden, überprüfen Sie Ihren Linux-Kernel auf Moby-Kompatibilität. Einige Hersteller von eingebetteten Geräten stellen Geräteimages mit benutzerdefinierten Linux-Kernels bereit, denen die Funktionen für die Containerengine-Kompatibilität fehlen. Führen Sie den folgenden Befehl aus, der das von Moby bereitgestellte [check-config-Skript](https://github.com/moby/moby/blob/master/contrib/check-config.sh) verwendet, um die Kernelkonfiguration zu überprüfen:
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > Überprüfen Sie in der Ausgabe des Skripts, ob alle Elemente unter `Generally Necessary` und `Network Drivers` aktiviert sind. Wenn Funktionen fehlen, aktivieren Sie diese, indem Sie Ihren Kernel aus der Quelle neu erstellen und die zugehörigen Module auswählen, die in die entsprechende Konfigurationsdatei für den Kernel eingebunden werden sollen. Gleiches gilt, wenn Sie einen Kernelkonfigurationsgenerator wie `defconfig` oder `menuconfig` verwenden: Suchen und aktivieren Sie die entsprechenden Funktionen, und erstellen Sie dann den Kernel dementsprechend neu. Sobald Sie Ihren neu bearbeiteten Kernel bereitgestellt haben, führen Sie das check-config-Skript erneut aus, um zu überprüfen, ob alle erforderlichen Funktionen erfolgreich aktiviert wurden.

### <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Der Daemon für IoT Edge-Sicherheit dient zum Bereitstellen und Einhalten von Sicherheitsstandards auf dem IoT Edge-Gerät. Der Daemon wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version, z. B eine Vorabversion, installieren oder eine Offlineinstallation durchführen müssen, führen Sie die Schritte unter **Offlineinstallation oder Installation einer bestimmten Version** weiter unten in diesem Artikel aus.

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

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version, z. B eine Vorabversion, installieren oder eine Offlineinstallation durchführen müssen, führen Sie die Schritte unter **Offlineinstallation oder Installation einer bestimmten Version** weiter unten in diesem Artikel aus.

>[!NOTE]
>In diesem Abschnitt werden die Schritte zum Installieren der IoT Edge-Version 1.2 gezeigt.
>
>Wenn Sie bereits ein IoT Edge-Gerät haben, auf dem eine ältere Version ausgeführt wird, und ein Upgrade auf 1.2 durchführen möchten, führen Sie die Schritte unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](../articles/iot-edge/how-to-update-iot-edge.md) aus. Version 1.2 unterscheidet sich ausreichend von früheren IoT Edge-Versionen, sodass bestimmte Schritte für das Upgrade erforderlich sind.

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
