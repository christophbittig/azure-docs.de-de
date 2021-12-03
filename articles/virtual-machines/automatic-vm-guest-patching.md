---
title: Automatische VM-Gastpatches für Azure-VMs
description: Erfahren Sie, wie Sie virtuelle Computer in Azure automatisch patchen.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: maintenance
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91d172cf1d3ba5bf78feb8e18382631464619c04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459727"
---
# <a name="automatic-vm-guest-patching-for-azure-vms"></a>Automatische VM-Gastpatches für Azure-VMs

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

Durch das Aktivieren von automatischen VM-Gastpatches für Ihre Azure-VMs können Sie die Updateverwaltung vereinfachen, indem Sie virtuelle Computer sicher und automatisch patchen, um Sicherheitskonformität zu wahren.

Automatische VM-Gastpatches weisen die folgenden Merkmale auf:
- Patches, die als *Kritisch* oder *Sicherheit* klassifiziert werden, werden automatisch heruntergeladen und auf die VM angewendet.
- Patches werden außerhalb der Spitzenzeiten in der Zeitzone der VM angewendet.
- Die Patchorchestrierung wird von Azure verwaltet, und Patches werden nach den [verfügbarkeitsbasierten Prinzipien](#availability-first-updates) angewendet.
- Die Integrität des virtuellen Computers wird anhand von Integritätssignalen der Plattform ermittelt und überwacht, um Patchfehler zu erkennen.
- Funktioniert für alle VM-Größen.

## <a name="how-does-automatic-vm-guest-patching-work"></a>Wie funktioniert automatisches VM-Gastpatchen?

Wenn automatisches VM-Gastpatchen auf einer VM aktiviert ist, werden die verfügbaren *kritischen* und *Sicherheitspatches* heruntergeladen und automatisch auf die VM angewendet. Dieser Prozess wird jeden Monat automatisch gestartet, wenn neue Patches freigegeben werden. Die Patchbewertung und -installation erfolgt automatisch, und der Vorgang umfasst einen Neustart der VM bei Bedarf.

Die VM wird regelmäßig alle paar Tage und mehrmals innerhalb eines 30-Tage-Zeitraums überprüft, um die anwendbaren Patches für diese VM zu ermitteln. Die Patches können an jedem beliebigen Tag außerhalb der Spitzenzeiten der VM installiert werden. Diese automatische Bewertung stellt sicher, dass alle fehlenden Patches zum frühestmöglichen Zeitpunkt erkannt werden.

Patches werden innerhalb von 30 Tagen nach den monatlichen Patchreleases nach der unten beschriebenen Verfügbarkeitsorchestrierung installiert. Patches werden nur außerhalb der Spitzenzeiten für die VM installiert, abhängig von der Zeitzone der VM. Die VM muss außerhalb der Spitzenzeiten ausgeführt werden, damit Patches automatisch installiert werden können. Wenn eine VM während einer periodischen Bewertung ausgeschaltet wird, wird die VM automatisch bewertet, und die anwendbaren Patches werden bei der nächsten periodischen Bewertung (in der Regel innerhalb weniger Tage) automatisch installiert, wenn die VM eingeschaltet wird.

Definitionsupdates und andere Patches, die nicht als *Kritisch* oder *Sicherheit* eingestuft sind, werden nicht über das automatische Patchen von VM-Gästen installiert. Um Patches mit anderen Patchklassifizierungen zu installieren oder die Patchinstallation innerhalb Ihres eigenen benutzerdefinierten Wartungsfensters zu planen, können Sie die [Updateverwaltung](./windows/tutorial-config-management.md#manage-windows-updates) verwenden.

### <a name="availability-first-updates"></a>Verfügbarkeitsupdates

Der Patchinstallationsvorgang wird global von Azure für alle VMs orchestriert, für die automatisches VM-Gastpatchen aktiviert ist. Diese Orchestrierung folgt den Verfügbarkeitsprinzipien auf unterschiedlichen Verfügbarkeitsstufen, die von Azure bereitgestellt werden.

Für eine Gruppe von virtuellen Computern, die ein Update durchlaufen, orchestriert die Azure-Plattform Updates wie folgt:

**Über Regionen hinweg:**
- Ein monatliches Update wird in Azure global phasenweise orchestriert, um globale Bereitstellungsfehler zu verhindern.
- Eine Phase kann sich über eine oder mehrere Regionen erstrecken, und ein Update geht nur dann in die nächsten Phasen über, wenn die in Betracht kommenden VMs in einer Phase erfolgreich aktualisiert wurden.
- Georegionspaare werden nicht gleichzeitig aktualisiert und können sich nicht in der gleichen Regionsphase befinden.
- Der Erfolg eines Updates wird durch die Nachverfolgung der Integrität nach dem Update der VM gemessen. Die VM-Integrität wird durch Integritätsindikatoren der Plattform für die VM nachverfolgt.

**Innerhalb einer Region:**
- VMs in verschiedenen Verfügbarkeitszonen werden nicht gleichzeitig mit demselben Update aktualisiert.
- VMs, die nicht Teil einer Verfügbarkeitsgruppe sind, werden nach bestem Wissen in einem Batch zusammengefasst, um gleichzeitige Updates für alle VMs in einem Abonnement zu vermeiden.

**Innerhalb einer Verfügbarkeitsgruppe:**
- Alle VMs in einer gemeinsamen Verfügbarkeitsgruppe werden nicht gleichzeitig aktualisiert.
-   VMs in einer gemeinsamen Verfügbarkeitsgruppe werden innerhalb der Grenzen der Updatedomäne aktualisiert, und VMs in mehreren Updatedomänen werden nicht gleichzeitig aktualisiert.

Das Patchinstallationsdatum für eine bestimmte VM variiert ggf. von Monat zu Monat, da eine bestimmte VM in einem anderen Batch zwischen monatlichen Patchzyklen ausgewählt werden kann.

### <a name="which-patches-are-installed"></a>Welche Patches werden installiert?
Die installierten Patches hängen von der Rolloutphase des virtuellen Computers ab. Jeden Monat wird ein neues globales Rollout gestartet, bei dem alle für eine einzelne VM bewerteten Sicherheits- und kritischen Patches für diese VM installiert werden. Der Rollout wird über alle Azure-Regionen hinweg in Batches orchestriert (wie im Abschnitt „Patches nach Verfügbarkeit“ oben beschrieben).

Die genaue Menge der zu installierenden Patches hängt von der VM-Konfiguration, einschließlich des Betriebssystemtyps und dem Bewertungszeitpunkt ab. Es ist möglich, dass zwei identische VMs in verschiedenen Regionen unterschiedliche Patches installiert bekommen, wenn mehr oder weniger Patches verfügbar sind, wenn die Patchorchestrierung verschiedene Regionen zu unterschiedlichen Zeiten erreicht. In ähnlicher Weise, aber weniger häufig, können VMs innerhalb derselben Region, aber zu unterschiedlichen Zeitpunkten bewertet (aufgrund unterschiedlicher Verfügbarkeitszonen- oder Verfügbarkeitsgruppenbatches), unterschiedliche Patches erhalten.

Da die automatischen VM-Gastpatches die Patchquelle nicht konfigurieren, können zwei ähnliche VMs, die für unterschiedliche Patchquellen konfiguriert sind, z. B. öffentliches Repository im Vergleich zu privatem Repository, auch einen Unterschied in der genauen Menge der installierten Patches aufweisen.

Bei Betriebssystemtypen, die Patches in einem festen Rhythmus veröffentlichen, können VMs, die für das öffentliche Repository des Betriebssystems konfiguriert sind, erwarten, dass sie in den verschiedenen Rolloutphasen eines Monats den gleichen Satz an Patches erhalten. Beispiel: Virtuelle Windows-Computer, die für das öffentliche Windows Update-Repository konfiguriert sind.

Da jeden Monat ein neues Rollout ausgelöst wird, erhält eine VM jeden Monat mindestens ein Patchrollout, wenn die VM außerhalb der Spitzenzeiten eingeschaltet ist. Durch diesen Prozess wird sichergestellt, dass die VM monatlich mit den neuesten verfügbaren Sicherheits- und kritischen Patches gepatcht wird. Um die Konsistenz der installierten Patches sicherzustellen, können Sie Ihre VMs so konfigurieren, dass sie Patches aus Ihren eigenen privaten Repositorys bewerten und herunterladen.

## <a name="supported-os-images"></a>Unterstützte Betriebssystemimages
Zurzeit werden nur VMs, die aus bestimmten Betriebssystem-Plattformimages erstellt wurden, in der Vorschau unterstützt. Benutzerdefinierte Bilder werden derzeit nicht unterstützt.

Derzeit werden die folgenden Plattform-SKUs unterstützt (weitere werden regelmäßig hinzugefügt):

| Herausgeber               | Betriebssystemangebot      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Canonical  | 0001-com-ubuntu-pro-bionic | pro-18_04-lts |
| Canonical  | 0001-com-ubuntu-server-focal | 20_04-lts |
| Canonical  | 0001-com-ubuntu-pro-focal | pro-20_04-lts |
| Redhat  | RHEL | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7_9, 7-RAW, 7-LVM |
| Redhat  | RHEL | 8, 8.1, 8.2, 8_3, 8_4, 8-LVM |
| Redhat  | RHEL-RAW | 8-raw |
| OpenLogic  | CentOS | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7_8, 7_9, 7-LVM |
| OpenLogic  | CentOS | 8.0, 8_1, 8_2, 8_3, 8-lvm |
| SUSE  | sles-12-sp5 | gen1, gen2 |
| SUSE  | sles-15-sp2 | gen1, gen2 |
| MicrosoftWindowsServer  | Windows Server | 2008-R2-SP1 |
| MicrosoftWindowsServer  | Windows Server | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | Windows Server | 2016-Datacenter    |
| MicrosoftWindowsServer  | Windows Server | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | Windows Server | 2019-Datacenter |
| MicrosoftWindowsServer  | Windows Server | 2019-Datacenter-Core |

> [!NOTE]
>Automatische VM-Gastpatches, bedarfsgesteuerte Patchbewertung und bedarfsbasierte Patchinstallation werden nur für VMs unterstützt, die aus Images erstellt wurden, die genau die Kombination aus Herausgeber, Angebot und SKU aus der Liste der unterstützten Betriebssystemimages enthalten. Benutzerdefinierte Images oder andere Herausgeben-, Angebots- und SKU-Kombinationen werden nicht unterstützt.

## <a name="patch-orchestration-modes"></a>Patchorchestrierungsmodi
VMs in Azure unterstützen nun die folgenden Patchorchestrierungsmodi:

**AutomaticByPlatform:**
- Dieser Modus wird sowohl für Linux- als auch für Windows-VMs unterstützt.
- Dieser Modus ermöglicht automatisches VM-Gastpatchen für den virtuellen Computer, und die nachfolgende Patchinstallation wird von Azure orchestriert.
- Dieser Modus ist erforderlich für Patches nach Verfügbarkeit.
- Dieser Modus wird nur für VMs unterstützt, die mithilfe der oben genannten unterstützten Betriebssystem-Plattformimages erstellt wurden.
- Wenn Sie diesen Modus für virtuelle Windows-Computer festlegen, werden die nativen automatischen Updates auf dem virtuellen Windows-Computer deaktiviert, um Duplizierung zu vermeiden.
- Um diesen Modus auf virtuellen Linux-Computern zu verwenden, legen Sie die Eigenschaft `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` in der VM-Vorlage fest.
- Um diesen Modus auf virtuellen Windows-Computern zu verwenden, legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` in der VM-Vorlage fest.

**AutomaticByOS:**
- Dieser Modus wird nur für virtuelle Windows-Computer unterstützt.
- Dieser Modus ermöglicht automatische Updates auf dem virtuellen Windows-Computer, und Patches werden über automatische Updates auf der VM installiert.
- Dieser Modus unterstützt keine Patches nach Verfügbarkeit.
- Dieser Modus wird standardmäßig festgelegt, wenn kein anderer Patchmodus für einen virtuellen Windows-Computer angegeben wird.
- Um diesen Modus auf virtuellen Windows-Computern zu verwenden, legen Sie die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates=true` fest, und legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` in der VM-Vorlage fest.

**Manual (Manuell):**
- Dieser Modus wird nur für virtuelle Windows-Computer unterstützt.
- In diesem Modus werden automatische Updates auf dem virtuellen Windows-Computer deaktiviert.
- Dieser Modus unterstützt keine Patches nach Verfügbarkeit.
- Dieser Modus sollte festgelegt werden, wenn Sie benutzerdefinierte Patchlösungen verwenden.
- Um diesen Modus auf virtuellen Windows-Computern zu verwenden, legen Sie die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates=false` fest, und legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` in der VM-Vorlage fest.

**ImageDefault:**
- Dieser Modus wird nur für virtuelle Linux-Computer unterstützt.
- Dieser Modus unterstützt keine Patches nach Verfügbarkeit.
- Dieser Modus berücksichtigt die standardmäßige Patchkonfiguration in dem Image, das zum Erstellen des virtuellen Computers verwendet wurde.
- Dieser Modus wird standardmäßig festgelegt, wenn kein anderer Patchmodus für einen virtuellen Linux-Computer angegeben wird.
- Um diesen Modus auf virtuellen Linux-Computern zu verwenden, legen Sie die Eigenschaft `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` in der VM-Vorlage fest.

> [!NOTE]
>Die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates` kann für virtuelle Windows-Computer nur bei der Erstellung der VM festgelegt werden. Dies wirkt sich auf bestimmte Patchmodusübergänge aus. Der Wechsel zwischen den Modi „AutomaticByPlatform“ und „Manual“ wird auf VMs unterstützt, die über `osProfile.windowsConfiguration.enableAutomaticUpdates=false` verfügen. In ähnlicher Weise wird der Wechsel zwischen den Modi „AutomaticByPlatform“ und „AutomaticByOS“ auf VMs unterstützt, die über `osProfile.windowsConfiguration.enableAutomaticUpdates=true` verfügen. Der Wechsel zwischen den Modi „AutomaticByOS“ und „Manual“ wird nicht unterstützt.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Anforderungen zum Aktivieren von automatischen VM-Gastpatches

- Auf dem virtuellen Computer muss der Azure-VM-Agent für [Windows](./extensions/agent-windows.md) oder [Linux](./extensions/agent-linux.md) installiert sein.
- Bei virtuellen Linux-Computern muss der Azure Linux-Agent die Version 2.2.53.1 oder höher aufweisen. [Aktualisieren Sie den Linux-Agent](./extensions/update-linux-agent.md), wenn die aktuelle Version niedriger als die erforderliche Version ist.
- Der Windows Update-Dienst muss für virtuelle Windows-Computer auf dem virtuellen Computer ausgeführt werden.
- Der virtuelle Computer muss auf die konfigurierten Update-Endpunkte zugreifen können. Wenn Ihr virtueller Computer für die Verwendung privater Repositorys für Linux oder Windows Server Update Services (WSUS) für virtuelle Windows-Computer konfiguriert ist, müssen Sie auf die relevanten Update-Endpunkte zugreifen können.
- Verwenden Sie Compute-API Version 2021-03-01 oder höher, um auf alle Funktionen zuzugreifen, einschließlich bedarfsbasierter Bewertung und bedarfsgesteuertem Patching.
- Benutzerdefinierte Images werden derzeit nicht unterstützt.

## <a name="enable-automatic-vm-guest-patching"></a>Aktivieren automatischer VM-Gastpatches
Automatische VM-Gastpatches können auf jeder Windows- oder Linux-VM aktiviert werden, die aus einem unterstützten Plattformimage erstellt wird. Um automatische VM-Gastpatches auf einem virtuellen Windows-Computer zu aktivieren, stellen Sie sicher, dass die Eigenschaft *osProfile.windowsConfiguration.enableAutomaticUpdates* in der VM-Vorlagendefinition auf *true* festgelegt ist. Diese Eigenschaft kann nur beim Erstellen der VM festgelegt werden. Diese zusätzliche Eigenschaft gilt nicht für virtuelle Linux-Computer.

### <a name="rest-api-for-linux-vms"></a>REST-API für virtuelle Linux-Computer
Im folgenden Beispiel wird das Aktivieren von automatischen VM-Gastpatches beschrieben:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST-API für virtuelle Windows-Computer
Im folgenden Beispiel wird das Aktivieren von automatischen VM-Gastpatches beschrieben:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell für Windows-VMs
Verwenden Sie das Cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem), um beim Erstellen oder Aktualisieren einer VM automatische VM-Gastpatches zu aktivieren.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI für virtuelle Windows-Computer
Verwenden Sie [az vm create](/cli/azure/vm#az_vm_create), um beim Erstellen einer neuen VM automatische VM-Gastpatches zu aktivieren. Im folgenden Beispiel werden automatische VM-Gastpatches für eine VM mit dem Namen *myVM* in der Ressourcengruppe mit dem Namen *myResourceGroup* konfiguriert:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Verwenden Sie [az vm update](/cli/azure/vm#az_vm_update), um eine vorhandene VM zu ändern.

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Aktivierung und Bewertung

> [!NOTE]
>Es kann mehr als drei Stunden dauern, bis automatische VM-Gastupdates auf einer VM aktiviert werden, da die Aktivierung außerhalb der Spitzenzeiten der VM abgeschlossen wird. Da Bewertung und Patchinstallation nur außerhalb der Spitzenzeiten stattfinden, muss Ihre VM auch außerhalb der Spitzenzeiten ausgeführt werden, damit Patches angewendet werden können.

Wenn automatische VM-Gastpatches für einen virtuellen Computer aktiviert sind, wird eine VM-Erweiterung vom Typ `Microsoft.CPlat.Core.LinuxPatchExtension` auf einem virtuellen Linux-Computer installiert, oder eine VM-Erweiterung des Typs `Microsoft.CPlat.Core.WindowsPatchExtension` wird auf einem virtuellen Windows-Computer installiert. Diese Erweiterung muss nicht manuell installiert oder aktualisiert werden, da diese Erweiterung von der Azure-Plattform im Rahmen des automatischen VM-Gastpatchprozesses verwaltet wird.

Es kann mehr als drei Stunden dauern, bis automatische VM-Gastupdates auf einer VM aktiviert werden, da die Aktivierung außerhalb der Spitzenzeiten der VM abgeschlossen wird. Die Erweiterung wird ebenfalls außerhalb der Spitzenzeiten für die VM installiert und aktualisiert. Wenn der Zeitraum mit schwacher Auslastung der VM endet, bevor die Aktivierung abgeschlossen werden kann, wird der Aktivierungsprozess während des nächsten verfügbaren Zeitraums mit schwacher Auslastung fortgesetzt.

Automatische Updates sind in den meisten Szenarien deaktiviert, und die Installation von Patches wird künftig über die Erweiterung durchgeführt. Die folgenden Bedingungen gelten.
- Wenn für einen virtuellen Windows-Computer zuvor automatische Windows-Updates im AutomaticByOS-Patchmodus aktiviert waren, werden automatische Windows-Updates für die VM deaktiviert, wenn die Erweiterung installiert wird.
- Bei virtuellen Ubuntu-Computern werden die standardmäßigen automatischen Updates automatisch deaktiviert, wenn die automatischen VM-Gastpatches die Aktivierung abschließen.
- Für RHEL müssen die automatischen Updates in der Vorschau manuell deaktiviert werden. Führen Sie folgende Befehle aus:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Überprüfen Sie die Instanzansicht der VM, um zu ermitteln, ob automatische VM-Gastpatches abgeschlossen wurden und die Patcherweiterung auf der VM installiert wurde. Nachdem der Aktivierungsvorgang abgeschlossen wurde, wird die Erweiterung installiert, und die Bewertungsergebnisse für die VM sind unter `patchStatus` verfügbar. Der Zugriff auf die Instanzansicht der VM kann auf verschiedene Weise erfolgen, wie unten beschrieben.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) mit dem Parameter `-Status`, um auf die Instanzansicht Ihrer VM zuzugreifen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell stellt zurzeit nur Informationen zur Patcherweiterung zur Verfügung. Informationen zu `patchStatus` werden demnächst ebenfalls über PowerShell verfügbar sein.

### <a name="azure-cli"></a>Azure CLI
Verwenden Sie [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view), um auf die Instanzansicht für Ihre VM zuzugreifen.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Grundlegendes zum Patchstatus für Ihre VM

Der Abschnitt `patchStatus` der Antwort der Instanzansicht enthält Details zur neuesten Bewertung und zur letzten Patchinstallation für Ihre VM.

Die Bewertungsergebnisse für Ihre VM können im Abschnitt `availablePatchSummary` überprüft werden. Eine Bewertung wird regelmäßig für eine VM durchgeführt, für die automatische VM-Gastpatches aktiviert sind. Die Anzahl der verfügbaren Patches nach einer Bewertung wird unter den `criticalAndSecurityPatchCount`- und `otherPatchCount`-Ergebnissen bereitgestellt. Mit automatischen VM-Gastpatches werden alle Patches installiert, die mit den Patchklassifizierungen *Kritisch* und *Sicherheit* bewertet werden. Alle anderen bewerteten Patches werden übersprungen.

Die Ergebnisse der Patchinstallation für Ihre VM können im Abschnitt `lastPatchInstallationSummary` überprüft werden. Dieser Abschnitt enthält Details zum letzten Patchinstallationsversuch auf der VM, einschließlich der Anzahl der Patches, die installiert wurden, ausstehend bzw. fehlgeschlagen sind oder übersprungen wurden. Patches werden nur während des Wartungsfensters außerhalb der Spitzenzeiten für die VM installiert. Ausstehende und fehlerhafte Patches werden während des nächsten Wartungsfensters außerhalb der Spitzenzeiten automatisch wiederholt.

## <a name="disable-automatic-vm-guest-patching"></a>Deaktivieren automatischer VM-Gastpatches
Die automatischen VM-Gastpatches können durch Ändern des [Patchorchestrierungsmodus](#patch-orchestration-modes) für die VM deaktiviert werden.

Ändern Sie den Patch-Modus in `ImageDefault`, um automatische VM-Gastpatches auf einer Linux-VM zu deaktivieren.

Um automatische VM-Gastpatches auf einer Windows-VM zu aktivieren, bestimmt die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates`, welche Patchmodi auf der VM festgelegt werden können, und diese Eigenschaft kann nur festgelegt werden, wenn die VM zum ersten Mal erstellt wird. Dies wirkt sich auf bestimmte Patchmodusübergänge aus:
- Deaktivieren Sie bei VMs mit `osProfile.windowsConfiguration.enableAutomaticUpdates=false` die automatischen VM-Gastpatches, indem Sie den Patchmodus in `Manual` ändern.
- Deaktivieren Sie bei VMs mit `osProfile.windowsConfiguration.enableAutomaticUpdates=true`, die automatischen VM-Gastpatches, indem Sie den Patchmodus in `AutomaticByOS` ändern.
- Der Wechsel zwischen den Modi „AutomaticByOS“ und „Manual“ wird nicht unterstützt.

Verwenden Sie die Beispiele aus dem Abschnitt zur [Aktivierung](#enable-automatic-vm-guest-patching) oben in diesem Artikel für API-, PowerShell- und CLI-Verwendungsbeispiele, um den erforderlichen Patchmodus festzulegen.

## <a name="on-demand-patch-assessment"></a>Bedarfsgesteuerte Patchbewertung
Wenn automatische VM-Gastpatches bereits für Ihre VM aktiviert sind, wird eine regelmäßige Patchbewertung für die VM außerhalb der Spitzenzeiten der VM durchgeführt. Dieser Prozess erfolgt automatisch, und die Ergebnisse der aktuellen Bewertung können wie zuvor in diesem Dokument beschrieben über die Instanzansicht der VM überprüft werden. Sie können auch jederzeit eine bedarfsgesteuerte Patchbewertung für Ihren virtuellen Computer auslösen. Die Patchbewertung kann einige Minuten dauern, und der Status der aktuellen Bewertung wird in der Instanzansicht der VM aktualisiert.

> [!NOTE]
>Bei der bedarfsgesteuerten Patchbewertung wird die Patchinstallation nicht automatisch auslöst. Wenn Sie das automatische Patchen von VM-Gästen aktiviert haben, werden die bewerteten und anwendbaren Patches für die VM außerhalb der Spitzenzeiten der VM installiert. Dies erfolgt nach dem zuvor in diesem Dokument beschriebenen Vorgang zur Patchverfügbarkeit.

### <a name="rest-api"></a>REST-API
Verwenden Sie die API für die [Patchbewertung](/rest/api/compute/virtual-machines/assess-patches), um verfügbare Patches für Ihren virtuellen Computer zu bewerten.
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment), um die verfügbaren Patches für Ihren virtuellen Computer zu bewerten.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Verwenden Sie [az vm assess-patches](/cli/azure/vm#az_vm_assess_patches), um verfügbare Patches für Ihren virtuellen Computer zu bewerten.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="on-demand-patch-installation"></a>Bedarfsbasierte Patchinstallation
Wenn automatische VM-Gastpatches für Ihre VM bereits aktiviert sind, wird eine regelmäßige Patchinstallation von Sicherheitspatches und wichtigen Patches für die VM außerhalb der Spitzenzeiten der VM durchgeführt. Dieser Prozess erfolgt automatisch, und die Ergebnisse der aktuellen Installation können wie zuvor in diesem Dokument beschrieben über die Instanzansicht der VM überprüft werden.

Sie können auch jederzeit eine bedarfsbasierte Patchinstallation für Ihren virtuellen Computer auslösen. Die Patchinstallation kann einige Minuten dauern, und der Status der aktuellen Installation wird in der Instanzansicht der VM aktualisiert.

Sie können die bedarfsbasierte Patchinstallation verwenden, um alle Patches einer oder mehrere Patchklassifizierungen zu installieren. Sie können auch wählen, ob Sie bestimmte Pakete für Linux oder bestimmte KB-IDs für Windows ein- oder ausschließen möchten. Wenn Sie eine bedarfsbasierte Patchinstallation auslösen, stellen Sie sicher, dass Sie mindestens eine Patchklassifizierung oder mindestens ein Patch (Paket für Linux, KB-ID für Windows) in der Aufnahmeliste angeben.

### <a name="rest-api"></a>REST-API
Verwenden Sie die API zum [Installieren von Patches](/rest/api/compute/virtual-machines/install-patches), um Patches auf Ihrem virtuellen Computer zu installieren.

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/installPatches?api-version=2020-12-01`
```

Beispiel für einen Anforderungstext für Linux:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "linuxParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

Beispiel für einen Anforderungstext für Windows:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "windowsParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Invoke-AzVMInstallPatch](/powershell/module/az.compute/invoke-azvminstallpatch), um Patches auf Ihrem virtuellen Computer zu installieren.

Beispiel für die Installation bestimmter Pakete auf einer Linux-VM:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Linux -ClassificationToIncludeForLinux "Security" -PackageNameMaskToInclude ["package123"] -PackageNameMaskToExclude ["package567"]
```

Beispiel für die Installation aller wichtigen Patches auf einer Windows-VM:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT2H" -RebootSetting "Never" -Windows   -ClassificationToIncludeForWindows Critical
```

Beispiel für die Installation aller Sicherheitspatches auf einer Windows-VM, wobei Patches mit bestimmten KB-IDs ein- und ausgeschlossen werden und alle Patches, die einen Neustart erfordern, ausgeschlossen werden:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Windows -ClassificationToIncludeForWindows "Security" -KBNumberToInclude ["KB1234567", "KB123567"] -KBNumberToExclude ["KB1234702", "KB1234802"] -ExcludeKBsRequiringReboot
```

### <a name="azure-cli"></a>Azure CLI
Verwenden Sie [az vm install-patches](/cli/azure/vm#az_vm_install_patches), um Patches auf Ihrem virtuellen Computer zu installieren.

Beispiel für die Installation aller wichtigen Patches auf einer Linux-VM:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-linux Critical
```

Beispiel für die Installation aller wichtigen Patches und Sicherheitspatches auf einer Windows-VM, wobei alle Patches, die einen Neustart erfordern, ausgeschlossen werden:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-win Critical Security --exclude-kbs-requiring-reboot true
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Weitere Informationen zum Erstellen und Verwalten virtueller Windows-Computer](./windows/tutorial-manage-vm.md)
