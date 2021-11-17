---
title: Deaktivieren von Funktionen in Azure Functions
description: Erfahren Sie, wie Sie Funktionen in Azure Functions deaktivieren und aktivieren.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: e2df38182dd8b199ca13d7e5a9b7484aee004f52
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265379"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Deaktivieren von Funktionen in Azure Functions

In diesem Artikel wird erläutert, wie Sie eine Funktion in Azure Functions deaktivieren. Durch das *Deaktivieren* einer Funktion wird die Runtime angewiesen, den für die Funktion definierten automatischen Trigger zu ignorieren. Dadurch können Sie das Ausführen einer bestimmten Funktion verhindern, ohne die gesamte Funktions-App zu beenden.

Es wird empfohlen, eine Funktion mithilfe einer App-Einstellung im Format `AzureWebJobs.<FUNCTION_NAME>.Disabled`, das auf `true` festgelegt ist, zu deaktivieren. Sie können diese Anwendungseinstellung auf verschiedene Weise erstellen und ändern, z. B. über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) und im [Azure-Portal](https://portal.azure.com) auf der Registerkarte **Übersicht** der Funktion. 

## <a name="disable-a-function"></a>Deaktivieren einer Funktion

# <a name="portal"></a>[Portal](#tab/portal)

Verwenden Sie die Schaltflächen **Aktivieren** und **Deaktivieren** auf der Seite **Übersicht** der Funktion. Diese Schaltflächen ändern den Wert der App-Einstellung `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Diese funktionsspezifische Einstellung wird beim ersten Deaktivieren erstellt. 

![Option „Funktionszustand“](media/disable-function/function-state-switch.png)

Auch wenn Sie Ihre Funktions-App aus einem lokalen Projekt veröffentlichen, können Sie weiterhin das Portal verwenden, um Funktionen in der Funktions-App zu deaktivieren. 

> [!NOTE]  
> Die im Portal integrierte Testfunktion ignoriert die Einstellung `Disabled`. Dies bedeutet, dass eine deaktivierte Funktion immer noch ausgeführt wird, wenn sie über das Fenster **Test** im Portal gestartet wird. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

Verwenden Sie zum Erstellen und Ändern der App-Einstellung den Azure CLI-Befehl [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). Der folgende Befehl deaktiviert eine Funktion namens `QueueTrigger`. Hierzu wird eine App-Einstellung namens `AzureWebJobs.QueueTrigger.Disabled` erstellt und auf `true` festgelegt. 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Wenn Sie die Funktion wieder aktivieren möchten, führen Sie den gleichen Befehl mit dem Wert `false` aus.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Der Befehl [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) dient zum Hinzufügen oder Aktualisieren einer Anwendungseinstellung. Der folgende Befehl deaktiviert eine Funktion namens `QueueTrigger`. Hierzu wird eine App-Einstellung namens `AzureWebJobs.QueueTrigger.Disabled` erstellt und auf `true` festgelegt. 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Wenn Sie die Funktion wieder aktivieren möchten, führen Sie den gleichen Befehl mit dem Wert `false` aus.

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funktionen in einem Slot

App-Einstellungen gelten standardmäßig auch für Apps, die in Bereitstellungsslots ausgeführt werden. Sie können jedoch die vom Slot verwendete App-Einstellung überschreiben, indem Sie eine slotspezifische App-Einstellung festlegen. Beispielsweise soll eine Funktion in der Produktionsumgebung aktiv sein, aber nicht während der Bereitstellungstests, z. B. eine Funktion, die von einem Timer ausgelöst wird. 

So deaktivieren Sie eine Funktion nur im Bereitstellungsslot

# <a name="portal"></a>[Portal](#tab/portal)

Navigieren Sie zu der Slotinstanz Ihrer Funktions-App, indem Sie unter **Bereitstellung** die Option **Bereitstellungsslots** und dann Ihren Slot und in der Slotinstanz **Funktionen** auswählen.  Wählen Sie Ihre Funktion aus, und verwenden Sie dann die Schaltflächen **Aktivieren** und **Deaktivieren** auf der Seite **Übersicht** der Funktion. Diese Schaltflächen ändern den Wert der App-Einstellung `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Diese funktionsspezifische Einstellung wird beim ersten Deaktivieren erstellt. 

Sie können die App-Einstellung mit dem Namen `AzureWebJobs.<FUNCTION_NAME>.Disabled` und dem Wert `true` auch direkt in der **Konfiguration** für die Slotinstanz hinzufügen. Wenn Sie eine slotspezifische App-Einstellung hinzufügen, stellen Sie sicher, dass Sie das Kontrollkästchen **Bereitstellungssloteinstellung** aktivieren. Dadurch wird der Einstellungswert während eines Austauschs für den Slot beibehalten.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Wenn Sie die Funktion wieder aktivieren möchten, führen Sie den gleichen Befehl mit dem Wert `false` aus.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell unterstützt diese Funktion zurzeit nicht.

---

Weitere Informationen finden Sie unter [Azure Functions-Bereitstellungsslots](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

Funktionen können bei lokaler Ausführung auf die gleiche Weise deaktiviert werden. Um eine Funktion mit dem Namen `HttpExample` zu deaktivieren, fügen Sie der Values-Sammlung in der Datei „local.settings.json“ einen Eintrag wie folgt hinzu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": true
  }
}
``` 

## <a name="other-methods"></a>Andere Methoden

Obwohl die Methode der Anwendungseinstellung für alle Sprachen und alle Runtimeversionen empfohlen wird, gibt es verschiedene weitere Möglichkeiten, Funktionen zu deaktivieren. Diese Methoden, die je nach Sprache und Runtimeversion variieren, werden aus Gründen der Abwärtskompatibilität beibehalten. 

### <a name="c-class-libraries"></a>C#-Klassenbibliotheken

In einer Klassenbibliotheksfunktion können Sie auch das `Disable`-Attribut verwenden, um das Auslösen der Funktion zu verhindern. Mit diesem Attribut können Sie den Namen der Einstellung anpassen, mit der die Funktion deaktiviert wird. Verwenden Sie die Version des Attributs, mit der Sie wie im folgenden Beispiel gezeigt einen Konstruktorparameter hinzuzufügen, der auf eine boolesche App-Einstellung verweist:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Mit dieser Methode können Sie die Funktion durch Ändern der App-Einstellung ohne erneutes Kompilieren oder erneutes Bereitstellen aktivieren und deaktivieren. Das Ändern einer App-Einstellung bewirkt einen Neustart der Funktions-App, sodass die Änderung des Zustands „Deaktiviert“ sofort erkannt wird.

Es gibt auch einen Konstruktor für den Parameter, der keine Zeichenfolge für den Einstellungsnamen akzeptiert. Diese Version des Attributs wird nicht empfohlen. Wenn Sie diese Version verwenden, müssen Sie das Projekt erneut kompilieren und bereitstellen, um den deaktivierten Zustand der Funktion zu ändern.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x – Skriptsprachen

In Version 1.x können Sie auch die `disabled`-Eigenschaft in der Datei *function.json* verwenden, um der Runtime mitzuteilen, dass keine Funktion ausgelöst werden soll. Diese Methode funktioniert nur bei Skriptsprachen wie C#-Skripten und JavaScript. Die `disabled`-Eigenschaft kann auf `true` oder auf den Namen einer App-Einstellung festgelegt werden:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
oder 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Im zweiten Beispiel wird die Funktion deaktiviert, wenn eine App-Einstellung mit dem Namen „IS_DISABLED“ vorhanden und auf `true` oder 1 festgelegt ist.

>[!IMPORTANT]  
>Im Portal werden v1.x-Funktionen über Anwendungseinstellungen deaktiviert. Wenn eine Anwendungseinstellung mit der Datei „function.json“ in Konflikt steht, kann ein Fehler auftreten. Sie sollten die Eigenschaft `disabled` aus der Datei „function.json“ entfernen, um Fehler zu verhindern. 

## <a name="considerations"></a>Weitere Überlegungen

Berücksichtigen Sie beim Deaktivieren von Funktionen folgende Aspekte:

+ Wenn Sie eine durch HTTP ausgelöste Funktion mithilfe der in diesem Artikel beschriebenen Methoden deaktivieren, kann der Zugriff auf den Endpunkt bei Ausführung auf dem lokalen Computer weiterhin möglich sein.  

+ Derzeit können Funktionen mit Namen, die einen Bindestrich (`-`) enthalten, nicht deaktiviert werden, wenn sie unter in einem Linux-Plan ausgeführt werden. Falls Sie Ihre Funktionen bei Verwendung von Linux in einem Plan deaktivieren müssen, verwenden Sie keine Bindestriche in Ihren Funktionsnamen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wird das Deaktivieren von automatischen Triggern behandelt. Weitere Informationen zu Triggern finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md).
