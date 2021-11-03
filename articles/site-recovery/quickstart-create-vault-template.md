---
title: Schnellstartanleitung zum Erstellen eines Azure Recovery Services-Tresors mithilfe einer Azure Resource Manager-Vorlage
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) einen Azure Recovery Services-Tresor erstellen.
ms.date: 04/28/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: b08ab9a685b08927ec081968656dc2ed47cf2847
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019322"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Schnellstart: Erstellen eines Recovery Services-Tresors mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) einen Recovery Services-Tresor einrichten. Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre BCDR-Strategie (Business Continuity & Disaster Recovery), damit der Onlinezustand Ihrer Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Site Recovery verwaltet die Notfallwiederherstellung von lokalen Computern und virtuellen Azure-Computern (VMs), einschließlich Replikation, Failover und Wiederherstellung.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.recoveryservices%2Frecovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie kein aktives Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.recoveryservices/recovery-services-vault-create/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/vaults): zum Erstellen des Tresors
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backup-resource-storage-configs): zum Konfigurieren der Sicherungsredundanzeinstellungen des Tresors

Die Vorlage enthält optionale Parameter für die Sicherungskonfiguration des Tresors. Als Speicherredundanz kann lokal redundanter Speicher (LRS) oder geografisch redundanter Speicher (GRS) festgelegt werden. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanz](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Weitere Azure Recovery Services-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Zum Bereitstellen der Vorlage sind das **Abonnement**, die **Ressourcengruppe** und der **Tresorname** erforderlich.

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.recoveryservices%2Frecovery-services-vault-create%2Fazuredeploy.json)

1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Vorlage zum Erstellen eines Recovery Services-Tresors":::

   - **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie eine vorhandene Gruppe aus, oder wählen Sie die Option **Neu erstellen** aus, um eine Gruppe hinzuzufügen.
   - **Standort**: Für diese Option wird standardmäßig der Standort der Ressourcengruppe festgelegt, und die Option ist nach der Auswahl einer Ressourcengruppe nicht mehr verfügbar.
   - **Tresorname**: Geben Sie einen Namen für den Tresor an.
   - **Change Storage Type** (Speichertyp ändern): Die Standardeinstellung lautet **false**. Wählen Sie **true** nur aus, wenn Sie den Speichertyp des Tresors ändern müssen.
   - **Vault Storage Type** (Speichertyp des Tresors): Die Standardeinstellung lautet **GloballyRedundant**. Wurde der Speichertyp auf **true** festgelegt, wählen Sie **LocallyRedundant** aus.
   - **Standort**: Für die Funktion `[resourceGroup().location]` wird standardmäßig der Standort der Ressourcengruppe festgelegt. Wenn Sie den Standort ändern möchten, geben Sie einen Wert ein, etwa **westus**.
   - Aktivieren Sie das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**.

1. Wählen Sie die Schaltfläche **Kaufen** aus, um mit der Bereitstellung des Tresors zu beginnen. Nach einer erfolgreichen Bereitstellung wird eine Benachrichtigung angezeigt.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Erfolgreiche Tresorbereitstellung":::

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Verwenden Sie die Azure CLI oder Azure PowerShell, um zu überprüfen, ob der Tresor erstellt wurde.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

Die folgende Ausgabe ist ein Auszug aus den Tresorinformationen:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie beabsichtigen, die neuen Ressourcen zu verwenden, ist keine Aktion erforderlich. Andernfalls können Sie die Ressourcengruppe und den Tresor entfernen, die in dieser Schnellstartanleitung erstellt wurden. Die Ressourcengruppe und die zugehörigen Ressourcen können über die Azure-Befehlszeilenschnittstelle oder mithilfe von Azure PowerShell gelöscht werden.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Recovery Services-Tresor erstellt. Weitere Informationen zur Notfallwiederherstellung finden Sie im nächsten Schnellstartartikel:

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung](azure-to-azure-quickstart.md)
