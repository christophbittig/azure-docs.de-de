---
title: Kontextwechsel in Azure Automation
description: In diesem Artikel wird der Kontextwechsel erläutert, und Sie erfahren mehr darüber, wie Sie Runbookprobleme vermeiden.
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 293dcb57ec25fb4e6f3cc15e64f4b49b2d6ccb5f
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091310"
---
# <a name="context-switching-in-azure-automation"></a>Kontextwechsel in Azure Automation

Kontextwechsel bedeutet, dass der Kontext in einem Prozess den Kontext in einem anderen Prozess ändert. Bei einem Azure-Kontext handelt es sich um einen Satz von Informationen, die das Ziel von Azure PowerShell-Cmdlets definieren. Der Kontext besteht aus den folgenden Eigenschaften:

|Eigenschaft | Beschreibung |
|---|---|
|Name | Der Name des Kontexts.|
|Konto | Dies ist der Benutzername oder Dienstprinzipal, der verwendet wird, um die Kommunikation mit Azure zu authentifizieren.|
|Umgebung | Hiermit wird die globale Azure-Cloud oder eine der nationalen Azure-Clouds dargestellt (z. B. Azure Government). Sie können auch eine Hybrid-Cloud-Plattform wie Azure Stack angeben.|
|Subscription | Hiermit wird das Azure-Abonnement dargestellt, das die Ressourcen enthält, die Sie verwalten möchten.|
|Tenant | Dies ist eine dedizierte und vertrauenswürdige Azure Active Directory-Instanz, die eine einzelne Organisation darstellt.|
|Anmeldeinformationen | Dies sind die Informationen, die von Azure verwendet werden, um Ihre Identität zu überprüfen und die Autorisierung des Zugriffs auf Ressourcen in Azure zu bestätigen.|

Wenn sich ein Konto anmeldet, das auf mehrere Abonnements zugreifen kann, kann jedes dieser Abonnements dem Kontext der Benutzer*innen hinzugefügt werden. Um die Verwendung des richtigen Abonnements zu gewährleisten, müssen Sie es beim Herstellen der Verbindung deklarieren. Verwenden Sie z. B. `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`. Es können jedoch Probleme auftreten, wenn Ihre Runbooks, die ein Abonnement verwalten, im gleichen Sandboxprozess wie Ihre anderen Runbooks ausgeführt werden, die Ressourcen in einem anderen Abonnement aus demselben Automation-Konto verwalten. Änderungen am Kontext, die von einem Runbook vorgenommen werden, können sich bei Verwendung des Standardkontexts auf Ihre anderen Runbooks auswirken. Da der Kontext Informationen wie die zu verwendenden Anmeldeinformationen und das Zielabonnement enthält, können Cmdlets das falsche Abonnement als Ziel verwenden, was möglicherweise dazu führt, dass `not found` oder Berechtigungsfehler ausgegeben werden. Dieses Problem wird als **Kontextwechsel** bezeichnet.

## <a name="manage-azure-contexts"></a>Verwalten von Azure-Kontexten

Lesen Sie die folgenden Empfehlungen, um zu verhindern, dass Ihre Runbooks für Ressourcen im falschen Abonnement ausgeführt werden:

1. Deaktivieren Sie das Speichern des Sandboxkontexts in Ihrem Automation-Runbook, indem Sie den folgenden Befehl am Anfang jedes Runbooks verwenden: `Disable-AzContextAutosave -Scope Process`.
1. Die Azure PowerShell-Cmdlets unterstützen den `-DefaultProfile`-Parameter. Dieser Parameter wurde zu allen Az- und Azure Resource Manager-Cmdlets (AzureRM) hinzugefügt, um das Ausführen mehrerer Skripte in demselben Vorgang zu unterstützen, sodass Sie für jedes Cmdlet angeben können, welcher Kontext verwendet werden soll. Speichern Sie Ihr Kontextobjekt in Ihrem Runbook, wenn es erstellt wird und jedes Mal, wenn es geändert wird. Verweisen Sie dann in jedem Aufruf, der mithilfe des Az- oder AzureRM-Cmdlets erfolgt, auf dieses Objekt. Beispiel: `$AzureContext = Set-AzContext -SubscriptionId $subID`.
1. Übergeben Sie das Kontextobjekt an das PowerShell-Cmdlet (z. B. `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext`).

Hier sehen Sie einen PowerShell-Runbookcodeausschnitt, der eine systemseitig zugewiesene verwaltete Identität verwendet und die Empfehlungen berücksichtigt, einen Kontextwechsel zu vermeiden.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>Mögliche Symptome

Wenn Sie diesen Empfehlungen nicht nachkommen, kann es zwar sein, dass kein Problem auftritt, die Möglichkeit besteht aber dennoch. Das zugrunde liegende Problem in dieser Situation ist die zeitliche Steuerung. Diese hängt davon ab, was jedes Runbook zu dem Zeitpunkt macht, zu dem das andere Runbook seinen Kontext wechselt. Im Folgenden finden Sie einige mögliche Fehlermeldungen. Diese Fehlermeldungen können jedoch durch nicht kontextabhängige Wechselbedingungen verursacht werden.

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die Azure Automation-Kontoauthentifizierung](automation-security-overview.md)
- [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md)
- [Verwalten von Modulen in Azure Automation](./shared-resources/modules.md)

