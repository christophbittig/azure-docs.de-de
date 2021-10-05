---
title: Verwenden von PowerShell zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2
description: Verwenden Sie PowerShell-Cmdlets, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace (HNS) aktiviert ist.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d0d7a6c1cb91daa4eb75f5e980355d866346c8e3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555525"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von PowerShell zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell die Zugriffssteuerungslisten von Verzeichnissen und Dateien abrufen, festlegen und aktualisieren.

Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können Zugriffssteuerungslisten aber auch rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

[Referenz](/powershell/module/Az.Storage/) | [Rekursive ACL-Beispiele](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) | [Feedback geben](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Azure CLI, Version `2.6.0` oder höher.

- Eine der folgenden Sicherheitsberechtigungen:

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie die ACL-Einstellungen anwenden möchten. Beim rekursiven Festlegen von ACLs schließt dies alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein.

  - Speicherkontoschlüssel

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Option 1: Abrufen der Autorisierung mithilfe von Azure Active Directory (AD)

> [!NOTE]
> Wenn Sie Azure Active Directory (Azure AD) verwenden, um den Zugriff zu autorisieren, sollten Sie sicherstellen, dass Ihrem Sicherheitsprinzipal die Rolle [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) zugewiesen wurde. Weitere Informationen dazu, wie ACL-Berechtigungen angewandt werden und wie sich Änderungen daran auswirken, finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Abrufen von ACLs

Verwenden Sie das Cmdlet `Get-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei abzurufen.

In diesem Beispiel wird die Zugriffssteuerungsliste des Stammverzeichnisses eines **Containers** abgerufen und dann an der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste eines **Verzeichnisses** abgerufen und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste einer **Datei** abgerufen und dann in der Konsole ausgegeben.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Die folgende Abbildung zeigt die Ausgabe nach dem Abrufen der Zugriffssteuerungsliste eines Verzeichnisses.

![Ausgabe der Zugriffssteuerungsliste für Verzeichnis abrufen](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In diesem Beispiel verfügt der zuständige Benutzer über Berechtigungen zum Lesen, Schreiben und Ausführen. Die zuständige Gruppe verfügt lediglich über Berechtigungen zum Lesen und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Festlegen von ACLs

Wenn Sie eine Zugriffssteuerungsliste *festlegen*, **ersetzen** Sie die gesamte Zugriffssteuerungsliste einschließlich aller Einträge. Wenn Sie die Berechtigungsebene eines Sicherheitsprinzipals ändern oder der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen möchten, ohne dass sich dies auf bereits vorhandene Einträge auswirkt, sollten Sie die Zugriffssteuerungsliste stattdessen *aktualisieren*. Informationen dazu, wie eine Zugriffssteuerungsliste aktualisiert statt ersetzt wird, finden Sie im Abschnitt [Aktualisieren von ACLs](#update-acls) in diesem Artikel.

Wenn Sie die Zugriffssteuerungsliste *festlegen*, müssen Sie einen Eintrag für den besitzenden Benutzer, einen Eintrag für die besitzende Gruppe und einen Eintrag für alle anderen Benutzer hinzufügen. Weitere Informationen zum besitzenden Benutzer, zur besitzenden Gruppe und zu allen anderen Benutzern finden Sie unter [Benutzer und Identitäten](data-lake-storage-access-control.md#users-and-identities).

In diesem Abschnitt erfahren Sie Folgendes:

- Festlegen einer Zugriffssteuerungsliste
- Rekursives Festlegen von ACLs

### <a name="set-an-acl"></a>Festlegen einer Zugriffssteuerungsliste

Verwenden Sie das Cmdlet `set-AzDataLakeGen2ItemAclObject`, um eine Zugriffssteuerungsliste für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer zu erstellen. Verwenden Sie dann das Cmdlet `Update-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste zu committen.

In diesem Beispiel wird die Zugriffssteuerungsliste für das Stammverzeichnis eines **Containers** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann an der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In diesem Beispiel wird die Zugriffssteuerungsliste auf ein **Verzeichnis** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

In diesem Beispiel wird die Zugriffssteuerungsliste auf eine **Datei** für den zuständigen Benutzer, die zuständige Gruppe oder andere Benutzer festgelegt und dann in der Konsole ausgegeben.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Verwenden Sie die entsprechenden Objekt-IDs, um die Zugriffssteuerungsliste einer spezifischen Gruppe oder eines spezifischen Benutzers festzulegen. Zum Beispiel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` oder `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Die folgende Abbildung zeigt die Ausgabe nach dem Festlegen der Zugriffssteuerungsliste einer Datei.

![Ausgabe der Zugriffssteuerungsliste für Datei abrufen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In diesem Beispiel verfügen der zuständige Benutzer und die zuständige Gruppe nur über Berechtigungen zum Lesen und Schreiben. Alle anderen Benutzer verfügen über Berechtigungen zum Schreiben und Ausführen. Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Rekursives Festlegen von ACLs

Zum rekursiven Festlegen von Zugriffssteuerungslisten verwenden Sie das Cmdlet **Set-AzDataLakeGen2AclRecursive**.

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Mit diesen Einträgen werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

## <a name="update-acls"></a>Aktualisieren von ACLs

Wenn Sie eine Zugriffssteuerungsliste *aktualisieren*, ändern Sie die Zugriffssteuerungsliste, anstatt sie zu ersetzen. Beispielsweise können Sie der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen, ohne dass sich dies auf andere Sicherheitsprinzipale auswirkt, die in der Zugriffssteuerungsliste aufgelistet sind. Informationen dazu, wie eine Zugriffssteuerungsliste ersetzt statt aktualisiert wird, finden Sie im Abschnitt [Festlegen von Zugriffssteuerungslisten](#set-acls) in diesem Artikel.

Um eine Zugriffssteuerungsliste zu aktualisieren, erstellen Sie ein neues Zugriffssteuerungsliste-Objekt mit dem Zugriffssteuerungslisteneintrag, den Sie aktualisieren möchten, und verwenden Sie dieses Objekt dann im Aktualisierungsvorgang der Zugriffssteuerungsliste. Rufen Sie nicht die vorhandene Zugriffssteuerungsliste ab, stellen Sie lediglich die zu aktualisierenden Zugriffssteuerungslisteneinträge bereit.

In diesem Abschnitt erfahren Sie Folgendes:

- Aktualisieren einer Zugriffssteuerungsliste (ACL)
- Rekursives Aktualisieren von Zugriffssteuerungslisten

### <a name="update-an-acl"></a>Aktualisieren einer Zugriffssteuerungsliste (ACL)

Rufen Sie zuerst die Zugriffssteuerungsliste ab. Verwenden Sie dann das Cmdlet `set-AzDataLakeGen2ItemAclObject`, um einen ACL-Eintrag hinzuzufügen oder zu aktualisieren. Verwenden Sie das Cmdlet `Update-AzDataLakeGen2Item`, um die Zugriffssteuerungsliste zu committen.

Dieses Beispiel erstellt oder aktualisiert die Zugriffssteuerungsliste in einem **Verzeichnis** für einen Benutzer.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Rekursives Aktualisieren von Zugriffssteuerungslisten

Sie aktualisieren Zugriffssteuerungslisten rekursiv mithilfe des Cmdlets **Update-AzDataLakeGen2AclRecursive**.

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Verwenden Sie die entsprechenden Objekt-IDs, um die Zugriffssteuerungsliste einer spezifischen Gruppe oder eines spezifischen Benutzers festzulegen. Zum Beispiel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` oder `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches aktualisiert, finden Sie im Referenzartikel zu [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive).

## <a name="remove-acl-entries"></a>Entfernen von ACL-Einträgen

In diesem Abschnitt erfahren Sie Folgendes:

- Entfernen eines ACL-Eintrags
- Rekursives Entfernen von ACL-Einträgen

### <a name="remove-an-acl-entry"></a>Entfernen eines ACL-Eintrags

Dieses Beispiel entfernt einen Eintrag aus einer vorhandenen Zugriffssteuerungsliste.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Rekursives Entfernen von ACL-Einträgen

Sie können einen oder mehrere ACL-Einträge rekursiv entfernen. Um einen ACL-Eintrag zu entfernen, erstellen Sie ein neues ACL-Objekt für den zu entfernenden ACL-Eintrag, und verwenden Sie dieses Objekt dann im Entfernungsvorgang der ACL. Rufen Sie nicht die vorhandene ACL ab, stellen Sie lediglich die zu entfernenden ACL-Einträge bereit.

Sie entfernen ACL-Einträge mithilfe des Cmdlets **Remove-AzDataLakeGen2AclRecursive**.

In diesem Beispiel wird ein ACL-Eintrag aus dem Stammverzeichnis des Containers entfernt.

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---"

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches entfernt, finden Sie im Referenzartikel zu [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive).

## <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern

Beim rekursiven Ändern von Zugriffssteuerungslisten können Laufzeit- oder Berechtigungsfehler auftreten.

Starten Sie den Prozess bei Laufzeitfehlern vom Anfang neu. Berechtigungsfehler können auftreten, wenn der Sicherheitsprinzipal nicht über ausreichende Berechtigungen verfügt, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei zu ändern, die sich in der geänderten Verzeichnishierarchie befindet. Beheben Sie das Berechtigungsproblem, und setzen Sie dann den Prozess ab dem Zeitpunkt des Fehlers mithilfe eines Fortsetzungstokens fort, oder starten Sie den Prozess vom Anfang neu. Sie müssen das Fortsetzungstoken nicht verwenden, wenn Sie vom Anfang neu starten. Sie können ACL-Einträge ohne negative Auswirkung erneut anwenden.

Dieses Beispiel gibt Ergebnisse an die Variable zurück und leitet fehlerhafte Einträge dann an eine formatierte Tabelle weiter.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft
```

Basierend auf der Ausgabe der Tabelle können Sie alle Berechtigungsfehler beheben und die Ausführung dann mit dem Fortsetzungstoken fortsetzen.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

Wenn Sie möchten, dass der Prozess ohne Unterbrechung durch Berechtigungsfehler durchgeführt wird, können Sie dies angeben.

Dieses Beispiel verwendet den Parameter `ContinueOnFailure`, sodass die Ausführung auch bei einem Berechtigungsfehler im Vorgang fortgesetzt wird.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft)
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Weitere Informationen

- [Bekannte Probleme](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Storage PowerShell-Cmdlets](/powershell/module/az.storage)
- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
