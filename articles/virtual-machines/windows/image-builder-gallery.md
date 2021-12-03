---
title: Verwenden von Azure Image Builder mit einem Katalog für Windows-VMs
description: Erstellen Sie Azure Shared Gallery-Imageversionen mithilfe von Azure Image Builder und Azure PowerShell.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 224a24de18060568ef8b5ba86f9da8354fab8ddd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462877"
---
# <a name="create-a-windows-image-and-distribute-it-to-an-azure-compute-gallery"></a>Erstellen eines Windows-Images und Verteilen des Images an eine Azure Compute Gallery 

**Gilt für**: :heavy_check_mark: Windows-VMs 

In diesem Artikel erfahren Sie, wie Sie mit Azure Image Builder und Azure PowerShell eine Imageversion in einer [Azure Compute Gallery](../shared-image-galleries.md) (ehemals Shared Image Gallery) erstellen und das Image dann global verteilen. Sie können hierfür auch die [Azure-Befehlszeilenschnittstelle](../linux/image-builder-gallery.md) verwenden.

Wir verwenden eine JSON-Vorlage, um das Image zu konfigurieren. Wir verwenden die JSON-Datei [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Wir laden eine lokale Version der Vorlage herunter und bearbeiten diese, sodass in diesem Artikel die lokale PowerShell-Sitzung verwendet wird.

Die Vorlage verwendet [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) als Wert für den Abschnitt `distribute` der Vorlage, um das Image an die Azure Compute Gallery zu verteilen.

Azure Image Builder führt automatisch Sysprep aus, um das Image zu generalisieren. Dies ist ein generischer Sysprep-Befehl, den Sie bei Bedarf [außer Kraft setzen](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) können. 

Achten Sie darauf, wie oft Sie Anpassungen nacheinander vornehmen. Sie können den Sysprep-Befehl auf einem einzelnen Windows-Image nur eine begrenzte Anzahl von Malen ausführen. Wenn Sie den Sysprep-Grenzwert erreicht haben, müssen Sie Ihr Windows-Image neu erstellen. Weitere Informationen finden Sie unter [Grenzwerte für die Ausführung von Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 


## <a name="register-the-features"></a>Registrieren des Features
Um Azure Image Builder verwenden zu können, müssen Sie das Feature registrieren.

Überprüfen Sie Ihre Anbieterregistrierungen. Vergewissern Sie sich, dass jede `Registered` zurückgibt.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Wenn nicht `Registered` zurückgegeben wird, registrieren Sie die Anbieter wie folgt:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

Installieren Sie PowerShell-Module:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Erstellen von Variablen

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern. Ersetzen Sie die Werte für die Variablen, wie `username` und `vmpassword`, durch Ihre eigenen Angaben.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Azure Compute Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Erstellen einer vom Benutzer zugewiesenen Identität und Festlegen von Berechtigungen für die Ressourcengruppe
Image Builder verwendet die angegebene [Benutzeridentität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), um das Image in die Azure Compute Gallery (SIG) einzufügen. In diesem Beispiel erstellen Sie eine Azure-Rollendefinition, die über die präzisen Aktionen zur Verteilung des Images an die SIG verfügt. Die Rollendefinition wird dann der Benutzeridentität zugewiesen.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Zuweisen von Berechtigungen für die Identität zum Verteilen von Images

Mit diesem Befehl wird eine Azure-Rollendefinitionsvorlage heruntergeladen und die Vorlage mit den zuvor angegebenen Parametern aktualisiert.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-azure-compute-gallery"></a>Erstellen der Azure Compute Gallery

Zum Verwenden von Image Builder mit einer Azure Compute Gallery benötigen Sie einen vorhandenen Katalog und eine Imagedefinition. Der Katalog und die Imagedefinition werden nicht von Image Builder für Sie erstellt.

Wenn Sie noch nicht über einen zu verwendenden Katalog und eine Imagedefinition verfügen, erstellen Sie diese zunächst. Erstellen Sie zunächst einen Katalog.

```powershell
# Gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Herunterladen und Konfigurieren der Vorlage

Laden Sie die JSON-Vorlage herunter, und konfigurieren Sie diese mit Ihren Variablen.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Ihre Vorlage muss an den Dienst übermittelt werden. Dadurch werden alle abhängigen Artefakte wie Skripts heruntergeladen und mit dem Präfix *IT_* in der Stagingressourcengruppe gespeichert.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -ApiVersion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Um das Image zu erstellen, müssen Sie „Ausführen“ für die Vorlage aufrufen.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

Das Erstellen des Images und Replizieren in beiden Regionen kann einige Zeit in Anspruch nehmen. Warten Sie den Abschluss des Vorgangs ab, bevor Sie eine VM erstellen.

Informationen zu den Optionen zum Automatisieren des Imagebuildstatus finden Sie in der [Infodatei].
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie eine VM aus der Imageversion, die von Azure Image Builder erstellt wurde.

Rufen Sie die erstellte Imageversion ab.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Erstellen Sie die VM in der zweiten Region, in der das Image repliziert wurde.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Überprüfen der Anpassung
Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM eine Eingabeaufforderung, und geben Sie Folgendes ein:

```console
dir c:\
```

Es sollte das Verzeichnis `buildActions` angezeigt werden, das bei der Anpassung des Images erstellt wurde.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Imageversion erneut anpassen möchten, um eine neue Version desselben Images zu erstellen, **überspringen Sie den nächsten Schritt**, und [erstellen Sie mit Azure Image Builder eine weitere Imageversion](image-builder-gallery-update-image-version.md).


Das erstellte Image und dessen Ressourcendateien werden gelöscht. Achten Sie darauf, dass Sie mit dieser Bereitstellung fertig sind, bevor Sie die Ressourcen löschen.

Löschen Sie zuerst die Ressourcengruppenvorlage, andernfalls wird die von AIB verwendete Stagingressourcengruppe (*IT_* ) nicht bereinigt.

Rufen Sie die Ressourcen-ID der Imagevorlage ab. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Löschen Sie die Imagevorlage.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Löschen von Rollenzuweisungen

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Definitionen entfernen

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Identität löschen

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

Löschen Sie die Ressourcengruppe.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktualisieren der Imageversion finden Sie im Artikel zum [Erstellen einer weiteren Imageversion mit Azure Image Builder](image-builder-gallery-update-image-version.md).
