---
title: Korrigieren nicht konformer Azure Automation State Configuration-Server
description: In diesem Artikel erfahren Sie, wie Sie Konfigurationen bedarfsabhängig erneut auf Server anwenden, bei denen sich der Konfigurationszustand verändert hat.
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: cf4286634e72d687fe78c53a8f20abe5d5b2b6eb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489868"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Korrigieren nicht konformer Azure Automation State Configuration-Server

Wenn Server mit Azure Automation State Configuration registriert wurden, wird der Konfigurationsmodus auf `ApplyOnly`, `ApplyAndMonitor` oder `ApplyAndAutoCorrect` festgelegt. Wenn der Modus nicht auf `ApplyAndAutoCorrect` festgelegt ist, verbleiben Server, für die aus irgendeinem Grund der konforme Zustand nicht mehr besteht, im nicht konformen Zustand, bis der Fehler manuell behoben wird.

Azure Compute verfügt über das Feature „Befehl ausführen“, mit dem Kunden Skripts auf virtuellen Computern ausführen können.
Dieses Dokument enthält Beispielskripts für dieses Feature, die genutzt werden können, wenn Abweichungen bei der Konfiguration manuell korrigiert werden sollen.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Korrigieren von Abweichungen virtueller Windows-Computer mit PowerShell

Abweichungen bei virtuellen Windows-Computern können mithilfe des Befehlsfeatures `Run` korrigiert werden. Weitere Informationen finden Sie unter [Ausführen von PowerShell-Skripts in Ihrer Windows-VM mit „Befehl ausführen“](../virtual-machines/windows/run-command.md).

Verwenden Sie das Cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration), um zu erzwingen, dass ein Azure Automation State Configuration-Knoten die aktuelle Konfiguration herunterlädt und anwendet.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Korrigieren von Abweichungen virtueller Linux-Computer

Bei virtuellen Linux-Computern kann der Befehl `Run` nicht verwendet werden. Abweichungen können für diese Computer nur durch Wiederholung des Registrierungsprozesses korrigiert werden. 

Für Azure-Knoten können Sie die Abweichung im Azure-Portal oder mithilfe von Cmdlets des Az-Moduls korrigieren. Ausführliche Informationen zu diesem Prozess finden Sie unter [Aktivieren eines virtuellen Computers über das Azure-Portal](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Für Hybridknoten kann die Korrektur von Abweichungen mithilfe der Python-Skripts durchgeführt werden. Weitere Informationen finden Sie unter [Ausführen von DSC-Vorgängen über den Linux-Computer](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation/#automation).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
