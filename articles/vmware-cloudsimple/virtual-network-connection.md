---
title: Verbinden des virtuellen Azure-Netzwerks mit CloudSimple mithilfe von ExpressRoute – Azure VMware Solution by CloudSimple
description: Beschreibt, wie Peeringinformationen für eine Verbindung zwischen dem virtuellen Azure-Netzwerk und Ihrer CloudSimple-Umgebung abgerufen werden.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98f38a7222b569dc4c1a8a9ecef0fd6bd6818ede
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339027"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Verbinden des virtuellen Azure-Netzwerks mit CloudSimple mithilfe von ExpressRoute

Sie können Ihr privates Cloudnetzwerk auf Ihr virtuelles Azure-Netzwerk und Azure-Ressourcen erweitern. Eine ExpressRoute-Verbindung ermöglicht Ihnen den Zugriff auf verschiedene Ressourcen, die in Ihrem Azure-Abonnement über Ihre private Cloud ausgeführt werden.

## <a name="request-authorization-key"></a>Anfordern eines Autorisierungsschlüssels

Ein Autorisierungsschlüssel ist für die ExpressRoute-Verbindung zwischen Ihrer privaten Cloud und dem virtuellen Azure-Netzwerk erforderlich. Um einen Schlüssel zu erhalten, müssen Sie ein <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Supportticket</a> einreichen.  Verwenden Sie die folgenden Informationen für die Anforderung:

* Problemtyp: **Technisch**
* Abonnement: **Wählen Sie das Abonnement aus, in dem der CloudSimple-Dienst bereitgestellt wird.**
* Dienst: **VMware Solution by CloudSimple**
* Problemtyp: **Service Request**
* Problemuntertyp: **Autorisierungsschlüssel für Azure VNET-Verbindung**
* Betreff: **Anforderung eines Autorisierungsschlüssels für Azure VNET-Verbindung**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Abrufen von Informationen zum Peering aus dem CloudSimple-Portal

Zum Einrichten der Verbindung müssen Sie eine Verbindung zwischen dem virtuellen Azure-Netzwerk und ihrer CloudSimple-Umgebung herstellen.  Als Teil dieses Verfahrens müssen Sie den Peerleitungs-URI und den Autorisierungsschlüssel angeben. Rufen Sie den URI und den Autorisierungsschlüssel aus dem [CloudSimple-Portal](access-cloudsimple-portal.md) ab.  Wählen Sie im seitlichen Menü **Network** (Netzwerk) aus, und wählen Sie dann **Azure Network Connection** (Azure-Netzwerkverbindung) aus. Oder wählen Sie im seitlichen Menü **Account** (Konto) aus, und wählen Sie dann **Azure Network Connection** (Azure-Netzwerkverbindung) aus.

Kopieren Sie den Peerleitungs-URI und für den Autorisierungsschlüssel für jede der Regionen mithilfe des *Kopieren*-Symbols. Für jede CloudSimple-Region, die Sie verbinden möchten:

1. Klicken Sie auf **Copy** (Kopieren), um den URI zu kopieren. Fügen Sie ihn in eine Datei ein, aus der er dem Azure-Portal hinzugefügt werden kann.  
2. Klicken Sie auf **Copy** (Kopieren), um den Autorisierungsschlüssel zu kopieren und ebenfalls in die Datei einzufügen.

Kopieren Sie den Autorisierungsschlüssel und den Peerleitungs-URI, der sich im Zustand **Verfügbar** befindet.  Der Status **Verwendet** zeigt an, dass der Schlüssel bereits zum Erstellen einer virtuellen Netzwerkverbindung verwendet wurde.

![Seite „Verbindung mit virtuellem Netzwerk“](media/virtual-network-connection.png)

Weitere Informationen zum Einrichten der Verbindung zwischen dem virtuellen Azure-Netzwerk und CloudSimple finden Sie unter [Herstellen einer Verbindung aus Ihrer privaten CloudSimple-Cloudumgebung mit dem virtuellen Azure-Netzwerk mithilfe von ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Nächste Schritte

* [Virtuelle Azure-Netzwerkverbindung zur Private Cloud](azure-expressroute-connection.md)
* [Connect from on-premises to CloudSimple using ExpressRoute](on-premises-connection.md)
