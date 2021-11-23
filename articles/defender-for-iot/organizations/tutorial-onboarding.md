---
title: Setup der Testversion von Microsoft Defender für IoT
description: In diesem Tutorial erfahren Sie, wie Sie das Onboarding für Microsoft Defender für IoT mit einem virtuellen Sensor auf einem virtuellen Computer mit einem Testabonnement von Microsoft Defender für IoT durchführen.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 91e7d217861be41453669ea47525ed563200f54e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283635"
---
# <a name="tutorial-microsoft-defender-for-iot-trial-setup"></a>Tutorial: Setup der Testversion von Microsoft Defender für IoT

In diesem Tutorial erfahren Sie, wie Sie das Onboarding für Microsoft Defender für IoT mit einem virtuellen Sensor auf einem virtuellen Computer mit einem Testabonnement von Microsoft Defender für IoT durchführen. In diesem Tutorial wird das optimale Setup für Benutzer gezeigt, die Microsoft Defender für IoT testen möchten, bevor sie sich registrieren und die Lösung in ihre Umgebung integrieren.

Durch die Verwendung virtueller Umgebungen und der zum Erstellen eines Sensors erforderlichen Software ermöglicht Defender für IoT Ihnen Folgendes:

- Verwenden der passiven Netzwerküberwachung ohne Agent, um ohne jegliche Beeinträchtigung des IoT- und OT-Netzwerks den vollständigen Bestand aller IoT- und OT-Geräte, die zugehörigen Details und die Kommunikation untereinander zu ermitteln

- Identifizieren von Risiken und Sicherheitsrisiken in ihrer IoT- und OT-Umgebung, zum Beispiel nicht gepatchte Geräte, geöffnete Ports, nicht autorisierte Anwendungen und nicht autorisierte Verbindungen. Sie können auch Änderungen an Gerätekonfigurationen, am PLC-Code und der Firmware identifizieren.

- Erkennen abweichender oder nicht autorisierter Aktivitäten mit spezieller IoT- und OT-fähiger Threat Intelligence und Verhaltensanalyse. Sie können sogar komplexere Bedrohungen erkennen, die von statischen IoCs übersehen werden (z. B. Zero-Day-Malware, dateilose Schadsoftware und Living-off-the-Land-Taktiken).

- Durch die Integration in Microsoft Sentinel erhalten Sie einen Überblick über Ihre gesamte Organisation. Implementieren Sie durch die Integration in vorhandene Workflows (einschließlich Tools von Drittanbietern wie Splunk, IBM QRadar und ServiceNow) eine einheitliche IoT- und OT-Sicherheitsgovernance.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Durchführen des Onboardings mit Microsoft Defender für IoT
> * Herunterladen der ISO-Datei für den virtuellen Sensor
> * Erstellen eines virtuellen Computers für den Sensor
> * Installieren der Software für den virtuellen Sensor
> * Konfigurieren eines SPAN-Ports
> * Durchführen des Onboardings und Aktivieren des virtuellen Sensors

## <a name="prerequisites"></a>Voraussetzungen

- Berechtigungen: **Abonnementbesitzer** oder **Abonnementmitwirkende** in Azure

- Mindestens ein zu überwachendes Gerät, das mit einem SPAN-Port am Switch verbunden ist

- VMware (ESXi 5.5 oder höher) oder Hyper-V-Hypervisor (Windows 10 Pro oder Enterprise) wurde installiert und ist betriebsbereit.

- Ein Azure-Konto. Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

## <a name="onboard-with-microsoft-defender-for-iot"></a>Durchführen des Onboardings mit Microsoft Defender für IoT

Für den Einstieg in Microsoft Defender für IoT benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie über kein Abonnement verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

Zum Evaluieren von Defender für IoT können Sie ein Testabonnement verwenden. Die Testversion ist 30 Tage lang gültig und unterstützt bis zu 1.000 committete Geräte. Mit der Testversion können Sie einen virtuellen Sensor in Ihrem Netzwerk bereitstellen. Verwenden Sie die Sensoren, um u. a. Datenverkehr zu überwachen, Daten zu analysieren, Warnungen zu generieren und mehr über Netzwerkrisiken und Sicherheitsrisiken zu erfahren. Mit der Testversion können Sie auch eine virtuelle lokale Verwaltungskonsole bereitstellen, um die vom Sensor generierten aggregierten Informationen anzeigen zu können.

**So integrieren Sie ein Abonnement in Microsoft Defender für IoT**

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).

1. Suchen Sie nach **Microsoft Defender für IoT**, und wählen Sie den Eintrag aus.

1. Klicken Sie auf **Onboard subscription** (Onboarding für Abonnement durchführen).

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="Screenshot: Auswählen der Schaltfläche „Abonnement integrieren“ auf der Seite „Erste Schritte“":::

1. Wählen Sie auf der Preisseite die Option **Mit einer Testversion beginnen** aus.

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="Screenshot: Schaltfläche „Mit einer Testversion starten“ zum Öffnen des Fensters für die Testversion":::

1. Wählen Sie im Bereich „Onboarding von Testabonnement“ ein Abonnement und dann **Auswerten** aus.

1. Bestätigen Sie Ihre Auswertung.

## <a name="download-the-iso-for-the-virtual-sensor"></a>Herunterladen der ISO-Datei für den virtuellen Sensor

Für die virtuellen Geräte gelten Mindestspezifikationen, die sowohl für den Sensor als auch für die Verwaltungskonsole erforderlich sind. In der folgenden Tabelle sind die Spezifikationen aufgeführt, die je nach Umgebung für den Sensor erforderlich sind:

### <a name="virtual-sensor"></a>Virtueller Sensor

| type | Unternehmen | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Arbeitsspeicher | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

**So laden Sie die ISO-Datei für den virtuellen Sensor herunter:**

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).

1. Suchen Sie nach **Microsoft Defender für IoT**, und wählen Sie den Eintrag aus.

1. Wählen Sie auf der Seite „Erste Schritte“ die Registerkarte **Sensor** aus.

1. Wählen Sie **Download** aus.

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="Screenshot: Registerkarte „Sensor“. Auswählen von „Herunterladen“, um die ISO-Datei für den virtuellen Sensor herunterzuladen":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>Erstellen eines virtuellen Computers für den Sensor

Der virtuelle Sensor unterstützt sowohl VMware- als auch Hyper-V-Bereitstellungsoptionen. Bevor Sie mit der Installation beginnen, müssen die folgenden Elemente vorhanden sein:

- VMware (ESXi 5.5 oder höher) oder Hyper-V-Hypervisor (Windows 10 Pro oder Enterprise) installiert und betriebsbereit

- Verfügbare Hardwareressourcen für den virtuellen Computer

- ISO-Installationsdatei für den Microsoft Defender für IoT-Sensor

- Sorgen Sie dafür, dass der Hypervisor ausgeführt wird.

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>Erstellen des virtuellen Computers für den Sensor mit ESXi

**So erstellen Sie den virtuellen Computer für den Sensor mit ESXi:**

1. Melden Sie sich beim ESXi an, wählen Sie den relevanten **Datenspeicher** und dann **Datenspeicherbrowser** aus.

1. **Laden Sie das Image hoch**, und wählen Sie **Schließen** aus.

1. Wechseln Sie zu **Virtuelle Computer**, und wählen Sie **VM erstellen/registrieren** aus.

1. Wählen Sie **Neuen virtuellen Computer erstellen** und dann **Weiter** aus.

1. Fügen Sie einen Sensornamen hinzu, und wählen Sie:

   - Kompatibilität: **&lt;Neueste ESXi-Version&gt;** aus.

   - Gastbetriebssystemfamilie: **Linux**

   - Gastbetriebssystemversion: **Ubuntu Linux (64 Bit)**

1. Wählen Sie **Weiter** aus.

1. Wählen Sie den relevanten Datenspeicher und dann **Weiter** aus.

1. Ändern Sie die virtuellen Hardwareparameter entsprechend der erforderlichen [Architektur](#download-the-iso-for-the-virtual-sensor).

1. Wählen Sie für **CD/DVD-Laufwerk 1** die Option **Datenspeicher-ISO-Datei** und dann die zuvor hochgeladene ISO-Datei aus.

1. Klicken Sie auf **Weiter** > **Fertig stellen**.

1. Schalten Sie den virtuellen Computer ein, und öffnen Sie eine Konsole.

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>Erstellen eines virtuellen Computers für den Sensor mit Hyper-V

In diesem Verfahren wird beschrieben, wie Sie mithilfe von Hyper-V einen virtuellen Computer erstellen.

**So erstellen Sie einen virtuellen Computer mit Hyper-V:**

1. Erstellen Sie im Hyper-V-Manager einen virtuellen Datenträger.

1. Wählen Sie **format = VHDX** aus.

1. Wählen Sie **type = Dynamic Expanding** (Dynamisch erweiterbar) aus.

1. Geben Sie den Namen und Speicherort für die virtuelle Festplatte ein.

1. Geben Sie die erforderliche Größe (entsprechend der [Architektur](#download-the-iso-for-the-virtual-sensor)) ein.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Erstellen Sie im Menü **Aktionen** einen neuen virtuellen Computer.

1. Geben Sie einen Namen für den virtuellen Computer ein.

1. Wählen Sie **Generation angeben** > **Generation 1** aus.

1. Geben Sie die Speicherbelegung (entsprechend der [Architektur](#download-the-iso-for-the-virtual-sensor)) an, und aktivieren Sie das Kontrollkästchen für den dynamischen Arbeitsspeicher.

1. Konfigurieren Sie den Netzwerkadapter entsprechend Ihrer Server-Netzwerktopologie.

1. Verbinden Sie die zuvor erstellte VHDX-Datei mit dem virtuellen Computer.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Klicken Sie mit der rechten Maustaste auf den neuen virtuellen Computer, und wählen Sie **Einstellungen** aus.

1. Wählen Sie **Hardware hinzufügen** aus, und fügen Sie einen neuen Netzwerkadapter hinzu.

1. Wählen Sie den virtuellen Switch aus, der eine Verbindung mit dem Sensorverwaltungsnetzwerk herstellen wird.

1. Ordnen Sie CPU-Ressourcen (entsprechend der [Architektur](#download-the-iso-for-the-virtual-sensor)) zu.

1. Verbinden Sie das ISO-Image der Verwaltungskonsole mit einem virtuellen DVD-Laufwerk.

1. Starten Sie den virtuellen Computer.

1. Wählen Sie im Menü **Aktionen** die Option **Verbinden** aus, um die Softwareinstallation fortzusetzen.

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>Installieren der Software für den virtuellen Sensor mit ESXi oder Hyper-V

Sie können entweder ESXi oder Hyper-V verwenden, um die Software für den virtuellen Sensor zu installieren.

**So installieren Sie die Software auf dem virtuellen Sensor:**

1. Öffnen Sie die Konsole für virtuelle Computer.

1. Der virtuelle Computer wird aus dem ISO-Image gestartet, und der Bildschirm für die Sprachauswahl wird angezeigt. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie die erforderliche [Architektur](#download-the-iso-for-the-virtual-sensor) aus.

1. Definieren Sie das Applianceprofil und die Netzwerkeigenschaften:

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | Basierend auf der erforderlichen [Architektur](#download-the-iso-for-the-virtual-sensor) |
    | **Verwaltungsschnittstelle** | **ens192** |
    | **Netzwerkparameter (vom Kunden bereitgestellt)** | **IP-Adresse des Verwaltungsnetzwerks:** <br/>**Subnetzmaske:** <br>**Appliance-Hostname:** <br/>**DNS:** <br/>**Standardgateway:** <br/>**Eingabeschnittstellen:**|
    | **Bridge-Schnittstellen:** | Die Bridge-Schnittstelle muss nicht konfiguriert werden. Diese Option wird nur bei besonderen Anwendungsfällen verwendet. |

1. Geben Sie **Y** ein, um die Einstellungen zu akzeptieren.

1. Anmeldeinformationen werden automatisch generiert und angezeigt. Kopieren Sie den Benutzernamen und das Kennwort an einen sicheren Ort, weil sie für die Anmeldung und Verwaltung Ihres Geräts erforderlich sind. Benutzername und Kennwort werden nicht erneut angezeigt.

    - **Support**: Der Administrator für die Benutzerverwaltung.

    - **CyberX**: Die Entsprechung von „root“ für den Zugriff auf die Appliance.

1. Die Appliance wird neu gestartet.

1. Greifen Sie über die zuvor konfigurierte IP-Adresse auf den Sensor zu: `https://ip_address`.

### <a name="post-installation-validation"></a>Überprüfung nach der Installation

Zum Überprüfen der Installation einer physischen Appliance müssen Sie viele Tests durchführen.

Die Überprüfung steht für den Benutzer **Support** und den Benutzer **CyberX** zur Verfügung.

**So greifen Sie auf das Tool für die Nachprüfung zu:**

1. Melden Sie sich beim Sensor an.

1. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

1. Wählen Sie die Schaltfläche :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false"::: aus.

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="Screenshot: Systemintegritätsprüfung" lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

Für die Überprüfung nach der Installation müssen Sie Tests ausführen, um sicherzustellen, dass das System ausgeführt wird, dass Sie über die richtige Version verfügen und dass alle Eingabeschnittstellen, die während des Installationsprozesses konfiguriert wurden, ausgeführt werden.

**So überprüfen Sie, ob das System ausgeführt wird:**

1. Wählen Sie **Appliance** aus, und vergewissern Sie sich, dass in jeder Zeile `Running` und in der untersten Zeile `System is up` angezeigt wird.

1. Wählen Sie **Version** aus, und vergewissern Sie sich, dass die richtige Version angezeigt wird.

1. Wählen Sie **ifconfig** aus, um die Parameter für die physischen Schnittstellen der Appliance anzuzeigen, und stellen Sie sicher, dass sie korrekt sind.

## <a name="configure-a-span-port"></a>Konfigurieren eines SPAN-Ports

Ein virtueller Switch verfügt nicht über Spiegelungsfunktionen. Sie können jedoch den promisken Modus in einer Umgebung mit virtuellen Switches verwenden. Der promiske Modus ist ein Betriebsmodus sowie eine Sicherheits-, Überwachungs- und Verwaltungstechnik, die auf der Ebene des virtuellen Switches oder der Portgruppe definiert ist. Der promiske Modus ist standardmäßig deaktiviert. Bei Aktivierung des promisken Modus verwenden die Netzwerkschnittstellen des virtuellen Computers, die sich in derselben Portgruppe befinden, den promisken Modus, um den gesamten Netzwerkdatenverkehr anzuzeigen, der diesen virtuellen Switch durchläuft. Sie können eine Problemumgehung entweder mit ESXi oder Hyper-V implementieren.

:::image type="content" source="media/tutorial-onboarding/purdue-model.png" alt-text="Screenshot der Stelle in Ihrer Architektur, an der der Sensor platziert werden soll.":::

### <a name="configure-a-span-port-with-esxi"></a>Konfigurieren eines SPAN-Ports mit ESXi

**So konfigurieren Sie einen SPAN-Port mit ESXi:**

1. Öffnen Sie „vSwitch“-Eigenschaften.

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie **Virtueller Computer** > **Weiter** aus.

1. Fügen Sie die Netzwerkbezeichnung **SPAN-Netzwerk** ein, wählen Sie **VLAN-ID** > **Alle** und dann **Weiter** aus.

1. Wählen Sie **Fertig stellen** aus.

1. Wählen Sie **SPAN-Netzwerk** > **Bearbeiten* aus.

1. Wählen Sie **Sicherheit** aus, und überprüfen Sie, ob die Richtlinie für **Promisker Modus** auf den Modus **Akzeptieren** festgelegt wurde.

1. Wählen Sie **OK** und dann **Schließen** aus, um die vSwitch-Eigenschaften zu schließen.

1. Öffnen Sie die **XSense VM**-Eigenschaften.

1. Wählen Sie für **Netzwerkadapter 2** das **SPAN**-Netzwerk aus.

1. Klicken Sie auf **OK**.

1. Stellen Sie eine Verbindung mit dem Sensor her, und überprüfen Sie, ob die Spiegelung funktioniert.

### <a name="configure-a-span-port-with-hyper-v"></a>Konfigurieren eines SPAN-Ports mit Hyper-V

Bevor Sie beginnen, müssen Sie Folgendes sicherstellen:

- Es wird keine Instanz einer virtuellen Appliance ausgeführt.

- SPAN ist für den Datenport und nicht für den Verwaltungsport aktiviert.

- Die SPAN-Konfiguration des Datenports ist nicht mit einer IP-Adresse konfiguriert.

**So konfigurieren Sie einen SPAN-Port mit Hyper-V:**

1. Öffnen Sie den Manager für virtuelle Switches.

1. Wählen Sie in der Liste virtueller Switches **Neuer virtueller Netzwerkswitch** > **Extern** als Typ für den dedizierten übergreifenden Netzwerkadapter aus.

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="Screenshot: Auswählen von „Neuer virtueller Netzwerkswitch“ und „Extern“ vor dem Erstellen des virtuellen Switches":::

1. Wählen Sie **Virtuellen Switch erstellen** aus.

1. Wählen Sie unter „Verbindungstyp“ die Option **Externes Netzwerk** aus.

1. Vergewissern Sie sich, dass **Gemeinsames Verwenden dieses Netzwerkadapters für das Verwaltungsbetriebssystem zulassen** aktiviert ist.

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="Auswählen von „Externes Netzwerk“ und „Gemeinsames Verwenden dieses Netzwerkadapters für das Verwaltungsbetriebssystem zulassen“":::

1. Klicken Sie auf **OK**.

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>Anfügen einer virtuellen SPAN-Schnittstelle an den virtuellen Switch

Sie können über Windows PowerShell oder den Hyper-V-Manager eine virtuelle SPAN-Schnittstelle an den virtuellen Switch anfügen.

**So fügen Sie über PowerShell eine virtuelle SPAN-Schnittstelle an den virtuellen Switch an**

1. Wählen Sie den neu hinzugefügten virtuellen SPAN-Switch aus, und fügen Sie mit dem folgenden Befehl einen neuen Netzwerkadapter hinzu:

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. Aktivieren Sie mit dem folgenden Befehl die Portspiegelung für die ausgewählte Schnittstelle als SPAN-Ziel:

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | Parameter | BESCHREIBUNG |
    |--|--|
    | VK-C1000V-LongRunning-650 | Name der virtuellen CPPM-Appliance |
    |vSwitch_Span |Name des neu hinzugefügten virtuellen SPAN-Switches |
    |Monitor |Name des neu hinzugefügten Adapters |

1. Klicken Sie auf **OK**.

Mit diesen Befehlen wird der Name der neu hinzugefügten Adapterhardware auf `Monitor` festgelegt. Bei Verwendung des Hyper-V-Managers wird der Name der neu hinzugefügten Adapterhardware auf `Network Adapter` festgelegt.

**So fügen Sie über den Hyper-V-Manager eine virtuelle SPAN-Schnittstelle an den virtuellen Switch an**

1. Wählen Sie in der Hardwareliste die Option **Netzwerkadapter** aus.

1. Wählen Sie im Feld „Virtueller Switch“ die Option **vSwitch_Span** aus.

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="Screenshot: Auswählen der folgenden Optionen auf dem Bildschirm des virtuellen Switches":::

1. Wählen Sie in der Hardwareliste in der Dropdownliste „Netzwerkadapter“ die Option **Erweiterte Features** aus.

1. Wählen Sie im Abschnitt „Portspiegelung“ die Option **Ziel** als Spiegelungsmodus für die neue virtuelle Schnittstelle aus.

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="Screenshot: Erforderliche Auswahl zum Konfigurieren des Spiegelungsmodus":::

1. Klicken Sie auf **OK**.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>Aktivieren der Microsoft NDIS-Aufzeichnungserweiterungen für den virtuellen Switch

Die Microsoft NDIS-Aufzeichnungserweiterungen müssen für den neuen virtuellen Switch aktiviert werden.

**So aktivieren Sie Microsoft NDIS-Aufzeichnungserweiterungen für den neu hinzugefügten virtuellen Switch:**

1. Öffnen Sie auf dem Hyper-V-Host den Manager für virtuelle Switches.

1. Erweitern Sie in der Liste „Virtuelle Switches“ den Namen des virtuellen Switches (`vSwitch_Span`), und wählen Sie **Erweiterungen** aus.

1. Wählen Sie im Feld „Switcherweiterungen“ die Option **Microsoft NDIS-Aufzeichnung** aus.

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="Screenshot: Aktivieren von „Microsoft NDIS-Aufzeichnung“ durch Auswahl im Menü „Switcherweiterungen“":::

1. Klicken Sie auf **OK**.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>Festlegen des Spiegelungsmodus für den externen Port

Der Spiegelungsmodus muss für den externen Port des neuen virtuellen Switches festgelegt werden, der als Quelle fungieren soll.

Sie müssen den virtuellen Hyper-V-Switch (vSwitch_Span) so konfigurieren, dass der gesamte Datenverkehr, der an den externen Quellport gesendet wird, an den virtuellen Netzwerkadapter weitergeleitet wird, den Sie als Ziel konfiguriert haben.

Verwenden Sie die folgenden PowerShell-Befehle, um den Port des externen virtuellen Switches auf den Quellspiegelungsmodus festzulegen:

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| Parameter | BESCHREIBUNG |
|--|--|
| vSwitch_Span | Name des neu hinzugefügten virtuellen SPAN-Switches |
| MonitorMode=2 | `Source` |
| MonitorMode=1 | Destination |
| MonitorMode=0 | Keine |

Verwenden Sie den folgenden PowerShell-Befehl, um den Status des Überwachungsmodus zu überprüfen:

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| Parameter | BESCHREIBUNG |
|--|--|
| vSwitch_Span | Name des neu hinzugefügten virtuellen SPAN-Switches |
## <a name="onboard-and-activate-the-virtual-sensor"></a>Durchführen des Onboardings und Aktivieren des virtuellen Sensors

Bevor Sie Ihren Defender für IoT-Sensor verwenden können, müssen Sie den erstellten virtuellen Sensor in Ihr Azure-Abonnement integrieren und die Aktivierungsdatei des virtuellen Sensors herunterladen, um den Sensor zu aktivieren.

### <a name="onboard-the-virtual-sensor"></a>Integrieren des virtuellen Sensors

**So integrieren Sie den virtuellen Sensor:**

1. Navigieren Sie im Azure-Portal zu [Defender für IoT: Erste Schritte](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Wählen Sie **Sensor integrieren** aus.

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="Screenshot: Auswählen von „Sensor integrieren“, um den Onboardingprozess für Ihren Sensor zu starten":::

1. Geben Sie einen Namen für den Sensor ein.

   Es wird empfohlen, die IP-Adresse des Sensors als Teil des Namens anzugeben oder einen leicht identifizierbaren Namen zu verwenden. Durch die Verwendung eines solchen Sensornamens wird die Nachverfolgung vereinfacht.

1. Wählen Sie ein Abonnement aus dem Dropdownmenü aus.

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="Screenshot: Eingeben eines aussagekräftigen Namens und Herstellen der Verbindung zwischen Ihrem Sensor und einem Abonnement":::

1. Wählen Sie mithilfe der Umschaltfläche **Mit der Cloud verbunden** einen Sensorverbindungsmodus aus. Ist der Umschalter auf „Ein“ festgelegt, ist der Sensor mit der Cloud verbunden. Wenn der Umschalter auf „Aus“ festgelegt ist, wird der Sensor lokal verwaltet.

   - **Mit der Cloud verbundene Sensoren**: Die vom Sensor erkannten Informationen werden in der Sensorkonsole angezeigt. Warnungsinformationen werden über einen IoT-Hub übermittelt und können für andere Azure-Dienste wie Microsoft Sentinel freigegeben werden. Darüber hinaus können Threat Intelligence-Pakete aus Defender für IoT an Sensoren gepusht werden. Wenn der Sensor hingegen nicht mit der Cloud verbunden ist, müssen Sie Threat Intelligence-Pakete herunterladen und dann auf Ihre Unternehmenssensoren hochladen. Um Defender für IoT das Pushen von Paketen an Sensoren zu ermöglichen, aktivieren Sie die Umschaltfläche **Automatische Threat Intelligence-Updates**. Weitere Informationen finden Sie unter [Forschung und Pakete zur Bedrohungsanalyse](how-to-work-with-threat-intelligence-packages.md).

      Bei mit der Cloud verbundenen Sensoren wird in der Sensorkonsole der Name angezeigt, der während des Onboardings definiert wurde. Sie können diesen Namen nicht direkt über die Konsole ändern. Bei lokal verwalteten Sensoren wird der während des Onboardings angewendete Name in Azure gespeichert, dieser kann jedoch in der Sensorkonsole geändert werden.

   - **Lokal verwaltete Sensoren**: Die von Sensoren erkannten Informationen werden in der Sensorkonsole angezeigt. Wenn Sie in einem Air-Gap-Netzwerk arbeiten und eine einheitliche Ansicht aller Informationen haben möchten, die von mehreren lokal verwalteten Sensoren erkannt wurden, arbeiten Sie mit der lokalen Verwaltungskonsole.

1. Wählen Sie eine Site zum Zuweisen Ihres Sensors in einem IoT Hub aus. Dabei dient die IoT Hub-Instanz als Gateway zwischen diesem Sensor und Microsoft Defender für IoT. Legen Sie einen Anzeigenamen und eine Zone fest. Sie können auch beschreibende Tags hinzufügen. Der Anzeigename, die Zone und Tags sind beschreibende Einträge unter [Anzeigen integrierter Sensoren](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors).

1. Wählen Sie **Registrieren**.

### <a name="download-the-sensor-activation-file"></a>Herunterladen der Sensoraktivierungsdatei

Sobald die Registrierung für den Sensor abgeschlossen ist, können Sie eine Aktivierungsdatei für den Sensor herunterladen. Die Sensoraktivierungsdatei enthält Anweisungen zum Verwaltungsmodus des Sensors. Die von Ihnen heruntergeladene Aktivierungsdatei ist für jeden von Ihnen bereitgestellten Sensor eindeutig. Der Benutzer, der sich zum ersten Mal bei der Sensorkonsole anmeldet, lädt die Aktivierungsdatei auf den Sensor hoch.

**Gehen Sie wie folgt vor, um eine Aktivierungsdatei herunterzuladen:**

1. Wählen Sie auf der Seite **Sensor integrieren** die Option **Registrieren** aus.

1. Wählen Sie **Aktivierungsdatei herunterladen** aus.

1. Stellen Sie die Datei für den Benutzer bereit, der sich zum ersten Mal bei der Sensorkonsole anmeldet.

### <a name="sign-in-and-activate-the-sensor"></a>Anmelden und Aktivieren des Sensors

**So erfolgen Anmeldung und Aktivierung**

1. Rufen Sie die Sensorkonsole in Ihrem Browser auf, indem Sie die bei der Installation festgelegte IP-Adresse verwenden.

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Screenshot: Microsoft Defender für IoT-Sensor":::

1. Geben Sie die bei der Sensorinstallation festgelegten Anmeldeinformationen ein.

1. Nach der Anmeldung wird das Dialogfeld **Aktivierung** geöffnet. Wählen Sie **Hochladen** aus, und navigieren Sie zu der Aktivierungsdatei, die Sie beim Onboarding des Sensors heruntergeladen haben.

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="Screenshot: Auswählen von „Hochladen“ und Navigieren zur Aktivierungsdatei":::

1. Akzeptieren Sie die Nutzungsbedingungen.

1. Wählen Sie **Aktivieren** aus. Das Dialogfeld „SSL/TLS-Zertifikat“ wird geöffnet.

1. Legen Sie einen Zertifikatnamen fest.

1. Laden Sie die CRT- und KEY-Datei hoch.

1. Geben Sie eine Passphrase ein, und laden Sie bei Bedarf eine PEM-Datei hoch.

1. Wählen Sie **Weiter** aus. Der Bildschirm zur Überprüfung wird geöffnet. Die Überprüfung zwischen Verwaltungskonsole und verbundenen Sensoren ist standardmäßig aktiviert.

1. Deaktivieren Sie den Umschalter **Enable system-wide validation** (Systemweite Überprüfung aktivieren), um die Überprüfung zu deaktivieren. Es wird empfohlen, die Überprüfung zu aktivieren.

1. Wählen Sie **Speichern** aus.  

Möglicherweise müssen Sie den Bildschirm aktualisieren, nachdem Sie das von einer Zertifizierungsstelle signierte Zertifikat hochgeladen haben.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Einrichten [weiterer Appliances](how-to-install-software.md#about-defender-for-iot-appliances).

Erfahren Sie mehr über die [Architektur ohne Agent](architecture.md).
