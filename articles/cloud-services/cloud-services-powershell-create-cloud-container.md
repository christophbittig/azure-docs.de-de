---
title: Erstellen eines Clouddienstcontainers (klassisch) mit PowerShell | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Erstellen eines Clouddienstcontainers mit PowerShell. Der Container hostet Web- und Workerrollen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c466182d12662453ab649ef9e7f4039b0acf967c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823579"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Verwenden eines Azure PowerShell-Befehls zum Erstellen eines leeren Clouddienstcontainers (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Dieser Artikel erläutert das schnelle Erstellen eines Cloud Services-Containers mithilfe von Azure PowerShell-Cmdlets. Führen Sie die folgenden Schritte aus:

1. Installieren Sie das Microsoft Azure PowerShell-Cmdlet über die [Azure PowerShell-Downloadseite](https://aka.ms/webpi-azps) .
2. Öffnen Sie die PowerShell-Eingabeaufforderung.
3. Melden Sie sich mithilfe von [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) an.

   > [!NOTE]
   > Weitere Anweisungen zum Installieren des Azure PowerShell-Cmdlets und zum Herstellen einer Verbindung zu Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).
   >
   >
4. Verwenden Sie das **New-AzureService** -Cmdlet zum Erstellen eines leeren Clouddienstcontainers.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Folgen Sie diesem Beispiel, um das Cmdlet aufzurufen:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Führen Sie Folgendes aus, um weitere Informationen zum Erstellen eines Azure-Clouddiensts zu erhalten:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Nächste Schritte

* Informationen zur Verwaltung der Clouddienstbereitstellung erhalten Sie unter den Befehlen [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService) und [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice). Weitere Informationen finden Sie auch unter [Konfigurieren von Clouddiensten](cloud-services-how-to-configure-portal.md) .
* Um Ihr Clouddienstprojekt in Azure zu veröffentlichen, nutzen Sie das Codebeispiel **PublishCloudService.ps1** aus dem [archivierten Cloud Services-Repository](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).