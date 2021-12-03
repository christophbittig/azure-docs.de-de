---
title: Ausführen von Skripts auf einem virtuellen Linux-Computer in Azure mithilfe der Aktion Befehle ausführen
description: In diesem Thema erfahren Sie, wie Sie Skripts innerhalb eines virtuellen Azure-Computers unter Linux mithilfe des Features „Skriptausführung“ ausführen.
services: automation
ms.service: virtual-machines
ms.collection: linux
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a608c252b806e4b6e538ab4849ca305de7602732
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424720"
---
# <a name="run-scripts-in-your-linux-vm-by-using-action-run-commands"></a>Ausführen von Skripts auf Ihrem virtuellen Linux-Computer mithilfe der Aktion Befehle ausführen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Das Feature „Skriptausführung“ verwendet den VM-Agent (virtual machine, virtueller Computer), um Shellskripts innerhalb eines virtuellen Azure-Computers unter Linux auszuführen. Diese Skripts können für die allgemeine Computer- oder Anwendungsverwaltung verwendet werden. Mit ihrer Hilfe können Sie Zugriffs- und Netzwerkprobleme eines virtuellen Computers schnell diagnostizieren und beheben und den virtuellen Computer wieder in einen funktionierenden Zustand versetzen.

## <a name="benefits"></a>Vorteile

Sie können auf verschiedene Weise auf Ihre virtuellen Computer zugreifen. Die Skriptausführung kann Skripts remote unter Verwendung des VM-Agents auf Ihren virtuellen Computern ausführen. Die Skriptausführung kann für virtuelle Linux-Computer über das Azure-Portal, die [REST-API](/rest/api/compute/virtual-machines-run-commands/run-command) oder die [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke) verwendet werden.

Diese Funktion ist in allen Szenarien sinnvoll, in denen Sie ein Skript innerhalb eines virtuellen Computers ausführen möchten. Es ist eine der wenigen Möglichkeiten, Fehler auf einem virtuellen Computer zu beheben, bei dem der RDP- oder SSH-Port aufgrund einer falschen Netzwerk- oder Administratorkonfiguration nicht geöffnet ist.

## <a name="restrictions"></a>Beschränkungen

Die Verwendung der Skriptausführung unterliegt den folgenden Einschränkungen:

* Die Ausgabe ist auf die letzten 4.096 Bytes beschränkt.
* Der Mindestzeitraum für die Ausführung eines Skripts liegt bei etwa 20 Sekunden.
* Skripts werden unter Linux standardmäßig mit erhöhten Benutzerberechtigungen ausgeführt.
* Es können nicht mehrere Skripts gleichzeitig ausgeführt werden.
* Skripts, die Informationen anfordern (interaktiver Modus), werden nicht unterstützt.
* Die Ausführung von Skripts kann nicht abgebrochen werden.
* Die maximal zulässige Ausführungsdauer eines Skripts beträgt 90 Minuten. Danach tritt ein Timeout auf.
* Um die Ergebnisse des Skripts zurückzugeben, ist eine ausgehende Konnektivität des virtuellen Computers erforderlich.

> [!NOTE]
> Die Skriptausführung muss über den Port 443 eine Verbindung mit öffentlichen Azure-IP-Adressen herstellen können. Wenn die Erweiterung keinen Zugriff auf diese Endpunkte hat, werden die Skripts zwar möglicherweise erfolgreich ausgeführt, geben aber keine Ergebnisse zurück. Wenn Sie Datenverkehr auf dem virtuellen Computer blockieren, können Sie [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) verwenden, um Datenverkehr mit öffentlichen Azure-IP-Adressen über das Tag `AzureCloud` zuzulassen.

## <a name="available-commands"></a>Verfügbare Befehle

Diese Tabelle enthält die Liste der für virtuelle Linux-Computer verfügbaren Befehle. Mit dem Befehl **RunShellScript** können Sie jedes beliebige benutzerdefinierte Skript ausführen. Wenn Sie einen Befehl mithilfe der Azure-Befehlszeilenschnittstelle oder über PowerShell ausführen, muss für den Parameter `--command-id` oder `-CommandId` einer der Werte aus der folgenden Liste angegeben werden. Wenn Sie einen Wert angeben, bei dem es sich nicht um einen verfügbaren Befehl handelt, tritt der folgende Fehler auf:

```error
The entity was not found in this Azure location
```

|**Name**|**Beschreibung**|
|---|---|
|**RunShellScript**|Führt ein Linux-Shellskript aus.|
|**ifconfig**| Ruft die Konfiguration aller Netzwerkschnittstellen ab.|

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel wird der Befehl [az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) verwendet, um ein Shellskript auf einem virtuellen Azure-Computer unter Linux auszuführen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Wenn Sie Befehle als anderer Benutzer ausführen möchten, geben Sie `sudo -u` ein, um ein Benutzerkonto anzugeben.

## <a name="azure-portal"></a>Azure-Portal

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einem virtuellen Computer, und wählen Sie im linken Menü unter **Vorgänge** die Option **Befehl ausführen** aus. Daraufhin wird eine Liste mit den verfügbaren Befehlen angezeigt, die auf dem virtuellen Computer ausgeführt werden können.

![Befehlsliste](./media/run-command/run-command-list.png)

Wählen Sie einen auszuführenden Befehl aus. Einige der Befehle verfügen möglicherweise über optionale oder erforderliche Eingabeparameter. Bei diesen Befehlen werden die Parameter in Form von Textfeldern für die Eingabewerte angezeigt. Für jeden Befehl können Sie das ausgeführte Skript anzeigen, indem Sie **Skript anzeigen** erweitern. Der Befehl **RunShellScript** unterscheidet sich von den anderen Befehlen, da er die Angabe eines eigenen, benutzerdefinierten Skripts ermöglicht.

> [!NOTE]
> Die integrierten Befehle können nicht bearbeitet werden.

Nachdem Sie den Befehl ausgewählt haben, wählen Sie **Ausführen** aus, um das Skript auszuführen. Nach Abschluss des Skripts gibt es die Ausgabe und eventuelle Fehler im Ausgabefenster zurück. Der folgende Screenshot zeigt eine Beispielausgabe für die Ausführung des **ifconfig**-Befehls.

![Skriptausgabe von „Befehl ausführen“](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Im folgenden Beispiel wird das Cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) zum Ausführen eines PowerShell-Skripts auf einem virtuellen Azure-Computer verwendet. Für das Cmdlet gilt, dass das im Parameter `-ScriptPath` referenzierte Skript am Ausführungsort des Cmdlets lokal sein muss.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Einschränken des Zugriffs auf „Befehl ausführen“

Zum Auflisten der ausführbaren Befehle oder Anzeigen der Details zu einem Befehl ist die Berechtigung `Microsoft.Compute/locations/runCommands/read` erforderlich. Die integrierte Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) und höhere Rollen verfügen über diese Berechtigung.

Zum Ausführen eines Befehls ist die Berechtigung `Microsoft.Compute/virtualMachines/runCommand/action` erforderlich. Die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und höhere Rollen verfügen über diese Berechtigung.

Für die Skriptausführung können Sie eine der [integrierten Rollen](../../role-based-access-control/built-in-roles.md) verwenden oder eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu weiteren Möglichkeiten für die Remoteausführung von Skripts und Befehlen auf Ihrem virtuellen Computer finden Sie unter [Ausführen von Skripts in Ihrer Linux-VM](run-scripts-in-vm.md).
