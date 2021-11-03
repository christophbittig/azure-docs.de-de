---
title: Informationen zum Azure-Netzwerkfunktionsmanager
description: Hier erhalten Sie mehr Informationen über den Azure-Netzwerkfunktionsmanager, einen vollständig verwalteten cloudnativen Orchestrierungsdienst, mit dem Sie Netzwerkfunktionen für Azure Stack Edge Pro mit GPU bereitstellen können, um eine konsistente Hybriderfahrung mithilfe des Azure-Portals zu ermöglichen.
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55ea5ebe1dbeeb0d3e1d17c2085c19d471396b21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024105"
---
# <a name="what-is-azure-network-function-manager"></a>Was ist der Azure-Netzwerkfunktionsmanager?

Der Azure-Netzwerkfunktionsmanager nutzt [Azure Marketplace](https://azure.microsoft.com/marketplace/) für die Bereitstellung von Netzwerkfunktionen wie mobilen Kernnetzen, SD-WAN-Edge und VPN-Diensten auf Ihrem [Azure Stack Edge-Gerät](https://azure.microsoft.com/products/azure-stack/edge/), das in Ihrer lokalen Umgebung ausgeführt wird. Sie können nun einen privaten mobilen Netzwerkdienst oder eine SD-WAN-Lösung direkt über das Azure-Verwaltungsportal schnell auf Ihrem Edge-Gerät bereitstellen. Der Netzwerkfunktionsmanager bietet verschiedene Netzwerkfunktionen aus einem stetig wachsenden [Partner](#partners)-System. Der Netzwerkfunktionsmanager wird von [Azure Stack Edge Pro mit GPU](../databox-online/azure-stack-edge-gpu-overview.md) unterstützt.

## <a name="features"></a><a name="features"></a> Funktionen

* **Konsistente Verwaltungserfahrung:** Der Netzwerkfunktionsmanager bietet eine konsistente Azure-Verwaltungsumgebung für Netzwerkfunktionen von verschiedenen Partnern, die im Edge-Bereich Ihres Unternehmens bereitgestellt werden. Auf diese Weise können Sie Governance und Verwaltung vereinfachen. Darüber hinaus können Sie Ihre vertrauten Azure-Tools und das SDK verwenden, um die Bereitstellung von Netzwerkfunktionen über deklarative Vorlagen zu automatisieren. Durch die rollenbasierte Zugriffssteuerung von Azure [Azure RBAC](../role-based-access-control/overview.md) ist es möglich, die Netzwerkfunktionen global auf Ihren Azure Stack Edge-Geräten bereitzustellen.

* **Azure Marketplace-Nutzung für 5G-Netzwerkfunktionen**: Beschleunigen Sie die Bereitstellung einer privaten mobilen Netzwerklösung auf Ihrem Azure Stack Edge-Gerät, indem Sie direkt über Azure Marketplace die gewünschten Kernfunktionen der LTE- und 5G-Mobilpakete auswählen.

* **Nahtlose Cloud-zu-Edge-Erfahrung für SD-WAN- und VPN-Lösungen**: Der Netzwerkfunktionsmanager weitet die Azure-Verwaltung für Marketplace-Netzwerkfunktionen, die Sie bereits aus der öffentlichen Cloud kennen, auf Ihr Edge-Gerät aus. Auf diese Weise können Sie die Vorteile einer konsistenten Bereitstellung Ihrer gewählten SD-WAN- und VPN-Partnernetzwerkfunktionen in der öffentlichen Azure-Cloud oder auf einem Azure Stack Edge-Gerät genießen.

* **Azure Managed Applications-Erfahrung für Netzwerkfunktionen im Edge-Bereich Ihres Unternehmens**: Der Netzwerkfunktionsmanager ermöglicht eine vereinfachte Bereitstellung spezialisierter Netzwerkfunktionen wie mobilen Kernnetzen auf Ihrem Azure Stack Edge-Gerät. Wir haben die Lebenszyklusverwaltung für genehmigte Netzwerkfunktionsimages unserer Partner vorab geprüft. Sie können sicher sein, dass Ihre Netzwerkfunktionsressourcen in einem konsistenten Zustand auf sämtlichen Geräten bereitgestellt werden. Weitere Informationen finden Sie unter [Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md).

* **Netzwerkbeschleunigung und dynamische oder statische IP-Zuteilung für Netzwerkfunktionen**: Der Netzwerkfunktionsmanager und [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) unterstützen eine verbesserte Netzwerkleistung der Netzwerkfunktionsworkloads. Spezialisierte Netzwerkfunktionen wie mobile Kernnetze können jetzt auf dem Azure Stack Edge-Gerät mit einer schnelleren Datenpfadleistung im Zugriffspunktnetzwerk und im Netzwerk auf Benutzerebene bereitgestellt werden. Sie können auch zwischen einer statischen und einer dynamischen IP-Zuteilung für die verschiedenen virtuellen Schnittstellen Ihrer Netzwerkfunktionsbereitstellungen wählen. Informieren Sie sich bei Ihrem Netzwerkfunktionspartner über Unterstützungsmöglichkeiten für diese Netzwerkfunktionen.  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>Azure Managed Applications für Netzwerkfunktionen

Die Netzwerkfunktionen, die mit dem Netzwerkfunktionsmanager bereitgestellt werden können, sind speziell für die Ausführung auf Ihrem Azure Stack Edge-Gerät entwickelt. Das Netzwerkfunktionsangebot wird im Azure Marketplace als [Azure Managed Application](../azure-resource-manager/managed-applications/overview.md) veröffentlicht. Kunden können das Angebot direkt über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) oder über die Netzwerkfunktionsmanager-Geräteressource im Azure-Portal bereitstellen. 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="Diagramm des Workflows für verwaltete Anwendungen.":::

Alle Netzwerkfunktionsangebote, die mit dem Netzwerkfunktionsmanager bereitgestellt werden können, verfügen über eine verwaltete Anwendung, die im Azure Marketplace verfügbar ist. Durch die verwalteten Anwendungen können Partner:

* eine benutzerdefinierte Bereitstellungserfahrung für ihre Netzwerkfunktion über das Azure-Portal ermöglichen. 

* eine Resource Manager-Vorlage zur Verfügung stellen, mit der sie die Netzwerkfunktion direkt auf dem Azure Stack Edge-Gerät erstellen können.

* Direkte Abrechnung der Kosten für die Softwarelizenzierung oder Abrechnung über den Azure Marketplace 

* Verfügbarmachen von benutzerdefinierten Eigenschaften und Verbrauchseinheiten für Ressourcen

Netzwerkfunktionspartner können abhängig von ihren Anforderungen bezüglich der Bereitstellung, Konfigurationslizenzierung und Verwaltung des NVA verschiedene Ressourcen erstellen. Wenn ein Kunde eine Netzwerkfunktion auf dem Azure Stack Edge-Gerät erstellt, werden in seinem Abonnement zwei Ressourcengruppen erstellt (dies ist bei allen Azure Managed Applications der Fall):

* **Kundenressourcengruppe**: Diese Ressourcengruppe enthält einen Anwendungsplatzhalter für die verwaltete Anwendung. Partner können damit beliebige benutzerdefinierte Eigenschaften verfügbar machen. 

* **Verwaltete Ressourcengruppe**: Sie können Ressourcen nicht direkt in dieser Ressourcengruppe konfigurieren oder ändern, da sie vom Herausgeber der verwalteten Anwendung gesteuert wird. Diese Ressourcengruppe enthält die **Netzwerkfunktions**-Ressource des Netzwerkfunktionsmanagers.

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="Diagramm der Ressourcengruppen verwalteter Anwendungen":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>Konfigurationsprozess für Netzwerkfunktionen 

Netzwerkfunktionspartner, die ihre verwalteten Azure-Anwendungen mit dem Netzwerkfunktionsmanager anbieten, ermöglichen Ihren Kunden eine Benutzererfahrung, bei der die Netzwerkfunktion automatisch im Rahmen des Bereitstellungsprozesses konfiguriert wird. Nach der erfolgreichen Bereitstellung der verwalteten Anwendung und der Netzwerkfunktionsinstanz in Azure Stack Edge, muss jede zusätzliche Konfiguration, die möglicherweise für die Netzwerkfunktion erforderlich ist, über das Verwaltungsportal der Netzwerkfunktionspartner erfolgen. Informieren Sie sich bei Ihrem Netzwerkfunktionspartner über die End-to-End-Verwaltung der Netzwerkfunktionen, die auf dem Azure Stack Edge-Gerät bereitgestellt werden.

## <a name="region-availability"></a><a name="regions"></a>Regionale Verfügbarkeit

Die Azure Stack Edge-Ressource, das Azure-Netzwerkfunktionsmanager-Gerät und die verwalteten Azure-Anwendungen für Netzwerkfunktionen sollten sich in derselben Azure-Region befinden. Das physische Azure Stack Edge Pro-Gerät muss sich nicht in derselben Region befinden.

* **Ressourcenverfügbarkeit:** Die Netzwerkfunktionsmanager-Ressourcen sind in den folgenden Regionen verfügbar:

   [!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

* **Geräteverfügbarkeit:** Eine Liste aller Länder/Regionen, in denen das Azure Stack Edge Pro GPU-Gerät verfügbar ist, finden Sie auf der Seite [Azure Stack Edge Pro GPU-Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Weitere Informationen finden Sie auf der Registerkarte **Azure Stack Edge Pro** im Abschnitt  **Verfügbarkeit**.

Bei dem aktuellen Release des Netzwerkfunktionsmanagers handelt es sich um einen regionalen Dienst. Bei regionsweiten Ausfällen sind nur die Verwaltungsvorgänge für Netzwerkfunktionsmanager-Ressourcen betroffen, nicht aber die Netzwerkfunktionen, die auf dem Azure Stack Edge-Gerät ausgeführt werden.

## <a name="partner-solutions"></a><a name="partners"></a>Partnerlösungen

Auf der [Partner-Seite](partners.md) des Netzwerkfunktionsmanagers finden Sie eine Übersicht über das stetig wachsende System der Partner, die verwaltete Marketplace-Anwendungen für private mobile Netzwerke sowie SD-WAN- und VPN-Lösungen anbieten.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

Hier finden Sie die [Häufig gestellten Fragen zum Netzwerkfunktionsmanager.](faq.md)

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Geräteressource](create-device.md)
