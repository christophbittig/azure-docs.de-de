---
title: Erstellen und Bereitstellen von Geräten für IoT Edge für Linux unter Windows mit X.509-Zertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von X.509-Zertifikatnachweisen zum Testen der Gerätebereitstellung im großen Stil für Azure IoT Edge mit dem Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 59c29f3ce91f5ee1e1e5ee65710cc34ce1572907
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845994"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Erstellen und Bereitstellen von Geräten für IoT Edge für Linux unter Windows im großen Stil mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung von einem oder mehreren Geräten für [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) mithilfe von X.509-Zertifikaten. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub-Gerätebereitstellungs-Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Generieren Sie Zertifikate und Schlüssel.
1. Erstellen Sie entweder eine **individuelle Registrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Stellen Sie einen virtuellen Linux-Computer mit installierter IoT Edge-Runtime bereit, und verbinden Sie ihn mit dem IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

## <a name="generate-device-identity-certificates"></a>Generieren von Geräteidentitätszertifikaten

Das Geräteidentitätszertifikat ist ein Blattzertifikat, das über eine Zertifikatvertrauenskette mit dem obersten X.509-Zertifikat der Zertifizierungsstelle (Certificate Authority, CA) verbunden ist. Für das Geräteidentitätszertifikat muss der allgemeine Name (Common Name, CN) auf die Geräte-ID festgelegt sein, die das Gerät in Ihrem IoT-Hub haben soll.

Geräteidentitätszertifikate werden nur für die Bereitstellung des IoT Edge Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Im Gegensatz zu den Zertifizierungsstellenzertifikaten, die das IoT Edge-Gerät Modulen oder Blattgeräten zur Überprüfung präsentiert, signieren sie keine Zertifikate. Weitere Informationen finden Sie unter [Details zur Verwendung von Azure IoT Edge-Zertifikaten](iot-edge-certs.md).

Nachdem Sie das Geräteidentitätszertifikat erstellt haben, sollten Sie zwei Dateien haben: eine CER- oder PEM-Datei, die den öffentlichen Teil des Zertifikats enthält, und eine CER- oder PEM-Datei mit dem privaten Schlüssel des Zertifikats. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette außerdem den öffentlichen Teil eines Zwischenzertifikats oder eines Stammzertifikats der Zertifizierungsstelle.

Zum Einrichten der automatischen Bereitstellung mit X.509 benötigen Sie die folgenden Dateien:

* Das Geräteidentitätszertifikat und das zugehörige private Schlüsselzertifikat. Das Geräteidentitätszertifikat wird auf DPS hochgeladen, wenn Sie eine individuelle Registrierung erstellen. Der private Schlüssel wird an die IoT Edge-Runtime übermittelt.
* Ein vollständiges Kettenzertifikat, das mindestens die Geräteidentität und die Zwischenzertifikate enthalten sollte. Das vollständige Kettenzertifikat wird an die IoT Edge-Runtime übermittelt.
* Ein Zwischenzertifikat oder ein Zertifikat der Stammzertifizierungsstelle aus der Zertifikatvertrauenskette. Dieses Zertifikat wird auf DPS hochgeladen, wenn Sie eine Gruppenregistrierung erstellen.

> [!NOTE]
> Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am oder nach dem 1. Januar 2038 ablaufen.

### <a name="use-test-certificates-optional"></a>Verwenden von Testzertifikaten (optional)

Wenn Sie über keine Zertifizierungsstelle zum Erstellen neuer Identitätszertifikate verfügen und dieses Szenario testen möchten, enthält das Git-Repository für Azure IoT Edge Skripts, mit denen Sie Testzertifikate generieren können. Diese Zertifikate sind nur für Entwicklungstests konzipiert und dürfen in der Produktionsumgebung nicht verwendet werden.

Führen Sie zum Erstellen von Testzertifikaten die Schritte in [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md) aus. Führen Sie die beiden erforderlichen Abschnitte zum Einrichten der Skripts für die Zertifikatgenerierung und Erstellen eines Stammzertifikats der Zertifizierungsstelle aus. Führen Sie danach die Schritte zum Erstellen eines Geräteidentitätszertifikats aus. Wenn Sie sie abgeschlossen haben, sollten Sie über die folgende Zertifikatskette und das folgende Schlüsselpaar verfügen:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Sie benötigen diese beiden Zertifikate auf dem IoT Edge-Gerät. Wenn Sie die individuelle Registrierung in DPS verwenden möchten, laden Sie die Datei „.cert.pem“ hoch. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette zum Hochladen außerdem ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle. Wenn Sie Demozertifikate verwenden, verwenden Sie das Zertifikat für die Gruppenregistrierung, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`.

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen dem Gerätebereitstellungs-Service und IoT Hub verwendet.

Halten Sie die folgenden Informationen bereit:

* Den DPS-Wert **ID-Bereich**. Sie können diesen Wert im Azure-Portal von der Übersichtsseite für Ihre DPS-Instanz abrufen.
* Die Kettendatei für das Geräteidentitätszertifikat auf dem Gerät.
* Die Schlüsseldatei für die Geräteidentität auf dem Gerät.

Sie können Ihr IoT Edge-Gerät mithilfe von PowerShell oder Windows Admin Center bereitstellen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie für PowerShell den folgenden Befehl mit den Platzhalterwerten aus, die mit Ihren eigenen Werten aktualisiert wurden:

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Führen Sie für Windows Admin Center die folgenden Schritte aus:

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **X.509-Zertifikat (DPS)** aus.

1. Geben Sie in den Feldern „Windows Admin Center“ Ihre DPS-Bereichs-ID, die Geräteregistrierungs-ID und den Primärschlüssel oder abgeleiteten Schlüssel für die Registrierung an.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Mit der ausgewählten Methode bereitstellen“ nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit symmetrischen Schlüsseln (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

---

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Gerätebereitstellungs-Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Gerätebereitstellungs-Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Gerätebereitstellungsdienst erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Gerätebereitstellungs-Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Melden Sie sich mithilfe des folgenden Befehls in Ihrer PowerShell-Sitzung bei Ihrem virtuellen IoT Edge für Linux-Computer unter Windows an.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >Das einzige Konto, das zum Herstellen eines SSH mit dem virtuellen Computer zulässig ist, ist der Benutzer, der es erstellt.

1. Sobald Sie angemeldet sind, können Sie die Liste der laufenden IoT Edge-Module mithilfe des folgenden Linux-Befehls überprüfen:

   ```bash
   sudo iotedge list
   ```

1. Wenn Sie eine Problembehandlung für den IoT Edge-Dienst durchführen müssen, verwenden Sie die folgenden Linux-Befehle.

    1. Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Verwenden Sie das Tool `check`, um die Konfiguration und den Verbindungsstatus des Geräts zu überprüfen.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie Ihr IoT Edge-Gerät in der Liste der verbundenen Geräte in Windows Admin Center aus, um eine Verbindung damit herzustellen.

1. Auf der Übersichtsseite des Geräts werden Informationen zum Gerät angezeigt:

   * Im Abschnitt mit der **Liste der IoT Edge-Module** werden die auf dem Gerät ausgeführten Module angezeigt. Wenn der IoT Edge-Dienst zum ersten Mal gestartet wird, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.

   * Im Abschnitt **IoT Edge-Status** wird der Dienststatus angezeigt. Dieser sollte **Aktiv (wird ausgeführt)** lauten.

---

Wenn Sie ein neues IoT Edge-Gerät erstellen, wird es im Azure-Portal mit dem Statuscode `417 -- The device's deployment configuration is not set` angezeigt. Dieser Status ist normal und bedeutet, dass das Gerät bereit ist, eine Modulbereitstellung zu empfangen.

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Gerätebereitstellungs-Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).

Sie können außerdem:

* Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
* Erfahren Sie, wie Sie [Zertifikate auf Ihrem IoT Edge für Linux auf einem virtuellen Windows-Computer verwalten](how-to-manage-device-certificates.md) und Dateien vom Host-Betriebssystem auf Ihren virtuellen Linux-Computer übertragen.
* Erfahren Sie, wie [Sie Ihre IoT Edge-Geräte für die Kommunikation über einen Proxy-Server konfigurieren](how-to-configure-proxy-support.md).
