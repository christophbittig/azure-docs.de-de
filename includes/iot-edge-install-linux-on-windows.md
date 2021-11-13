---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890ec787bfc7a4316df9d27271460a90d5047175
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842683"
---
## <a name="install-iot-edge"></a>Installieren von IoT Edge

Stellen Sie Azure IoT Edge für Linux unter Windows auf Ihrem Zielgerät bereit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Zielgerät.

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
   >Mit dem folgenden Befehl wird beispielsweise ein VM mit 4 vCPU-Kernen, 4 GB RAM (angegeben in MB) und 20 GB Speicherplatz erstellt:
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](../articles/iot-edge/gpu-acceleration.md) aufgeführt werden.

   Wenn Sie GPU-Passthrough verwenden möchten, müssen Sie Ihrem Befehl `Deploy-Eflow` die Parameter **gpuName**, **gpuPassthroughType** und **gpuCount** hinzufügen. Informationen zu allen verfügbaren optionalen Parametern finden Sie unter [PowerShell-Funktionen für IoT Edge für Linux unter Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Geben Sie „Y“ ein, um den Lizenzbedingungen zuzustimmen.

1. Durch die Eingabe von „O“ oder „R“ können Sie **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Sobald die Bereitstellung abgeschlossen ist, meldet das PowerShell-Fenster **Bereitstellung erfolgreich**.

   ![Bei einer erfolgreichen Bereitstellung wird am Ende der Meldungen „Bereitstellung erfolgreich“ angezeigt (PNG).](./media/iot-edge-install-linux-on-windows/successful-powershell-deployment.png)

   Nach einer erfolgreichen Bereitstellung können Sie Ihr Gerät bereitstellen.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Die Azure IoT Edge-Erweiterung für Windows Admin Center befindet sich zurzeit in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Installations- und Verwaltungsprozesse können sich von denen bei allgemeiner Verfügbarkeit unterscheiden.

Installieren Sie Azure IoT Edge für Linux unter Windows auf Ihrem Gerät.

1. Laden Sie das [Installationsprogramm für Azure IoT Edge für Linux unter Windows](https://aka.ms/AzEflowMSI) herunter.

1. Nachdem Sie Azure IoT Edge für Linux unter Windows installiert haben, öffnen Sie Windows Admin Center.

   Auf der Startseite von Windows Admin Center wird in der Liste der Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.

   Sie können mit Windows Admin Center Azure IoT Edge für Linux unter Windows auf Ihrem lokalen Gerät und auf Remotegeräten installieren und verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows.

1. Vergewissern Sie sich, dass Ihr lokales Gerät, wie unten gezeigt, unter **Alle Verbindungen** aufgeführt wird.

   ![Anfängliches Windows Admin Center-Dashboard mit aufgeführtem Zielgerät, PNG.](./media/iot-edge-install-linux-on-windows/windows-admin-center-initial-dashboard.png)

   Wenn Sie die Bereitstellung nicht auf Ihrem lokalen Gerät, sondern auf einem Remotezielgerät durchführen möchten und das gewünschte Zielgerät in der Liste nicht angezeigt wird, führen Sie die [Anleitungen zum Hinzufügen Ihres Zielgeräts](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters) aus.

1. Wählen Sie **+ Hinzufügen** aus, um mit dem Erstellen Ihrer Bereitstellung zu beginnen. Die Bereitstellung erstellt Ihren virtuellen Linux-Computer und installiert die IoT Edge Runtime automatisch.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

   Wenn Sie bereits über eine Instanz von IoT Edge für Linux unter Windows verfügen, die auf einem Gerät ausgeführt wird, können Sie **Hinzufügen** auswählen, um eine Verbindung mit diesem IoT Edge-Gerät herzustellen und es mit Windows Admin Center zu verwalten.

   ![Auswählen von „Neu erstellen“ auf der Kachel „Azure IoT Edge“ in Windows Admin Center (PNG).](./media/iot-edge-install-linux-on-windows/resource-creation-tiles.png)

1. Der Bereich **Create an Azure IoT Edge for Linux on Windows deployment** (Azure IoT Edge für Linux unter Windows-Bereitstellung erstellen) wird geöffnet. Überprüfen Sie auf der Registerkarte **Erste Schritte** die Mindestanforderungen, und wählen Sie **Weiter** aus.

1. Lesen Sie die Lizenzbedingungen, aktivieren Sie **Ich stimme zu**, und wählen Sie **Weiter** aus.

1. Sie können **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Klicken Sie auf **Weiter: Bereitstellen**.

   ![Auswählen der Schaltfläche „Weiter: Bereitstellen“ nach dem Umschalten von „Optionale Diagnosedaten“ (PNG).](./media/iot-edge-install-linux-on-windows/select-next-deploy.png)

1. Klicken Sie auf der Registerkarte **2. Bereitstellen** unter **Zielgerät auswählen** auf das aufgeführte Gerät, um zu überprüfen, ob es die Mindestanforderungen erfüllt. Wenn als Status „Unterstützt“ angezeigt wird, wählen Sie **Weiter** aus.

   ![Auswählen des Geräts zur Überprüfung der Unterstützung (PNG).](./media/iot-edge-install-linux-on-windows/evaluate-supported-device.png)

1. Überprüfen Sie auf der Registerkarte **2.2 Einstellungen** die Konfigurationseinstellungen der Bereitstellung. Die folgende Tabelle zeigt die Einstellungen und deren Standardwerte.

   | Einstellung | Standardwert |
   | ------- | ------------- |
   | Größe des virtuellen Datenträgers in GB | 16 |
   | Arbeitsspeicher in MB | 1024 |
   | Anzahl von Kernen | 2 |
   | Azure IoT Edge-Version | 1.1 (LTS) |

   >[!NOTE]
   >IoT Edge Für Linux unter Windows verwendet einen Standardswitch, über den der Linux-VM eine interne IP-Adresse zugewiesen wird. Diese interne IP-Adresse ist von außerhalb des virtuellen Windows-Computers nicht erreichbar. Sie können lokal eine Verbindung mit der VM herstellen, während Sie am Windows-Computer angemeldet sind.
   >
   >Wenn Sie Windows Server verwenden, [richten Sie einen Standardswitch ein](../articles/iot-edge/how-to-create-virtual-switch.md), bevor Sie IoT Edge für Linux unter Windows bereitstellen.

   Sie können Ihrer Bereitstellung eine GPU zuweisen, um GPU-beschleunigte Linux-Module zu aktivieren. Um Zugriff auf diese Features zu erhalten, müssen Sie die erforderlichen Komponenten installieren, die unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](../articles/iot-edge/gpu-acceleration.md) aufgeführt werden. Wenn Sie diese erforderlichen Komponenten erst zu diesem Zeitpunkt im Bereitstellungsprozess installieren, müssen Sie von vorn beginnen.

   Abhängig vom GPU-Adapter, den Sie Ihrer Bereitstellung zuweisen, stehen zwei Optionen für das GPU-Passthrough zur Verfügung: **Diskrete Gerätezuweisung (Discrete Device Assignment, DDA)** und **GPU-Paravirtualisierung (GPU-PV)** .

   Für die diskrete Gerätezuweisung wählen Sie die Anzahl von GPU-Kernen aus, die Ihrer Linux-VM zugeordnet werden sollen.

   Für die Paravirtualisierungsmethode sind keine zusätzlichen Einstellungen erforderlich.

   >[!WARNING]
   >Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Weiter** aus.

1. Auf der Registerkarte **2.3 Bereitstellung** können Sie den Fortschritt der Bereitstellung überwachen. Der gesamte Prozess umfasst das Herunterladen und Installieren des Pakets von Azure IoT Edge für Linux unter Windows, das Konfigurieren des Hostgeräts und das Einrichten der Linux-VM. Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung werden für jeden Schritt ein grünes Häkchen und die Bezeichnung „Erledigt“ angezeigt (PNG).](./media/iot-edge-install-linux-on-windows/successful-deployment.png)

1. Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen. Klicken Sie auf **Weiter: Verbinden**, um zur Registerkarte **3. Verbinden** zu wechseln, auf der die Bereitstellung des Azure IoT Edge-Geräts durchgeführt wird.

---
