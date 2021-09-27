---
title: Erstellen eines virtuellen Windows-Computers mit Azure Image Builder
description: Erstellen Sie mit Azure Image Builder einen virtuellen Windows-Computer.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/23/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 83deba3d6fe63f5a63bda04556b911168527b065
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553059"
---
# <a name="create-a-windows-vm-with-azure-image-builder"></a>Erstellen eines virtuellen Windows-Computers mit Azure Image Builder

**Gilt für**: :heavy_check_mark: Windows VMs 

In diesem Artikel erfahren Sie, wie Sie mit Azure VM Image Builder ein benutzerdefiniertes Windows-Image erstellen können. Im Beispiel in diesem Artikel werden [Anpassungen](../linux/image-builder-json.md#properties-customize) für das Image verwendet:
- PowerShell (ScriptUri): lädt ein [PowerShell-Skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1) herunter und führt es aus.
- Windows-Neustart: startet die VM neu.
- PowerShell (inline): führt einen spezifischen Befehl aus. In diesem Beispiel wird mit `mkdir c:\\buildActions` ein Verzeichnis auf dem virtuellen Computer erstellt.
- Datei: kopiert eine Datei aus GitHub auf den virtuellen Computer. In diesem Beispiel wird [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) in `c:\buildArtifacts\index.html` auf den virtuellen Computer kopiert.
- buildTimeoutInMinutes: Der Standardwert hierfür beträgt 240 Minuten, und Sie können die Buildzeit erhöhen, um zeitintensivere Builds zu ermöglichen.
- vmProfile: Geben Sie Eigenschaften für VM-Größe und Netzwerk an.
- osDiskSizeGB: Sie können ein Image vergrößern.
- identity: Geben Sie eine Identität an, die Azure Image Builder während des Buildprozesses verwendet.


Sie können auch `buildTimeoutInMinutes` angeben. Der Standardwert ist 240 Minuten, und Sie können die Buildzeit vergrößern, um zeitintensive Builds zuzulassen. Der zulässige Mindestwert beträgt sechs Minuten. Kürzere Werte führen zu Fehlern.

Wir verwenden zum Konfigurieren des Images eine JSON-Beispielvorlage. Wir verwenden die JSON-Datei [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 



> [!NOTE]
> Für Windows-Benutzer können die folgenden Beispiele der Azure-Befehlszeilenschnittstelle mithilfe von Bash in [Azure Cloud Shell](https://shell.azure.com) ausgeführt werden.


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


## <a name="set-variables"></a>Festlegen von Variablen

Einige Angaben verwenden wir wiederholt. Aus diesem Grund erstellen wir einige Variablen, um diese Informationen zu speichern.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Erstellen Sie eine Variable für Ihre Abonnement-ID.

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Diese Ressourcengruppe wird verwendet, um das Artefakt und Image der Imagekonfigurationsvorlage zu speichern.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Erstellen einer vom Benutzer zugewiesenen Identität und Festlegen von Berechtigungen für die Ressourcengruppe
Image Builder verwendet die angegebene [Benutzeridentität](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity), um das Image in die Ressourcengruppe einzufügen. In diesem Beispiel erstellen Sie eine Azure-Rollendefinition, die über die präzisen Aktionen zur Verteilung des Images verfügt. Die Rollendefinition wird dann der Benutzeridentität zugewiesen.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität und Gewähren von Berechtigungen 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Herunterladen des Beispiels für die Imagekonfigurationsvorlage

Eine parametrisierte Imagekonfigurationsvorlage wurde erstellt, die Sie zum Ausprobieren verwenden können. Laden Sie die JSON-Beispieldatei herunter, und konfigurieren Sie sie mit den zuvor festgelegten Variablen.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Sie können dieses Beispiel im Terminal mithilfe eines Text-Editors wie `vi` ändern.

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Für das Quellimage müssen Sie immer [eine Version angeben](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version). Sie können nicht `latest` verwenden.
> Wenn Sie die Ressourcengruppe, in der das Image verteilt wird, hinzufügen oder ändern, müssen Sie sicherstellen, dass die [Berechtigungen für die Ressourcengruppe festgelegt sind](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group).
 
## <a name="create-the-image"></a>Erstellen des Images

Senden der Imagekonfiguration an den VM-Image Builder-Dienst

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Nach Abschluss des Vorgangs wird eine Erfolgsmeldung an die Konsole zurückgegeben und eine `Image Builder Configuration Template` in `$imageResourceGroup` erstellt. Diese Ressource wird in der Ressourcengruppe im Azure-Portal angezeigt, wenn Sie „Ausgeblendete Typen anzeigen“ aktivieren.

Außerdem erstellt Image Builder im Hintergrund eine Stagingressourcengruppe in Ihrem Abonnement. Diese Ressourcengruppe wird für den Imagebuild verwendet. Sie sollte folgendes Format aufweisen: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Sie dürfen die Stagingressourcengruppe nicht direkt löschen. Löschen Sie zuerst das Artefakt der Imagevorlage. Dabei wird die Stagingressourcengruppe automatisch gelöscht.

Gehen Sie folgendermaßen vor, wenn der Dienst während der Übermittlung der Imagekonfigurationsvorlage einen Fehler meldet:
-  Sehen Sie sich [diese Problembehandlungsschritte](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) an. 
- Sie müssen die Vorlage mit dem folgenden Codeausschnitt löschen, bevor Sie die Übermittlung erneut versuchen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

## <a name="start-the-image-build"></a>Starten des Imagebuilds
Starten Sie den Imagebuildprozess mithilfe von [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Warten Sie, bis der Build abgeschlossen ist. Der Vorgang kann bis zu 15 Minuten dauern.

Wenn Fehler auftreten sollten, prüfen Sie die Schritte unter [Problembehandlung](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors).


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie den virtuellen Computer mithilfe des erstellten Images. Ersetzen Sie *\<password>* durch Ihr eigenes Kennwort für `aibuser` auf dem virtuellen Computer.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Überprüfen der Anpassung

Stellen Sie eine Remotedesktopverbindung mit der VM mit dem Benutzernamen und dem Kennwort her, die Sie beim Erstellen der VM festgelegt haben. Öffnen Sie in der VM eine Eingabeaufforderung, und geben Sie Folgendes ein:

```console
dir c:\
```

Bei der Anpassung des Images sollten diese beiden Verzeichnisse erstellt werden:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Bereinigung

Löschen Sie nach Abschluss des Vorgangs die Ressourcen.

### <a name="delete-the-image-builder-template"></a>Löschen der Image Builder-Vorlage

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Löschen Sie die Rollenzuweisung, die Rollendefinition und die Benutzeridentität.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Löschen der Imageressourcengruppe

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Komponenten der in diesem Artikel verwendeten JSON-Datei finden Sie unter [Image Builder-Vorlagenreferenz](../linux/image-builder-json.md).
