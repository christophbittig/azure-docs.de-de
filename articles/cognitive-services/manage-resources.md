---
title: Wiederherstellen gelöschter Cognitive Services-Ressourcen
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Anweisungen zum Wiederherstellen einer bereits gelöschten Cognitive Services-Ressource.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 4dce35c1713a4dcb4880080d1f28ed124a2209be
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546075"
---
# <a name="recover-deleted-cognitive-services-resources"></a>Wiederherstellen gelöschter Cognitive Services-Ressourcen

Dieser Artikel enthält Anweisungen zum Wiederherstellen einer Cognitive Services-Ressource, die bereits gelöscht wurde. Der Artikel enthält auch Anweisungen zum endgültigen Löschen einer gelöschten Ressource.

> [!NOTE]
> Die Anweisungen in diesem Artikel gelten sowohl für eine Ressource für mehrere Dienste als auch für eine Ressource für einen einzelnen Dienst. Eine Ressource für mehrere Dienste ermöglicht den Zugriff auf mehrere Cognitive Services-Instanzen mit einem einzelnen Schlüssel und Endpunkt. Andererseits ermöglicht eine Ressource für einen einzelnen Dienst den Zugriff auf die einzelne Cognitive Services-Instanz, für die die Ressource erstellt wurde.

## <a name="prerequisites"></a>Voraussetzungen

* Die wiederherzustellende Ressource muss innerhalb der letzten 48 Stunden gelöscht worden sein.
* Die wiederherzustellende Ressource darf nicht bereits endgültig gelöscht worden sein. Eine endgültig gelöschte Ressource kann nicht wiederhergestellt werden.
* Bevor Sie eine gelöschte Ressource wiederherzustellen versuchen, vergewissern Sie sich, dass die Ressourcengruppe für dieses Konto vorhanden ist. Wenn die Ressourcengruppe gelöscht wurde, müssen Sie sie neu erstellen. Eine Ressourcengruppe kann nicht wiederhergestellt werden. Weitere Informationen finden Sie unter  [Verwalten von Ressourcengruppen](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Wenn die gelöschte Ressource kundenseitig verwaltete Schlüssel mit Azure Key Vault verwendet hat und der Schlüsseltresor ebenfalls gelöscht wurde, müssen Sie ihn zuerst wiederherstellen, bevor Sie die Cognitive Services-Ressource wiederherstellen. Weitere Informationen finden Sie unter [Verwalten der Azure Key Vault-Wiederherstellung](../key-vault/general/key-vault-recovery.md).
* Wenn die gelöschte Ressource einen kundenseitig verwalteten Speicher verwendet hat und das Speicherkonto ebenfalls gelöscht wurde, müssen Sie das Speicherkonto wiederherstellen, bevor Sie die Cognitive Services-Ressource wiederherstellen. Anweisungen finden Sie unter [Wiederherstellen eines gelöschten Speicherkontos](../storage/common/storage-account-recover.md).

Ihr Abonnement muss über `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete`-Berechtigungen zum Bereinigen von Ressourcen verfügen, z. B. [Cognitive Services-Mitwirkender](../role-based-access-control/built-in-roles.md#cognitive-services-contributor) oder [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor). 

## <a name="recover-a-deleted-resource"></a>Wiederherstellen einer gelöschten Ressource 

Verwenden Sie zum Wiederherstellen einer gelöschten Cognitive Services-Ressource die folgenden Befehle. Ersetzen Sie ggf. Folgendes:

* `{subscriptionID}` durch Ihre Azure-Abonnement-ID
* `{resourceGroup}` durch Ihre Ressourcengruppe
* `{resourceName}` durch Ihren Ressourcennamen
* `{location}` durch den Speicherort Ihrer Ressource

### <a name="using-the-rest-api"></a>Verwenden der REST-API

Verwenden Sie den folgenden `PUT`-Befehl:

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

Verwenden Sie das folgende JSON-Format im Anforderungstext:

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>PowerShell

Verwenden Sie den folgenden Befehl, um die Ressource wiederherzustellen: 

```powershell
New-AzResource -Location {location} -Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

Wenn Sie den Namen Ihrer gelöschten Ressourcen suchen müssen, können Sie mit dem folgenden Befehl eine Liste der Namen gelöschter Ressourcen abrufen: 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>Endgültiges Löschen einer gelöschten Ressource 

Nachdem Sie eine Ressource gelöscht haben, können Sie 48 Stunden lang keine weitere Ressource mit demselben Namen erstellen. Um eine Ressource mit demselben Namen zu erstellen, müssen Sie die gelöschte Ressource endgültig löschen.

Verwenden Sie zum endgültigen Löschen einer gelöschten Cognitive Services-Ressource die folgenden Befehle. Ersetzen Sie ggf. Folgendes:

* `{subscriptionID}` durch Ihre Azure-Abonnement-ID
* `{resourceGroup}` durch Ihre Ressourcengruppe
* `{resourceName}` durch Ihren Ressourcennamen
* `{location}` durch den Speicherort Ihrer Ressource

> [!NOTE]
> Nachdem eine Ressource endgültig gelöscht wurde, ist sie dauerhaft gelöscht und kann nicht wiederhergestellt werden. Sie verlieren alle Daten und Schlüssel, die der Ressource zugeordnet sind.

### <a name="using-the-rest-api"></a>Verwenden der REST-API

Verwenden Sie den folgenden `DELETE`-Befehl:

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>PowerShell

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>Siehe auch
* [Erstellen einer neuen Ressource über das Azure-Portal](cognitive-services-apis-create-account.md)
* [Erstellen einer neuen Ressource über die Azure CLI](cognitive-services-apis-create-account-cli.md)
* [Erstellen einer neuen Ressource mithilfe der Clientbibliothek](cognitive-services-apis-create-account-client-library.md)
* [Erstellen einer neuen Ressource mithilfe einer ARM-Vorlage](create-account-resource-manager-template.md)
