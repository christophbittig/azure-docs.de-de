---
title: Registrieren eines Ledgerdienstprinzipals bei Microsoft Azure Confidential Ledger
description: Registrieren eines Ledgerdienstprinzipals bei Microsoft Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 843c2489052ed1df78c738b680740d2a7206e4a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454502"
---
# <a name="register-a-confidential-ledger-service-principal"></a>Registrieren eines Confidential Ledger-Dienstprinzipals

Zunächst müssen Sie den Confidential Ledger-Dienstprinzipal registrieren, um Ihrem Confidential Ledger-Dienst ein Speicherkonto zuzuordnen.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Führen Sie zum Erstellen eines Dienstprinzipals den Azure CLI-Befehl [az ad sp create](/cli/azure/ad/sp#az_ad_sp_create) oder die Azure PowerShell-Cmdlets [Connect-AzureAD](/powershell/module/azuread/connect-azuread) und [New-AzureADServicePrincipal](/powershell/module/azuread/new-azureadserviceprincipal) aus.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli-interactive
az ad sp create --id 4353526e-1c33-4fcf-9e82-9683edf52848
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzureAD -TenantId "<tenant-id-of-customer>"
New-AzureADServicePrincipal -AppId 4353526e-1c33-4fcf-9e82-9683edf52848 -DisplayName ConfidentialLedger
```
---

## <a name="assign-roles"></a>Zuweisen von Rollen

Legen Sie für IAM [Mitwirkender an Storage-Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) für den Confidential Ledger-Dienstprinzipal für das Speicherkonto fest. Sie können diese Aktion mit Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment) oder dem Azure PowerShell-Cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) ausführen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli-interactive
az role assignment create --role "Storage Blob Data Contributor" --assignee "4353526e-1c33-4fcf-9e82-9683edf52848" --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId 4353526e-1c33-4fcf-9e82-9683edf52848 -RoleDefinitionName "Storage Blob Data Contributor" -Scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
---

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)
