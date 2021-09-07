---
title: Installieren von Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Anweisungen zur Installation von Azure IoT Edge auf Windows-Geräten
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4720056254c3983f8b63c7ed2c46d55e4eabc7d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338849"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device"></a>Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden: von der einfachen PC-Klasse bis zu Industrieservern. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen. Weitere Informationen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Azure IoT Edge für Linux unter Windows ermöglicht Ihnen die Installation von IoT Edge auf Linux-VMs, die auf Windows-Geräten ausgeführt werden. Die Linux-Version von Azure IoT Edge und alle darin bereitgestellten Linux-Module werden auf der VM ausgeführt. Von dort aus können Windows-Anwendungen und Windows-Code sowie die IoT Edge-Runtime und Module frei miteinander interagieren.

In diesem Artikel werden die Schritte zum Einrichten von IoT Edge auf einem Windows-Gerät beschrieben. Mit diesen Schritten wird eine Linux-VM mit der IoT Edge-Runtime für die Ausführung auf Ihrem Windows-Gerät bereitgestellt. Anschließend wird das Gerät mit seiner IoT Hub-Geräteidentität bereitgestellt.

>[!NOTE]
>IoT Edge für Linux unter Windows ist die empfohlene Umgebung für die Verwendung von Azure IoT Edge in einer Windows-Umgebung. Windows-Container sind aber weiterhin verfügbar. Wenn Sie lieber Windows-Container verwenden möchten, finden Sie weitere Informationen unter [Installieren und Verwalten von Azure IoT Edge mit Windows-Containern](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem gültigen Abonnement. Wenn Sie kein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

* Eine [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) in Azure mit dem Tarif „Free“ oder „Standard“.

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

* Wenn Sie ein IoT Edge-Gerät mithilfe von Windows Admin Center installieren und verwalten möchten, stellen Sie sicher, dass Sie Zugriff auf Windows Admin Center besitzen und die Azure IoT Edge-Erweiterung installiert haben:

   1. Laden Sie das [Windows Admin Center-Installationsprogramm](https://aka.ms/wacdownload) herunter, und führen Sie es aus. Befolgen Sie die Anweisungen im Installations-Assistenten, um Windows Admin Center zu installieren.

   1. Verwenden Sie nach der Installation einen unterstützten Browser, um das Windows Admin Center zu öffnen. Zu den unterstützten Browsern gehören Microsoft Edge (Windows 10, Version 1709 oder höher), Google Chrome und Microsoft Edge Insider.

   1. Bei der ersten Verwendung von Windows Admin Center werden Sie aufgefordert, ein Zertifikat auszuwählen. Wählen Sie als Zertifikat **Windows Admin Center-Client** aus.

   1. Installieren Sie die Azure IoT Edge-Erweiterung. Wählen Sie das Zahnradsymbol in der rechten oberen Ecke des Windows Admin Center-Dashboards aus.

      ![Auswählen des Zahnradsymbols in der rechten oberen Ecke des Dashboards zum Öffnen der Einstellungen](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Wählen Sie im Menü **Einstellungen** unter **Gateway** die Option **Erweiterungen** aus.

   1. Suchen Sie auf der Registerkarte **Verfügbare Erweiterungen** in der Liste der Erweiterungen nach **Azure IoT Edge**. Markieren Sie diese Option, und wählen Sie über der Liste der Erweiterungen die Aufforderung **Installieren** aus.

   1. Nach Abschluss der Installation sollte Azure IoT Edge in der Liste der installierten Erweiterungen auf der Registerkarte **Installierte Erweiterungen** angezeigt werden.

* Wenn Sie **GPU-beschleunigte Linux-Module** in Ihrer Azure IoT Edge für Linux unter Windows-Bereitstellung verwenden möchten, gibt es mehrere Konfigurationsoptionen zu berücksichtigen. Abhängig von Ihrer GPU-Architektur müssen Sie die richtigen Treiber installieren, und Sie benötigen möglicherweise Zugriff auf einen Windows-Insider-Build. Informationen zum Ermitteln Ihrer Konfigurationsanforderungen und zum Erfüllen dieser Voraussetzungen finden Sie unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md).

## <a name="choose-your-provisioning-method"></a>Auswählen der Bereitstellungsmethode

Azure IoT Edge für Linux unter Windows unterstützt die folgenden Bereitstellungsmethoden:

* **Manuelle Bereitstellung** für ein einzelnes Gerät.

  * Befolgen Sie zum Vorbereiten der manuellen Bereitstellung die Schritte unter [Registrieren eines IoT Edge-Geräts in IoT Hub](how-to-register-device.md). Wählen Sie entweder die Authentifizierung mit symmetrischem Schlüssel oder die X.509-Zertifikatauthentifizierung aus, und kehren Sie dann zu diesem Artikel zurück, um IoT Edge zu installieren und bereitzustellen.

* **Automatische Bereitstellung** mithilfe des IoT Hub Device Provisioning Service (DPS) für ein oder mehrere Geräte.

  * Wählen Sie die gewünschte Authentifizierungsmethode aus, und befolgen Sie dann die Schritte im entsprechenden Artikel, um eine Instanz von DPS und eine Registrierung zum Bereitstellen Ihrer Geräte zu erstellen. Weitere Informationen zu den Registrierungstypen finden Sie unter den [Konzepten von Azure IoT Hub Device Provisioning Service](../iot-dps/concepts-service.md#enrollment).

    * [Bereitstellen eines IoT Edge-Geräts mit DPS und symmetrischen Schlüsseln](how-to-auto-provision-symmetric-keys.md)
    * [Bereitstellen eines IoT Edge-Geräts mit DPS und X.509-Zertifikaten](how-to-auto-provision-x509-certs.md)
    * [Bereitstellen eines IoT Edge-Geräts mit DPS und TPM-Nachweis](how-to-auto-provision-tpm-linux-on-windows.md)

## <a name="create-a-new-deployment"></a>Erstellen einer neuen Bereitstellung

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

1. Legen Sie die Ausführungsrichtlinie für das Zielgerät auf `AllSigned` fest, sofern nicht bereits geschehen. Sie können die aktuelle Ausführungsrichtlinie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten wie folgt überprüfen:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` ist, können Sie die Ausführungsrichtlinie wie folgt festlegen:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Erstellen Sie die Bereitstellung von IoT Edge für Linux unter Windows.

   ```powershell
   Deploy-Eflow
   ```

   Der Befehl `Deploy-Eflow` verwendet optionale Parameter, mit denen Sie Ihre Bereitstellung anpassen können.

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md) aufgeführt werden.

   Zur Verwendung von GPU-Passthrough müssen Sie Ihrem Befehl `Deploy-Eflow` die Parameter **gpuName**, **gpuPassthroughType** und **gpuCount** hinzufügen. Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).



1. Geben Sie „Y“ ein, um den Lizenzbedingungen zuzustimmen.

1. Durch die Eingabe von „O“ oder „R“ können Sie **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Sobald die Bereitstellung abgeschlossen ist, meldet das PowerShell-Fenster **Bereitstellung erfolgreich**.

   ![Bei einer erfolgreichen Bereitstellung wird am Ende der Meldungen „Deployment successful“ (Bereitstellung erfolgreich) angezeigt.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Die Azure IoT Edge-Erweiterung für Windows Admin Center befindet sich derzeit in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Installations- und Verwaltungsprozesse können sich von denen bei allgemeiner Verfügbarkeit unterscheiden.

Auf der Startseite von Windows Admin Center wird in der Liste der Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.

Sie können mit Windows Admin Center Azure IoT Edge für Linux unter Windows auf Ihrem lokalen Gerät und auf Remotegeräten installieren und verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows.

Wenn Sie die Bereitstellung nicht auf dem lokalen Gerät, sondern auf einem Remotezielgerät ausführen möchten, und das gewünschte Zielgerät nicht in der Liste angezeigt wird, befolgen Sie die Anweisungen zum [Hinzufügen Ihres Zielgeräts](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Anfängliches Windows Admin Center-Dashboard mit aufgeführtem Zielgerät](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Wählen Sie **Hinzufügen**.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

   Wenn Sie bereits über eine Instanz von IoT Edge für Linux unter Windows verfügen, die auf einem Gerät ausgeführt wird, können Sie **Hinzufügen** auswählen, um eine Verbindung mit diesem IoT Edge-Gerät herzustellen und es mit Windows Admin Center zu verwalten.

   ![Auswählen von „Neu erstellen“ auf der Kachel „Azure IoT Edge“ in Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Der Bereich **Create an Azure IoT Edge for Linux on Windows deployment** (Azure IoT Edge für Linux unter Windows-Bereitstellung erstellen) wird geöffnet. Überprüfen Sie auf der Registerkarte **Erste Schritte** die Mindestanforderungen, und wählen Sie **Weiter** aus.

1. Lesen Sie die Lizenzbedingungen, aktivieren Sie **Ich stimme zu**, und wählen Sie **Weiter** aus.

1. Sie können **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Klicken Sie auf **Weiter: Bereitstellen**.

   ![Auswählen der Schaltfläche „Weiter: Bereitstellen“ nach dem Umschalten von „Optionale Diagnosedaten“](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Klicken Sie auf der Registerkarte **2. Bereitstellen** unter **Zielgerät auswählen** auf das aufgeführte Gerät, um zu überprüfen, ob es die Mindestanforderungen erfüllt. Wenn als Status „Unterstützt“ angezeigt wird, wählen Sie **Weiter** aus.

   ![Auswählen des Geräts zur Überprüfung der Unterstützung](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Überprüfen Sie auf der Registerkarte **2.2 Einstellungen** die Konfigurationseinstellungen der Bereitstellung.

   >[!NOTE]
   >IoT Edge Für Linux unter Windows verwendet einen Standardswitch, über den der Linux-VM eine interne IP-Adresse zugewiesen wird. Diese interne IP-Adresse ist von außerhalb des virtuellen Windows-Computers nicht erreichbar. Sie können lokal eine Verbindung mit der VM herstellen, während Sie am Windows-Computer angemeldet sind.
   >
   >Wenn Sie Windows Server verwenden, richten Sie einen Standardswitch ein, bevor Sie IoT Edge für Linux unter Windows bereitstellen.

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](gpu-acceleration.md) aufgeführt werden. Wenn Sie diese erforderlichen Komponenten erst zu diesem Zeitpunkt im Bereitstellungsprozess installieren, müssen Sie von vorn beginnen.

   Abhängig vom GPU-Adapter, den Sie Ihrer Bereitstellung zuweisen, stehen zwei Optionen für das GPU-Passthrough zur Verfügung: **Diskrete Gerätezuweisung (Discrete Device Assignment, DDA)** und **GPU-Paravirtualisierung (GPU-PV)** . Im Folgenden finden Sie Beispiele für jede Methode.

   Für die diskrete Gerätezuweisung wählen Sie die Anzahl von GPU-Kernen aus, die Ihrer Linux-VM zugeordnet werden sollen.

   ![Konfigurationseinstellungen mit aktivierter GPU für die diskrete Gerätezuweisung](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-direct-device-assignment.png)

   Für die Paravirtualisierungsmethode sind keine zusätzlichen Einstellungen erforderlich.

   ![Konfigurationseinstellungen mit aktivierter GPU für die Paravirtualisierung](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Weiter** aus.

1. Auf der Registerkarte **2.3 Bereitstellung** können Sie den Fortschritt der Bereitstellung überwachen. Der gesamte Prozess umfasst das Herunterladen und Installieren des Pakets von Azure IoT Edge für Linux unter Windows, das Konfigurieren des Hostgeräts und das Einrichten der Linux-VM. Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung werden für jeden Schritt ein grünes Häkchen und die Bezeichnung „Erledigt“ angezeigt.](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen. Klicken Sie auf **Weiter: Verbinden**, um zur Registerkarte **3. Verbinden** zu wechseln, auf der die Bereitstellung des Azure IoT Edge-Geräts durchgeführt wird.

---

## <a name="provision-your-device"></a>Bereitstellen Ihres Geräts

Wählen Sie eine Methode für die Bereitstellung Ihres Geräts aus, und befolgen Sie die Anweisungen im zugehörigen Abschnitt. Dieser Artikel enthält die Schritte zum manuellen Bereitstellen Ihres Geräts mit symmetrischen Schlüsseln oder X.509-Zertifikaten. Wenn Sie die automatische Bereitstellung mit DPS verwenden, folgen Sie den entsprechenden Links, um die Bereitstellung abzuschließen.

Sie können für das Bereitstellen Ihrer Geräte Windows Admin Center oder eine PowerShell-Sitzung mit erhöhten Rechten verwenden.

* Manuelle Bereitstellung:

  * [Manuelle Bereitstellung mit der Verbindungszeichenfolge Ihres IoT Edge-Geräts](#manual-provisioning-using-the-connection-string)
  * [Manuelle Bereitstellung mithilfe von X.509-Zertifikaten](#manual-provisioning-using-x509-certificates)

* Automatische Bereitstellung:

  * [Automatische Bereitstellung mit Device Provisioning Service (DPS) und symmetrischen Schlüsseln](how-to-auto-provision-symmetric-keys.md?tabs=eflow#configure-the-device-with-provisioning-information)
  * [Automatische Bereitstellung mithilfe von DPS und X.509-Zertifikaten](how-to-auto-provision-x509-certs.md?tabs=eflow#configure-the-device-with-provisioning-information)
  * [Automatische Bereitstellung mithilfe von DPS und TPM-Nachweis](how-to-auto-provision-tpm-linux-on-windows.md#configure-the-device-with-provisioning-information)

### <a name="manual-provisioning-using-the-connection-string"></a>Manuelle Bereitstellung mit der Verbindungszeichenfolge

In diesem Abschnitt wird das manuelle Bereitstellen Ihres Geräts mithilfe der Verbindungszeichenfolge Ihres Azure IoT Edge-Geräts beschrieben.

Sofern nicht bereits geschehen, befolgen Sie die Schritte unter [Registrieren eines IoT Edge-Geräts in IoT Hub](how-to-register-device.md), um Ihr Gerät zu registrieren und die zugehörige Verbindungszeichenfolge abzurufen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie den folgenden Befehl mit erhöhten Rechten in einer PowerShell-Sitzung auf Ihrem Zielgerät aus. Ersetzen Sie den Platzhaltertext durch Ihre eigenen Werte.

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

Weitere Informationen zum Befehl `Provision-EflowVM` finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **Verbindungszeichenfolge (manuell)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zur Registerkarte **IoT Edge** Ihres IoT-Hubs.

1. Klicken Sie auf die Geräte-ID Ihres Geräts. Kopieren Sie den Wert im Feld **Primäre Verbindungszeichenfolge**.

1. Geben Sie die **Geräteverbindungszeichenfolge** an, die Sie nach dem Registrieren des Geräts aus IoT Hub abgerufen haben.

1. Wählen Sie die Option **Mit der ausgewählten Methode bereitstellen** aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Einfügen der Verbindungszeichenfolge](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät vom Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

---

### <a name="manual-provisioning-using-x509-certificates"></a>Manuelle Bereitstellung mithilfe von X.509-Zertifikaten

In diesem Abschnitt wird das manuelle Bereitstellen Ihres Geräts mithilfe von X.509-Zertifikaten für Ihr IoT Edge-Gerät beschrieben.

Sofern nicht bereits geschehen, befolgen Sie die Schritte unter [Registrieren eines IoT Edge-Geräts in IoT Hub](how-to-register-device.md), um die benötigten Zertifikate vorzubereiten und Ihr Gerät zu registrieren. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Halten Sie das Geräteidentitätszertifikat und den zugehörigen privaten Schlüssel für Ihr Zielgerät bereit. Sie müssen den absoluten Pfad zu beiden Dateien kennen.

Führen Sie den folgenden Befehl mit erhöhten Rechten in einer PowerShell-Sitzung auf Ihrem Zielgerät aus. Ersetzen Sie den Platzhaltertext durch Ihre eigenen Werte.

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

Weitere Informationen zum Befehl `Provision-EflowVM` finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden die Option **ManualX509** aus.

   ![Auswahl der manuellen Bereitstellung mit X.509-Zertifikaten](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-manual-x509.png)

1. Geben Sie die erforderlichen Parameter an:

   * **IoT Hub-Hostname**: Der Name des IoT Hubs, bei dem dieses Gerät registriert ist.
   * **Geräte-ID**: Der Name, mit dem dieses Gerät registriert ist.
   * **Zertifikatdatei**: Laden Sie das Geräteidentitätszertifikat hoch, das auf die VM verschoben und zum Bereitstellen des Geräts verwendet wird.
   * **Datei mit privatem Schlüssel**: Laden Sie die Datei mit dem zugehörigen privaten Schlüssel hoch, die auf die VM verschoben und zum Bereitstellen des Geräts verwendet wird.

1. Wählen Sie die Option **Mit der ausgewählten Methode bereitstellen** aus.

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

    1. Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Verwenden Sie das Tool `check`, um die Konfiguration und den Verbindungsstatus des Geräts zu überprüfen.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

> [!NOTE]
> Wenn Sie öffentliche PowerShell-Funktionen für IoT Edge für Linux unter Windows verwenden, müssen Sie die Ausführungsrichtlinie für das Zielgerät auf `AllSigned` festlegen. Stellen Sie sicher, dass alle Voraussetzungen für [PowerShell-Funktionen für IoT Edge für Linux unter Windows](reference-iot-edge-for-linux-on-windows-functions.md) erfüllt sind.

1. Wählen Sie Ihr IoT Edge-Gerät in der Liste der verbundenen Geräte in Windows Admin Center aus, um eine Verbindung damit herzustellen.

1. Auf der Übersichtsseite des Geräts werden Informationen zum Gerät angezeigt:

   * Im Abschnitt mit der **Liste der IoT Edge-Module** werden die auf dem Gerät ausgeführten Module angezeigt. Wenn der IoT Edge-Dienst zum ersten Mal gestartet wird, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.

   * Im Abschnitt **IoT Edge-Status** wird der Dienststatus angezeigt. Dieser sollte **Aktiv (wird ausgeführt)** lauten.

---

## <a name="next-steps"></a>Nächste Schritte

* Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
* Erfahren Sie, wie Sie [Zertifikate auf Ihrem IoT Edge für Linux auf einem virtuellen Windows-Computer verwalten](how-to-manage-device-certificates.md) und Dateien vom Host-Betriebssystem auf Ihren virtuellen Linux-Computer übertragen.
* Erfahren Sie, wie [Sie Ihre IoT Edge-Geräte für die Kommunikation über einen Proxy-Server konfigurieren](how-to-configure-proxy-support.md).
