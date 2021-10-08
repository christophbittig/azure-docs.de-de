---
title: Erstellen und Bereitstellen von Geräten für IoT Edge für Linux unter Windows mit X.509-Zertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von X.509-Zertifikatnachweisen zum Testen der Gerätebereitstellung im großen Stil für Azure IoT Edge mit dem Device Provisioning Service
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700167"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Erstellen und Bereitstellen von Geräten für IoT Edge für Linux unter Windows im großen Stil mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung von einem oder mehreren Geräten für [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) mithilfe von X.509-Zertifikaten. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Generieren Sie Zertifikate und Schlüssel.
1. Erstellen Sie entweder eine **individuelle Registrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Stellen Sie einen virtuellen Linux-Computer mit installierter IoT Edge-Runtime bereit, und verbinden Sie ihn mit dem IoT Hub.

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub.
* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.
* Ein Windows-Gerät mit den folgenden Mindestsystemanforderungen:
  * Windows 10 ab Version 1809, Build 17763 oder höher
  * Professional, Enterprise oder Server Edition
  * Mindestens erforderlicher freier Arbeitsspeicher: 1 GB
  * Mindestens erforderlicher freier Speicherplatz: 10 GB
  * Virtualisierungsunterstützung
    * Aktivieren Sie unter Windows 10 Hyper-V. Weitere Informationen finden Sie unter [Installieren von Hyper-V unter Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
    * Installieren Sie unter Windows Server die Hyper-V-Rolle, und erstellen Sie einen standardmäßigen Netzwerkswitch. Weitere Informationen finden Sie unter [Geschachtelte Virtualisierung für Azure IoT Edge für Linux unter Windows](nested-virtualization.md).
    * Konfigurieren Sie auf einer VM die geschachtelte Virtualisierung. Weitere Informationen finden Sie unter [Geschachtelte Virtualisierung](nested-virtualization.md).
  * Netzwerkunterstützung
    * Windows Server umfasst keinen Standardswitch. Bevor Sie EFLOW auf einem Windows Server-Gerät bereitstellen können, müssen Sie einen virtuellen Switch erstellen.  Weitere Informationen finden Sie unter [Erstellen eines virtuellen Switches](how-to-create-virtual-switch.md).
    * Windows Desktop-Versionen umfassen einen Standardswitch, der für die EFLOW-Installation verwendet werden kann. Bei Bedarf können Sie einen benutzerdefinierten virtuellen Switch erstellen.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.
* Wenn Sie **GPU-beschleunigte Linux-Module** in Ihrer Azure IoT Edge für Linux unter Windows-Bereitstellung verwenden möchten, gibt es mehrere Konfigurationsoptionen zu berücksichtigen. Abhängig von Ihrer GPU-Architektur müssen Sie die richtigen Treiber installieren, und Sie benötigen möglicherweise Zugriff auf einen Windows-Insider-Build. Informationen zum Ermitteln Ihrer Konfigurationsanforderungen und zum Erfüllen dieser Voraussetzungen finden Sie unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md).

Sie können **PowerShell** oder **Windows Admin Center** verwenden, um Ihre IoT Edge-Geräte zu installieren und zu verwalten. Für jedes Tool gelten eigene Voraussetzungen:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie PowerShell verwenden möchten, führen Sie die folgenden Schritte aus, um Azure IoT Edge für Linux unter Windows herunterzuladen und zu installieren:

1. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten die folgenden Befehle nacheinander aus, um IoT Edge für Linux unter Windows herunterzuladen.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Sie können benutzerdefinierte Installations- und VHDX-Verzeichnisse angeben, indem Sie dem Installationsbefehl die Parameter `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` und `VHDXDIR="<FULLY_QUALIFIED_PATH>"` hinzufügen.

1. Legen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` fest. Sie können die aktuelle Ausführungsrichtlinie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten wie folgt überprüfen:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` ist, können Sie die Ausführungsrichtlinie wie folgt festlegen:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Weitere Informationen zum PowerShell-Modul „Azure IoT Edge für Linux unter Windows“ finden Sie in der [PowerShell-Funktionsreferenz](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Wenn Sie Windows Admin Center verwenden möchten, stellen Sie sicher, dass Sie Zugriff auf Windows Admin Center haben und die Azure IoT Edge-Erweiterung installiert ist:

   1. Laden Sie das [Windows Admin Center-Installationsprogramm](https://aka.ms/wacdownload) herunter, und führen Sie es aus. Befolgen Sie die Anweisungen im Installations-Assistenten, um Windows Admin Center zu installieren.

   1. Verwenden Sie nach der Installation einen unterstützten Browser, um das Windows Admin Center zu öffnen. Zu den unterstützten Browsern gehören Microsoft Edge (Windows 10, Version 1709 oder höher), Google Chrome und Microsoft Edge Insider.

   1. Bei der ersten Verwendung von Windows Admin Center werden Sie aufgefordert, ein Zertifikat auszuwählen. Wählen Sie als Zertifikat **Windows Admin Center-Client** aus.

   1. Installieren Sie die Azure IoT Edge-Erweiterung. Wählen Sie das Zahnradsymbol in der rechten oberen Ecke des Windows Admin Center-Dashboards aus.

      ![Auswählen des Zahnradsymbols in der rechten oberen Ecke des Dashboards zum Öffnen der Einstellungen (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. Wählen Sie im Menü **Einstellungen** unter **Gateway** die Option **Erweiterungen** aus.

   1. Suchen Sie auf der Registerkarte **Verfügbare Erweiterungen** in der Liste der Erweiterungen nach **Azure IoT Edge**. Markieren Sie diese Option, und wählen Sie über der Liste der Erweiterungen die Aufforderung **Installieren** aus.

   1. Nach Abschluss der Installation sollte Azure IoT Edge in der Liste der installierten Erweiterungen auf der Registerkarte **Installierte Erweiterungen** angezeigt werden.

---

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

Wenn Sie ein einzelnes IoT Edge-Gerät bereitstellen möchten, erstellen Sie eine **individuelle Registrierung**. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer DPS-**Gruppenregistrierung** aus.

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

Nachdem jetzt eine Registrierung für diese Geräte vorhanden ist, kann die IoT Edge-Runtime die Geräte während der Installation automatisch bereitstellen.

---

Sie können mit dem nächsten Abschnitt fortfahren.

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>Erstellen einer neuen Bereitstellung von IoT Edge für Linux unter Windows

Stellen Sie Azure IoT Edge für Linux unter Windows auf Ihrem Zielgerät bereit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie IoT Edge für Linux unter Windows auf dem Zielgerät, sofern dies noch nicht erfolgt ist.

> [!NOTE]
> Das folgende PowerShell-Verfahren beschreibt, wie Sie IoT Edge für Linux unter Windows auf dem lokalen Gerät bereitstellen. Für die Bereitstellung auf einem Remotezielgerät mithilfe von PowerShell können Sie über [Remote PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) eine Verbindung mit einem Remotegerät herstellen und diese Befehle remote auf dem Gerät ausführen.

1. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten die folgenden Befehle nacheinander aus, um IoT Edge für Linux unter Windows herunterzuladen.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Sie können benutzerdefinierte Installations- und VHDX-Verzeichnisse für IoT Edge für Linux unter Windows angeben, indem Sie dem Installationsbefehl die Parameter `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` und `VHDXDIR="<FULLY_QUALIFIED_PATH>"` hinzufügen.

1. Legen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` fest, wenn dies noch nicht so eingestellt ist. In den PowerShell-Voraussetzungen finden Sie die Befehle, mit denen Sie die aktuelle Ausführungsrichtlinie überprüfen und auf `AllSigned` festlegen können.

1. Erstellen Sie die Bereitstellung von IoT Edge für Linux unter Windows.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >Standardmäßig erstellt der Befehl `Deploy-Eflow` Ihren Linux-VM mit 1 GB RAM, 16 vCPU-Kern und 16 GB Speicherplatz. Die Ressourcen, die Ihr VM benötigt, sind jedoch stark von den Workloads abhängig, die Sie bereitstellen. Wenn Ihr VM nicht über genügend Arbeitsspeicher verfügt, um Ihre Workloads zu unterstützen, kann er nicht gestartet werden.
   >
   >Sie können die verfügbaren Ressourcen des VM mithilfe der optionalen Parameter des Befehls `Deploy-Eflow` anpassen.
   >
   >Mit dem folgenden Befehl wird beispielsweise ein VM mit vier vCPU-Kernen, 4 GB RAM und 20 GB Speicherplatz erstellt:
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md) aufgeführt werden.

   Zur Verwendung von GPU-Passthrough müssen Sie Ihrem Befehl `Deploy-Eflow` die Parameter **gpuName**, **gpuPassthroughType** und **gpuCount** hinzufügen. Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Geben Sie „Y“ ein, um den Lizenzbedingungen zuzustimmen.

1. Durch die Eingabe von „O“ oder „R“ können Sie **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Sobald die Bereitstellung abgeschlossen ist, meldet das PowerShell-Fenster **Bereitstellung erfolgreich**.

   ![Bei einer erfolgreichen Bereitstellung wird am Ende der Meldungen „Bereitstellung erfolgreich“ angezeigt (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Die Azure IoT Edge-Erweiterung für Windows Admin Center befindet sich zurzeit in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Installations- und Verwaltungsprozesse können sich von denen bei allgemeiner Verfügbarkeit unterscheiden.

Auf der Startseite von Windows Admin Center wird in der Liste der Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.

Sie können mit Windows Admin Center Azure IoT Edge für Linux unter Windows auf Ihrem lokalen Gerät und auf Remotegeräten installieren und verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows.

Wenn Sie die Bereitstellung nicht auf dem lokalen Gerät, sondern auf einem Remotezielgerät ausführen möchten, und das gewünschte Zielgerät nicht in der Liste angezeigt wird, befolgen Sie die Anweisungen zum [Hinzufügen Ihres Zielgeräts](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Anfängliches Windows Admin Center-Dashboard mit aufgeführtem Zielgerät (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. Wählen Sie **Hinzufügen**.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

   Wenn Sie bereits über eine Instanz von IoT Edge für Linux unter Windows verfügen, die auf einem Gerät ausgeführt wird, können Sie **Hinzufügen** auswählen, um eine Verbindung mit diesem IoT Edge-Gerät herzustellen und es mit Windows Admin Center zu verwalten.

   ![Auswählen von „Neu erstellen“ auf der Kachel „Azure IoT Edge“ in Windows Admin Center (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. Der Bereich **Create an Azure IoT Edge for Linux on Windows deployment** (Azure IoT Edge für Linux unter Windows-Bereitstellung erstellen) wird geöffnet. Überprüfen Sie auf der Registerkarte **Erste Schritte** die Mindestanforderungen, und wählen Sie **Weiter** aus.

1. Lesen Sie die Lizenzbedingungen, aktivieren Sie **Ich stimme zu**, und wählen Sie **Weiter** aus.

1. Sie können **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Klicken Sie auf **Weiter: Bereitstellen**.

   ![Auswählen der Schaltfläche „Weiter: Bereitstellen“ nach dem Umschalten von „Optionale Diagnosedaten“ (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. Klicken Sie auf der Registerkarte **2. Bereitstellen** unter **Zielgerät auswählen** auf das aufgeführte Gerät, um zu überprüfen, ob es die Mindestanforderungen erfüllt. Wenn als Status „Unterstützt“ angezeigt wird, wählen Sie **Weiter** aus.

   ![Auswählen des Geräts zur Überprüfung der Unterstützung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. Überprüfen Sie auf der Registerkarte **2.2 Einstellungen** die Konfigurationseinstellungen der Bereitstellung.

   >[!NOTE]
   >IoT Edge Für Linux unter Windows verwendet einen Standardswitch, über den der Linux-VM eine interne IP-Adresse zugewiesen wird. Diese interne IP-Adresse ist von außerhalb des virtuellen Windows-Computers nicht erreichbar. Sie können lokal eine Verbindung mit der VM herstellen, während Sie am Windows-Computer angemeldet sind.
   >
   >Wenn Sie Windows Server verwenden, richten Sie einen Standardswitch ein, bevor Sie IoT Edge für Linux unter Windows bereitstellen.

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md) aufgeführt werden. Wenn Sie diese erforderlichen Komponenten erst zu diesem Zeitpunkt im Bereitstellungsprozess installieren, müssen Sie von vorn beginnen.

   Abhängig vom GPU-Adapter, den Sie Ihrer Bereitstellung zuweisen, stehen zwei Optionen für das GPU-Passthrough zur Verfügung: **Diskrete Gerätezuweisung (Discrete Device Assignment, DDA)** und **GPU-Paravirtualisierung (GPU-PV)** . Im Folgenden finden Sie Beispiele für jede Methode.

   Für die diskrete Gerätezuweisung wählen Sie die Anzahl von GPU-Kernen aus, die Ihrer Linux-VM zugeordnet werden sollen.

   ![Konfigurationseinstellungen mit aktivierter GPU für die direkte Gerätezuweisung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   Für die Paravirtualisierungsmethode sind keine zusätzlichen Einstellungen erforderlich.

   ![Konfigurationseinstellungen mit aktivierter GPU für die Paravirtualisierung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Weiter** aus.

1. Auf der Registerkarte **2.3 Bereitstellung** können Sie den Fortschritt der Bereitstellung überwachen. Der gesamte Prozess umfasst das Herunterladen und Installieren des Pakets von Azure IoT Edge für Linux unter Windows, das Konfigurieren des Hostgeräts und das Einrichten der Linux-VM. Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung werden für jeden Schritt ein grünes Häkchen und die Bezeichnung „Erledigt“ angezeigt (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen. Klicken Sie auf **Weiter: Verbinden**, um zur Registerkarte **3. Verbinden** zu wechseln, auf der die Bereitstellung des Azure IoT Edge-Geräts durchgeführt wird.

---

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

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

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

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

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).

Sie können außerdem:

* Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
* Erfahren Sie, wie Sie [Zertifikate auf Ihrem IoT Edge für Linux auf einem virtuellen Windows-Computer verwalten](how-to-manage-device-certificates.md) und Dateien vom Host-Betriebssystem auf Ihren virtuellen Linux-Computer übertragen.
* Erfahren Sie, wie [Sie Ihre IoT Edge-Geräte für die Kommunikation über einen Proxy-Server konfigurieren](how-to-configure-proxy-support.md).
