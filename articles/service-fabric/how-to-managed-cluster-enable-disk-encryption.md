---
title: Aktivieren Sie die Datenträgerverschlüsselung für die von Service Fabric verwalteten Clusterknoten
description: Hier erfahren Sie, wie Sie die Datenträgerverschlüsselung für verwaltete Azure Service Fabric-Clusterknoten in Windows mithilfe einer ARM-Vorlage aktivieren.
ms.topic: how-to
ms.date: 11/8/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d1d1db17d6914904d9995883903c50982de84daa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486945"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Aktivieren von Datenträgerverschlüsselung für verwaltete Service Fabric-Clusterknoten

Die Managed Cluster von Service Fabric unterstützen zwei Optionen für die Verschlüsselung von Datenträgern, um Ihre Daten zu schützen und Ihre organisatorischen Sicherheits- und Compliance-Verpflichtungen zu erfüllen. Die empfohlene Option ist Verschlüsselung auf dem Host, aber auch Azure Disk Encryption wird unterstützt. Überprüfen Sie die [Optionen zur Datenträgerverschlüsselung](../virtual-machines/disk-encryption-overview.md) und stellen Sie sicher, dass die gewählte Option Ihren Anforderungen entspricht.


## <a name="enable-encryption-at-host-preview"></a>Verschlüsselung auf dem Host aktivieren (Vorschau)

Diese Verschlüsselungsmethode verbessert die [Azure Disk Encryption](how-to-managed-cluster-enable-disk-encryption.md), indem sie alle OS-Typen und Images, einschließlich benutzerdefinierter Images, für Ihre VMs unterstützt, indem sie Daten im Azure Storage Service verschlüsselt. Diese Methode nutzt weder die CPU Ihrer VMs noch beeinträchtigt sie die Leistung Ihrer VMs, so dass die Arbeitslasten alle verfügbaren SKU-Ressourcen der VMs nutzen können.

> [!Note]
> Sie können diese Funktion nicht für bestehende Knotentypen aktivieren. Sie müssen einen neuen Knotentyp bereitstellen und Ihre Arbeitslast migrieren.

> [!Note]
> Der Status der Datenträgerverschlüsselung im Azure Security Center wird derzeit als "Unhealthy" angezeigt, wenn Verschlüsselung auf dem Host verwendet wird

Führen Sie die folgenden Schritte aus und verweisen Sie auf diese [Beispielvorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-HostEncryption), um einen neuen verwalteten Service Fabric-Cluster mit aktivierter Host-Verschlüsselung einzusetzen.

1. Überprüfen Sie die folgenden [Einschränkungen](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#restrictions), um sicherzustellen, dass sie Ihren Anforderungen entsprechen.

2. Richten Sie die erforderlichen [Voraussetzungen](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#prerequisites) vor dem Bereitstellung des Clusters ein.

3. Konfigurieren Sie die Eigenschaft `enableEncryptionAtHost` in der verwalteten Cluster-Vorlage für jeden Node-Typ, für den eine Datenträger-Verschlüsselung erforderlich ist. Das Muster ist vorkonfiguriert.

   * Die apiVersion der von Service Fabric verwalteten Clusterressource muss **2021-11-01-preview** oder höher sein.

   ```json
        {
               "apiVersion": "[variables('sfApiVersion')]",
               "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
               "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
               "location": "[resourcegroup().location]",
               "properties": {
                   "enableEncryptionAtHost": true
                   ...
               }
       }
   ```

4. Einsetzen und Überprüfen

   Stellen Sie Ihren verwalteten Cluster mit aktivierter Host-Verschlüsselung bereit.

   ```powershell
   $clusterName = "<clustername>" 
   $resourceGroupName = "<rg-name>"

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Mithilfe des Befehls `Get-AzVmss` können Sie den Status der Datenträgerverschlüsselung für die zugrunde liegende Skalierungsgruppe eines Knotentyps überprüfen. Zunächst müssen Sie den Namen der unterstützenden Ressourcengruppe Ihres verwalteten Clusters finden (die das zugrunde liegende virtuelle Netzwerk, den Load Balancer, die öffentliche IP-Adresse, die NSG, die Skalierungsgruppe(n) und die Speicherkonten enthält). Achten Sie darauf, dass Sie `NodeTypeNAme` in den Namen des zu überprüfenden Clusterknotens (wie in der Bereitstellungsvorlage angegeben) ändern.

   ```powershell
   $NodeTypeName = "NT2"
   $clustername = <clustername>
   $resourceGroupName = "<rg-name>"
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   $VMSS = Get-AzVmss -ResourceGroupName $supportResourceGroupName -Name $NodeTypeName
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   ```

   Die Rückgabe sollte in etwa so aussehen:

   ```console
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   True
   ```

## <a name="enable-azure-disk-encryption"></a>Azure Disk Encryption aktivieren
Azure Disk Encryption bietet Volume-Verschlüsselung für die Betriebssystem- und Datenträger von virtuellen Azure-Maschinen (VMs) unter Verwendung der DM-Crypt-Funktion in Linux oder der BitLocker-Funktion von Windows. ADE ist in Azure Key Vault integriert, um Ihnen die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel und -geheimnisse zu erleichtern.

In dieser Anleitung erfahren Sie, wie Sie die Datenträgerverschlüsselung für verwaltete Service Fabric-Clusterknoten in Windows mithilfe der Funktion [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) für [VM-Skalierungsgruppen](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) über ARM-Vorlagen (Azure Resource Manager) aktivieren.

1. Registrieren für Azure Disk Encryption

   Die Vorschauversion der Datenträgerverschlüsselung für die VM-Skalierungsgruppe erfordert eine Selbstregistrierung. Führen Sie den folgenden Befehl aus:

   ```powershell
   Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
   ```

   Überprüfen Sie den Registrierungsstatus, indem Sie Folgendes ausführen:

   ```powershell
   Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   ```

   Sobald sich der Status in *Registriert* geändert hat, können Sie den Vorgang fortsetzen.

2. Bereitstellen eines Key Vault für die Datenträgerverschlüsselung

   Azure Disk Encryption erfordert Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung. Ihr Key Vault und Ihr verwalteter Service Fabric-Cluster müssen sich in derselben Azure-Region und demselben Azure-Abonnement befinden. Solange diese Anforderungen erfüllt sind, können Sie einen neuen oder vorhandenen Key Vault verwenden, indem Sie die Datenträgerverschlüsselung für diesen aktivieren.

3. Erstellen eines Key Vault mit aktivierter Datenträger Verschlüsselung

   Führen Sie die folgenden Befehle aus, um einen neuen Key Vault für die Datenträgerverschlüsselung zu erstellen. Vergewissern Sie sich, dass die Region für Ihren Key Vault in der gleichen Region liegt wie Ihr Cluster.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
   New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
   ```

   ---

4. Aktualisieren eines vorhandenen Key Vault zum Aktivieren der Datenträgerverschlüsselung

   Führen Sie die folgenden Befehle aus, um die Datenträgerverschlüsselung für einen vorhandenen Key Vault zu aktivieren.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli
   az keyvault update --name keyvaultName --enabled-for-disk-encryption 
   ```

   ---

### <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Aktualisieren der Vorlagen- und Parameterdateien für die Datenträgerverschlüsselung

Der folgende Schritt führt Sie durch die erforderlichen Änderungen an der Vorlage, um die Datenträgerverschlüsselung für einen [vorhandenen verwalteten Cluster](tutorial-managed-cluster-deploy.md) zu aktivieren. Alternativ können Sie mit der folgenden Vorlage einen neuen verwalteten Service Fabric-Cluster mit aktivierter Datenträgerverschlüsselung bereitstellen: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Fügen Sie der Vorlage die folgenden Parameter hinzu, und ersetzen Sie dabei das Abonnement, den Ressourcengruppennamen und den Key Vault-Namen unter `keyVaultResourceId` durch Ihre eigenen Werte:

   ```json
   "parameters": {
    "keyVaultResourceId": { 
      "type": "string", 
      "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
      "metadata": { 
      "description": "Full resource id of the Key Vault used for disk encryption." 
   } 
    },
    "volumeType": { 
     "type": "string", 
     "defaultValue": "All", 
     "metadata": { 
      "description": "Type of the volume OS or Data to perform encryption operation" 
   }
   }
   }, 
   ```

2. Fügen Sie als Nächstes die VM-Erweiterung `AzureDiskEncryption` den verwalteten Clusterknotentypen in der Vorlage hinzu:

   ```json
   "properties": { 
   "vmExtensions": [ 
   { 
   "name": "AzureDiskEncryption", 
   "properties": { 
     "publisher": "Microsoft.Azure.Security", 
     "type": "AzureDiskEncryption", 
     "typeHandlerVersion": "2.2", 
     "autoUpgradeMinorVersion": true, 
     "settings": {      
           "EncryptionOperation": "EnableEncryption", 
           "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
        "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
        "VolumeType": "[parameters('volumeType')]" 
        } 
      } 
   } 
   ] 
   } 
   ```

3. Aktualisieren Sie schließlich die Parameterdatei, und ersetzen Sie dabei das Abonnement, die Ressourcengruppe und den Key Vault-Namen in *keyVaultResourceId* durch Ihre eigenen Werte:

   ```json
   "parameters": { 
   ...
    "keyVaultResourceId": { 
     "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
     "value": "All" 
    }    
   } 
   ```

4. Bereitstellen und Überprüfen der Änderungen

   Sobald Sie fertig sind, stellen Sie die Änderungen bereit, um die Datenträgerverschlüsselung für Ihren verwalteten Cluster zu aktivieren.

   ```powershell
   $clusterName = "<clustername>" 

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Mithilfe des Befehls `Get-AzVmssDiskEncryption` können Sie den Status der Datenträgerverschlüsselung für die zugrunde liegende Skalierungsgruppe eines Knotentyps überprüfen. Zuerst müssen Sie den Namen der unterstützenden Ressourcengruppe Ihres verwalteten Clusters (mit dem zugrunde liegenden virtuellen Netzwerk, dem Load Balancer, der öffentlichen IP-Adresse, der NSG, den Skalierungsgruppen und Speicherkonten) ermitteln. Achten Sie darauf, dass Sie `VmssName` in den Namen des zu überprüfenden Clusterknotens (wie in der Bereitstellungsvorlage angegeben) ändern.

   ```powershell
   $VmssName = "NT1"
   $clustername = <clustername>
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
   ```

   Die Ausgabe sollte in etwa folgendermaßen aussehen:

   ```console
   ResourceGroupName            : SFC_########-####-####-####-############
   VmScaleSetName               : NT1
   EncryptionEnabled            : True
   EncryptionExtensionInstalled : True
   ```

## <a name="next-steps"></a>Nächste Schritte

[Beispiel: Standard SKU Service Fabric verwalteter Cluster, ein Knotentyp mit aktivierter Datenträger-Verschlüsselung](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption für Windows-VMs](../virtual-machines/windows/disk-encryption-overview.md)

[Verschlüsseln von VM-Skalierungsgruppen mit Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)