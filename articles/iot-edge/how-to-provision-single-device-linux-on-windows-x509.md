---
title: Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe von X.509-Zertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Erstellen und Bereitstellen eines einzelnen IoT Edge für Linux unter Windows-Geräts in IoT Hub mithilfe der manuellen Bereitstellung mit X.509-Zertifikaten
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: ccc4efe5978b9a97e0e4d535a42545161f33c6c4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842684"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-using-x509-certificates"></a>Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe von X.509-Zertifikaten

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen zum Registrieren und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird für Produktionsszenarien empfohlen.

In diesem Artikel wird die Verwendung von X.509-Zertifikaten als Authentifizierungsmethode behandelt. Wenn Sie symmetrische Schlüssel verwenden möchten, lesen Sie [Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe von symmetrischen Schlüsseln](how-to-provision-single-device-linux-on-windows-symmetric.md).

> [!NOTE]
> Wenn Sie viele Geräte einrichten müssen und sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel, wie IoT Edge beim IoT Hub Device Provisioning Service (Gerätebereitstellungsdienst) funktioniert:
>
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von symmetrischen Schlüsseln](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie Ihr IoT Edge-Gerät registrieren und IoT Edge für Linux unter Windows installieren. Bei diesen Aufgaben gibt es unterschiedliche Voraussetzungen und Hilfsprogramme zu deren Erledigung. Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie den Vorgang fortsetzen.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Generate device identity certificates H2 and content -->
[!INCLUDE [iot-edge-generate-device-identity-certs.md](../../includes/iot-edge-generate-device-identity-certs.md)]

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-x509.md](../../includes/iot-edge-register-device-x509.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Sie können Ihr Gerät mit den zugehörigen Cloudidentitäts- und Authentifizierungsinformationen einrichten.

Um Ihr Gerät mithilfe von X.509-Zertifikaten bereitstellen zu können, benötigen Sie Ihren **IoT Hub-Namen**, die **Geräte-ID** und die absoluten Pfade zu Ihrem **Identitätszertifikat** sowie **private Schlüssel** auf Ihrem Windows-Hostcomputer.

Sie können für das Bereitstellen Ihrer Geräte Windows Admin Center oder eine PowerShell-Sitzung mit erhöhten Rechten verwenden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Halten Sie das Geräteidentitätszertifikat und den zugehörigen privaten Schlüssel für Ihr Zielgerät bereit. Sie müssen den absoluten Pfad zu beiden Dateien kennen.

Führen Sie den folgenden Befehl mit erhöhten Rechten in einer PowerShell-Sitzung auf Ihrem Zielgerät aus. Ersetzen Sie den Platzhaltertext durch Ihre eigenen Werte.

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "HUB_HOSTNAME_HERE" -deviceId "DEVICE_ID_HERE" -identityCertPath "ABSOLUTE_PATH_TO_IDENTITY_CERT_HERE" -identityPrivKeyPath "ABSOLUTE_PATH_TO_PRIVATE_KEY_HERE"
```

Weitere Informationen zum Befehl `Provision-EflowVM` finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden die Option **ManualX509** aus.

   ![Auswahl der manuellen Bereitstellung mit X.509-Zertifikaten](./media/how-to-provision-single-device-linux-on-windows-x509/provisioning-with-selected-method-manual-x509.png)

1. Geben Sie die erforderlichen Parameter an:

   * **IoT Hub-Hostname**: Der Name des IoT Hubs, bei dem dieses Gerät registriert ist.
   * **Geräte-ID**: Der Name, mit dem dieses Gerät registriert ist.
   * **Zertifikatdatei**: Laden Sie das Geräteidentitätszertifikat hoch, das auf die VM verschoben und zum Bereitstellen des Geräts verwendet wird.
   * **Datei mit privatem Schlüssel**: Laden Sie die Datei mit dem zugehörigen privaten Schlüssel hoch, die auf die VM verschoben und zum Bereitstellen des Geräts verwendet wird.

1. Wählen Sie **Mit der ausgewählten Methode bereitstellen** aus.

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät vom Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

---

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

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

* Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
* Erfahren Sie, wie Sie [Zertifikate auf Ihrem IoT Edge für Linux auf einem virtuellen Windows-Computer verwalten](how-to-manage-device-certificates.md) und Dateien vom Host-Betriebssystem auf Ihren virtuellen Linux-Computer übertragen.
* Erfahren Sie, wie [Sie Ihre IoT Edge-Geräte für die Kommunikation über einen Proxy-Server konfigurieren](how-to-configure-proxy-support.md).
