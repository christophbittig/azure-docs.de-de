---
title: Verschlüsseln von Datenträgern für Azure-Skalierungsgruppen mit Azure PowerShell
description: Erfahren Sie, wie Sie mit Azure PowerShell VM-Instanzen und angefügte Datenträger in Windows-VM-Skalierungsgruppen verschlüsseln können.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9b7ed6cbfcf93e05efb294c7af907a2416c3fe56
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691340"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure PowerShell

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Das Azure PowerShell-Modul dient zum Erstellen und Verwalten von Azure-Ressourcen über die PowerShell-Befehlszeile oder mit Skripts.  In diesem Artikel wird veranschaulicht, wie Sie Azure PowerShell verwenden, um eine VM-Skalierungsgruppe zu erstellen und zu verschlüsseln. Weitere Informationen zum Anwenden von Azure Disk Encryption auf eine VM-Skalierungsgruppe finden Sie unter [Azure Disk Encryption für VM-Skalierungsgruppen](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Erstellen eines für die Datenträgerverschlüsselung aktivierten Azure-Schlüsseltresors

In Azure Key Vault können Schlüssel, geheime Schlüssel und Kennwörter gespeichert werden, um eine sichere Implementierung in Anwendungen und Diensten zu ermöglichen. Kryptografische Schlüssel werden in Azure Key Vault mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel aber auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Die kryptografischen Schlüssel dienen zum Verschlüsseln und Entschlüsseln virtueller Datenträger, die an Ihren virtuellen Computer angefügt sind. Diese kryptografischen Schlüssel werden allein von Ihnen kontrolliert, und Sie können deren Verwendung überwachen.

Erstellen Sie mit [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) einen Schlüsseltresor. Damit der Schlüsseltresor für die Datenträgerverschlüsselung verwendet werden kann, legen Sie den Parameter *EnabledForDiskEncryption* fest. Das folgende Beispiel definiert auch Variablen für den Ressourcengruppennamen, den Schlüsseltresornamen und den Speicherort. Geben Sie Ihren eigenen eindeutigen Schlüsseltresornamen an:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Verwenden eines vorhandenen Schlüsseltresors

Dieser Schritt ist nur erforderlich, wenn Sie über einen vorhandenen Schlüsseltresor verfügen, den Sie mit der Datenträgerverschlüsselung verwenden möchten. Überspringen Sie diesen Schritt, wenn Sie im vorherigen Abschnitt einen Schlüsseltresor erstellt haben.

Sie können mit [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy) einen vorhandenen Schlüsseltresor im selben Abonnement und derselben Region wie die Skalierungsgruppe für die Datenträgerverschlüsselung aktivieren. Definieren Sie den Namen Ihres vorhandenen Schlüsseltresors in der Variablen *$vaultName* wie folgt:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) zuerst den Benutzernamen und das Kennwort des Administrators der VM-Instanzen fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie nun mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Aktivieren der Verschlüsselung

Um VM-Instanzen in einer Skalierungsgruppe zu verschlüsseln, rufen Sie zunächst mit [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault) einige Informationen zum Schlüsseltresor-URI und zur Ressourcen-ID ab. Diese Variablen dienen dann zum Starten des Verschlüsselungsvorgangs mit [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Wenn Sie dazu aufgefordert werden, geben Sie *y* ein, um die Datenträgerverschlüsselung auf VM-Instanzen der Skalierungsgruppe fortzusetzen.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivieren der Verschlüsselung mithilfe von KEK zum Umschließen des Schlüssels

Sie können auch einen Schlüsselverschlüsselungsschlüssel für zusätzliche Sicherheit bei der Verschlüsselung der VM-Skalierungsgruppe verwenden.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Die Syntax für den Wert des Parameters disk-encryption-keyvault ist die vollständige Bezeichnerzeichenfolge:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Die Syntax für den Wert des Parameters key-encryption-key ist der vollständige URI zum KEK, beispielsweise:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Überprüfen des Verschlüsselungsfortschritts

Verwenden Sie [Get AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption) zum Überprüfen des Status der Datenträgerverschlüsselung:


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Wenn VM-Instanzen verschlüsselt sind, gibt der Code *EncryptionSummary* den Wert *ProvisioningState/succeeded* an, wie in der folgenden Beispielausgabe gezeigt:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Deaktivieren der Verschlüsselung

Wenn Sie die verschlüsselten Datenträger von VM-Instanzen nicht mehr verwenden möchten, können Sie die Verschlüsselung mit [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) wie folgt deaktivieren:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Nächste Schritte

- In diesem Artikel haben Sie Azure PowerShell verwendet, um eine VM-Skalierungsgruppe zu verschlüsseln. Sie können auch die [Azure CLI](disk-encryption-cli.md) oder [Azure Resource Manager-Vorlagen](disk-encryption-azure-resource-manager.md) verwenden.
- Wenn Sie Azure Disk Encryption anwenden möchten, nachdem eine weitere Erweiterung bereitgestellt wurde, können Sie [Erweiterungssequenzierung](virtual-machine-scale-sets-extension-sequencing.md) verwenden.
