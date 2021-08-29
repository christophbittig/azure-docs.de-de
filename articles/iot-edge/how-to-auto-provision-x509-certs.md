---
title: Automatische Bereitstellung von Geräten mit DPS mithilfe von X.509-Zertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von X.509-Zertifikaten zum Testen der automatischen Gerätebereitstellung für Azure IoT Edge mithilfe des Device Provisioning-Diensts
author: kgremban
ms.author: kgremban
ms.date: 06/18/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4095207fbc4fbbf4cb3f6463e3091717fcd3921a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346782"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Mit dem [Azure IoT Hub Device Provisioning-Dienstanbieter (DPS)](../iot-dps/index.yml) können Sie IoT Edge-Geräte mithilfe von X.509-Zertifikaten automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel erfahren Sie, wie Sie eine Registrierung beim Device Provisioning-Dienst auf einem IoT Edge-Gerät mithilfe von X.509-Zertifikaten mit den folgenden Schritten erstellen:

* Generieren Sie Zertifikate und Schlüssel.
* Erstellen Sie entweder eine individuelle Registrierung für ein Gerät oder eine Gruppenregistrierung für eine Gruppe von Geräten.
* Installieren Sie die IoT Edge-Runtime, und registrieren Sie das Gerät bei IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub.
* Ein physisches oder virtuelles Gerät, das das IoT Edge-Gerät sein soll.
* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning-Diensts verfügen, führen Sie die Anleitungen unter [Einrichten des IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md) aus.
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

### <a name="use-test-certificates-optional"></a>Verwenden von Testzertifikaten (optional)

Wenn Sie über keine Zertifizierungsstelle zum Erstellen neuer Identitätszertifikate verfügen und dieses Szenario testen möchten, enthält das Git-Repository für Azure IoT Edge Skripts, mit denen Sie Testzertifikate generieren können. Diese Zertifikate sind nur für Entwicklungstests konzipiert und dürfen in der Produktionsumgebung nicht verwendet werden.

Führen Sie zum Erstellen von Testzertifikaten die Schritte in [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md) aus. Führen Sie die beiden erforderlichen Abschnitte zum Einrichten der Skripts für die Zertifikatgenerierung und Erstellen eines Stammzertifikats der Zertifizierungsstelle aus. Führen Sie danach die Schritte zum Erstellen eines Geräteidentitätszertifikats aus. Wenn Sie sie abgeschlossen haben, sollten Sie über die folgende Zertifikatskette und das folgende Schlüsselpaar verfügen:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Sie benötigen diese beiden Zertifikate auf dem IoT Edge-Gerät. Wenn Sie die individuelle Registrierung in DPS verwenden möchten, laden Sie die Datei „.cert.pem“ hoch. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette zum Hochladen außerdem ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle. Wenn Sie Demozertifikate verwenden, verwenden Sie das Zertifikat für die Gruppenregistrierung, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`.

## <a name="create-a-dps-individual-enrollment"></a>Erstellen einer individuellen DPS-Registrierung

Verwenden Sie Ihre generierten Zertifikate und Schlüssel, um in DPS eine individuelle Registrierung für ein einzelnes IoT Edge-Gerät zu erstellen. Individuelle Registrierungen verwenden den öffentlichen Teil des Identitätszertifikats eines Geräts und gleichen es mit dem Zertifikat auf dem Gerät ab.

Wenn Sie mehrere IoT Edge Geräte bereitstellen möchten, führen Sie die Schritte im nächsten Abschnitt, [Erstellen einer DPS-Gruppenregistrierung](#create-a-dps-group-enrollment), aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

Weitere Informationen zu Registrierungen im Device Provisioning-Dienst finden Sie unter [Verwalten von Geräteregistrierungen](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > In der Azure CLI können Sie eine [Registrierung](/cli/azure/iot/dps/enrollment) oder eine [Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group) erstellen und mithilfe des Flags **edge-enabled** angeben, dass ein Gerät oder eine Gruppe von Geräten ein IoT Edge-Gerät ist.

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

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen. Setzen Sie den Vorgang mit dem Abschnitt [Installieren der IoT Edge-Runtime](#install-the-iot-edge-runtime) fort, um Ihr IoT Edge-Gerät einzurichten.

## <a name="create-a-dps-group-enrollment"></a>Erstellen einer DPS-Gruppenregistrierung

Verwenden Sie Ihre generierten Zertifikate und Schlüssel, um in DPS eine Gruppenregistrierung für mehrere IoT Edge-Geräte zu erstellen. Bei Gruppenregistrierungen wird ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle aus der Zertifikatvertrauenskette verwendet, das zum Generieren der einzelnen Geräteidentitätszertifikate verwendet wurde.

Wenn Sie stattdessen ein einzelnes IoT Edge-Gerät bereitstellen möchten, führen Sie die Schritte im vorherigen Abschnitt, [Erstellen einer individuellen DPS-Registrierung](#create-a-dps-individual-enrollment), aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

### <a name="verify-your-root-certificate"></a>Überprüfen Ihres Stammzertifikats

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

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Laden Sie im Azure-Portal auf derselben Seite „Zertifikatdetails“ das neu generierte Verifizierungszertifikat hoch.

1. Wählen Sie **Überprüfen** aus.

### <a name="create-enrollment-group"></a>Erstellen einer Registrierungsgruppe

Weitere Informationen zu Registrierungen im Device Provisioning-Dienst finden Sie unter [Verwalten von Geräteregistrierungen](../iot-dps/how-to-manage-enrollments.md).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Registrierungsgruppe hinzufügen**, und führen Sie dann die folgenden Schritte zum Konfigurieren der Registrierung aus:

   * **Gruppenname**: Geben Sie einen einprägsamen Namen für diese Gruppenregistrierung an.

   * **Nachweistyp**: Wählen Sie **Certificate** aus.

   * **IoT Edge-Gerät**: Wählen Sie **True** aus. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

   * **Zertifikattyp**: Wählen Sie **Zertifizierungsstellenzertifikat** aus, wenn Sie über ein mit DPS gespeichertes überprüftes Zertifizierungsstellenzertifikat verfügen, oder **Zwischenzertifikat**, wenn Sie eine neue Datei nur für diese Registrierung hochladen möchten.

   * **Primäres Zertifikat**: Wenn Sie im letzten Abschnitt „Zertifizierungsstellenzertifikat“ ausgewählt haben, wählen Sie in der Dropdownliste Ihr Zertifikat aus. Wenn Sie ein Zwischenzertifikat ausgewählt haben, laden Sie die öffentliche Datei aus einem Zertifizierungsstellenzertifikat in der Zertifikatvertrauenskette hoch, die zum Generieren der Geräteidentitätszertifikate verwendet wurde.

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

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen. Setzen Sie den Vorgang mit dem nächsten Abschnitt fort, um Ihr IoT Edge-Gerät einzurichten.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Die Komponenten werden in Containern ausgeführt, und Sie können weitere Container auf dem Gerät bereitstellen, um Code im Edge-Bereich auszuführen.

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

Führen Sie die entsprechenden Schritte aus, um Azure IoT Edge basierend auf Ihrem Betriebssystem zu installieren:

* [Installieren von IoT Edge für Linux](how-to-install-iot-edge.md)
* [Installieren von IoT Edge für Linux auf Windows-Geräten](how-to-install-iot-edge-on-windows.md)
  * Dieses Szenario ist die empfohlene Methode zum Ausführen von IoT Edge auf Windows-Geräten.
* [Installieren von IoT Edge mit Windows-Containern](how-to-install-iot-edge-windows-on-windows.md)

Sobald IoT Edge auf Ihrem Gerät installiert wurde, kehren Sie zu diesem Artikel zurück, um das Gerät zu bereitstellen.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Führen Sie die Schritte in [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) aus, und kehren Sie dann zu diesem Artikel zurück, um das Gerät bereitzustellen.

:::moniker-end
<!-- end 1.2 -->

Die X.509-Bereitstellung mit DPS wird in IoT Edge erst ab Version 1.0.9 unterstützt.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

Halten Sie die folgenden Informationen bereit:

* Den DPS-Wert **ID-Bereich**. Sie können diesen Wert im Azure-Portal von der Übersichtsseite für Ihre DPS-Instanz abrufen.
* Die Kettendatei für das Geräteidentitätszertifikat auf dem Gerät.
* Die Schlüsseldatei für die Geräteidentität auf dem Gerät.

# <a name="linux"></a>[Linux](#tab/linux)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

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

# <a name="linux-on-windows"></a>[Linux unter Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Sie können Ihr IoT Edge-Gerät mithilfe von PowerShell oder Windows Admin Center bereitstellen.

### <a name="powershell"></a>PowerShell

Führen Sie für PowerShell den folgenden Befehl mit den Platzhalterwerten aus, die mit Ihren eigenen Werten aktualisiert wurden:

```powershell
Provision-EflowVm -provisioningType DPSX509 -scopeId <ID_SCOPE_HERE> -identityCertPath <ABSOLUTE_CERT_DEST_PATH_ON_WINDOWS_HOST> -identityPrivKeyPath <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_WINDOWS_HOST>
```

### <a name="windows-admin-center"></a>Windows Admin Center

Führen Sie für Windows Admin Center die folgenden Schritte aus:

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **X.509-Zertifikat (DPS)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Kopieren Sie auf der Registerkarte **Übersicht** den Wert **ID-Bereich**. Fügen Sie ihn in Windows Admin Center in das Feld mit der Bereichs-ID ein.

1. Geben Sie die Registrierungs-ID Ihres Geräts in das Feld „Registrierungs-ID“ in Windows Admin Center ein.

1. Laden Sie die Dateien mit Ihrem Zertifikat und dem privaten Schlüssel hoch.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit X.509-Zertifikaten](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, unter IoT Edge für Linux für Windows nicht unterstützt.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Bei der Installation von IoT Edge müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Der Befehl verwendet standardmäßig die manuelle Bereitstellung mit Windows-Containern. Verwenden Sie deshalb das Flag `-DpsX509` für die automatische Bereitstellung per Authentifizierung mit X.509-Zertifikat.

   Ersetzen Sie die Platzhalterwerte für `{scope_id}`, `{identity cert chain path}` und `{identity key path}` durch die entsprechenden Werte aus Ihrer DPS-Instanz und die Dateipfade auf Ihrem Gerät.

   Fügen Sie die `-RegistrationId {registration_id}` hinzu, wenn Sie die Geräte-ID als etwas anderes als den CN-Namen des Identitätszertifikats festlegen möchten.

   Wenn Sie Linux-Container unter Windows verwenden, fügen Sie den Parameter `-ContainerOs Linux` hinzu.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >In der Konfigurationsdatei werden Ihre Zertifikat- und Schlüsselinformationen als Datei-URIs gespeichert. Weil aber der Befehl „Initialize-IoTEdge“ diesen Formatierungsschritt automatisch verarbeitet, können Sie den absoluten Pfad zu den Zertifikats- und Schlüsseldateien auf Ihrem Gerät bereitstellen.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, unter Windows nicht unterstützt.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Linux unter Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Stellen Sie auf dem virtuellen Windows-Computer eine Verbindung mit IoT Edge für Linux her.

```powershell
Connect-EflowVM
```

Überprüfen Sie den Status des IoT Edge-Diensts.

```cmd/sh
sudo systemctl status iotedge
```

Untersuchen Sie die Dienstprotokolle.

```cmd/sh
sudo journalctl -u iotedge --no-pager --no-full
```

Führen Sie ausgeführte Module auf.

```cmd/sh
sudo iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, unter IoT Edge für Linux für Windows nicht unterstützt.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

Überprüfen Sie den Status des IoT Edge-Diensts.

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf.

```powershell
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, unter Windows nicht unterstützt.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
