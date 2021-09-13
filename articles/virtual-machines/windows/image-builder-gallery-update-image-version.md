---
title: Erstellen einer neuen Imageversion aus einer vorhandenen Imageversion mit Azure Image Builder unter Windows
description: Hier erfahren Sie mehr über das Erstellen einer neuen VM-Imageversion aus einer vorhandenen Imageversion mit Azure Image Builder unter Windows.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.collection: windows
ms.openlocfilehash: 08cc123df4d0b4af0d5a0e94d5ef0e4826b0681c
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594803"
---
# <a name="create-a-new-windows-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Erstellen einer neuen VM-Imageversion aus einer vorhandenen Imageversion mit Azure Image Builder unter Windows

In diesem Artikel erfahren Sie, wie Sie eine vorhandene Imageversion in einem [Katalog mit freigegebenen Images](../shared-image-galleries.md) aktualisieren und als neue Imageversion im Katalog veröffentlichen.

Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 


## <a name="register-the-features"></a>Registrieren des Features
Um Azure Image Builder zu verwenden, müssen Sie das Feature registrieren.

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


## <a name="set-variables-and-permissions"></a>Festlegen von Variablen und Berechtigungen

Wenn Sie Ihren Katalog mit freigegebenen Images anhand von [Erstellen eines ein Images und Verteilen des Images in einem Katalog mit freigegebenen Images](image-builder-gallery.md) erstellt haben, haben Sie die benötigten Variablen bereits erstellt. Richten Sie andernfalls nun einige Variablen ein, die in diesem Beispiel verwendet werden.

Image Builder unterstützt nur das Erstellen von benutzerdefinierten Images in derselben Ressourcengruppe, in der sich auch das verwaltete Quellimage befindet. Aktualisieren Sie den Namen der Ressourcengruppe in diesem Beispiel, sodass es sich um dieselbe Ressourcengruppe handelt, in der sich auch das verwaltete Quellimage befindet.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Erstellen Sie eine Variable für Ihre Abonnement-ID. Diese können Sie mit `az account show | grep id` abrufen.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Rufen Sie die Imageversion ab, die Sie aktualisieren möchten.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Erstellen einer vom Benutzer zugewiesene Identität und Festlegen von Berechtigungen für die Ressourcengruppe
Da Sie im vorigen Beispiel die Benutzeridentität eingerichtet haben, müssen Sie nur deren Ressourcen-ID ermitteln, die dann an die Vorlage angefügt wird.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Wenn Sie bereits über einen eigenen Katalog mit freigegebenen Images verfügen und nicht nach dem vorherigen Beispiel vorgegangen sind, müssen Sie Image Builder Berechtigungen zum Zugreifen auf die Ressourcengruppe zuweisen, damit er auf den Katalog zugreifen kann. Überprüfen Sie die Schritte im Beispiel [Erstellen und Verteilen eines Images an eine Shared Image Gallery](image-builder-gallery.md).


## <a name="modify-helloimage-example"></a>Ändern des Beispiels helloImage
Sie können das verwendete Beispiel überprüfen, indem Sie die JSON-Datei [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) zusammen mit der [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md) öffnen. 


Laden Sie das JSON-Beispiel herunter, und konfigurieren Sie es mit Ihren Variablen. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Erstellen des Images

Senden Sie die Imagekonfiguration an den VM-Image Builder-Dienst.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --location $location \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Starten Sie den Imagebuild.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Warten Sie, bis das Image erstellt und repliziert wurde, bevor Sie mit dem nächsten Schritt fortfahren.


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Überprüfen der Anpassung
Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM eine Eingabeaufforderung, und geben Sie Folgendes ein:

```console
dir c:\
```

Sie sollten nun zwei Verzeichnisse sehen:
- `buildActions`, das in der ersten Imageversion erstellt wurde.
- `buildActions2`, das im Rahmen der Aktualisierung der ersten Imageversion zum Erstellen der zweiten Imageversion erstellt wurde.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md).
