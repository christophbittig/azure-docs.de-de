---
title: Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke
description: Schritte zum Verbinden von ExpressRoute mit dem Gateway für virtuelle Netzwerke
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: a3e759c12496613848f6f0bc8de596a5099b904b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438219"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. Fordern Sie einen ExpressRoute-Autorisierungsschlüssel an:

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. Navigieren Sie zu dem VNET-Gateway, das Sie verwenden möchten, und wählen Sie **Verbindungen** >  **+ Hinzufügen** aus.

1. Geben Sie auf der Seite **Verbindung hinzufügen** Werte für die Felder an, und wählen Sie **OK** aus. 

   | Feld | Wert |
   | --- | --- |
   | **Name**  | Geben Sie einen Namen für die Verbindung ein.  |
   | **Verbindungstyp**  | Wählen Sie **ExpressRoute** aus.  |
   | **Autorisierung einlösen**  | Stellen Sie sicher, dass dieses Kontrollkästchen aktiviert ist.  |
   | **Gateway für virtuelle Netzwerke** | Das VNET-Gateway, das Sie verwenden möchten  |
   | **Autorisierungsschlüssel**  | Fügen Sie den zuvor kopierten Autorisierungsschlüssel ein. |
   | **Peerleitungs-URI**  | Fügen Sie die zuvor kopierte ExpressRoute-ID ein.  |

   :::image type="content" source="../media/tutorial-configure-networking/add-connection.png" alt-text="Ein Screenshot, der die Seite „Hinzufügen einer Verbindung“ zum Verbinden von ExpressRoute mit dem virtuellen Netzwerkgateway zeigt":::.

Die Verbindung zwischen Ihrer ExpressRoute-Leitung und Ihrem virtuellen Netzwerk wird erstellt.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Ein Screenshot, der eine erfolgreiche Verbindung mit dem virtuellen Netzwerkgateway zeigt.":::