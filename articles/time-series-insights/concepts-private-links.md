---
title: Private Links - Azure Time Series Insights Gen2 | Microsoft-Dokumenationen
description: Übersicht über private Links und öffentliche Zugriffsbeschränkungen in Azure Time Series Insights Gen2.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207771"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Zugriff auf private Netzwerke mit Azure Private Link (Vorschau) 

[Azure Private Link](/azure/private-link/private-link-overview) ist ein Dienst, der Ihnen den Zugriff auf Azure-Ressourcen (wie [Azure Event Hubs](/azure/event-hubs/event-hubs-about), [Azure Storage](/azure/storage/common/storage-introduction) und [Azure Cosmos DB](/azure/cosmos-db/introduction)) und von Azure gehostete Kunden- und Partnerdienste über einen privaten Endpunkt in Ihrem [Azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) ermöglicht. 

In ähnlicher Weise können Sie private Endpunkte für Ihre Time Series Insights-Instanz verwenden, um Clients, die sich in Ihrem virtuellen Netzwerk befinden, den sicheren Zugriff auf die Instanz über Private Link zu ermöglichen. 

## <a name="about-the-private-endpoints"></a>Über die privaten Endpunkte

Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum Ihrer Azure Virtual Network-Instanz. Der Netzwerkverkehr zwischen einem Client in Ihrem privaten Netzwerk und der Time Series Insights-Instanz wird über das VNet und einen Private Link im Microsoft-Backbone-Netzwerk abgewickelt, so dass keine Verbindung zum öffentlichen Internet besteht. Dies ist eine visuelle Darstellung dieses Systems: 

[![Zeitreihe Private Links DNS](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

Kunden können auch den Zugriff auf die TSI-Umgebung für den öffentlichen Zugang sperren, so dass diese nur über das VNET zugänglich ist. Die Konfiguration eines privaten Endpunkts für Ihre Time Series Insights-Instanz ermöglicht es Ihnen, Ihre Time Series Insights-Instanz zu sichern und die Öffentlichkeit auszuschließen sowie die Exfiltration von Daten aus Ihrem VNet zu verhindern. 

[![Zeitreihe Private Links Netzwerk](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

Sobald ein privater Endpunkt aktiviert und der öffentliche Zugang eingeschränkt ist, muss der Kunde eine andere Adresse für TSI Explorer verwenden, um auf TSI Environment zuzugreifen. Diese Adresse finden Sie im Azure-Portal unter dem Abschnitt Übersicht. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [So konfigurieren Sie private Endpunkte für die TSI-Umgebung](./how-to-private-links.md) 