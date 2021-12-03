---
title: Installieren des Log Analytics-Agents auf Windows-Computern
description: In diesem Artikel wird beschrieben, wie Windows-Computer, die in anderen Clouds oder lokal gehostet werden, über den Log Analytics-Agent für Windows mit Azure Monitor verbunden werden können.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: 0e2b1711ed8f5a5bcf7059242116ece3e70f1235
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332191"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Installieren des Log Analytics-Agents auf Windows-Computern
Dieser Artikel enthält ausführliche Informationen zum Installieren des Log Analytics-Agents auf Windows-Computern mithilfe der folgenden Methoden:

* Manuelle Installation mithilfe des [Setup-Assistenten](#install-agent-using-setup-wizard) oder der [Befehlszeile](#install-agent-using-command-line).
* [Azure Automation Desired State Configuration (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> Die in diesem Artikel beschriebenen Installationsmethoden werden in der Regel für virtuelle Computer in der lokalen Umgebung oder in anderen Clouds verwendet. Effizientere Optionen für virtuelle Azure-Computer finden Sie unter [Installationsoptionen](./log-analytics-agent.md#installation-options).

> [!NOTE]
> Wenn Sie den Agenten so konfigurieren müssen, dass er an mehrere Arbeitsbereiche berichtet, ist dies bei der ersten Einrichtung nicht möglich. Sie können dies nur später tun, indem Sie die Einstellungen aus der Systemsteuerung oder aus PowerShell aktualisieren, wie dies unter [Hinzufügen oder Entfernen von Arbeitsbereichen](agent-manage.md#adding-or-removing-a-workspace) beschrieben ist.  

> [!NOTE]
> Die Installation des Log Analytics-Agents erfordert in der Regel keinen Neustart des Computers.  

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Eine Liste der Windows-Versionen, die vom Log Analytics-Agent unterstützt werden, finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md#supported-operating-systems).

### <a name="sha-2-code-signing-support-requirement"></a>Erforderliche Unterstützung der SHA-2-Codesignierung 
Der Windows-Agent verwendet ab dem 17. August 2020 ausschließlich die SHA-2-Signierung. Diese Änderung wirkt sich auf Kunden aus, die den Log Analytics-Agenten auf einem älteren Betriebssystem als Teil eines beliebigen Azure-Dienstes verwenden (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Microsoft Defender for Cloud, Microsoft Sentinel, Windows Defender ATP). Die Änderung erfordert keine kundenseitige Aktion, es sei denn, Sie führen den Agent auf einer älteren Betriebssystemversion (Windows 7, Windows Server 2008 R2 und Windows Server 2008) aus. Kunden mit einer älteren Betriebssystemversion müssen vor dem 17. August 2020 die folgenden Aktionen auf ihren Computern durchführen, da die Agents andernfalls keine Daten mehr an ihre Log Analytics-Arbeitsbereiche senden:

1. Installieren Sie das neueste Service Pack für Ihr Betriebssystem. Folgende Service Pack-Versionen sind erforderlich:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installieren Sie die Windows-Updates für die SHA-2-Signierung für Ihr Betriebssystem wie in [Unterstützung der SHA-2-Codesignierung für Windows und WSUS (2019)](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) beschrieben.
3. Aktualisieren Sie auf die aktuelle Version des Windows-Agents (10.20.18029).
4. Es wird empfohlen, den Agent zur Verwendung von [TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12) zu konfigurieren. 

## <a name="network-requirements"></a>Netzwerkanforderungen
Die Netzwerkanforderungen für den Windows-Agent finden Sie unter [Übersicht über den Log Analytics-Agent](./log-analytics-agent.md#network-requirements).


   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurieren des Agents für die Verwendung von TLS 1.2
Das [TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12)-Protokoll gewährleistet die Sicherheit der Daten während der Übertragung bei der Kommunikation zwischen dem Windows-Agent und dem Log Analytics-Dienst. Wenn Sie die Installation auf einem Betriebssystem ausführen [auf dem TLS 1.2 standardmäßig nicht aktiviert ist](../logs/data-security.md#sending-data-securely-using-tls-12), sollten Sie TLS 1.2 über die nachstehenden Schritte konfigurieren.

1. Suchen Sie nach dem folgenden Registrierungsschlüssel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Erstellen Sie einen Unterschlüssel unter **Protokolle** für TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Erstellen Sie einen Unterschlüssel **Client** unter dem Versionsunterschlüssel des TLS 1.2-Protokolls, den Sie zuvor erstellt haben. Beispiel: **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Erstellen Sie die folgenden DWORD-Werte unter **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Enabled** [Wert = 1]
    * **DisabledByDefault** [Wert = 0]  

Konfigurieren Sie .NET Framework 4.6 oder höher, um sichere Kryptografie zu unterstützen, da diese standardmäßig deaktiviert ist. Die [sicherere Kryptografie](/dotnet/framework/network-programming/tls#schusestrongcrypto) verwendet sicherere Netzwerkprotokolle wie TLS 1.2 und blockiert Protokolle, die nicht sicher sind. 

1. Suchen Sie nach dem folgenden Registrierungsschlüssel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Erstellen Sie den DWORD-Wert **SchUseStrongCrypto** unter diesem Unterschlüssel mit einem Wert von **1**.  
3. Suchen Sie nach dem folgenden Registrierungsschlüssel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Erstellen Sie den DWORD-Wert **SchUseStrongCrypto** unter diesem Unterschlüssel mit einem Wert von **1**. 
5. Starten Sie das System neu, damit die Einstellungen wirksam werden. 

## <a name="install-agent-using-setup-wizard"></a>Installieren des Agents mithilfe des Setup-Assistenten
Mit den folgenden Schritten wird der Log Analytics-Agent in Azure und Azure Government Cloud mithilfe des Setup-Assistenten für den Agent auf Ihrem Computer installiert und konfiguriert. Wenn Sie erfahren möchten, wie Sie den Agent so konfigurieren, dass er auch an eine System Center Operations Manager-Verwaltungsgruppe berichtet, lesen Sie [Bereitstellen des Operations Manager-Agents mit dem Agent-Setup-Assistenten](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Wählen Sie in Ihrem Log Analytics-Arbeitsbereich auf der Seite **Windows-Server**, auf die Sie zuvor navigiert sind, die entsprechende Version für **Windows-Agent herunterladen** aus, um den Download abhängig von der Prozessorarchitektur des Windows-Betriebssystems auszuführen.   
2. Führen Sie Setup aus, um den Agent auf Ihrem Computer zu installieren.
2. Klicken Sie auf der Seite **Willkommen** auf **Weiter**.
3. Lesen Sie die Seite **Lizenzbedingungen** durch, und klicken Sie anschließend auf **Ich stimme zu**.
4. Auf der Seite **Zielordner** können Sie den Standardinstallationsordner entweder ändern oder beibehalten. Klicken Sie anschließend auf **Weiter**.
5. Wählen Sie auf der Seite **Agent-Setupoptionen** aus, dass der Agent mit Azure Log Analytics verbunden werden soll, und klicken Sie dann auf **Weiter**.   
6. Führen Sie auf der Seite **Azure Log Analytics** die folgenden Schritte aus:
   1. Fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** ein, die Sie zuvor kopiert haben.  Wenn der Computer in einen Log Analytics-Arbeitsbereich in Azure Government Cloud melden soll, wählen Sie **Azure US-Regierung** aus der Dropdownliste **Azure Cloud** aus.  
   2. Wenn der Computer über einen Proxyserver mit dem Log Analytics-Dienst kommunizieren muss, klicken Sie auf **Erweitert**, und stellen Sie die URL sowie die Portnummer des Proxyservers bereit.  Wenn der Proxyserver eine Authentifizierung erfordert, geben Sie den Benutzernamen und das Kennwort für die Authentifizierung mit dem Proxyserver ein, und klicken Sie dann auf **Weiter**.  
7. Klicken Sie auf **Weiter**, nachdem Sie die Bereitstellung der erforderlichen Konfigurationseinstellungen abgeschlossen haben.<br><br> ![Arbeitsbereichs-ID und Primärschlüssel einfügen](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Überprüfen Sie Ihre Auswahl auf der Seite **Bereit zum Installieren**, und klicken Sie dann auf **Installieren**.
9. Klicken Sie auf der Seite **Die Konfiguration wurde erfolgreich abgeschlossen** auf **Fertig stellen**.

Nach Abschluss wird der **Microsoft Monitoring Agent** in der **Systemsteuerung** angezeigt. Informationen zum Überprüfen, ob er Berichte an Log Analytics übermittelt, finden Sie unter [Überprüfen der Agent-Konnektivität mit Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Installieren des Agents über die Befehlszeile
Die heruntergeladene Datei für den Agent ist ein eigenständiges Installationspaket.  Das Setupprogramm für den Agent und die unterstützenden Dateien sind im Paket enthalten und müssen extrahiert werden, um eine ordnungsgemäße Installation über die in den folgenden Beispielen gezeigte Befehlszeile durchführen zu können.    

>[!NOTE]
>Wenn Sie einen Agent aktualisieren möchten, müssen Sie die Skripting-API für Log Analytics verwenden. Weitere Informationen finden Sie im Thema [Verwalten und Warten des Log Analytics-Agents für Windows und Linux](agent-manage.md).

In der folgenden Tabelle sind die spezifischen Parameter aufgeführt, die im Setup (auch bei der Bereitstellung mit Automation DSC) für den Agent unterstützt werden.

|MMA-spezifische Optionen                   |Notizen         |
|---------------------------------------|--------------|
| NOAPM=1                               | Dieser Parameter ist optional. Installiert den Agent ohne .NET-Anwendungsleistungsüberwachung.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurieren des Agents, sodass Berichte an einen Arbeitsbereich übermittelt werden                |
|OPINSIGHTS_WORKSPACE_ID                | Arbeitsbereichs-ID (GUID) für den hinzuzufügenden Arbeitsbereich                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbeitsbereichsschlüssel, der für die erste Authentifizierung beim Arbeitsbereich verwendet wird |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Angabe der Cloudumgebung, in der sich der Arbeitsbereich befindet <br> 0 = Azure Commercial Cloud (Standard) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI für den zu verwendenden Proxy |
|OPINSIGHTS_PROXY_USERNAME               | Benutzername für den Zugriff auf einen authentifizierten Proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Kennwort für den Zugriff auf einen authentifizierten Proxy |

1. Um die Installationsdateien des Agent zu extrahieren, führen Sie `MMASetup-<platform>.exe /c` von einer Eingabeaufforderung mit erhöhten Rechten aus. Sie werden dann nach dem Pfad gefragt, in den die Dateien extrahiert werden sollen.  Alternativ können Sie den Pfad angeben, indem Sie die Argumente `MMASetup-<platform>.exe /c /t:<Full Path>` übergeben.  
2. Für die Installation des Agents im Hintergrund und seine Konfiguration zum Übermitteln von Berichten an einen Arbeitsbereich in der Azure Commercial-Cloud extrahieren Sie die Setupdateien aus dem Ordner und geben Folgendes ein: 
   
    ```shell
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   Zum Konfigurieren des Agents zum Übermitteln von Berichten an die Azure US Government-Cloud geben Sie Folgendes ein: 

    ```shell
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Die Zeichenfolgenwerte für die Parameter *OPINSIGHTS_WORKSPACE_ID* und *OPINSIGHTS_WORKSPACE_KEY* müssen in doppelte Anführungszeichen gesetzt werden, damit Windows Installer angewiesen wird, diese als gültige Optionen für das Paket zu interpretieren. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Installieren des Agents mithilfe von DSC in Azure Automation

Sie können das folgende Skriptbeispiel verwenden, um den Agent mit Azure Automation DSC zu installieren.   Wenn Sie über kein Automation-Konto verfügen, finden Sie unter [Erste Schritte mit Azure Automation](../../automation/index.yml) Informationen zu den Anforderungen und Schritten zum Erstellen eines Automation-Kontos, die vor der Verwendung von Automation DSC erforderlich sind.  Wenn Sie nicht mit Automation DSC vertraut sind, finden Sie entsprechende Informationen unter [Erste Schritte mit Azure Automation DSC](../../automation/automation-dsc-getting-started.md).

Im folgenden Beispiel wird der vom `URI`-Wert identifizierte 64-Bit-Agent installiert. Sie können auch die 32-Bit-Version installieren, indem Sie den URI-Wert ersetzen. Die URIs für beide Versionen lauten:

- Windows-64-Bit-Agent: https://go.microsoft.com/fwlink/?LinkId=828603
- Windows-32-Bit-Agent: https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Mit diesem Verfahren und dem Skriptbeispiel kann der bereits auf einem Windows-Computer bereitgestellte Agent nicht aktualisiert werden.

Die 32-Bit- und die 64-Bit-Version des Agent-Pakets weisen unterschiedliche Produktcodes auf. Auch neu veröffentlichten Versionen ist ein eindeutiger Wert zugewiesen.  Der Produktcode ist eine GUID, die als hauptsächliche Identifikation einer Anwendung oder eines Produkts dient und durch die Windows Installer-Eigenschaft **ProductCode** angegeben wird.  Der Wert `ProductId` im Skript **MMAgent.ps1** muss mit dem Produktcode aus dem Installationspaket für den 32-Bit- oder 64-Bit-Agent übereinstimmen.

Den Produktcode können Sie mithilfe von „Orca.exe“ aus [Windows SDK Components for Windows Installer Developers](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers), bei dem es sich um eine Komponente des Windows Software Development Kit handelt, oder über PowerShell entsprechend einem von einem MVP (Microsoft Valuable Professional) geschriebenen [Beispielskript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) direkt aus dem Installationspaket für den Agent abrufen.  Für beide Vorgehensweisen müssen Sie zuerst die Datei **MOMAgent.msi** aus dem MMASetup-Installationspaket extrahieren.  Dies wird weiter oben im ersten Schritt im Abschnitt [Installieren des Agents über die Befehlszeile](#install-agent-using-command-line) gezeigt.  

1. Importieren Sie das DSC-Modul „xPSDesiredStateConfiguration“ aus [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) in Azure Automation.  
2.    Erstellen Sie Variablenassets in Azure Automation für *OPSINSIGHTS_WS_ID* und *OPSINSIGHTS_WS_KEY*. Legen Sie für *OPSINSIGHTS_WS_ID* Ihre Log Analytics-Arbeitsbereichs-ID und für *OPSINSIGHTS_WS_KEY* den Primärschlüssel Ihres Arbeitsbereichs fest.
3.    Kopieren Sie das Skript, und speichern Sie es unter „MMAgent.ps1“.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Aktualisieren Sie den Wert `ProductId` im Skript mithilfe der zuvor empfohlenen Methoden mit dem Produktcode, den Sie aus der neuesten Version des Agent-Installationspakets extrahiert haben. 
5. [Importieren Sie das Konfigurationsskript „MMAgent.ps1“](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) in Ihr Automation-Konto. 
6. [Weisen Sie einen Windows-Computer oder -Knoten](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) der Konfiguration hinzu. Innerhalb von 15 Minuten prüft der Knoten die Konfiguration, und der Agent wird auf den Knoten gepusht.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Überprüfen der Agent-Konnektivität mit Azure Monitor

Nachdem die Installation des Agents abgeschlossen ist, kann auf zwei Arten überprüft werden, ob er verbunden ist und Berichte übermittelt.  

Suchen Sie auf dem Computer in der **Systemsteuerung** das Element **Microsoft Monitoring Agent**.  Wählen Sie das Element aus. Der Agent sollte auf der Registerkarte **Azure Log Analytics** eine Meldung wie die folgende anzeigen: **Der Microsoft Monitoring Agent hat erfolgreich eine Verbindung mit dem Microsoft Operations Management Suite-Dienst hergestellt.**<br><br> ![MMA-Verbindungsstatus mit Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Außerdem können Sie eine einfache Protokollabfrage im Azure-Portal durchführen.  

1. Suchen Sie im Azure-Portal nach dem Eintrag **Überwachen**, und wählen Sie ihn aus.
1. Wählen Sie im Menü **Protokolle** aus.
1. Geben Sie im Bereich **Protokolle** im Abfragefeld Folgendes ein:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

In den zurückgegebenen Suchergebnissen sollten Heartbeat-Datensätze für den Computer angezeigt werden, die angeben, dass dieser verbunden ist und Berichte an den Dienst übermittelt.

## <a name="cache-information"></a>Informationen zum Cache

Daten vom Log Analytics-Agent werden auf dem lokalen Computer unter *C:\Programme\Microsoft Monitoring Agent\Agent\Health Service State* zwischengespeichert, bevor sie an Azure Monitor gesendet werden. Der Agent versucht den Upload alle 20 Sekunden. Wenn ein Fehler auftritt, wird bis zum erfolgreichen Abschluss jeweils eine exponentiell steigende Zeitspanne gewartet. Vor dem zweiten Versuch wird 30 Sekunden gewartet, vor dem nächsten 60 Sekunden, dann 120 Sekunden usw. bis maximal 8,5 Stunden zwischen Wiederholungen, bis wieder eine Verbindung hergestellt wurde. Diese Wartezeit ist mit einem leichten Zufallsfaktor versehen, um zu vermeiden, dass alle Agents gleichzeitig versuchen, eine Verbindung herzustellen. Wenn die maximale Puffergröße erreicht ist, werden die ältesten Daten verworfen.

Die Standardcachegröße beträgt 50 MB, dies kann jedoch zwischen 5 MB und 1,5 GB konfiguriert werden. Sie wird im Registrierungsschlüssel *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum* gespeichert. Der Wert stellt die Anzahl der Seiten mit 8 KB pro Seite dar.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Neukonfiguration, Upgrade oder Entfernen des Agents auf dem virtuellen Computer finden Sie unter [Verwalten und Warten des Log Analytics-Agents für Windows und Linux](agent-manage.md).

- Lesen Sie [Problembehandlung für den Windows-Agent](agent-windows-troubleshoot.md), wenn bei der Installation oder Verwaltung des Agent Probleme auftreten.
