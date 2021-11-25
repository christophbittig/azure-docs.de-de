---
title: Pfadauswahl mit dem Azure-Routenserver
description: Erfahren Sie, wie der Azure-Routenserver die Pfadauswahl für Ihr virtuelles Netzwerkgerät ermöglicht.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: duau
ms.openlocfilehash: 011156427bfaed238679d4fc4b4e6d51e566ec6e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350375"
---
# <a name="path-selection-with-azure-route-server"></a>Pfadauswahl mit dem Azure-Routenserver

Mit dem Azure-Routenserver können Sie den Datenverkehr steuern, der über Ihr SDWAN-NVA und über das Internet an Ihr lokales Netzwerk geroutet werden soll. In diesem Abschnitt wird beschrieben, wie der Azure-Routenserver die Pfadauswahl ermöglicht, mit der Sie Ihr SNWAN-NVA so konfigurieren können, dass es bei der Kommunikation mit Ihrem lokalen Netzwerk eine [Routingpräferenz](../virtual-network/ip-services/routing-preference-overview.md) hat.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

:::image type="content" source="./media/path-selection/routing-preference.png" alt-text="Diagramm des Azure-Routenservers und eines SDWAN mit Internetrouting-IP.":::

### <a name="cold-potato-routing"></a>Cold-Potato-Routing

Wenn Sie ein SDWAN-NVA im gleichen virtuellen Netzwerk des Azure-Routenservers bereitstellen, wird es mit einer Microsoft-Netzwerk-IP-Adresse konfiguriert. Der Datenverkehrspfad zu Ihrer lokalen Umgebung verwendet das globale Microsoft-Netzwerk und verlässt das Microsoft-Netzwerk, das dem Ziel am nächsten ist. Das Routing von Ihrem lokalen Standort aus wird in das Microsoft-Netzwerk gelangen, das dem Benutzer im Rückgabepfad am nächsten ist. Diese Routingmethode dient der Leistungsoptimierung und bietet daher die bestmögliche Benutzererfahrung zu einem Preis. 

### <a name="hot-potato-routing"></a>Hot-Potato-Routing

Um Kosten optimieren zu können, wird eine zweite Routingmethode durch Zuweisen Ihres SDWAN-NVA mit einer Internet-IP-Adresse eingeführt. Wenn Datenverkehr an Ihre lokale Umgebung geroutet wird, wird das Microsoft-Netzwerk in derselben Region verlassen, in der der Dienst gehostet wird. Anschließend wird er über das Internet mithilfe des Netzwerks vom Internetdienstanbieter geroutet. Das Lokale Routing (routing from on-premises) wird in das Microsoft-Netzwerk in der Nähe der gehosteten Dienstregion gelangen. Diese Routingmethode bietet den besten Gesamtpreis, wenn eine Aufgabe wie das Übertragen großer Datenmengen abgeschlossen werden soll.

## <a name="how-to-enable-routing-preference"></a>Aktivieren der Routingpräferenz

Wählen Sie das **Internet** als die *Routingpräferenz* aus, wenn Sie eine neue öffentliche IP-Adresse erstellen. Weisen Sie dann die öffentliche IP-Adresse dem SDWAN-NVA zu.

:::image type="content" source="./media/path-selection/internet-ip.png" alt-text="Screenshot einer Internet-Routingpräferenz für eine öffentliche IP-Adresse":::

Microsoft empfiehlt die Implementierung einer Konnektivitätslösung mit dem Microsoft-Netzwerk und dem Internet, um Ihrer Umgebung eine zusätzliche Resilienzebene zu bieten.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Route Server](route-server-faq.md).
- Erfahren Sie, wie Sie [Azure Route Server konfigurieren](quickstart-configure-route-server-powershell.md).
