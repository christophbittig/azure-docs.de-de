---
title: Aktivieren von Azure Monitor für eine Hybridumgebung
description: In diesem Artikel wird beschrieben, wie Sie VM Insights für eine Hybridcloudumgebung aktivieren, die einen oder mehrere virtuelle Computer enthält.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: f9f49cf1fa651f1e52c4abbb84e7f8f7840fbb9d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373230"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>Aktivieren von VM Insights für eine Hybrid-VM
In diesem Artikel wird beschrieben, wie Sie VM Insights für einen virtuellen Computer außerhalb von Azure aktivieren, einschließlich lokaler und anderer Cloudumgebungen.

> [!IMPORTANT]
> Bei der empfohlenen Methode zum Aktivieren von Hybrid-VMs wird zunächst [Azure Arc für Server](../../azure-arc/servers/overview.md) aktiviert, damit die VMs mithilfe von Prozessen, die Azure-VMs ähneln, für VM Insights aktiviert werden können. In diesem Artikel wird beschrieben, wie Sie Hybrid-VMs integrieren, falls Sie sich gegen die Verwendung von Azure Arc entschieden haben.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen und konfigurieren Sie einen Log Analytics-Arbeitsbereich](./vminsights-configure-workspace.md).
- Beachten Sie die Informationen unter [Unterstützte Betriebssysteme](./vminsights-enable-overview.md#supported-operating-systems), um sicherzustellen, dass das Betriebssystem der VM bzw. der VM-Skalierungsgruppe, die Sie aktivieren, unterstützt wird. 


## <a name="overview"></a>Übersicht
Virtuelle Computer außerhalb von Azure erfordern denselben Log Analytics-Agent und Dependency-Agent, die für Azure-VMs verwendet werden. Da Sie die Agents nicht mithilfe von VM-Erweiterungen installieren können, müssen Sie sie manuell im Gastbetriebssystem installieren oder mithilfe einer andere Methode installieren lassen. 

Ausführliche Informationen zum Bereitstellen des Log Analytics-Agents finden Sie unter [Verbinden von Windows-Computern mit Azure Monitor](../agents/agent-windows.md) oder [Verbinden von Linux-Computern mit Azure Monitor](../agents/agent-linux.md). Ausführliche Informationen zum Dependency-Agent sind in diesem Artikel enthalten. 

## <a name="firewall-requirements"></a>Firewallanforderungen
Die Firewallanforderungen für den Log Analytics-Agent finden Sie in der [Übersicht über den Log Analytics-Agent](../agents/log-analytics-agent.md#network-requirements). Der Dependency-Agent für das Zuordnungsfeature von VM Insights überträgt selbst keine Daten und erfordert keine Änderungen an Firewalls oder Ports. Die Zuordnungsdaten werden immer vom Log Analytics-Agent an den Azure Monitor-Dienst übertragen, entweder direkt oder über das [Operations Management Suite-Gateway](../../azure-monitor/agents/gateway.md), falls Ihre IT-Sicherheitsrichtlinien es nicht zulassen, dass Computer im Netzwerk eine Verbindung mit dem Internet herstellen.


## <a name="dependency-agent"></a>Abhängigkeits-Agent

>[!NOTE]
>Die in diesem Abschnitt beschriebenen Informationen gelten auch für die [Dienstzuordnungslösung](./service-map.md).  

Sie können den Dependency-Agent über die folgenden Speicherorte herunterladen:

| Datei | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.12.18430 | 9CE3B53D3A67A2C3239E1162364BF94B772764B4ADD78C48559E56F46B98C484  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.12.18430 | 04BD3D2F449220B19DD1DA47A6995087123140B13E45747C743BAD79A312ACE6 |


## <a name="install-the-dependency-agent-on-windows"></a>Installieren des Dependency-Agents unter Windows

Sie können den Dependency-Agent durch Ausführen von `InstallDependencyAgent-Windows.exe` manuell auf Windows-Computern installieren. Wenn Sie diese ausführbare Datei ohne Optionen ausführen, wird ein Setup-Assistent gestartet, mit dem Sie die Installation des Agent interaktiv durchführen können. Zum Installieren oder Deinstallieren des Agent benötigen Sie *Administratorrechte* für das Gastbetriebssystem.

In der folgenden Tabelle sind die Parameter hervorgehoben, die von Setup für den Agent auf der Befehlszeile unterstützt werden.

| Parameter | BESCHREIBUNG |
|:--|:--|
| /? | Gibt eine Liste der Befehlszeilenoptionen zurück. |
| /S | Führt eine automatische Installation ohne Benutzereingriff aus. |

Um das Installationsprogramm beispielsweise mit dem Parameter `/?` auszuführen, geben Sie **InstallDependencyAgent-Windows.exe /?** ein.

Dateien für den Dependency-Agent für Windows werden standardmäßig in *C:\Programme\Microsoft Dependency Agent* installiert. Falls der Dependency-Agent nach Abschluss des Setups nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Das Protokollverzeichnis ist *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="powershell-script"></a>PowerShell-Skript
Verwenden Sie das folgende PowerShell-Beispielskript, um den Agent herunterzuladen und zu installieren:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Installieren des Dependency-Agents unter Linux

Der Dependency-Agent wird auf Linux-Servern mit *InstallDependencyAgent-Linux64.bin* installiert, einem Shellskript mit einer selbstextrahierenden Binärdatei. Sie können die Datei mit `sh` ausführen oder der Datei selbst Ausführungsberechtigungen hinzufügen.

>[!NOTE]
> Zum Installieren oder Konfigurieren des Agent ist Root-Zugriff erforderlich.
>

| Parameter | BESCHREIBUNG |
|:--|:--|
| -help | Ruft eine Liste der Befehlszeilenoptionen ab. |
| -S | Führt eine automatische Installation ohne Benutzereingaben aus. |
| --check | Überprüft Berechtigungen und das Betriebssystem, ohne den Agent zu installieren. |

Um das Installationsprogramm beispielsweise mit dem Parameter `-help` auszuführen, geben Sie **InstallDependencyAgent-Linux64.bin -help** ein. Installieren Sie den Dependency-Agent für Linux mit Root-Berechtigungen, indem Sie den Befehl `sh InstallDependencyAgent-Linux64.bin` ausführen.

Falls der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Für Linux-Agents lautet das Protokollverzeichnis */var/opt/microsoft/dependency-agent/log*.

Dateien für den Dependency-Agent werden in den folgenden Verzeichnissen gespeichert:

| Dateien | Standort |
|:--|:--|
| Hauptdateien | /opt/microsoft/dependency-agent |
| Protokolldateien | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsdateien | /etc/opt/microsoft/dependency-agent/config |
| Ausführbare Dienstdateien | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binäre Speicherdateien | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Shellskript 
Verwenden Sie das folgende Shellskriptbeispiel, um den Agent herunterzuladen und zu installieren:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Zum Bereitstellen des Dependency-Agents über Desired State Configuration (DSC) können Sie das Modul „xPSDesiredStateConfiguration“ mit dem folgenden Beispielcode verwenden:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>Problembehandlung

### <a name="vm-doesnt-appear-on-the-map"></a>Der virtuelle Computer wird auf der Karte nicht angezeigt

Wenn die Installation des Dependency-Agents erfolgreich war, der Computer jedoch nicht auf der Karte angezeigt wird, diagnostizieren Sie das Problem, indem Sie die folgenden Schritte ausführen.

1. Wurde der Dependency-Agent erfolgreich installiert? Überprüfen Sie, ob der Dienst installiert wurde und ausgeführt wird.

    **Windows**: Suchen Sie nach dem Dienst „Microsoft Dependency-Agent“.

    **Linux:** Suchen Sie nach dem laufenden Prozess „microsoft-dependency-agent“.

2. Nutzen Sie den [Tarif „Free“ von Log Analytics](../insights/solutions.md)? Der kostenlose Plan („Free“) lässt bis zu fünf eindeutige Computer zu. Alle weiteren Computer werden auf der Karte nicht angezeigt, selbst wenn die vorherigen fünf keine Daten mehr senden.

3. Sendet der Computer Protokoll- und Leistungsdaten an Azure Monitor-Protokolle? Führen Sie die folgende Abfrage für Ihren Computer aus:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Hat sie ein oder mehrere Ergebnisse zurückgegeben? Sind die Daten aktuell? Wenn dies der Fall ist, funktioniert Ihr Log Analytics-Agent ordnungsgemäß und kommuniziert mit dem Dienst. Wenn nicht, überprüfen Sie den Agent auf dem Server: [Behandeln von Problemen mit dem Log Analytics-Agent für Windows](../agents/agent-windows-troubleshoot.md) oder [Behandeln von Problemen beim Linux-Agent für Log Analytics](../agents/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Der Computer wird auf der Karte angezeigt, enthält aber keine Prozesse

Wenn Ihr Server auf der Karte angezeigt wird, aber keine Prozess- oder Verbindungsdaten enthält, weist dies darauf hin, dass der Dependency-Agent installiert ist und ausgeführt wird, der Kerneltreiber aber nicht geladen wurde.

Überprüfen Sie die Datei „C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log“ (Windows) bzw. die Datei „/var/opt/microsoft/dependency-agent/log/service.log“ (Linux). Die letzten Zeilen der Datei sollten den Grund angeben, warum der Kernel nicht geladen wurde. Beispielsweise, weil der Kernel nicht unterstützt wird, was unter Linux nach der Aktualisierung des Kernels auftreten kann.


## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihre virtuellen Computer aktiviert wurde, stehen diese Informationen nun für die Analyse mit VM Insights zur Verfügung.

- Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Anzeigen der Zuordnung in VM Insights](vminsights-maps.md).

- Informationen zum Erkennen von Engpässen und der Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [Anzeigen der Leistung von Azure-VMs](vminsights-performance.md).
