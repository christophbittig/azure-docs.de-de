---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f74228b918b3d87ab77b75132501327b08b25668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022042"
---
1. Vergewissern Sie sich, dass die neueste [Azure PowerShell-Version](/powershell/azure/install-az-ps) installiert ist und Sie mit Connect-AzAccount bei einem Azure-Konto angemeldet sind.

1. Erstellen Sie eine Azure Key Vault-Instanz und den Verschlüsselungsschlüssel.

    Beim Erstellen der Key Vault-Instanz müssen Sie den Schutz vor endgültigem Löschen aktivieren. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName `
    -ResourceGroupName $ResourceGroupName `
    -Location $LocationName `
    -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName `
          -Name $keyName `
          -Destination $keyDestination 
    ```

1.    Erstellen Sie eine DiskEncryptionSet-Instanz. Sie können „RotationToLatestKeyVersionEnabled“ auf „$true“ festlegen, um die automatische Rotation des Schlüssels zu aktivieren. Wenn Sie die automatische Rotation aktivieren, aktualisiert das System innerhalb von einer Stunde automatisch alle verwalteten Datenträger, Momentaufnahmen und Images, die auf den Datenträgerverschlüsselungssatz verweisen, damit die neue Version des Schlüssels verwendet wird.  
    
        ```powershell
      $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName `
            -SourceVaultId $keyVault.ResourceId `
            -KeyUrl $key.Key.Kid `
            -IdentityType SystemAssigned `
            -RotationToLatestKeyVersionEnabled $false

       $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName `
               -ResourceGroupName $ResourceGroupName `
               -InputObject $desConfig
        ```

1.    Gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Schlüsseltresor.

        > [!NOTE]
        > Es kann einige Minuten dauern, bis Azure die Identität des Datenträgerverschlüsselungssatzes in Azure Active Directory erstellt hat. Wenn Sie bei der Ausführung des folgenden Befehls einen ähnlichen Fehler wie „Active Directory-Objekt kann nicht gefunden werden“ erhalten, warten Sie einige Minuten, und versuchen Sie es erneut.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

### <a name="use-a-key-vault-in-a-different-subscription"></a>Verwenden eines Schlüsseltresors in einem anderen Abonnement

Alternativ können Sie Ihre Azure Key Vault-Instanzen zentral in einem einzelnen Abonnement verwalten und die im Key Vault gespeicherten Schlüssel verwenden, um verwaltete Datenträger und Momentaufnahmen in anderen Abonnements in Ihrer Organisation zu verschlüsseln. Dadurch kann Ihr Sicherheitsteam eine stabile Sicherheitsrichtlinie für ein einzelnes Abonnement erzwingen und einfach verwalten.

> [!IMPORTANT]
> Für diese Konfiguration müssen sich sowohl Ihre Key Vault-Instanz als auch Ihr Datenträger-Verschlüsselungssatz in derselben Region befinden und denselben Mandanten verwenden.

Das folgende Skript ist ein Beispiel dafür, wie Sie einen Datenträger-Verschlüsselungssatz so konfigurieren, dass er einen Schlüssel aus einer Key Vault-Instanz in einem anderen Abonnement, aber derselben Region verwendet:

```azurepowershell
$sourceSubscriptionId="<sourceSubID>"
$sourceKeyVaultName="<sourceKVName>"
$sourceKeyName="<sourceKeyName>"

$targetSubscriptionId="<targetSubID>"
$targetResourceGroupName="<targetRGName>"
$targetDiskEncryptionSetName="<targetDiskEncSetName>"
$location="<targetRegion>"

Set-AzContext -Subscription $sourceSubscriptionId

$key = Get-AzKeyVaultKey -VaultName $sourceKeyVaultName -Name $sourceKeyName

Set-AzContext -Subscription $targetSubscriptionId

$desConfig=New-AzDiskEncryptionSetConfig -Location $location `
-KeyUrl $key.Key.Kid `
-IdentityType SystemAssigned `
-RotationToLatestKeyVersionEnabled $false

$des=New-AzDiskEncryptionSet -Name $targetDiskEncryptionSetName `
-ResourceGroupName $targetResourceGroupName `
-InputObject $desConfig
```