---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: fa07459629d832516fda5d3141f85b23afe3fbb8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861594"
---
Ein Ressourcenanbieter ist ein Dienst, der Azure-Ressourcen bereitstellt. Verwenden Sie den Azure CLI-Befehl [az provider register](/cli/azure/provider#az_provider_register) oder das Azure PowerShell-Cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), um den Azure Confidential Ledger-Ressourcenanbieter „microsoft.ConfidentialLedger“ zu registrieren.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

Mit dem Azure CLI-Befehl [az provider register](/cli/azure/provider#az_provider_show) oder mit dem Azure PowerShell-Cmdlet [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) können Sie überprüfen, ob die Registrierung abgeschlossen ist.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
