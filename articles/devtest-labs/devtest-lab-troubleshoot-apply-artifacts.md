---
title: Behandeln von Problemen mit Artefakten
description: Erfahren Sie, wie Sie Probleme beheben, die beim Anwenden von Artefakten auf einem virtuellen Azure DevTest Labs-Computer auftreten.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee5d056a092e6cfb632c65cc0876c9a1ef8988e6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846651"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Beheben von Problemen, die beim Anwenden von Artefakten auf einem virtuellen Azure DevTest Labs-Computer auftreten

Das Anwenden von Artefakten auf einem virtuellen Computer kann aus verschiedenen Gründen fehlschlagen. Dieser Artikel stellt Ihnen einige der Methoden vor, die Sie beim Identifizieren möglicher Ursachen unterstützen.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)stellen, um dort Hilfe von Azure DTL-Experten (Azure DevTest Labs) zu erhalten. Sie können auch einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Supportwebsite](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus.   

> [!NOTE]
> Dieser Artikel gilt sowohl für virtuelle Windows- als auch für Nicht-Windows-Computer. Es gibt zwar einige Unterschiede, aber diese werden explizit in diesem Artikel aufgeführt.

## <a name="quick-troubleshooting-steps"></a>Schritte zur schnellen Problembehandlung
Überprüfen Sie, ob die VM ausgeführt wird. DevTest Labs erfordert, dass die VM ausgeführt wird und dass der [VM-Agent (Microsoft Azure Virtual Machine-Agent](../virtual-machines/extensions/agent-windows.md)) installiert und bereit ist.

> [!TIP]
> Navigieren Sie im **Azure-Portal** zur Seite **Artefakte verwalten** für die VM, um festzustellen, ob die VM für das Anwenden von Artefakten bereit ist. Ganz oben auf der Seite wird eine Meldung angezeigt. 
> 
> Überprüfen Sie mithilfe von **Azure PowerShell** das Flag **canApplyArtifacts**, das nur beim Erweitern eines GET-Vorgangs zurückgegeben wird. Der folgende Beispielbefehl zeigt dies:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Methoden zur Problembehandlung 
Probleme mit virtuellen Computern, die unter Verwendung von DevTest Labs und des Resource Manager-Bereitstellungsmodells erstellt wurden, können folgendermaßen behandelt werden:

- **Azure-Portal**: Hervorragend geeignet, wenn Sie schnell einen visuellen Hinweis darauf erhalten möchten, was das Problem verursachen könnte.
- **Azure PowerShell**: Benutzer, die mit der PowerShell-Eingabeaufforderung vertraut sind, können DevTest Lab-Ressourcen schnell mithilfe der Azure PowerShell-Cmdlets abfragen.

> [!TIP]
> Weitere Informationen zum Überprüfen der Artefaktausführung auf einem virtuellen Computer finden Sie unter [Diagnostizieren von Artefaktfehlern im Lab](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Symptome, Ursachen und mögliche Lösungen 

### <a name="artifact-appears-to-stop-responding"></a>Artefakt reagiert nicht mehr

Ein Artefakt scheint so lange nicht mehr zu reagieren, bis ein vordefiniertes Timeout abgelaufen ist und das Artefakt als **Fehlerhaft** markiert wird.

Wenn ein Artefakt scheinbar nicht mehr reagiert, stellen Sie zunächst fest, wo das Problem auftritt. Ein Artefakt kann während der Ausführung bei jedem der folgenden Schritte blockiert werden:

- **Während der ersten Anforderung**. DevTest Labs erstellt eine Azure Resource Manager-Vorlage, um die Verwendung der benutzerdefinierten Skripterweiterung (Custom Script Extension, CSE) anzufordern. Im Hintergrund wird eine Ressourcengruppenbereitstellung ausgelöst. Wenn auf dieser Ebene ein Fehler auftritt, erhalten Sie Details in den **Aktivitätsprotokollen** der Ressourcengruppe für die betreffende VM.  
    - Sie können auf das Aktivitätsprotokoll über die Navigationsleiste der Lab-VM-Seite zugreifen. Wenn Sie diese Option auswählen, sehen Sie einen Eintrag entweder für das **Anwenden von Artefakten auf den virtuellen Computer** (wenn der Vorgang zum Anwenden von Artefakten direkt ausgelöst wurde) oder für das **Hinzufügen oder Ändern von virtuellen Computern** (wenn der Vorgang zum Anwenden von Artefakten Teil des VM-Erstellungsprozesses war).
    - Suchen Sie unter diesen Einträgen nach Fehlern. Manchmal wird der Fehler nicht gekennzeichnet, daher müssen Sie jeden Eintrag untersuchen.
    - Überprüfen Sie bei der Untersuchung der Details der einzelnen Einträge den Inhalt der JSON-Nutzlast. Am Ende dieses Dokuments wird möglicherweise ein Fehler angezeigt.

- **Beim Versuch, das Artefakt auszuführen**. Die Ursache können Netzwerk- oder Speicherprobleme sein. Weitere Informationen finden Sie im entsprechenden Abschnitt weiter unten in diesem Artikel. Dies kann auch durch die Art und Weise erfolgen, in der das Skript erstellt wird. Beispiel:
    - Ein PowerShell-Skript verfügt über **erforderliche Parameter**, aber einer der Parameter kann keinen Wert an das Skript übergeben, weil Sie dem Benutzer erlauben, den Parameterwert leer zu lassen, oder weil kein Standardwert für die Eigenschaft in der Definitionsdatei „artifactfile.json“ vorhanden ist. Das Skript reagiert nicht mehr, weil es auf eine Benutzereingabe wartet.
    - Ein PowerShell-Skript **erfordert Benutzereingaben** als Teil der Ausführung. Skripts müssen so geschrieben werden, dass sie ohne Benutzereingriff unbeaufsichtigt funktionieren.

- **VM-Agent benötigt lange, bis er bereit ist**. Beim ersten Start der VM oder beim ersten Installieren der benutzerdefinierten Skripterweiterung, um die Anforderung zum Anwenden von Artefakten zu erfüllen, muss die VM möglicherweise entweder ein Upgrade des VM-Agents durchführen oder darauf warten, dass der VM-Agent initialisiert wird. Möglicherweise sind Dienste vorhanden, von denen der VM-Agent abhängig ist, deren Initialisierung lange dauert. In diesen Fällen finden Sie Informationen zur weiteren Problembehandlung unter [Übersicht über den Agent für virtuelle Azure-Computer](../virtual-machines/extensions/agent-windows.md).

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>So überprüfen Sie, ob das Artefakt aufgrund des Skripts nicht mehr reagiert

1. Melden Sie sich beim entsprechenden virtuellen Computer an.
2. Kopieren Sie das Skript lokal auf den virtuellen Computer, oder suchen Sie es auf dem virtuellen Computer unter `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`. Dies ist der Speicherort, in den Artefaktskripts heruntergeladen werden.
3. Führen Sie das Skript mithilfe einer Eingabeaufforderung mit erhöhten Rechten lokal aus, und geben Sie die gleichen Parameterwerte an, die verwendet werden, um das Problem zu verursachen.
4. Ermitteln Sie, ob das Skript unerwünschtes Verhalten aufweist. Wenn dies der Fall ist, fordern Sie entweder ein Update des Artefakts an (wenn es aus dem öffentlichen Repository stammt), oder nehmen Sie die Korrekturen selbst vor (wenn es aus Ihrem privaten Repository stammt).

> [!TIP]
> Sie können Probleme mit Artefakten beheben, die in unserem [öffentlichen Repository](https://github.com/Azure/azure-devtestlab) gehostet werden und die Änderungen für eine Überprüfung und Genehmigung durch uns übermitteln. Weitere Informationen finden Sie im Abschnitt **Beiträge** im Dokument [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md).
> 
> Weitere Informationen zum Schreiben eigener Artefakte finden Sie im Dokument [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md).

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>So überprüfen Sie, ob das Artefakt aufgrund des VM-Agents nicht mehr reagiert
1. Melden Sie sich beim entsprechenden virtuellen Computer an.
2. Navigieren Sie im Datei-Explorer zu **C:\WindowsAzure\logs**.
3. Suchen und öffnen Sie die Datei **WaAppAgent.log**.
4. Suchen Sie nach Einträgen, die zeigen, wann der VM-Agent gestartet wird und wann die Initialisierung abgeschlossen ist (also der erste Heartbeat gesendet wird). Bevorzugen Sie neuere Einträge oder insbesondere diejenigen rund um den Zeitraum, in dem das Problem aufgetreten ist.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    In diesem Beispiel hat die Startzeit des VM-Agents 10 Minuten und 20 Sekunden betragen, weil ein Heartbeat gesendet wurde. In diesem Fall war die Ursache, dass der Start des OOBE-Diensts eine lange Zeit in Anspruch genommen hat.

> [!TIP]
> Allgemeine Informationen zu Azure-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Speicherfehler
DevTest Labs erfordert Zugriff auf das Speicherkonto des Labs, das zum Zwischenspeichern von Artefakten erstellt wird. Wenn DevTest Labs ein Artefakt anwendet, werden die Artefaktkonfiguration und die zugehörigen Dateien aus den konfigurierten Repositorys gelesen. Standardmäßig konfiguriert DevTest Labs den Zugriff auf das **öffentliche Artefaktrepository**.

Je nachdem, wie eine VM konfiguriert ist, besitzt sie möglicherweise keinen direkten Zugriff auf dieses Repository. DevTest Labs speichert die Artefakte entwurfsbedingt in einem Speicherkonto zwischen, das bei der ersten Initialisierung des Labs erstellt wird.

Wenn der Zugriff auf dieses Speicherkonto in irgendeiner Weise blockiert wird (wenn etwa der Datenverkehr vom virtuellen Computer zum Azure Storage-Dienst blockiert wird), wird möglicherweise ein Fehler wie der folgende angezeigt:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Der oben angegebene Fehler würde im Abschnitt **Bereitstellungsmeldung** auf der Seite **Artefaktergebnisse** unter **Artefakte verwalten** angezeigt. Er wird auch in den **Aktivitätsprotokollen** unter der Ressourcengruppe des betreffenden virtuellen Computers angezeigt.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>So stellen Sie sicher, dass die Kommunikation mit dem Azure Storage-Dienst nicht blockiert wird:

- **Überprüfung auf hinzugefügte Netzwerksicherheitsgruppen (NSGs)** . Möglicherweise wurde eine Abonnementrichtlinie hinzugefügt, durch die NSGs in allen virtuellen Netzwerken automatisch konfiguriert werden. Dies wirkt sich auch auf das standardmäßige virtuelle Netzwerk des Labs (sofern verwendet) oder auf ein anderes virtuelles Netzwerk aus, das in Ihrem Lab konfiguriert ist und für die Erstellung von VMs verwendet wird.

- **Überprüfen Sie das Speicherkonto des Standard-Labs** (also das erste Speicherkonto, das beim Erstellen des Labs erstellt wurde, dessen Name in der Regel mit dem Buchstaben „a“ beginnt und auf eine mehrstelligen Zahl endet, d. h. a\<labname\>#).
    1. Navigieren Sie zur Ressourcengruppe des Labs.
    2. Suchen Sie die Ressource vom Typ **Speicherkonto**, deren Name mit der Konvention übereinstimmt.
    3. Navigieren Sie zur Speicherkontoseite mit dem Titel **Firewalls und virtuelle Netzwerke**.
    4. Stellen Sie sicher, dass die Option **Alle Netzwerke** festgelegt ist. Wenn die Option **Ausgewählter Netzwerke** ausgewählt ist, stellen Sie sicher, dass die virtuellen Netzwerke des Labs, die zum Erstellen von VMs verwendet werden, der Liste hinzugefügt werden.

Ausführlichere Informationen zur Problembehandlung finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../storage/common/storage-network-security.md).

> [!TIP]
> **Überprüfen der Netzwerksicherheitsgruppen-Regeln**. Verwenden Sie den Ansatz [Überprüfen des IP-Flusses](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify), um zu bestätigen, dass eine Regel in einer Netzwerksicherheitsgruppe den Datenverkehr an einen bzw. von einem virtuellen Computer blockiert. Sie können auch die aktiven Sicherheitsgruppenregeln überprüfen, um sicherzustellen, dass die NSG-Regel **Zulassen** für eingehende Verbindungen vorhanden ist. Weitere Informationen finden Sie unter [Problembehandlung bei Netzwerksicherheitsgruppen über das Azure-Portal](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Andere Fehlerquellen
Es gibt weitere weniger häufige mögliche Fehlerquellen. Stellen Sie sicher, dass Sie alle Fehlerquellen auswerten, um zu ermitteln, ob sie für Ihren Fall gelten. Dies ist eine davon: 

- **Abgelaufenes persönliches Zugriffstoken für das private Repository**. Wenn das Artefakt abgelaufen ist, wird es nicht aufgelistet, und alle Skripts, die auf Artefakte aus dem Repository verweisen, schlagen fehl.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.

