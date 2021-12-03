---
title: Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mit X.509-Zertifikaten unter Linux – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von X.509-Zertifikaten zum Testen der Gerätebereitstellung im großen Stil für Azure IoT Edge mithilfe des Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 35f8b3482eff16197f9851ab849d9bdd370ad314
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850810"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Das Erstellen und Bereitstellen eines IoT Edge-Geräts im großen Stil mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel enthält End-to-End-Anweisungen für die automatische Bereitstellung eines oder mehrerer IoT Edge Linux-Geräte mit X.509-Zertifikaten. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Generieren Sie Zertifikate und Schlüssel.
1. Erstellen Sie entweder eine **individuelle Registrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Installieren Sie die IoT Edge-Runtime, und registrieren Sie das Gerät bei IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein physisches oder virtuelles Gerät, das das IoT Edge-Gerät sein soll.

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

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen dem Device Provisioning Service und IoT Hub verwendet.

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
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "x509"
   #   registration_id: "OPTIONAL_REGISTRATION_ID_LEAVE_COMMENTED_OUT_TO_REGISTER_WITH_CN_OF_IDENTITY_CERT"
       identity_cert: "REQUIRED_URI_TO_DEVICE_IDENTITY_CERTIFICATE_HERE"
       identity_pk: "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY_HERE"
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
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "REGISTRATION_ID_HERE"

   identity_cert = "DEVICE_IDENTITY_CERTIFICATE_HERE"

   identity_pk = "DEVICE_IDENTITY_PRIVATE_KEY_HERE"
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

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

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

Der Registrierungsprozess des Device Provisioning Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
