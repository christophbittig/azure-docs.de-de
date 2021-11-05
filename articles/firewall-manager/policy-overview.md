---
title: Übersicht über die Azure Firewall Manager-Richtlinie
description: Hier finden Sie Informationen zu den Richtlinien von Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: victorh
ms.openlocfilehash: 98524c2c5c73ab7a75395464911585f80bcd092c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024294"
---
# <a name="azure-firewall-manager-policy-overview"></a>Übersicht über die Azure Firewall Manager-Richtlinie

Firewallrichtlinie ist die empfohlene Methode zum Konfigurieren Ihrer Azure Firewall. Sie ist eine globale Ressource, die über mehrere Azure Firewall-Instanzen hinweg in geschützten virtuellen Hubs sowie in virtuellen Hubnetzwerken verwendet werden kann. Richtlinien können regions- und abonnementübergreifend verwendet werden.

![Azure Firewall Manager-Richtlinie](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Richtlinienerstellung und -zuordnung

Eine Richtlinie kann auf verschiedene Arten erstellt und verwaltet werden – etwa über das Azure-Portal, per REST-API, mithilfe von Vorlagen, per Azure PowerShell oder über die Befehlszeilenschnittstelle.

Darüber hinaus können bereits vorhandene klassische Regeln über das Portal oder per Azure PowerShell aus Azure Firewall migriert werden, um Richtlinien zu erstellen. Weitere Informationen finden Sie unter [Migrieren von Azure Firewall-Konfigurationen zu einer Azure Firewall-Richtlinie](migrate-to-policy.md). 

Richtlinien können virtuellen Hubs oder VNETs zugeordnet werden. Die Firewall kann sich in einem beliebigen, Ihrem Konto zugeordneten Abonnement und in einer beliebigen Region befinden.

## <a name="classic-rules-and-policies"></a>Klassische Regeln und Richtlinien

Azure Firewall unterstützt sowohl klassische Regeln als auch Richtlinien, Richtlinien sind jedoch die empfohlene Konfiguration. Die folgende Tabelle enthält eine Gegenüberstellung von Richtlinien und klassische Regeln:


| Subject | Richtlinie  | Klassische Regeln |
| ------- | ------- | ----- |
|Enthält     |NAT, Netzwerk, Anwendungsregeln, benutzerdefinierte DNS- und DNS-Proxyeinstellungen, IP-Gruppen und Threat Intelligence-Einstellungen (einschließlich Zulassungsliste), IDPS, TLS-Überprüfung, Webkategorien, URL-Filterung|NAT, Netzwerk, Anwendungsregeln, benutzerdefinierte DNS- und DNS-Proxy-Einstellungen, IP-Gruppen und Threat Intelligence-Einstellungen (einschließlich Zulassungsliste)|
|Schutz     |Virtuelle Hubs und virtuelle Netzwerke|Nur virtuelle Netzwerke|
|Portalfunktion     |Zentrale Verwaltung mithilfe von Firewall Manager|Eigenständige Firewallumgebung|
|Unterstützung mehrerer Firewalls     |Die Firewallrichtlinie ist eine separate Ressource und kann firewallübergreifend verwendet werden.|Sie können Regeln manuell exportieren und importieren oder Verwaltungslösungen von Drittanbietern verwenden. |
|Preise     |Abrechnung auf der Grundlage der Firewallzuordnung. Siehe [Preise](#pricing).|Kostenlos|
|Unterstützte Bereitstellungsmechanismen     |Portal, REST-API, Vorlagen, Azure PowerShell und Befehlszeilenschnittstelle|Portal, REST-API, Vorlagen, PowerShell und Befehlszeilenschnittstelle |

## <a name="standard-and-premium-policies"></a>Standard- und Premium-Richtlinien

Azure Firewall unterstützt Standard- und Premium-Richtlinien. In der folgenden Tabelle werden die Unterschiede zwischen diesen beiden zusammengefasst.


|Richtlinientyp|Featureunterstützung  | Firewall-SKU-Unterstützung|
|---------|---------|----|
|Standardrichtlinie    |NAT-Regeln, Netzwerkregeln, Anwendungsregeln<br>Benutzerdefiniertes DNS, DNS-Proxy<br>IP-Adressgruppen<br>Webkategorien<br>Threat Intelligence|Standard oder Premium|
|Premium-Richtlinie    |Unterstützung für alle Standardfeatures sowie:<br><br>TLS-Überprüfung<br>Webkategorien<br>URL-Filterung<br>IDPS|Premium


## <a name="hierarchical-policies"></a>Hierarchische Richtlinien

Neue Richtlinien können von Grund auf neu erstellt oder von vorhandenen Richtlinien geerbt werden. Die Vererbung ermöglicht DevOps die Erstellung lokaler Firewallrichtlinien zusätzlich zur obligatorischen Basisrichtlinie der Organisation.

Richtlinien, die mit nicht leeren übergeordneten Richtlinien erstellt werden, erben alle Regelsammlungen der übergeordneten Richtlinie. Netzwerkregelsammlungen, die von einer übergeordneten Richtlinie geerbt wurden, haben immer Vorrang vor Netzwerkregelsammlungen, die als Teil einer neuen Richtlinie definiert werden. Gleiches gilt auch für Anwendungsregelsammlungen. Allerdings werden Netzwerkregelsammlungen unabhängig von der Vererbung immer vor Anwendungsregelsammlungen verarbeitet.

Der Threat Intelligence-Modus wird ebenfalls von der übergeordneten Richtlinie geerbt. Sie können Ihren Threat Intelligence-Modus auf einen anderen Wert festlegen, um dieses Verhalten zu überschreiben, Sie können den Modus jedoch nicht deaktivieren. Er kann nur mit einem strengeren Wert überschrieben werden. Beispiel: Wenn die übergeordnete Richtlinie auf **Nur Warnung** festgelegt ist, können Sie diese lokale Richtlinie mit **Warnen und ablehnen** konfigurieren.

Wie der Threat Intelligence-Modus wird auch die Threat Intelligence-Zulassungsliste von der übergeordneten Richtlinie geerbt. Die untergeordnete Richtlinie kann der Zulassungsliste zusätzliche IP-Adressen hinzufügen.

NAT-Regelsammlungen werden nicht geerbt, da sie für eine bestimmte Firewall spezifisch sind.

Bei der Vererbung werden alle Änderungen an der übergeordneten Richtlinie automatisch auf die zugehörigen untergeordneten Firewallrichtlinien angewendet.



## <a name="pricing"></a>Preise

Richtlinien werden basierend auf der Firewallzuordnung abgerechnet. Richtlinien mit bis zu einer Firewallzuordnung sind kostenlos. Richtlinien mit mehreren Firewallzuordnungen werden zu festen Preisen abgerechnet. Weitere Informationen finden Sie unter [Preise für Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Nächste Schritte

Wie Sie eine Azure Firewall-Instanz bereitstellen, erfahren Sie unter [Tutorial: Schützen Ihres Cloudnetzwerks mithilfe von Azure Firewall Manager unter Verwendung des Azure-Portals](secure-cloud-network.md).
