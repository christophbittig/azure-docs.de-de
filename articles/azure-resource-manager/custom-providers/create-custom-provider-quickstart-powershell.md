---
title: Erstellen eines benutzerdefinierten Azure-Ressourcenanbieters mit Azure PowerShell
description: Hier erfahren Sie, wie Sie einen benutzerdefinierten Azure-Ressourcenanbieter mit Azure PowerShell erstellen.
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 910a586b15aef0d88409f5756fd340d5cb7c06d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087341"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Schnellstart: Erstellen eines benutzerdefinierten Azure-Ressourcenanbieters mit Azure PowerShell

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit dem PowerShell-Modul [Az.CustomProviders](/powershell/module/az.customproviders) einen eigenen benutzerdefinierten Azure-Ressourcenanbieter erstellen.

> [!CAUTION]
> Benutzerdefinierte Azure-Anbieter sind derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requirements (Anforderungen)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps). Wenn Sie sich für die Verwendung von Cloud Shell entscheiden, finden Sie unter [Übersicht über Azure Cloud Shell](../../cloud-shell/overview.md) weitere Informationen.

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.CustomProviders** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem angegebenen Namen am angegebenen Standort erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Erstellen eines benutzerdefinierten Ressourcenanbieters

Zum Erstellen oder Aktualisieren eines benutzerdefinierten Ressourcenanbieters verwenden Sie das Cmdlet [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider), wie im folgenden Beispiel gezeigt:

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Abrufen des Manifests für benutzerdefinierte Ressourcenanbieter

Zum Abrufen von Informationen zum Manifest für benutzerdefinierte Ressourcenanbieter verwenden Sie das Cmdlet [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider), wie im folgenden Beispiel gezeigt:

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Erstellen einer Zuordnung

Verwenden Sie zum Erstellen oder Aktualisieren einer Zuordnung das Cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation), wie im folgenden Beispiel gezeigt:

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Abrufen einer Zuordnung

Verwenden Sie zum Abrufen von Informationen zu einer Zuordnung das Cmdlet [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation), wie im folgenden Beispiel gezeigt:

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="delete-an-association"></a>Löschen einer Zuordnung

Um eine Zuordnung zu entfernen, verwenden Sie das Cmdlet [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation). Im folgenden Beispiel wird eine Zuordnung gelöscht:

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Löschen eines benutzerdefinierten Ressourcenanbieters

Zum Entfernen eines benutzerdefinierten Ressourcenanbieters verwenden Sie das Cmdlet [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider). Im folgenden Beispiel wird ein benutzerdefinierter Ressourcenanbieter gelöscht:

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [benutzerdefinierte Azure-Ressourcenanbieter](overview.md).
