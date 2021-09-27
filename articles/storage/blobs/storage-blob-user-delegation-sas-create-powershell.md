---
title: Verwenden von PowerShell zum Erstellen einer SAS für die Benutzerdelegierung für einen Container oder ein Blob
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie mithilfe von PowerShell eine SAS für die Benutzerdelegierung mit Azure Active Directory-Anmeldeinformationen erstellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22ababc0bca34423a6205d52f29f18ad691f919e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662620"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Erstellen einer SAS für die Benutzerdelegierung für einen Container oder ein Blob mit PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Active Directory-Anmeldeinformationen (Azure AD) verwenden, um eine SAS für die Benutzerdelegierung für einen Container oder ein Blob mit Azure PowerShell zu erstellen.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Installieren des PowerShell-Moduls

Zum Erstellen einer SAS für die Benutzerdelegierung mithilfe von PowerShell installieren Sie Version 1.10.0 oder höher des Az.Storage-Moduls. Führen Sie die folgenden Schritte aus, um die neueste Version des Moduls zu installieren:

1. Deinstallieren Sie alle älteren Installationen von Azure PowerShell:

    - Entfernen Sie alle früheren Installationen von Azure PowerShell mit der Einstellung **Apps & Features** (unter **Einstellungen**) aus Windows.
    - Entfernen Sie alle **Azure**-Module aus `%Program Files%\WindowsPowerShell\Modules`.

1. Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie den folgenden Befehl aus, um die neueste Version zu installieren:

    ```powershell
    Install-Module PowerShellGet -Repository PSGallery -Force
    ```

1. Schließen Sie nach dem Installieren von PowerShellGet das PowerShell-Fenster, und öffnen Sie es dann erneut.

1. Installieren Sie die neueste Version von Azure PowerShell:

    ```powershell
    Install-Module Az -Repository PSGallery -AllowClobber
    ```

1. Vergewissern Sie sich, dass mindestens Version 3.2.0 von Azure PowerShell installiert ist. Führen Sie den folgenden Befehl aus, um die neueste Version des Azure Storage-PowerShell-Moduls zu installieren:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Schließen Sie das PowerShell-Fenster, und öffnen Sie es dann erneut.

Um zu überprüfen, welche Version des Az.Storage-Moduls installiert ist, führen Sie den folgenden Befehl aus:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Weitere Informationen zum Installieren von Azure PowerShell finden Sie unter [Installieren von Azure PowerShell mit PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Anmelden bei Azure PowerShell mit Azure AD

Geben Sie den Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) aus, um sich mit Ihrem Azure AD-Konto anzumelden:

```powershell
Connect-AzAccount
```

Weitere Informationen zum Anmelden mit PowerShell finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Zuweisen von Berechtigungen mit Azure RBAC

Um eine SAS für die Benutzerdelegierung aus Azure PowerShell zu erstellen, muss dem Azure AD-Konto, mit dem die Anmeldung bei PowerShell erfolgt, eine Rolle zugewiesen werden, die die Aktion **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** beinhaltet. Diese Berechtigung ermöglicht es diesem Azure AD Konto, den *Benutzerdelegierungsschlüssel* anzufordern. Der Benutzerdelegierungsschlüssel wird zum Signieren der SAS für die Benutzerdelegierung verwendet. Die Rolle, die die Aktion **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bereitstellt, muss auf der Ebene des Speicherkontos, der Ressourcengruppe oder des Abonnements zugewiesen werden. Weitere Informationen zu Azure RBAC-Berechtigungen zum Erstellen einer SAS für die Benutzerdelegierung finden Sie im Abschnitt **Zuweisen von Berechtigungen mit Azure RBAC** unter [Erstellen einer SAS für die Benutzerdelegierung](/rest/api/storageservices/create-user-delegation-sas).

Wenn Sie nicht über ausreichende Berechtigungen zum Zuweisen von Azure-Rollen zu einem Azure AD-Sicherheitsprinzipal verfügen, müssen Sie möglicherweise den Kontobesitzer oder den Administrator bitten, die erforderlichen Berechtigungen zuzuweisen.

Im folgenden Beispiel wird die Rolle **Storage Blob Data Contributor** (Speicherblob-Datenmitwirkender) zugewiesen, die die Aktion **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** beinhaltet. Die Rolle wird auf der Ebene des Speicherkontos festgelegt.

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Weitere Informationen zu den integrierten Rollen, die die Aktion **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** enthalten, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Verwenden von Azure AD-Anmeldeinformationen zum Sichern einer SAS

Wenn Sie eine SAS für die Benutzerdelegierung mit Azure PowerShell erstellen, wird der Benutzerdelegierungsschlüssel, der zum Signieren der SAS verwendet wird, implizit für Sie erstellt. Die Startzeit und die Ablaufzeit, die Sie für die SAS angeben, werden auch als Startzeit und Ablaufzeit für den Benutzerdelegierungsschlüssel verwendet.

Da das maximale Intervall, in dem der Benutzerdelegierungsschlüssel gültig ist, 7 Tage ab dem Startdatum beträgt, sollten Sie eine Ablaufzeit für die SAS angeben, die innerhalb von 7 Tagen der Startzeit liegt. Die SAS ist ungültig, nachdem der Benutzerdelegierungsschlüssel abgelaufen ist, sodass eine SAS mit einer Ablaufzeit von mehr als 7 Tagen trotzdem nur 7 Tage gültig ist.

Um eine SAS für die Benutzerdelegierung für einen Container oder ein Blob mit Azure PowerShell zu erstellen, erstellen Sie zunächst ein neues Azure Storage-Kontextobjekt und geben dabei den Parameter `-UseConnectedAccount` an. Der `-UseConnectedAccount`-Parameter gibt an, dass der Befehl das Kontextobjekt unter dem Azure AD-Konto erstellt, mit dem Sie sich angemeldet haben.

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Erstellen einer SAS für die Benutzerdelegierung für einen Container

Um ein SAS-Token für die Benutzerdelegierung für einen Container zurückzugeben, geben Sie den Befehl [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) aus, und übergeben Sie dabei das Azure Storage-Kontextobjekt, das Sie zuvor erstellt haben.

Im folgenden Beispiel wird eine SAS für die Benutzerdelegierung für einen Container zurückgegeben. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Das zurückgegebene SAS-Token für die Benutzerdelegierung ähnelt dem folgenden Token:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Erstellen einer SAS für die Benutzerdelegierung für ein Blob

Um ein SAS-Token für die Benutzerdelegierung für ein Blob zurückzugeben, geben Sie den Befehl [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) aus, und übergeben Sie dabei das Azure Storage-Kontextobjekt, das Sie zuvor erstellt haben.

Die folgende Syntax gibt eine SAS für die Benutzerdelegierung für ein Blob zurück. Im Beispiel wird der `-FullUri`-Parameter angegeben, der den Blob-URI mit angefügtem SAS-Token zurückgibt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Der zurückgegebene SAS-URI für die Benutzerdelegierung ähnelt dem folgenden URI:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Eine SAS für die Benutzerdelegierung unterstützt nicht das Definieren von Berechtigungen mit einer gespeicherten Zugriffsrichtlinie.

## <a name="revoke-a-user-delegation-sas"></a>Widerrufen einer SAS für die Benutzerdelegierung

Um eine SAS für die Benutzerdelegierung aus Azure PowerShell zu widerrufen, rufen Sie den Befehl **Revoke-AzStorageAccountUserDelegationKeys** auf. Mit diesem Befehl werden alle Benutzerdelegierungsschlüssel widerrufen, die dem angegebenen Speicherkonto zugeordnet sind. Alle mit diesen Schlüsseln verknüpften Shared Access Signatures werden für ungültig erklärt.

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Sowohl der Benutzerdelegierungsschlüssel als auch die Azure-Rollenzuweisungen werden von Azure Storage zwischengespeichert. Daher kann es zu einer Verzögerung zwischen der Initiierung des Sperrprozesses und dem Zeitpunkt kommen, zu dem eine SAS für die Benutzerdelegierung ungültig wird.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer SAS für die Benutzerdelegierung (REST-API)](/rest/api/storageservices/create-user-delegation-sas)
- [Vorgang zum Abrufen eines Benutzerdelegierungsschlüssels](/rest/api/storageservices/get-user-delegation-key)
