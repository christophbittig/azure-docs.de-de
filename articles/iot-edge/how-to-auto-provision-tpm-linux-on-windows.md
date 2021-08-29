---
title: Automatische Bereitstellung von Windows-Geräten mit DPS und TPM – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden der automatischen Gerätebereitstellung für IoT Edge für Linux unter Windows mit dem Device Provisioning-Dienst (Device Provisioning Service, DPS) und TPM-Nachweis
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 963a1f874bb66a923ebee04768fb238a5aecd4ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349612"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts für Linux unter Windows mit TPM-Nachweis

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge-Geräte können genauso wie nicht Edge-fähige Geräte mit dem [Device Provisioning-Dienst](../iot-dps/index.yml) (Device Provisioning Service, DPS) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Übersicht zur [Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

DPS unterstützt den TPM-Nachweis (Trusted Platform Module) für IoT Edge-Geräte nur bei der individuellen Registrierung, nicht bei der Gruppenregistrierung.

In diesem Artikel erfahren Sie, wie Sie die automatische Bereitstellung auf einem Gerät mit IoT Edge für Linux unter Windows mit den folgenden Schritten einsetzen können:

* Installieren Sie IoT Edge für Linux unter Windows.
* Rufen Sie die TPM-Informationen von Ihrem Gerät ab.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Stellen Sie Ihr Gerät mit seinen TPM-Informationen bereit.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Windows-Gerät. Informationen zu unterstützten Windows-Versionen finden Sie unter [Betriebssysteme](support.md#operating-systems).
* Ein aktiver IoT-Hub.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning-Diensts verfügen, führen Sie die Anleitungen unter [Einrichten des IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md) aus.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie verwenden diesen Wert beim Bereitstellen des IoT Edge-Geräts.

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird. Es kann nur zum Erstellen von einzelnen Registrierungen (keinen Gruppenregistrierungen) verwendet werden.

## <a name="install-iot-edge-for-linux-on-windows"></a>Installieren von IoT Edge für Linux unter Windows

Die Installationsschritte in diesem Abschnitt sind gekürzt, um sie speziell für das TPM-Bereitstellungsszenario hervorzuheben. Ausführlichere Anleitungen, darunter Voraussetzungen und Schritte für die Remoteinstallation, finden Sie unter [Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät](how-to-install-iot-edge-on-windows.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öffnen Sie auf dem Windows-Gerät eine PowerShell-Sitzung mit erhöhten Rechten.

1. Laden Sie IoT Edge für Linux unter Windows herunter.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. Damit die Bereitstellung erfolgreich ausgeführt werden kann, müssen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` festlegen, wenn das noch nicht geschehen ist.

   1. Überprüfen Sie die aktuelle Ausführungsrichtlinie.

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` lautet, aktualisieren Sie die Richtlinie.

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. Stellen Sie IoT Edge für Linux unter Windows bereit.

   ```powershell
   Deploy-Eflow
   ```

1. Geben Sie `Y` ein, um die Lizenzbedingungen zu akzeptieren.

1. Durch die Eingabe von `O` oder `R` können Sie **Optionale Diagnosedaten** je nach Ihrer Einstellung ein- oder ausschalten.

1. Sobald IoT Edge für Linux unter Windows auf Ihrem Gerät erfolgreich bereitgestellt wurde, meldet die Ausgabe **Bereitstellung erfolgreich**.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Die Azure IoT Edge-Erweiterung für Windows Admin Center befindet sich zurzeit in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Installations- und Verwaltungsprozesse können sich von denen bei allgemeiner Verfügbarkeit unterscheiden.

1. Windows Admin Center muss mit der Erweiterung **Azure IoT Edge** konfiguriert sein.

1. Wählen Sie auf der Seite mit den Windows Admin Center-Verbindungen **Hinzufügen** aus.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

1. Führen Sie die Schritte im Bereitstellungs-Assistenten zum Installieren und Konfigurieren von IoT Edge für Linux unter Windows aus.

   1. Überprüfen Sie in den unter **Erste Schritte** aufgeführten Schritten die Voraussetzungen, akzeptieren Sie die Lizenzbedingungen, und wählen Sie, ob Diagnosedaten gesendet werden sollen oder nicht.

   1. Wählen Sie in den Schritten unter **Bereitstellen** Ihr Gerät und dessen Konfigurationseinstellungen aus. Beobachten Sie dann den Fortschritt, während IoT Edge auf Ihrem Gerät bereitgestellt wird.

   1. Wählen Sie **Weiter** aus, um den Vorgang mit dem Schritt **Verbinden** fortzusetzen, in dem Sie die Bereitstellungsinformationen für Ihr Gerät angeben.

1. Beenden Sie auf der Seite **Bereitstellung** die Arbeit mit dem Bereitstellungs-Assistenten. Es müssen nämlich noch einige Schritte zur Vorbereitung Ihres Geräts für die Bereitstellung mit TPM ausgeführt werden, bevor Sie weitermachen können. Lassen Sie das Windows Admin Center-Fenster geöffnet, da Sie am Ende dieses Artikels dorthin zurückkehren, um die Bereitstellungsschritte abzuschließen.

---

## <a name="enable-tpm-passthrough"></a>Aktivieren des TPM-Passthroughs

Der virtuelle IoT Edge-Computer für Linux unter Windows hat ein TPM-Feature, das aktiviert oder deaktiviert werden kann. Es ist standardmäßig deaktiviert. Wenn dieses Feature aktiviert wurde, kann der virtuelle Computer auf das TPM des Hostcomputers zugreifen.

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

Zur Bereitstellung Ihres Geräts müssen Sie Informationen aus Ihrem TPM-Chip erfassen und für Ihre Instanz des Device Provisioning-Diensts (Device Provisioning Service, DPS) bereitstellen, damit der Dienst Ihr Gerät erkennen kann, wenn es eine Verbindung herzustellen versucht.

Zuerst müssen Sie den **Endorsement Key** ermitteln, der bei jedem TPM-Chip eindeutig ist und von dem ihm zugeordneten TPM-Chiphersteller abgerufen wird. Danach müssen Sie eine **Registrierungs-ID** für Ihr Gerät angeben. Sie können eine eindeutige Registrierungs-ID für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

IoT Edge für Linux unter Windows enthält eine PowerShell-Funktion zum Abrufen dieser Informationen aus Ihrem TPM. Führen Sie zur Verwendung des Skripts die folgenden Schritte auf Ihrem Gerät aus:

1. Öffnen Sie PowerShell in einer Sitzung mit erhöhten Rechten.

1. Führen Sie den Befehl aus.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>Simulieren eines TPM (optional)

Wenn Sie kein physisches TPM zur Verfügung haben und diese Bereitstellungsmethode testen möchten, können Sie ein TPM auf Ihrem Gerät simulieren.

IoT Hub Device Provisioning Service enthält Beispiele, die ein TPM simulieren sowie den Endorsement Key und die Registrierungs-ID automatisch zurückgeben.

1. Wählen Sie eines der Beispiele – basierend auf Ihrer bevorzugten Sprache – aus der folgenden Liste aus.
1. Beenden Sie die Ausführung der DPS-Beispielschritte, nachdem Sie das simulierte TPM ausgeführt sowie den Endorsement Key und die Registrierungs-ID erfasst haben. Drücken Sie zum Ausführen der Registrierung in der Beispielanwendung nicht die *EINGABETASTE*.
1. Behalten Sie die Ausführung des Fensters, in dem das simulierte TPM gehostet wird, so lange bei, bis Sie das Testen dieses Szenarios vollständig abgeschlossen haben.
1. Kehren Sie zum Erstellen einer DPS-Registrierung und Konfigurieren Ihres Geräts zu diesem Artikel zurück.

Simulierte TPM-Beispiele:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

## <a name="create-a-device-enrollment-entry"></a>Erstellen eines Geräteregistrierungseintrags

Der Device Provisioning-Dienst unterstützt zwei Registrierungsarten. Registrierungsgruppen werden für die Registrierung mehrerer verbundener Geräte verwendet. Individuelle Registrierungen werden für die Registrierung eines einzelnen Geräts verwendet. Der TPM-Nachweis unterstützt nur individuelle Registrierungen, weil eine Gruppe von Geräten TPM-Informationen nicht freigeben kann. Deshalb werden in diesem Artikel individuelle Registrierungen erläutert.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Instanz des Device Provisioning-Diensts.

1. Wählen Sie im Menü von Device Provisioning Service die Option **Registrierungen verwalten** aus. Wählen Sie oben die Schaltfläche **Individuelle Registrierung hinzufügen**.

1. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:

   * Wählen Sie **TPM** als Mechanismus für den Nachweis der Identität aus.
   * Geben Sie die Registrierungs-ID und den Endorsement Key für Ihr TPM-Gerät aus den Werten ein, die Sie sich zuvor notiert haben.
   * Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
   * Geben Sie optional eine eindeutige Geräte-ID ein. Wenn Sie keine Angabe machen, wird das Gerät stattdessen über die Registrierungs-ID identifiziert.
   * Wählen Sie **True** aus, um anzugeben, dass diese Registrierung für ein IoT Edge-Gerät vorgesehen ist.

1. Wählen Sie **Speichern** aus, um Ihre individuelle Registrierung zu speichern.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurieren des Geräts mit Bereitstellungsinformationen

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öffnen Sie auf dem Windows-Gerät eine PowerShell-Sitzung mit erhöhten Rechten.

1. Stellen Sie Ihr Gerät unter Angabe der **Bereichs-ID** bereit, die Sie aus Ihrer Instanz des Device Provisioning-Diensts erfasst haben.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Stellen Sie Ihr Gerät im Schritt **Verbinden** bereit.

   1. Wählen Sie die Bereitstellungsmethode **DpsTpm** aus.
   1. Geben Sie die **Bereichs-ID** an, die Sie aus Ihrer Instanz des Device Provisioning-Diensts abrufen.

   ![Stellen Sie Ihr Gerät mit DPS und TPM-Nachweis zur Verfügung.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Wählen Sie **Mit der ausgewählten Methode bereitstellen** aus.

1. Sobald IoT Edge auf Ihrem Gerät erfolgreich installiert und bereitgestellt wurde, wählen Sie **Fertig stellen** aus, um den Bereitstellungs-Assistenten zu beenden.

---

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

1. Stellen Sie mithilfe des folgenden Befehls in Ihrer PowerShell-Sitzung eine Verbindung mit Ihrem virtuellen IoT Edge-Computer für Linux unter Windows her:

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

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure-Befehlszeilenschnittstelle](how-to-deploy-cli-at-scale.md)
