---
title: 'Schnellstart: Verwalten von Azure-Dateifreigaben'
description: Erfahren Sie, wie Sie Azure-Dateifreigaben mit dem Azure-Portal, der Azure CLI oder dem Azure PowerShell-Modul erstellen und verwalten. Erstellen Sie ein Speicherkonto und eine Azure-Dateifreigabe, und verwenden Sie die Azure-Dateifreigabe.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 09/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 24a84fb0bc64c4c44d056098e8c429660b88e0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603522"
---
# <a name="quickstart-create-and-manage-azure-file-shares"></a>Schnellstart: Erstellen und Verwalten von Azure-Dateifreigaben
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können unter Windows, Linux und macOS bereitgestellt werden. In dieser Anleitung werden die Grundlagen der Arbeit mit Azure-Dateifreigaben mit dem Azure-Portal, der Azure CLI oder dem Azure PowerShell-Modul Schritt für Schritt beschrieben.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="pre-requisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für diese Anleitung mindestens die Version Az 0.7 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um zu ermitteln, welche Version des Azure PowerShell-Moduls Sie ausführen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um sich bei Ihrem Azure-Konto anzumelden.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Standardmäßig wird für Azure CLI-Befehle JSON-Code (JavaScript Object Notation) zurückgegeben. JSON ist die Standardmethode zum Senden und Empfangen der Nachrichten von REST-APIs. Um die Verwendung von JSON-Antworten zu ermöglichen, wird in einigen Beispielen dieses Artikels für Azure CLI-Befehle der Parameter *query* verwendet. Für diesen Parameter wird die [JMESPath-Abfragesprache](http://jmespath.org/) zum Analysieren des JSON-Codes genutzt. Weitere Informationen zur Verwendung der Ergebnisse von Azure CLI-Befehlen per JMESPath-Abfragesprache erhalten Sie im [JMESPath-Tutorial](http://jmespath.org/tutorial.html).

---

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


### <a name="powershell---create-a-resource-group"></a>PowerShell: Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Falls Sie nicht bereits über eine Azure-Ressourcengruppe verfügen, können Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue erstellen. Sie benötigen eine Ressourcengruppe, um ein Speicherkonto zu erstellen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region „USA, Westen 2“ erstellt:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

### <a name="powershell---create-a-storage-account"></a>PowerShell: Erstellen eines Speicherkontos

Bei einem Speicherkonto handelt es sich um einen gemeinsam genutzten Pool mit Speicherplatz, den Sie zum Bereitstellen von Azure-Dateifreigaben verwenden können.

In diesem Beispiel wird mithilfe des Cmdlets [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Speicherkonto erstellt. Das Speicherkonto wird mit dem Namen *mystorageaccount\<random number>* versehen, und ein Verweis auf das Speicherkonto wird in der Variablen **$storageAcct** gespeichert. Da Speicherkontonamen eindeutig sein müssen, sollten Sie `Get-Random` verwenden, um zu diesem Zweck eine Zahl an den Namen anzufügen. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cli---create-a-resource-group"></a>CLI: Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Falls Sie nicht bereits über eine Azure-Ressourcengruppe verfügen, können Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Sie benötigen eine Ressourcengruppe, um ein Speicherkonto zu erstellen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *USA, Westen 2* erstellt:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

### <a name="cli---create-a-storage-account"></a>CLI: Erstellen eines Speicherkontos
Bei einem Speicherkonto handelt es sich um einen gemeinsam genutzten Pool mit Speicherplatz, in dem Sie Azure-Dateifreigaben bereitstellen können.

Im folgenden Beispiel wird mit dem Befehl [az storage account create](/cli/azure/storage/account) ein Speicherkonto erstellt. Da Speicherkontonamen eindeutig sein müssen, sollten Sie `$RANDOM` verwenden, um zu diesem Zweck eine Zahl an den Namen anzufügen.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```


---


## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe


# <a name="portal"></a>[Portal](#tab/azure-portal)

So erstellen Sie eine Azure-Dateifreigabe:

1. Wählen Sie im Dashboard das Speicherkonto aus.
1. Wählen Sie auf der Speicherkontoseite im Abschnitt **Dienste** die Option **Dateien**.
    
    ![Screenshot: Abschnitt „Datenspeicher“ des Speicherkontos. Auswählen von „Dateifreigaben“](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. Klicken Sie im Menü oben auf der Seite **Dateidienst** auf **Dateifreigabe**. Die Seite **Neue Dateifreigabe** wird angezeigt.
1. Geben Sie unter **Name** den Namen *myshare* und dann ein Kontingent ein. Lassen Sie unter **Tarife** die Option **Transaktion optimiert** ausgewählt.
1. Wählen Sie **Erstellen** aus, um die Azure-Dateifreigabe zu erstellen.

Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und einzelne Bindestriche enthalten und dürfen nicht mit einem Bindestrich beginnen. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nachdem Sie nun ein Speicherkonto erstellt haben, können Sie Ihre erste Azure-Dateifreigabe erstellen. Erstellen Sie eine Dateifreigabe mit dem Cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare). In diesem Beispiel wird eine Freigabe mit dem Namen **myshare** erstellt.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nachdem Sie nun ein Speicherkonto erstellt haben, können Sie Ihre erste Azure-Dateifreigabe erstellen. Erstellen Sie mithilfe des Befehls [az storage share-rm create](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create) Dateifreigaben. In diesem Beispiel wird eine Azure-Dateifreigabe mit dem Namen **myshare** erstellt: 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

---

#### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

# <a name="portal"></a>[Portal](#tab/azure-portal)

Gehen Sie wie folgt vor, um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen *myDirectory* zu erstellen:

1. Wählen Sie auf der Seite **Dateidienst** die Dateifreigabe **myshare** aus. Die Seite für Ihre Dateifreigabe wird geöffnet.
1. Wählen Sie im Menü oben auf der Seite die Option **+ Verzeichnis hinzufügen**. Die Seite **Neues Verzeichnis** wird angezeigt.
1. Geben Sie *myDirectory* ein, und klicken Sie anschließend auf **OK**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory), um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen **myDirectory** zu erstellen.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Verwenden Sie den Befehl [`az storage directory create`](/cli/azure/storage/directory), um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen **myDirectory** zu erstellen:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

---


#### <a name="upload-a-file"></a>Hochladen einer Datei 
# <a name="portal"></a>[Portal](#tab/azure-portal)


Um das Hochladen einer Datei demonstrieren zu können, müssen Sie zuerst eine Datei erstellen oder auswählen, die hochgeladen werden kann. Hierfür können Sie die von Ihnen bevorzugte Vorgehensweise verwenden. Gehen Sie wie folgt vor, nachdem Sie die gewünschte Datei zum Hochladen ausgewählt haben:

1. Wählen Sie das Verzeichnis **myDirectory** aus. Das Panel **myDirectory** wird geöffnet.
1. Wählen Sie im Menü am oberen Rand die Option **Hochladen**. Das Panel **Dateien hochladen** wird geöffnet.  
    
    ![Screenshot: Panel zum Hochladen von Dateien](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Wählen Sie das Ordnersymbol aus, um ein Fenster zu öffnen, in dem Sie Ihre lokalen Dateien durchsuchen können. 
1. Wählen Sie eine Datei und dann **Öffnen** aus. 
1. Überprüfen Sie auf der Seite **Dateien hochladen** den Dateinamen, und wählen Sie dann **Hochladen** aus.
1. Nach Abschluss des Vorgangs sollte die Datei in der Liste auf der Seite **myDirectory** angezeigt werden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um das Hochladen einer Datei mit dem Cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) demonstrieren zu können, müssen wir zuerst auf Ihrem temporären Laufwerk von PowerShell Cloud Shell eine Datei erstellen, die hochgeladen werden kann. 

In diesem Beispiel werden das aktuelle Datum und die Uhrzeit in eine neue Datei auf Ihrem temporären Laufwerk eingefügt, und anschließend wird die Datei auf die Dateifreigabe hochgeladen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Beim lokalen Ausführen von PowerShell ersetzen Sie `~/CloudDrive/` durch einen Pfad, der auf Ihrem Computer vorhanden ist.

Nach dem Hochladen der Datei können Sie das Cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zum Überprüfen verwenden, um sicherzustellen, dass die Datei auf Ihre Azure-Dateifreigabe hochgeladen wurde. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Demonstrieren eines Dateiuploads mit dem Befehl [`az storage file upload`](/cli/azure/storage/file) erstellen Sie zuerst eine Datei zum Hochladen auf das temporäre Cloud Shell-Laufwerk. Im folgenden Beispiel erstellen Sie die Datei und laden diese anschließend hoch:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Beim lokalen Ausführen der Azure CLI ersetzen Sie `~/clouddrive` durch einen Pfad, der auf Ihrem Computer vorhanden ist.

Nach dem Hochladen der Datei können Sie mit dem Befehl [`az storage file list`](/cli/azure/storage/file) sicherstellen, dass die Datei auf Ihre Azure-Dateifreigabe hochgeladen wurde:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```


---

#### <a name="download-a-file"></a>Herunterladen einer Datei
# <a name="portal"></a>[Portal](#tab/azure-portal)

Sie können eine Kopie der hochgeladenen Datei herunterladen, indem Sie mit der rechten Maustaste auf die Datei klicken. Nachdem Sie die Schaltfläche zum Herunterladen ausgewählt haben, richtet sich die genaue Vorgehensweise nach dem verwendeten Betriebssystem und Browser.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie können das Cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) verwenden, um eine Kopie der Datei herunterzuladen, die Sie auf das temporäre Laufwerk von Cloud Shell hochgeladen haben.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Nach dem Herunterladen der Datei können Sie `Get-ChildItem` verwenden, um zu prüfen, ob die Datei auf das temporäre Laufwerk der Cloud Shell von PowerShell heruntergeladen wurde.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Sie können den Befehl [`az storage file download`](/cli/azure/storage/file) verwenden, um eine Kopie der Datei herunterzuladen, die Sie auf das temporäre Cloud Shell-Laufwerk hochgeladen haben:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie fertig sind, können Sie die Ressourcengruppe und alle in der Ressourcengruppe enthaltenen Ressourcen mit dem Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) löschen. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie fertig sind, können Sie die Ressourcengruppe und alle in der Ressourcengruppe enthaltenen Ressourcen mit dem Befehl [`az group delete`](/cli/azure/group) löschen: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```


---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist Azure Files?](storage-files-introduction.md)