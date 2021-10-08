---
title: Konfigurieren von NSX-Netzwerkkomponenten mit Azure VMware Solution
description: So konfigurieren Sie NSX-T-Netzwerksegmente mit Azure VMware Solution.
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700383"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>Konfigurieren von NSX-Netzwerkkomponenten mit Azure VMware Solution

Eine Private Cloud von Azure VMware Solution enthält standardmäßig NSX-T. Die Private Cloud wird vorab mit einem NSX-T Tier-0-Gateway im **Aktiv/Aktiv-Modus** und einem standardmäßigen NSX-T Tier-1-Gateway im Aktiv/Standby-Modus bereitgestellt.  Mit diesen Gateways können Sie eine Verbindung der Segmente herstellen (logische Switches) und Ost-West- und Nord-Süd-Konnektivität bereitstellen. 

Nach der Bereitstellung von Azure VMware Solution können Sie die notwendigen NSX-T-Objekte im Azure-Portal konfigurieren.  Darin wird eine vereinfachte Ansicht von NSX-T-Vorgängen angezeigt, die ein VMware-Administrator täglich benötigt, und die für Benutzer bestimmt ist, die mit NSX-T Manager nicht vertraut sind.  

Sie haben vier Optionen zum Konfigurieren von NSX-T-Komponenten in der Azure VMware Solution-Konsole:

- **Segmente**: Erstellen Sie Segmente, die im NSX-T Manager und in vCenter angezeigt werden. Weitere Informationen finden Sie unter [Hinzufügen eines NSX-T-Segments über das Azure-Portal](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment).

- **DHCP**: Erstellen Sie einen DHCP-Server oder ein DHCP-Relay, wenn Sie DHCP verwenden möchten.  Weitere Informationen finden Sie unter [Verwenden des Azure-Portals zum Erstellen eines DHCP-Servers oder Relays](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay).

- **Portspiegelung**: Erstellen Sie eine Portspiegelung, um Netzwerkprobleme zu beheben. Weitere Informationen finden Sie unter [Konfigurieren der Portspiegelung im Azure-Portal](configure-port-mirroring-azure-vmware-solution.md).

- **DNS**: Erstellen Sie eine DNS-Weiterleitung, um DNS-Anforderungen zur Auflösung an einen bestimmten DNS-Server zu senden.  Weitere Informationen finden Sie unter [Konfigurieren einer DNS-Weiterleitung im Azure-Portal](configure-dns-azure-vmware-solution.md).

>[!IMPORTANT]
>Sie haben weiterhin Zugriff auf die NSX-T Manager-Konsole, in der Sie die erwähnten erweiterten Einstellungen und andere NSX-T-Features verwenden können. 

