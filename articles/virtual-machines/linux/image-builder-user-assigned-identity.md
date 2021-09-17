---
title: Erstellen eines VM-Images und Verwenden einer benutzerseitig zugewiesenen verwalteten Identität zum Zugreifen auf Dateien in Azure Storage
description: Erstellen Sie ein VM-Image mit Azure Image Builder, das mithilfe einer benutzerseitig zugewiesenen verwalteten Identität auf in Azure Storage gespeicherte Dateien zugreifen kann.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 588e32e2a531f08319a3120a99ca70048c4c24b2
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770410"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Erstellen eines Images und Verwenden einer benutzerseitig zugewiesenen verwalteten Identität zum Zugreifen auf Dateien in Azure Storage 

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Azure Image Builder unterstützt standardmäßig die Verwendung von Skripts und das Kopieren von Dateien aus mehreren Quellen wie z. B. GitHub und Azure Storage. Azure Image Builder muss extern auf diese zugreifen können, um sie verwenden zu können. Sie können jedoch Azure Storage-Blobs mit SAS-Token schützen.

In diesem Artikel erfahren Sie, wie Sie mit Azure VM Image Builder ein benutzerdefiniertes Image erstellen. Dabei verwendet der Dienst eine [benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md), um für die Imageanpassung auf Dateien in Azure Storage zuzugreifen, ohne die Dateien öffentlich verfügbar machen zu müssen oder SAS-Token einzurichten.

Im unten verwendeten Beispiel erstellen Sie zwei Ressourcengruppen. Eine wird für das benutzerdefinierte Image verwendet, und die andere hostet das Azure-Speicherkonto, das eine Skriptdatei enthält. Dadurch wird ein realitätsnahes Szenario simuliert, bei dem Sie Artefakte oder Imagedateien außerhalb von Azure Image Builder in unterschiedlichen Speicherkonten erstellt haben. Sie erstellen eine benutzerseitig zugewiesene Identität, der Sie dann Leseberechtigungen für die Skriptdatei gewähren, ohne die Datei öffentlich verfügbar zu machen. Anschließend verwenden Sie die Shellanpassung, um das Skript aus dem Speicherkonto herunterzuladen und auszuführen.


## <a name="register-the-features"></a>Registrieren des Features
Um Azure Image Builder verwenden zu können, müssen Sie das Feature registrieren.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Überprüfen Sie den Status der Featureregistrierung.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Überprüfen Sie die Registrierung.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Wenn nicht „registered“ ausgegeben wird, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Erstellen Sie eine Variable für Ihre Abonnement-ID.

```console
subscriptionID=$(az account show --query id --output tsv)
```

Erstellen Sie die Ressourcengruppen für das Image und das Speichern des Skripts.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Erstellen Sie eine vom Benutzer zugewiesene Identität, und legen Sie Berechtigungen für die Ressourcengruppe fest.

Image Builder verwendet die angegebene [Benutzeridentität](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity), um das Image in die Ressourcengruppe einzufügen. In diesem Beispiel erstellen Sie eine Azure-Rollendefinition, die über die präzisen Aktionen zur Verteilung des Images verfügt. Die Rollendefinition wird dann der Benutzeridentität zugewiesen.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Erstellen Sie den Speicher, kopieren Sie das Beispielskript von GitHub, und fügen Sie es dort ein.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Erteilen Sie Azure Image Builder die Berechtigung, Ressourcen in dieser Imageressourcengruppe erstellen zu können. Der Wert `--assignee` ist die Benutzeridentitäts-ID.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Anpassen des Beispiels

Laden Sie die JSON-Beispieldatei herunter, und konfigurieren Sie sie mit den erstellten Variablen.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Erstellen des Images

Senden Sie die Imagekonfiguration an den Azure Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Warten Sie, bis der Buildvorgang abgeschlossen wird. Der Vorgang kann bis zu 15 Minuten dauern.

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie anhand des Images eine VM. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Starten Sie eine SSH-Sitzung, nachdem Sie die VM erstellt haben.

```console
ssh aibuser@<publicIp>
```

Sie sollten sehen, dass das Image angepasst wurde. Es wird eine „Nachricht des Tages“ ausgegeben, sobald die SSH-Verbindung hergestellt wurde.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Bereinigung

Wenn Sie fertig sind, können Sie die Ressourcen löschen, wenn Sie sie nicht mehr brauchen.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie Probleme bei der Arbeit mit Azure Image Builder haben, finden Sie im Artikel zur [Problembehandlung](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json) weitere Informationen.
