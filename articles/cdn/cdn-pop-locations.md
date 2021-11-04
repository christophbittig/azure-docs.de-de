---
title: Azure CDN-POP-Standorte nach Region | Microsoft-Dokumentation
description: In diesem Artikel werden die Azure CDN-POP-Standorte sortiert nach Region für Azure CDN-Produkte aufgelistet.
services: cdn
documentationcenter: ''
author: sohamnchatterjee
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: c7d1c51bfe56f6fc5b9109f38019f902deff8b27
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461454"
---
# <a name="azure-cdn-coverage-by-metro"></a>Azure CDN-Abdeckung nach Metropolregion 
> [!div class="op_single_selector"]
> * [POP-Standorte nach Region](cdn-pop-locations.md)
> * Die [Verizon POP-Standorte nach Abkürzung](cdn-pop-abbreviations.md)
> * Die [Microsoft POP-Standorte nach Abkürzung](microsoft-pop-abbreviations.md)
>


Dieser Artikel enthält die aktuellen Metropolregionen mit den POP-Standorten (Point of Presence), die nach der Region für Azure CDN-Produkte (Content Delivery Network) sortiert sind. Jede Metropolregion kann mehr als einen POP enthalten. Das Azure CDN von Microsoft verfügt z. B. über 118 POP-Standorte in 100 Metropolregionen. 

> [!IMPORTANT]
> Da jedes Azure CDN-Produkt unterschiedliche Methoden zur Erstellung seiner CDN-Infrastrukturen anwendet, rät Microsoft davon ab, POP-Standorte als Entscheidungskriterium für die Auswahl eines Azure CDN-Produkts heranzuziehen. Stattdessen sollten Sie die jeweiligen Funktionen und Endbenutzerleistung betrachten. Testen Sie die Leistung jedes Azure CDN-Produkts, um das richtige Produkt für Ihre Benutzer auszuwählen. 
> 

## <a name="microsoft"></a>Microsoft

[!INCLUDE [front-door-edge-location](../../includes/front-door-edge-locations.md)]

## <a name="partners"></a>Partner

> [!IMPORTANT]
> POP-Standorte für **Azure CDN von Akamai** werden nicht einzeln offengelegt.  
> 

| Region | Verizon | Akamai |
|--|--|--|
| Nordamerika | Guadalajara, Mexiko<br />Mexiko-Stadt, Mexiko<br />Puebla, Mexiko<br />Querétaro, Mexiko<br />Atlanta, GA, USA<br />Boston, MA, USA<br />Chicago, IL, USA<br />Dallas, TX, USA<br />Denver, CO, USA<br />Detroit, MI, USA<br />Los Angeles, CA, USA<br />Miami, FL, USA<br />New York, NY, USA<br />Philadelphia, PA, USA<br />San Jose, CA, USA<br />Seattle, WA, USA<br />Washington, DC, USA <br /> Ashburn, VA, USA <br /> Phoenix, AZ, USA | Canada<br />Mexiko<br />USA |
| Südamerika | Buenos Aires, Argentinien<br />Rio de Janeiro, Brasilien<br />São Paulo, Brasilien<br />Valparaíso, Chile<br />Bogotá, Kolumbien<br />Barranquilla, Kolumbien<br />Medellin, Kolumbien<br />Quito, Ecuador<br />Lima, Peru | Argentinien<br />Brasilien<br />Chile<br />Kolumbien<br />Ecuador<br />Peru<br />Uruguay |
| Europa | Wien, Österreich<br />Kopenhagen, Dänemark<br />Helsinki, Finnland<br />Marseille, Frankreich<br />Paris, Frankreich<br />Frankfurt, Deutschland<br />Mailand, Italien<br />Riga, Lettland<br />Amsterdam, Niederlande<br />Warschau, Polen<br />Madrid, Spanien<br />Stockholm, Schweden<br />London, Großbritannien <br /> Manchester, Großbritannien | Österreich<br />Bulgarien<br />Dänemark<br />Finnland<br />Frankreich<br />Deutschland<br />Griechenland<br />Irland<br />Italien<br />Niederlande<br />Polen<br />Russland<br />Spanien<br />Schweden<br />Schweiz<br />United Kingdom |
| Afrika | Johannesburg, Südafrika <br/> Nairobi, Kenia | Südafrika |
| Naher Osten | Maskat, Oman<br />Fujirah, Vereinigte Arabische Emirate | Katar<br />Vereinigte Arabische Emirate |
| Indien | Bengaluru (Bangalore), Indien<br />Chennai, Indien<br />Mumbai, Indien<br />Neu-Delhi, Indien<br /> | Indien |
| Asia | Hongkong<br />Jakarta, Indonesien<br />Osaka, Japan<br />Tokio, Japan<br />Singapur<br />Kaohsiung, Taiwan<br />Taipei, Taiwan <br />Manila, Philippinen | Hongkong<br />Indonesien<br />Israel<br />Japan<br />Macau (SAR)<br />Malaysia<br />Philippinen<br />Singapur<br />Südkorea<br />Taiwan<br />Thailand<br />Türkei<br />Vietnam |
| Australien und Neuseeland | Melbourne, Australien<br />Sydney, Australien<br />Auckland, Neuseeland | Australien<br />Neuseeland |

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abrufen der aktuellen IP-Adressen für Positivlisten finden Sie unter der [Azure CDN-Edgeknoten-API](/rest/api/cdn/edge-nodes/list).
