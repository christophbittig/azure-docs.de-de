---
title: Verwalten einer Verbindung mit einem privaten Endpunkt in Azure
titleSuffix: Azure Private Link
description: Informationen zum Verwalten von Verbindungen mit einem privaten Endpunkt in Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535953"
---
# <a name="manage-a-private-endpoint-connection"></a>Verwalten einer Verbindung mit einem privatem Endpunkt

Azure Private Link arbeitet an einem Genehmigungsmodell, bei dem der Private Link-Dienstconsumer eine Verbindung mit dem Dienstanbieter anfordern kann, um den Dienst zu nutzen. 

Der Dienstanbieter kann dann entscheiden, ob er es dem Consumer gestattet, die Verbindung herzustellen. Azure Private Link bietet Dienstanbietern die Möglichkeit, die private Endpunktverbindung über ihre Ressourcen zu verwalten. 

Dieser Artikel enthält Anweisungen zur Verwaltung der privaten Endpunktverbindungen.

![Verwalten von privaten Endpunkten](media/manage-private-endpoint/manage-private-endpoint.png)

Es gibt zwei Methoden zur Genehmigung von Verbindungen, aus denen ein Private Link-Dienstconsumer auswählen kann:

- **Automatisch**: Wenn der Dienstconsumer für die Dienstanbieterressource über Berechtigungen der rollenbasierten Zugriffssteuerung in Azure verfügt, kann der Consumer die automatische Genehmigungsmethode auswählen. Wenn die Anforderung die Ressource des Dienstanbieters erreicht, ist keine Aktion des Dienstanbieters erforderlich, und die Verbindung wird automatisch genehmigt. 

- **Manuell**: Wenn der Dienstconsumer für die Ressource des Dienstanbieters nicht über Berechtigungen der rollenbasierten Zugriffssteuerung in Azure verfügt, kann der Consumer die manuelle Genehmigungsmethode auswählen. Die Verbindungsanforderung wird für die Dienstressourcen als **Ausstehend** angezeigt. Der Dienstanbieter muss die Anforderung manuell genehmigen, bevor Verbindungen hergestellt werden können. In manuellen Fällen kann der Dienstconsumer auch eine Nachricht mit der Anforderung angeben, dem Dienstanbieter mehr Kontext bereitzustellen. Der Dienstanbieter hat folgende Optionen für alle privaten Endpunktverbindungen zur Auswahl: **Genehmigt**, **Ablehnen**, **Entfernen**.

Die folgende Tabelle zeigt die verschiedenen Aktionen des Dienstanbieters und die daraus resultierenden Verbindungszustände für private Endpunkte. Der Dienstanbieter kann den Verbindungsstatus zu einem späteren Zeitpunkt ändern, ohne dass der Consumer eingreifen muss. Durch die Aktion wird der Zustand des Endpunkts auf der Consumerseite aktualisiert. 


| Dienstanbieteraktion  | Privater Endpunktzustand des Dienstconsumers | BESCHREIBUNG |
|---------|---------|---------|
| Keine    |    Ausstehend     |    Die Verbindung wurde manuell erstellt, wobei die Genehmigung des Besitzers der Private Link-Ressource aussteht.       |
| Genehmigen    |  Genehmigt       |  Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit.     |
| Reject     | Rejected (Abgelehnt)        | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt.        |
| Remove (Entfernen)    |  Getrennt       | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt. Der private Endpunkt dient nur der Information und sollte zur Bereinigung gelöscht werden.        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Verwalten privater Endpunktverbindungen für Azure PaaS-Ressourcen

Das Azure-Portal ist die bevorzugte Methode für die Verwaltung privater Endpunktverbindungen für Azure PaaS-Ressourcen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im oberen Bereich des Portals den Suchbegriff **Private Link** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Private Link** aus.

3. Wählen Sie in **Private Link Center** die Option **Private Endpunkte** oder **Private Link-Dienste** aus.

4. Für jeden Ihrer Endpunkte können Sie die Anzahl der zugeordneten privaten Endpunktverbindungen anzeigen. Sie können die Ressourcen bei Bedarf filtern.

5. Wählen Sie den privaten Endpunkt aus.  Wählen Sie unter den aufgelisteten Verbindungen die Verbindung aus, die Sie verwalten möchten. 

6. Sie können den Zustand der Verbindung ändern, indem Sie eine der obigen Optionen auswählen.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Verwalten von privaten Endpunktverbindungen auf einem Private Link-Dienst, der sich im Besitz eines Kunden/Partners befindet

Azure PowerShell und Azure CLI sind die bevorzugten Methoden für die Verwaltung von privaten Endpunktverbindungen für Microsoft Partner Services oder kundeneigene Dienste. 
 
### <a name="powershell"></a>PowerShell 
  
Verwenden Sie die folgenden PowerShell-Befehle, um private Endpunktverbindungen zu verwalten.  

#### <a name="get-private-link-connection-states"></a>Abrufen von Private Link-Verbindungszuständen 

Verwenden Sie das Cmdlet [Get-AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection), um die privaten Endpunktverbindungen und deren Zustände abzurufen.  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Genehmigen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection), um eine private Endpunktverbindung zu genehmigen. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Ablehnen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet [Deny-AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection), um eine private Endpunktverbindung abzulehnen. 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>Entfernen einer privaten Endpunktverbindung 
 
Verwenden Sie das Cmdlet [Remove-AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection), um eine private Endpunktverbindung zu entfernen. 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>Abrufen von Private Link-Verbindungszuständen 

Verwenden Sie [az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show), um die privaten Endpunktverbindungen und deren Zustände abzurufen.  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Genehmigen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet [az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve), um eine private Endpunktverbindung zu genehmigen. 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>Ablehnen einer Verbindung mit einem privaten Endpunkt 
 
Verwenden Sie das Cmdlet [az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject), um eine private Endpunktverbindung abzulehnen. 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>Entfernen einer privaten Endpunktverbindung 
 
Verwenden Sie das Cmdlet [az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete), um eine private Endpunktverbindung zu entfernen. 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu privaten Endpunkten](private-endpoint-overview.md)
 
