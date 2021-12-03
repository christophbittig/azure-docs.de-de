---
title: Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von symmetrischen Schlüsseln unter Linux unter Windows – Azure IoT Edge | Microsoft-Dokumentation
description: Verwendung des Nachweises des symmetrischen Schlüssels zum Testen der bedarfsgerechten Bereitstellung von Linux auf Windows-Geräten für Azure IoT Edge mit dem Device Provisioning Service (Gerätebereitstellungsdienst)
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b11313c7ff443226352558ffb172c3bbf59964b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852121"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>Erstellen und Bereitstellen von IoT Edge für Linux auf Windows-Geräten im großen Stil mithilfe von symmetrischen Schlüsseln 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung von mindestens einem [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md)-Gerät mithilfe von symmetrischen Schlüsseln. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub-Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Erstellen Sie entweder eine **individuelle Registrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Stellen Sie einen virtuellen Linux-Computer mit installierter IoT Edge-Runtime bereit, und verbinden Sie ihn mit dem IoT Hub.

Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts mit einer Device Provisioning Service-Instanz. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben. Die Gerätebestätigung bzw. der Nachweis mithilfe eines [TPM](../iot-dps/concepts-tpm-attestation.md) (Trusted Platform Module) oder von [X.509-Zertifikaten](../iot-dps/concepts-x509-attestation.md) ist sicherer und sollte verwendet werden, wenn striktere Sicherheitsanforderungen gelten.

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Sobald die Runtime auf Ihrem Gerät installiert wurde, konfigurieren Sie es mit den Informationen, die es zum Herstellen einer Verbindung zwischen dem Device Provisioning Service und IoT Hub verwendet.

Halten Sie die folgenden Informationen bereit:

* Den Wert für den DPS-**ID-Bereich**
* Die von Ihnen erstellte **Registrierungs-ID** für das Gerät
* Entweder der **Primärschlüssel** aus einer individuellen Registrierung oder ein [abgeleiteter Schlüssel](#derive-a-device-key) für Geräte, die eine Gruppenregistrierung verwenden.

Sie können Ihr IoT Edge-Gerät mithilfe von PowerShell oder Windows Admin Center bereitstellen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie für PowerShell den folgenden Befehl mit den Platzhalterwerten aus, die mit Ihren eigenen Werten aktualisiert wurden:

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Führen Sie für Windows Admin Center die folgenden Schritte aus:

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **Symmetrischer Schlüssel (DPS)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Geben Sie in den Feldern „Windows Admin Center“ Ihre DPS-Bereichs-ID, die Geräteregistrierungs-ID und den Primärschlüssel oder abgeleiteten Schlüssel für die Registrierung an.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Mit der ausgewählten Methode bereitstellen“ nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit symmetrischen Schlüsseln (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

---

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Device Provisioning Service-Instanz. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

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

    1. Rufen Sie die Dienstprotokolle ab.

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

Der Registrierungsprozess des Device Provisioning Service ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).

Sie können außerdem:

* Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
* Erfahren Sie, wie Sie [Zertifikate auf Ihrem IoT Edge für Linux auf einem virtuellen Windows-Computer verwalten](how-to-manage-device-certificates.md) und Dateien vom Host-Betriebssystem auf Ihren virtuellen Linux-Computer übertragen.
* Erfahren Sie, wie [Sie Ihre IoT Edge-Geräte für die Kommunikation über einen Proxy-Server konfigurieren](how-to-configure-proxy-support.md).
