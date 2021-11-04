---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 633121c21bf09c608c0c40d6b87bf68edfbaf1f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478863"
---
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Bereinigen von Ressourcen über das Azure-Portal

Löschen Sie die Ressourcen im Azure-Portal, indem Sie die Schritte zum [Bereinigen von Ressourcen](../ingest-data/data-explorer-ingest-event-hub-portal.md#clean-up-resources) ausführen.

### <a name="clean-up-resources-using-powershell"></a>Bereinigen von Ressourcen über PowerShell

Wenn die Cloud Shell noch geöffnet ist, muss die erste Zeile (Read-Host) nicht kopiert/ausgeführt werden.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
