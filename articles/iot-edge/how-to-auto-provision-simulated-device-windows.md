---
title: Automatisches Bereitstellen von Windows-Geräten mit DPS – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines simulierten Geräts auf Ihrem Windows-Computer zum Testen der automatischen Gerätebereitstellung für Azure IoT Edge mithilfe des Device Provisioning-Diensts
author: kgremban
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 01d0fc11b6aaa7e3a0a874bf60912883619f9ae8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273436"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Erstellen und Bereitstellen eines simulierten IoT Edge-Geräts mit einem virtuellen TPM unter Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge-Geräte können genau wie nicht Edge-fähige Geräte mit dem [Device Provisioning-Dienst](../iot-dps/index.yml) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

DPS unterstützt den Nachweis symmetrischer Schlüssel für IoT Edge-Geräte sowohl in Einzelregistrierungen als auch in Gruppenregistrierungen. Wenn Sie die Option zum Festlegen eines IoT Edge-Geräts für den Nachweis symmetrischer Schlüssel auf TRUE festlegen, werden bei Gruppenregistrierungen alle in dieser Registrierungsgruppe registrierten Geräte als IoT Edge-Geräte markiert.

In diesem Artikel wird beschrieben, wie Sie die automatische Bereitstellung auf einem simulierten IoT Edge-Gerät mit den folgenden Schritten testen:

* Erstellen Sie eine neue Instanz für den IoT Hub Device Provisioning-Dienst (DPS).
* Erstellen Sie ein simuliertes Gerät auf Ihrem Windows-Computer (VM) mit einem simulierten Trusted Platform Module (TPM) für die Hardwaresicherheit.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Installieren Sie IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub.

> [!TIP]
> In diesem Artikel wird beschrieben, wie Sie die automatische Bereitstellung mithilfe des TPM-Nachweises auf virtuellen Geräten testen. Viele Informationen darin gelten aber auch bei der Verwendung von physischer TPM-Hardware.

## <a name="prerequisites"></a>Voraussetzungen

* Windows-Entwicklungscomputer. In diesem Artikel wird Windows 10 verwendet.
* Aktiver IoT Hub.

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird. Es kann nur zum Erstellen von einzelnen Registrierungen (keinen Gruppenregistrierungen) verwendet werden.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Einrichten des IoT Hub Device Provisioning Service

Erstellen Sie eine neue Instanz des IoT Hub Device Provisioning Service in Azure, und verknüpfen Sie sie mit Ihrem IoT-Hub. Befolgen Sie die Anweisungen unter [Einrichten des IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md).

Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

> [!TIP]
> Wenn Sie ein physisches TPM-Gerät verwenden, müssen Sie den **Endorsement Key** ermitteln, der für jeden TPM-Chip eindeutig ist und von dem ihm zugeordneten TPM-Chiphersteller abgerufen wird. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.
>
> Folgen Sie den Anleitungen im Artikel [Verwalten von Geräteregistrierungen mit dem Azure Portal](../iot-dps/how-to-manage-enrollments.md) zum Erstellen Ihrer Registrierung in DPS, und lesen Sie dann den Abschnitt [Installieren der IoT Edge-Runtime](#install-the-iot-edge-runtime) in diesem Artikel, um den Vorgang fortzusetzen.

## <a name="simulate-a-tpm-device"></a>Simulieren eines TPM-Geräts

Erstellen Sie ein simuliertes TPM-Gerät auf dem Windows-Entwicklungscomputer. Rufen Sie die **Registrierungs-ID** und den **Endorsement Key** für Ihr Gerät ab, und verwenden Sie sie zum Erstellen eines individuellen Registrierungseintrags im DPS.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-at-scale.md) verwendet.

Wählen Sie die SDK-Sprache für die Erstellung des simulierten Geräts, und führen Sie die Schritte durch, um die individuelle Registrierung zu erstellen.

Wenn Sie die individuelle Registrierung erstellen, wählen Sie **TRUE** aus, um anzugeben, dass das simulierte TPM-Gerät auf Ihrem Windows-Entwicklungscomputer ein **IoT Edge-Gerät** ist.

> [!TIP]
> In der Azure CLI können Sie eine [Registrierung](/cli/azure/iot/dps/enrollment) oder eine [Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group) erstellen und mithilfe des Flags **edge-enabled** angeben, dass ein Gerät oder eine Gruppe von Geräten ein IoT Edge-Gerät ist.

Informationen zum Erstellen einer individuellen Registrierung finden Sie in der [Schnellstartanleitung zum Bereitstellen eines simulierten TPM-Geräts](../iot-dps/quick-create-simulated-device-tpm.md).


Nachdem Sie die individuelle Registrierung erstellt haben, speichern Sie den Wert der **Registrierungs-ID**. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Die Komponenten werden in Containern ausgeführt, und Sie können weitere Container auf dem Gerät bereitstellen, um Code im Edge-Bereich auszuführen. Installieren Sie die IoT Edge-Runtime auf dem Gerät, auf dem das simulierte TPM ausgeführt wird.

Führen Sie die Schritte in [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) aus, und kehren Sie dann zu diesem Artikel zurück, um das Gerät bereitzustellen.

> [!TIP]
> Lassen Sie das Fenster, in dem der TPM-Simulator ausgeführt wird, während der Installation und beim Testen geöffnet.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen Device Provisioning Service und IoT Hub verwendet.

1. Ermitteln Sie Ihren DPS-**ID-Bereich** und die **Registrierungs-ID** des Geräts, die in den vorhergehenden Abschnitten erfasst wurden.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. Bei der Installation von IoT Edge müssen Sie eine AMD64-Sitzung von PowerShell – nicht PowerShell (x86) – verwenden.

1. Durch den Befehl **Deploy-IoTEdge** wird überprüft, ob Ihr Windows-Computer über eine unterstützte Version verfügt. Außerdem aktiviert der Befehl das Containerfeature und lädt dann die Moby-Runtime und die IoT Edge-Runtime herunter. Der Befehl verwendet standardmäßig Windows-Container.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. An diesem Punkt könnten IoT Core-Geräte möglicherweise automatisch neu starten. Windows 10- oder Windows Server-Geräte könnten Sie zum Neustart auffordern. Wenn ja, starten Sie Ihr Gerät jetzt neu. Sobald Ihr Gerät bereit ist, führen Sie PowerShell erneut als Administrator aus.

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
