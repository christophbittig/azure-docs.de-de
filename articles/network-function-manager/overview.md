---
title: Informationen zum Azure-Netzwerkfunktionsmanager
description: Hier erhalten Sie mehr Informationen über den Azure-Netzwerkfunktionsmanager, einen vollständig verwalteten cloudnativen Orchestrierungsdienst, mit dem Sie Netzwerkfunktionen für Azure Stack Edge Pro mit GPU bereitstellen können, um eine konsistente Hybriderfahrung mithilfe des Azure-Portals zu ermöglichen.
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 06/16/2021
ms.author: prmitt
ms.custom: references_regions
ms.openlocfilehash: c64dd8ca25ee4b39533022e83e572442cc7529c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263342"
---
# <a name="what-is-azure-network-function-manager-preview"></a>Was ist der Azure-Netzwerkfunktionsmanager? (Vorschau)


Der Azure-Netzwerkfunktionsmanager nutzt [Azure Marketplace](https://azure.microsoft.com/marketplace/) für die Bereitstellung von Netzwerkfunktionen wie mobilen Kernnetzen, SD-WAN-Edge und VPN-Diensten auf Ihrem [Azure Stack Edge-Gerät](https://azure.microsoft.com/products/azure-stack/edge/), das in Ihrer lokalen Umgebung ausgeführt wird. Sie können nun einen privaten mobilen Netzwerkdienst oder eine SD-WAN-Lösung direkt über das Azure-Verwaltungsportal schnell auf Ihrem Edge-Gerät bereitstellen. Der Netzwerkfunktionsmanager bietet verschiedene Netzwerkfunktionen aus einem stetig wachsenden [Partner](#partners)-System. Diese Vorschauversion wird von [Azure Stack Edge Pro mit GPU](../databox-online/azure-stack-edge-gpu-overview.md) unterstützt. 

## <a name="preview-features"></a><a name="preview"></a>Vorschaufeatures

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

## <a name="prerequisites"></a><a name="prereq"></a>Voraussetzungen

### <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Installiertes und aktiviertes Azure Stack Edge Pro mit GPU

Der Azure-Netzwerkfunktionsmanager ist auf dem Azure Stack Edge Pro-Gerät aktiviert. Vergewissern Sie sich vor dem Bereitstellen der Netzwerkfunktionen, dass Azure Stack Edge Pro installiert und aktiviert ist. Die Azure Stack Edge-Ressource muss in einer Region bereitgestellt werden, die von den Netzwerkfunktionsmanager-Ressourcen unterstützt wird. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](#regions). Stellen Sie sicher, dass Sie alle Schritte in den [Schnellstartanleitungen](../databox-online/azure-stack-edge-gpu-quickstart.md) und den [Tutorials](../databox-online/azure-stack-edge-gpu-deploy-checklist.md) für Azure Stack Edge Pro ausführen.

Sie sollten auch überprüfen, ob der **Status** des Geräts im Abschnitt „Eigenschaften“ für die Azure Stack Edge-Ressource im Azure-Verwaltungsportal auf **Online** eingestellt ist.

:::image type="content" source="./media/overview/properties.png" alt-text="Screenshot der Eigenschaften" lightbox="./media/overview/properties.png":::

### <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Voraussetzungen für Partner 

Kunden können einen oder mehrere Netzwerkfunktionsmanager-[Partner](#partners) auswählen, um deren Netzwerkfunktion auf einem Azure Stack Edge-Gerät bereitzustellen. Jeder Partner hat bestimmte Netzwerkanforderungen für die Bereitstellung seiner Netzwerkfunktionen auf einem Azure Stack Edge-Gerät. Die folgenden Konfigurationsaufgaben finden Sie in der Produktdokumentation der Netzwerkfunktionspartner:

* [Konfigurieren des Netzwerks an verschiedenen Ports](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Aktivieren des Computingnetzwerks auf Ihrem Azure Stack Edge-Gerät](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network). 


### <a name="azure-account"></a><a name="account"></a>Azure-Konto

Der Azure-Netzwerkfunktionsmanager besteht aus **Geräte-** und **Netzwerkfunktions**-Ressourcen. Die Geräte- und Netzwerkfunktionsressourcen befinden sich innerhalb von Azure-Abonnements. Für das Aktivieren des Azure Stack Edge Pro-Geräts und der Netzwerkfunktionsmanager-Ressourcen sollte die gleiche Azure-Abonnement-ID verwendet werden. 

Sie können Ihre Azure-Abonnement-ID für die Vorschauversion des Netzwerkfunktionsmanagers anmelden, indem Sie das [Azure Function Manager-Vorschauformular](https://go.microsoft.com/fwlink/?linkid=2163583) ausfüllen. Für die Vorschauversion müssen Partner des Azure-Netzwerkfunktionsmanagers dieselbe Azure-Abonnement-ID aktivieren, um ihre Netzwerkfunktion über Azure Marketplace bereitstellen zu können. Stellen Sie sicher, dass Ihre Azure-Abonnement-ID an beiden Stellen für die Vorschauversion angemeldet ist. 

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Registrierung bei und Berechtigungen von Ressourcenanbietern

Die Netzwerkfunktionsmanager-Ressourcen befinden sich beim Ressourcenanbieter Microsoft.HybridNetwork. Nachdem die Azure-Abonnement-ID für die Vorschau beim Netzwerkfunktionsmanager-Dienst angemeldet wurde, müssen Sie die Abonnement-ID beim Ressourcenanbieter Microsoft.HybridNetwork registrieren. Weitere Informationen zur Registrierung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

Die Konten, die Sie zum Erstellen der Netzwerkfunktionsmanager-Geräteressource verwenden, müssen einer benutzerdefinierten Rolle zugeordnet werden, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen sind. Weitere Informationen finden Sie unter [Benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md).

| Name | Aktion|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Erforderlich für das Lesen der Azure Stack Edge-Ressource, auf der die Netzwerkfunktionen bereitgestellt werden. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Erforderlich für das Lesen der Eigenschaften der Azure Stack Edge-Ressource. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Erforderlich für das Erstellen der Netzwerkfunktionsmanager-Geräteressource auf der Azure Stack Edge-Ressource.|
| Microsoft.HybridNetwork/devices/* | Erforderlich für das Erstellen, Aktualisieren und Löschen der Netzwerkfunktionsmanager-Geräteressource. |

Die Konten, die Sie für das Erstellen der Azure Managed Applications-Ressource verwenden, müssen einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md) zugeordnet werden, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen sind: 

|Name |Action |
|---|---|
|[Rolle „Mitwirkender für verwaltete Anwendungen“](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Erforderlich für das Erstellen von verwalteten App-Ressourcen.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Verwaltete Identität 

Netzwerkfunktionspartner, die ihre verwalteten Azure-Anwendungen mit dem Netzwerkfunktionsmanager anbieten, ermöglichen Ihnen eine optimale Benutzererfahrung, bei der Sie eine verwaltete Anwendung bereitstellen können, die an eine vorhandene Netzwerkfunktionsmanager-Geräteressource angefügt ist. Wenn Sie die vom Partner verwaltete Anwendung im Azure-Portal bereitstellen, müssen Sie eine benutzerseitig zugewiesene verwaltete Identitätsressource bereitstellen, die Zugriff auf die Netzwerkfunktionsmanager-Geräteressource hat. Durch die benutzerseitig zugewiesene verwaltete Identität erhalten der Ressourcenanbieter der verwalteten Anwendung und der Herausgeber der Netzwerkfunktion die notwendigen Berechtigungen für die Netzwerkfunktionsmanager-Geräteressource, die außerhalb der verwalteten Ressourcengruppe bereitgestellt wird. Weitere Informationen finden Sie unter [Verwalten einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

So erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität für die Bereitstellung von Netzwerkfunktionen:

1. Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität und ordnen Sie diese einer benutzerdefinierten Rolle mit Berechtigungen für Microsoft.HybridNetwork/devices/join/action zu. Weitere Informationen finden Sie unter [Verwalten einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Geben Sie diese verwaltete Identität an, wenn Sie die verwaltete Anwendung eines Partners im Azure-Portal erstellen. Weitere Informationen finden Sie unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).


## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Portanforderungen und Firewallregeln

NFM-Dienste (Netzwerkfunktionsmanager), die in Azure Stack Edge ausgeführt werden, erfordern ausgehende Verbindungen mit dem NFM-Clouddienst für Verwaltungsdatenverkehr, um Netzwerkfunktionen bereitzustellen. NFM ist vollständig in den Azure Stack Edge-Dienst integriert. Überprüfen Sie die Netzwerkportanforderungen und Firewallregeln für das [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements)-Gerät.  

Netzwerkfunktionspartner haben unterschiedliche Anforderungen an die Firewall- und Portkonfigurationsregeln, mit denen der Datenverkehr zum Partnerverwaltungsportal verwaltet werden soll. Wenden Sie sich an Ihren Netzwerkfunktionspartner, um Informationen zu bestimmten Anforderungen zu erhalten.  

## <a name="region-availability"></a><a name="regions"></a>Regionale Verfügbarkeit

Die Azure Stack Edge-Ressource, das Azure-Netzwerkfunktionsmanager-Gerät und die verwalteten Azure-Anwendungen für Netzwerkfunktionen sollten sich in derselben Azure-Region befinden. Das physische Azure Stack Edge Pro-Gerät muss sich nicht in derselben Region befinden. 

* **Ressourcenverfügbarkeit:** Für die Vorschauversion sind die Netzwerkfunktionsmanager-Ressourcen in den folgenden Regionen verfügbar:

   [!INCLUDE [Preview- available regions](../../includes/network-function-manager-regions-include.md)]

* **Geräteverfügbarkeit:** Eine Liste aller Länder/Regionen, in denen das Azure Stack Edge Pro GPU-Gerät verfügbar ist, finden Sie auf der Seite [Azure Stack Edge Pro GPU-Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Weitere Informationen finden Sie auf der Registerkarte **Azure Stack Edge Pro** im Abschnitt  **Verfügbarkeit**.

Bei dem aktuellen Release des Netzwerkfunktionsmanagers handelt es sich um einen regionalen Dienst. Bei regionsweiten Ausfällen sind nur die Verwaltungsvorgänge für Netzwerkfunktionsmanager-Ressourcen betroffen, nicht aber die Netzwerkfunktionen, die auf dem Azure Stack Edge-Gerät ausgeführt werden. 

## <a name="partner-solutions"></a><a name="partners"></a>Partnerlösungen

Auf der [Partner-Seite](partners.md) des Netzwerkfunktionsmanagers finden Sie eine Übersicht über das stetig wachsende System der Partner, die verwaltete Marketplace-Anwendungen für private mobile Netzwerke sowie SD-WAN- und VPN-Lösungen anbieten.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

Hier finden Sie die [Häufig gestellten Fragen zum Netzwerkfunktionsmanager.](faq.md)

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Geräteressource](create-device.md)
