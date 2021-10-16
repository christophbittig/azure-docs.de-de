---
title: Bereitstellen eines Geräts mit einem virtuellen TPM unter Windows – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines simulierten TPMs auf einem Windows-Gerät, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f130adffa4f5d542082b3ab6271952a867c30cdb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661810"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM unter Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung eines Windows IoT Edge-Geräts mithilfe eines TPMs (Trusted Platform Module). Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

1. Automatische Bereitstellung eines Windows-Geräts mit physischer TPM-Hardware.
1. Automatische Bereitstellung eines Windows-Geräts, auf dem ein simuliertes TPM ausgeführt wird. Diese Methodik wird nur als Testszenario empfohlen, weil ein simuliertes TPM nicht dieselbe Sicherheit wie ein physisches TPM bietet.

Die Anleitungen unterscheiden sich je nach Ihrer Methodik. Sorgen Sie deshalb künftig dafür, dass die richtige Registerkarte angezeigt wird.

Aufgaben:

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

* Rufen Sie die Bereitstellungsinformationen für Ihr Gerät ab.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Installieren Sie IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub.

# <a name="simulated-tpm"></a>[Simuliertes TPM](#tab/simulated-tpm)

* Richten Sie Ihr simuliertes TPM ein, und rufen Sie dessen Bereitstellungsinformationen ab.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Installieren Sie IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub.

---

## <a name="prerequisites"></a>Voraussetzungen

Die Voraussetzungen für physische TPM- und virtuelle TPM-Lösungen sind identisch.

* Windows-Entwicklungscomputer. In diesem Artikel wird Windows 10 verwendet.
* Aktiver IoT Hub.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird.
>
> Sie können nur einzelne DPS-Registrierungen (keine Gruppe) erstellen, wenn Sie ein TPM verwenden.

## <a name="set-up-your-tpm"></a>Einrichten Ihres TPMs

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

In diesem Abschnitt erstellen Sie ein Tool, mit dem Sie die **Registrierungs-ID** und den **Endorsement Key** für Ihr TPM abrufen können.

1. Führen Sie die Schritte in [Einrichten einer Windows-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) zum Installieren und Erstellen des Azure IoT-Geräte-SDKs für C aus.

1. Führen Sie die folgenden Befehle in einer PowerShell-Sitzung mit erhöhten Rechten aus, um das SDK-Tool zu erstellen, das Ihre Gerätebereitstellungsinformationen für Ihr TPM abruft.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** des Geräts angezeigt. Kopieren Sie diese Werte. Sie werden später zur Erstellung einer individuellen Registrierung für Ihr Gerät in DPS benötigt.

> [!TIP]
> Wenn Sie nicht das SDK-Tool zum Abrufen der Bereitstellungsinformationen verwenden möchten, müssen Sie eine andere Möglichkeit zum Abrufen dieser Informationen finden. Der **Endorsement Key**, der bei jedem TPM-Chip eindeutig ist, wird von dem ihm zugeordneten TPM-Chiphersteller abgerufen. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

Sobald Sie über Ihre Registrierungs-ID und Ihren Endorsement Key verfügen, können Sie den Vorgang fortsetzen.

# <a name="simulated-tpm"></a>[Simuliertes TPM](#tab/simulated-tpm)

Wenn Sie kein physisches TPM zur Verfügung haben und diese Bereitstellungsmethode testen möchten, können Sie ein TPM auf Ihrem Gerät simulieren.

IoT Hub Device Provisioning Service enthält Beispiele, die ein TPM simulieren sowie den **Endorsement Key** und die **Registrierungs-ID** automatisch zurückgeben.

1. Wählen Sie eines der Beispiele – basierend auf Ihrer bevorzugten Sprache – aus der folgenden Liste aus.
1. Beenden Sie die Ausführung der DPS-Beispielschritte, nachdem Sie das simulierte TPM ausgeführt sowie den **Endorsement Key** und die **Registrierungs-ID** erfasst haben. Drücken Sie zum Ausführen der Registrierung in der Beispielanwendung nicht die *EINGABETASTE*.
1. Behalten Sie die Ausführung des Fensters, in dem das simulierte TPM gehostet wird, so lange bei, bis Sie das Testen dieses Szenarios abgeschlossen haben.
1. Kehren Sie zum Erstellen einer DPS-Registrierung und Konfigurieren Ihres Geräts zu diesem Artikel zurück.

Simulierte TPM-Beispiele:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Verwenden Sie die Bereitstellungsinformationen Ihres TPMs zum Erstellen einer individuellen Registrierung im Device Provisioning Service.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

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

In diesem Abschnitt bereiten Sie Ihren virtuellen Windows-Computer oder Ihr physisches Gerät für IoT Edge vor. Anschließend installieren Sie IoT Edge.

Es gibt einen Schritt, den Sie auf Ihrem Gerät ausführen müssen, bevor es für die Installation der IoT Edge-Runtime bereit ist. Auf Ihrem Gerät muss eine Container-Engine installiert werden.

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

1. Ermitteln Sie Ihren DPS-**ID-Bereich** und die **Registrierungs-ID** des Geräts, die in den vorhergehenden Abschnitten erfasst wurden.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Bei der Installation von IoT Edge müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet. Verwenden Sie das `-Dps`-Flag, um den Device Provisioning Service anstelle der manuellen Bereitstellung zu verwenden.

   Ersetzen Sie die Platzhalterwerte für `{scope_id}` und `{registration_id}` durch die zuvor gesammelten Daten.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen. Verwenden Sie die folgenden Befehle auf Ihrem Gerät, um zu überprüfen, ob das Installieren und Starten der Runtime erfolgreich war.  

Überprüfen Sie den Status des IoT Edge-Diensts.

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle der letzten fünf Minuten.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure-Befehlszeilenschnittstelle](how-to-deploy-cli-at-scale.md)
