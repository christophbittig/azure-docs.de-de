---
title: Was ist eine Netzwerkgruppe in Azure Virtual Network Manager (Vorschau)?
description: Erfahren Sie, wie Netzwerkgruppen Sie bei der Verwaltung Ihrer virtuellen Netzwerke unterstützen können.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5da75fba68e285ea4b01feee514bd38f6ceae036
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493594"
---
# <a name="what-is-a-network-group-in-azure-virtual-network-manager-preview"></a>Was ist eine Netzwerkgruppe in Azure Virtual Network Manager (Vorschau)?

In diesem Artikel lernen Sie *Netzwerkgruppen* kennen und erfahren, wie sie Ihnen helfen können, virtuelle Netzwerke zur einfacheren Verwaltung zusammenzufassen. Sie werden auch etwas über *Statische Gruppenzugehörigkeit* und *Dynamische Gruppenzugehörigkeit* lernen und wie man jede Art von Mitgliedschaft verwendet.

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-group"></a>Netzwerkgruppe

Eine *Netzgruppe* ist eine Gruppe von virtuellen Netzen, die manuell oder mit Hilfe von bedingten Anweisungen ausgewählt wird. Wenn Sie virtuelle Netze manuell auswählen, werden sie *statische Mitglieder* genannt. Virtuelle Netze, die mit Hilfe von bedingten Anweisungen ausgewählt werden, werden als *dynamische Mitglieder* bezeichnet. 

## <a name="static-membership"></a>Statische Mitgliedschaft

Wenn Sie eine Netzwerkgruppe erstellen, können Sie virtuelle Netzwerke zu einer Gruppe hinzufügen, indem Sie manuell einzelne virtuelle Netzwerke aus einer bereitgestellten Liste auswählen. Die Liste der virtuellen Netzwerke ist abhängig vom Bereich (Verwaltungsgruppe oder Abonnement), der zum Zeitpunkt der Bereitstellung von Azure Virtual Network Manager definiert wurde. Diese Methode ist nützlich, wenn Sie einige virtuelle Netzwerke haben, die Sie der Netzwerkgruppe hinzufügen möchten. Aktualisierungen von Konfigurationen, die statische Mitglieder enthalten, müssen erneut bereitgestellt werden, damit die neuen Änderungen übernommen werden.

## <a name="dynamic-membership"></a>Dynamische Mitgliedschaft

Die dynamische Mitgliedschaft gibt Ihnen die Flexibilität, mehrere virtuelle Netzwerke auf einmal auszuwählen, wenn sie die von Ihnen definierten Bedingungen erfüllen. Diese Methode ist nützlich für Szenarien, in denen Sie Hunderte oder Tausende von virtuellen Netzwerken in einem oder mehreren Abonnements haben und eine Handvoll davon entweder nach Namen, IDs oder Tags auswählen müssen. Jede Bedingung wird in der aufgelisteten Reihenfolge abgearbeitet und die Konfigurationen werden auf die virtuellen Netzwerke angewendet, um diese Bedingungen zu erfüllen. Siehe [Elemente von der dynamischen Mitgliedschaft ausschließen](how-to-exclude-elements.md), um zu erfahren, wie man bedingte Anweisungen konfiguriert.

## <a name="network-group-and-azure-policy"></a>Netzwerkgruppe und Azure-Richtlinie

Wenn Sie eine Netzwerkgruppe erstellen, wird eine Azure-Richtlinie erstellt, damit Azure Virtual Network Manager über Änderungen an der Mitgliedschaft im virtuellen Netzwerk informiert wird. Die definierten Richtlinien sind für Sie einsehbar, können aber derzeit von den Benutzern nicht bearbeitet werden. Das Erstellen, Ändern und Löschen von Azure-Richtliniendefinitionen und Zuweisungen für Netzwerkgruppen ist heute nur noch über den Azure Network Manager möglich. 

Um eine Definition und Zuweisung einer Azure-Richtlinieninitiative für Azure Network Manager-Ressourcen zu erstellen, müssen Sie eine Netzwerkgruppe mit den erforderlichen Konfigurationen erstellen und bereitstellen. Um eine bestehende Azure-Richtlinieninitiativendefinition oder eine entsprechende Zuweisung zu aktualisieren, müssen Sie Änderungen an der Netzwerkgruppe innerhalb der Azure Virtual Network Manager-Ressource vornehmen und bereitstellen. Um eine Azure-Richtlinieninitiativendefinition und -zuweisung zu löschen, müssen Sie die Azure Virtual Network Manager-Ressourcen, die mit Ihrer Richtlinie verbunden sind, zurücksetzen und löschen. Dies kann die Rücknahme der Bereitstellung einer Konfiguration, das Löschen einer Konfiguration und das Löschen einer Netzwerkgruppe umfassen. Weitere Informationen zur Löschung finden Sie in der Azure Virtual Network Manager [Checkliste zum Entfernen von Komponenten](concept-remove-components-checklist.md).  

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz über das Azure-Portal.
- Erfahren Sie, wie Sie eine [Hub-and-Spoke-Topologie](how-to-create-hub-and-spoke.md) mit Azure Virtual Network Manager erstellen.
- Erfahren Sie, wie Sie den Netzwerkverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-portal.md) blockieren können.
