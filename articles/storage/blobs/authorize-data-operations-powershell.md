---
title: Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blobdaten
titleSuffix: Azure Storage
description: PowerShell unterstützt die Anmeldung mit Azure AD-Anmeldeinformationen zum Ausführen von Befehlen für Blobdaten in Azure Storage. Ein Zugriffstoken wird jeweils für die Sitzung bereitgestellt und zum Autorisieren von Aufrufvorgängen verwendet. Die Berechtigungen hängen von der Azure-Rolle ab, die dem Azure AD-Sicherheitsprinzipal zugewiesen ist.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: dae63f442c2e2df068cc3f17bb5355abd4b7de77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615584"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blobdaten

Azure Storage bietet Erweiterungen für PowerShell, mit deren Hilfe Sie sich mit Azure Active Directory-Anmeldeinformationen (Azure AD) anmelden und Skriptbefehle ausführen können. Wenn Sie sich mit Azure AD-Anmeldeinformationen bei PowerShell anmelden, wird ein OAuth 2.0-Zugriffstoken zurückgegeben. Dieses Token wird dann automatisch von PowerShell verwendet, um nachfolgende Vorgänge für Blob Storage-Daten zu autorisieren. Für unterstützte Vorgänge müssen Sie mit dem Befehl keinen Kontoschlüssel und kein SAS-Token mehr übergeben.

Sie können einem Azure AD-Sicherheitsprinzipal über die rollenbasierte Zugriffssteuerung (Azure RBAC) Berechtigungen für Blobdaten zuweisen. Weitere Informationen zum Zuweisen von Azure-Rollen in Azure Storage finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](assign-azure-role-data-access.md).

## <a name="supported-operations"></a>Unterstützte Vorgänge

Die Azure Storage-Erweiterungen werden für Vorgänge mit Blobdaten unterstützt. Welche Vorgänge Sie aufrufen können, hängt von den Berechtigungen des Azure AD-Sicherheitsprinzipals ab, mit dem Sie sich bei PowerShell anmelden. Die Berechtigungen für Azure Storage-Container werden über Azure RBAC zugewiesen. Wenn Ihnen beispielsweise die Rolle **Blobdatenleser** zugewiesen wurde, können Sie Skriptbefehle ausführen, die Daten aus einem Container lesen. Wurde Ihnen die Rolle **Mitwirkender an Blobdaten** zugewiesen, können Sie Skriptbefehle ausführen, die einen Container bzw. die darin enthaltenen Daten lesen, schreiben oder löschen.

Einzelheiten zu den Berechtigungen, die für die einzelnen Azure Storage-Vorgänge in einem Container erforderlich sind, finden Sie unter [Aufrufen von Speichervorgängen mit OAuth-Token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> Wenn ein Speicherkonto mit **ReadOnly** in Azure Resource Manager gesperrt ist, ist der Vorgang [Schlüssel auflisten](/rest/api/storagerp/storageaccounts/listkeys) für dieses Speicherkonto nicht zulässig. **Schlüssel auflisten** ist ein POST-Vorgang, und alle POST-Vorgänge werden verhindert, wenn die Sperre **ReadOnly** für das Konto festgelegt wurde. Aus diesem Grund müssen Benutzer, die nicht bereits über die Kontoschlüssel verfügen, Azure AD-Anmeldeinformationen für den Zugriff auf Blobdaten verwenden, wenn das Konto mit **ReadOnly** gesperrt ist. Fügen Sie in PowerShell den Parameter `-UseConnectedAccount` ein, um ein Objekt vom Typ **AzureStorageContext** mit Ihren Azure AD-Anmeldeinformationen zu erstellen.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Aufrufen von PowerShell-Befehlen mithilfe von Azure AD-Anmeldeinformationen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie sich über Azure PowerShell anmelden und mithilfe der Azure AD-Anmeldeinformationen nachfolgende Vorgänge in Azure Storage ausführen möchten, erstellen Sie einen Speicherkontext als Verweis für das Speicherkonto, und verwenden Sie dabei den `-UseConnectedAccount`-Parameter.

Im folgenden Beispiel sehen Sie, wie in einem neuen Speicherkonto mithilfe Ihrer Azure AD-Anmeldeinformationen über Azure PowerShell ein Container erstellt wird. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto an:

    ```powershell
    Connect-AzAccount
    ```

    Weitere Informationen zum Anmelden mit Azure PowerShell finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Erstellen Sie eine Azure-Ressourcengruppe, indem Sie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) aufrufen.

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Erstellen Sie ein Speicherkonto, indem Sie [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aufrufen.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Rufen Sie den Speicherkontokontext ab, der das neue Speicherkonto bestimmt, indem Sie [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) aufrufen. Wenn Sie Aktionen für ein Speicherkonto ausführen, können Sie auf den Kontext verweisen, anstatt immer wieder die Anmeldeinformationen zu übergeben. Schließen Sie den `-UseConnectedAccount`-Parameter ein, um mithilfe Ihrer Azure AD-Anmeldeinformationen beliebige anschließende Datenvorgänge aufzurufen:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Weisen Sie sich vor der Erstellung des Containers selbst die Rolle [Mitwirkender an Storage-Blobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) zu. Obwohl Sie der Kontobesitzer sind, benötigen Sie explizite Berechtigungen, um Datenvorgänge in Ihrem Speicherkonto ausführen zu können. Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Zuweisen einer RBAC-Rolle für den Zugriff auf Blobdaten](assign-azure-role-data-access.md).

    > [!IMPORTANT]
    > Die Azure-Rollenzuweisungen können einige Minuten dauern.

1. Erstellen Sie einen Container, indem Sie [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) aufrufen. Da dieser Aufruf den Kontext verwendet, den Sie im vorherigen Schritt erstellt haben, wird der Container mithilfe Ihrer Azure AD-Anmeldeinformationen erstellt.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](assign-azure-role-data-access.md)
- [Autorisieren des Zugriffs auf Blob- und Warteschlangendaten mit verwalteten Identitäten für Azure-Ressourcen](../common/storage-auth-aad-msi.md)
