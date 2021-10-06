---
title: Azure NetApp Files für Azure Government | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie eine Verbindung zu Azure Government US herstellen, um Azure NetApp Files zu verwenden
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: b-juche
ms.openlocfilehash: b76895fdd82122413c83ebc017944578001ce0b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700211"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure NetApp Files für Azure Government 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) bietet eine dedizierte Cloud, die es Behörden und ihren Partnern ermöglicht, geschäftskritische Workloads in die Cloud zu transformieren.  

Dieser Artikel zeigt Ihnen, wie Sie innerhalb von Azure Government auf den Dienst Azure NetApp Files zugreifen können. 

## <a name="portal"></a>Portal 

Benutzer von Azure Government können auf Azure NetApp Files zugreifen, indem sie ihren Browser auf **portal.azure.us**.  Der Name der Portalsite lautet **Microsoft Azure Government**. Siehe [Verbinden mit Azure Government über das Portal](../azure-government/documentation-government-get-started-connect-with-portal.md)für Einzelheiten.   

![Screenshot von dem Azure Government-Portal mit portal.azure.us als URL](../media/azure-netapp-files/azure-government.jpg)

Über das Microsoft Azure Government-Portal können Sie auf Azure NetApp Files auf die gleiche Weise zugreifen wie im Azure-Portal.Sie können zum Beispiel **Azure NetApp Files** in das Feld Ressourcensuche des Portals eingeben und dann **Azure NetApp Files** aus der angezeigten Liste auswählen.  

Weitere Informationen zur Verwendung des Dienstes finden Sie in der [Azure NetApp Files](/azure/azure-netapp-files/)-Dokumentation.

## <a name="azure-cli"></a>Azure CLI 

Sie können eine Verbindung zu Azure Government herstellen, indem Sie den Cloud-Namen auf `AzureUSGovernment` setzen und dann mit der Anmeldung fortfahren, wie Sie es normalerweise mit dem Befehl `az login` tun würden. Nach der Ausführung des Anmeldebefehls wird ein Browser gestartet, in den Sie die entsprechenden Azure Government-Anmeldedaten eingeben.  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

Führen Sie den Befehl aus, um zu bestätigen, dass die Cloud auf `AzureUSGovernment` gesetzt wurde: 

```azurecli 

az cloud list --output table 

``` 

Mit diesem Befehl wird eine Tabelle mit den Azure-Cloud-Speicherorten erstellt. Der Eintrag in der Spalte `isActive` für `AzureUSGovernment` sollte `true` lauten.  

Weitere Informationen finden Sie unter [Verbinden zu Azure Government mit Azure CLI](../azure-government/documentation-government-get-started-connect-with-cli.md).

## <a name="rest-api"></a>REST-API

Die Endpunkte für Azure Government unterscheiden sich von den kommerziellen Azure-Endpunkten. Eine Liste der verschiedenen Endpunkte finden Sie unter [Leitfaden für Entwickler](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers) von Azure Government.

## <a name="powershell"></a>PowerShell

Wenn Sie sich über PowerShell mit Azure Government verbinden, müssen Sie einen Umgebungsparameter angeben, um sicherzustellen, dass Sie mit den richtigen Endpunkten verbunden sind. Von dort aus können Sie Azure NetApp Files wie gewohnt mit PowerShell verwenden. 

| Verbindungstyp | Befehl | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) Befehle |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread)-Befehle |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| Befehle für [Azure (klassisches Bereitstellungsmodell)](/powershell/module/servicemanagement/azure.service/add-azureaccount) |`Add-AzureAccount -Environment AzureUSGovernment` | 
| Befehle für [Azure Active Directory (klassisches Bereitstellungsmodell)](/previous-versions/azure/jj151815(v=azure.100)) |`Connect-MsolService -AzureEnvironment UsGovernment` | 

Weitere Informationen finden Sie [unter Verbinden zum Azure Government mit PowerShell.](../azure-government/documentation-government-get-started-connect-with-ps.md)

## <a name="next-steps"></a>Nächste Schritte
* [Was ist Azure Government?](../azure-government/documentation-government-welcome.md)
* [Vergleichen von Azure Government und globales Azure](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp Files: REST-API](azure-netapp-files-develop-with-rest-api.md)
* [Azure NetApp Files REST-API mit PowerShell](develop-rest-api-powershell.md)
