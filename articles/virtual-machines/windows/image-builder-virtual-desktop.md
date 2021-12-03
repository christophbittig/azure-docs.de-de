---
title: 'Image Builder: Erstellen eines Azure Virtual Desktop-Images'
description: Erstellen Sie ein Azure-VM-Image von Azure Virtual Desktop mithilfe von Azure VM Image Builder in PowerShell.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 05/12/2021
ms.topic: article
ms.service: virtual-machines
ms.collection: windows
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 503b2663d6de83d982e6f8e9c2538de3765c9294
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466654"
---
# <a name="create-a-azure-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Erstellen eines Azure Virtual Desktop-Images mithilfe von Azure VM Image Builder und PowerShell

**Gilt für**: :heavy_check_mark: Windows-VMs 

In diesem Artikel wird erläutert, wie Sie ein Azure Virtual Desktop-Image mit folgenden Anpassungen erstellen:

* Installieren von [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)
* Ausführen eines [Optimierungsskripts für Azure Virtual Desktop](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) aus dem Communityrepository
* Installieren von [Microsoft Teams](../../virtual-desktop/teams-on-avd.md)
* [Neu starten](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* Ausführen von [Windows Update](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer)

Es wird beschrieben, wie Sie diese Vorgänge mithilfe von Azure VM Image Builder automatisieren und das Image an eine [Azure Compute Gallery](../shared-image-galleries.md)-Instanz (zuvor als Shared Image Gallery bezeichnet) verteilen, in der Sie die Replikation in anderen Regionen durchführen, die Skalierung steuern und das Image innerhalb und außerhalb Ihrer Organisationen freigeben können.


Um die Bereitstellung einer Image Builder-Konfiguration zu vereinfachen, wird in diesem Beispiel eine Azure Resource Manager-Vorlage mit der darin geschachtelten Image Builder-Vorlage verwendet. Dies bietet Ihnen einige weitere Vorteile, z. B. Variablen und Parametereingaben. Sie können Parameter auch über die Befehlszeile übergeben.

Dieser Artikel ist als Kopier- und Einfügeübung gedacht.

> [!NOTE]
> Die Skripts zum Installieren der Anwendungen befinden sich in [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Sie dienen nur zur Veranschaulichung und zu Testzwecken und sind nicht für Produktionsworkloads geeignet. 

## <a name="tips-for-building-windows-images"></a>Tipps zum Erstellen von Windows-Images 

- VM-Größe: Die standardmäßige VM-Größe ist `Standard_D1_v2`, die für Windows jedoch nicht geeignet ist. Verwenden Sie `Standard_D2_v2` oder größer.
- In diesem Beispiel werden die [PowerShell-Anpassungsskripts](../linux/image-builder-json.md) verwendet. Sie müssen diese Einstellungen verwenden, andernfalls reagiert der Build nicht mehr.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Beispiel:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Kommentieren des Codes: Das AIB-Buildprotokoll (customization.log) ist sehr ausführlich. Wenn Sie die Skripts mit „write-host“ kommentieren, werden sie an die Protokolle gesendet und erleichtern die Problembehandlung.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Exitcodes: In AIB wird vorausgesetzt, dass alle Skripts den Exitcode 0 zurückgeben. Jeder Exitcode ungleich 0 führt dazu, dass die Anpassung in AIB nicht durchgeführt und der Build beendet wird. Wenn Sie komplexe Skripts verwenden, Instrumentierung hinzufügen und Exitcodes ausgeben, wird dies in „customization.log“ angegeben.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Tests: Führen Sie wiederholt Tests für den Code auf einer eigenständigen VM durch. Stellen Sie sicher, dass keine Eingabeaufforderungen vorhanden sind, dass Sie die richtigen Berechtigungen verwenden usw.

- Netzwerk: `Set-NetAdapterAdvancedProperty`. Dies wird im Optimierungsskript eingestellt, schlägt aber beim AIB-Build fehl, da es die Netzwerkverbindung trennt, dies ist auskommentiert. Es wird derzeit untersucht.

## <a name="prerequisites"></a>Voraussetzungen

Die neuesten Azure PowerShell-Cmdlets müssen installiert sein. Weitere Informationen zur Installation finden Sie in der [Übersicht über Azure PowerShell](/powershell/azure/overview).

```PowerShell
# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not show as registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Einrichten der Umgebung und Variablen

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Berechtigungen, Benutzeridentität und Rolle 


 Erstellen Sie eine Benutzeridentität.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Weisen Sie der Identität Berechtigungen zum Verteilen von Images zu. Mit diesem Befehl wird die Vorlage mit den zuvor angegebenen Parametern heruntergeladen und aktualisiert.

```azurepowershell-interactive
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
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Wenn der Fehler „New-AzRoleDefinition: Das Limit für Rollendefinitionen ist überschritten. Es können keine weiteren Rollendefinitionen erstellt werden.“ angezeigt wird, Weitere Informationen finden Sie unter [Behandeln von Problemen bei Azure RBAC](../../role-based-access-control/troubleshooting.md).


## <a name="create-the-azure-compute-gallery"></a>Erstellen der Azure Compute Gallery 

Wenn Sie noch nicht über eine Azure Compute Gallery-Instanz verfügen, müssen Sie eine solche erstellen.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Konfigurieren der Imagevorlage

Für dieses Beispiel haben wir eine Vorlage vorbereitet, über die die Vorlage mit den zuvor angegebenen Parametern heruntergeladen und aktualisiert wird. FsLogix, Betriebssystemoptimierungen und Microsoft Teams werden installiert. Am Ende wird Windows Update ausgeführt.

Wenn Sie die Vorlage öffnen, können Sie in der source-Eigenschaft das verwendete Image sehen. In diesem Beispiel wird ein Win 10-Image für mehrere Sitzungen verwendet. 

### <a name="windows-10-images"></a>Windows 10-Images
Zwei Haupttypen sind zu unterscheiden: Images für mehrere Sitzungen und für einzelne Sitzungen.

Images für mehrere Sitzungen sind zur Verwendung in einem Pool vorgesehen. Hier ein Beispiel für die Imagedetails in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Images für einzelne Sitzungen sind zur individuellen Verwendung vorgesehen. Hier ein Beispiel für die Imagedetails in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Sie können außerdem die verfügbaren Win 10-Images ändern:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Herunterladen und Konfigurieren der Vorlage

Nun müssen Sie die Vorlage herunterladen und für Ihre spezifische Verwendung konfigurieren.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Sehen Sie sich die [Vorlage](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json) an, der gesamte Code kann angezeigt werden.


## <a name="submit-the-template"></a>Übermitteln der Vorlage

Ihre Vorlage muss an den Dienst übermittelt werden. Dadurch werden alle abhängigen Artefakte (z. B. Skripts) heruntergeladen und die Berechtigungen validiert, überprüft und mit dem Präfix *IT_* in der Stagingressourcengruppe gespeichert.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Erstellen des Image
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Mit dem Befehl wird nicht gewartet, bis der Imagebuild durch den Image Builder-Dienst abgeschlossen wurde. Sie können den Status wie folgt abfragen.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Erstellen einer VM
Sobald der Buildprozess abgeschlossen ist, können Sie eine VM anhand des Images erstellen. Sie können dazu die unter [New-AzVM (Az.Compute)](/powershell/module/az.compute/new-azvm#examples) aufgeführten Beispiele verwenden.

## <a name="clean-up"></a>Bereinigen

Löschen Sie zunächst die Ressourcengruppenvorlage. Löschen Sie nicht nur die gesamte Ressourcengruppe, da andernfalls nicht die in AIB verwendete Stagingressourcengruppe (*IT_* ) bereinigt wird.

Entfernen Sie die Imagevorlage.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Löschen Sie die Rollenzuweisung.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Löschen Sie die Ressourcengruppe.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie in [GitHub](https://github.com/azure/azvmimagebuilder/tree/master/quickquickstarts).

