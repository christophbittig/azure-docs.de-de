---
title: 'Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mithilfe von Azure PowerShell (Vorschau) – Azure ABAC'
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell und Azure ABAC (Attribute-Based Access Control, attributbasierte Zugriffssteuerung) eine Rollenzuweisungsbedingung hinzufügen, um den Zugriff auf Blobs einzuschränken.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 19aee45977dfde4b401dc4736e24970e311a4cc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621127"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-powershell-preview"></a>Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mithilfe von Azure PowerShell (Vorschau)

> [!IMPORTANT]
> Azure RBAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In den meisten Fällen gewährt eine Rollenzuweisung die für Azure-Ressourcen benötigten Berechtigungen. Manchmal ist jedoch unter Umständen eine präzisere Zugriffssteuerung erforderlich, was durch Hinzufügen einer Rollenzuweisungsbedingung erreicht werden kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Hinzufügen einer Bedingung zu einer Rollenzuweisung
> - Einschränken des Zugriffs auf Blobs basierend auf einem Blobindextag

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen oder Bearbeiten von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Bedingungen](../../role-based-access-control/conditions-prerequisites.md).

## <a name="condition"></a>Bedingung

In diesem Tutorial wird der Zugriff auf Blobs mit einem bestimmten Tag eingeschränkt. Hierzu wird beispielsweise einer Rollenzuweisung eine Bedingung hinzugefügt, die dafür sorgt, dass von Chandra nur Dateien mit dem Tag „Project=Cascade“ gelesen werden können.

![Diagramm: Rollenzuweisung mit einer Bedingung](./media/shared/condition-role-assignment-rg.png)

Wenn Chandra versucht, ein Blob ohne das Tag „Project=Cascade“ zu lesen, wird der Zugriff verweigert.

![Diagramm: Lesezugriff auf Blobs mit dem Tag „Project=Cascade“](./media/shared/condition-access.png)

Im Code sieht die Bedingung wie folgt aus:

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

## <a name="step-1-install-prerequisites"></a>Schritt 1: Installieren der erforderlichen Komponenten

1. Öffnen Sie ein PowerShell-Fenster.

1. Überprüfen Sie mithilfe von [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) die Versionen der installierten Module.

    ```azurepowershell
    Get-InstalledModule -Name Az
    Get-InstalledModule -Name Az.Resources
    Get-InstalledModule -Name Az.Storage
    ```

1. Verwenden Sie bei Bedarf [Install-Module](/powershell/module/powershellget/install-module), um die erforderlichen Versionen für die Module [Az](https://www.powershellgallery.com/packages/Az/), [Az.Resources](https://www.powershellgallery.com/packages/Az.Resources/) und [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) zu installieren.

    ```azurepowershell
    Install-Module -Name Az -RequiredVersion 5.5.0
    Install-Module -Name Az.Resources -RequiredVersion 3.2.1
    Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
    ```

1. Schließen Sie PowerShell, und öffnen Sie PowerShell wieder, um die Sitzung zu aktualisieren.

## <a name="step-2-sign-in-to-azure"></a>Schritt 2: Anmelden bei Azure

1. Verwenden Sie den Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount), und befolgen Sie die angezeigten Anweisungen, um sich als [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../../role-based-access-control/built-in-roles.md#owner) bei Ihrem Verzeichnis anzumelden.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Listen Sie mithilfe von [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) alle Ihre Abonnements auf.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Ermitteln Sie die Abonnement-ID, und initialisieren Sie die Variable.

    ```azurepowershell
    $subscriptionId = "<subscriptionId>"
    ```

1. Legen Sie das Abonnement als aktives Abonnement fest.

    ```azurepowershell
    $context = Get-AzSubscription -SubscriptionId $subscriptionId
    Set-AzContext $context
    ```

## <a name="step-3-create-a-user"></a>Schritt 3: Erstellen eines Benutzers

1. Verwenden Sie [New-AzureADUser](/powershell/module/azuread/new-azureaduser), um einen Benutzer zu erstellen oder nach einem vorhandenen Benutzer zu suchen. In diesem Tutorial wird Chandra als Beispiel verwendet.

1. Initialisieren Sie die Variable für die Objekt-ID des Benutzers.

    ```azurepowershell
    $userObjectId = "<userObjectId>"
    ```

## <a name="step-4-set-up-storage"></a>Schritt 4: Einrichten des Speichers

1. Erstellen Sie mithilfe von [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Speicherkonto, das mit der Blobindexfunktion kompatibel ist. Weitere Informationen finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Erstellen Sie mithilfe von [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) innerhalb des Speicherkontos einen neuen Container, und legen Sie die öffentliche Zugriffsebene auf **Privat (kein anonymer Zugriff)** fest.

1. Verwenden Sie [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent), um eine Textdatei in den Container hochzuladen.

1. Fügen Sie der Textdatei das folgende Blobindextag hinzu. Weitere Informationen finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden.

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Cascade |

1. Laden Sie eine zweite Textdatei in den Container hoch.

1. Fügen Sie der zweiten Textdatei das folgende Blobindextag hinzu:

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Baker |

1. Initialisieren Sie die folgenden Variablen mit den von Ihnen verwendeten Namen:

    ```azurepowershell
    $resourceGroup = "<resourceGroup>"
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameCascade = "<blobNameCascade>"
    $blobNameBaker = "<blobNameBaker>"
    ```

## <a name="step-5-assign-a-role-with-a-condition"></a>Schritt 5: Zuweisen einer Rolle mit einer Bedingung

1. Initialisieren Sie die Rollenvariablen [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

    ```azurepowershell
    $roleDefinitionName = "Storage Blob Data Reader"
    $roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. Initialisieren Sie den Bereich für die Ressourcengruppe.

    ```azurepowershell
    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. Initialisieren Sie die Bedingung.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
    ```

    Wenn Ihre Bedingung ein Dollarzeichen ($) enthält, müssen Sie ihm in PowerShell einen umgekehrten Schrägstrich (\`) voranstellen. In dieser Bedingung werden beispielsweise Dollarzeichen zum Trennen der Tagschlüsselnamen verwendet.

1. Initialisieren Sie die Bedingungsversion und -beschreibung.

    ```azurepowershell
    $conditionVersion = "2.0"
    $description = "Read access to blobs with the tag Project=Cascade"
    ```

1. Verwenden Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um dem Benutzer in einem Ressourcengruppenbereich die Rolle [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) zuzuweisen.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
    ```

    Beispiel für die Ausgabe:

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))
    ```

## <a name="step-6-optional-view-the-condition-in-the-azure-portal"></a>(Optional) Schritt 6: Anzeigen der Bedingung im Azure-Portal

1. Öffnen Sie die Ressourcengruppe im Azure-Portal.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Navigieren Sie auf der Registerkarte „Rollenzuweisungen“ zur Rollenzuweisung.

1. Klicken Sie in der Spalte **Bedingung** auf **Anzeigen/bearbeiten**, um die Bedingung anzuzeigen.

    ![Screenshot: Bedingung „Rollenzuweisung hinzufügen“ im Azure-Portal](./media/shared/condition-view.png)

## <a name="step-7-test-the-condition"></a>Schritt 7: Testen der Bedingung

1. Öffnen Sie ein neues PowerShell-Fenster.

1. Verwenden Sie [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount), um sich als Chandra anzumelden.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Initialisieren Sie die folgenden Variablen mit den von Ihnen verwendeten Namen:

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. Erstellen Sie mithilfe von [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) einen spezifischen Kontext für den einfacheren Zugriff auf Ihr Speicherkonto.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. Versuchen Sie, mithilfe von [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) die Datei für das Projekt „Baker“ zu lesen.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Datei aufgrund der hinzugefügten Bedingung **nicht** lesen.

    ```azurepowershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sat, 24 Apr 2021 13:26:25 GMT
    At line:1 char:1
    + Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Con ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzStorageBlob], StorageException
        + FullyQualifiedErrorId : StorageException,Microsoft.WindowsAzure.Commands.Storage.Blob.Cmdlet.GetAzureStorageBlob
       Command
    ```

1. Lesen Sie die Datei für das Projekt „Cascade“.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Datei lesen, da sie über das Tag „Project=Cascade“ verfügt.

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-8-optional-edit-the-condition"></a>(Optional) Schritt 8: Bearbeiten der Bedingung

1. Verwenden Sie im anderen PowerShell-Fenster [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment), um die hinzugefügte Rollenzuweisung abzurufen.

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. Bearbeiten Sie die Bedingung.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Baker'))"
    ```

1. Initialisieren Sie die Bedingung und Beschreibung.

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access to blobs with the tag Project=Cascade or Project=Baker"
    ```

1. Aktualisieren Sie mithilfe von [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) die Bedingung für die Rollenzuweisung.

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    Beispiel für die Ausgabe:

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade or Project=Baker
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.S
                         torage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
                         StringEquals 'Baker'))
    ```

## <a name="step-9-clean-up-resources"></a>Schritt 9: Bereinigen der Ressourcen

1. Verwenden Sie [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment), um die hinzugefügte Rollenzuweisung und Bedingung zu entfernen.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $userObjectId -RoleDefinitionName $roleDefinitionName -ResourceGroupName $resourceGroup
    ```

1. Löschen Sie das von Ihnen erstellte Speicherkonto.

1. Löschen Sie den von Ihnen erstellten Benutzer.

## <a name="next-steps"></a>Nächste Schritte

- [Exemplarische Azure-Rollenzuweisungsbedingungen (Vorschau)](storage-auth-abac-examples.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Format und Syntax von Azure-Rollenzuweisungsbedingungen](../../role-based-access-control/conditions-format.md)
