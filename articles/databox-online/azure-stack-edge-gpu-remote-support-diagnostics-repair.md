---
title: Aktivieren von Fernbetreuung (Remote Support), Diagnose und Wartung für Azure-Stack-Edge-Geräte
description: Hier wird beschrieben, wie die Fernbetreuung auf Ihren Azure-Stack-Edge-Geräten aktiviert wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 364845dc046664a7af4d9f7ac6fbb965a818430a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096156"
---
# <a name="remote-support-and-diagnostics-for-azure-stack-edge"></a>Remote Support (Fernbetreuung) und Diagnose für Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]


> [!IMPORTANT]
> Der Remote Support befindet sich in der öffentlichen Vorschau (Public Preview) und gilt für die Azure-Stack-Edge-Version 2110 oder höher.

Auf Ihrem Azure-Stack-Edge-Gerät können Sie die Fernbetreuung (Remote Support) aktivieren, damit Microsoft-Techniker Probleme diagnostizieren und beheben können, indem Sie von der Ferne auf Ihr Gerät zugreifen. Wenn Sie diese Funktion aktivieren, stimmen Sie der Zugriffsebene und der Zugriffsdauer zu. 

In diesem Abschnitt wird die Fernbetreuungsfunktion beschrieben, einschließlich der Verwendung dieser Funktion, der Aktivierung der Funktion und einer Liste der zulässigen Vorgänge, die Microsoft-Techniker von der Ferne auf Ihrem Gerät ausführen können.


## <a name="about-remote-support"></a>Informationen über die Fernbetreuung

Wenn bei Azure Stack Edge ein Problem auftritt, sollten Sie in der Regel ein Support-Paket sammeln und für Microsoft-Support bereitstellen. In einigen Fällen verfügen die Protokolle nicht über genügend Informationen, um das Problem zu diagnostizieren und zu beheben. Microsoft-Support wendet sich dann an Sie, um Ihre Zustimmung für die Fernbetreuung einzuholen.

Hier sind einige Vorteile der Fernbetreuung:

- Sie können das Problem schneller beheben, da Microsoft-Support keine persönliche Besprechung organisieren muss.
- Sie können das Transkript der Fernbetreuungsberatung einsehen, einschließlich aller Vorgänge, die auf dem Gerät ausgeführt wurden.
- Sie haben die vollständige Kontrolle über Ihre Daten und können die Zustimmung jederzeit widerrufen. Wenn Sie vergessen, die Beratung zu beenden, wird der Zugriff auf die Beratung automatisch deaktiviert, sobald die Zugriffsdauer abläuft.

## <a name="how-remote-support-works"></a>Funktionsweise der Fernbetreuung

Die folgende Abbildung zeigt, wie die Fernbetreuung funktioniert.

![Abbildung der Fernbetreuung auf Azure Stack Edge](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/remote-support-flow-1.png)

1. Sie geben über das Azure-Portal ihre Einwilligung für die Fernbetreuung für Ihr Gerät. Außerdem konfigurieren Sie die Ebene und die Dauer des Zugriffs auf Ihr Gerät.
1. Die Zustimmung und die Anfrage für eine Fernbetreuung werden vom Azure-Portal an den Azure-Stack-Edge-Ressourcen-Provider (RP) gesendet, der ihn wiederum an das Gerät sendet. Der Geräte-Agent fragt den RP nach den Anmeldeinformationen für die Hybridverbindung, mit denen er eine Verbindung herstellen kann.
1. Es werden Anmeldeinformationen für hybride Verbindungen erstellt, die zum Herstellen einer Azure-Hybridverbindung verwendet werden. Diese Verbindung ist:
    
    - Gerätespezifisch. Jedes Gerät verfügt über eine eigene Verbindung und die Verbindung wird nicht freigegeben.
    - Sie ermöglicht Microsoft-Support einen Just-In-Time-Zugriff (JIT) auf das Gerät über einen sicheren, überwachten und konformen Kanal.  
    - Diese Verbindung verwendet ein *HTTPS-Protokoll* über Port 443 und der Datenverkehr wird mit TLS 1.2 verschlüsselt.
     
1. Der Geräte-Agent beginnt mit dem Lauschen auf diese Hybridverbindung in Bezug auf alle Anfragen, die über die Browserschnittstelle für das Azure-Support Center eingehen. 
1. Der Browser fordert an, eine Verbindung mit der Hybridverbindung herzustellen, und die Anfrage wird an das Gerät gesendet, um eine eingeschränkte PowerShell-Sitzung zu öffnen. Gibt es eine Zustimmung, wird die Anfrage akzeptiert. Wenn das Gerät nicht über die Zustimmung verfügt, lehnt es diese Verbindung einfach ab. 

Sobald die Verbindung hergestellt wurde, erfolgt die gesamte Kommunikation über diese sichere Verbindung. 

Die Vorgänge, die der Microsoft-Support über diese Verbindung ausführen kann, werden basierend auf der Zugriffsebene *eingeschränkt,* die mit [begrenzten Verwaltungsvorgängen (Just Enough Administration)](/powershell/scripting/learn/remoting/jea/overview) (JEA) gewährt wird. Weitere Informationen zu cmdlets, die der Microsoft-Support während einer Fernbetreuungssitzung ausführen kann, finden Sie in diesem Abschnitt in der [Liste der zulässigen Microsoft-Support-Eingriffe](#operations-allowed-in-remote-support).


## <a name="enable-remote-support"></a>Aktivieren der Fernbetreuung

Führen Sie die folgenden Schritte aus, um eine Fernbetreuung für Ihr Azure-Stack-Edge-Gerät zu konfigurieren:

1. Wechseln Sie im Azure-Portal zur Azure-Stack-Edge-Ressource für Ihr Gerät und dann gehen Sie auf **Diagnose**.
1. Standardmäßig hat Microsoft keinen Remotezugriff auf Ihr Gerät und der Fernbetreuungsstatus wird als **Nicht aktiviert** angezeigt. Um diese Funktion zu aktivieren, wählen Sie **Remote Support konfigurieren** aus.

    ![Screenshot einer aktivierten Fernbetreuung (Remote Support) auf Azure Stack Edge mit hervorgehobenem Schriftzug "Remote Support konfigurieren"](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-1.png)

1. Wählen Sie die **Zugriffsebene** als **Diagnose** aus, um JIT-Zugriff für den Microsoft-Support zum Sammeln von Diagnoseinformationen aus der Ferne zu gewährleisten. Bei Bedarf empfiehlt das Support Team, **Diagnose und Reparatur** auszuwählen, um Wartungsaktionen aus der Ferne durchführen zu können. 

    Jede Zugriffsebene ermöglicht eine andere Zusammenstellung von Remotebefehlen auf dem Gerät und somit eine andere Reihe von Vorgängen. 

    - Die Diagnose ermöglicht größtenteils Lesevorgänge, sodass größtenteils `Get`cmdlets verfügbar sind.
    - Diagnose und Reparatur ermöglichen Lese-/Schreibzugriff, sodass zusätzlich zu `Get` cmdlets  auch die cmdlets`Set`, `Add`, `Start`, `Restart`, `Stop`, `Invoke`, `Remove` verfügbar sind.
    
    Weitere Informationen finden Sie in der [Liste aller Supportvorgänge, die in den einzelnen Zugriffsebenen zulässig sind](#operations-allowed-in-remote-support).

1. Wählen Sie die Dauer für die Bereitstellung des Remotezugriffs aus. Die Dauer kann 7, 15, 21 oder 30 Tage betragen. Bei Ablauf dieser Dauer wird der Remotezugriff auf das Gerät automatisch deaktiviert. 

1. Um den Zugriff jederzeit zu widerrufen, legen Sie **Zugriff nicht zulassen** fest. Durch diese Aktion werden alle vorhandenen Sitzungen beendet, sodass keine neuen Sitzungen mehr vorgenommen werden können.

1. Nachdem Sie die Fernbetreuung konfiguriert haben, wählen Sie **Übernehmen** aus, damit die Einstellungen wirksam werden. Wenn Sie **Übernehmen** auswählen, erteilen Sie Microsoft die Zustimmung, Diagnoseinformationen aus einer sicheren und kontrollierten Umgebung aus der Ferne (remote) zu sammeln.

    ![Screenshot der Remote-Support-Einstellungen auf Azure Stack Edge mit hervorgehobener Zugriffsebene und hervorgehobener Dauer](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-2.png)    

## <a name="remote-support-examples"></a>Beispiele für Remote Support

In den folgenden Beispielszenarien wird gezeigt, wie Sie verschiedene Vorgänge ausführen, wenn die Remoteunterstützung auf Ihrem Gerät aktiviert ist. 

Zum Durchführen eines Remote Supports müssen Sie sich zunächst [mit der PowerShell-Schnittstelle des Geräts verbinden](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) und dann die cmdlets starten.

### <a name="list-existing-remote-sessions"></a>Auflisten vorhandener Remote-Sitzungen

Verwenden Sie das `Get-HcsRemoteSupportSession`cmdlet, um alle Remotesitzungen aufzulisten, die innerhalb der angegebenen Anzahl von Tagen für das Gerät durchgeführt wurden.

```powershell
Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays <Number of days>
```
Hier sehen Sie ein Beispiel für alle Remote-Support-Sitzungen, die in den letzten 10 Tagen konfiguriert wurden. 

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays 10

SessionId                : 3c135cba-f479-4fef-8dbb-a2b52b744504
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 10:41:03 PM +00:00
SessionEndTime           : 8/19/2021 10:44:31 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.Lp+Myohb.20210
                           819154101.txt
SessionTranscriptContent :

SessionId                : c0f2d002-66a0-4d54-87e4-4b1b949ad686
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 7:41:19 PM +00:00
SessionEndTime           : 8/19/2021 7:58:20 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.j0lCd5Tm.20210
                           819124117.txt
SessionTranscriptContent :

SessionId                : ca038e58-5344-4377-ab9c-c857a27c8b73
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/19/2021 10:49:39 PM +00:00
SessionEndTime           : 8/20/2021 12:49:40 AM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.YHmes94q.
                           20210819154937.txt
SessionTranscriptContent :

[10.126.76.20]: PS>
```


### <a name="get-details-on-a-specific-remote-session"></a>Abrufen von Details zu einer bestimmten Remote-Sitzung

Verwenden Sie das `Get-HcsRemoteSupportSession` cmdlet, um Details für die Remotesitzung mit der ID *SessionID* abzurufen.

```powershell
Get-HcsRemoteSupportSession -SessionId <SessionId> -IncludeSessionTranscript $true
```

Hier sehen Sie ein Beispiel für eine bestimmte Sitzung, in der die Remoteunterstützung mit der Option **Diagnose** konfiguriert wurde. Die `AccessLevel` ist in diesem Fall `ReadOnly` .

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId 360706a2-c530-419f-932b-55403e19502e -IncludeTranscriptContents $true

SessionId                : 360706a2-c530-419f-932b-55403e19502e
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/26/2021 2:35:37 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.u7qF2J2d.
                           20210826073536.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826073536
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteDiagnostics
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 10976
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}



                           PS>CommandInvocation(Get-Command): "Get-Command"
                           CommandInvocation(Get-HcsApplianceInfo): "Get-HcsApplianceInfo"

[10.126.76.20]: PS>
```
Im Folgenden finden Sie ein Beispiel dafür, in dem die Option **Diagnose und Reparatur** für die Remoteunterstützung konfiguriert wurde. Die `AccessLevel` für die Remoteunterstützungssitzung ist `ReadWrite`.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId add360db-4593-4026-93d5-6d6d05d39046 -IncludeTranscriptContents $true

SessionId                : add360db-4593-4026-93d5-6d6d05d39046
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/26/2021 2:57:08 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.ZroHb8Un.20210
                           826075705.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826075705
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteRepair
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 21832
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}

                           PS>CommandInvocation(Get-Command): "Get-Command"
[10.126.76.20]: PS>
```
 
### <a name="collect-remote-session-transcripts"></a>Transkripte von Remotesitzungen sammeln

Abhängig von Ihren Protokollanforderungen müssen Sie möglicherweise die Transkripte anzeigen. Führen Sie die folgenden Schritte aus, um Transkripte von Remotesitzungen zu erfassen:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung > Support**. Erfassen eines Support-Pakets.
1. Nachdem das Support-Paket gesammelt wurde, laden Sie das Support-Paket herunter. Extrahieren Sie den gezippten Ordner. Die Transkripte befinden sich im Ordner **SupportTranscriptsripts** im Support-Paket. 

    ![Screenshot: hervorgehobener Ordner "SupportTranscripts" im Support-Paket](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/transcript-folder-support-package-1.png)


 
## <a name="operations-allowed-in-remote-support"></a>zulässige Eingriffe in der Remoteunterstützung

In den folgenden Abschnitten werden die zulässigen cmdlets aufgeführt, die Microsoft-Support während einer Remoteunterstützungssitzung ausführen können. Die cmdlet-Verfügbarkeit wird nach der Zugriffsebene festgelegt, die auf **Diagnose** oder Diagnose und **Reparatur** eingestellt ist.

#### <a name="azure-stack-edge-cmdlets"></a>Azure-Stack-Edge-cmdlets

| Cmdlets                      | Diagnose | Diagnose & Reparatur | BESCHREIBUNG |
|-----------------------------------------------|-------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Add-HcsExternalVirtualSwitch                  |             | J                   | Erstellt einen neuen externen virtuellen Switch zum Konfigurieren von Kubernetes auf Ihrem Gerät.  |
| Add-HcsVirtualNetwork                         |             | J                   | Dadurch wird ein virtueller Switch an der angegebenen Netzwerkschnittstelle erstellt.|
| Get-AcsHealthStatus                           | J           | J                   | Ruft den Integritätsstatus von Azure-konsistenten Dienstanbietern ab. |
| Get-AzureDataBoxEdgeRole                      | J           | J                   | Wenn die Compute-Rolle auf Ihrem Gerät konfiguriert ist, können Sie die Compute-Protokolle auch über die PowerShell-Schnittstelle abrufen.|
| Get-HcsApplianceInfo                          | J           | J                   | Ruft Informationen für Ihr Gerät ab, z. B. ID, Anzeigename, Softwareversion oder Systemstatus.|
| Get-HcsApplianceSupportPackage                | J           | J                   | Erfasst das Support-Paket für Ihr Gerät.     |
| Get-HcsArpResponse                            | J           | J                   |   |
| Get-HcsControllerSetupInformation             | J           | J                   | Ruft das Microsoft.Hcs.Setup.ControllerInfo-Objekt ab. |
| Get-HcsDataBoxAccount                         | J           | J                   ||
| Add-HcsExternalVirtualSwitch                  | J           | J                   | Ruft den Switch ab, auf dem Kubernetes konfiguriert werden soll.   |
| Get-HcsGpuNvidiaSmi                           | J           | J                   | Ruft die GPU-Treiberinformationen für Ihr Gerät ab.  |
| Get-HcsIPAddress                              | J           | J                   | Ruft die Konfiguration der Netzwerkadapter aus dem Datenspeicher oder System ab.   |
| Get-HcsIPAddressPool                          |             | J                   | |
| Get-HcsKubeClusterInfo                        | J           | J                   | Ruft Konfigurationsinformationen für den Kubernetes-Cluster ab.|
| Get-HcsKubeClusterInfo                 | J           | J                   | Ruft den Kubernetes-Dienst und die Pod-Subnetze ab.                                                                                                                                                   |
| Get-HcsKubernetesAzureMonitorConfiguration    | J           | J                   | Ruft Informationen zum Azure-Monitor ab, der auf dem Kubernetes-Cluster auf Ihrem Gerät ausgeführt wird.                                                                                                        |
| Get-HcsKubernetesContainerRegistryInfo        | J           | J                   | Ruft die Details für die Edge-Container-Registrierung auf Ihrem Gerät ab.                                                                                                                               |
| Get-HcsKubernetesDashboardToken               | J           | J                   | Ruft das Kubernetes-Dashboard-Token ab, um das Dashboard anzuzeigen (sie können dies auch über die lokale Benutzeroberfläche tun.                                                                                               |
| Get-HcsKubernetesNamespaces                   | J           | J                   | Ruft den Kubernetes-Namensraum (Namespace) ab, den Sie konfiguriert haben.  |
| Get-HcsKubernetesUserConfig                   | J           | J                   | Ruft den `kubeconfig` ab, der einem bestimmten Namensraum entspricht, den Sie konfiguriert haben.  |
| Get-HcsLocalWebUICertificateHash              | J           | J                   | Ruft den Fingerabdruck des konfigurierten lokalen Webbenutzeroberflächen-Zertifikats ab. |
| Get-HcsMacAddressPool                         |             | J                   | Ruft den Kubernetes-VM-Mac-Adresspool ab.     |
| Get-HcsNetBmcInterface                        | J           | J                   | Ruft die Netzwerkkonfigurationseigenschaften des Baseboard-Verwaltungscontrollers (Baseboard Management Controller, BMC) ab, z. B. IPv4-Adresse, IPv4-Gateway, IPv4-Subnetzmaske und, ob DHCP auf Ihrem Gerät aktiviert ist oder nicht. |
| Get-HcsNetInterface                           | J           | J                   | Ruft die gewünschte Netzwerkkonfiguration des Geräts ab.    |
| Get-HcsNetRoute                               | J           | J                   | Überprüft alle benutzerdefinierten Routenkonfigurationen, die Sie auf Ihrem Gerät hinzugefügt haben. Diese Routen enthalten nicht alle Systemrouten oder Standardrouten, die bereits auf dem Gerät vorhanden sind.            |
| Get-HcsNodeSecureEraseLogs                    | J           | J                   | muss entfernt werden, bestätigen Sie dies aus Ernie: Ruft Protokolle ab, die bestätigen, dass Laufwerke auf dem Gerät während der vorangegangenen Zurücksetzung auf die Werkseinstellung sicher gelöscht wurden.     |
| Get-HcsNodeSupportPackage                     | J           | J                   | Erfasst Protokolle von Ihrem Gerät und kopiert diese in ein angegebenes Netzwerkfreigabe oder eine angegebene lokale Freigabe in Form von einem Support-Paket.   |
| Get-HcsRemoteSupportConsent                   | J           | J                   | Ruft die Zustimmung des Kunden zum Aktivieren des Remote Supports auf dem Gerät ab.   |
| Get-HcsRestEndPoint                           | J           | J                   |  |
| Get-HcsSetupDesiredStateResult                | J           | J                   | Ruft Desired-State-Configuration-(DSC)-Ergebnisobjekte einiger DSC-Ausführungen für Ihr Gerät ab. |
| Get-HcsSmbConfiguration                       | J           | J                   | <!-- to be removed--> |
| Get-HcsSupportedVpnRegions                    | J           | J                   |   |
| Get-HcsSupportPackageUploadJob                | J           | J                   | Ruft den Status eines ausgeführten Hochladevorgangs eines Support-Pakets ab.  |
| Get-HcsUpdateConfiguration                    | J           | J                   | Ruft die Update-Server-Einstellungen ab, z. B. Servertyp, URI-Pfad zum Server, der für Ihr Azure-Stack-Edge-Gerät konfiguriert ist.  |
| Get-HcsUpdateJob                              | J           | J                   | Ruft alle Update-Vorgänge ab, die auf Ihrem Gerät ausgeführt werden, oder den Status eines bestimmten Update-Vorganges, wenn die Auftrags-ID übergeben wird. |
| Add-HcsVirtualNetwork                         |             | J                   | Identifiziert das virtuelle Netzwerk und das Subnetz, das einem Switch zugeordnet ist, den Sie auf Ihrem Gerät erstellt haben.   |
| Get-HcsVirtualSwitch                          | J           | J                   | Ruft einen virtuellen Switch ab, der einer angegebenen Netzwerkschnittstelle zugeordnet ist.  |
| Get-VMInGuestLogs                             |             | J                   | Erfasst Gastprotokolle für ausgefallene VMs auf Ihrem Gerät.  |
| Invoke-AzureDataBoxEdgeRoleReconcile          |             | J                   | Wird verwendet, um die Kubernetes-Clusterkonfiguration auf den neuesten Stand zu bringen. |
| Invoke-AzureDataBoxEdgeRoleReconcile        |             | J                   | Wird verwendet, um die Konfiguration des Kubernetes-Clusters zu ändern.  |
| Remove-HcsIPAddressPool                       |             |                     | Ruft den Kubernetes-VM-Mac-Adresspool ab. |
| Get-HcsKubeClusterInfo              |             | J                   | Die Kubernetes-Dienstsubnetze oder -pods abändern.|
| Remove-HcsKubernetesAzureArcAgent             |             | J                   | Entfernt den Azure-Arc-Agent aus dem Kubernetes-Cluster auf Ihrem Gerät.  |
| Remove-HcsKubernetesAzureMonitorConfiguration |             | J                   | Entfernt den Azure Monitor aus dem Kubernetes-Cluster auf Ihrem Gerät und ermöglicht Ihnen das Sammeln von Container-Protokollen und Prozessorwerten aus dem Kubernetes-Cluster.  |
| Remove-HcsKubernetesContainerRegistry         |             | J                   | Löscht die Edge-Container-Registrierung von Ihrem Gerät.   |
| Remove-HcsKubernetesNamespace                 |             | J                   | Löscht einen angegebenen Kubernetes-Namensraum. |
| Remove-HcsMacAddressPool                      |             | J                   | Ruft den Kubernetes-VM-Mac-Adresspool ab.|
| Remove-HcsNetRoute                            |             | J                   | Entfernt eine Routenkonfiguration, die Sie auf Ihrem Gerät hinzugefügt haben. |
| Remove-HcsVirtualNetwork                      |             | J                   | Identifiziert das virtuelle Netzwerk und das Subnetz, das einem Switch zugeordnet ist, den Sie auf Ihrem Gerät erstellt haben. |
| Remove-HcsVirtualSwitch                       |             | J                   | Entfernt einen virtuellen Switch, der einem Port auf Ihrem Gerät zugeordnet ist.  |
| Restart-HcsNode                               |             | J                   | Startet einen Knoten auf Ihrem Gerät neu. Bei einem Gerät mit einem einzelnen Knoten wird das Gerät neu gestartet und es kommt zu einer Ausfallzeit. |
| Set-AzureDataBoxEdgeRoleCompute -Name -Memory               |             | J                   | Wird zum Konfigurieren von Compute über das Azure-Portal verwendet und erstellt und konfiguriert den Kubernetes-Cluster.   |
| Set-HcsCertificate                            |             | J                   | Damit können Sie eigene Zertifikate nutzen. |
| Set-HcsClusterLevelSecurity                   |             | J                   | Konfigurieren Sie die Sicherheitsstufe des Clusterdatenverkehrs. Dies schließt knoteninternen Clusterdatenverkehr und Datenverkehr von freigegebenen Clustervolumes (Cluster Shared Volumes, CSV) ein.    |
| Set-HcsClusterWitness                         |             | J                   | Erstellt oder konfiguriert einen Windows-Clusterzeugen. Ein Clusterzeuge hilft beim Einrichten eines Quorums, wenn ein Knoten auf einem Gerät mit zwei Knoten ausläuft.     |
| Set-HcsEastWestCsvEncryption                  |             | J                   | <!--can be removed, confirmed with Vibha/Deepan. Talk to Phani. -->  |
| Add-HcsExternalVirtualSwitch                  |             | J                   | Konfiguriert einen externen virtuellen Switch an dem Port, für den Sie Compute auf Ihrem Gerät aktiviert haben.  |
| Get-HcsIPAddress                              |             | J                   | Aktualisiert Netzwerkschnittstelleneigenschaften.                 |
| Get-HcsIPAddressPool                          |             |                     | Legt den Kubernetes-VM-Mac-Adresspool fest.       |
| Get-HcsKubeClusterInfo                 |             | J                   | Ändert Kubernetes-Pod- und Dienstsubnetze, bevor Sie Compute über das Azure-Portal auf Ihrem Gerät konfigurieren.                                                                                  |
| Remove-HcsKubernetesAzureArcAgent                |             | J                   | Ermöglicht Azure Arc im Kubernetes-Cluster, indem der Arc-Agent auf Ihrem Gerät installiert wird. |
| Set-HcsKubernetesAzureArcDataController       |             | J                   | Erstellt einen Datencontroller im Kubernetes-Cluster auf Ihrem Gerät. |
| Get-HcsKubernetesAzureMonitorConfiguration    |             | J                   | Entfernt den Azure Monitor aus dem Kubernetes-Cluster auf Ihrem Gerät und ermöglicht Ihnen das Sammeln von Container-Protokollen und Prozessorwerten aus dem Kubernetes-Cluster.|
| Set-HcsKubernetesContainerRegistry            |             | J                   | Aktiviert die Edge-Container-Registrierung als Add-On für Ihr Gerät.                                                                                                                                  |
| Get-HcsMacAddressPool                         |             | J                   | Legt den Kubernetes-VM-Mac-Adresspool fest. |
| Get-HcsNetBmcInterface                        |             | J                   | Aktiviert oder deaktiviert die DHCP-Konfiguration für BMC. |
| Set-HcsNetInterface                           |             | J                   | Konfigurieren Sie die IP-Adresse, die Subnetzmaske und das Gateway für eine Netzwerkschnittstelle auf Ihrem Azure-Stack-Edge-Gerät.|
| Set-HcsSmbServerEncryptionConfiguration       |             | J                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsSmbSigningConfiguration                |             | J                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsUpdateConfiguration                    |             | J                   | Legt die Update-Server-Einstellungen für Ihr Gerät fest, einschließlich des Servertyps und des URI-Pfads zum Server. Sie können Updates von einem Microsoft-Update-Server oder von dem Windows-Server-Update-Services-(WSUS)-Server installieren.   |
| Add-HcsVirtualNetwork                         |             | J                   |Erstellt das virtuelle Netzwerk und das Subnetz, das einem Switch zugeordnet ist, den Sie auf Ihrem Gerät erstellt haben. |
| Set-HcsVpnS2SInterface                        |             | J                   | <!--VPN should be P2S-->  |
| Start-HcsGpuMPS                               |             | J                   | Aktiviert den Multiprozessordienst (MpS) auf Ihrem Gerät.|
| Get-HcsSupportPackageUploadJob              | J           | J                   | Erfasst ein Support-Paket mit allen Protokollen und lädt das Paket hoch, damit Microsoft es zum Debuggen von Problemen mit Ihrem Gerät verwenden kann.|
| Start-HcsUpdateJob                            |             | J                   | Startet den Update-Vorgang.|
| Stop-HcsGpuMPS                                |             | J                   | Aktiviert den Multiprozessordienst (MpS) auf Ihrem Gerät. |
| Test-HcsKubernetesStatus                      | J           | J                   | Führt den Kubernetes-Diagnosecontainer aus. |

#### <a name="generic-network-cmdlets"></a>Generische Netzwerk-cmdlets

| Cmdlets   | Diagnose | Diagnose & Reparatur | BESCHREIBUNG                                                                                                                            |
|---------------------------|-------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Find-NetRoute             | J           | J                    | Sucht die beste lokale IP-Adresse und die beste Route, um eine Remoteadresse zu erreichen.                                                          |
| Get-NetAdapter            | J           | J                    | Ruft die grundlegenden Netzwerkadaptereigenschaften ab.                                                                                             |
| Get-NetIPAddress          | J           | J                    | Ruft die gesamte IP-Adresskonfiguration für den Computer ab.                                                                             |
| Get-NetNat                | J           | J                    | Ruft NAT-Objekte (Network Address Translation) ab, die auf einem Computer konfiguriert sind.                                                               |
| Get-NetNatExternalAddress | J           | J                    | Ruft eine Liste externer Adressen ab, die für eine NAT-Instanz (Network Address Translation) konfiguriert sind.                                          |
| Get-NetRoute              | J           | J                    | Ruft IP-Routeninformationen aus der IP-Routingtabelle ab, einschließlich Zielnetzwerkpräfixe, IP-Adressen des nächsten Hops und Routenwerte. |
| Get-NetCompartment        | J           | J                    | Ruft Netzwerk-Compartments im Protokollstapel ab.                                                                                        |
| Get-NetNeighbor           | J           | J                    | Ruft benachbarte Cacheeinträge ab.                                                                                                           |
| Get-NetAdapterSriov       | J           | J                    | Ruft die SR-IOV-Eigenschaften des Netzwerkadapters ab.                                                                                     |
| Resolve-DnsName           |             | J                    | Führt eine DNS-Namensabfrageauflösung für den angegebenen Namen aus.                                                                           |


####  <a name="multi-access-edge-computing-mec-cmdlets"></a>Cmdlets für Multi-Access Edge Computing (MEC)

| Cmdlets | Diagnose | Diagnose & Reparatur | BESCHREIBUNG                                                                                                       |
|-------------------------------------------|-------------|----------------------|-------------------------------------------------------------------------------------------------------------------|
| Get-MecVnf                                | J           | J                    | Ruft eine Liste aller virtuellen Netzwerkfunktions-VMs mit Multizugriff-Edge-Compute-Funktion ab, die in Ihrer Azure Stack Edge angwandt werden.       |
| Get-MecVirtualMachine                     | J           | J                    | Ruft eine Liste der virtuellen Computer ab, die von Funktionen für virtuelle Computenetzwerke mit mehreren Zugriffen erstellt wurden. Funktionen.                    |
| Get-MecServiceConfig                      | J           | J                    | Ruft die Konfiguration des Edge-Computediensts mit mehreren Zugriffen ab, die sich auf die Funktionen des virtuellen Netzwerks auswirkt. Lebenszyklusworkflow. |
| Get-MecInformation                        | J           | J                    | Ruft Edge-Computeinformationen mit mehreren Zugriffen ab. Beispielsweise, ob Ihr Azure-Stack-Edge-Gerät beim Azure-Netzwerkfunktions-Manager registriert wurde.

####  <a name="general-purpose-os-cmdlets"></a>Allgemeine Betriebssystem-cmdlets

| Cmdlets | Diagnose | Diagnose & Reparatur | BESCHREIBUNG                                                                                                                  |
|----------------------------|-------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Select-String              | J           | J                    | Sucht nach Text in Dateien und Zeichenfolgen.                                                                                             |
| Write-Progress             | J           | J                    | Zeigt eine Statusanzeige in einem PowerShell-Befehlsfenster an.                                                                  |
| Get-Command                | J           | J                    | Ruft eine Liste von Befehlen ab, die in einer Sitzung ausgeführt werden können.                                                                        |
| Measure-Object             | J           | J                    | Berechnet die numerischen Eigenschaften von Objekten und die Zeichen, Wörter und Zeilen in Zeichenfolgenobjekten, z. B. Textdateien. |
| Select-Object              | J           | J                    | Wählt Objekte oder Objekteigenschaften.                                                                                        |
| Out-Default                |             | J                    | Sendet die Ausgabe an das Standardformatierungsprogramm und das Standard-Ausgabe-Cmdlet.                                                  |
| Get-WinEvent               | J           | J                    | Ruft Ereignisse aus Ereignisprotokollen und Protokolldateien der Ereignisablaufverfolgung ab.                                                                     |
| Get-Counter                | J           | J                    | Abfragen von Leistungsindikatordaten                                                                                               |
| Abrufen-Volumen                 | J           | J                    | Ruft das angegebene Volume-Objekt oder alle Volumes ab, wenn kein Filter angegeben wird.                                            |
| Get-Service                | J           | J                    | Ruft Objekte ab, die die Dienste darstellen.                                                                                    |

#### <a name="cluster-cmdlets"></a>Cluster-cmdlets 

| Cmdlets       | Diagnose | Diagnose & Reparatur | BESCHREIBUNG                                                                            |
|-----------------------|-------------|----------------------|----------------------------------------------------------------------------------------|
| Abrufen-ClusterResource   | J           | J                    | Ruft Informationen zu einer oder mehreren Ressourcen in einem Failover-Cluster ab.                    |
| Abrufen-Cluster           | J           | J                    | Ruft Informationen zum Failover-Cluster ab.                                               |
| Get-ClusterNode-Cmdlets von       | J           | J                    | Ruft Informationen zu einem oder mehreren Knoten bzw. Servern in einem Failover-Cluster ab:           |
| Cluster starten         |             | J                    | Startet den Clusterdienst auf allen Serverknoten des Clusters, auf dem er noch nicht gestartet wurde. |
| Starten-ClusterResource |             | J                    | Onlineschaltung einer Ressource in einem Failover-Cluster.                                        |
| Stop-ClusterResource  |             | J                    | Offlineschaltung einer Ressource in einem Failover-Cluster.                                        |


#### <a name="hyper-v-cmdlets"></a>Hyper-V-cmdlets

| Cmdlets      | Diagnose | Diagnose & Reparatur | BESCHREIBUNG                                                                                                                                             |
|----------------------|-------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abrufen-VM               | J           | J                    | Ruft die virtuellen Computer auf dem Computer ab.                                                                                                              |
| Abrufen-VMNetworkAdapter | J           | J                    | Ruft die virtuellen Netzwerkadapter eines virtuellen Computers, einer Momentaufnahme, eines Verwaltungsbetriebssystems oder eines virtuellen Computers und eines Verwaltungsbetriebssystems ab. |
| Abrufen-VMFestplatte  | J           | J                    | Ruft die virtuellen Festplatten ab, die einem oder mehreren virtuellen Computern zugeordnet sind.                                                                             |
| Abrufen-VMSchalter         | J           | J                    |  Ruft die Liste der virtuellen Switches ab.                                                                                                                     |

## <a name="next-steps"></a>Nächste Schritte

[Wenden Sie sich an den Microsoft-Support](azure-stack-edge-contact-microsoft-support.md).
