---
title: Bereitstellen eines Geräts mit einem virtuellen TPM unter Linux – Azure IoT Edge
description: Verwenden eines simulierten TPMs auf einem Linux-Gerät, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7d36bace70e8e539d03e75808152a79b17b35423
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063391"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM unter Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung eines Linux IoT Edge-Geräts mithilfe eines TPMs (Trusted Platform Module). Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

1. Automatische Bereitstellung eines Linux-Geräts mit physischer TPM-Hardware. Beispiel: der [Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e).
1. Automatische Bereitstellung eines virtuellen Linux-Computers (Virtual Machine, VM) mit einem simulierten TPM, das auf einem Windows-Entwicklungscomputer mit aktiviertem Hyper-V ausgeführt wird. Dies wird nur als Testszenario empfohlen, weil ein simuliertes TPM nicht dieselbe Sicherheit wie ein physisches TPM bietet.

Die Anleitungen unterscheiden sich je nach Ihrer Methodik. Sorgen Sie deshalb künftig dafür, dass die richtige Registerkarte angezeigt wird.

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

Aufgaben:

1. Rufen Sie Bereitstellungsinformationen für Ihr TPM ab.
1. Erstellen Sie eine individuelle Registrierung für Ihr Gerät in einer Instanz des IoT Hub Device Provisioning Service.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT-Hub.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

Aufgaben:

1. Erstellen Sie in Hyper-V einen virtuellen Linux-Computer mit einem simulierten TPM für die Hardwaresicherheit.
1. Rufen Sie Bereitstellungsinformationen für Ihr TPM ab.
1. Erstellen Sie eine individuelle Registrierung für Ihr Gerät in einer Instanz des IoT Hub Device Provisioning Service.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT-Hub.

---

## <a name="prerequisites"></a>Voraussetzungen

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

* Ein aktiver IoT-Hub.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

* Aktiver IoT Hub.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.
* Ein Windows-Entwicklungscomputer mit [aktiviertem Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In diesem Artikel wird das Betriebssystem Windows 10 verwendet, auf dem ein Ubuntu Server-VM ausgeführt wird.

---

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird.
>
> Sie können nur einzelne DPS-Registrierungen (keine Gruppe) erstellen, wenn Sie ein TPM verwenden.

## <a name="set-up-your-device"></a>Einrichten des Geräts

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

Wenn Sie ein physisches Linux-Gerät mit einem TPM verwenden, gibt es keine zusätzlichen Schritte zum Einrichten Ihres Geräts.

Sie können den Vorgang fortsetzen.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

In diesem Abschnitt erstellen Sie einen neuen virtuellen Linux-Computer unter Hyper-V. Dieser virtuelle Computer wird mit einem simulierten TPM konfiguriert, um die Funktionsweise der automatischen Bereitstellung mit IoT Edge zu testen.

> [!TIP]
> Wenn Sie einen virtuellen Computer verwenden, werden Sie den virtuellen Computer in diesem Artikel mehrmals kopieren und einfügen. Das Kopieren und Einfügen ist über die Hyper-V-Manager-Verbindungsanwendung nicht einfach. Es empfiehlt sich gegebenenfalls, einmalig über den Hyper-V-Manager eine Verbindung mit dem virtuellen Computer herzustellen, um dessen IP-Adresse abzurufen. Führen Sie `sudo apt install net-tools` und dann `hostname -I` aus. Anschließend können Sie die IP-Adresse verwenden, um eine Verbindung per SSH herzustellen: `ssh <username>@<ipaddress>`.

### <a name="create-a-virtual-switch"></a>Erstellen eines virtuellen Switches

Mit einem virtuellen Switch können Sie Ihren virtuellen Computer mit einem physischen Netzwerk verbinden.

1. Öffnen Sie den Hyper-V-Manager auf Ihrem Windows-Computer.

1. Klicken Sie im Menü **Aktionen** auf **Manager für virtuelle Switches**.

1. Wählen Sie einen **externen** virtuellen Switch, und klicken Sie anschließend auf **Virtuellen Switch erstellen**.

1. Geben Sie Ihrem neuen virtuellen Switch einen Namen. Beispiel: **EdgeSwitch**. Stellen Sie sicher, dass der Verbindungstyp auf **Externes Netzwerk**, festgelegt ist, und klicken Sie anschließend auf **OK**.

1. Ein Popup warnt Sie, dass die Netzwerkkonnektivität möglicherweise unterbrochen wird. Wählen Sie **Yes** (Ja), um fortzufahren.

> [!TIP]
> Wenn beim Erstellen des neuen virtuellen Switches Fehler angezeigt werden, sorgen Sie dafür, dass keine anderen Switches den Ethernet-Adapter und keine anderen Switches denselben Namen verwenden.

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Erstellen Sie einen neuen virtuellen Computer aus einer startbaren Imagedatei.

1. Laden Sie eine für Ihren virtuellen Computer zu verwendende Festplatten-Imagedatei herunter, und speichern Sie sie lokal. Beispiel: [Ubuntu-Server 18.04](http://releases.ubuntu.com/18.04/). Informationen zu unterstützten Betriebssystemen für IoT Edge-Geräte finden Sie unter [Von Azure IoT Edge unterstützte Systeme](/azure/iot-edge/support).

1. Wählen Sie im Hyper-V-Manager im Menü **Aktionen** nacheinander **Aktion** > **Neu** > **Virtueller Computer** aus.

1. Geben Sie im **Assistenten für neue virtuelle Computer** die folgenden speziellen Konfigurationen ein:

   1. **Generation angeben**: Wählen Sie **Generation 2** aus. Für virtuelle Computer der zweiten Generation ist die geschachtelte Virtualisierung aktiviert, die zum Ausführen von IoT Edge auf einem virtuellen Computer erforderlich ist.
   1. **Konfigurieren der Netzwerkeinstellungen**: Legen Sie den Wert der **Verbindung** auf den virtuellen Switch fest, den Sie im vorherigen Abschnitt erstellt haben.
   1. **Installationsoptionen**: Wählen Sie **Betriebssystem von einer startbaren Imagedatei installieren** aus, und navigieren Sie zu der Datenträger-Imagedatei, die Sie lokal gespeichert haben.

1. Wählen Sie im Assistenten die Option **Fertig stellen**, um den virtuellen Computer zu erstellen.

Die Erstellung der neuen VM kann einige Minuten dauern.

### <a name="enable-virtual-tpm"></a>Virtuelles TPM aktivieren

Öffnen Sie nach der Erstellung Ihres virtuellen Computers dessen Einstellungen zum Aktivieren des virtuellen TPMs, mit dem Sie das Gerät automatisch bereitstellen lassen können.

1. Klicken Sie im Hyper-V-Manager mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Einstellungen** aus.

1. Navigieren zu **Sicherheit**.

1. Deaktivieren Sie **Sicheren Start aktivieren**.

1. Aktivieren Sie **Trusted Platform Module aktivieren**.

1. Klicken Sie auf **OK**.

### <a name="start-the-virtual-machine"></a>Starten des virtuellen Computers

Starten Sie Ihren virtuellen Computer, um den Installationsvorgang abzuschließen.

1. Starten Sie im Hyper-V-Manager Ihren virtuellen Computer, und stellen Sie eine Verbindung damit her.

1. Befolgen Sie die Anweisungen auf dem virtuellen Computer, um die Installation abzuschließen und den Computer zu starten.

Sobald die Installation abgeschlossen ist und Sie bei Ihrem virtuellen Computer wieder angemeldet sind, können Sie den Vorgang fortsetzen.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>Abrufen von Bereitstellungsinformationen für Ihr TPM

In diesem Abschnitt erstellen Sie ein Tool, mit dem Sie die **Registrierungs-ID** und den **Endorsement Key** für Ihr TPM abrufen können.

1. Melden Sie sich bei Ihrem Gerät an, und führen Sie dann die Schritte unter [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) zum Installieren und Erstellen des Azure IoT-Geräte-SDKs für C aus.

1. Führen Sie die folgenden Befehle zum Erstellen des SDK-Tools aus, das Ihre Gerätebereitstellungsinformationen für Ihr TPM abruft.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** des Geräts angezeigt. Kopieren Sie diese Werte. Sie werden später zur Erstellung einer individuellen Registrierung für Ihr Gerät in DPS benötigt.

> [!TIP]
> Wenn Sie nicht das SDK-Tool zum Abrufen der Bereitstellungsinformationen verwenden möchten, müssen Sie eine andere Möglichkeit zum Abrufen dieser Informationen finden. Der **Endorsement Key**, der bei jedem TPM-Chip eindeutig ist, wird von dem ihm zugeordneten TPM-Chiphersteller abgerufen. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

Sobald Sie über Ihre Registrierungs-ID und Ihren Endorsement Key verfügen, können Sie den Vorgang fortsetzen.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Rufen Sie die Bereitstellungsinformationen aus Ihrem TPM ab, und verwenden Sie sie zum Erstellen einer individuellen Registrierung im Device Provisioning Service.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik – z. B. Region, Umgebung, Speicherort oder Gerätetyp – festlegen, die in Ihrer Lösung verwendet wird. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Wählen Sie **Individuelle Registrierung hinzufügen** aus, und führen Sie dann die folgenden Schritte zum Konfigurieren der Registrierung aus:

   1. Klicken Sie unter **Mechanismus** auf die Option **TPM**.

   1. Geben Sie den **Endorsement Key** und die **Registrierungs-ID** an, den bzw. die Sie von Ihrem virtuellen Computer oder physischen Gerät kopiert haben.

   1. Stellen Sie nach Wunsch eine ID für Ihr Gerät bereit. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   1. Wählen Sie **True** aus, um zu deklarieren, dass Ihr virtueller Computer oder physisches Gerät ein IoT Edge-Gerät ist.

   1. Wählen Sie die verknüpfte IoT Hub-Instanz, mit der Sie Ihr Gerät verbinden möchten, oder **Link to new IoT Hub** (Mit neuer IoT Hub-Instanz verknüpfen) aus. Sie können mehrere Hubs auswählen. Das Gerät wird dann je nach gewählter Zuweisungsrichtlinie einem dieser Hubs zugewiesen.

   1. Fügen Sie nach Wunsche einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md).

   1. Wählen Sie **Speichern** aus.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

In diesem Abschnitt bereiten Sie Ihren virtuellen Linux Computer oder Ihr physisches Gerät für loT Edge vor. Anschließend installieren Sie loT Edge.

Es gibt zwei Schritte, die Sie auf Ihrem Gerät durchführen müssen, bevor es für die Installation der loT Edge-Runtime bereit ist. Ihr Gerät benötigt Zugang zu den Microsoft-Installationspaketen, und es muss eine Container-Engine installiert werden.

### <a name="access-the-microsoft-installation-packages"></a>Zugriff auf die Microsoft-Installationspakete

Ihr Gerät muss Zugriff auf die Microsoft-Installationspakete haben.

1. Installieren Sie die Repository-Konfiguration, die dem Betriebssystem Ihres Geräts entspricht.

   * **Ubuntu Sever 18.04**:

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
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Für Azure IoT Edge-Softwarepakete gelten die in jedem Paket (im Verzeichnis `usr/share/doc/{package-name}` oder `LICENSE`) enthaltenen Lizenzbedingungen. Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

### <a name="install-a-container-engine"></a>Installieren einer Containerengine

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. Für Produktionsszenarien empfehlen wir die Verwendung der Moby-Engine. Die Moby-Engine ist die einzige Container-Engine, das bei Azure IoT Edge offiziell unterstützt wird. Docker CE/EE-Containerimages sind mit der Moby-Runtime kompatibel.

1. Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

1. Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Wenn beim Installieren der Moby-Containerengine Fehler angezeigt werden, überprüfen Sie Ihren Linux-Kernel auf Moby-Kompatibilität. Einige Hersteller von eingebetteten Geräten stellen Geräteimages mit benutzerdefinierten Linux-Kernels bereit, denen die Funktionen für die Containerengine-Kompatibilität fehlen. Führen Sie den folgenden Befehl aus, der das von Moby bereitgestellte [check-config-Skript](https://github.com/moby/moby/blob/master/contrib/check-config.sh) verwendet, um die Kernelkonfiguration zu überprüfen.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > Überprüfen Sie in der Ausgabe des Skripts, ob alle Elemente unter `Generally Necessary` und `Network Drivers` aktiviert sind. Wenn Funktionen fehlen, aktivieren Sie diese, indem Sie Ihren Kernel aus der Quelle neu erstellen und die zugehörigen Module zum Einbinden in die entsprechende Konfigurationsdatei für den Kernel auswählen. Gleiches gilt, wenn Sie einen Kernelkonfigurationsgenerator wie `defconfig` oder `menuconfig` verwenden: Suchen und aktivieren Sie die entsprechenden Funktionen, und erstellen Sie dann den Kernel dementsprechend neu. Sobald Sie Ihren bearbeiteten Kernel bereitgestellt haben, führen Sie das check-config-Skript erneut aus, um zu überprüfen, ob alle erforderlichen Funktionen erfolgreich aktiviert wurden.

### <a name="install-iot-edge"></a>Installieren von IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Der Daemon für IoT Edge-Sicherheit dient zum Bereitstellen und Einhalten von Sicherheitsstandards auf dem IoT Edge-Gerät. Der Daemon wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version installieren müssen, beispielsweise eine Vorabversion, oder eine Offlineinstallation durchführen müssen, folgen Sie den Schritten der Offlineinstallation oder der Installation einer bestimmten Version.

1. Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

1. Installieren Sie IoT Edge, Version 1.1.*, zusammen mit dem Paket **libiothsm-std**.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge, Version 1.1, ist der Branch für langfristigen Support von IoT Edge. Wenn Sie eine ältere Version laufen lassen, empfehlen wir, den neuesten Patch zu installieren oder darauf zu aktualisieren, da ältere Versionen nicht mehr unterstützt werden.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Der IoT Edge-Dienst stellt Sicherheitsstandards auf dem IoT Edge-Gerät bereit und sorgt für deren Einhaltung. Der Dienst wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Der IoT-Identitätsdienst wurde zusammen mit Version 1.2 von IoT Edge eingeführt. Dieser Dienst übernimmt die Identitätsbereitstellung und -verwaltung für IoT Edge und andere Gerätekomponenten, die mit IoT Hub kommunizieren müssen.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version installieren müssen, beispielsweise eine Vorabversion, oder eine Offlineinstallation durchführen müssen, folgen Sie den Schritten der Offlineinstallation oder der Installation einer bestimmten Version.

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

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ermitteln Sie Ihren DPS-**ID-Bereich** und die **Registrierungs-ID** des Geräts, die zuvor erfasst wurden.

1. Öffnen Sie die Konfigurationsdatei auf Ihrem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zur Bereitstellungskonfiguration der Datei. Heben Sie die Auskommentierung der Zeilen für die TPM-Bereitstellung auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   Der Zeile `provisioning:` sollte kein Leerzeichen vorangestellt und geschachtelte Elemente sollten um zwei Leerzeichen eingerückt sein.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die Werte `scope_id` und `registration_id` mit Ihren DPS- und Geräteinformationen. `scope_id` ist der **ID-Bereich** auf der Übersichtsseite für Ihre DPS-Instanz.

1. Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst eine Bereitstellung mit DPS versuchen und dann bei einem Fehler auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch bereitstellen soll, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein Ereignis der erneuten Bereitstellung erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Speichern und schließen Sie die Datei.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ermitteln Sie Ihren DPS-**ID-Bereich** und die **Registrierungs-ID** des Geräts, die zuvor erfasst wurden.

1. Erstellen Sie eine Konfigurationsdatei für Ihr Gerät basierend auf einer Vorlagendatei, die im Rahmen der IoT Edge-Installation bereitgestellt wird.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen der Datei. Heben Sie die Auskommentierung der Zeilen für die TPM-Bereitstellung auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Aktualisieren Sie die Werte `id_scope` und `registration_id` mit Ihren DPS- und Geräteinformationen. `scope_id` ist der **ID-Bereich** auf der Übersichtsseite für Ihre DPS-Instanz.

1. Suchen Sie optional in der Datei nach dem Abschnitt zum „Modus für die automatische erneute Bereitstellung“. Verwenden Sie den Parameter `auto_reprovisioning_mode`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts für `Dynamic`, `AlwaysOnStartup` oder `OnErrorOnly` zu konfigurieren. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Speichern und schließen Sie die Datei.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Gewähren von IoT Edge-Zugriff für das TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Für die automatische Gerätebereitstellung benötigt die IoT Edge-Runtime Zugriff auf das TPM.

Sie können TPM-Zugriff auf die IoT Edge-Runtime gewähren, indem Sie die Systemeinstellungen überschreiben, sodass der Dienst `iotedge` über Stammrechte verfügt. Wenn Sie die Dienstberechtigungen nicht erhöhen möchten, können Sie auch die folgenden Schritte ausführen, um den TPM-Zugriff manuell bereitzustellen.

1. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf tpm0 und tpmrm0 erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Kopieren Sie die folgenden Informationen, in die Regeldatei. `tpmrm0` ist unter Umständen nicht auf Geräten vorhanden, die eine ältere Kernel-Version als 4.12 verwenden. Geräte ohne tpmrm0 ignorieren diese Regel.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Speichern und beenden Sie die Datei.

1. Lösen Sie das Udev-System aus, um die neue Regel ausgewertet.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vergewissern Sie sich, dass die Regel erfolgreich angewendet wurde.

   ```bash
   ls -l /dev/tpm*
   ```

   Die erfolgreiche Ausgabe sieht wie folgt aus:

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um Udev zu aktualisieren.

1. Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Die IoT Edge-Runtime basiert auf einem TPM-Dienst, der den Zugriff eines Brokers auf das TPM eines Geräts vermittelt. Dieser Dienst muss für die automatische Bereitstellung Ihres Geräts auf das TPM zugreifen.

Sie können Zugriff auf das TPM gewähren, indem Sie die Systemeinstellungen überschreiben, sodass der Dienst `aziottpm` Stammrechte hat. Wenn Sie die Dienstberechtigungen nicht erhöhen möchten, können Sie auch die folgenden Schritte ausführen, um den TPM-Zugriff manuell bereitzustellen.

1. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf tpm0 und tpmrm0 erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Kopieren Sie die folgenden Informationen, in die Regeldatei. `tpmrm0` ist unter Umständen nicht auf Geräten vorhanden, die eine ältere Kernel-Version als 4.12 verwenden. Geräte ohne tpmrm0 ignorieren diese Regel.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Speichern und beenden Sie die Datei.

1. Lösen Sie das Udev-System aus, um die neue Regel ausgewertet.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vergewissern Sie sich, dass die Regel erfolgreich angewendet wurde.

   ```bash
   ls -l /dev/tpm*
   ```

   Die erfolgreiche Ausgabe sieht wie folgt aus:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um Udev zu aktualisieren.

1. Wenden Sie die am Gerät vorgenommenen Konfigurationsänderungen an.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Neustarten von IoT Edge und Überprüfen der erfolgreichen Installation

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Wenn dies noch nicht geschehen ist, starten Sie die IoT Edge-Runtime neu, damit sie alle von Ihnen am Gerät vorgenommenen Konfigurationsänderungen erfasst.

   ```bash
   sudo systemctl restart iotedge
   ```

Überprüfen Sie, ob die IoT Edge-Runtime ausgeführt wird.

   ```bash
   sudo systemctl status iotedge
   ```

Untersuchen Sie die Daemonprotokolle.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Wenn Bereitstellungsfehler angezeigt werden, sind die Konfigurationsänderungen möglicherweise noch nicht wirksam. Starten Sie den IoT Edge-Daemon erneut.

   ```bash
   sudo systemctl daemon-reload
   ```

Oder starten Sie Ihren virtuellen Computer erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Wenn dies noch nicht geschehen ist, wenden Sie die von Ihnen am Gerät vorgenommenen Konfigurationsänderungen an.

   ```bash
   sudo iotedge config apply
   ```

Überprüfen Sie, ob die IoT Edge-Runtime ausgeführt wird.

   ```bash
   sudo iotedge system status
   ```

Untersuchen Sie die Daemonprotokolle.

   ```cmd/sh
   sudo iotedge system logs
   ```

Wenn Bereitstellungsfehler angezeigt werden, sind die Konfigurationsänderungen möglicherweise noch nicht wirksam. Versuchen Sie, den IoT Edge-Daemon neu zu starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Oder starten Sie Ihren virtuellen Computer erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.
:::moniker-end
<!-- end 1.2 -->

Wenn die Runtime erfolgreich gestartet wurde, können Sie zum IoT Hub wechseln und sehen, dass Ihr neues Gerät automatisch bereitgestellt wurde. Es ist jetzt bereit für die Ausführung von IoT Edge-Modulen.

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

## <a name="next-steps"></a>Nächste Schritte

Der DPS-Registrierungsprozess ermöglicht das Festlegen der Geräte-ID und der Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
