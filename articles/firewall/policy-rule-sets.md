---
title: Azure Firewall-Regelsätze
description: Die Azure Firewall-Richtlinie hat eine Hierarchie von Regelsammlungsgruppen, Regelsammlungen und Regeln.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/9/2021
ms.author: victorh
ms.openlocfilehash: 5dcb1c84c91e295d7588acccd33ee157937f9fb8
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138849"
---
# <a name="azure-firewall-policy-rule-sets"></a>Azure Firewall-Richtlinien-Regelsätze

Die Firewall-Richtlinie ist eine Ressource der obersten Ebene, die Sicherheits- und Betriebseinstellungen für Azure Firewall enthält. Sie können Firewall-Richtlinien verwenden, um Regelsätze zu verwalten, die die Azure Firewall zum Filtern des Datenverkehrs verwendet. Die Firewall-Richtlinie organisiert, priorisiert und verarbeitet die Regelsätze basierend auf einer Hierarchie mit den folgenden Komponenten: Regelsammlungsgruppen, Regelsammlungen und Regeln.

:::image type="content" source="media/policy-rule-sets/policy-rule-sets.png" alt-text=" Azure Policy Regelsatz-Hierarchie":::

## <a name="rule-collection-groups"></a>Regelsammelgruppen

Eine Regelsammlungsgruppe wird zur Gruppierung von Regelsammlungen verwendet. Sie sind die erste Einheit, die von der Azure Firewall verarbeitet wird, und sie folgen einer Prioritätsreihenfolge, die auf Werten basiert. Es gibt drei Standard-Regelsammlungsgruppen, deren Prioritätswerte von vornherein festgelegt sind. Sie werden in der folgenden Reihenfolge bearbeitet:


|Name der Regelsammelgruppe  |Priorität  |
|---------|---------|
|Standard-DNAT-Regelsammelgruppe (Destination Network Address Translation)      |100|
|Standard-Sammelgruppe für Netzwerkregeln      |200|
|Standard-Anwendungsregel-Sammelgruppe      |300|

Auch wenn Sie die Standard-Regelsammlungsgruppen nicht löschen oder ihre Prioritätswerte ändern können, können Sie ihre Verarbeitungsreihenfolge auf andere Weise beeinflussen. Wenn Sie eine andere Prioritätsreihenfolge als die Standardreihenfolge festlegen möchten, können Sie benutzerdefinierte Regelsammlungsgruppen mit den von Ihnen gewünschten Prioritätswerten erstellen. In diesem Szenario verwenden Sie die Standard-Regelsammlungsgruppen überhaupt nicht und verwenden nur die von Ihnen erstellten Gruppen, um die Verarbeitungslogik anzupassen.  

Regelsammlungsgruppen enthalten eine oder mehrere Regelsammlungen, die vom Typ DNAT, Netzwerk oder Anwendung sein können. Zum Beispiel können Sie Regeln, die zu denselben Workloads oder einem VNet gehören, in einer Regelsammlungsgruppe gruppieren. 

Regelsammelgruppen haben eine maximale Größe von 2 MB. Wenn Sie mehr als 2 Mb benötigen, können Sie die Regeln in mehrere Regelsammlungsgruppen aufteilen. Eine Firewall-Richtlinie kann 50 Regelsammlungsgruppen enthalten.


## <a name="rule-collections"></a>Regelsammlungen

Eine Regelsammlung gehört zu einer Regelsammlungsgruppe und enthält eine oder mehrere Regeln. Sie sind die zweite Einheit, die von der Firewall verarbeitet wird, und sie folgen einer Prioritätsreihenfolge, die auf Werten basiert. Regelsammlungen müssen eine definierte Aktion (zulassen oder verweigern) und einen Prioritätswert haben. Die definierte Aktion gilt für alle Regeln innerhalb der Regelsammlung. Der Prioritätswert bestimmt die Reihenfolge, in der die Regelsammlungen verarbeitet werden.
  
Es gibt drei Arten von Regelsammlungen:

- DNAT
- Netzwerk
- Anwendung

Regelsammlungstypen müssen mit ihrer übergeordneten Regelsammlungsgruppenkategorie übereinstimmen. Ein Beispiel: Eine DNAT-Regelsammlung kann nur Teil einer DNAT-Regelsammlungsgruppe sein.

## <a name="rules"></a>Regeln

Eine Regel gehört zu einer Regelsammlung und legt fest, welcher Datenverkehr in Ihrem Netzwerk zugelassen oder abgelehnt wird. Sie sind die dritte Einheit, die von der Firewall verarbeitet wird, und sie folgen keiner Prioritätsreihenfolge auf der Grundlage von Werten. Die Verarbeitungslogik für Regeln folgt einem Top-Down-Ansatz. Der gesamte Datenverkehr, der die Firewall durchläuft, wird anhand der definierten Regeln auf eine Übereinstimmung mit "Zulassen" oder "Verweigern" geprüft. Wenn es keine Regel gibt, die den Datenverkehr zulässt, wird der Datenverkehr standardmäßig verweigert.

Bei Anwendungsregeln wird der Datenverkehr von unserer eingebauten [Infrastrukturregelsammlung](infrastructure-fqdns.md) verarbeitet, bevor er standardmäßig verweigert wird.

Es gibt drei Arten von Regeln:

- DNAT
- Netzwerk
- Anwendung

### <a name="dnat-rules"></a>DNAT-Regeln

DNAT-Regeln erlauben oder verweigern eingehenden Datenverkehr über die öffentliche(n) IP-Adresse(n) der Firewall. Sie können eine DNAT-Regel verwenden, wenn Sie möchten, dass eine öffentliche IP-Adresse in eine private IP-Adresse übersetzt wird. Die öffentlichen IP-Adressen der Azure Firewall können verwendet werden, um eingehenden Datenverkehr aus dem Internet abzuhören, den Datenverkehr zu filtern und diesen Datenverkehr auf interne Ressourcen in Azure zu übertragen.

### <a name="network-rules"></a>Netzwerkregeln

Netzwerkregeln erlauben oder verweigern eingehenden, ausgehenden und Ost-West-Verkehr auf Basis der Netzwerkschicht (L3) und der Transportschicht (L4).  
Sie können eine Netzwerkregel verwenden, wenn Sie den Datenverkehr auf der Grundlage von IP-Adressen, beliebigen Ports und Protokollen filtern möchten.


### <a name="application-rules"></a>Anwendungsregeln

Anwendungsregeln erlauben oder verweigern eingehenden, ausgehenden und Ost-West-Verkehr auf der Grundlage der Anwendungsschicht (L7). Sie können eine Anwendungsregel verwenden, wenn Sie den Datenverkehr auf der Grundlage von vollständig qualifizierten Domänennamen (FQDNs) und HTTP/HTTPS-Protokollen filtern möchten. 


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Verarbeitung von Azure Firewall-Regeln: [Konfigurieren Sie Azure Firewall-Regeln](rule-processing.md).
