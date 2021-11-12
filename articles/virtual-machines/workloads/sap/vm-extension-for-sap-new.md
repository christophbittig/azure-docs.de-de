---
title: Neue Version der Azure-VM-Erweiterung für SAP-Lösungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die neue VM-Erweiterung für SAP bereitstellen.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: ce34b313661106e903a92aaf1a3b2f65213a1634
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577259"
---
# <a name="new-version-of-azure-vm-extension-for-sap-solutions"></a>Neue Version der Azure-VM-Erweiterung für SAP-Lösungen 
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[std-extension]:vm-extension-for-sap-standard.md (Standardversion der Azure-VM-Erweiterung für SAP-Lösungen)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Konfigurieren der neuen Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell)
[troubleshoot-windows]:vm-extension-for-sap-new.md#dee9099b-7b8a-4cdd-86a2-3f6ee964266f (Problembehandlung für Windows)
[troubleshoot-linux]:vm-extension-for-sap-new.md#02783aa4-5443-43f5-bc11-7af19ebf0c36 (Problembehandlung für Linux)
[deployment-guide-4.1]:vm-extension-for-sap-new.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Bereitstellen von Azure PowerShell-Cmdlets)
[azure-cli-2]:/cli/azure/install-azure-cli
[configure-linux]:vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481 (Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell)
[health-check]:vm-extension-for-sap-new.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Integritätsprüfungen)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[readiness-check]:vm-extension-for-sap-new.md#5774c1db-1d3c-4b34-8448-3afd0b0f18ab (Bereitschaftsprüfung)

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Allgemeine Aussage zum Support: Unterstützung für die Azure-Erweiterung für SAP wird über SAP-Supportkanäle bereitgestellt.
> Wenn Sie Unterstützung bei der Azure-VM-Erweiterung für SAP-Lösungen benötigen, öffnen Sie einen Supportfall beim SAP-Support.
  
> [!NOTE]
> Deinstallieren Sie unbedingt die VM-Erweiterung, bevor Sie von der Standard- auf die neue Version der Azure-Erweiterung für SAP umsteigen.

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Artikel wird die **neue** Version der Azure-VM-Erweiterung für SAP behandelt. Anleitungen zum Installieren der Standardversion finden Sie unter [Standardversion der Azure-VM-Erweiterung für SAP-Lösungen][std-extension].

* Stellen Sie sicher, dass Sie SAP Host Agent 7.21 PL 47 oder höher verwenden.
* Stellen Sie sicher, dass der virtuelle Computer, auf dem die Erweiterung aktiviert ist, Zugriff auf management.azure.com besitzt.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Bereitstellen von Azure PowerShell-Cmdlets

Führen Sie die Schritte aus, die im Artikel [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) beschrieben werden.

Prüfen Sie regelmäßig, ob Updates für die PowerShell-Cmdlets vorhanden sind. Normalerweise wird einmal im Monat eine Aktualisierung durchgeführt. Führen Sie die in [diesem](/powershell/azure/install-az-ps#update-the-azure-powershell-module) Artikel beschriebenen Schritte aus. Sofern in SAP-Hinweis [1928533] oder SAP-Hinweis [2015553] nicht anders angegeben, empfehlen wir Ihnen die Verwendung der jeweils aktuellen Version der Azure PowerShell-Cmdlets.

Führen Sie diesen PowerShell-Befehl aus, um die Version der Azure PowerShell-Cmdlets zu überprüfen, die auf Ihrem Computer installiert ist:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Bereitstellen der Azure-Befehlszeilenschnittstelle

Führen Sie die Schritte aus, die im Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben werden.

Prüfen Sie regelmäßig, ob Updates für die Azure-Befehlszeilenschnittstelle vorhanden sind. Normalerweise wird einmal im Monat eine Aktualisierung durchgeführt.

Führen Sie diesen Befehl aus, um zu prüfen, welche Version der Azure-Befehlszeilenschnittstelle auf Ihrem Computer installiert ist:

```console
az --version
```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="a876ee7b-43b4-4782-aa5f-73753b6af0ea"></a>Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell
 
Die neue VM-Erweiterung für SAP verwendet eine verwaltete Identität, die der VM zugewiesen ist, um auf Überwachungs- und Konfigurationsdaten der VM zuzugreifen. Um die neue Azure-Erweiterung für SAP mithilfe von PowerShell zu installieren, müssen Sie der VM zunächst eine solche Identität zuweisen und dieser Identität Zugriff auf alle Ressourcen gewähren, die von dieser VM verwendet werden (z. B. Datenträger und Netzwerkschnittstellen).

> [!NOTE]
> Die folgenden Schritte erfordern Besitzerberechtigungen für die Ressourcengruppe oder einzelne Ressourcen (VM, Datenträger und Netzwerkschnittstellen).

1. Stellen Sie sicher, dass Sie SAP Host Agent 7.21 PL 47 oder höher verwenden.
1. Stellen Sie sicher, dass Sie die Standardversion der VM-Erweiterung für SAP deinstallieren. Die Installation beider Versionen der VM-Erweiterung für SAP auf demselben virtuellen Computer wird nicht unterstützt.
1. Achten Sie darauf, die aktuelle Version der Azure PowerShell-Cmdlets (mindestens 4.3.0) zu verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1].
1. Führen Sie das folgende PowerShell-Cmdlet aus.
    Führen Sie das Cmdlet `Get-AzEnvironment` aus, um eine Liste der verfügbaren Umgebungen zu erhalten. Falls Sie öffentliches Azure verwenden möchten, lautet die zugehörige Umgebung **AzureCloud**. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

    Die VM-Erweiterung für SAP unterstützt die Konfiguration eines Proxys, den die Erweiterung verwenden soll, um sich mit externen Ressourcen zu verbinden, zum Beispiel mit der Azure Resource Manager API. Bitte verwenden Sie den Parameter -ProxyURI, um den Proxy zu setzen.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="fa4428b9-bed6-459a-9dfb-74cc27454481"></a>Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI
 
Die neue VM-Erweiterung für SAP verwendet eine verwaltete Identität, die der VM zugewiesen ist, um auf Überwachungs- und Konfigurationsdaten der VM zuzugreifen.

> [!NOTE]
> Die folgenden Schritte erfordern Besitzerberechtigungen für die Ressourcengruppe oder einzelne Ressourcen (VM, Datenträger usw.).

1. Stellen Sie sicher, dass Sie Version 7.21 PL 47 oder höher des SAP-Host-Agents verwenden.
1. Deinstallieren Sie die aktuelle Version der VM-Erweiterung für SAP. Es ist nicht möglich, beide Versionen der VM-Erweiterung für SAP auf derselben VM zu installieren. 
1. Installieren Sie die neueste Version von [Azure CLI 2.0][azure-cli-2] (Version 2.19.1 oder höher).
1. Melden Sie sich mit Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

1. Installieren Sie die Azure CLI-AEM-Erweiterung. Stellen Sie sicher, dass Sie Version 0.2.2 oder höher verwenden.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Aktivieren Sie die neue Erweiterung:
  
   Die VM-Erweiterung für SAP unterstützt die Konfiguration eines Proxys, den die Erweiterung verwenden soll, um sich mit externen Ressourcen zu verbinden, zum Beispiel mit der Azure Resource Manager API. Bitte verwenden Sie den Parameter --proxy-uri, um den Proxy zu setzen.

   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```
 
 
## <a name="readiness-check"></a><a name="5774c1db-1d3c-4b34-8448-3afd0b0f18ab"></a>Bereitschaftsprüfung

Mit dieser Prüfung wird sichergestellt, dass alle Leistungsmetriken, die in Ihrer SAP-Anwendung angezeigt werden, über die zugrunde liegende Azure-Erweiterung für SAP bereitgestellt werden.

### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Windows-Computer

1. Melden Sie sich am virtuellen Azure-Computer an (die Verwendung eines Administratorkontos ist nicht erforderlich).
1. Öffnen Sie einen Webbrowser, und navigieren Sie zu http://127.0.0.1:11812/azure4sap/metrics.
1. Der Browser sollte eine XML-Datei anzeigen oder herunterladen, die die Überwachungsdaten des virtuellen Computers enthält. Falls nicht, vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist.
1. Überprüfen Sie den Inhalt der XML-Datei. Die XML-Datei, auf die Sie unter http://127.0.0.1:11812/azure4sap/metrics zugreifen können, enthält alle gefüllten Azure-Leistungsindikatoren für SAP. Außerdem enthält sie eine Zusammenfassung und einen Integritätsindikator für den Status der Azure-Erweiterung für SAP.
1. Überprüfen Sie den Wert des Elements **Provider Health Description**. Wenn der Wert nicht **OK** lautet, folgen Sie den Anweisungen im Kapitel [Integritätsprüfungen][health-check].
 
### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Linux-Computer

1. Stellen Sie per SSH eine Verbindung mit der Azure-VM her.
1. Überprüfen Sie die Ausgabe des folgenden Befehls.
   ```bash
   curl http://127.0.0.1:11812/azure4sap/metrics
   ```
   **Erwartetes Ergebnis**: Rückgabe eines XML-Dokuments, das die Überwachungsinformationen für den virtuellen Computer, seine Datenträger und die Netzwerkschnittstellen enthält.
   1. Stellen Sie per SSH eine Verbindung mit der Azure-VM her.

1. Überprüfen Sie die Ausgabe des folgenden Befehls.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Erwartetes Ergebnis**: Rückgabe eines XML-Dokuments, das die Überwachungsinformationen für den virtuellen Computer, seine Datenträger und die Netzwerkschnittstellen enthält.

Führen Sie diese zusätzliche Überprüfungen durch, wenn die vorherige Überprüfung nicht erfolgreich war:

1. Achten Sie darauf, dass waagent installiert und aktiviert wurde.

   a.  Ausführen von `sudo ls -al /var/lib/waagent/`

     **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses „waagent“ auf.

   b.  Ausführen von `ps -ax | grep waagent`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /usr/sbin/waagent -daemon`

1. Vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist und ausgeführt wird.

   a.  Ausführen von `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses für die Azure-Erweiterung für SAP auf.

   b. Ausführen von `ps -ax | grep AzureEnhanced`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Installieren Sie den SAP Host Agent gemäß SAP-Hinweis [1031096], und überprüfen Sie die Ausgabe von `saposcol`.

   a.  Ausführen von `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Ausführen von `dump ccm`

   c.  Überprüfen Sie, ob für die Metrik **Virtualization_Configuration\Enhanced Monitoring Access** der Wert **true** angegeben ist.

Wenn bereits ein SAP NetWeaver ABAP-Anwendungsserver installiert ist, öffnen Sie die Transaktion ST06, und überprüfen Sie, ob die Überwachung aktiviert ist.

Falls eine dieser Überprüfungen einen Fehler verursacht und Sie ausführliche Informationen zur Bereitstellung der Erweiterung benötigen, lesen Sie [Problembehandlung für Windows][troubleshoot-windows] oder [Problembehandlung für Linux][troubleshoot-linux].
 
## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Integritätsprüfungen

Wenn einige Infrastrukturdaten gemäß der Beschreibung des Tests unter [Bereitschaftsprüfung][readiness-check] nicht richtig bereitgestellt werden, führen Sie die in diesem Kapitel beschriebenen Integritätsprüfungen aus, um zu überprüfen, ob die Azure-Infrastruktur und die Azure-Erweiterung für SAP ordnungsgemäß konfiguriert sind.

### <a name="health-checks-using-powershell"></a>Integritätsprüfungen mithilfe von PowerShell

1. Achten Sie darauf, dass die neueste Version des Azure PowerShell-Cmdlets gemäß [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1] installiert ist.
1. Führen Sie das folgende PowerShell-Cmdlet aus. Führen Sie zum Anzeigen der Liste mit den verfügbaren Umgebungen das `Get-AzEnvironment`-Cmdlet aus. Wählen Sie die Umgebung **AzureCloud** aus, um globales Azure zu verwenden. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```
1. Mit dem Skript wird die Konfiguration des von Ihnen ausgewählten virtuellen Computers getestet.

Stellen Sie sicher, dass das Ergebnis für jede Integritätsprüfung **OK** lautet. Wenn einige Prüfungen nicht **OK** anzeigen, führen Sie das Aktualisierungs-Cmdlet wie unter [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben aus. Wiederholen Sie die unter [Bereitschaftsprüfung][readiness-check] und in diesem Kapitel beschriebenen Überprüfungen. Wenn die Prüfungen immer noch ein Problem mit einigen oder allen Leistungsindikatoren anzeigen, lesen Sie [Problembehandlung für Linux][troubleshoot-linux] oder [Problembehandlung für Windows][troubleshoot-windows].

### <a name="health-checks-using-azure-cli"></a>Integritätsprüfungen mit der Azure CLI

So führen Sie die Integritätsprüfung für die Azure-VM-Erweiterung für SAP mithilfe der Azure CLI aus:
 
1. Installieren Sie [Azure CLI 2.0][azure-cli-2]. Stellen Sie sicher, dass Sie mindestens Version 2.19.1 oder höher verwenden (die aktuelle Version). 
1. Melden Sie sich mit Ihrem Azure-Konto an:
   ```azurecli
   az login
   ```

1. Installieren Sie die Azure CLI-AEM-Erweiterung. Stellen Sie sicher, dass Sie Version 0.2.2 oder höher verwenden.
   ```azurecli
   az extension add --name aem
   ```

1. Überprüfen Sie die Installation der Erweiterung: 
   ```azurecli
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
Mit dem Skript wird die Konfiguration des von Ihnen ausgewählten virtuellen Computers getestet.

Stellen Sie sicher, dass das Ergebnis für jede Integritätsprüfung **OK** lautet. Wenn einige Prüfungen nicht **OK** anzeigen, führen Sie das Aktualisierungs-Cmdlet wie unter [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben aus. Wiederholen Sie die unter [Bereitschaftsprüfung][readiness-check] und in diesem Kapitel beschriebenen Überprüfungen. Wenn die Prüfungen immer noch ein Problem mit einigen oder allen Leistungsindikatoren anzeigen, lesen Sie [Problembehandlung für Linux][troubleshoot-linux] oder [Problembehandlung für Windows][troubleshoot-windows].


## <a name="troubleshooting-for-windows"></a><a name="dee9099b-7b8a-4cdd-86a2-3f6ee964266f"></a>Problembehandlung für Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Es werden keinerlei Azure-Leistungsindikatoren angezeigt.
Mit dem Prozess AzureEnhancedMonitoring werden in Azure Leistungsmetriken gesammelt. Wenn der Prozess in Ihrer VM nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.
##### <a name="issue"></a>Problem
Das Installationsverzeichnis „C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;Version>“ ist leer.
##### <a name="solution"></a>Lösung
Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Möglicherweise müssen Sie den Computer neu starten oder die VM-Erweiterung erneut installieren.
 
### <a name="some-azure-performance-counters-are-missing"></a>Einige Azure-Leistungsindikatoren fehlen.

Mit dem Windows-Prozess AzureEnhancedMonitoring werden in Azure Leistungsmetriken gesammelt. Mit dem Prozess werden Daten aus mehreren Quellen abgerufen. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus Azure Monitor ausgelesen.

Falls die Problembehandlung mit SAP-Hinweis [1999351] das Problem nicht löst, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer. Fügen Sie die Protokolldatei „C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;Version>\\logapp.txt“ dem Incident an.

## <a name="troubleshooting-for-linux"></a><a name="02783aa4-5443-43f5-bc11-7af19ebf0c36"></a>Problembehandlung für Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Es werden keinerlei Azure-Leistungsindikatoren angezeigt.
Leistungsmetriken werden in Azure über einen Daemon erfasst. Wenn der Daemon nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.
##### <a name="issue"></a>Problem
Das Verzeichnis „/var/lib/waagent/“ enthält kein Unterverzeichnis für die Azure-Erweiterung für SAP.
##### <a name="solution"></a>Lösung
Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Möglicherweise müssen Sie den Computer neu starten und/oder die VM-Erweiterung erneut installieren.
 
### <a name="some-azure-performance-counters-are-missing"></a>Einige Azure-Leistungsindikatoren fehlen.

Leistungsmetriken werden in Azure über einen Daemon erfasst, der Daten aus mehreren Quellen abruft. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus Azure Monitor ausgelesen.
Eine vollständige und aktuelle Liste mit bekannten Problemen finden Sie im SAP-Hinweis [1999351], der zusätzliche Informationen zur Problembehandlung für die Azure-Erweiterung für SAP enthält.
Falls die Problembehandlung mit SAP-Hinweis [1999351] das Problem nicht löst, installieren Sie die Erweiterung wie unter [Konfigurieren der Azure-Erweiterung für SAP][configure-windows] beschrieben erneut. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer. Fügen Sie die Protokolldatei „/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;Version>logapp.txt“ dem Incident an.


## <a name="azure-extension-error-codes"></a>Fehlercodes der Azure-Erweiterung
 
Alle Fehler-IDs verfügen über ein eindeutiges Tag in Form von a-#, wobei # eine Zahl ist. Dies ermöglicht eine schnelle Suche nach einem bestimmten Fehler und möglichen Lösungen.
 
| Fehler-ID | Fehlerbeschreibung | Lösungen |
|---|---|---|
| `a-0116` | Kein Authentifizierungstoken | Weitere Informationen:<br />Die Erweiterung kann kein Authentifizierungstoken für den Zugriff auf VM-Metriken in Azure Monitor abrufen. Um VM-Metriken abzurufen, ist Zugriff auf VM-Ressourcen wie die VM selbst, alle Datenträger und alle NiCs erforderlich, die an eine VM angefügt sind.<br />Lösung:<br />Aktivieren Sie die verwaltete VM-Identität, und weisen Sie ihr eine Leserrolle für eine VM-Ressourcengruppe zu. Wenn Sie ein Setupskript verwenden, führt das Skript dies für Sie aus. Normalerweise müssen Sie die verwaltete VM-Identität nicht manuell aktivieren und zuweisen. |

## <a name="next-steps"></a>Nächste Schritte
* [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](./deployment-guide.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md)
