---
title: 'Bereitstellen eines Clouddiensts (erweiterter Support): PowerShell'
description: Bereitstellen eines Clouddiensts (erweiterter Support) mithilfe von PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: e157fcb3dd3c25d2724d9f0a190596549913edb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229868"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Bereitstellen eines Clouddiensts (erweiterter Support) mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie das PowerShell-Modul `Az.CloudService` verwenden, um Cloud Services (erweiterter Support) mit mehreren Rollen („WebRole“ und „WorkerRole“) in Azure bereitzustellen.

## <a name="pre-requisites"></a>Voraussetzungen

1. Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support), und erstellen Sie die entsprechenden Ressourcen. 
2. Installieren Sie das PowerShell-Modul „Az.CloudService“.

    ```azurepowershell-interactive
    Install-Module -Name Az.CloudService 
    ```

3. Erstellen einer neuen Ressourcengruppe Bei Verwendung einer vorhandenen Ressourcengruppe ist dieser Schritt optional.   

    ```azurepowershell-interactive
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

4. Erstellen Sie ein Speicherkonto und einen Container zum Speichern der Clouddienstpaket-Datei (.cspkg) und der Dienstkonfigurationsdatei (.cscfg). Für das Speicherkonto muss ein eindeutiger Name verwendet werden. Bei Verwendung eines vorhandenen Speicherkontos ist dieser Schritt optional.

    ```azurepowershell-interactive
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```
    
## <a name="deploy-a-cloud-services-extended-support"></a>Bereitstellen einer Instanz von Cloud Services (erweiterter Support)

Verwenden Sie eines der folgenden PowerShell-Cmdlets, um Cloud Services bereitzustellen (erweiterter Support):

1. [**Schnellerstellung eines Clouddiensts mithilfe eines Speicherkontos**](#quick-create-cloud-service-using-a-storage-account)

    - Dieser Parametersatz gibt die CSCFG-, CSPKG- und CSDEF-Dateien zusammen mit dem Speicherkonto als Eingaben ein. 
    - Das Rollenprofil, Netzwerkprofil und Betriebssystemprofil des Clouddiensts werden vom Cmdlet mit minimaler Eingabe des Benutzers erstellt. 
    - Für die Zertifikateingabe muss der Schlüsseltresorname angegeben werden. Die Zertifikatfingerabdrücke im Schlüsseltresor werden anhand der in der CSCFG-Datei angegebenen Fingerabdrücke überprüft.
    
 2. [**Schnellerstellung eines Clouddiensts mithilfe eines SAS-URIs**](#quick-create-cloud-service-using-a-sas-uri)
 
    - Dieser Parametersatz gibt den SAS-URI der CSPKG-Datei zusammen mit den lokalen Pfaden der CSDEF- und CSCFG-Dateien ein. Es ist keine Speicherkontoeingabe erforderlich. 
    - Das Rollenprofil, Netzwerkprofil und Betriebssystemprofil des Clouddiensts werden vom Cmdlet mit minimaler Eingabe des Benutzers erstellt. 
    - Für die Zertifikateingabe muss der Schlüsseltresorname angegeben werden. Die Zertifikatfingerabdrücke im Schlüsseltresor werden anhand der in der CSCFG-Datei angegebenen Fingerabdrücke überprüft.
    
3. [**Erstellen eines Clouddiensts mit Rollen-, Betriebssystem-, Netzwerk- und Erweiterungsprofil und SAS-URIs**](#create-cloud-service-using-profile-objects--sas-uris)

    - Dieser Parametersatz gibt die SAS-URIs der CSCFG- und CSPKG-Dateien ein.
    - Die Rollen-, Netzwerk-, Betriebssystem- und Erweiterungsprofile müssen vom Benutzer angegeben werden und mit den Werten in den CSCFG- und CSDEF-Dateien übereinstimmen. 

### <a name="quick-create-cloud-service-using-a-storage-account"></a>Schnellerstellung eines Clouddiensts mithilfe eines Speicherkontos

Erstellen Sie die Clouddienstbereitstellung mithilfe von CSCFG-, CSDEF- und CSPKG-Dateien.

```azurepowershell-interactive
$cspkgFilePath = "<Path to cspkg file>"
$cscfgFilePath = "<Path to cscfg file>"
$csdefFilePath = "<Path to csdef file>"
      
# Create Cloud Service       
New-AzCloudService
-Name "ContosoCS" `
-ResourceGroupName "ContosOrg" `
-Location "EastUS" `
-ConfigurationFile $cscfgFilePath `
-DefinitionFile $csdefFilePath `
-PackageFile $cspkgFilePath `
-StorageAccount $storageAccount `
[-KeyVaultName <string>]
```

### <a name="quick-create-cloud-service-using-a-sas-uri"></a>Schnellerstellung eines Clouddiensts mithilfe eines SAS-URIs

1. Laden Sie Ihr Clouddienstpaket (.cspkg) in das Speicherkonto hoch.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    $cscfgFilePath = "<Path to cscfg file>"
    $csdefFilePath = "<Path to csdef file>"
    ```

 2. Erstellen Sie die Clouddienstbereitstellung mithilfe von CSCFG-, CSDEF- und CSPKG-SAS-URIs.

    ```azurepowershell-interactive
    New-AzCloudService
        -Name "ContosoCS" `
        -ResourceGroupName "ContosOrg" `
        -Location "EastUS" `
        -ConfigurationFile $cspkgFilePath `
        -DefinitionFile $csdefFilePath `
        -PackageURL $cspkgUrl `
        [-KeyVaultName <string>]
    ```
    
### <a name="create-cloud-service-using-profile-objects--sas-uris"></a>Erstellen eines Clouddiensts mithilfe von Profilobjekten und SAS-URIs

1.  Laden Sie Ihre Clouddienstkonfiguration (.cscfg) in das Speicherkonto hoch. 

    ```azurepowershell-interactive
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```
2. Laden Sie Ihr Clouddienstpaket (.cspkg) in das Speicherkonto hoch.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
    
3. Erstellen Sie ein virtuelles Netzwerk und ein Subnetz. Bei Verwendung eines vorhandenen Netzwerks und Subnetzes ist dieser Schritt optional. In diesem Beispiel werden für beide Clouddienstrollen („WebRole“ und „WorkerRole“) ein einzelnes virtuelles Netzwerk und ein einzelnes Subnetz verwendet. 

    ```azurepowershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
4. Erstellen Sie eine öffentliche IP-Adresse, und legen Sie die DNS-Bezeichnungseigenschaft der öffentlichen IP-Adresse fest. Von Cloud Services (erweiterter Support) werden nur öffentliche IP-Adressen mit der SKU [Basic](../virtual-network/ip-services/public-ip-addresses.md#basic) unterstützt. Öffentliche IP-Adressen der Standard-SKU funktionieren nicht mit Cloud Services.
Wenn Sie eine statische IP-Adresse verwenden, muss darauf in der Dienstkonfigurationsdatei (.cscfg) als reservierte IP verwiesen werden.

    ```azurepowershell-interactive
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

5. Erstellen Sie ein Netzwerkprofilobjekt, und ordnen Sie die öffentliche IP-Adresse dem Front-End des Lastenausgleichs zu. Die Azure-Plattform erstellt automatisch eine Lastenausgleichsressource der SKU „Klassisch“ in demselben Abonnement wie die Clouddienstressource. Die Lastenausgleichsressource ist eine schreibgeschützte Ressource in Azure Resource Manager. Alle Aktualisierungen der Ressource werden nur über die Clouddienst-Bereitstellungsdateien (.cscfg und .csdef) unterstützt.

    ```azurepowershell-interactive
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
6. Erstellen einer Key Vault-Instanz Diese Key Vault-Instanz wird zum Speichern von Zertifikaten verwendet, die den Rollen des Clouddiensts (erweiterter Support) zugeordnet sind. Die Key Vault-Instanz muss in der gleichen Region und im gleichen Abonnement erstellt werden wie der Clouddienst und einen eindeutigen Namen besitzen. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten mit Azure Cloud Services (erweiterter Support)](certificates-and-key-vault.md).

    ```azurepowershell-interactive
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

7. Aktualisieren Sie die Key Vault-Zugriffsrichtlinie, und erteilen Sie Ihrem Benutzerkonto Zertifikatberechtigungen. 

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Alternativ können Sie die Zugriffsrichtlinie per Objekt-ID festlegen (diese ID kann durch Ausführen von `Get-AzADUser` abgerufen werden). 
    
    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

8. In diesem Beispiel fügen wir einer Key Vault-Instanz ein selbstsigniertes Zertifikat hinzu. Der Zertifikatfingerabdruck muss in der Clouddienst-Konfigurationsdatei (.cscfg) für die Bereitstellung in Clouddienstrollen hinzugefügt werden. 

    ```azurepowershell-interactive
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
9. Erstellen Sie ein In-Memory-Objekt vom Typ „Betriebssystemprofil“. Das Betriebssystemprofil gibt die Zertifikate an, die Clouddienstrollen zugeordnet sind. Hierbei handelt es sich um das Zertifikat, das im vorherigen Schritt erstellt wurde. 

    ```azurepowershell-interactive
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

10. Erstellen Sie ein In-Memory-Objekt vom Typ „Rollenprofil“. Das Rollenprofil definiert SKU-spezifische Eigenschaften einer Rolle wie Name, Kapazität und Ebene. In diesem Beispiel wurden zwei Rollen definiert: „frontendRole“ und „backendRole“. Die Rollenprofilinformationen müssen der Rollenkonfiguration entsprechen, die in der Konfigurationsdatei (.cscfg) und in der Dienstdefinitionsdatei (.csdef) definiert ist. 

    ```azurepowershell-interactive
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

11. Optional: Erstellen Sie ein In-Memory-Objekt vom Typ „Erweiterungsprofil“, das Sie Ihrem Clouddienst hinzufügen möchten. In diesem Beispiel wird die RDP-Erweiterung hinzugefügt. 

    ```azurepowershell-interactive
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    
    „ConfigFile“ sollte nur über Tags vom Typ „PublicConfig“ verfügen und etwa folgenden Namespace enthalten:
   
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
    
12. Optional: Definieren Sie Tags als PowerShell-Hashtabelle, die Sie Ihrem Clouddienst hinzufügen möchten. 

    ```azurepowershell-interactive
    $tag=@{"Owner" = "Contoso"} 
    ```

13. Erstellen Sie eine Clouddienstbereitstellung mithilfe von Profilobjekten und SAS-URLs.

    ```azurepowershell-interactive
    $cloudService = New-AzCloudService ` 
        -Name “ContosoCS” ` 
        -ResourceGroupName “ContosOrg” ` 
        -Location “East US” ` 
        -PackageUrl $cspkgUrl ` 
        -ConfigurationUrl $cscfgUrl ` 
        -UpgradeMode 'Auto' ` 
        -RoleProfile $roleProfile ` 
        -NetworkProfile $networkProfile  ` 
        -ExtensionProfile $extensionProfile ` 
        -OSProfile $osProfile `
        -Tag $tag 
    ```

## <a name="next-steps"></a>Nächste Schritte 
- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
- Stellen Sie eine Cloud Service-Instanz (erweiterter Support) über das [Azure-Portal](deploy-portal.md), mit [PowerShell](deploy-powershell.md), einer [Vorlage](deploy-template.md) oder [Visual Studio](deploy-visual-studio.md) bereit.
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).