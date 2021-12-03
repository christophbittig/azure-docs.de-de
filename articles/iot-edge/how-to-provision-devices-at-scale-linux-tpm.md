---
title: Erstellen und Bereitstellen von Geräten mit einem virtuellen TPM unter Linux – Azure IoT Edge
description: Verwenden eines simulierten TPMs auf einem Linux-Gerät, um den Azure IoT Hub-Dienst für Azure IoT Edge zu testen
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b353f683a64444a0ad89f062d0b826484260681f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852045"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM unter Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel enthält Anleitungen für die automatische Bereitstellung eines Azure IoT Edge für Linux-Geräts mithilfe eines TPM (Trusted Platform Module). Sie können loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (Gerätebereitstellungsdienst) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

- Automatische Bereitstellung eines Linux-Geräts mit physischer TPM-Hardware. Ein Beispiel ist der [Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e).
- Automatische Bereitstellung eines virtuellen Linux-Computers (Virtual Machine, VM) mit einem simulierten TPM, das auf einem Windows-Entwicklungscomputer mit aktiviertem Hyper-V ausgeführt wird. Wir empfehlen diese Methodik nur als Testszenario. Ein simuliertes TPM bietet nicht die gleiche Sicherheit wie ein physisches TPM.

Die Anleitungen unterscheiden sich je nach Ihrer Methodik. Sorgen Sie deshalb künftig dafür, dass die richtige Registerkarte angezeigt wird.

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

Aufgaben:

1. Rufen Sie Bereitstellungsinformationen für Ihr TPM ab.
1. Erstellen Sie eine individuelle Registrierung für Ihr Gerät in einer Instanz des IoT Hub Device Provisioning Service.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT-Hub.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

Aufgaben:

1. Erstellen Sie in Hyper-V eine Linux-VM mit einem simulierten TPM für die Hardwaresicherheit.
1. Rufen Sie Bereitstellungsinformationen für Ihr TPM ab.
1. Erstellen Sie eine individuelle Registrierung für Ihr Gerät in einer Instanz des IoT Hub Device Provisioning Service.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT-Hub.

---

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

Ein physisches Gerät, das das IoT Edge-Gerät sein soll.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

Ein Windows-Entwicklungscomputer mit [aktiviertem Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In diesem Artikel wird das Betriebssystem Windows 10 verwendet, auf dem ein Ubuntu Server-VM ausgeführt wird.

---

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn Sie den TPM-Nachweis beim Device Provisioning Service verwenden.
>
> Wenn Sie ein TPM verwenden, können Sie nur individuelle Registrierungen des Device Provisioning Service erstellen, aber keine Gruppenregistrierungen.

## <a name="set-up-your-device"></a>Einrichten des Geräts

# <a name="physical-device"></a>[Physisches Gerät](#tab/physical-device)

Wenn Sie ein physisches Linux-Gerät mit einem TPM verwenden, gibt es keine zusätzlichen Schritte zum Einrichten Ihres Geräts.

Sie können den Vorgang fortsetzen.

# <a name="virtual-machine"></a>[Virtueller Computer](#tab/virtual-machine)

In diesem Abschnitt erstellen Sie eine neue Linux-VM unter Hyper-V. Diese VM wird mit einem simulierten TPM konfiguriert, um die Funktionsweise der automatischen Bereitstellung mit IoT Edge zu testen.

> [!TIP]
> Wenn Sie eine VM verwenden, führen Sie Kopieren und Einfügen für diese VM mehrmals in diesem Artikel aus. Das Kopieren und Einfügen ist über die Hyper-V-Manager-Verbindungsanwendung nicht einfach. Es empfiehlt sich gegebenenfalls, einmalig über den Hyper-V-Manager eine Verbindung mit der VM herzustellen, um dessen IP-Adresse abzurufen. Führen Sie `sudo apt install net-tools` und dann `hostname -I` aus. Anschließend können Sie die IP-Adresse verwenden, um eine Verbindung per SSH herzustellen: `ssh <username>@<ipaddress>`.

### <a name="create-a-virtual-switch"></a>Erstellen eines virtuellen Switches

Mit einem virtuellen Switch können Sie Ihre VM mit einem physischen Netzwerk verbinden.

1. Öffnen Sie den Hyper-V-Manager auf Ihrem Windows-Computer.

1. Klicken Sie im Menü **Aktionen** auf **Manager für virtuelle Switches**.

1. Wählen Sie einen **externen** virtuellen Switch, und klicken Sie anschließend auf **Virtuellen Switch erstellen**.

1. Geben Sie Ihrem neuen virtuellen Switch einen Namen. Verwenden Sie zum Beispiel **EdgeSwitch**. Stellen Sie sicher, dass der Verbindungstyp auf **Externes Netzwerk**, festgelegt ist, und klicken Sie anschließend auf **OK**.

1. Ein Popup warnt Sie, dass die Netzwerkkonnektivität möglicherweise unterbrochen wird. Wählen Sie **Yes** (Ja), um fortzufahren.

> [!TIP]
> Wenn beim Erstellen des neuen virtuellen Switches Fehler angezeigt werden, sorgen Sie dafür, dass keine anderen Switches den Ethernet-Adapter und keine anderen Switches denselben Namen verwenden.

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Erstellen Sie einen neuen virtuellen Computer aus einer startbaren Imagedatei.

1. Laden Sie eine für Ihre VM zu verwendende Festplatten-Imagedatei herunter, und speichern Sie sie lokal. Beispiel: [Ubuntu-Server 18.04](http://releases.ubuntu.com/18.04/). Informationen zu unterstützten Betriebssystemen für IoT Edge-Geräte finden Sie unter [Von Azure IoT Edge unterstützte Systeme](./support.md).

1. Wählen Sie im Hyper-V-Manager im Menü **Aktionen** > **Neu** > **Virtueller Computer** im Menü **Aktionen** aus.

1. Geben Sie im **Assistenten für neue virtuelle Computer** die folgenden speziellen Konfigurationen ein:

   1. **Generation angeben**: Wählen Sie **Generation 2** aus. Für VMs der zweiten Generation ist die geschachtelte Virtualisierung aktiviert, die zum Ausführen von IoT Edge auf einer VM erforderlich ist.
   1. **Konfigurieren der Netzwerkeinstellungen**: Legen Sie den Wert der **Verbindung** auf den virtuellen Switch fest, den Sie im vorherigen Abschnitt erstellt haben.
   1. **Installationsoptionen**: Wählen Sie **Betriebssystem von einer startbaren Imagedatei installieren** aus, und navigieren Sie zu der Datenträger-Imagedatei, die Sie lokal gespeichert haben.

1. Wählen Sie im Assistenten die Option **Fertig stellen**, um die VM zu erstellen.

Die Erstellung der neuen VM kann einige Minuten dauern.

### <a name="enable-virtual-tpm"></a>Virtuelles TPM aktivieren

Öffnen Sie nach der Erstellung Ihres virtuellen Computers dessen Einstellungen zum Aktivieren des virtuellen TPMs, mit dem Sie das Gerät automatisch bereitstellen lassen können.

1. Klicken Sie im Hyper-V-Manager mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Einstellungen** aus.

1. Navigieren zur Seite **Sicherheit**.

1. Deaktivieren Sie **Sicheren Start aktivieren**.

1. Aktivieren Sie **Trusted Platform Module aktivieren**.

1. Klicken Sie auf **OK**.

### <a name="start-the-virtual-machine"></a>Starten des virtuellen Computers

Starten Sie Ihre VM, um die Installation abzuschließen.

1. Starten Sie im Hyper-V-Manager Ihren virtuellen Computer, und stellen Sie eine Verbindung damit her.

1. Befolgen Sie die Anweisungen auf der VM, um die Installation abzuschließen und den Computer zu starten.

Sobald die Installation abgeschlossen ist und Sie bei Ihrem virtuellen Computer wieder angemeldet sind, können Sie den Vorgang fortsetzen.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>Abrufen von Bereitstellungsinformationen für Ihr TPM

In diesem Abschnitt erstellen Sie ein Tool, mit dem Sie die Registrierungs-ID und den Endorsement Key für Ihr TPM abrufen können.

1. Melden Sie sich bei Ihrem Gerät an und führen Sie dann die Schritte unter [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um das Azure IoT-Geräte-SDK für C zu installieren und zu erstellen.

1. Führen Sie die folgenden Befehle zum Erstellen des SDK-Tools aus, das Ihre Gerätebereitstellungsinformationen für Ihr TPM abruft.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** des Geräts angezeigt. Kopieren Sie diese Werte zur späteren Verwendung, wenn Sie eine individuelle Registrierung für Ihr Gerät im Device Provisioning Service erstellen.

> [!TIP]
> Wenn Sie nicht das SDK-Tool zum Abrufen der Bereitstellungsinformationen verwenden möchten, müssen Sie eine andere Möglichkeit zum Abrufen dieser Informationen finden. Der Endorsement Key, der bei jedem TPM-Chip eindeutig ist, wird von dem ihm zugeordneten TPM-Chiphersteller abgerufen. Sie können eine eindeutige Registrierungs-ID für Ihr TPM-Gerät ableiten. So können Sie beispielsweise einen SHA-256-Hash des Endorsement Key erstellen.

Nachdem Sie über Ihre Registrierungs-ID und Ihren Endorsement Key verfügen, können Sie den Vorgang fortsetzen.

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen dem Device Provisioning Service und IoT Hub verwendet.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ermitteln Sie Ihren **ID-Bereich** des Device Provisioning Service und die **Registrierungs-ID** des Geräts, die in zuvor erfasst wurden.

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
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "tpm"
       registration_id: "REGISTRATION_ID_HERE"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die Werte `scope_id` und `registration_id` mit Ihrem Gerätebereitstellungsdienst und den Geräteinformationen. Der `scope_id` Wert ist der **ID-Bereich** auf der Übersichtsseite Ihrer Gerätebereitstellungsdienst-Instanz.

1. Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst versuchen, mit Gerätebereitstellungsdienst bereitzustellen, und bei einem Fehler auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch bereitstellen soll, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein Ereignis der erneuten Bereitstellung erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Speichern und schließen Sie die Datei.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ermitteln Sie Ihren **ID-Bereich** des Device Provisioning Service und die **Registrierungs-ID** des Geräts, die in zuvor erfasst wurden.

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
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "REGISTRATION_ID_HERE"
   ```

1. Aktualisieren Sie die Werte `id_scope` und `registration_id` mit Ihrem Gerätebereitstellungsdienst und den Geräteinformationen. Der `scope_id` Wert ist der **ID-Bereich** auf der Übersichtsseite Ihrer Gerätebereitstellungsdienst-Instanz.

1. Suchen Sie optional in der Datei nach dem Abschnitt zum „Modus für die automatische erneute Bereitstellung“. Verwenden Sie den Parameter `auto_reprovisioning_mode`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts für `Dynamic`, `AlwaysOnStartup` oder `OnErrorOnly` zu konfigurieren. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Speichern und schließen Sie die Datei.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Gewähren von IoT Edge-Zugriff für das TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Für die automatische Gerätebereitstellung benötigt die IoT Edge-Runtime Zugriff auf das TPM.

Sie können TPM-Zugriff auf die IoT Edge-Runtime gewähren, indem Sie die Systemeinstellungen überschreiben, sodass der Dienst `iotedge` über Stammrechte verfügt. Wenn Sie die Dienstberechtigungen nicht erhöhen möchten, können Sie auch die folgenden Schritte ausführen, um den TPM-Zugriff manuell bereitzustellen.

1. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf `tpm0` und `tpmrm0` erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Kopieren Sie die folgenden Informationen, in die Regeldatei. `tpmrm0` ist unter Umständen nicht auf Geräten vorhanden, die eine ältere Kernel-Version als 4.12 verwenden. Geräte ohne `tpmrm0` können diese Regel ignorieren.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Speichern und beenden Sie die Datei.

1. Lösen Sie das `udev`-System aus, um die neue Regel auszuwerten.

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

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um `udev` zu aktualisieren.

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

1. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf `tpm0` und `tpmrm0` erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Kopieren Sie die folgenden Informationen, in die Regeldatei. `tpmrm0` ist unter Umständen nicht auf Geräten vorhanden, die eine ältere Kernel-Version als 4.12 verwenden. Geräte ohne `tpmrm0` können diese Regel ignorieren.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Speichern und beenden Sie die Datei.

1. Lösen Sie das `udev`-System aus, um die neue Regel auszuwerten.

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

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um `udev` zu aktualisieren.

1. Wenden Sie die am Gerät vorgenommenen Konfigurationsänderungen an.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

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

Oder starten Sie Ihre VM erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.
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

Oder starten Sie Ihre VM erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.
:::moniker-end
<!-- end 1.2 -->

Wenn die Runtime erfolgreich gestartet wurde, können Sie zum IoT Hub wechseln und sehen, dass Ihr neues Gerät automatisch bereitgestellt wurde. Es ist jetzt bereit für die Ausführung von IoT Edge-Modulen.

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning-Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen.

Weitere Informationen finden Sie unter „[Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Microsoft Azure-Portals](how-to-deploy-at-scale.md) oder [mithilfe der Azure CLI](how-to-deploy-cli-at-scale.md)“.
