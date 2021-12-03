---
title: Was ist Azure Virtual Network Manager (Vorschau)?
description: Erfahren Sie, wie Azure Virtual Network Manager die Verwaltung und Skalierbarkeit Ihrer virtuellen Netzwerke vereinfachen kann.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 81603472d4a5a254fe86f2a6866d8a7edcbbef88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459632"
---
# <a name="what-is-azure-virtual-network-manager-preview"></a>Was ist Azure Virtual Network Manager (Vorschau)?

Azure Virtual Network Manager ist ein Verwaltungsdienst, mit dem Sie virtuelle Netzwerke global und abonnementübergreifend gruppieren, konfigurieren, bereitstellen und verwalten können. Mit Virtual Network Manager können Sie Netzwerkgruppen definieren, um Ihre virtuellen Netzwerke zu identifizieren und logisch zu segmentieren. Anschließend können Sie die gewünschten Konnektivitäts- und Sicherheitskonfigurationen ermitteln und auf alle ausgewählten virtuellen Netzwerke in Netzwerkgruppen gleichzeitig anwenden. 

> [!IMPORTANT]
> Der Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-azure-virtual-network-manager-work"></a>Wie funktioniert Azure Virtual Network Manager?

:::image type="content" source="./media/overview/management-group.png" alt-text="Abbildung: Verwaltungsgruppe in Virtual Network Manager.":::

Während des Erstellungsprozesses definieren Sie den Bereich, den Azure Virtual Network Manager verwaltet. Zum Definieren eines Bereichs muss eine [Verwaltungsgruppe](../governance/management-groups/overview.md) erstellt werden. Nachdem Sie den Bereich definiert haben, aktivieren Sie Features wie *Konnektivität* und die *Rolle SecurityAdmin* für Virtual Network Manager.

Nachdem Sie die Virtual Network Manager-Instanz bereitgestellt haben, erstellen Sie eine *Netzwerkgruppe*, indem Sie bedingte Anweisungen verwenden, um virtuelle Netzwerke nach Namen, Tags oder IDs (dynamische Mitgliedschaft) auszuwählen. Sie können auch bestimmte virtuelle Netzwerke (statische Mitgliedschaft) auswählen. Die definierten Netzwerkgruppenregeln werden in Azure Policy als benutzerdefinierte Initiativendefinition und entsprechende Zuweisung widergespiegelt, die die Regeln veranschaulichen, die Sie für die Mitgliedschaft virtueller Netzwerke definiert haben. Weitere Informationen zu Azure Policy-Initiativen finden Sie unter [Azure Policy-Initiativenstruktur](../governance/policy/concepts/initiative-definition-structure.md). Diese Richtlinien sind derzeit im schreibgeschützten Modus verfügbar. Weitere Informationen zum Erstellen, Aktualisieren und Löschen dieser Richtlinien finden Sie unter [Netzwerkgruppen und Azure Policy](concept-network-groups.md#network-group-and-azure-policy). Anschließend erstellen Sie Konnektivitäts- und/oder Sicherheitskonfigurationen, die basierend auf Ihrer Topologie und Ihren Sicherheitsanforderungen auf diese Netzwerkgruppen angewendet werden. 

Mit einer Konnektivitätskonfiguration können Sie eine Mesh- oder Hub-and-Spoke-Netzwerktopologie erstellen. Mit einer Sicherheitskonfiguration können Sie eine Sammlung von Regeln definieren, die Sie auf mindestens eine Netzwerkgruppe auf globaler Ebene anwenden können. Nachdem Sie die gewünschten Netzwerkgruppen und Konfigurationen erstellt haben, können Sie die Konfigurationen in einer beliebigen Region Ihrer Wahl bereitstellen.

## <a name="key-benefits"></a>Hauptvorteile

* Verwalten Sie Konnektivitäts- und Sicherheitsrichtlinien global über Regionen und Abonnements hinweg.

* Ermöglichen Sie transitive Kommunikation zwischen Spokes in einer Hub-and-Spoke-Konfiguration, ohne die Komplexität der Verwaltung eines Meshnetzwerks.

* Hochgradig skalierbarer und hochverfügbarer Dienst mit Redundanz und Replikation auf der ganzen Welt.

* Möglichkeit zum Erstellen globaler Netzwerksicherheitsregeln, die Regeln für Netzwerksicherheitsgruppen außer Kraft setzen.

* Geringe Latenz und hohe Bandbreite zwischen Ressourcen in verschiedenen virtuellen Netzwerken mithilfe von Peering virtueller Netzwerke.

* Rollout von Netzwerkänderungen über eine bestimmte Region mit Sequenz und Häufigkeit Ihrer Wahl.

## <a name="public-preview-regions"></a>Regionen mit öffentlicher Vorschau

* USA Nord Mitte

* USA (Westen)

* USA, Westen 2

* East US

* USA (Ost) 2

* Nordeuropa

* Europa, Westen

* Frankreich, Mitte

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz über das Azure-Portal.
- Weitere Informationen zu [Netzwerkgruppen](concept-network-groups.md) in Azure Virtual Network Manager.
- Erfahren Sie, wozu Sie eine [Konnektivitätskonfiguration](concept-connectivity-configuration.md) verwenden können.
- Erfahren Sie mehr über [Sicherheitsadministratorkonfigurationen](concept-security-admins.md).
