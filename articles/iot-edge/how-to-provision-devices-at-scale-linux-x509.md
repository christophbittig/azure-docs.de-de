---
title: Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mit X.509-Zertifikaten unter Linux – Azure IoT Edge | Microsoft-Dokumentation
description: Das Verwenden von X.509-Zertifikaten zum Testen der Gerätebereitstellung im großen Stil für Azure IoT Edge mithilfe des Gerätebereitstellungsdiensts
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2dc45c3b088dc25a39496280b1e612a27c9dd69d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700396"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Das Erstellen und Bereitstellen eines IoT Edge-Geräts im großen Stil mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel enthält End-to-End-Anweisungen für die automatische Bereitstellung eines oder mehrerer IoT Edge Linux-Geräte mit X.509-Zertifikaten. Sie können Azure IoT Edge-Geräte mit dem [Azure IoT Hub Device Provisioning Service (Microsoft System Center Data Protection Manager)](../iot-dps/index.yml) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Generieren Sie Zertifikate und Schlüssel.
1. Erstellen Sie entweder eine **individuelle Registrierung** für ein Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Installieren Sie die IoT Edge-Runtime, und registrieren Sie das Gerät bei IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub.
* Ein physisches oder virtuelles Linux-Gerät, dass das IoT Edge-Gerät sein soll.
* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Gerätebereitstellungsdienst-Instanz verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs mit Ihrem Gerätebereitstellungsdienst](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) des IoT-Hub Device Provisioning Service-Schnellstarts befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="generate-device-identity-certificates"></a>Generieren von Geräteidentitätszertifikaten

Das Geräteidentitätszertifikat ist ein Blattzertifikat, das über eine Zertifikatvertrauenskette mit dem obersten X.509-Zertifikat der Zertifizierungsstelle (Certificate Authority, CA) verbunden ist. Für das Geräteidentitätszertifikat muss der allgemeine Name (Common Name, CN) auf die Geräte-ID festgelegt sein, die das Gerät in Ihrem IoT-Hub haben soll.

Geräteidentitätszertifikate werden nur für die Bereitstellung des IoT Edge Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Im Gegensatz zu den Zertifizierungsstellenzertifikaten, die das IoT Edge-Gerät Modulen oder Blattgeräten zur Überprüfung präsentiert, signieren sie keine Zertifikate. Weitere Informationen finden Sie unter [Details zur Verwendung von Azure IoT Edge-Zertifikaten](iot-edge-certs.md).

Nachdem Sie das Geräteidentitätszertifikat erstellt haben, sollten Sie zwei Dateien haben: eine CER- oder PEM-Datei, die den öffentlichen Teil des Zertifikats enthält, und eine CER- oder PEM-Datei mit dem privaten Schlüssel des Zertifikats. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette außerdem den öffentlichen Teil eines Zwischenzertifikats oder eines Stammzertifikats der Zertifizierungsstelle.

Zum Einrichten der automatischen Bereitstellung mit X.509 benötigen Sie die folgenden Dateien:

* Das Geräteidentitätszertifikat und das zugehörige private Schlüsselzertifikat. Das Geräteidentitätszertifikat wird auf DPS hochgeladen, wenn Sie eine individuelle Registrierung erstellen. Der private Schlüssel wird an die IoT Edge-Runtime übermittelt.
* Ein vollständiges Kettenzertifikat, das mindestens die Geräteidentität und die Zwischenzertifikate enthalten sollte. Das vollständige Kettenzertifikat wird an die IoT Edge-Runtime übermittelt.
* Ein Zwischenzertifikat oder ein Zertifikat der Stammzertifizierungsstelle aus der Zertifikatvertrauenskette. Dieses Zertifikat wird auf DPS hochgeladen, wenn Sie eine Gruppenregistrierung erstellen.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am oder nach dem 1. Januar 2038 ablaufen.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>Verwenden von Testzertifikaten (optional)

Wenn Sie über keine Zertifizierungsstelle zum Erstellen neuer Identitätszertifikate verfügen und dieses Szenario testen möchten, enthält das Git-Repository für Azure IoT Edge Skripts, mit denen Sie Testzertifikate generieren können. Diese Zertifikate sind nur für Entwicklungstests konzipiert und dürfen in der Produktionsumgebung nicht verwendet werden.

Führen Sie zum Erstellen von Testzertifikaten die Schritte in [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md) aus. Führen Sie die beiden erforderlichen Abschnitte zum Einrichten der Skripts für die Zertifikatgenerierung und Erstellen eines Stammzertifikats der Zertifizierungsstelle aus. Führen Sie danach die Schritte zum Erstellen eines Geräteidentitätszertifikats aus. Wenn Sie sie abgeschlossen haben, sollten Sie über die folgende Zertifikatskette und das folgende Schlüsselpaar verfügen:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Sie benötigen diese beiden Zertifikate auf dem IoT Edge-Gerät. Wenn Sie die individuelle Registrierung in DPS verwenden möchten, laden Sie die Datei „.cert.pem“ hoch. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette zum Hochladen außerdem ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle. Wenn Sie Demozertifikate verwenden, verwenden Sie das Zertifikat für die Gruppenregistrierung, `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Verwenden Sie Ihre generierten Zertifikate und Schlüssel, um in Microsoft System Center Data Protection Manager eine Gruppenregistrierung für ein oder mehrere IoT Edge-Geräte zu erstellen.

Wenn Sie ein einzelnes IoT Edge Gerät bereitstellen möchten, müssen Sie eine **individuelle Registrierung** erstellen. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer **DPS-Gruppenregistrierung** aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

Weitere Informationen zu Registrierungen im Device Provisioning-Dienst finden Sie unter [Verwalten von Geräteregistrierungen](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Erstellen einer individuellen DPS-Registrierung

Individuelle Registrierungen verwenden den öffentlichen Teil des Identitätszertifikats eines Geräts und gleichen es mit dem Zertifikat auf dem Gerät ab.

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   * **Mechanismus**: Wählen Sie **X.509** aus.

   * **Primäres Zertifikat (PEM- oder CER-Datei)** : Laden Sie die öffentliche Datei aus dem Geräteidentitätszertifikat hoch. Wenn Sie die Skripts zum Generieren eines Testzertifikats verwendet haben, wählen Sie die folgende Datei aus:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub-Geräte-ID**: Stellen Sie nach Wunsch eine ID für Ihr Gerät bereit. Sie können mithilfe von Geräte-IDs ein einzelnes Gerät als Ziel für die Modulbereitstellung festlegen. Wenn Sie keine Geräte-ID angeben, wird der allgemeine Name (Common Name, CN) im X.509-Zertifikat verwendet.

   * **IoT Edge-Gerät**: Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt.

   * **Wählen Sie die IoT-Hubs aus, denen dieses Gerät zugewiesen werden kann**: Wählen Sie den verknüpften IoT-Hub, mit dem Sie Ihr Gerät verbinden möchten. Sie können mehrere Hubs auswählen, und das Gerät wird dann je nach gewählter Zuteilungsrichtlinie einem dieser Hubs zugewiesen.

   * **Anfangszustand des Gerätezwillings**: Fügen Sie einen Tagwert hinzu, der dem Gerätezwilling bei Bedarf hinzugefügt werden soll. Mithilfe von Tags können Sie Gruppen von Geräten als Ziel für die automatische Bereitstellung festlegen. Beispiel:

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

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Erstellen einer DPS-Gruppenregistrierung

Bei Gruppenregistrierungen wird ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle aus der Zertifikatvertrauenskette verwendet, das zum Überprüfen der einzelnen Geräteidentitätszertifikate verwendet wurde.

#### <a name="verify-your-root-certificate"></a>Überprüfen Ihres Stammzertifikats

Wenn Sie eine Registrierungsgruppe erstellen, haben Sie die Möglichkeit, ein überprüftes Zertifikat zu verwenden. Sie können ein Zertifikat mit DPS überprüfen, indem Sie den Besitz des Stammzertifikats nachweisen. Weitere Informationen finden Sie unter [Nachweis des Besitzes für X.509-Zertifizierungsstellenzertifikate](../iot-dps/how-to-verify-certificates.md).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Wählen Sie im Menü auf der linken Seite **Zertifikate** aus.

1. Wählen Sie **Hinzufügen** aus, um ein neues Zertifikat hinzuzufügen.

1. Geben Sie einen Anzeigenamen für Ihr Zertifikat ein, und navigieren Sie zu der CER- oder PEM-Datei, die den öffentlichen Teil Ihres X.509-Zertifikats darstellt.

   Wenn Sie die Demozertifikate verwenden, laden Sie das Zertifikat `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` hoch.

1. Wählen Sie **Speichern** aus.

1. Ihr Zertifikat sollte nun auf der Seite **Zertifikate** aufgeführt werden. Wählen Sie es aus, um die Zertifikatdetails zu öffnen.

1. Wählen Sie **Prüfcode generieren**  aus, und kopieren Sie den generierten Code.

1. Unabhängig davon, ob Sie Ihr eigenes Zertifizierungsstellenzertifikat verwendet haben oder die Demozertifikate verwenden, können Sie das Überprüfungstool aus dem IoT Edge-Repository verwenden, um den Nachweis des Besitzes zu überprüfen. Das Überprüfungstool verwendet Ihr Zertifizierungsstellenzertifikat zum Signieren eines neuen Zertifikats mit dem angegebenen Prüfcode als Antragstellername.

   ```bash
   ./certGen.sh create_verification_certificate <verification code>
   ```

1. Laden Sie im Azure-Portal auf derselben Seite „Zertifikatdetails“ das neu generierte Verifizierungszertifikat hoch.

1. Wählen Sie **Überprüfen** aus.

#### <a name="create-enrollment-group"></a>Erstellen einer Registrierungsgruppe

Weitere Informationen zu Registrierungen im Device Provisioning-Dienst finden Sie unter [Verwalten von Geräteregistrierungen](../iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können Gruppenregistrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für IoT Edge-Geräte gilt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Registrierungsgruppe hinzufügen**, und führen Sie dann die folgenden Schritte zum Konfigurieren der Registrierung aus:

   * **Gruppenname**: Geben Sie einen einprägsamen Namen für diese Gruppenregistrierung an.

   * **Nachweistyp**: Wählen Sie **Certificate** aus.

   * **IoT Edge-Gerät**: Wählen Sie **True** aus. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

   * **Zertifikattyp**: Wählen Sie **Zertifizierungsstellenzertifikat** aus.

   * **Primäres Zertifikat**: Wählen Sie Ihr Zertifikat aus der Dropdownliste aus.

   * **Wählen Sie die IoT-Hubs aus, denen dieses Gerät zugewiesen werden kann**: Wählen Sie den verknüpften IoT-Hub, mit dem Sie Ihr Gerät verbinden möchten. Sie können mehrere Hubs auswählen, und das Gerät wird dann je nach gewählter Zuteilungsrichtlinie einem dieser Hubs zugewiesen.

   * **Anfangszustand des Gerätezwillings**: Fügen Sie einen Tagwert hinzu, der dem Gerätezwilling bei Bedarf hinzugefügt werden soll. Mithilfe von Tags können Sie Gruppen von Geräten als Ziel für die automatische Bereitstellung festlegen. Beispiel:

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

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

---

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

In diesem Abschnitt bereiten Sie Ihren virtuellen Linux-Computer oder Ihr physisches Gerät für IoT Edge vor. Installieren Sie anschließend IoT Edge.

Es gibt zwei Schritte, die Sie auf Ihrem Gerät ausführen müssen, bevor es bereit ist, die IoT Edge-Runtime zu installieren. Ihr Gerät benötigt Zugriff auf die Microsoft-Installationspakete und es muss ein Container-Modul installiert sein.

### <a name="access-the-microsoft-installation-packages"></a>Zugriff auf die Microsoft-Installationspakete

Ihr Gerät muss Zugriff auf die Microsoft-Installationspakete haben.

1. Installieren Sie die zum Betriebssystem Ihres Geräts die passende Repositorykonfiguration.

   * **Ubuntu-Sever 18.04**:

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

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. Für Produktionsszenarien empfehlen wir die Verwendung des Moby-Moduls. Die Moby-Engine ist die einzige Container-Engine, das bei Azure IoT Edge offiziell unterstützt wird. Docker CE/EE-Containerimages sind mit der Moby-Runtime kompatibel.

1. Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

1. Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Wenn beim Installieren der Moby-Containerengine Fehler angezeigt werden, überprüfen Sie Ihren Linux-Kernel auf Moby-Kompatibilität. Einige Hersteller von eingebetteten Geräten stellen Geräteimages mit benutzerdefinierten Linux-Kernels bereit, denen die Funktionen für die Containerengine-Kompatibilität fehlen. Führen Sie den folgenden Befehl aus, der das von Moby bereitgestellte [check-config-Skript](https://github.com/moby/moby/blob/master/contrib/check-config.sh) verwendet, um Ihre Kernelkonfiguration zu überprüfen.
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

1. Installieren Sie IoT Edge-Version 1.1*, zusammen mit dem Paket **libiothsm-std**.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge-Version 1.1 ist der langfristige Supportzweig von IoT Edge. Wenn Sie eine ältere Version ausführen, empfehlen wir, den neuesten Patch zu installieren oder darauf zu aktualisieren, da die älteren Versionen nicht mehr unterstützt werden.

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

Halten Sie die folgenden Informationen bereit:

* Den DPS-Wert **ID-Bereich**. Sie können diesen Wert im Azure-Portal von der Übersichtsseite für Ihre DPS-Instanz abrufen.
* Die Kettendatei für das Geräteidentitätszertifikat auf dem Gerät.
* Die Schlüsseldatei für die Geräteidentität auf dem Gerät.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen für die Datei. Heben Sie die Auskommentierung der Zeilen für die DPS-Bereitstellung mit X.509-Zertifikat auf, und stellen Sie sicher, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   Der Zeile `provisioning:` sollte kein Leerzeichen vorangestellt und geschachtelte Elemente sollten um zwei Leerzeichen eingerückt sein.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die Werte `scope_id`, `identity_cert`und `identity_pk` mit Ihren DPS- und Geräteinformationen.

   Wenn Sie das X.509-Zertifikat und die Schlüsselinformationen zur Datei „config.yaml“ hinzufügen, sollten die Pfade als Datei-URIs angegeben werden. Beispiel:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Geben Sie optional die `registration_id` für das Gerät an. Diese muss mit dem allgemeinen Namen (Common Name, CN) des Identitätszertifikats übereinstimmen. Wenn Sie diese Zeile auskommentiert lassen, wird der CN automatisch angewendet.

1. Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst versuchen, mit DPS bereitzustellen, und bei einem Fehler auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch erneut bereitstellt, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein erneutes Bereitstellungsereignis erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Speichern und schließen Sie die Datei „config.yaml“.

1. Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Suchen Sie den Abschnitt **Provisioning** (Bereitstellung) der Datei. Heben Sie die Auskommentierung der Zeilen für die DPS-Bereitstellung mit X.509-Zertifikat auf, und stellen Sie sicher, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Aktualisieren Sie den Wert von `id_scope` mit der Bereichs-ID, die Sie zuvor aus Ihrer DPS-Instanz kopiert haben.

1. Geben Sie eine `registration_id` für das Gerät an. Dies ist die ID, die das Gerät in IoT Hub aufweist. Die Registrierungs-ID muss mit dem allgemeinen Namen (Common Name, CN) des Identitätszertifikats übereinstimmen.

1. Aktualisieren Sie die Werte von `identity_cert` und `identity_pk` mit Ihren Zertifikat- und Schlüsselinformationen.

   Der Wert des Identitätszertifikats kann als Datei-URI bereitgestellt oder mithilfe von EST oder einer lokalen Zertifizierungsstelle dynamisch ausgestellt werden. Heben Sie die Auskommentierung von nur einer Zeile auf, basierend auf dem Format, das Sie verwenden möchten.

   Der Wert des privaten Identitätsschlüssels kann als Datei-URI oder PKCS#11-URI angegeben werden. Heben Sie die Auskommentierung von nur einer Zeile auf, basierend auf dem Format, das Sie verwenden möchten.

   Wenn Sie PKCS#11-URIs verwenden, suchen Sie in der Konfigurationsdatei den Abschnitt **PKCS#11**, und stellen Sie Informationen zu Ihrer PKCS#11-Konfiguration bereit.

1. Speichern und schließen Sie die Datei.

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

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Gerätebereitstellungsdienst erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

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
