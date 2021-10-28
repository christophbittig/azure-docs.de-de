---
title: Erstellen und Bereitstellen von loT Edge-Geräten mit symmetrischen Schlüsseln unter Linux – Azure loT Edge | Microsoft-Dokumentation
description: Verwenden Sie die Bescheinigung mit symmetrischen Schlüsseln, um die Bereitstellung von Linux-Geräten in großem Umfang für Azure loT Edge mit dem Device Provisioning Service zu testen
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b19990be57332df6de4ccbb45ae92c74b30041b3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226051"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Erstellen und Bereitstellen von loT Edge-Geräten in großem Umfang unter Linux mit symmetrischem Schlüssel

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel stellt eine End-to-End-Anleitung für die automatische Bereitstellung eines oder mehrerer Linux loT Edge-Geräte mit symmetrischen Schlüsseln bereit. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Erstellen Sie entweder eine **Einzelregistrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Verwenden Sie für eine vereinfachte Benutzeroberfläche das [Azure IoT Edge-Konfigurationstool](https://github.com/azure/iot-edge-config). Dieses Befehlszeilentool, das sich zurzeit in der öffentlichen Vorschau befindet, installiert IoT Edge auf Ihrem Gerät und stellt es mithilfe von DPS und dem Nachweis des symmetrischen Schlüssels zur Verfügung.
:::moniker-end

Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts mit einer Device Provisioning Service-Instanz. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben. Die Gerätebestätigung bzw. der Nachweis mithilfe eines [TPM](../iot-dps/concepts-tpm-attestation.md) (Trusted Platform Module) oder von [X.509-Zertifikaten](../iot-dps/concepts-x509-attestation.md) ist sicherer und sollte verwendet werden, wenn striktere Sicherheitsanforderungen gelten.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub.
* Ein physisches oder virtuelles Gerät, das das IoT Edge-Gerät sein soll.
  * Sie müssen eine *eindeutige* **Registrierungs-ID** definieren, um jedes Gerät zu identifizieren. Sie können die MAC-Adresse, Seriennummer oder eindeutige Informationen vom Gerät verwenden. So könnten Sie beispielsweise mithilfe einer Kombination aus MAC-Adresse und Seriennummer diese Zeichenfolge für eine Registrierungs-ID bilden: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Gültige Zeichen sind alphanumerische Kleinbuchstaben und Bindestriche (`-`).
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Erstellen Sie eine Registrierung, um ein oder mehrere Geräte über DPS bereitzustellen.

Wenn Sie ein einzelnes IoT Edge-Gerät bereitstellen möchten, erstellen Sie eine **individuelle Registrierung**. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer DPS-**Gruppenregistrierung** aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit, einen **Anfangszustand des Gerätezwillings** zu deklarieren. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

Weitere Informationen zu Registrierungen im Device Provisioning-Dienst finden Sie unter [Verwalten von Geräteregistrierungen](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Erstellen einer individuellen DPS-Registrierung

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Wählen Sie unter **Mechanismus** die Option **Symmetrischer Schlüssel** aus.

   1. Geben Sie eine eindeutige **Registrierungs-ID** für Ihr Gerät an.

   1. Optional können Sie eine **IoT Hub-Geräte-ID** für Ihr Gerät angeben. Sie können mithilfe von Geräte-IDs ein einzelnes Gerät als Ziel für die Modulbereitstellung festlegen. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   1. Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt.

   1. Fügen Sie optional einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Beispiel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Wählen Sie **Speichern** aus.

1. Kopieren Sie den Wert **Primärschlüssel** der individuellen Registrierung, der beim Installieren der IoT Edge-Runtime verwendet werden soll.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Erstellen einer DPS-Gruppenregistrierung

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können Gruppenregistrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für IoT Edge-Geräte gilt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Geben Sie einen Wert für **Gruppenname** an.

   1. Wählen Sie als Nachweistyp **Symmetrischer Schlüssel** aus.

   1. Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

   1. Fügen Sie optional einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Beispiel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Wählen Sie **Speichern** aus.

1. Kopieren Sie den Wert für **Primärschlüssel** Ihrer Registrierungsgruppe, der beim Erstellen von Geräteschlüsseln für eine Gruppenregistrierung verwendet werden soll.

Da es jetzt eine Registrierungsgruppe gibt, kann die IoT Edge-Runtime Geräte während der Installation automatisch bereitstellen.

#### <a name="derive-a-device-key"></a>Ableiten eines Geräteschlüssels

Jedes Gerät, das im Rahmen einer Gruppenregistrierung bereitgestellt wird, benötigt einen abgeleiteten Geräteschlüssel, um während der Bereitstellung einen Nachweis des symmetrischen Schlüssels bei der Registrierung durchzuführen.

Verwenden Sie zum Generieren eines Geräteschlüssels den aus Ihrer DPS-Registrierungsgruppe kopierten Schlüssel, um einen [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Wert für die eindeutige Registrierungs-ID des Geräts zu berechnen und das Ergebnis in das Base64-Format zu konvertieren.

> [!IMPORTANT]
> Nehmen Sie den Primär- oder Sekundärschlüssel Ihrer Registrierung nicht in den Gerätecode auf.

Unter Linux können Sie Ihren abgeleiteten Geräteschlüssel mithilfe von „openssl“ generieren, wie im folgenden Beispiel gezeigt wird.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch die Registrierungs-ID Ihres Geräts.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

Hier ist eine Beispielausgabe eines abgeleiteten Geräteschlüssels:

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

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

Die Schritte in diesem Abschnitt stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät mit einer Internetkonnektivität. Wenn Sie eine bestimmte Version installieren müssen, z.B. eine Vorabversion, oder wenn Sie offline installieren müssen, folgen Sie den Schritten für die Installation von [Offline oder einer bestimmten Version](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional).

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

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version installieren müssen, z.B. eine Vorabversion, oder wenn Sie offline installieren müssen, folgen Sie den Schritten für die Installation von [Offline oder einer bestimmten Version](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional).

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

Halten Sie die folgenden Informationen bereit:

* Den Wert für den DPS-**ID-Bereich**
* Die von Ihnen erstellte **Registrierungs-ID** für das Gerät
* Entweder der **Primärschlüssel** aus einer individuellen Registrierung oder ein [abgeleiteter Schlüssel](#derive-a-device-key) für Geräte, die eine Gruppenregistrierung verwenden.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen für die Datei. Heben Sie die Auskommentierung der Zeilen für die Bereitstellung des symmetrischen DPS-Schlüssels auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   Der Zeile `provisioning:` sollte kein Leerzeichen vorangestellt und geschachtelte Elemente sollten um zwei Leerzeichen eingerückt sein.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die Werte `scope_id`, `registration_id`und `symmetric_key` mit Ihren DPS- und Geräteinformationen.

1. Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst versuchen, mit DPS bereitzustellen, und bei einem Fehler auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch erneut bereitstellt, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein erneutes Bereitstellungsereignis erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Erstellen Sie eine Konfigurationsdatei für Ihr Gerät basierend auf einer Vorlagendatei, die im Rahmen der IoT Edge-Installation bereitgestellt wird.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Suchen Sie den Abschnitt **Provisioning** (Bereitstellung) der Datei. Heben Sie die Auskommentierung der Zeilen für die DPS-Bereitstellung mit symmetrischem Schlüssel auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Aktualisieren Sie die Werte `id_scope`, `registration_id`und `symmetric_key` mit Ihren DPS- und Geräteinformationen.

   Der symmetrische Schlüsselparameter kann einen Wert eines Inline-Schlüssels, eines Datei-URIs oder eines PKCS#11-URIs akzeptieren. Heben Sie die Auskommentierung nur einer symmetrischen Schlüsselzeile auf, basierend auf dem von Ihnen verwendeten Format.

   Wenn Sie PKCS#11-URIs verwenden, suchen Sie in der Konfigurationsdatei den Abschnitt **PKCS#11**, und stellen Sie Informationen zu Ihrer Konfiguration von PKCS#11 bereit.

1. Speichern und schließen Sie die Datei „config.toml“.

1. Wenden Sie die an IoT Edge vorgenommenen Konfigurationsänderungen an.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

---

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Überprüfen Sie den Status des IoT Edge-Diensts.

```cmd/sh
systemctl status iotedge
```

Untersuchen Sie die Dienstprotokolle.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Überprüfen Sie den Status des IoT Edge-Diensts.

```cmd/sh
sudo iotedge system status
```

Untersuchen Sie die Dienstprotokolle.

```cmd/sh
sudo iotedge system logs
```

Führen Sie ausgeführte Module auf.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
