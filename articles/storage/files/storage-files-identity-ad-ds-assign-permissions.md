---
title: 'Steuern des Zugriffs auf Dateifreigaben in Azure: lokale AD DS-Authentifizierung'
description: In diesem Artikel erfahren Sie, wie Sie einer Active Directory Domain Services-Identität, die Ihr Speicherkonto darstellt, Berechtigungen zuweisen. So können Sie den Zugriff über die identitätsbasierte Authentifizierung steuern.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: cf2d9c2921599680781695631eae9c5276ff53c2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400642"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Teil 2: Zuweisen von Berechtigungen auf Freigabeebene für eine Identität

Bevor Sie sich diesen Artikel ansehen, sollten Sie den vorherigen Artikel, [Teil 1: Aktivieren der AD DS-Authentifizierung für Ihre Dateifreigaben in Azure](storage-files-identity-ad-ds-enable.md), bereits gelesen haben.

Sobald Sie die Active Directory Domain Services-Authentifizierung (AD DS) für Ihr Speicherkonto aktiviert haben, müssen Sie Berechtigungen auf Freigabeebene konfigurieren, um Zugriff auf Ihre Dateifreigaben zu erhalten. Es gibt zwei Möglichkeiten, Berechtigungen auf Freigabeebene zu gewähren. Sie können sie bestimmten Azure AD-Benutzern/-Benutzergruppen zuweisen, und Sie können sie allen authentifizierten Identitäten als Standardberechtigung auf Freigabeebene zuweisen.

> [!IMPORTANT]
> Die vollständige administrative Kontrolle über eine Dateifreigabe, einschließlich der Möglichkeit, den Besitz einer Rolle zu übernehmen, erfordert die Verwendung des Speicherkontenschlüssels. Die administrative Kontrolle wird mit Azure AD-Anmeldeinformationen nicht unterstützt.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>Welche Konfiguration sollten Sie verwenden?

Die meisten Benutzer sollten bestimmten Azure AD-Benutzern oder -Gruppen Berechtigungen auf Freigabeebene zuweisen und dann Windows-ACLs für eine präzise Zugriffssteuerung auf Verzeichnis- und Dateiebene verwenden. Dies ist die strengste und sicherste Konfiguration.

Es gibt drei Szenarien, in denen wir stattdessen die Verwendung von Standardberechtigungen auf Freigabeebene empfehlen, die allen authentifizierten Identitäten zugewiesen sind:

- Wenn Sie Ihre lokalen AD DS nicht mit Azure AD synchronisieren können, können Sie alternativ eine Standardberechtigung auf Freigabeebene verwenden. Wenn Sie eine Standardberechtigung auf Freigabeebene zuweisen, können Sie die Synchronisierungsanforderung umgehen, da Sie die Berechtigung nicht Identitäten in Azure AD zuweisen müssen. Dann können Sie mit Windows-ACLs präzise Berechtigungen für Ihre Dateien und Verzeichnisse erzwingen.
- Die lokalen AD DS, die Sie verwenden, werden mit einem anderen Azure AD synchronisiert als dem Azure AD, in dem die Dateifreigabe bereitgestellt wird.
    - Dies ist typisch, wenn Sie Umgebungen mit mehreren Mandanten verwalten. Wenn Sie die Standardberechtigung auf Freigabeebene verwenden, können Sie die Anforderung einer Azure AD-Hybrididentität umgehen. Sie können immer noch mit Windows-ACLs präzise Berechtigungen für Ihre Dateien und Verzeichnisse erzwingen.
- Sie bevorzugen, die Authentifizierung nur mit Windows-ACLS auf Datei- und Verzeichnisebene zu erzwingen. 

## <a name="share-level-permissions"></a>Berechtigungen auf Freigabeebene

In der folgenden Tabelle sind die Berechtigungen auf Freigabeebene und deren Ausrichtung an den integrierten RBAC-Rollen aufgeführt:

|Unterstützte integrierte Rollen  |BESCHREIBUNG  |
|---------|---------|
|[Speicherdateidaten-SMB-Freigabeleser](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |Ermöglicht den Lesezugriff auf Dateien und Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Lesen auf Windows-Dateiservern. [Weitere Informationen](storage-files-identity-auth-active-directory-enable.md)         |
|[Speicherdateidaten-SMB-Freigabemitwirkender](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |Ermöglicht den Lese-, Schreib- und Löschzugriff auf Dateien und Verzeichnisse in Azure-Dateifreigaben. [Weitere Informationen](storage-files-identity-auth-active-directory-enable.md)         |
|[Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |Ermöglicht das Lesen, Schreiben, Löschen und Bearbeiten von ACLs für Dateien und Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Ändern auf Windows-Dateiservern. [Weitere Informationen](storage-files-identity-auth-active-directory-enable.md)         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>Berechtigungen auf Freigabeebene für bestimmte Azure AD-Benutzer oder -Gruppen

Wenn Sie beabsichtigen, einen bestimmten Azure AD-Benutzer oder eine bestimmte Gruppe für den Zugriff auf Azure-Dateifreigaberessourcen zu verwenden, muss diese Identität eine **Hybrididentität sein, die sowohl in lokalen AD DS als auch in Azure AD vorhanden ist**. Beispiel: Ein Benutzer user1@onprem.contoso.com in Ihrem AD ist über die Azure AD Connect-Synchronisierung als user1@contoso.com mit Azure AD synchronisiert. Damit dieser Benutzer auf Azure Files zugreifen kann, müssen Sie user1@contoso.com die Berechtigungen auf Freigabeebene zuweisen. Dasselbe Konzept gilt für Gruppen oder Dienstprinzipale. Aus diesem Grund müssen Sie die Benutzer und Gruppen aus Ihrem AD mithilfe der Azure AD Connect-Synchronisierung mit Azure AD synchronisieren. 

Der Azure AD-Identität, die für denselben Benutzer oder dieselbe Gruppe in Ihrer AD DS-Instanz steht, müssen Berechtigungen auf Freigabeebene zugewiesen werden, um die AD DS-Authentifizierung für Ihre Dateifreigabe in Azure zu unterstützen. Die Authentifizierung und Autorisierung mit Identitäten, die nur in Azure AD vorhanden sind, z. B. verwaltete Identitäten in Azure, wird bei der AD DS-Authentifizierung nicht unterstützt.

Sie können das Azure-Portal, Azure PowerShell-Modul oder die Azure-Befehlszeilenschnittstelle verwenden, um die integrierten Rollen der Azure AD-Identität eines Benutzers zuzuweisen und damit Berechtigungen auf Freigabeebene zu erteilen.

> [!IMPORTANT]
> Es dauert bis zu drei Stunden nach Abschluss, bis die Berechtigungen auf Freigabeebene wirksam werden. Warten Sie, bis die Berechtigungen synchronisiert wurden, bevor Sie mit Ihren Anmeldeinformationen eine Verbindung mit Ihrer Dateifreigabe herstellen.   

# <a name="portal"></a>[Portal](#tab/azure-portal)

Um einer Azure AD-Identität eine Azure-Rolle zuzuweisen, führen Sie im [Azure-Portal](https://portal.azure.com) die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrer Dateifreigabe, oder [erstellen Sie eine Dateifreigabe](storage-how-to-create-file-share.md).
1. Wählen Sie **Access Control (IAM)** aus.
1. Auswählen von **Rollenzuweisung hinzufügen**
1. Wählen Sie auf dem Blatt **Rollenzuweisung hinzufügen** in der Liste **Rolle** die [entsprechende integrierte Rolle](#share-level-permissions) aus.
    1. Leser für Speicherdateidaten-SMB-Freigabe
    1. Mitwirkender für Speicherdateidaten-SMB-Freigabe
    1. Mitwirkender mit erhöhten Rechten für Speicherdateidaten-SMB-Freigabe
1.  Behalten Sie für **Zugriff zuweisen zu** die Standardeinstellung bei: **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal**. Wählen Sie die Azure AD-Zielidentität anhand des Namens oder der E-Mail-Adresse aus. **Bei der ausgewählten Azure AD-Identität muss es sich um eine hybride Identität handeln. Es kann sich um keine nur in der Cloud verfügbare Identität handeln.** Das bedeutet, dieselbe Identität ist auch in AD DS vorhanden.
1. Wählen Sie abschließend **Speichern** aus, um den Vorgang der Rollenzuweisung abzuschließen.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Das folgende PowerShell-Beispiel zeigt, wie einer Azure AD-Identität basierend auf dem Anmeldenamen eine Azure-Rolle zugewiesen wird. Weitere Informationen zum Zuweisen von Azure-Rollen mit PowerShell finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe des Azure PowerShell-Moduls](../../role-based-access-control/role-assignments-powershell.md).

Denken Sie vor dem Ausführen des folgenden Beispielskripts daran, Platzhalterwerte durch eigene Werte zu ersetzen (einschließlich der Klammern).

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
  
Der folgende CLI 2.0-Befehl zeigt, wie einer Azure AD-Identität basierend auf dem Anmeldenamen eine Azure-Rolle zugewiesen wird. Weitere Informationen zum Zuweisen von Azure-Rollen mit der Azure CLI finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Denken Sie vor dem Ausführen des folgenden Beispielskripts daran, Platzhalterwerte durch eigene Werte zu ersetzen (einschließlich der Klammern).

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>Berechtigungen auf Freigabeebene für alle authentifizierten Identitäten

Sie können Ihrem Speicherkonto eine Standardberechtigung auf Freigabeebene hinzufügen, anstatt Berechtigungen auf Freigabeebene für Azure AD-Benutzer oder -Gruppen zu konfigurieren. Eine Standardberechtigung auf Freigabeebene, die Ihrem Speicherkonto zugewiesen ist, gilt für alle Dateifreigaben, die im Speicherkonto enthalten sind. 

Wenn Sie eine Standardberechtigung auf Freigabeebene festlegen, verfügen alle authentifizierten Benutzer und Gruppen über die gleiche Berechtigung. Authentifizierte Benutzer oder Gruppen werden identifiziert, da die Identität für die lokalen AD DS authentifiziert werden kann, denen das Speicherkonto zugeordnet ist. Die Standardberechtigung auf Freigabeebene wird bei der Initialisierung auf **Keine** festgelegt, was bedeutet, dass kein Zugriff auf Dateien und Verzeichnisse in der Azure-Dateifreigabe zulässig ist.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Sie können dem Speicherkonto derzeit keine Berechtigungen mit dem Azure-Portal zuweisen. Verwenden Sie stattdessen das Azure PowerShell-Modul oder die Azure CLI.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Sie können das folgende Skript verwenden, um Standardberechtigungen auf Freigabeebene für Ihr Speicherkonto zu konfigurieren. Sie können die Standardberechtigung auf Freigabeebene nur für Speicherkonten aktivieren, die einem Verzeichnisdienst für die Files-Authentifizierung zugeordnet sind. 

Stellen Sie vor dem Ausführen des folgenden Skripts sicher, dass Sie mindestens über Version 3.7.0 des Az.Storage-Moduls verfügen.

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können das folgende Skript verwenden, um Standardberechtigungen auf Freigabeebene für Ihr Speicherkonto zu konfigurieren. Sie können die Standardberechtigung auf Freigabeebene nur für Speicherkonten aktivieren, die einem Verzeichnisdienst für die Files-Authentifizierung zugeordnet sind. 

Stellen Sie vor dem Ausführen des folgenden Skripts sicher, dass Sie mindestens über Version 2.24.1 der Azure CLI verfügen.

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>Was geschieht, wenn Sie beide Konfigurationen verwenden?

Sie könnten auch allen authentifizierten Azure AD-Benutzern und bestimmten Azure AD-Benutzern/-Gruppen Berechtigungen zuweisen. Bei dieser Konfiguration verfügt ein bestimmter Benutzer oder eine bestimmte Gruppe über die Obermenge von Berechtigungen, die gemäß der Standardberechtigung auf Freigabeebene und der RBAC-Zuweisung zulässig sind. Das folgende Beispiel soll dies veranschaulichen: Angenommen, Sie haben einem Benutzer die Rolle **Speicherdateidaten-SMB-Leser** auf der Zieldateifreigabe erteilt. Außerdem haben Sie allen authentifizierten Benutzern die Standardberechtigung auf Freigabeebene **Mitwirkender mit erhöhten Rechten für Speicherdateidaten-SMB-Freigabe** gewährt. Mit dieser Konfiguration hat dieser bestimmte Benutzer auf der Ebene **Mitwirkender mit erhöhten Rechten für Speicherdateidaten-SMB-Freigabe** Zugriff auf die Dateifreigabe. Berechtigungen auf höherer Ebene haben immer Vorrang.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Berechtigungen auf Freigabeebene zugewiesen haben, müssen Sie Berechtigungen auf Verzeichnis- und Dateiebene konfigurieren. Fahren Sie mit dem nächsten Artikel fort.

[Teil 3: Konfigurieren von Berechtigungen auf Verzeichnis- und Dateiebene über SMB](storage-files-identity-ad-ds-configure-permissions.md)
