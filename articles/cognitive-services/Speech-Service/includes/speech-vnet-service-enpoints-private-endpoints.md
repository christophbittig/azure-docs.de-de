---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453981"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>Private Endpunkte und VNet-Dienstendpunkte

Azure bietet private Endpunkte und VNet-Dienstendpunkte für das Tunneln von Datenverkehr mithilfe des [privaten Azure-Backbone-Netzwerks](https://azure.microsoft.com/global-infrastructure/global-network/). Der Zweck und die zugrunde liegenden Technologien dieser Endpunkttypen sind ähnlich. Es gibt jedoch Unterschiede zwischen den beiden Technologien. Es wird empfohlen, dass Sie sich vor dem Entwurf Ihres Netzwerks über die Vor- und Nachteile beider Technologien informieren.

Bei der Entscheidung, welche Technologie verwendet werden soll, müssen einige Punkte berücksichtigt werden:
- Beide Technologien stellen sicher, dass der Datenverkehr zwischen dem virtuellen Netzwerk und der Speech-Ressource nicht über das öffentliche Internet übertragen wird.
- Ein privater Endpunkt stellt eine dedizierte private IP-Adresse für Ihre Speech-Ressource bereit. Auf diese IP-Adresse kann nur innerhalb eines bestimmten VNet und Subnetzes zugegriffen werden. Sie haben die volle Kontrolle über den Zugriff auf diese IP-Adresse innerhalb Ihrer Netzwerkinfrastruktur.
- VNet-Dienstendpunkte stellen keine dedizierte private IP-Adresse für die Speech-Ressource zur Verfügung. Stattdessen kapseln sie alle an die Speech-Ressource gesendeten Pakete und stellen sie direkt über das Azure-Backbonenetzwerk zu.
- Beide Technologien unterstützen lokale Szenarien. Bei Verwendung von VNet-Dienstendpunkten sind über virtuelle Netzwerke geschützte Azure-Dienstressourcen standardmäßig nicht von lokalen Netzwerken aus erreichbar. Sie können dieses [Verhalten jedoch ändern](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises).
- VNet-Dienstpunkte werden häufig dazu verwendet, den Zugriff auf Ihre Speech-Ressource basierend auf den virtuellen Netzwerken einzuschränken, aus denen der Datenverkehr stammt.
- Bei Cognitive Services erzwingt die Aktivierung des VNet-Dienstendpunkts, dass der Datenverkehr für alle Cognitive Services-Ressourcen über das private Backbonenetzwerk geleitet wird. Dies erfordert eine explizite Konfiguration des Netzwerkzugriffs. (Weitere Informationen finden Sie unter [Konfigurieren virtueller Netzwerke und der Netzwerkeinstellungen für Speech-Ressourcen](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings).) Private Endpunkte unterliegen nicht dieser Einschränkung und bieten mehr Flexibilität für Ihre Netzwerkkonfiguration. Sie können auf eine Ressource über das private Backbone und auf eine andere Ressource über das öffentliche Internet zugreifen, indem Sie dasselbe Subnetz desselben virtuellen Netzwerks verwenden.
- Für private Endpunkte entstehen [zusätzliche Kosten](https://azure.microsoft.com/pricing/details/private-link). VNet-Dienstendpunkte sind kostenlos.
- Private Endpunkte erfordern eine [zusätzliche DNS-Konfiguration](../speech-services-private-link.md#turn-on-private-endpoints).
- Eine Speech-Ressource kann gleichzeitig mit privaten Endpunkten und VNet-Dienstendpunkten genutzt werden.

Es wird empfohlen, beide Endpunkttypen zu testen, bevor Sie eine Entscheidung für Ihren Produktionsentwurf treffen. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Dokumentation zu Azure Private Link und privaten Endpunkten](../../../private-link/private-link-overview.md)
- [Dokumentation zu VNet-Dienstendpunkten](../../../virtual-network/virtual-network-service-endpoints-overview.md)
