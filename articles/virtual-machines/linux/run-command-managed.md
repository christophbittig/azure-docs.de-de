---
title: Skripte in einer Linux-VM in Azure mit verwalteten Ausführungsbefehlen ausführen (Vorschau)
description: In diesem Thema wird beschrieben, wie Skripte innerhalb einer virtuellen Azure Linux-Maschine mithilfe der aktualisierten verwalteten Funktion "Befehl ausführen" ausgeführt werden können.
services: automation
ms.service: virtual-machines
ms.collection: linux
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71c08740161fd3df80757c6d86a10dec3e917198
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554809"
---
# <a name="preview-run-scripts-in-your-linux-vm-by-using-managed-run-commands"></a>Vorschau: Skripte in Ihrer Linux-VM mit verwalteten Run-Befehlen ausführen

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

> [!IMPORTANT]
> **Der verwaltete Ausführungsbefehl** befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die Funktion "Befehl ausführen" verwendet den Agenten der virtuellen Maschine (VM) für Skripte innerhalb einer Azure Linux VM. Diese Skripts können für die allgemeine Computer- oder Anwendungsverwaltung verwendet werden. Sie können Ihnen helfen, VM-Zugangs- und Netzwerkprobleme schnell zu diagnostizieren und zu beheben und die VM wieder in einen guten Zustand zu versetzen.

Der *aktualisierte* verwaltete Ausführungsbefehl verwendet denselben VM-Agentenkanal zur Ausführung von Skripten und bietet die folgenden Verbesserungen gegenüber dem [ursprünglichen aktionsorientierten Ausführungsbefehl](run-command.md): 
- Unterstützung für aktualisierten Ausführungsbefehl durch ARM-Bereitstellungsvorlage 
- Parallele Ausführung von mehreren Skripten 
- Sequentielle Ausführung von Skripten 
- RunCommand-Skript kann abgebrochen werden  
- Benutzerdefiniertes Skript-Timeout 
- Unterstützung für zeitintensiv Skripte (Stunden/Tage) 
- Übermittlung von Geheimnissen (Parameter, Passwörter) auf sichere Art und Weise


## <a name="register-for-preview"></a>Registrieren für die Vorschau

Sie müssen Ihr Abonnement registrieren, um den verwalteten Ausführungsbefehl während der öffentlichen Vorschau verwenden zu können. Gehen Sie zu [Einrichten von Previewfunktionen in Azure-Abonnement](../../azure-resource-manager/management/preview-features.md) für Registrierungsanweisungen und verwenden Sie den Funktionsnamen `RunCommandPreview`.

## <a name="azure-cli"></a>Azure CLI 

Die folgenden Beispiele verwenden [az vm run-command](/cli/azure/vm/run-command), um ein Shell-Skript auf einer Azure Linux VM auszuführen.

### <a name="execute-a-script-with-the-vm"></a>Ausführen eines Skripts mit der VM
Dieser Befehl liefert das Skript an die VM, führt es aus und gibt die erfasste Ausgabe zurück.

```azurecli-interactive
az vm run-command create --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "echo Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Auflisten aller bereitgestellten RunCommand-Ressourcen auf einer VM 
Mit diesem Befehl erhalten Sie eine vollständige Liste der zuvor bereitgestellten Run Commands mit ihren Eigenschaften. 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>Ausführungsstatus und Ergebnisse abrufen 
Dieser Befehl ruft den aktuellen Ausführungsfortschritt ab, einschließlich der letzten Ausgabe, der Start-/Endzeit, des Exit-Codes und des Terminalstatus der Ausführung.

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>RunCommand-Ressource aus der VM löschen
Entfernen Sie die zuvor auf der VM bereitgestellte RunCommand-Ressource. Wenn die Ausführung des Skripts noch nicht abgeschlossen ist, wird die Ausführung abgebrochen. 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>Ausführen eines Skripts mit der VM
Dieser Befehl liefert das Skript an die VM, führt es aus und gibt die erfasste Ausgabe zurück.

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" – Script "echo Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Auflisten aller bereitgestellten RunCommand-Ressourcen auf einer VM 
Mit diesem Befehl erhalten Sie eine vollständige Liste der zuvor bereitgestellten Run Commands mit ihren Eigenschaften.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>Ausführungsstatus und Ergebnisse abrufen 
Dieser Befehl ruft den aktuellen Ausführungsfortschritt ab, einschließlich der letzten Ausgabe, der Start-/Endzeit, des Exit-Codes und des Terminalstatus der Ausführung.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>RunCommand-Ressource aus der VM löschen
Entfernen Sie die zuvor auf der VM bereitgestellte RunCommand-Ressource. Wenn die Ausführung des Skripts noch nicht abgeschlossen ist, wird die Ausführung abgebrochen. 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName"
```
 


## <a name="rest-api"></a>REST-API 

Um einen neuen Run Command bereitzustellen, führen Sie ein PUT direkt auf der VM aus und geben einen eindeutigen Namen für die Run Command-Instanz an. 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "echo Hello World", 
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
 
- Sie können ein Inline-Skript, einen Skript-URI oder eine integrierte Skript-ID[ als Eingabequelle angeben](run-command.md#available-commands)
- Für die Ausführung eines Befehls wird nur eine Art der Quelleingabe unterstützt 
- Run Command unterstützt die Ausgabe in Storage Blobs, die zum Speichern großer Skriptausgaben verwendet werden können
- Run Command unterstützt Fehlerausgabe in Storage-Blobs 

### <a name="list-running-instances-of-run-command-on-a-vm"></a>Auflisten der laufenden Instanzen von Run Command auf einer VM 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>Ausgabedetails für einen bestimmten Run Command-Bereitstellung abrufen 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>Abbrechen eines bestimmten Run Command-Bereitstellung 

Um eine laufende Bereitstellung abzubrechen, können Sie PUT oder PATCH auf die laufende Instanz von Run Command anwenden und ein leeres Skript im Anforderungstext angeben. Dadurch wird die laufende Ausführung abgebrochen. 

Sie können die Instanz von Run Command auch löschen.  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>Skripte in einer geordneten Reihenfolge bereitstellen 

Um Skripte nacheinander einzusetzen, verwenden Sie eine Bereitstellung vorlage, die eine `dependsOn`-Beziehung zwischen aufeinanderfolgenden Skripten angibt. 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "echo Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>Mehrere Run Commands nacheinander ausführen 

Wenn Sie mehrere RunCommand-Ressourcen mithilfe einer Bereitstellungsvorlage bereitstellen, werden diese standardmäßig gleichzeitig auf der VM ausgeführt. Wenn Sie von den Skripten abhängig sind und eine bestimmte Ausführungsreihenfolge bevorzugen, können Sie die Eigenschaft `dependsOn` verwenden, um sie nacheinander auszuführen. 

In diesem Beispiel wird **zweiterRunCommand** nach **erster RunCommand** ausgeführt. 

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
               "script":"echo First: Hello World!"
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

Informationen zu weiteren Möglichkeiten für die Remoteausführung von Skripts und Befehlen auf Ihrem virtuellen Computer finden Sie unter [Ausführen von Skripts in Ihrer Linux-VM](run-scripts-in-vm.md).
