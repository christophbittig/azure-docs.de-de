---
title: Ausführen von Skripts auf einer Windows-VM in Azure mithilfe von verwalteter Skriptausführung (Vorschau)
description: In diesem Thema wird beschrieben, wie Skripts auf einem virtuellen Azure Windows-Computer mithilfe des aktualisierten Features „Skriptausführung“ ausgeführt werden.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a1617fea3ea7de71a040852f4906ecffe1b0432
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557887"
---
# <a name="preview-run-scripts-in-your-windows-vm-by-using-managed-run-commands"></a>Vorschau: Ausführen von Skripts auf Ihrer Windows-VM mithilfe verwalteter Skriptausführung

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

> [!IMPORTANT]
> **Verwaltete Skriptausführung** ist aktuell als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Das Feature „Skriptausführung“ verwendet den VM-Agent, um Skripts auf einer Azure Windows-VM auszuführen. Diese Skripts können für die allgemeine Computer- oder Anwendungsverwaltung verwendet werden. Mit ihrer Hilfe können Sie VM-Zugriffs- und -Netzwerkprobleme schnell diagnostizieren und beheben und die VM wieder in einen funktionierenden Zustand versetzen.

Die *aktualisierte* verwaltete Skriptausführung verwendet den gleichen VM-Agent-Kanal zum Ausführen von Skripts und bietet die folgenden Verbesserungen im Vergleich zur [ursprünglichen aktionsorientierten Skriptausführung](run-command.md): 
- Unterstützung für aktualisierte Skriptausführung über eine ARM-Bereitstellungsvorlage 
- Parallele Ausführung mehrerer Skripts 
- Sequenzielle Ausführung von Skripts 
- RunCommand-Skript kann abgebrochen werden  
- Vom Benutzer angegebenes Skripttimeout 
- Unterstützung für zeitintensive Skripts (Stunden/Tage) 
- Sicheres Übergeben von Geheimnissen (Parameter, Kennwörter)


## <a name="register-for-preview"></a>Registrieren für die Vorschau

Sie müssen Ihr Abonnement registrieren, um verwaltete Skriptausführung während der öffentlichen Vorschauphase verwenden zu können. Wechseln Sie zu [Einrichten von Previewfunktionen im Azure-Abonnement](../../azure-resource-manager/management/preview-features.md), um Registrierungsanweisungen zu erhalten, und verwenden Sie den Featurenamen `RunCommandPreview`.

## <a name="azure-cli"></a>Azure CLI 

Im folgenden Beispiel wird der Befehl [az vm run-command](/cli/azure/vm/run-command) verwendet, um ein Shellskript auf einer Azure Windows-VM auszuführen.

### <a name="execute-a-script-with-the-vm"></a>Ausführen eines Skripts mit der VM
Dieser Befehl übermittelt das Skript an die VM, führt es aus und gibt die erfasste Ausgabe zurück.

```azurecli-interactive
az vm run-command create --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Auflisten aller bereitgestellten RunCommand-Ressourcen auf einer VM 
Dieser Befehl gibt eine vollständige Liste der zuvor bereitgestellten Skriptausführungen zusammen mit ihren Eigenschaften zurück. 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>Abrufen des Ausführungsstatus und der Ergebnisse 
Dieser Befehl ruft den aktuellen Ausführungsstatus ab, z. B. die aktuelle Ausgabe, die Start-/Endzeit, den Exitcode und den Endzustand der Ausführung.

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Löschen der RunCommand-Ressource von der VM
Entfernen Sie die zuvor auf der VM bereitgestellte RunCommand-Ressource. Wenn die Skriptausführung aktuell noch stattfindet, wird die Ausführung beendet. 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>Ausführen eines Skripts mit der VM
Dieser Befehl übermittelt das Skript an die VM, führt es aus und gibt die erfasste Ausgabe zurück.

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" – Script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Auflisten aller bereitgestellten RunCommand-Ressourcen auf einer VM 
Dieser Befehl gibt eine vollständige Liste der zuvor bereitgestellten Skriptausführungen zusammen mit ihren Eigenschaften zurück.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>Abrufen des Ausführungsstatus und der Ergebnisse 
Dieser Befehl ruft den aktuellen Ausführungsstatus ab, z. B. die aktuelle Ausgabe, die Start-/Endzeit, den Exitcode und den Endzustand der Ausführung.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Löschen der RunCommand-Ressource von der VM
Entfernen Sie die zuvor auf der VM bereitgestellte RunCommand-Ressource. Wenn die Skriptausführung aktuell noch stattfindet, wird die Ausführung beendet. 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName"
```
 

## <a name="rest-api"></a>REST-API 

Um eine neue Skriptausführung bereitzustellen, führen Sie einen PUT-Befehl direkt auf der VM aus, und geben Sie einen eindeutigen Namen für die Skriptausführungsinstanz an. 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "Write-Host Hello World!", 
        "scriptUri": "<URI>",  
        "commandId": "<Id>"  
        }, 
    "parameters": [ 
        { 
            "name": "param1",
            "value": "value1" 
            }, 
        { 
            "name": "param2", 
            "value": "value2" 
            } 
        ], 
    "protectedParameters": [ 
        { 
            "name": "secret1", 
            "value": "value1" 
            }, 
        { 
            "name": "secret2", 
            "value": "value2" 
            } 
        ], 
    "runAsUser": "userName",
    "runAsPassword": "userPassword", 
    "timeoutInSeconds": 3600, 
    "outputBlobUri": "<URI>", 
    "errorBlobUri": "<URI>"  
    }
}
```

### <a name="notes"></a>Notizen
 
- Sie können ein Inlineskript, einen Skript-URI oder eine integrierte [Skriptbefehls-ID](run-command.md#available-commands) als Eingabequelle angeben.
- Für eine Befehlsausführung wird nur ein Typ von Quelleingabe unterstützt. 
- Die Skriptausführung unterstützt die Ausgabe in Storage-Blobs, die zum Speichern großer Skriptausgaben verwendet werden können.
- Die Skriptausführung unterstützt Fehlerausgabe an Storage-Blobs. 


### <a name="list-running-instances-of-run-command-on-a-vm"></a>Auflisten ausgeführter Instanzen der Skriptausführung auf einer VM 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>Abrufen von Ausgabedetails für eine bestimmte Skriptausführungsbereitstellung 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>Abbrechen einer bestimmten Skriptausführungsbereitstellung 

Um eine ausgeführte Bereitstellung abzubrechen, können Sie PUT oder PATCH für die ausgeführte Instanz der Skriptausführung ausführen und ein leeres Skript im Anforderungstext angeben. Dadurch wird die laufende Ausführung abgebrochen. 

Sie können auch die Instanz der Skriptausführung löschen.  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>Bereitstellen von Skripts in einer geordneten Reihenfolge 

Um Skripts sequenziell bereitzustellen, verwenden Sie eine Bereitstellungsvorlage, die eine `dependsOn`-Beziehung zwischen sequenziellen Skripts angibt. 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "Write-Host Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>Sequenzielles Ausführen mehrerer Skriptausführungen 

Wenn Sie mehrere RunCommand-Ressourcen mithilfe einer Bereitstellungsvorlage bereitstellen, werden diese standardmäßig gleichzeitig auf der VM ausgeführt. Wenn Sie eine Abhängigkeit von den Skripts und eine bevorzugte Ausführungsreihenfolge haben, können Sie die `dependsOn`-Eigenschaft verwenden, damit sie sequenziell ausgeführt werden. 

In diesem Beispiel wird **secondRunCommand** nach **firstRunCommand** ausgeführt. 

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "resources":[
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/firstRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
         ],
         "properties":{
            "source":{
               "script":"Write-Host First: Hello World!"
            },
            "parameters":[
               {
                  "name":"param1",
                  "value":"value1"
               },
               {
                  "name":"param2",
                  "value":"value2"
               }
            ],
            "timeoutInSeconds":20
         }
      },
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/secondRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'),'runcommands/firstRunCommand')]"
         ],
         "properties":{
            "source":{
               "scriptUrl":"http://github.com/myscript.ps1"
            },
            "timeoutInSeconds":60
         }
      }
   ]
}
```


## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Möglichkeiten für die Remoteausführung von Skripts und Befehlen in Ihrer VM finden Sie unter [Ausführen von Skripts in Ihrer Windows-VM](run-scripts-in-vm.md).
