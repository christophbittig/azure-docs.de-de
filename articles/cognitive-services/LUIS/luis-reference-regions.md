---
title: Veröffentlichungsregionen und Endpunkte – LUIS
description: Die im Azure-Portal angegebene Region ist dieselbe, in der Sie die LUIS-App veröffentlichen werden, und es wird eine Endpunkt-URL für diese Region generiert.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 7dc91bf74cf342d882a978c6e7b3a8e39d401644
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576239"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Erstellungs- und Veröffentlichungsregionen und die dazugehörigen Schlüssel

LUIS-Erstellungsregionen werden vom LUIS-Portal unterstützt. Zum Veröffentlichen einer LUIS-App in mehreren Regionen benötigen Sie mindestens einen Schlüssel pro Region.


<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-Erstellungsregionen

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS verfügt über die folgenden Erstellungsregionen:
    
* Australien, Osten
* Europa, Westen
* USA (Westen)
* Schweiz, Norden


LUIS verfügt über ein Portal, das Sie unabhängig von der Region verwenden können: [www.luis.ai](https://www.luis.ai). Sie müssen die Erstellung und Veröffentlichung aber noch immer in derselben Region durchführen. Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Veröffentlichungsregionen und Azure-Ressourcen

Die App wird in allen Regionen veröffentlicht, die den im LUIS-Portal hinzugefügten LUIS-Ressourcen zugeordnet sind. Wenn Sie z. B. für eine auf [www.luis.ai][www.luis.ai] erstellte App eine LUIS- oder Cognitive Service-Ressource in **westus** erstellen und [sie der App als Ressource hinzufügen](luis-how-to-azure-subscription.md), wird die App in dieser Region veröffentlicht.

## <a name="public-apps"></a>Öffentliche Apps
Eine öffentliche App wird in allen Regionen veröffentlicht, so dass ein Benutzer mit einem regionsbasierten LUIS-Ressourcenschlüssel auf die App in jeder Region zugreifen kann, die dem Ressourcenschlüssel zugeordnet ist.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Veröffentlichungsregionen sind an Erstellungsregionen gebunden.

Die App der Erstellungsregion kann nur in einer entsprechenden Veröffentlichungsregion veröffentlicht werden. Wenn sich die App derzeit in der falschen Erstellungsregion befindet, exportieren Sie die App und importieren sie in die richtige Erstellungsregion für Ihre Veröffentlichungsregion.

> [!NOTE]
> LUIS-Apps, die in https://www.luis.ai erstellt wurden, können jetzt auf allen Endpunkten einschließlich in den Regionen in [Europa](#publishing-to-europe) und [Australien](#publishing-to-australia) veröffentlicht werden.

## <a name="publishing-to-europe"></a>Veröffentlichen in Europa

 Globale Region | Erstellung der API-Region | Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| Europa | `westeurope`| Frankreich, Mitte<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Nordeuropa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa, Westen<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| UK, Süden<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Schweiz, Norden<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Norwegen, Osten<br>`norwayeast`    |  `https://norwayeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Veröffentlichen in Australien

 Globale Region | Erstellung der API-Region | Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| Australien | `australiaeast` | Australien (Osten)<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Sonstige Veröffentlichungsregionen

 Globale Region | Erstellung der API-Region | Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Südafrika, Norden<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, Mitte<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Osten<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Osten<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Westen<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Korea, Mitte<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Südosten<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| VAE, Norden<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada, Mitte<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA (Mitte)<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Ost) 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Nord Mitte<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Süd Mitte<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen-Mitte<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Westen)<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen 3<br>`westus3`    |  `https://westus3.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Südamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brasilien Süd<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Endpunkte

Erfahren Sie mehr über die [Erstellen- und Vorhersageendpunkte](developer-reference-resource.md).

## <a name="failover-regions"></a>Failoverregionen

Jede Region verfügt über eine sekundäre Region für Failover. Europa führt Failover innerhalb Europas aus, Australien innerhalb von Australien.

Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vordefinierter Entitätsverweis](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
