---
title: Schützen Ihrer Netzwerkressourcen in Azure Security Center
description: Dieses Dokument behandelt Empfehlungen in Azure Security Center, die zum Schutz Ihrer Azure-Netzwerkressourcen sowie zur Einhaltung von Sicherheitsrichtlinien beitragen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: dd09279665230062c7a2567784731144ef438c6c
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237238"
---
# <a name="protect-your-network-resources"></a>Schützen von Netzwerkressourcen
Azure Security Center analysiert ständig den Sicherheitsstatus Ihrer Azure-Ressourcen anhand bewährter Methoden für Netzwerksicherheit. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center Empfehlungen, die Sie beim Konfigurieren der erforderlichen Steuerelemente zum Sichern und Schützen Ihrer Ressourcen unterstützen.

Eine vollständige Liste der Empfehlungen für das Netzwerk finden Sie unter [Netzwerkempfehlungen](recommendations-reference.md#recs-networking).

Dieser Artikel behandelt Empfehlungen, die aus Sicht der Netzwerksicherheit auf Ihre Azure-Ressourcen zutreffen. Bei Netzwerkempfehlungen stehen Firewalls der nächsten Generation, Netzwerksicherheitsgruppen, Regeln für den eingehenden Datenverkehr mit übermäßigem JIT-VM-Zugriff und vieles mehr im Mittelpunkt. Eine Liste von Netzwerkempfehlungen und Aktionen zur Problembehebung finden Sie unter [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

Das Feature **Netzwerk** von Security Center umfasst Folgendes: 

- Netzwerkübersicht (erfordert Azure Defender)
- [Adaptive Netzwerkhärtung](security-center-adaptive-network-hardening.md) (erfordert Azure Defender)
- Empfehlungen für die Netzwerksicherheit
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Anzeigen der Netzwerkressourcen und ihrer Empfehlungen

Wählen Sie auf der Seite [Asset Inventory](asset-inventory.md) (Ressourcenbestand) über den Ressourcentypfilter die Netzwerkressourcen aus, die Sie untersuchen möchten:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Netzwerk-Ressourcentypen im Ressourcenbestand" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Netzwerkübersicht

Die interaktive Netzwerkübersicht bietet eine grafische Darstellung mit Angaben zu Sicherheitsaspekten, die Empfehlungen und Einblicke zum Absichern Ihrer Netzwerkressourcen umfassen. Anhand der Übersicht können Sie die Netzwerktopologie Ihrer Azure-Workloads sowie Verbindungen zwischen Ihren virtuellen Computern und Subnetzen erkennen. Außerdem können Sie von der Übersicht aus genauere Informationen zu bestimmten Ressourcen und die Empfehlungen für diese Ressourcen anzeigen.

So öffnen Sie die Netzwerkübersicht:

1. Öffnen Sie im Menü von Security Center das Azure Defender-Dashboard, und wählen Sie **Netzwerkübersicht** aus.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Öffnen der Netzwerkübersicht auf dem Azure Defender-Dashboard" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Wählen Sie im Menü **Schichten** die Option **Topologie** aus.
 
Die Standardansicht der Topologie umfasst Folgendes:

- Abonnements, die Sie in Azure ausgewählt haben. Die Übersicht unterstützt mehrere Abonnements.
- Virtuelle Computer, Subnetze und VNets vom Resource Manager-Ressourcentyp (klassische Azure-Ressourcen werden nicht unterstützt).
- VNets mit Peering
- Nur Ressourcen, die [Netzwerkempfehlungen](security-center-recommendations.md) mit einem hohen oder mittleren Schweregrad aufweisen.  
- Ressourcen mit Internetzugriff.
- Die Übersicht ist für die in Azure ausgewählten Abonnements optimiert. Wenn Sie die Auswahl ändern, wird die Übersicht neu berechnet und entsprechend den neuen Einstellungen optimiert.  

[![Übersicht über die Netzwerktopologie](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Grundlegendes zur Netzwerkübersicht

In der Netzwerkübersicht können Ihre Azure-Ressourcen in der Ansicht **Topologie** und der Ansicht **Datenverkehr** angezeigt werden. 

### <a name="the-topology-view"></a>Topologieansicht

Die Ansicht **Topologie** der Netzwerkübersicht bietet Ihnen folgende Einblicke in Ihre Netzwerkressourcen:

- Im inneren Kreis sehen Sie alle VNETs in den ausgewählten Abonnements, der nächste Kreis zeigt alle Subnetze und der äußere Kreis alle virtuellen Computer.
- Die Verbindungslinien zwischen den Ressourcen in der Übersicht geben an, welche Ressourcen miteinander verknüpft sind und wie Ihr Azure-Netzwerk strukturiert ist. 
- Verwenden Sie die Indikatoren für den Schweregrad, um schnell einen Überblick über die Ressourcen zu erhalten, für die offene Empfehlungen aus Security Center vorhanden sind.
- Sie können auf einzelne Ressourcen klicken, um genauere Informationen aufzurufen und die Details zu dieser Ressource sowie die jeweiligen Empfehlungen direkt und im Kontext der Netzwerkübersicht anzuzeigen.  
- Wenn zu viele Ressourcen in der Übersicht angezeigt werden, verwendet Azure Security Center einen eigenen Algorithmus zum intelligenten Gruppieren Ihrer Ressourcen und hebt diejenigen hervor, die den kritischsten Zustand und die meisten Empfehlungen mit höchstem Schweregrad aufweisen. 

Da die Übersicht interaktiv und dynamisch ist, kann auf jeden Knoten geklickt und die Ansicht anhand der Filter geändert werden:

1. Sie können die Anzeige in der Netzwerkübersicht mithilfe der Filter am oberen Rand ändern. Sie können als Schwerpunkt der Übersicht Folgendes festlegen:

   -  **Sicherheitsintegrität**: Sie können die Übersicht nach Schweregrad (Hoch, Mittel, Niedrig) Ihrer Azure-Ressourcen filtern.
   - **Empfehlungen**: Sie können die Anzeige von Ressourcen basierend auf den Empfehlungen auswählen, die für diese Ressourcen aktiv sind. Sie können beispielsweise nur Ressourcen anzeigen, für die Security Center das Aktivieren von Netzwerksicherheitsgruppen empfiehlt.
   - **Netzwerkzonen**: Standardmäßig werden in der Übersicht nur Ressourcen mit Internetzugriff angezeigt, Sie können jedoch auch interne virtuelle Computer auswählen.
 
2. Sie können jederzeit auf **Zurücksetzen** in der oberen linken Ecke klicken, um die Übersicht in den Standardzustand zurückzusetzen.

So zeigen Sie Detailinformationen zu einer Ressource an:

1. Wenn Sie eine bestimmte Ressource in der Übersicht auswählen, wird der rechte Bereich geöffnet und zeigt allgemeine Informationen zur Ressource, verbundene Sicherheitslösungen (sofern vorhanden) und die für die Ressource relevanten Empfehlungen an. Dieses Verhalten ist für jeden ausgewählten Ressourcentyp gleich. 
2. Wenn Sie auf einen Knoten in der Übersicht zeigen, können Sie allgemeine Informationen zur Ressource sehen, einschließlich Abonnement, Ressourcentyp und Ressourcengruppe.
3. Verwenden Sie den Link, um die QuickInfo zu vergrößern und die Übersicht auf diesen bestimmten Knoten neu zu fokussieren. 
4. Um die Fokussierung der Übersicht auf einen bestimmten Knoten aufzuheben, verkleinern Sie die Anzeige.

### <a name="the-traffic-view"></a>Datenverkehrsansicht

Die Ansicht **Datenverkehr** bietet Ihnen eine Übersicht über den gesamten möglichen Datenverkehr zwischen Ihren Ressourcen. Dadurch erhalten Sie eine visuellen Überblick über alle konfigurierten Regeln, die festlegen, welche Ressourcen mit wem kommunizieren können. Sie können so die vorhandene Konfiguration der Netzwerksicherheitsgruppen sehen und auch schnell mögliche riskante Konfigurationen innerhalb Ihrer Workloads erkennen.

### <a name="uncover-unwanted-connections"></a>Entdecken unerwünschter Verbindungen

Die Stärke dieser Ansicht ist die Fähigkeit, diese zulässigen Verbindungen zusammen mit den bestehenden Sicherheitsrisiken anzuzeigen, sodass Sie diesen Querschnitt der Daten für die erforderliche Verstärkung der Sicherheit Ihrer Ressourcen verwenden können. 

Sie können beispielsweise zwei Computer erkennen, von denen Sie gar nicht wussten, dass diese miteinander kommunizieren können, und so die Workloads und Subnetze besser isolieren.

### <a name="investigate-resources"></a>Untersuchen von Ressourcen

So zeigen Sie Detailinformationen zu einer Ressource an:

1. Wenn Sie eine bestimmte Ressource in der Übersicht auswählen, wird der rechte Bereich geöffnet und zeigt allgemeine Informationen zur Ressource, verbundene Sicherheitslösungen (sofern vorhanden) und die für die Ressource relevanten Empfehlungen an. Dieses Verhalten ist für jeden ausgewählten Ressourcentyp gleich. 
2. Klicken Sie auf **Datenverkehr**, um die Liste des möglichen ausgehenden und eingehenden Datenverkehrs für die Ressource anzuzeigen. Dabei handelt es sich um eine umfassende Liste, wer mit der Ressource kommunizieren kann, mit wem die Ressource kommunizieren kann und über welche Protokolle und Ports die Kommunikation erfolgt. Wenn Sie beispielsweise eine VM auswählen, werden alle VMs angezeigt, mit denen diese kommunizieren kann, und wenn Sie ein Subnetz auswählen, werden alle Subnetze angezeigt, mit denen dieses kommunizieren kann.

**Diese Daten basieren auf einer Analyse der Netzwerksicherheitsgruppen sowie erweiterten Machine Learning-Algorithmen, die mehrere Regeln analysieren, um deren Überkreuzungen und Interaktionen zu verstehen.** 

[![Übersicht über den Netzwerkdatenverkehr](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

- [Schützen von Computern und Anwendungen im Azure Security Center](./asset-inventory.md)