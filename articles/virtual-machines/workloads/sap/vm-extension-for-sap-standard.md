---
title: Standardversion der Azure-VM-Erweiterung für SAP-Lösungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die VM-Standarderweiterung für SAP bereitstellen.
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
ms.openlocfilehash: 8e723e39364207e1075d66eeafcdf12b74e16624
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261384"
---
# <a name="standard-version-of-azure-vm-extension-for-sap-solutions"></a>Standardversion der Azure-VM-Erweiterung für SAP-Lösungen
[new-extension]:vm-extension-for-sap-new.md (Neue Version der Azure-VM-Erweiterung für SAP-Lösungen)
[configure-windows]:vm-extension-for-sap-standard.md#cb095b34-51c9-41f3-aeed-30a16072a1dc (Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell)
[configure-linux]:vm-extension-for-sap-standard.md#c691e304-3524-4bfd-8612-992d5715a689 (Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI)
[configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[troubleshoot-windows]:vm-extension-for-sap-standard.md#f566b77e-5427-451b-b4ca-6b93114d51e8 (Problembehandlung für Windows)
[troubleshoot-linux]:vm-extension-for-sap-standard.md#a4dae567-e7fd-4d4b-8279-510b8e5fae4a (Problembehandlung für Linux)
[healthcheck]:vm-extension-for-sap-standard.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Integritätsprüfung für die Azure-Erweiterung für die SAP-Konfiguration)
[deployment-guide-4.1]:vm-extension-for-sap-standard.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Bereitstellen von Azure PowerShell-Cmdlets)
[deploy-cli]:vm-extension-for-sap-standard.md#1ded9453-1330-442a-86ea-e0fd8ae8cab3 (Bereitstellen der Azure CLI)
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[azure-cli-2]:/cli/azure/install-azure-cli
[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[readiness-check]:vm-extension-for-sap-standard.md#6b2561a0-b476-4579-847d-6c68117eb26e (Bereitschaftsprüfung)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-Ressourcen)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[deployment-guide-contact-support]:vm-extension-for-sap-standard.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Problembehandlung für die Azure-Erweiterung für SAP – Support kontaktieren)
[deployment-guide-run-the-script]:vm-extension-for-sap-standard.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Problembehandlung für die Azure-Erweiterung für SAP – Ausführen des Setups)
[deployment-guide-redeploy-after-sysprep]:vm-extension-for-sap-standard.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Problembehandlung für die Azure-Erweiterung für SAP – erneute Bereitstellung nach Sysprep)
[deployment-guide-fix-internet-connection]:vm-extension-for-sap-standard.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Problembehandlung für die Azure-Erweiterung für SAP – Korrektur der Internetverbindung)



## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Allgemeine Aussage zum Support: Unterstützung für die Azure-Erweiterung für SAP wird über SAP-Supportkanäle bereitgestellt.
> Wenn Sie Unterstützung bei der Azure-VM-Erweiterung für SAP-Lösungen benötigen, öffnen Sie einen Supportfall beim SAP-Support.
  
> [!NOTE]
> Deinstallieren Sie unbedingt die VM-Erweiterung, bevor Sie von der Standard- auf die neue Version der Azure-Erweiterung für SAP umsteigen.

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Artikel wird die **Standardversion** der Azure-VM-Erweiterung für SAP behandelt. Anleitungen zum Installieren der neuen Version finden Sie unter [Neue Version der Azure-VM-Erweiterung für SAP-Lösungen][new-extension].


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


## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="cb095b34-51c9-41f3-aeed-30a16072a1dc"></a>Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell 
 
Gehen Sie wie folgt vor, um die Azure-Erweiterung für SAP mit PowerShell zu installieren:

1. Achten Sie darauf, dass Sie die aktuelle Version der Azure PowerShell-Cmdlets verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1].
1. Führen Sie das folgende PowerShell-Cmdlet aus. Führen Sie das Cmdlet `Get-AzEnvironment` aus, um eine Liste der verfügbaren Umgebungen zu erhalten. Falls Sie öffentliches Azure verwenden möchten, lautet die zugehörige Umgebung **AzureCloud**. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**. 
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   
   Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> 
   ``` 
Nachdem Sie Ihre Kontodaten angegeben haben, stellt das Skript die erforderlichen Erweiterungen bereit und aktiviert die benötigten Features. Dies kann einige Minuten dauern.
Weitere Informationen zu `Set-AzVMAEMExtension` finden Sie unter [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Erfolgreiche Ausführung des SAP-spezifischen Azure-Cmdlets „Set-AzVMAEMExtension“][deployment-guide-figure-900]

Bei der `Set-AzVMAEMExtension`-Konfiguration werden alle Schritte zum Konfigurieren der Hostdatensammlung für SAP ausgeführt.

Die Skriptausgabe enthält die folgenden Informationen:

* Bestätigung, dass die Datensammlung für den Betriebssystemdatenträger und alle weiteren Datenträger für Daten konfiguriert wurde.
* Mit den nächsten beiden Meldungen wird die Konfiguration der Speichermetriken für ein bestimmtes Speicherkonto bestätigt.
* Eine Zeile der Ausgabe gibt den Zustand der laufenden Aktualisierung der Konfiguration der VM-Erweiterung für SAP an.
* In einer anderen Zeile der Ausgabe wird bestätigt, dass die Konfiguration bereitgestellt oder aktualisiert wurde.
* Die letzte Zeile der Ausgabe dient nur zu Informationszwecken. Sie enthält Ihre Optionen für ein Testen der Konfiguration der VM-Erweiterung für SAP.
* Um zu überprüfen, ob alle Schritte der Konfiguration der VM-Erweiterung für SAP erfolgreich ausgeführt wurden und die Azure-Infrastruktur die notwendigen Daten bereitstellt, fahren Sie mit der Bereitschaftsprüfung für die Azure-Erweiterung für SAP fort. Diese wird unter [Bereitschaftsprüfung][readiness-check] beschrieben.
* Warten Sie 15 bis 30 Minuten, bis die relevanten Daten mit der Azure-Diagnose erfasst wurden.

## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="c691e304-3524-4bfd-8612-992d5715a689"></a>Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI
 
Gehen Sie wie folgt vor, um die Azure-VM-Erweiterung für SAP über die Azure CLI zu installieren:

1. Stellen Sie sicher, dass Sie die neueste Version der Azure CLI verwenden. Weitere Informationen finden Sie unter [Bereitstellen der Azure CLI][deploy-cli]. 

1. Melden Sie sich mit Ihrem Azure-Konto an:
   ```azurecli
   az login
   ```

1. Installieren Sie die Azure CLI-AEM-Erweiterung. Stellen Sie sicher, dass Sie Version 0.2.2 oder höher verwenden.
   ```azurecli
   az extension add --name aem
   ```

1. Aktivieren der Erweiterung: 
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> 
   ```

1. Stellen Sie sicher, dass die Azure-Erweiterung für SAP auf dem virtuellen Azure Linux-Computer aktiv ist. Überprüfen Sie, ob die Datei „/var/lib/AzureEnhancedMonitor/PerfCounters“ vorhanden ist. Ist sie vorhanden, führen Sie an einer Befehlseingabeaufforderung den folgenden Befehl aus, um die mit der Azure-Erweiterung für SAP gesammelten Informationen anzuzeigen:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Die Ausgabe sieht wie folgt aus:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualisieren der Konfiguration der Azure-Erweiterung für SAP

Aktualisieren Sie die Konfiguration der Azure-Erweiterung für SAP, wenn eines der folgenden Szenarien zutrifft:
* Das gemeinsame Team von Microsoft und SAP hat die Funktionalität der VM-Erweiterung ausgeweitet, wodurch mehr oder weniger Leistungsindikatoren erforderlich sind.
* Microsoft führt eine neue Version der zugrunde liegenden Azure-Infrastruktur ein, die die Daten bereitstellt, und die Azure-Erweiterung für SAP muss an diese Änderungen angepasst werden.
* Sie binden zusätzliche Datenträger für Daten in den virtuellen Azure-Computer oder entfernen einen Datenträger für Daten. In diesem Fall muss die Sammlung mit den speicherbezogenen Daten aktualisiert werden. Wenn die Konfiguration durch Hinzufügen oder Löschen von Endpunkten oder Zuweisen von IP-Adressen zu einem virtuellen Computer geändert wird, wirkt sich dies nicht auf die Konfiguration der Erweiterung aus.
* Beispielsweise ändern Sie die Größe Ihrer Azure-VM von A5 in eine andere VM-Größe.
* Dem virtuellen Azure-Computer werden neue Netzwerkschnittstellen hinzugefügt.

Aktualisieren Sie zum Aktualisieren der Einstellungen die Konfiguration der Azure-Erweiterung für SAP, indem Sie die Schritte unter [Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] ausführen.

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Überprüfungen und Problembehandlung

Nachdem Sie den virtuellen Azure-Computer bereitgestellt und die entsprechende Azure-Erweiterung für SAP eingerichtet haben, sollten Sie überprüfen, ob alle Komponenten der Erweiterung ordnungsgemäß funktionieren.

Führen Sie die Bereitschaftsprüfung für die Azure-Erweiterung für SAP wie unter [Bereitschaftsprüfung][readiness-check] beschrieben aus. Wenn alle Ergebnisse der Bereitschaftsprüfung positiv ausfallen und alle relevanten Leistungsindikatoren in Ordnung sind, wurde die Azure-Erweiterung für SAP erfolgreich eingerichtet. Sie können mit der Installation des SAP Host Agents fortfahren, wie in den SAP-Hinweisen unter [SAP-Ressourcen][deployment-guide-2.2] beschrieben. Wenn für die Bereitschaftsprüfung das Fehlen von Leistungsindikatoren angezeigt wird, führen Sie den Integritätstest für die Azure-Erweiterung für SAP wie unter [Integritätsprüfung für die Konfiguration der Azure-Erweiterung für SAP][healthcheck] beschrieben aus. Weitere Optionen zur Problembehandlung finden Sie unter [Problembehandlung für Windows][troubleshoot-windows] oder [Problembehandlung für Linux][troubleshoot-linux].

### <a name="readiness-check"></a><a name="6b2561a0-b476-4579-847d-6c68117eb26e"></a>Bereitschaftsprüfung

Mit dieser Prüfung wird sichergestellt, dass alle Leistungsmetriken, die in Ihrer SAP-Anwendung angezeigt werden, über die zugrunde liegende Azure-Erweiterung für SAP bereitgestellt werden.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Windows-Computer
 
1. Melden Sie sich am virtuellen Azure-Computer an (die Verwendung eines Administratorkontos ist nicht erforderlich).
1. Öffnen Sie ein Eingabeaufforderungsfenster.
1. Ändern Sie an der Eingabeaufforderung das Verzeichnis für den Installationsordner der Azure-Erweiterung für SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   Die *Version* im Pfad zur Erweiterung kann variieren. Sollten Ordner für mehrere Versionen der Erweiterung im Installationsordner vorhanden sein, sollten Sie die Konfiguration des Windows-Diensts „AzureEnhancedMonitoring“ überprüfen und dann in den Ordner wechseln, der als *Pfad zur ausführbaren Datei* angegeben ist.

   ![Eigenschaften des Diensts, der die Azure-Erweiterung für SAP ausführt][deployment-guide-figure-1000]

1. Führen Sie an der Befehlseingabeaufforderung die Datei **azperflib.exe** ohne Parameter aus.

   > [!NOTE]
   > Das Programm „azperflib.exe“ wird in einer Schleife ausgeführt und aktualisiert die erfassten Leistungsindikatoren alle 60 Sekunden. Schließen Sie das Eingabeaufforderungsfenster, um die Schleife zu beenden.
   >
   >

Wenn die Azure-Erweiterung für SAP nicht installiert ist oder der Dienst „AzureEnhancedMonitoring“ nicht ausgeführt wird, wurde die Erweiterung nicht richtig konfiguriert. Ausführliche Informationen zur Problembehandlung für die Erweiterung finden Sie unter [Problembehandlung für Windows][troubleshoot-windows] oder [Problembehandlung für Linux][troubleshoot-linux].

> [!NOTE]
> „Azperflib.exe“ kann nicht für eigene Zwecke verwendet werden. Sie ist eine Komponente, die Azure-Infrastrukturdaten in Bezug auf die VM exklusiv für den SAP-Host-Agent bereitstellt.
> 
##### <a name="check-the-output-of-azperflibexe"></a>Überprüfen der Ausgabe von „azperflib.exe“
 
Die Ausgabe von „azperflib.exe“ enthält alle ausgefüllten Azure-Leistungsindikatoren für SAP. Am Ende der Liste mit den gesammelten Indikatoren finden Sie eine Übersicht und einen Integritätsindikator, der den Zustand der Azure-Erweiterung für SAP anzeigt.

![Ausgabe der Integritätsprüfung durch Ausführen von „azperflib.exe“, wenn keine Probleme bestehen][deployment-guide-figure-1100]
<a name="figure-11"></a>

Prüfen Sie das Ergebnis unter **Counters total** (Indikatoren gesamt), das als „Leer“ gemeldet wird, und für den **Integritätsstatus**, wie in der obigen Abbildung gezeigt.

Interpretieren Sie die Ergebniswerte wie folgt:

| Ergebniswerte von „azperflib.exe“ | Integritätsstatus der Azure-Erweiterung für SAP |
| --- | --- |
| **API-Aufrufe – nicht verfügbar** | Leistungsindikatoren, die nicht verfügbar sind, gelten entweder nicht für die Konfiguration des virtuellen Computers oder sind fehlerhaft. Weitere Informationen finden Sie unter **Integritätsstatus**. |
| **Indikatoren gesamt – leer** |Die folgenden beiden Azure Storage-Indikatoren dürfen leer sein: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Alle anderen Leistungsindikatoren müssen Werte enthalten. |
| **Integritätsstatus** |Nur in Ordnung bei Rückgabestatus **OK**. |
| **Diagnose** |Liefert ausführliche Informationen zum Integritätsstatus. |

Wenn für den Wert **Integritätsstatus** nicht **OK** angezeigt wird, hilft Ihnen die Anleitung unter [Integritätsprüfung für die Konfiguration der Azure-Erweiterung für SAP][healthcheck] weiter.
 
##### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Linux-Computer

1. Stellen Sie per SSH eine Verbindung mit der Azure-VM her.
1. Überprüfen Sie die Ausgabe der Azure-Erweiterung für SAP.

   a.  Ausführen von `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Erwartetes Ergebnis**: Gibt die Liste der Leistungsindikatoren zurück. Die Datei sollte nicht leer sein.

   b. Ausführen von `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Erwartetes Ergebnis**: Gibt eine Zeile zurück, für die der Fehler **none** lautet, z.B. **3;config;Error;;0;0;none;0;1456416792;tst-servercs;** .

   c. Ausführen von `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Erwartetes Ergebnis**: Wird als „Leer“ zurückgegeben oder ist nicht vorhanden.

Führen Sie diese zusätzliche Überprüfungen durch, wenn die vorherige Überprüfung nicht erfolgreich war:

1. Achten Sie darauf, dass waagent installiert und aktiviert wurde.

   a.  Ausführen von `sudo ls -al /var/lib/waagent/`

     **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses „waagent“ auf.

   b.  Ausführen von `ps -ax | grep waagent`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /usr/sbin/waagent -daemon`

1. Vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist und ausgeführt wird.

   a.  Ausführen von `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses für die Azure-Erweiterung für SAP auf.

   b. Ausführen von `ps -ax | grep AzureEnhanced`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installieren Sie den SAP Host Agent gemäß SAP-Hinweis [1031096], und überprüfen Sie die Ausgabe von `saposcol`.

   a.  Ausführen von `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Ausführen von `dump ccm`

   c.  Überprüfen Sie, ob für die Metrik **Virtualization_Configuration\Enhanced Monitoring Access** der Wert **true** angegeben ist.

Wenn bereits ein SAP NetWeaver ABAP-Anwendungsserver installiert ist, öffnen Sie die Transaktion ST06, und überprüfen Sie, ob die Überwachung aktiviert ist.

Falls eine dieser Überprüfungen einen Fehler verursacht und Sie ausführliche Informationen zur Bereitstellung der Erweiterung benötigen, lesen Sie [Problembehandlung für Windows][troubleshoot-linux] oder [Problembehandlung für Linux][troubleshoot-windows].

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

   ![Ausgabe eines erfolgreichen Tests der Azure-Erweiterung für SAP][deployment-guide-figure-1300]

Stellen Sie sicher, dass das Ergebnis für jede Integritätsprüfung **OK** lautet. Wenn einige Prüfungen nicht **OK** anzeigen, führen Sie das Aktualisierungs-Cmdlet wie unter [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben aus. Warten Sie 15 Minuten, und wiederholen Sie dann die unter [Bereitschaftsprüfung][readiness-check] und in diesem Kapitel beschriebenen Überprüfungen. Wenn die Prüfungen immer noch ein Problem mit einigen oder allen Leistungsindikatoren anzeigen, lesen Sie [Problembehandlung für Linux][troubleshoot-linux] oder [Problembehandlung für Windows][troubleshoot-windows].

> [!Note]
> Möglicherweise treten einige Warnungen auf, wenn Sie verwaltete Azure-Standarddatenträger verwenden. Die Tests geben nicht „OK“ zurück, stattdessen werden Warnungen angezeigt. Dies ist normal und im Fall dieses Datenträgertyps beabsichtigt. Weitere Informationen finden Sie unter [Problembehandlung für Linux][troubleshoot-linux] oder [Problembehandlung für Windows][troubleshoot-windows].
>

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
   ```azurecliw
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
Mit dem Skript wird die Konfiguration des von Ihnen ausgewählten virtuellen Computers getestet.

Stellen Sie sicher, dass das Ergebnis für jede Integritätsprüfung **OK** lautet. Wenn einige Prüfungen nicht **OK** anzeigen, führen Sie das Aktualisierungs-Cmdlet wie unter [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben aus. Warten Sie 15 Minuten, und wiederholen Sie dann die unter [Bereitschaftsprüfung][readiness-check] und in diesem Kapitel beschriebenen Überprüfungen. Wenn die Prüfungen immer noch ein Problem mit einigen oder allen Leistungsindikatoren anzeigen, lesen Sie [Problembehandlung für Linux][troubleshoot-linux] oder [Problembehandlung für Windows][troubleshoot-windows].

## <a name="troubleshooting-for-windows"></a><a name="f566b77e-5427-451b-b4ca-6b93114d51e8"></a>Problembehandlung für Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Es werden keinerlei Azure-Leistungsindikatoren angezeigt.
Mit dem Windows-Dienst „AzureEnhancedMonitoring“ werden in Azure Leistungsmetriken gesammelt. Wenn der Dienst nicht richtig installiert wurde oder wenn er auf der VM nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.
 
#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.

##### <a name="issue"></a>Problem
Das Installationsverzeichnis „C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\<version>\drop“ ist leer.

##### <a name="solution"></a>Lösung
Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Unter Umständen müssen Sie den Computer neu starten oder das Konfigurationsskript `Set-AzVMAEMExtension` erneut ausführen.

#### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Der Dienst für die Azure-Erweiterung für SAP ist nicht vorhanden.

##### <a name="issue"></a>Problem

Der Windows-Dienst „AzureEnhancedMonitoring“ ist nicht vorhanden.

Mit der Ausgabe von „azperflib.exe“ wird ein Fehler ausgelöst:

![Ausführung von „azperflib.exe“ mit der Angabe, dass der Dienst der Azure-Erweiterung für SAP nicht ausgeführt wird][deployment-guide-figure-1400]
<a name="figure-14"></a>
 
##### <a name="solution"></a>Lösung

Wenn der Dienst nicht vorhanden ist, wurde die Azure-Erweiterung für SAP nicht richtig installiert. Stellen Sie die Erweiterung wie unter [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben bereit.

Überprüfen Sie nach dem Bereitstellen der Erweiterung erneut, ob die Azure-Leistungsindikatoren in der Azure-VM angegeben werden.

#### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Der Dienst für die Azure-Erweiterung für SAP ist vorhanden, startet jedoch nicht.
##### <a name="issue"></a>Problem
Der Windows-Dienst „AzureEnhancedMonitoring“ ist vorhanden und aktiviert, kann aber nicht gestartet werden. Weitere Informationen finden Sie im Anwendungsereignisprotokoll.
##### <a name="solution"></a>Lösung
Die Konfiguration ist fehlerhaft. Starten Sie die Azure-Erweiterung für SAP auf der VM neu, wie dies unter Konfigurieren der Azure-Erweiterung für SAP beschrieben ist.
 
### <a name="some-azure-performance-counters-are-missing"></a>Einige Azure-Leistungsindikatoren fehlen.
Mit dem Windows-Dienst „AzureEnhancedMonitoring“ werden in Azure Leistungsmetriken gesammelt. Mit dem Dienst werden Daten aus mehreren Quellen abgerufen. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus der Azure-Diagnose ausgelesen. Die Speicherindikatoren werden aus Ihrer Protokollierung auf Speicherabonnementebene verwendet.

Führen Sie das Konfigurationsskript `Set-AzVMAEMExtension` erneut aus, falls die Problembehandlung mithilfe von SAP-Hinweis [1999351] nicht zur Lösung des Problems führt. Unter Umständen ist eine Wartezeit von einer Stunde erforderlich, da Speicheranalyse- und Diagnoseindikatoren ggf. nicht sofort nach der Aktivierung erstellt werden. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer.

## <a name="troubleshooting-for-linux"></a><a name="a4dae567-e7fd-4d4b-8279-510b8e5fae4a"></a>Problembehandlung für Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Es werden keinerlei Azure-Leistungsindikatoren angezeigt.
Leistungsmetriken werden in Azure über einen Daemon erfasst. Wenn der Daemon nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.
##### <a name="issue"></a>Problem
Das Verzeichnis „\\var\\lib\\waagent\\“ enthält kein Unterverzeichnis für die Azure-Erweiterung für SAP.
##### <a name="solution"></a>Lösung
Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Unter Umständen müssen Sie den Computer neu starten bzw. das Konfigurationsskript `Set-AzVMAEMExtension` erneut ausführen.

#### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Bei der Ausführung von „Set-AzVMAEMExtension“ und „Test-AzVMAEMExtension“ weisen Warnmeldungen darauf hin, dass verwaltete Standarddatenträger nicht unterstützt werden.
##### <a name="issue"></a>Problem
Bei der Ausführung von „Set-AzVMAEMExtension“ oder „Test-AzVMAEMExtension“ werden Meldungen wie die folgenden angezeigt:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Beim Ausführen von „azperfli.exe“ wie oben beschrieben können Sie ein Ergebnis erzielen, das auf einen fehlerhaften Zustand hinweist.

##### <a name="solution"></a>Lösung
Diese Meldungen werden dadurch verursacht, dass verwaltete Standarddatenträger nicht die APIs bereitstellen, die von der Azure-Erweiterung für SAP verwendet werden, um die Statistiken der Azure Storage-Standardkonten zu überprüfen. Dies ist kein Grund zur Besorgnis. Der Grund für die Einführung des Sammelns von Daten für Disk Storage-Standardkonten war die häufig auftretende Drosselung von Ein- und Ausgaben. Verwaltete Datenträger verhindern eine solche Drosselung, indem die Anzahl von Datenträgern in einem Storage-Konto eingeschränkt wird. Daher ist es kein Problem, wenn dieser Typ von Daten nicht verfügbar ist.

### <a name="some-azure-performance-counters-are-missing"></a>Einige Azure-Leistungsindikatoren fehlen.

Leistungsmetriken werden in Azure über einen Daemon erfasst, der Daten aus mehreren Quellen abruft. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus der Azure-Diagnose ausgelesen. Speicherindikatoren stammen aus den Protokollen in Ihrem Speicherabonnement.

Eine vollständige und aktuelle Liste mit bekannten Problemen finden Sie im SAP-Hinweis [1999351], der zusätzliche Informationen zur Problembehandlung für die Azure-Erweiterung für SAP enthält.

Wenn die Problembehandlung mithilfe von SAP-Hinweis [1999351] das Problem nicht löst, führen Sie das `Set-AzVMAEMExtension`-Konfigurationsskript wie unter [Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit der Azure CLI][configure-linux] oder [Konfigurieren der Azure-VM-Erweiterung für SAP-Lösungen mit PowerShell][configure-windows] beschrieben erneut aus. Unter Umständen ist eine Wartezeit von einer Stunde erforderlich, da Speicheranalyse- und Diagnoseindikatoren ggf. nicht sofort nach der Aktivierung erstellt werden. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer.

## <a name="azure-extension-error-codes"></a>Fehlercodes für die Azure-Erweiterung

| Fehler-ID | Fehlerbeschreibung | Lösung |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | App-Konfiguration fehlt. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Keine Bereitstellungs-ID in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Keine RoleInstanceId in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Keine RoleInstanceId in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure-Konfiguration kann nicht gelesen werden. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | App-Konfigurationsdatei fehlt. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Keine VM-Größe in der App-Konfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Fehler bei GlobalMemoryStatusEx-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Fehler bei MaxHwFrequency-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Fehler bei NIC-Indikatoren. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Fehler bei Prozessornamen-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Die Metrik „Datenträgertyp“ fehlt in der Erweiterungskonfigurationsdatei „config.xml“. „Datenträgertyp“ wurde zusammen mit einigen anderen Indikatoren in v2.2.0.68 12/16/2015 eingeführt. Wenn Sie die Erweiterung vor dem 16.12.2015 bereitgestellt haben, wird die alte Konfigurationsdatei verwendet. Das Azure-Erweiterungsframework führt automatisch ein Upgrade der Erweiterung auf eine neuere Version durch, aber die Datei „config.xml“ bleibt unverändert. Laden Sie zum Aktualisieren der Konfiguration das neueste PowerShell-Setupskript herunter, und führen Sie es aus. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Kein Zwischenspeichern auf Datenträger. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Kein Datenträger-SLA-Durchsatz. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Keine Datenträger-SLA-IOPS. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Fehler bei Indikator für letzte Hardwareänderung. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Fehler bei NIC-Indikatoren. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Wegen Sysprep der VM hat sich Ihre Windows-SID geändert. | [erneute Bereitstellung nach Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Fehler bei Zugriff auf die Speicheranalyse. <br /><br />Da das Auffüllen mit Speicheranalysedaten auf einer neu erstellten VM bis zu einer halben Stunde dauern kann, wird die Fehlermeldung möglicherweise nach einiger Zeit ausgeblendet. Wenn der Fehler weiterhin auftritt, führen Sie das Setupskript erneut aus. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Keine Speicheranalyse-Indikatoren. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Fehler bei Speicheranalyse. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Fehlerhafte WAD-Konfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Unerwartetes WAD-Format. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Keine WAD-Indikatoren gefunden. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Abgelaufene WAD-Indikatoren gefunden. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD-Tabelle kann nicht gelesen werden. Es besteht keine Verbindung mit der WAD-Tabelle. Dies kann mehrere Gründe haben:<br /><br /> 1) veraltete Konfiguration <br />2) keine Netzwerkverbindung mit Azure <br />3) Probleme mit dem WAD-Setup | [Setupskript ausführen][deployment-guide-run-the-script]<br />[Internetverbindung korrigieren][deployment-guide-fix-internet-connection]<br />[Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Fehler bei Systemmonitor-NIC-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Fehler bei Systemmonitor-Datenträger-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Fehler bei einigen Systemmonitor-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Fehler beim Erstellen eines Indikators durch den Systemmonitor. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Keine Anbieter für Metriken konfiguriert. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Fehlerhafte Speicheranalysekonfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Fehler bei Speicheranalyse-Metriken. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Fehler bei einem Anbieter für Metriken. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Fehler bei Anbieterthread. | [Support kontaktieren][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Ausführliche Richtlinien zu den bereitgestellten Lösungen

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Ausführen des Setupskripts

Führen Sie die Schritte im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][configure-windows] in diesem Handbuch aus, um die Erweiterung erneut zu installieren. Beachten Sie, dass einige Leistungsindikatoren für die Bereitstellung möglicherweise bis zu 30 Minuten benötigen.

Wenn die Fehler weiterhin auftreten, [wenden Sie sich an den Support][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Kontaktieren des Supports

Unerwarteter Fehler, oder es ist keine Lösung bekannt. Wenden Sie sich unter Verwendung der Protokolldatei „AzureEnhancedMonitoring_service.log“ im Ordner „C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop“ (Windows) oder „/var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux“ an den SAP-Support, um weitere Unterstützung zu erhalten.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Erneute Bereitstellung nach Sysprep

Wenn Sie ein verallgemeinertes, mit Sysprep bearbeitetes Betriebssystemimage (das SAP-Software einschließen kann) erstellen möchten, sollte dieses Image die Azure-Erweiterung für SAP nicht enthalten. Sie sollten die Azure-Erweiterung für SAP nach dem Bereitstellen der neuen Instanz des verallgemeinerten Betriebssystemimages installieren.

Wenn Ihr verallgemeinertes, mit Sysprep bearbeitetes Betriebssystemimage jedoch bereits die Azure-Erweiterung für SAP enthält, können Sie die Erweiterung mit folgender Problemumgehung auf der neu bereitgestellten VM-Instanz neu konfigurieren:

* Löschen Sie auf der neu bereitgestellten VM-Instanz den Inhalt der folgenden Ordner:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* Führen Sie die Schritte im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][configure-windows] in diesem Handbuch aus, um die Erweiterung erneut zu installieren.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Korrigieren der Internetverbindung

Der virtuelle Microsoft Azure-Computer, der die Azure-Erweiterung für SAP ausführt, benötigt Zugang zum Internet. Falls die Azure-VM Teil eines virtuellen Azure-Netzwerks oder einer lokalen Domäne ist, stellen Sie sicher, dass die entsprechenden Proxyeinstellungen festgelegt sind. Diese Einstellung müssen auch für den Internetzugang des lokalen Systemkontos gültig sein. Befolgen Sie das Kapitel [Konfigurieren des Proxy][configure-proxy] in diesem Handbuch.

Wenn Sie außerdem eine statische IP-Adresse für Ihren virtuellen Azure-Computer festlegen müssen, legen Sie sie nicht manuell innerhalb der Azure-VM fest, sondern mit [Azure PowerShell](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-cli.md) oder [Azure-Portal](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md). Die statische IP-Adresse wird über den Azure-DHCP-Dienst weitergegeben.

Manuelles Festlegen einer statischen IP-Adresse auf dem virtuellen Azure-Computer wird nicht unterstützt und kann zu Problemen mit der Azure-Erweiterung für SAP führen.


## <a name="next-steps"></a>Nächste Schritte
* [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](./deployment-guide.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md)