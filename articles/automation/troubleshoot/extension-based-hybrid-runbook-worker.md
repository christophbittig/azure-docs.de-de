---
title: Problembehandlung bei erweiterungsbasierten Hybrid Runbook Workern in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Probleme beheben, die bei erweiterungsbasierten Hybrid Runbook Workern in Azure Automation auftreten.
services: automation
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cdb8f63c16c9479d27bcb44147d78d1a8e4a7aa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489887"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Problembehandlung bei VM-erweiterungsbasierten Hybrid Runbook Workern in Automation

Dieser Artikel enthält Informationen zur Problembehandlung bei erweiterungsbasierten Hybrid Runbook Workern in Azure Automation. Informationen zur Problembehandlung bei Agent-basierten Workern finden Sie unter [Problembehandlung bei Agent-basierten Hybrid Runbook Workern in Automation](./hybrid-runbook-worker.md). Allgemeine Informationen finden Sie unter [Übersicht über Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

## <a name="general-checklist"></a>Allgemeine Checkliste

So beheben Sie Probleme mit erweiterungsbasierten Hybrid Runbook Workers:

- Überprüfen Sie, ob das Betriebssystem unterstützt wird und die Voraussetzungen erfüllt sind.Siehe [Voraussetzungen](../extension-based-hybrid-runbook-worker-install.md#prerequisites).

- Überprüfen Sie, ob die vom System zugewiesene verwaltete Identität auf dem virtuellen Computer aktiviert ist. Azure-VMs und Arc-fähige Azure-Computer sollten mit einer systemseitig zugewiesenen verwalteten Identität aktiviert werden.

- Überprüfen Sie, ob die Erweiterung mit den richtigen Einstellungen aktiviert ist.Die Einstellungsdatei sollte über die richtige  `AutomationAccountURL` verfügen.Überprüfen Sie die URL mit der Automation-Kontoeigenschaft  `AutomationHybridServiceUrl`.  
  - Für Windows: Die Einstellungsdatei finden Sie unter  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin`.
  - Für Linux: Die Einstellungsdatei finden Sie unter  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/`.

- Überprüfen Sie die Fehlermeldung, die unter Hybrid Worker-Erweiterungsstatus/Detaillierter Status angezeigt wird.Sie enthält Fehlermeldungen und entsprechende Empfehlungen, um das Problem zu beheben.

- Führen Sie das Problembehandlungstool auf dem virtuellen Computer aus, und es wird eine Ausgabedatei generiert. Öffnen Sie die Ausgabedatei, und überprüfen Sie die vom Problembehandlungstool identifizierten Fehler.
  - Für Windows: Das Problembehandlungstool finden Sie unter `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1`.
  - Für Linux: Das Problembehandlungstool finden Sie unter `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py`.

- Überprüfen Sie für Linux-Computer, ob der Benutzer `hweautomation` mit den richtigen Berechtigungen eingerichtet ist.  

- Überprüfen Sie, ob der Hybrid Worker-Prozess ausgeführt wird.
   - Für Windows: Überprüfen Sie den  `Hybrid Worker Service`-Dienst.
   - Für Linux: Überprüfen Sie den  `hwd.`-Dienst.

- Führen Sie das Protokollsammlertool aus, und überprüfen Sie die gesammelten Protokolle.
   - Für Windows: Die Protokolle befinden sich unter `C:\HybridWorkerExtensionLogs`. Das Tool befindet sich unter `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1`. Das Erweiterungsprotokoll ist `HybridWorkerExtensionHandler.log`. Das Workerprotokoll ist `SME.log`.
   - Für Linux: Die Protokolle befinden sich unter `/home/nxautomation/run`. Das Tool befindet sich unter `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py`. Das Workerprotokoll ist `worker.log`. Das Registrierungsprotokoll der Erweiterung ist `register_log`. Das Startprotokoll der Erweiterung ist `startup_log`. Das Erweiterungsprotokoll ist `extension_out`.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.