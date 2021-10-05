---
title: 'Verwenden von PowerShell zum Verwalten von Daten: Azure Data Lake Storage Gen2'
description: Verwenden Sie PowerShell-Cmdlets zum Verwalten von Verzeichnissen und Dateien in Speicherkonten, für die ein hierarchischer Namespace aktiviert ist.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f03426ab734101657ad5609fcd7782f7c75a6f9b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553201"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Verwenden von PowerShell zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell Verzeichnisse und Dateien in Speicherkonten erstellen und verwalten, die über einen hierarchischen Namespace verfügen.

Informationen zum Abrufen, Festlegen und Aktualisieren der Zugriffssteuerungslisten (Access Control Lists, ACLs) von Verzeichnissen und Dateien finden Sie unter [Verwenden von PowerShell zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Beispiel](/powershell/module/Az.Storage/) | [Zuordnung von Gen1 zu Gen2](#gen1-gen2-map) | [Feedback senden](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- .NET Framework 4.7.2 oder höher ist installiert. Weitere Informationen finden Sie unter [Herunterladen von .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- PowerShell Version `5.1` oder höher

## <a name="install-the-powershell-module"></a>Installieren des PowerShell-Moduls

1. Überprüfen Sie mit dem folgenden Befehl, ob PowerShell Version `5.1` oder höher installiert ist.

   ```powershell
   echo $PSVersionTable.PSVersion.ToString()
   ```

   Informationen zum Aktualisieren der PowerShell-Version finden Sie unter [Aktualisieren einer vorhandenen Windows PowerShell-Version](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

2. Installieren Sie das Modul **Az.Storage**.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Weitere Informationen zum Installieren von PowerShell-Modulen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

## <a name="connect-to-the-account"></a>Herstellen einer Verbindung mit dem Konto

Wählen Sie aus, wie Ihre Befehle die Autorisierung für das Speicherkonto abrufen sollen.

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Option 1: Abrufen der Autorisierung mithilfe von Azure Active Directory (Azure AD)

Bei diesem Ansatz stellt das System sicher, dass Ihr Benutzerkonto über die entsprechenden Azure RBAC-Zuweisungen (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung von Azure) und ACL-Berechtigungen verfügt.

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster, melden Sie sich dann mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, das Sie erstellen und in dem Sie Verzeichnisse verwalten möchten. In diesem Beispiel ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

3. Rufen Sie den Speicherkontokontext ab.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Option 2: Abrufen der Autorisierung mithilfe des Speicherkontoschlüssels

Bei diesem Ansatz überprüft das System nicht die Azure RBAC- oder ACL-Berechtigungen. Rufen Sie den Speicherkontokontext mithilfe eines Kontoschlüssels ab.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container fungiert als Dateisystem für Ihre Dateien. Mit dem Cmdlet `New-AzStorageContainer` können Sie einen erstellen.

In diesem Beispiel wird ein Container mit dem Namen `my-file-system` erstellt.

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie mit dem Cmdlet `New-AzDataLakeGen2Item` eine Verzeichnisreferenz.

In diesem Beispiel wird einem Container das Verzeichnis `my-directory` hinzugefügt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In diesem Beispiel wird das gleiche Verzeichnis hinzugefügt, es werden jedoch auch die Berechtigungen, die umask, Eigenschaftswerte und Metadatenwerte festgelegt.

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Anzeigen von Verzeichniseigenschaften

In diesem Beispiel werden mit dem Cmdlet `Get-AzDataLakeGen2Item` ein Verzeichnis abgerufen und dann die Eigenschaftswerte in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Lassen Sie zum Abrufen des Stammverzeichnisses des Containers den Parameter `-Path` weg.

## <a name="rename-or-move-a-directory"></a>Umbenennen oder Verschieben eines Verzeichnisses

Mit dem Cmdlet `Move-AzDataLakeGen2Item` können Sie ein Verzeichnis umbenennen oder verschieben.

In diesem Beispiel wird ein Verzeichnis mit dem Namen `my-directory` in `my-new-directory` umbenannt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Verwenden Sie den Parameter `-Force`, wenn Sie ohne Eingabeaufforderungen überschreiben möchten.

Im folgenden Beispiel wird ein Verzeichnis mit dem Namen `my-directory` in ein Unterverzeichnis von `my-directory-2` mit dem Namen `my-subdirectory` verschoben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Ein Verzeichnis kann mit dem Cmdlet `Remove-AzDataLakeGen2Item` gelöscht werden.

In diesem Beispiel wird das Verzeichnis `my-directory` gelöscht.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Mithilfe des Parameters `-Force` können Sie die Datei ohne Aufforderung entfernen.

## <a name="download-from-a-directory"></a>Herunterladen aus einem Verzeichnis

Laden Sie eine Datei mithilfe des Cmdlets `Get-AzDataLakeGen2ItemContent` aus einem Verzeichnis herunter.

In diesem Beispiel wird eine Datei mit dem Namen `upload.txt` aus einem Verzeichnis mit dem Namen `my-directory` heruntergeladen.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Auflisten des Verzeichnisinhalts

Listen Sie den Inhalt eines Verzeichnisses mithilfe des Cmdlets `Get-AzDataLakeGen2ChildItem` auf. Sie können den optionalen Parameter `-OutputUserPrincipalName` verwenden, um den Namen (anstelle der Objekt-ID) von Benutzern abzurufen.

In diesem Beispiel wird der Inhalt des Verzeichnisses `my-directory` aufgelistet.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Im folgenden Beispiel werden die Eigenschaften `ACL`, `Permissions`, `Group`und `Owner` der einzelnen Elemente im Verzeichnis aufgelistet. Der Parameter `-FetchProperty` ist erforderlich, um Werte für die Eigenschaft `ACL` abzurufen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Um den Inhalt des Stammverzeichnisses des Containers aufzulisten, lassen Sie den Parameter `-Path` weg.

## <a name="upload-a-file-to-a-directory"></a>Hochladen einer Datei in ein Verzeichnis

Laden Sie eine Datei mithilfe des Cmdlets `New-AzDataLakeGen2Item` in ein Verzeichnis hoch.

In diesem Beispiel wird die Datei `upload.txt` in das Verzeichnis `my-directory` hochgeladen.

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force
```

In diesem Beispiel wird die gleiche Datei hochgeladen. Anschließend werden jedoch die Berechtigungen, die umask, die Eigenschaftswerte und die Metadatenwerte der Zieldatei festgelegt. In diesem Beispiel werden diese Werte außerdem in der Konsole ausgegeben.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Lassen Sie zum Hochladen einer Datei in das Stammverzeichnis des Containers den Parameter `-Path` weg.

## <a name="show-file-properties"></a>Anzeigen von Dateieigenschaften

In diesem Beispiel werden mit dem Cmdlet `Get-AzDataLakeGen2Item` eine Datei abgerufen und dann die Eigenschaftswerte in der Konsole ausgegeben.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Löschen von Dateien

Mit dem Cmdlet `Remove-AzDataLakeGen2Item` können Sie eine Datei löschen.

In diesem Beispiel wird die Datei `upload.txt` gelöscht.

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath
```

Mithilfe des Parameters `-Force` können Sie die Datei ohne Aufforderung entfernen.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Zuordnung von Gen1 zu Gen2

In der folgenden Tabelle ist dargestellt, wie die für Data Lake Storage Gen1 verwendeten Cmdlets den Cmdlets für Data Lake Storage Gen2 zugeordnet werden.

|Data Lake Storage Gen1-Cmdlet| Data Lake Storage Gen2-Cmdlet| Notizen |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Standardmäßig listet das Cmdlet „Get-AzDataLakeGen2ChildItem“ nur die untergeordneten Elemente der ersten Ebene auf. Mit dem Parameter „-Recurse“ werden untergeordnete Elemente rekursiv aufgelistet. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Die Ausgabeelemente des Cmdlets „Get-AzDataLakeGen2Item“ verfügen über die folgenden Eigenschaften: ACL, Besitzer, Gruppe, Berechtigung.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Mit dem Cmdlet „Get-AzDataLakeGen2FileContent“ wird der Dateiinhalt in eine lokale Datei heruntergeladen.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Dieses Cmdlet lädt den neuen Dateiinhalt aus einer lokalen Datei hoch.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Mit dem Cmdlet „Update-AzDataLakeGen2Item“ wird nur ein einzelnes Element und nicht rekursiv aktualisiert. Wenn Sie rekursiv aktualisieren möchten, listen Sie Elemente mithilfe des Cmdlets „Get-AzDataLakeStoreChildItem“ auf, und führen Sie dann eine Pipeline zum Cmdlet „Update-AzDataLakeGen2Item“ aus.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Das Cmdlet „Get-AzDataLakeGen2Item“ meldet einen Fehler, wenn das Element nicht vorhanden ist.|

## <a name="see-also"></a>Weitere Informationen

- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Storage PowerShell-Cmdlets](/powershell/module/az.storage)
