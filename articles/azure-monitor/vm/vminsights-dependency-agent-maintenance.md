---
title: Durchführen eines Upgrades für den VM Insights-Dependency-Agent
description: In diesem Artikel wird beschrieben, wie Sie ein Upgrade des VM Insights-Dependency-Agents über die Befehlszeile, den Setup-Assistenten und mithilfe anderer Methoden durchführen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 282df705e8a98a7c236cfff549447cdd36017df8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431438"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Durchführen eines Upgrades für den VM Insights-Dependency-Agent

Nach der Erstbereitstellung des VM Insights-Dependency-Agents werden Updates veröffentlicht, die Fehlerbehebungen oder die Unterstützung neuer Features oder Funktionen enthalten.  Dieser Artikel enthält Informationen zu den verfügbaren Methoden sowie zur manuellen oder automatisierten Durchführung des Upgrades.

## <a name="upgrade-options"></a>Upgradeoptionen 

Für den Dependency-Agent für Windows und Linux kann je nach Bereitstellungsszenario und Umgebung, in der der Computer ausgeführt wird, ein manuelles oder automatisches Upgrade auf die neueste Version durchgeführt werden. Die folgenden Methoden können zum Aktualisieren des Agents verwendet werden.

|Environment |Installationsmethode |Aktualisierungsmethode |
|------------|--------------------|---------------|
|Azure VM | VM-Erweiterung für den Dependency-Agent für [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) und [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Der Agent wird standardmäßig automatisch aktualisiert, es sei denn, Sie haben Ihre Azure Resource Manager-Vorlage so konfiguriert, dass sie sich abmeldet, indem Sie die Eigenschaft *autoUpgradeMinorVersion* auf **false** setzen. Das Upgrade für Nebenversionen, bei denen das automatische Upgrade deaktiviert ist, und das Upgrade für Hauptversionen werden anhand derselben Methode durchgeführt: Deinstallation und erneute Installation der Erweiterung. |
| Benutzerdefinierte Azure VM-Images | Manuelles Installieren des Dependency-Agents für Windows/Linux | Das Aktualisieren von VMs auf die neueste Version des Agents muss über die Befehlszeile mit dem Windows-Installationspaket oder dem selbstextrahierenden und installierbaren Shellskript-Bundle von Linux erfolgen.|
| Nicht-Azure-VMs | Manuelles Installieren des Dependency-Agents für Windows/Linux | Das Aktualisieren von VMs auf die neueste Version des Agents muss über die Befehlszeile mit dem Windows-Installationspaket oder dem selbstextrahierenden und installierbaren Shellskript-Bundle von Linux erfolgen. |

## <a name="upgrade-windows-agent"></a>Aktualisieren des Windows-Agents 

Um den Agent auf einer Windows-VM auf die neueste Version zu aktualisieren, die nicht über die VM-Erweiterung für den Dependency-Agent installiert wurde, erfolgt die Ausführung entweder über die Eingabeaufforderung, das Skript oder eine andere Automatisierungslösung oder über den Setup-Assistenten „InstallDependencyAgent-Windows.exe“.  

Sie können die neueste Version des Windows-Agents [hier](https://aka.ms/dependencyagentwindows) herunterladen.

### <a name="using-the-setup-wizard"></a>Verwenden des Setup-Assistenten

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie **InstallDependencyAgent-Windows. exe** aus, um den Setup-Assistenten zu starten.
   
3. Folgen Sie den Anweisungen im **Dependency-Agent-Setup**-Assistenten, um die vorherige Version des Dependency-Agents zu deinstallieren und dann die neueste Version zu installieren.


### <a name="from-the-command-line"></a>Über die Befehlszeile

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie den folgenden Befehl aus.

    ```cmd
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Der Parameter `/RebootMode=manual` verhindert, dass bei einem Upgrade der Computer automatisch neu gestartet wird, wenn einige Prozesse Dateien der Vorgängerversion verwenden und diese gesperrt sind. 

3. Um zu überprüfen, ob das Upgrade erfolgreich durchgeführt wurde, suchen Sie in `install.log` nach detaillierten Setupinformationen. Das Protokollverzeichnis ist *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Aktualisieren des Linux-Agents 

Ein Upgrade von früheren Versionen des Dependency-Agents unter Linux wird unterstützt und mit dem gleichen Befehl wie eine Neuinstallation durchgeführt.

Sie können die neueste Version des Linux-Agents [hier](https://aka.ms/dependencyagentlinux) herunterladen.

1. Melden Sie sich auf dem Computer mit einem Konto an, das über Administratorrechte verfügt.

2. Führen Sie den folgenden Befehl als root aus.

    ```bash
    InstallDependencyAgent-Linux64.bin -s
    ```

Falls der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Für Linux-Agents lautet das Protokollverzeichnis */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Überwachung Ihrer VMs für einen bestimmten Zeitraum beenden oder VM Insights vollständig entfernen möchten, finden Sie Informationen dazu unter [Deaktivieren der Überwachung Ihrer virtuellen Computer in VM Insights](../vm/vminsights-optout.md).
