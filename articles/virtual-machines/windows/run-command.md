---
title: Ausführen von Skripts auf einem virtuellen Windows-Computer in Azure mithilfe der Aktion Befehle ausführen
description: In diesem Thema wird beschrieben, wie PowerShell-Skripts auf einem virtuellen Azure Windows-Computer mithilfe des Features „Befehl ausführen“ ausgeführt werden.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9501004578068f2c7841fd5f52091f50e97924b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441094"
---
# <a name="run-scripts-in-your-windows-vm-by-using-action-run-commands"></a>Ausführen von Skripts auf Ihrem virtuellen Windows-Computer mithilfe der Aktion Befehle ausführen

Das Feature „Befehl ausführen“ verwendet den VM-Agent, um PowerShell-Skripts innerhalb einer Azure Windows-VM auszuführen. Diese Skripts können für die allgemeine Computer- oder Anwendungsverwaltung verwendet werden. Mit ihrer Hilfe können Sie Zugriffs- und Netzwerkprobleme eines virtuellen Computers schnell diagnostizieren und beheben und den virtuellen Computer wieder in einen funktionierenden Zustand versetzen.


## <a name="benefits"></a>Vorteile

Sie können auf verschiedene Weise auf Ihre virtuellen Computer zugreifen. Die Skriptausführung kann Skripts remote unter Verwendung des VM-Agents auf Ihren virtuellen Computern ausführen. „Befehl ausführen“ kann für Windows-VMs über das Azure-Portal, die [REST-API](/rest/api/compute/virtual-machines-run-commands/run-command) oder [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) verwendet werden.

Diese Funktion ist in allen Szenarien sinnvoll, in denen Sie ein Skript innerhalb eines virtuellen Computers ausführen möchten. Es ist eine der wenigen Möglichkeiten, Fehler auf einem virtuellen Computer zu beheben, bei dem der RDP- oder SSH-Port aufgrund einer falschen Netzwerk- oder Administratorkonfiguration nicht geöffnet ist.

## <a name="restrictions"></a>Beschränkungen

Die Verwendung der Skriptausführung unterliegt den folgenden Einschränkungen:

* Die Ausgabe ist auf die letzten 4.096 Bytes beschränkt.
* Der Mindestzeitraum für die Ausführung eines Skripts liegt bei etwa 20 Sekunden.
* Skripts werden unter Windows als „System“ ausgeführt.
* Es kann Zeit jeweils nur ein Skript gleichzeitig ausgeführt werden.
* Skripts, die Informationen anfordern (interaktiver Modus), werden nicht unterstützt.
* Die Ausführung von Skripts kann nicht abgebrochen werden.
* Die maximal zulässige Ausführungsdauer eines Skripts beträgt 90 Minuten. Danach tritt ein Timeout auf.
* Um die Ergebnisse des Skripts zurückzugeben, ist eine ausgehende Konnektivität des virtuellen Computers erforderlich.
* Es wird nicht empfohlen, ein Skript auszuführen, das bewirkt, dass der VM-Agent angehalten oder aktualisiert wird. Das kann dazu führen, dass die Erweiterung in einem Übergangszustand verbleibt, wodurch ein Timeout verursacht wird.

> [!NOTE]
> Die Skriptausführung muss über den Port 443 eine Verbindung mit öffentlichen Azure-IP-Adressen herstellen können. Wenn die Erweiterung keinen Zugriff auf diese Endpunkte hat, werden die Skripts zwar möglicherweise erfolgreich ausgeführt, geben aber keine Ergebnisse zurück. Wenn Sie Datenverkehr auf dem virtuellen Computer blockieren, können Sie [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) verwenden, um Datenverkehr mit öffentlichen Azure-IP-Adressen über das Tag `AzureCloud` zuzulassen.
> 
> Das Feature „Befehl ausführen“ funktioniert nicht, wenn der Status des VM-Agents „NOT READY“ (NICHT BEREIT) lautet. Überprüfen Sie den Agent-Status im Azure-Portal in den Eigenschaften des virtuellen Computers.

## <a name="available-commands"></a>Verfügbare Befehle

Diese Tabelle enthält die Liste der für virtuelle Windows-Computer verfügbaren Befehle. Mit dem Befehl **RunPowerShellScript** können Sie jedes beliebige benutzerdefinierte Skript ausführen. Wenn Sie einen Befehl mithilfe der Azure-Befehlszeilenschnittstelle oder über PowerShell ausführen, muss für den Parameter `--command-id` oder `-CommandId` einer der Werte aus der folgenden Liste angegeben werden. Wenn Sie einen Wert angeben, bei dem es sich nicht um einen verfügbaren Befehl handelt, tritt der folgende Fehler auf:

```error
The entity was not found in this Azure location
```
<br>

| **Name** | **Beschreibung** |
|---|---|
| **RunPowerShellScript** | Führt ein PowerShell-Skript aus. |
| **DisableNLA** | Deaktiviert die Authentifizierung auf Netzwerkebene. |
| **DisableWindowsUpdate** | Deaktiviert automatische Updates per Windows Update. |
| **EnableAdminAccount** | Überprüft, ob das lokale Administratorkonto deaktiviert ist, und aktiviert es, wenn das der Fall ist. |
| **EnableEMS** | Aktiviert EMS. |
| **EnableRemotePS** | Konfiguriert den Computer für die Aktivierung von remote-PowerShell. |
| **EnableWindowsUpdate** | Aktiviert automatische Updates per Windows Update. |
| **IPConfig** | Zeigt detaillierte Informationen für die IP-Adresse, die Subnetzmaske und das Standardgateway für jeden an TCP/IP gebundenen Adapter an. |
| **RDPSetting** | Überprüft Registrierungseinstellungen und Domänen-Richtlinieneinstellungen. Schlägt Richtlinienaktionen vor, wenn der Computer Teil einer Domäne ist, oder ändert die Einstellungen in Standardwerte. |
| **ResetRDPCert** | Entfernt das TLS-/SSL-Zertifikat, das an den RDP-Listener gebunden ist, und stellt die Standardwerte für die Sicherheit des RDP-Listeners wieder her. Verwenden Sie dieses Skript, wenn irgendwelche Probleme in Verbindung mit dem Zertifikat auftreten. |
| **SetRDPPort** | Legt die standardmäßige oder vom Benutzer angegebene Portnummer für Remote Desktop-Verbindungen fest. Aktiviert die Firewallregeln für eingehenden Zugriff auf den Port. |

## <a name="azure-cli"></a>Azure CLI

Im folgenden Beispiel wird der Befehl [az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) verwendet, um ein Shellskript auf einer Azure Linux-VM auszuführen.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure-Portal

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einem virtuellen Computer, und wählen Sie im linken Menü unter **Vorgänge** die Option **Befehl ausführen** aus. Daraufhin wird eine Liste mit den verfügbaren Befehlen angezeigt, die auf dem virtuellen Computer ausgeführt werden können.

![Befehlsliste](./media/run-command/run-command-list.png)

Wählen Sie einen auszuführenden Befehl aus. Einige der Befehle verfügen möglicherweise über optionale oder erforderliche Eingabeparameter. Bei diesen Befehlen werden die Parameter in Form von Textfeldern für die Eingabewerte angezeigt. Für jeden Befehl können Sie das ausgeführte Skript anzeigen, indem Sie **Skript anzeigen** erweitern. **RunPowerShellScript** unterscheidet sich insofern von den anderen Befehlen, als es Ihnen die Angabe eines eigenen, benutzerdefinierten Skripts ermöglicht.

> [!NOTE]
> Die integrierten Befehle können nicht bearbeitet werden.

Nachdem Sie den Befehl ausgewählt haben, wählen Sie **Ausführen** aus, um das Skript auszuführen. Nach Abschluss des Skripts gibt es die Ausgabe und eventuelle Fehler im Ausgabefenster zurück. Der folgende Screenshot zeigt eine Beispielausgabe für die Ausführung des **RDPSettings**-Befehls.

![Skriptausgabe von „Befehl ausführen“](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Im folgenden Beispiel wird das Cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) zum Ausführen eines PowerShell-Skripts auf einem virtuellen Azure-Computer verwendet. Für das Cmdlet gilt, dass das im Parameter `-ScriptPath` referenzierte Skript am Ausführungsort des Cmdlets lokal sein muss.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Einschränken des Zugriffs auf „Befehl ausführen“

Zum Auflisten der ausführbaren Befehle oder Anzeigen der Details zu einem Befehl ist die Berechtigung `Microsoft.Compute/locations/runCommands/read` auf Abonnementebene erforderlich. Die integrierte Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) und höhere Rollen verfügen über diese Berechtigung.

Zum Ausführen eines Befehls ist die Berechtigung `Microsoft.Compute/virtualMachines/runCommand/action` erforderlich. Die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und höhere Rollen verfügen über diese Berechtigung.

Für die Skriptausführung können Sie eine der [integrierten Rollen](../../role-based-access-control/built-in-roles.md) verwenden oder eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Möglichkeiten für die Remoteausführung von Skripts und Befehlen in Ihrer VM finden Sie unter [Ausführen von Skripts in Ihrer Windows-VM](run-scripts-in-vm.md).
