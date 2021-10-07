---
title: Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil mit X.509-Zertifikaten unter Windows – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von X.509-Zertifikaten zum Testen der Gerätebereitstellung im großen Stil für Azure IoT Edge mithilfe des Gerätebereitstellungsdiensts
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d0be2b1752460744d9dad8686f69b27a9350ffb3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700292"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil unter Windows mit X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält umfassende Anweisungen für die automatische Bereitstellung eines oder mehrerer IoT Edge Windows-Geräte mit X.509-Zertifikaten. Sie können Azure IoT Edge-Geräte mit dem [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Generieren Sie Zertifikate und Schlüssel.
1. Erstellen Sie entweder eine **individuelle Registrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Installieren Sie die IoT Edge-Runtime, und registrieren Sie das Gerät bei IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub.
* Ein physisches oder virtuelles Windows-Gerät, das das IoT Edge-Gerät sein soll.
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

> [!NOTE]
> Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am oder nach dem 1. Januar 2038 ablaufen.

### <a name="use-test-certificates-optional"></a>Verwenden von Testzertifikaten (optional)

Wenn Sie über keine Zertifizierungsstelle zum Erstellen neuer Identitätszertifikate verfügen und dieses Szenario testen möchten, enthält das Git-Repository für Azure IoT Edge Skripts, mit denen Sie Testzertifikate generieren können. Diese Zertifikate sind nur für Entwicklungstests konzipiert und dürfen in der Produktionsumgebung nicht verwendet werden.

Führen Sie zum Erstellen von Testzertifikaten die Schritte in [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md) aus. Führen Sie die beiden erforderlichen Abschnitte zum Einrichten der Skripts für die Zertifikatgenerierung und Erstellen eines Stammzertifikats der Zertifizierungsstelle aus. Führen Sie danach die Schritte zum Erstellen eines Geräteidentitätszertifikats aus. Wenn Sie sie abgeschlossen haben, sollten Sie über die folgende Zertifikatskette und das folgende Schlüsselpaar verfügen:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Sie benötigen diese beiden Zertifikate auf dem IoT Edge-Gerät. Wenn Sie die individuelle Registrierung in DPS verwenden möchten, laden Sie die Datei „.cert.pem“ hoch. Wenn Sie die Gruppenregistrierung in DPS verwenden möchten, benötigen Sie in derselben Zertifikatvertrauenskette zum Hochladen außerdem ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle. Wenn Sie Demozertifikate verwenden, verwenden Sie das Zertifikat für die Gruppenregistrierung, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Verwenden Sie Ihre generierten Zertifikate und Schlüssel, um in DPS eine Registrierung für ein oder mehrere IoT Edge-Geräte zu erstellen.

Wenn Sie ein einzelnes IoT Edge-Gerät bereitstellen möchten, müssen Sie eine **individuelle Registrierung** erstellen. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer **DPS-Gruppenregistrierung** aus.

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

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

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

Bei Gruppenregistrierungen wird ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle aus der Zertifikatvertrauenskette verwendet, das zum Generieren der einzelnen Geräteidentitätszertifikate verwendet wurde.

#### <a name="verify-your-root-certificate"></a>Überprüfen Ihres Stammzertifikats

Wenn Sie eine Registrierungsgruppe erstellen, haben Sie die Möglichkeit, ein überprüftes Zertifikat zu verwenden. Sie können ein Zertifikat mit DPS überprüfen, indem Sie den Besitz des Stammzertifikats nachweisen. Weitere Informationen finden Sie unter [Nachweis des Besitzes für X.509-Zertifizierungsstellenzertifikate](../iot-dps/how-to-verify-certificates.md).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Wählen Sie im Menü auf der linken Seite **Zertifikate** aus.

1. Wählen Sie **Hinzufügen** aus, um ein neues Zertifikat hinzuzufügen.

1. Geben Sie einen Anzeigenamen für Ihr Zertifikat ein, und navigieren Sie zu der CER- oder PEM-Datei, die den öffentlichen Teil Ihres X.509-Zertifikats darstellt.

   Wenn Sie die Demozertifikate verwenden, laden Sie das Zertifikat `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` hoch.

1. Wählen Sie **Speichern** aus.

1. Ihr Zertifikat sollte nun auf der Seite **Zertifikate** aufgeführt werden. Wählen Sie es aus, um die Zertifikatdetails zu öffnen.

1. Wählen Sie **Prüfcode generieren**  aus, und kopieren Sie den generierten Code.

1. Unabhängig davon, ob Sie Ihr eigenes Zertifizierungsstellenzertifikat verwendet haben oder die Demozertifikate verwenden, können Sie das Überprüfungstool aus dem IoT Edge-Repository verwenden, um den Nachweis des Besitzes zu überprüfen. Das Überprüfungstool verwendet Ihr Zertifizierungsstellenzertifikat zum Signieren eines neuen Zertifikats mit dem angegebenen Prüfcode als Antragstellername.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
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

Sie können mit dem nächsten Abschnitt fortfahren.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

In diesem Abschnitt bereiten Sie Ihren virtuellen Windows-Computer oder Ihr physisches Gerät für IoT Edge vor. Anschließend installieren Sie IoT Edge.

Es gibt einen Schritt, den Sie auf Ihrem Gerät ausführen müssen, bevor es für die Installation der IoT Edge-Runtime bereit ist. Auf Ihrem Gerät muss eine Container-Engine installiert werden.

### <a name="install-a-container-engine"></a>Installieren einer Containerengine

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime wie [Moby](https://github.com/moby/moby), einer Moby-basierten Engine, die im Installationsskript enthalten ist. Es sind keine zusätzlichen Schritte erforderlich, um die-Engine zu installieren.

### <a name="install-iot-edge"></a>Installieren von IoT Edge

Der Daemon für IoT Edge-Sicherheit dient zum Bereitstellen und Einhalten von Sicherheitsstandards auf dem IoT Edge-Gerät. Der Daemon wird bei jedem Start gestartet und führt durch Starten der restlichen IoT Edge-Runtime einen Bootstrap für das Gerät aus.

Die in diesem Abschnitt beschriebenen Schritte stellen den typischen Prozess zum Installieren der neuesten Version auf einem Gerät dar, das über eine Internetverbindung verfügt. Wenn Sie eine bestimmte Version installieren müssen, beispielsweise eine Vorabversion, oder eine Offlineinstallation durchführen müssen, folgen Sie den Schritten der Offlineinstallation oder der Installation einer bestimmten Version.

1. Führen Sie PowerShell als Administrator aus.

   Verwenden Sie eine AMD64-Sitzung von PowerShell, nicht PowerShell(x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Führen Sie den Befehl [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) aus, der die folgenden Aufgaben ausführt:

   * Überprüft, ob Ihr Windows-Computer mit einer unterstützten Version läuft.
   * Aktiviert das Containerfeature.
   * Lädt die Moby-Engine und die IoT Edge-Runtime herunter.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Starten Sie das Gerät neu, wenn Sie dazu aufgefordert werden.

Wenn Sie IoT Edge auf einem Gerät installieren, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Legen Sie im Installationsprogramm ein lokales Verzeichnis für die Offlineinstallation fest.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge mit Windows-Containern](reference-windows-scripts.md).

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

Halten Sie die folgenden Informationen bereit:

* Den DPS-Wert **ID-Bereich**. Sie können diesen Wert im Azure-Portal von der Übersichtsseite für Ihre DPS-Instanz abrufen.
* Die Kettendatei für das Geräteidentitätszertifikat auf dem Gerät.
* Die Schlüsseldatei für die Geräteidentität auf dem Gerät.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Bei der Installation von IoT Edge müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Der Befehl verwendet standardmäßig die manuelle Bereitstellung mit Windows-Containern. Verwenden Sie deshalb das Flag `-DpsX509` für die automatische Bereitstellung per Authentifizierung mit X.509-Zertifikat.

   Ersetzen Sie die Platzhalterwerte für `{scope_id}`, `{identity cert chain path}` und `{identity key path}` durch die entsprechenden Werte aus Ihrer DPS-Instanz und die Dateipfade auf Ihrem Gerät.

   Fügen Sie den `-RegistrationId {registration_id}`-Parameter hinzu, wenn Sie die Geräte-ID als etwas anderes als den CN-Namen des Identitätszertifikats festlegen möchten.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >In der Konfigurationsdatei werden Ihre Zertifikat- und Schlüsselinformationen als Datei-URIs gespeichert. Weil aber der Befehl „Initialize-IoTEdge“ diesen Formatierungsschritt automatisch verarbeitet, können Sie den absoluten Pfad zu den Zertifikats- und Schlüsseldateien auf Ihrem Gerät bereitstellen.

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Gerätebereitstellungsdienst erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

---

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

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

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
