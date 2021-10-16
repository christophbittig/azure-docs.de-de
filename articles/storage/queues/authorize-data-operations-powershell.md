---
title: Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Warteschlangendaten
titleSuffix: Azure Storage
description: PowerShell unterstützt die Anmeldung mit Azure AD-Anmeldeinformationen zum Anwenden von Befehlen auf Azure Queue Storage-Daten. Ein Zugriffstoken wird jeweils für die Sitzung bereitgestellt und zum Autorisieren von Aufrufvorgängen verwendet. Die Berechtigungen hängen von der Azure-Rolle ab, die dem Azure AD-Sicherheitsprinzipal zugewiesen ist.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: bcf8d828bb20296a27b9288a2ba604bce1b7aef9
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855439"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Warteschlangendaten

Azure Storage bietet Erweiterungen für PowerShell, mit deren Hilfe Sie sich mit Azure Active Directory-Anmeldeinformationen (Azure AD) anmelden und Skriptbefehle ausführen können. Wenn Sie sich mit Azure AD-Anmeldeinformationen bei PowerShell anmelden, wird ein OAuth 2.0-Zugriffstoken zurückgegeben. Dieses Token wird dann automatisch von PowerShell verwendet, um nachfolgende Datenvorgänge für Queue Storage zu autorisieren. Für unterstützte Vorgänge müssen Sie mit dem Befehl keinen Kontoschlüssel und kein SAS-Token mehr übergeben.

Sie können einem Azure AD-Sicherheitsprinzipal über die rollenbasierte Zugriffssteuerung (Azure RBAC) Berechtigungen für Warteschlangendaten zuweisen. Weitere Informationen zu Azure-Rollen in Azure Storage finden Sie unter [Verwalten der Zugriffsrechte für Azure Storage-Daten mit Azure RBAC](assign-azure-role-data-access.md).

## <a name="supported-operations"></a>Unterstützte Vorgänge

Die Azure Storage-Erweiterungen werden für Vorgänge für Warteschlangendaten unterstützt. Welche Vorgänge Sie aufrufen können, hängt von den Berechtigungen des Azure AD-Sicherheitsprinzipals ab, mit dem Sie sich bei PowerShell anmelden. Berechtigungen für Warteschlangen werden über Azure RBAC zugewiesen. Wenn Ihnen beispielsweise die Rolle **Storage-Warteschlangendatenleser** zugewiesen wurde, können Sie Skriptbefehle ausführen, die Daten aus einer Warteschlange lesen. Wurde Ihnen die Rolle **Mitwirkender an Storage-Warteschlangendaten** zugewiesen, können Sie Skriptbefehle ausführen, die eine Warteschlange bzw. die darin enthaltenen Daten lesen, schreiben oder löschen.

Einzelheiten zu den Berechtigungen, die für die einzelnen Azure Storage-Vorgänge für Warteschlangen erforderlich sind, finden Sie unter [Aufrufen von Speichervorgängen mit OAuth-Token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> Wenn ein Speicherkonto mit **ReadOnly** in Azure Resource Manager gesperrt ist, ist der Vorgang [ Schlüssel auflisten](/rest/api/storagerp/storageaccounts/listkeys) für dieses Speicherkonto nicht zulässig. **Schlüssel auflisten** ist ein POST-Vorgang, und alle POST-Vorgänge werden verhindert, wenn die Sperre **ReadOnly** für das Konto festgelegt wurde. Aus diesem Grund müssen Benutzer, die nicht bereits über die Kontoschlüssel verfügen, Azure AD-Anmeldeinformationen für den Zugriff auf Warteschlangendaten verwenden, wenn das Konto mit **ReadOnly** gesperrt ist. Fügen Sie in PowerShell den Parameter `-UseConnectedAccount` ein, um ein **AzureStorageContext**-Objekt mit Ihren Azure AD-Anmeldeinformationen zu erstellen.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Aufrufen von PowerShell-Befehlen mithilfe von Azure AD-Anmeldeinformationen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie sich über Azure PowerShell anmelden und mithilfe der Azure AD-Anmeldeinformationen nachfolgende Vorgänge in Azure Storage ausführen möchten, erstellen Sie einen Speicherkontext als Verweis für das Speicherkonto, und verwenden Sie dabei den `-UseConnectedAccount`-Parameter.

Im folgenden Beispiel sehen Sie, wie Sie in einem neuen Speicherkonto mithilfe Ihrer Azure AD-Anmeldeinformationen über Azure PowerShell eine Warteschlange erstellen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

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

1. Weisen Sie sich vor der Erstellung der Warteschlange selbst die Rolle [Mitwirkender an Storage-Warteschlangendaten](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) zu. Obwohl Sie der Kontobesitzer sind, benötigen Sie explizite Berechtigungen, um Datenvorgänge in Ihrem Speicherkonto ausführen zu können. Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](assign-azure-role-data-access.md).

    > [!IMPORTANT]
    > Die Azure-Rollenzuweisungen können einige Minuten dauern.

1. Erstellen Sie eine Warteschlange, indem Sie [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) aufrufen. Da dieser Aufruf den Kontext verwendet, den Sie in den vorherigen Schritten erstellt haben, wird die Warteschlange mithilfe Ihrer Azure AD-Anmeldeinformationen erstellt.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](assign-azure-role-data-access.md)
- [Autorisieren des Zugriffs auf Blobdaten mit verwalteten Identitäten für Azure-Ressourcen](../blobs/authorize-managed-identity.md)
