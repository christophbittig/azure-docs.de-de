---
title: Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe eines TPMs – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines simulierten TPMs auf einem Linux unter Windows-Gerät, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661799"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>Bedarfsgerechtes Erstellen und Bereitstellen eines IoT Edge für Linux unter Windows-Geräts mithilfe eines TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält vollständige Anleitungen für die automatische Bereitstellung eines IoT Edge für Linux unter Windows-Geräts mithilfe eines TPMs (Trusted Platform Module). Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) automatisch bereitstellen. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

In diesem Artikel werden zwei Methodiken beschrieben. Wählen Sie Ihre Präferenz basierend auf der Architektur Ihrer Lösung aus:

1. Automatische Bereitstellung eines Linux unter Windows-Geräts mit physischer TPM-Hardware.
1. Automatische Bereitstellung eines Linux unter Windows-Geräts mithilfe eines simulierten TPMs. Diese Methodik wird nur als Testszenario empfohlen, weil ein simuliertes TPM nicht dieselbe Sicherheit wie ein physisches TPM bietet.

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

Cloudressourcen:

* Aktiver IoT Hub.
* Eine Instanz des IoT Hub Device Provisioning-Diensts in Azure, die mit Ihrem IoT-Hub verknüpft ist.
  * Wenn Sie nicht über eine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

Ein Entwicklungscomputer:

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

   > [!TIP]
   > Wenn Sie **GPU-beschleunigte Linux-Module** in Ihrer Azure IoT Edge für Linux unter Windows-Bereitstellung verwenden möchten, gibt es mehrere Konfigurationsoptionen zu berücksichtigen. Abhängig von Ihrer GPU-Architektur müssen Sie die richtigen Treiber installieren, und Sie benötigen möglicherweise Zugriff auf einen Windows-Insider-Build. Informationen zum Ermitteln Ihrer Konfigurationsanforderungen und zum Erfüllen dieser Voraussetzungen finden Sie unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md).

Sie können Ihre IoT Edge-Geräte mit **PowerShell** oder dem **Windows Admin Center** verwalten. Bei jedem Hilfsprogramm gibt es eigene Voraussetzungen:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie PowerShell verwenden möchten, führen Sie die folgenden Schritte aus, um Ihr Zielgerät für die Installation von Azure IoT Edge für Linux unter Windows und die Bereitstellung des virtuellen Linux-Computers vorzubereiten:

1. Legen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` fest. Sie können die aktuelle Ausführungsrichtlinie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten mit dem folgenden Befehl überprüfen:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` ist, können Sie die Ausführungsrichtlinie wie folgt festlegen:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Weitere Informationen zum PowerShell-Modul „Azure IoT Edge für Linux unter Windows“ finden Sie in der [PowerShell-Funktionsreferenz](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Wenn Sie Windows Admin Center verwenden möchten, führen Sie die folgenden Schritte zum Herunterladen und Installieren von Windows Admin Center sowie zum Installieren der Azure IoT Edge-Erweiterung für Windows Admin Center aus:

   1. Laden Sie das [Windows Admin Center-Installationsprogramm](https://aka.ms/wacdownload) herunter, und führen Sie es aus. Befolgen Sie die Anweisungen im Installations-Assistenten, um Windows Admin Center zu installieren.

   1. Verwenden Sie nach der Installation einen unterstützten Browser, um das Windows Admin Center zu öffnen. Zu den unterstützten Browsern gehören Microsoft Edge (Windows 10, Version 1709 oder höher), Google Chrome und Microsoft Edge Insider.

   1. Bei der ersten Verwendung von Windows Admin Center werden Sie aufgefordert, ein Zertifikat auszuwählen. Wählen Sie als Zertifikat **Windows Admin Center-Client** aus.

   1. Installieren Sie die Azure IoT Edge-Erweiterung. Wählen Sie das Zahnradsymbol in der rechten oberen Ecke des Windows Admin Center-Dashboards aus.

      ![Auswählen des Zahnradsymbols in der rechten oberen Ecke des Dashboards zum Öffnen der Einstellungen (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. Wählen Sie im Menü **Einstellungen** unter **Gateway** die Option **Erweiterungen** aus.

   1. Suchen Sie auf der Registerkarte **Verfügbare Erweiterungen** in der Liste der Erweiterungen nach **Azure IoT Edge**. Markieren Sie diese Option, und wählen Sie über der Liste der Erweiterungen die Aufforderung **Installieren** aus.

   1. Nach Abschluss der Installation sollte Azure IoT Edge in der Liste der installierten Erweiterungen auf der Registerkarte **Installierte Erweiterungen** angezeigt werden.

---

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird.
>
> Sie können nur einzelne DPS-Registrierungen (keine Gruppe) erstellen, wenn Sie ein TPM verwenden.

## <a name="install-iot-edge"></a>Installieren von IoT Edge

Stellen Sie Azure IoT Edge für Linux unter Windows auf Ihrem Zielgerät bereit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie Azure IoT Edge für Linux unter Windows auf Ihrem Zielgerät.

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

1. Erstellen Sie die Bereitstellung von IoT Edge für Linux unter Windows. Die Bereitstellung erstellt Ihren virtuellen Linux-Computer und installiert die IoT Edge Runtime automatisch.

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

   Wenn Sie GPU-Passthrough verwenden möchten, müssen Sie Ihrem Befehl `Deploy-Eflow` die Parameter **gpuName**, **gpuPassthroughType** und **gpuCount** hinzufügen. Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Geben Sie „Y“ ein, um den Lizenzbedingungen zuzustimmen.

1. Durch die Eingabe von „O“ oder „R“ können Sie **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Sobald die Bereitstellung abgeschlossen ist, meldet das PowerShell-Fenster **Bereitstellung erfolgreich**.

   ![Bei einer erfolgreichen Bereitstellung wird am Ende der Meldungen „Bereitstellung erfolgreich“ angezeigt (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Die Azure IoT Edge-Erweiterung für Windows Admin Center befindet sich zurzeit in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Installations- und Verwaltungsprozesse können sich von denen bei allgemeiner Verfügbarkeit unterscheiden.

Installieren Sie Azure IoT Edge für Linux unter Windows auf Ihrem Gerät.

1. Laden Sie das [Installationsprogramm für Azure IoT Edge für Linux unter Windows](https://aka.ms/AzEflowMSI) herunter.

1. Nachdem Sie Azure IoT Edge für Linux unter Windows installiert haben, öffnen Sie Windows Admin Center.

   Auf der Startseite von Windows Admin Center wird in der Liste der Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.

   Sie können mit Windows Admin Center Azure IoT Edge für Linux unter Windows auf Ihrem lokalen Gerät und auf Remotegeräten installieren und verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows.

1. Vergewissern Sie sich, dass Ihr lokales Gerät, wie unten gezeigt, unter **Alle Verbindungen** aufgeführt wird.

   ![Anfängliches Windows Admin Center-Dashboard mit aufgeführtem Zielgerät, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   Wenn Sie die Bereitstellung nicht auf Ihrem lokalen Gerät, sondern auf einem Remotezielgerät durchführen möchten und das gewünschte Zielgerät in der Liste nicht angezeigt wird, führen Sie die [Anleitungen zum Hinzufügen Ihres Zielgeräts](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters) aus.

1. Wählen Sie **+ Hinzufügen** aus, um mit dem Erstellen Ihrer Bereitstellung zu beginnen. Die Bereitstellung erstellt Ihren virtuellen Linux-Computer und installiert die IoT Edge Runtime automatisch.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

   Wenn Sie bereits über eine Instanz von IoT Edge für Linux unter Windows verfügen, die auf einem Gerät ausgeführt wird, können Sie **Hinzufügen** auswählen, um eine Verbindung mit diesem IoT Edge-Gerät herzustellen und es mit Windows Admin Center zu verwalten.

   ![Auswählen von „Neu erstellen“ auf der Kachel „Azure IoT Edge“ in Windows Admin Center (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. Der Bereich **Create an Azure IoT Edge for Linux on Windows deployment** (Azure IoT Edge für Linux unter Windows-Bereitstellung erstellen) wird geöffnet. Überprüfen Sie auf der Registerkarte **Erste Schritte** die Mindestanforderungen, und wählen Sie **Weiter** aus.

1. Lesen Sie die Lizenzbedingungen, aktivieren Sie **Ich stimme zu**, und wählen Sie **Weiter** aus.

1. Sie können **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Klicken Sie auf **Weiter: Bereitstellen**.

   ![Auswählen der Schaltfläche „Weiter: Bereitstellen“ nach dem Umschalten von „Optionale Diagnosedaten“ (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. Klicken Sie auf der Registerkarte **2. Bereitstellen** unter **Zielgerät auswählen** auf das aufgeführte Gerät, um zu überprüfen, ob es die Mindestanforderungen erfüllt. Wenn als Status „Unterstützt“ angezeigt wird, wählen Sie **Weiter** aus.

   ![Auswählen des Geräts zur Überprüfung der Unterstützung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. Überprüfen Sie auf der Registerkarte **2.2 Einstellungen** die Konfigurationseinstellungen der Bereitstellung.

   >[!NOTE]
   >IoT Edge Für Linux unter Windows verwendet einen Standardswitch, über den der Linux-VM eine interne IP-Adresse zugewiesen wird. Diese interne IP-Adresse ist von außerhalb des virtuellen Windows-Computers nicht erreichbar. Sie können lokal eine Verbindung mit der VM herstellen, während Sie am Windows-Computer angemeldet sind.
   >
   >Wenn Sie Windows Server verwenden, [richten Sie einen Standardswitch ein](how-to-create-virtual-switch.md), bevor Sie IoT Edge für Linux unter Windows bereitstellen.

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md) aufgeführt werden. Wenn Sie diese erforderlichen Komponenten erst zu diesem Zeitpunkt im Bereitstellungsprozess installieren, müssen Sie von vorn beginnen.

   Abhängig vom GPU-Adapter, den Sie Ihrer Bereitstellung zuweisen, stehen zwei Optionen für das GPU-Passthrough zur Verfügung: **Diskrete Gerätezuweisung (Discrete Device Assignment, DDA)** und **GPU-Paravirtualisierung (GPU-PV)** . Im Folgenden finden Sie Beispiele für jede Methode.

   Für die diskrete Gerätezuweisung wählen Sie die Anzahl von GPU-Kernen aus, die Ihrer Linux-VM zugeordnet werden sollen.

   ![Konfigurationseinstellungen mit aktivierter GPU für die direkte Gerätezuweisung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   Für die Paravirtualisierungsmethode sind keine zusätzlichen Einstellungen erforderlich.

   ![Konfigurationseinstellungen mit aktivierter GPU für die Paravirtualisierung (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Weiter** aus.

1. Auf der Registerkarte **2.3 Bereitstellung** können Sie den Fortschritt der Bereitstellung überwachen. Der gesamte Prozess umfasst das Herunterladen und Installieren des Pakets von Azure IoT Edge für Linux unter Windows, das Konfigurieren des Hostgeräts und das Einrichten der Linux-VM. Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung werden für jeden Schritt ein grünes Häkchen und die Bezeichnung „Erledigt“ angezeigt (PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen. Klicken Sie auf **Weiter: Verbinden**, um zur Registerkarte **3. Verbinden** zu wechseln, auf der die Bereitstellung des Azure IoT Edge-Geräts durchgeführt wird.

Beenden Sie auf der Seite **Bereitstellung** die Arbeit mit dem Bereitstellungs-Assistenten. Es müssen nämlich noch einige Schritte zur Vorbereitung Ihres Geräts für die Bereitstellung mit TPM ausgeführt werden, bevor Sie weitermachen können. Lassen Sie das Windows Admin Center-Fenster geöffnet, da Sie am Ende dieses Artikels dorthin zurückkehren, um die Bereitstellungsschritte abzuschließen.

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

# <a name="physical-tpm"></a>[Physisches TPM](#tab/physical-tpm)

Zur Bereitstellung Ihres Geräts müssen Sie Informationen aus Ihrem TPM-Chip erfassen und für Ihre Instanz des Device Provisioning-Diensts (Device Provisioning Service, DPS) bereitstellen, damit der Dienst Ihr Gerät erkennen kann, wenn es eine Verbindung herzustellen versucht.

Zuerst müssen Sie den **Endorsement Key** ermitteln, der bei jedem TPM-Chip eindeutig ist und von dem ihm zugeordneten TPM-Chiphersteller abgerufen wird. Danach müssen Sie eine **Registrierungs-ID** für Ihr Gerät angeben. Sie können eine eindeutige Registrierungs-ID für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

IoT Edge für Linux unter Windows enthält ein PowerShell-Skript zum Abrufen dieser Informationen aus Ihrem TPM. Führen Sie zur Verwendung des Skripts die folgenden Schritte auf Ihrem Gerät aus:

1. Öffnen Sie PowerShell in einer Sitzung mit erhöhten Rechten.

1. Klonen Sie das Repository [iotedge-eflow](https://github.com/Azure/iotedge-eflow).

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. Importieren Sie das heruntergeladene Modul.

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. Führen Sie den Befehl aus.

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

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

Weil es jetzt eine Registrierung für dieses Gerät gibt, kann es von der IoT Edge-Runtime bereitgestellt werden.

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