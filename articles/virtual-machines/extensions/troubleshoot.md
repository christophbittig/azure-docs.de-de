---
title: Problembehandlung bei Fehlern im Zusammenhang mit Windows-VM-Erweiterungen
description: Erfahren Sie mehr über die Problembehandlung für Fehler bei Azure Windows-VM-Erweiterungen.
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292794"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Problembehandlung bei Fehlern im Zusammenhang mit Azure Windows-VM-Erweiterungen
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Anzeigen des Erweiterungsstatus
Azure Resource Manager-Vorlagen können aus Azure PowerShell ausgeführt werden. Sobald die Vorlage ausgeführt wird, kann der Erweiterungsstatus im Azure-Ressourcen-Explorer oder in den Befehlszeilentools angezeigt werden.

Beispiel:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Hier ist die Beispielausgabe:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Problembehandlung bei Erweiterungsfehlern

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>Überprüfen Sie, ob der VM-Agent ausgeführt wird und bereit ist.
Der VM-Agent ist zum Verwalten, Installieren und Ausführen von Erweiterungen erforderlich. Wenn der VM-Agent nicht ausgeführt wird oder nicht den Status Bereit an die Azure-Plattform melden kann, funktionieren die Erweiterungen nicht ordnungsgemäß.

Informationen zur Problembehandlung für den VM-Agent finden Sie auf den folgenden Seiten:
- [Problembehandlung beim Microsoft Azure-Gast-Agent](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent) für eine Windows-VM
- [Behandeln von Problemen mit dem Azure Linux-Agent](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) für eine Linux-VM

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>Suchen Sie nach ihrem spezifischen Leitfaden zur Problembehandlung für Erweiterungen.
Einige Erweiterungen verfügen über eine bestimmte Seite, auf der die Problembehandlung beschrieben wird. Die Liste dieser Erweiterungen und Seiten finden Sie unter [Problembehandlung bei Erweiterungen](./overview.md#troubleshoot-extensions).

### <a name="view-the-extensions-status"></a>Anzeigen des Erweiterungsstatus
Wie oben erläutert wurde, kann der Status der Erweiterung durch Ausführen des PowerShell-Cmdlets:
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

oder des CLI-Befehls:
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

oder im Azure-Portal ermittelt werden, indem Sie zum VM-Blatt /Einstellungen/Erweiterungen navigieren. Sie können dann auf die Erweiterung klicken und deren Status und Meldung überprüfen.


### <a name="rerun-the-extension-on-the-vm"></a>Erneutes Ausführen der Erweiterung auf dem virtuellen Computer
Wenn Sie mithilfe der benutzerdefinierten Skripterweiterung Skripts auf dem virtuellen Computer ausführen, könnten ab und an Fehler auftreten, bei denen der virtuelle Computer erfolgreich erstellt wurde, das Skript jedoch fehlgeschlagen ist. Unter diesen Bedingungen empfiehlt es sich, die Erweiterung zu entfernen und die Vorlage erneut auszuführen, um den Fehler zu beheben.
Hinweis: In Zukunft wird diese Funktionalität verbessert, damit es nicht mehr notwendig ist, die Erweiterung zu deinstallieren.

#### <a name="remove-the-extension-from-azure-powershell"></a>Entfernen der Erweiterung aus Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Nachdem die Erweiterung entfernt wurde, kann die Vorlage erneut ausgeführt werden, um die Skripts auf dem virtuellen Computer auszuführen.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Auslösen eines neuen GoalState für die VM
Möglicherweise stellen Sie fest, dass eine Erweiterung nicht ausgeführt wurde oder aufgrund eines fehlenden „Microsoft Azure CRP Certificate Generator“ nicht ausgeführt werden kann. (Dieses Zertifikat wird verwendet, um den Transport der geschützten Einstellungen der Erweiterung abzusichern.)
Dieses Zertifikat wird automatisch erneut generiert, indem der Windows-Gast-Agent im virtuellen Computer neu gestartet wird:
- Öffnen Sie den Task-Manager.
- Navigieren Sie zur Registerkarte „Details“.
- Suchen Sie den Prozess „WindowsAzureGuestAgent.exe“.
- Klicken Sie mit der rechten Maustaste darauf, und wählen Sie „Task beenden“ aus. Der Prozess wird automatisch neu gestartet.


Ferner können Sie für die VM einen neuen GoalState auslösen, indem Sie ein „VM Reapply“ ausführen. VM [Reapply](/rest/api/compute/virtualmachines/reapply) ist eine API, die 2020 eingeführt wurde und dazu dient, den Zustand einer VM erneut anzuwenden. Wir empfehlen Ihnen, dies zu einem Zeitpunkt durchzuführen, an dem eine kurze Ausfallzeit der VM akzeptabel ist. Zwar löst Reapply seinerseits keinen Neustart der VM aus, und in der überwiegenden Mehrzahl der Fälle führt das Aufrufen von Reapply nicht zu einem Neustart der VM, es besteht jedoch das sehr kleine Risiko, dass ein anderes ausstehendes Update des VM-Modells angewendet wird, wenn Reapply einen neuen Zielzustand auslöst, und diese andere Änderung macht möglicherweise einen Neustart erforderlich. 

Azure-Portal:

Wählen Sie im Portal die VM und im linken Bereich unter **Support und Problembehandlung** die Option **Redeploy + reapply** (Erneut bereitstellen und erneut anwenden) aus. Wählen Sie dann **Reapply** (Erneut anwenden) aus.


Azure PowerShell *(ersetzen Sie den RG-Namen und den VM-Namen durch Ihre Werte)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(ersetzen Sie den RG-Namen und den VM-Namen durch Ihre Werte)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Wenn ein „VM Reapply“ nicht funktioniert, können Sie der VM einen neuen leeren Datenträger für Daten aus dem Azure-Verwaltungsportal hinzufügen und diesen später entfernen, nachdem das Zertifikat wieder hinzugefügt wurde.


### <a name="look-at-the-extension-logs-inside-the-vm"></a>Ansehen der Erweiterungsprotokolle auf dem virtuellen Computer

Wenn die vorherigen Schritte nicht funktioniert haben und sich Ihre Erweiterung weiterhin in einem fehlerhaften Zustand befindet, besteht der nächste Schritt darin, die zugehörigen Protokolle auf dem virtuellen Computer anzuzeigen.

Auf einer **Windows**-VM befinden sich die Erweiterungsprotokolle in der Regel in 
```
C:\WindowsAzure\Logs\Plugins
```
Die Erweiterungseinstellungen und Statusdateien befinden sich in 
```
C:\Packages\Plugins
```
<br/>

Auf einer **Linux**-VM befinden sich die Erweiterungsprotokolle in der Regel in 
```
/var/log/azure/
```
Die Erweiterungseinstellungen und Statusdateien befinden sich in 
```
/var/lib/waagent/
```


Jede Erweiterung ist anders, aber sie folgen in der Regel ähnlichen Prinzipien:

Erweiterungspakete und Binärdateien werden auf den virtuellen Computer heruntergeladen (z. B. _„/var/lib/waagent/custom-script/download/1“_ für Linux oder _„C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0“_ für Windows). 

Ihre Konfiguration und Einstellungen werden über den VM-Agent von der Azure-Plattform an den Erweiterungsbehandler übergeben (z. B. _„/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config“_ für Linux oder _„C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings“_ für Windows)

Erweiterungsbehandler innerhalb des virtuellen Computers schreiben in eine Statusdatei (z. B. _„/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status“_ für Linux oder _„C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status“_ für Windows), die dann an die Azure-Plattform gemeldet wird. Dieser Status wird über PowerShell, die CLI oder auf dem Blatt „Erweiterung“ des virtuellen Computers im Azure-Portal gemeldet.

Außerdem schreiben sie detaillierte Protokolle ihrer Ausführung (z. B. _„/var/log/azure/custom-script/handler.log“_ für Linux oder _„C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log“_ für Windows).


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>Wenn der virtuelle Computer von einem vorhandenen virtuellen Computer neu erstellt wird

Es kann vorkommen, dass Sie eine Azure-VM erstellen, die auf einem spezialisierten Datenträger basiert, der von einem anderen virtuellen Azure-Computer stammt. In diesem Fall ist es möglich, dass die alte VM Erweiterungen enthält und daher Binärdateien, Protokolle und Statusdateien übrig bleiben. Das neue VM-Modell erkennt die Erweiterungsstatus des vorherigen virtuellen Computers nicht und meldet möglicherweise einen falschen Status für diese Erweiterungen. Es wird dringend empfohlen, die Erweiterungen vom alten virtuellen Computer zu entfernen, bevor Sie die neue erstellen, und diese Erweiterungen neu zu installieren, sobald die neue VM erstellt wurde.
Dasselbe kann passieren, wenn Sie ein generalisiertes Image aus einer vorhandenen Azure-VM erstellen. Sie sollten Erweiterungen entfernen, um inkonsistente Status aus den Erweiterungen zu vermeiden.