---
title: Private Endpunktverbindungen (Übersicht)
description: Dieser Artikel enthält eine Übersicht über private Endpunktverbindungen mit Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 346630425cd6a77c7ebf18b6f16a1bbd1bcab705
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095113"
---
# <a name="private-endpoint-connections-overview"></a>Private Endpunktverbindungen (Übersicht)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel enthält eine Übersicht über private Endpunktverbindungen mit Media Services.

## <a name="clients-using-vnet"></a>Clients, die VNet verwenden

Clients in einem VNET, die den privaten Endpunkt verwenden, sollten den gleichen DNS-Namen verwenden, um eine Verbindung mit Media Services herzustellen, wie Clients, die eine Verbindung mit den öffentlichen Media Services-Endpunkten herstellen. Das automatische Routing der Verbindungen vom VNet zu den Media Services-Endpunkten über eine private Verbindung basiert auf der DNS-Auflösung.

> [!IMPORTANT]
> Verwenden Sie die gleichen DNS-Namen für die Media Services-Endpunkte, wenn Sie private Endpunkte verwenden. Stellen Sie keine Verbindung mit den Media Services-Endpunkten her, indem Sie die URL der Private Link-Unterdomäne verwenden.

Media Services erstellt standardmäßig eine [private DNS-Zone](/dns/private-dns-overview) mit den erforderlichen Updates für die privaten Endpunkte, die an das VNET angehängt ist. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise zusätzliche Änderungen an Ihrer DNS-Konfiguration vornehmen. Im folgenden Abschnitt zu DNS-Änderungen werden die für private Endpunkte erforderlichen Updates beschrieben.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der **DNS-CNAME**-Ressourceneintrag für jeden Media Services-Endpunkt auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig wird außerdem eine private DNS-Zone mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte erstellt, die der Unterdomäne `privatelink` entspricht.

Wenn Sie einen Media Services-DNS-Namen von außerhalb des VNet mit dem privaten Endpunkt auflösen, wird er an den öffentlichen Endpunkt des Media Services-Endpunkts aufgelöst. Die Media Services-URL wird zur IP-Adresse des privaten Endpunkts aufgelöst, wenn sie von dem VNet, das den privaten Endpunkt hostet, aufgelöst wird.

Die DNS-Ressourceneinträge für einen Streamingendpunkt im Media Services `MediaAccountA` sind, wenn sie von außerhalb des VNet aufgelöst werden, das den privaten Endpunkt hostet, zum Beispiel folgende:

| Name | type | Wert |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | Ein | `<Streaming Endpoint public IP address>` |

Sie können den öffentlichen Internetzugriff auf Media Services-Endpunkte verweigern oder einschränken, indem Sie Liste zugelassener IP-Adressen verwenden oder den öffentlichen Netzwerkzugriff für alle Ressourcen innerhalb des Kontos deaktivieren.

Die DNS-Ressourceneinträge des Beispielstreamingendpunkts für „MediaAccountA“ lauten nach dem Auflösen durch einen Client im VNet, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | A | `<Streaming Endpoint public endpoint>`, zum Beispiel" 10.0.0.9 |

Dieser Ansatz ermöglicht den Zugriff auf den Media Services-Endpunkt mit dem gleichen DNS-Namen für Clients innerhalb des VNet, das die privaten Endpunkte hostet. Dies gilt auch für Clients außerhalb des VNet.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients den FQDN für den Media Services-Endpunkt zur IP-Adresse des privaten Endpunkts auflösen. Konfigurieren Sie den DNS-Server so, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNet delegiert wird, oder konfigurieren Sie die A-Einträge für  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` mit der IP-Adresse des privaten Endpunkts.

> [!TIP]
> Wenn Sie einen benutzerdefinierten oder lokalen DNS-Server verwenden, sollten Sie den DNS-Server so konfigurieren, dass der Media Services-Endpunktname in der Unterdomäne „privatelink“ zur IP-Adresse des privaten Endpunkts aufgelöst wird. Hierzu können Sie die Unterdomäne „privatelink“ an die private DNS-Zone des VNet delegieren oder die DNS-Zone auf dem DNS-Server konfigurieren und die DNS-A-Einträge hinzufügen.

Die empfohlenen DNS-Zonennamen für private Endpunkte für Speicherdienste und die verknüpften untergeordneten Zielressourcen der Endpunkte lauten wie folgt:

| Media Services-Endpunkt | Private Link-Gruppen-ID | DNS-Zonenname |
| ----------------------- | --------------------- | ------------- |
| Streamingendpunkt | streamingendpoint | privatelink.media.azure.net |
| Schlüsselübermittlung | streamingendpoint | privatelink.media.azure.net |
| Liveereignis | liveevent | privatelink.media.azure.net |

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:

- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>Öffentliche Netzwerkzugriffsflags

Das `publicNetworkAccess`-Flag auf dem Media Services-Konto kann verwendet werden, um den Zugriff auf Media Services-Endpunkte aus dem öffentlichen Internet zuzulassen oder zu blockieren. Wenn `publicNetworkAccess` deaktiviert ist, werden Anforderungen an jegliche Media Services-Endpunkte aus dem öffentlichen Internet blockiert. Anforderungen an private Endpunkte sind weiterhin zulässig.  

## <a name="service-level-ip-allowlists"></a>Listen zugelassener IP-Adressen auf Dienstebene

Wenn `publicNetworkAccess` aktiviert ist, sind Anforderungen aus dem öffentlichen Internet abhängig von Listen zugelassener IP-Adressen auf Dienstebene zulässig. Wenn `publicNetworkAccess` deaktiviert ist, werden Anforderungen aus dem öffentlichen Internet unabhängig von den Einstellungen für die Liste zugelassener IP-Adressen blockiert. Listen zugelassener IP-Adressen gelten nur für Anforderungen aus dem öffentlichen Internet. Anforderungen an private Endpunkte werden nicht nach den Listen zugelassener IP-Adressen gefiltert.
