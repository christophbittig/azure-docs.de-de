---
title: Routing Preference Unmetered in Azure
description: Erfahren Sie, wie Sie die Routingpräferenz für die Ressourcen konfigurieren, die Daten an den CDN-Anbieter senden.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5d459c4a61518aae349184773561448148877afc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368241"
---
# <a name="what-is-routing-preference-unmetered"></a>Was ist Routing Preference Unmetered?

Routing Preference Unmetered ist für CDN-Anbieter (Content Delivery Network) verfügbar, deren Kunden ihre Ursprungsinhalte in Azure hosten. Mit dem Dienst können CDN-Anbieter direkte Peeringverbindungen mit Edgeroutern im globalen Netzwerk von Microsoft an verschiedenen Standorten herstellen.

![Routing Preference Unmetered](./media/routing-preference-unmetered/unmetered.png)

Der Netzwerkdatenverkehr vom Ursprung in Azure an den CDN-Anbieter profitiert von der direkten Verbindung.
* Die Datenübertragung für Datenverkehr von Ihren Azure-Ressourcen, der über diese direkten Verbindungen geleitet wird, ist nicht kostenpflichtig.
* Die direkte Verbindung zwischen dem CDN-Anbieter und dem Ursprung in Azure bietet eine optimale Leistung, da dazwischen keine Hops vorhanden sind. Hiervon profitiert die CDN-Workload, die häufig Daten vom Ursprung abruft.

## <a name="configuring-routing-preference-unmetered"></a>Konfigurieren von Routing Preference Unmetered

Um die Vorteile von Routing Preference Unmetered zu nutzen, müssen die CDN-Anbieter an diesem Programm teilnehmen. Wenden Sie sich an Ihren CDN-Anbieter, wenn dieser kein Teilnehmer des Programms ist.

Konfigurieren Sie dann die Routingpräferenz für Ihre Ressourcen, und legen Sie den Typ der Routingpräferenz auf **Internet** fest. Sie können die Routingpräferenz beim Erstellen einer öffentlichen IP-Adresse konfigurieren und die öffentliche IP-Adresse dann Ressourcen wie virtuellen Computern, Lastenausgleichsmodulen mit Internetzugriff usw. zuordnen. [Hier erfahren Sie, wie Sie mithilfe des Azure-Portals die Routingpräferenz für eine öffentliche IP-Adresse konfigurieren.](../../virtual-network/routing-preference-portal.md)

Sie können auch die Routingpräferenz für Ihr Speicherkonto aktivieren und einen zweiten Endpunkt veröffentlichen, der vom CDN-Anbieter zum Abrufen von Daten aus dem Speicherursprung verwendet werden muss. Wenn Sie z. B. einen speziellen Internetrouten-Endpunkt für das Speicherkonto *StorageAccountA* veröffentlichen, wird der zweite Endpunkt für die Speicherdienste veröffentlicht, wie unten dargestellt:

![Routingpräferenz für Speicherkonten](./media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe von Azure PowerShell](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe der Azure CLI](../../virtual-network/configure-routing-preference-virtual-machine-cli.md)
* [Konfigurieren der Routingpräferenz für ein Speicherkonto](../../storage/common/network-routing-preference.md)