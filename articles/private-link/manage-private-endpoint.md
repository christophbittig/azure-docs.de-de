---
title: Verwalten einer Verbindung mit einem privaten Endpunkt in Azure
description: Informationen zum Verwalten von Verbindungen mit einem privaten Endpunkt in Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4d5de43786952b3581067c69e2b8840d3aa7296
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553334"
---
# <a name="manage-a-private-endpoint-connection"></a>Verwalten einer Verbindung mit einem privatem Endpunkt
Azure Private Link arbeitet an einem Ablaufmodell für den Genehmigungsaufruf, bei dem der Private Link-Dienstconsumer eine Verbindung mit dem Dienstanbieter anfordern kann, um den Dienst zu nutzen. Der Dienstanbieter kann dann entscheiden, ob er es dem Consumer gestattet, die Verbindung herzustellen. Azure Private Link ermöglicht es den Dienstanbietern, die private Endpunktverbindung über ihre Ressourcen zu verwalten. Dieser Artikel enthält Anweisungen zur Verwaltung der privaten Endpunktverbindungen.

![Verwalten von privaten Endpunkten](media/manage-private-endpoint/manage-private-endpoint.png)

Es gibt zwei Methoden zur Genehmigung von Verbindungen, aus denen ein Private Link-Dienstconsumer auswählen kann:
- **Automatisch**: Wenn der Dienstconsumer über Azure RBAC-Berechtigungen für die Dienstanbieterressource verfügt, kann der Consumer die automatische Genehmigungsmethode auswählen. In diesem Fall ist keine Aktion des Dienstanbieters erforderlich, wenn die Anforderung die Ressource des Dienstanbieters erreicht, und die Verbindung wird automatisch genehmigt. 
- **Manuell**: Im Gegensatz dazu kann der Consumer die manuelle Genehmigungsmethode auswählen, wenn der Dienstconsumer nicht über Azure RBAC-Berechtigungen für die Ressource des Dienstanbieters verfügt. In diesem Fall wird die Verbindungsanforderung auf den Dienstressourcen als **Ausstehend** angezeigt. Der Dienstanbieter muss die Anforderung manuell genehmigen, bevor Verbindungen hergestellt werden können. In manuellen Fällen kann der Dienstconsumer auch eine Nachricht mit der Anforderung angeben, dem Dienstanbieter mehr Kontext bereitzustellen. Der Dienstanbieter hat folgende Optionen für alle privaten Endpunktverbindungen zur Auswahl: **Genehmigt**, **Ablehnen**, **Entfernen**.

Die folgende Tabelle zeigt die verschiedenen Aktionen des Dienstanbieters und die daraus resultierenden Verbindungszustände für private Endpunkte.  Der Dienstanbieter kann auch den Verbindungszustand der privaten Endpunktverbindung zu einem späteren Zeitpunkt ohne Eingriff des Consumers ändern. Durch die Aktion wird der Zustand des Endpunkts auf der Consumerseite aktualisiert. 


|Dienstanbieteraktion   |Privater Endpunktzustand des Dienstconsumers   |BESCHREIBUNG   |
|---------|---------|---------|
|Keine    |    Ausstehend     |    Die Verbindung wurde manuell erstellt, wobei die Genehmigung des Besitzers der Private Link-Ressource aussteht.       |
|Genehmigen    |  Genehmigt       |  Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit.     |
|Reject     | Rejected (Abgelehnt)        | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt.        |
|Remove (Entfernen)    |  Getrennt       | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt, der private Endpunkt wird informativ und sollte zur Bereinigung gelöscht werden.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Verwalten privater Endpunktverbindungen auf Azure PaaS-Ressourcen
Das Portal ist die bevorzugte Methode für die Verwaltung privater Endpunktverbindungen auf Azure PaaS-Ressourcen. 

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Navigieren Sie zum Private Link Center.
3. Wählen Sie unter **Ressourcen** den gewünschten Ressourcentyp zum Verwalten der privaten Endpunktverbindungen aus.
4. Für jeden Ihrer Ressourcentypen können Sie die Anzahl der zugeordneten privaten Endpunktverbindungen anzeigen. Sie können die Ressourcen bei Bedarf filtern.
5. Wählen Sie die privaten Endpunktverbindungen aus.  Wählen Sie unter den aufgelisteten Verbindungen die Verbindung aus, die Sie verwalten möchten. 
6. Sie können den Zustand der Verbindung ändern, indem Sie eine der obigen Optionen auswählen.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Verwalten von privaten Endpunktverbindungen auf einem Private Link-Dienst, der sich im Besitz eines Kunden/Partners befindet

Azure PowerShell und Azure CLI sind die bevorzugten Methoden für die Verwaltung von privaten Endpunktverbindungen für Microsoft Partner Services oder kundeneigene Dienste. 
 
### <a name="powershell"></a>PowerShell 
  
Verwenden Sie die folgenden PowerShell-Befehle, um private Endpunktverbindungen zu verwalten.  
#### <a name="get-private-link-connection-states"></a>Abrufen von Private Link-Verbindungszuständen 
Verwenden Sie das Cmdlet `Get-AzPrivateLinkService`, um die privaten Endpunktverbindungen und deren Zustände abzurufen.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Genehmigen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet `Approve-AzPrivateEndpointConnection`, um eine Verbindung mit einem privaten Endpunkt zu genehmigen. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Ablehnen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet `Deny-AzPrivateEndpointConnection`, um eine Verbindung mit einem privaten Endpunkt abzulehnen. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Entfernen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet `Remove-AzPrivateEndpointConnection`, um eine Verbindung mit einem privaten Endpunkt zu entfernen. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle 
 
Verwenden Sie `az network private-link-service update` zum Verwalten Ihrer privaten Endpunktverbindungen. Der Verbindungszustand ist im Parameter ```azurecli connection-status``` angegeben. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu privaten Endpunkten](private-endpoint-overview.md)
 
