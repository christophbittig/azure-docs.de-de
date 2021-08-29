---
title: Bereitstellen eines Geräts mithilfe des Nachweises des symmetrischen Schlüssels – Azure IoT Edge
description: Verwenden des Nachweises symmetrischer Schlüssel zum Testen der automatischen Gerätebereitstellung für Azure IoT Edge mithilfe des Device Provisioning-Diensts
author: kgremban
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/21/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d4aa7f1a02d8ab789810f06b38c95e9cfd76d5fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355613"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts mithilfe des Nachweises symmetrischer Schlüssel

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge-Geräte können genau wie nicht Edge-fähige Geräte mit dem [Device Provisioning-Dienst](../iot-dps/index.yml) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel erfahren Sie, wie Sie auf einem IoT Edge-Gerät mithilfe des Nachweises symmetrischer Schlüssel eine individuelle Registrierung oder Gruppenregistrierung beim Device Provisioning-Dienst mit den folgenden Schritten erstellen können:

* Erstellen Sie eine neue Instanz für den IoT Hub Device Provisioning-Dienst (DPS).
* Erstellen Sie eine Individuelle Registrierung oder eine Gruppenregistrierung.
* Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Verwenden Sie für eine vereinfachte Benutzeroberfläche das [Azure IoT Edge-Konfigurationstool](https://github.com/azure/iot-edge-config). Dieses Befehlszeilentool, das sich zurzeit in der öffentlichen Vorschau befindet, installiert IoT Edge auf Ihrem Gerät und stellt es mithilfe von DPS und dem Nachweis des symmetrischen Schlüssels zur Verfügung.
:::moniker-end

Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts mit einer Device Provisioning Service-Instanz. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben. Die Gerätebestätigung bzw. der Nachweis mithilfe eines [TPM](../iot-dps/concepts-tpm-attestation.md) (Trusted Platform Module) oder von [X.509-Zertifikaten](../iot-dps/concepts-x509-attestation.md) ist sicherer und sollte verwendet werden, wenn striktere Sicherheitsanforderungen gelten.

## <a name="prerequisites"></a>Voraussetzungen

* Aktiver IoT Hub
* Physisches oder virtuelles Gerät

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Einrichten des IoT Hub Device Provisioning Service

Erstellen Sie eine neue Instanz des IoT Hub Device Provisioning Service in Azure, und verknüpfen Sie sie mit Ihrem IoT-Hub. Befolgen Sie die Anweisungen unter [Einrichten des IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md).

Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="choose-a-unique-device-registration-id"></a>Auswählen einer eindeutigen Geräteregistrierungs-ID

Eine eindeutige Registrierungs-ID muss definiert werden, um jedes Gerät zu identifizieren. Sie können die MAC-Adresse, Seriennummer oder eindeutige Informationen vom Gerät verwenden. So könnten Sie beispielsweise mithilfe einer Kombination aus MAC-Adresse und Seriennummer diese Zeichenfolge für eine Registrierungs-ID bilden: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Gültige Zeichen sind alphanumerische Kleinbuchstaben und Bindestriche (`-`).

## <a name="option-1-create-a-dps-individual-enrollment"></a>Option 1: Erstellen einer individuellen DPS-Registrierung

Erstellen Sie eine individuelle Registrierung zum Bereitstellen eines einzelnen Geräts über DPS.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

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

## <a name="option-2-create-a-dps-enrollment-group"></a>Option 2: Erstellen einer DPS-Registrierungsgruppe

Verwenden Sie die Registrierungs-ID Ihres Geräts, um eine individuelle Registrierung im DPS zu erstellen.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für IoT Edge-Geräte gilt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

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

Da es jetzt eine Registrierungsgruppe gibt, kann die IoT Edge-Runtime Geräte während der Installation automatisch bereitstellen.

### <a name="derive-a-device-key"></a>Ableiten eines Geräteschlüssels

Jedes Gerät, das im Rahmen einer Gruppenregistrierung bereitgestellt wird, benötigt einen abgeleiteten Geräteschlüssel, um während der Bereitstellung einen Nachweis des symmetrischen Schlüssels bei der Registrierung durchzuführen.

Verwenden Sie zum Generieren eines Geräteschlüssels den aus Ihrer DPS-Registrierungsgruppe kopierten Schlüssel, um einen [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Wert für die eindeutige Registrierungs-ID des Geräts zu berechnen und das Ergebnis in das Base64-Format zu konvertieren.

Nehmen Sie den Primär- oder Sekundärschlüssel Ihrer Registrierung nicht in den Gerätecode auf.

#### <a name="derive-a-key-on-linux"></a>Ableiten eines Schlüssels unter Linux

Unter Linux können Sie Ihren abgeleiteten Geräteschlüssel mithilfe von „openssl“ generieren, wie im folgenden Beispiel gezeigt wird.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch die Registrierungs-ID Ihres Geräts.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

#### <a name="derive-a-key-on-windows"></a>Ableiten eines Schlüssels unter Windows

Unter Windows können Sie Ihren abgeleiteten Geräteschlüssel mithilfe von PowerShell generieren, wie im folgenden Beispiel gezeigt wird.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch die Registrierungs-ID Ihres Geräts.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

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

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

Halten Sie die folgenden Informationen bereit:

* Den Wert für den DPS-**ID-Bereich**
* Die von Ihnen erstellte **Registrierungs-ID** für das Gerät
* Entweder der **Primärschlüssel** aus einer individuellen Registrierung oder ein [abgeleiteter Schlüssel](#derive-a-device-key) für Geräte, die eine Gruppenregistrierung verwenden.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Linux unter Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Sie können Ihr IoT Edge-Gerät mithilfe von PowerShell oder Windows Admin Center bereitstellen.

### <a name="powershell"></a>PowerShell

Führen Sie für PowerShell den folgenden Befehl mit den Platzhalterwerten aus, die mit Ihren eigenen Werten aktualisiert wurden:

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

### <a name="windows-admin-center"></a>Windows Admin Center

Führen Sie für Windows Admin Center die folgenden Schritte aus:

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **Symmetrischer Schlüssel (DPS)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Geben Sie in den Feldern „Windows Admin Center“ Ihre DPS-Bereichs-ID, die Geräteregistrierungs-ID und den Primärschlüssel oder abgeleiteten Schlüssel für die Registrierung an.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit symmetrischen Schlüsseln](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

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
:::moniker range="=iotedge-2018-06"

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Bei der Installation von IoT Edge müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Der Befehl verwendet standardmäßig die manuelle Bereitstellung mit Windows-Containern. Verwenden Sie deshalb das Flag `-DpsSymmetricKey` für die automatische Bereitstellung per Authentifizierung mit symmetrischem Schlüssel.

   Ersetzen Sie die Platzhalterwerte für `{scope_id}`, `{registration_id}` und `{symmetric_key}` durch die Daten, die Sie zuvor gesammelt haben.

   Wenn Sie Linux-Container unter Windows verwenden, fügen Sie den Parameter `-ContainerOs Linux` hinzu.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

:::moniker-end
<!-- end 1.1 -->

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
