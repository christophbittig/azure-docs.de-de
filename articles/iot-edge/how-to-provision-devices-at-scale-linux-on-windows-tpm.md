---
title: Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe eines TPM – Azure IoT Edge | Microsoft-Dokumentation
description: Mithilfe eines simulierten TPM (Trusted Platform Module) auf einem Linux unter Windows-Gerät können Sie den Azure Device Provisioning Service (Gerätebereitstellungsdienst) für Azure IoT Edge testen.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: b286dfd6fd4e494427b1094fc38039e575af1e17
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846081"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>Bedarfsgerechtes Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe eines TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält Anleitungen für die automatische Bereitstellung eines Azure IoT Edge für Linux unter Windows-Geräts mithilfe eines TPM (Trusted Platform Module). Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (Gerätebereitstellungsdienst) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

- Automatische Bereitstellung eines Linux unter Windows-Geräts mit physischer TPM-Hardware.
- Automatische Bereitstellung eines Linux unter Windows-Geräts mithilfe eines simulierten TPM. Wir empfehlen diese Methodik nur als Testszenario. Ein simuliertes TPM bietet nicht die gleiche Sicherheit wie ein physisches TPM.

Aufgaben:

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

* Installieren Sie IoT Edge für Linux unter Windows.
* Rufen Sie die TPM-Informationen von Ihrem Gerät ab.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Stellen Sie Ihr Gerät mit seinen TPM-Informationen bereit.

# <a name="simulated-tpm"></a>[Simuliertes TPM](#tab/simulated-tpm)

* Installieren Sie IoT Edge für Linux unter Windows.
* Richten Sie Ihr simuliertes TPM ein, und rufen Sie dessen Bereitstellungsinformationen ab.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Stellen Sie Ihr Gerät mit seinen TPM-Informationen bereit.

---

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn Sie den TPM-Nachweis beim Device Provisioning Service verwenden.
>
> Wenn Sie ein TPM verwenden, können Sie nur individuelle Registrierungen des Device Provisioning Service erstellen, aber keine Gruppenregistrierungen.

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

Es gibt einige Schritte, um Ihr Gerät für die Bereitstellung mit TPM vorzubereiten. Lassen Sie Ihre Bereitstellung geöffnet, während Sie Ihr Gerät vorbereiten. Später in diesem Artikel kehren Sie zu Ihrer Bereitstellung zurück.

## <a name="enable-tpm-passthrough"></a>Aktivieren des TPM-Passthroughs

Die IoT Edge für Linux-VM unter Windows enthält ein TPM-Feature, das aktiviert oder deaktiviert werden kann. Es ist standardmäßig deaktiviert. Wenn dieses Feature aktiviert wurde, kann die VM auf das TPM des Hostcomputers zugreifen.

1. Öffnen Sie PowerShell in einer Sitzung mit erhöhten Rechten.

1. Wenn dies noch nicht geschehen ist, legen Sie für die Ausführungsrichtlinie auf Ihrem Gerät `AllSigned` fest, damit Sie die PowerShell-Funktionen von IoT Edge für Linux unter Windows ausführen können.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Aktivieren Sie das TPM-Feature.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>Abrufen der TPM-Informationen von Ihrem Gerät

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

Zum Bereitstellen Ihres Geräts benötigen Sie einen **Endorsement Key** für Ihren TPM-Chip und eine **Registrierungs-ID** für Ihr Gerät. Sie stellen diese Informationen für Ihre Instanz von Device Provisioning Service bereit, damit der Dienst Ihr Gerät beim Versuch der Verbindungsherstellung erkennen kann.

Der Endorsement Key ist für jeden TPM-Chip eindeutig. Er wird vom zugeordneten TPM-Chiphersteller bezogen. Sie können eine eindeutige Registrierungs-ID für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

IoT Edge für Linux unter Windows enthält ein PowerShell-Skript zum Abrufen dieser Informationen aus Ihrem TPM. Führen Sie zur Verwendung des Skripts die folgenden Schritte auf Ihrem Gerät aus:

1. Öffnen Sie PowerShell in einer Sitzung mit erhöhten Rechten.

1. Führen Sie den Befehl aus.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

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

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öffnen Sie auf dem Windows-Gerät eine PowerShell-Sitzung mit erhöhten Rechten.

1. Stellen Sie Ihr Gerät unter Angabe der **Bereichs-ID** bereit, die Sie aus Ihrer Instanz des Device Provisioning Service erfasst haben.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "SCOPE_ID_HERE"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Stellen Sie Ihr Gerät im Schritt **Verbinden** bereit.

   1. Wählen Sie die Bereitstellungsmethode **DpsTpm** aus.
   1. Geben Sie die **Bereichs-ID** an, die Sie aus Ihrer Instanz des Device Provisioning Service abrufen.

      ![Screenshot: Bereitstellung Ihres Geräts mit dem Device Provisioning Service und TPM-Nachweis.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Wählen Sie **Mit der ausgewählten Methode bereitstellen** aus.

1. Nachdem IoT Edge auf Ihrem Gerät erfolgreich installiert und bereitgestellt wurde, wählen Sie **Fertig stellen** aus, um den Bereitstellungs-Assistenten zu beenden.

---

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub wechseln und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning-Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

1. Stellen Sie mithilfe des folgenden Befehls in Ihrer PowerShell-Sitzung eine Verbindung mit Ihrer IoT Edge-VM für Linux unter Windows her:

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >Das einzige Konto, für das eine SSH-Verbindung mit der VM verwendet werden kann, ist der Benutzer, der sie erstellt hat.

1. Nachdem Sie angemeldet sind, können Sie die Liste der gerade ausgeführten IoT Edge-Module mithilfe des folgenden Linux-Befehls überprüfen:

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

   * Im Abschnitt mit der **Liste der IoT Edge-Module** werden die auf dem Gerät ausgeführten Module angezeigt. Wenn der IoT Edge-Dienst zum ersten Mal gestartet wird, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul „edgeAgent“ wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von weiteren Modulen, die Sie auf Ihrem Gerät bereitstellen.
   * Im Abschnitt **IoT Edge-Status** wird der Dienststatus angezeigt. Er sollte **Aktiv (wird ausgeführt)** lauten.

---

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen.

Weitere Informationen finden Sie unter „[Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [mithilfe der Azure CLI](how-to-deploy-cli-at-scale.md)“.
