---
ms.openlocfilehash: 6f7a0d8169fed92564437166c74255743ea61a7f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820798"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet/).
- Installieren des [ Azure Az PowerShell-Moduls](/powershell/azure/)

## <a name="create-azure-communication-services-resource"></a>Erstellen einer Azure Communication Services-Ressource

Melden Sie sich zunächst bei der [Azure-Befehlszeilenschnittstelle](/cli/azure/authenticate-azure-cli) an, um eine Azure Communication Services-Ressource zu erstellen. Verwenden Sie hierzu am Terminal den ```Connect-AzAccount```-Befehl, und geben Sie Ihre Anmeldeinformationen ein.

Installieren Sie zunächst unbedingt das Azure Communication Services-Modul ```Az.Communication``` mithilfe des folgenden Befehls:

```PowerShell
PS C:\> Install-Module Az.Communication
```

Führen Sie den folgenden Befehl aus, um die Ressource zu erstellen:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Wenn Sie ein bestimmtes Abonnement auswählen möchten, können Sie auch das ```--subscription```-Flag und die Abonnement-ID angeben.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Sie können Ihre Communication Services-Ressource mit den folgenden Optionen konfigurieren:

* Die Ressourcengruppe
* Den Namen der Communication Services-Ressource
* Die Geografie, der die Ressource zugeordnet wird

Im nächsten Schritt können Sie der Ressource Tags zuweisen. Tags können verwendet werden, um Azure-Ressourcen zu organisieren. Weitere Informationen zu Tags finden Sie in der [Dokumentation zum Anwenden von Tags auf Ressourcen](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Verwalten Ihrer Communication Services-Ressource

Führen Sie die folgenden Befehle aus, um Tags zu Ihrer Communication Services-Ressource hinzuzufügen. Sie können auch ein bestimmtes Abonnement als Ziel haben.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Verwenden Sie den folgenden Befehl, um alle Ihre Azure Communication Services-Ressourcen in einem bestimmten Abonnement aufzulisten:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Verwenden Sie den folgenden Befehl, um alle Informationen zu einer bestimmten Ressource aufzulisten:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
