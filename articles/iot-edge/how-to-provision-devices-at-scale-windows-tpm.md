---
title: Erstellen und Bereitstellen von Geräten mit einem virtuellen TPM unter Windows – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines simulierten TPM auf einem Windows-Gerät, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4211cd69cddfea77ccd3f6e2a095ced6ce5effc3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853869"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM unter Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält Anleitungen für die automatische Bereitstellung eines Azure IoT Edge für Windows-Geräts mithilfe eines TPM (Trusted Platform Module). Sie können loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (Gerätebereitstellungsdienst) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

>[!NOTE]
>Azure IoT Edge mit Windows-Containern wird ab Version 1.2 von Azure IoT Edge nicht mehr unterstützt.
>
>Ziehen Sie die neue Methode [Azure IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) zum Ausführen von IoT Edge auf Windows-Geräten in Betracht.
>
>Wenn Sie Azure IoT Edge für Linux unter Windows verwenden möchten, können Sie die Schritte in der [entsprechenden Schrittanleitung](how-to-provision-devices-at-scale-linux-on-windows-tpm.md) ausführen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

- Automatische Bereitstellung eines Windows-Geräts mit physischer TPM-Hardware.
- Automatische Bereitstellung eines Windows-Geräts, auf dem ein simuliertes TPM ausgeführt wird. Wir empfehlen diese Methodik nur als Testszenario. Ein simuliertes TPM bietet nicht die gleiche Sicherheit wie ein physisches TPM.

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

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Windows-Entwicklungscomputer. In diesem Artikel wird Windows 10 verwendet.

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn Sie den TPM-Nachweis beim Device Provisioning Service verwenden.
>
> Wenn Sie ein TPM verwenden, können Sie nur individuelle Registrierungen des Device Provisioning Service erstellen, aber keine Gruppenregistrierungen.

## <a name="set-up-your-tpm"></a>Einrichten Ihres TPMs

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

In diesem Abschnitt erstellen Sie ein Tool, mit dem Sie die Registrierungs-ID und den Endorsement Key für Ihr TPM abrufen können.

1. Führen Sie die Schritte in [Einrichten einer Windows-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) zum Installieren und Erstellen des Azure IoT-Geräte-SDKs für C aus.

1. Führen Sie die folgenden Befehle in einer PowerShell-Sitzung mit erhöhten Rechten aus, um das SDK-Tool zu erstellen, das Ihre Gerätebereitstellungsinformationen für Ihr TPM abruft.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** des Geräts angezeigt. Kopieren Sie diese Werte zur späteren Verwendung, wenn Sie eine individuelle Registrierung für Ihr Gerät im Device Provisioning Service erstellen.

> [!TIP]
> Wenn Sie nicht das SDK-Tool zum Abrufen der Bereitstellungsinformationen verwenden möchten, müssen Sie eine andere Möglichkeit zum Abrufen dieser Informationen finden. Der Endorsement Key, der bei jedem TPM-Chip eindeutig ist, wird von dem ihm zugeordneten TPM-Chiphersteller abgerufen. Sie können eine eindeutige Registrierungs-ID für Ihr TPM-Gerät ableiten. So können Sie beispielsweise einen SHA-256-Hash des Endorsement Key erstellen.

Nachdem Sie über Ihre Registrierungs-ID und Ihren Endorsement Key verfügen, können Sie den Vorgang fortsetzen.

# <a name="simulated-tpm"></a>[Simuliertes TPM](#tab/simulated-tpm)

Wenn Sie kein physisches TPM zur Verfügung haben und diese Bereitstellungsmethode testen möchten, können Sie ein TPM auf Ihrem Gerät simulieren.

Der IoT Hub Device Provisioning Service enthält Beispiele, die ein TPM simulieren sowie den Endorsement Key und die Registrierungs-ID automatisch zurückgeben.

1. Wählen Sie eines der Beispiele – basierend auf Ihrer bevorzugten Sprache – aus der folgenden Liste aus.
1. Beenden Sie die Ausführung der Beispielschritte für den Device Provisioning Service, nachdem Sie das simulierte TPM ausgeführt sowie den **Endorsement Key** und die **Registrierungs-ID** erfasst haben. Wählen Sie zum Ausführen der Registrierung in der Beispielanwendung nicht **Eingeben** aus.
1. Behalten Sie die Ausführung des Fensters, in dem das simulierte TPM gehostet wird, so lange bei, bis Sie das Testen dieses Szenarios abgeschlossen haben.
1. Kehren Sie zu diesem Artikel zurück, um eine Registrierung für den Device Provisioning Service zu erstellen und Ihr Gerät zu konfigurieren.

Simulierte TPM-Beispiele:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen dem Device Provisioning Service und IoT Hub verwendet.

1. Ermitteln Sie Ihren **ID-Bereich** des Device Provisioning Service und die **Registrierungs-ID** des Geräts, die in den vorhergehenden Abschnitten erfasst wurden.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Wenn Sie IoT Edge installieren, müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Der Befehl `Initialize-IoTEdge` konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet. Geben Sie das Flag `-Dps` an, wenn Sie den Device Provisioning Service statt der manuellen Bereitstellung verwenden möchten.

   Ersetzen Sie die Platzhalterwerte für `paste_scope_id_here` und `paste_registration_id_here` durch die zuvor gesammelten Daten.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here
   ```

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, wechseln Sie zu Ihrem IoT Hub, und beginnen Sie mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät. Verwenden Sie die folgenden Befehle auf Ihrem Gerät, um zu überprüfen, ob das Installieren und Starten der Runtime erfolgreich war.

1. Überprüfen Sie den Status des IoT Edge-Diensts.

    ```powershell
    Get-Service iotedge
    ```

1. Untersuchen Sie die Dienstprotokolle der letzten fünf Minuten.

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. Führen Sie ausgeführte Module auf.

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen.

Weitere Informationen finden Sie unter „[Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [mithilfe der Azure CLI](how-to-deploy-cli-at-scale.md)“.
