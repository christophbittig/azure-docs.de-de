---
title: Häufig gestellte Fragen zu Azure Virtual Network Manager
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Virtual Network Manager.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 921c829cefae8ab4ea96066d70c5d110fc9188f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451937"
---
# <a name="azure-virtual-network-manager-faq"></a>Azure Virtual Network Manager FAQ

## <a name="general"></a>Allgemein

### <a name="what-regions-are-available-in-public-preview"></a>Welche Regionen sind in der öffentlichen Vorschau verfügbar?

* USA Nord Mitte

* USA (Westen)

* USA, Westen 2

* East US

* USA (Ost) 2

* Nordeuropa

* Europa, Westen

* Frankreich, Mitte

### <a name="what-are-common-use-cases-for-using-azure-virtual-network-manager"></a>Was sind häufige Anwendungsfälle für die Verwendung von Azure Virtual Network Manager?

* Als IT-Sicherheitsmanager können Sie verschiedene Netzwerkgruppen erstellen, um den Anforderungen Ihrer Umgebung und deren Funktionen gerecht zu werden. Sie können beispielsweise Netzwerkgruppen für Produktions- und Test-Netzwerkumgebungen, Entwicklungsteams, die Finanzabteilung usw. erstellen, um deren Konnektivität und Sicherheitsregeln in großem Umfang zu verwalten. 

* Sie können Konnektivitätskonfigurationen anwenden, um eine Mesh- oder eine Hub-and-Spoke-Netzwerktopologie für eine große Anzahl von virtuellen Netzwerken in den Abonnements Ihres Unternehmens zu erstellen. 

* Sie können risikoreichen Datenverkehr ablehnen: Als Administrator eines Unternehmens können Sie bestimmte Protokolle oder Quellen blockieren, die alle NSG-Regeln außer Kraft setzen, die den Datenverkehr normalerweise zulassen würden.   

* Datenverkehr immer zulassen: Sie möchten einem bestimmten Sicherheitsscanner immer eine eingehende Verbindung zu all Ihren Ressourcen erlauben, auch wenn NSG-Regeln konfiguriert sind, die den Datenverkehr verweigern.   

## <a name="technical"></a>Technisch

### <a name="can-a-virtual-network-belong-to-multiple-azure-virtual-network-managers"></a>Kann ein virtuelles Netzwerk zu mehreren Azure Virtual Network Managern gehören?

Ja, ein virtuelles Netzwerk kann zu mehr als einem Azure Virtual Network Manager gehören.

### <a name="what-is-a-global-mesh-network-topology"></a>Was ist eine globale Mesh-Netzwerktopologie?

Ein globales Netz ermöglicht es, dass virtuelle Netze über verschiedene Regionen hinweg miteinander kommunizieren können. Die Auswirkungen sind ähnlich wie beim globalen virtuellen Netz-Peering.

### <a name="is-there-a-limit-to-how-many-network-groups-can-be-created"></a>Gibt es eine Grenze für die Anzahl der Netzwerkgruppen, die erstellt werden können?

Die Anzahl der Netzwerkgruppen, die erstellt werden können, ist unbegrenzt.

### <a name="how-do-i-remove-the-deployment-of-all-applied-configurations"></a>Gewusst wie kann ich die Bereitstellung aller angewendeten Konfigurationen aufheben?

Sie müssen eine **Keine**-Konfiguration für alle Regionen bereitstellen, auf die Sie eine Konfiguration angewendet haben.

### <a name="can-i-add-virtual-networks-from-another-subscription-not-managed-by-myself"></a>Kann ich virtuelle Netzwerke von einem anderen, nicht von mir verwalteten Abonnement hinzufügen?

Ja, Sie müssen über die entsprechenden Berechtigungen verfügen, um auf diese virtuellen Netzwerke zugreifen zu können.

### <a name="what-is-dynamic-group-membership"></a>Was bedeutet dynamische Gruppenmitgliedschaft?

Für weitere Informationen siehe [dynamische Mitgliedschaft](concept-network-groups.md#dynamic-membership).

### <a name="how-does-the-deployment-of-configuration-differ-for-dynamic-membership-and-static-membership"></a>Wie ist die Bereitstellung der Konfiguration bei dynamischer Mitgliedschaft und statischer Mitgliedschaft unterschiedlich?

Weitere Informationen finden Sie unter [Einsatz gegen Mitgliedschaftsarten](concept-deployments.md#deployment).

### <a name="how-do-i-delete-an-azure-virtual-network-manager-component"></a>Wie kann ich eine Azure Virtual Network Manager-Komponente löschen?

Weitere Informationen finden Sie in der Checkliste [Komponenten entfernen](concept-remove-components-checklist.md).

### <a name="how-can-i-see-what-configurations-are-applied-to-help-me-troubleshoot"></a>Wie kann ich sehen, welche Konfigurationen angewendet werden, um mir bei der Fehlersuche zu helfen?

Sie können die Einstellungen des Azure Virtual Network Manager unter **Netzwerkmanager** für ein virtuelles Netzwerk einsehen. Sie können sowohl die Konnektivitäts- als auch die Sicherheitsverwaltungskonfiguration sehen, die angewendet werden. Weitere Informationen finden Sie unter [Ansicht der angewandten Konfiguration](how-to-view-applied-configurations.md).

### <a name="can-a-virtual-network-managed-by-azure-virtual-network-manager-be-peered-to-a-non-managed-virtual-network"></a>Kann ein virtuelles Netzwerk, das von Azure Virtual Network Manager verwaltet wird, in ein nicht verwaltetes virtuelles Netzwerk eingesehen werden?

Ja, Sie können ein bereits erstelltes Peering überschreiben oder löschen.

### <a name="how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules"></a>Wie kann ich SQLMI-Datenverkehr explizit zulassen, bevor ich Regeln zum Verweigern eingerichtet habe?

Azure SQL Managed Instance weist einige Netzwerkanforderungen auf. Wenn Ihre Sicherheitsadministratorregeln die Netzwerkanforderungen blockieren können, können Sie die folgenden Beispielregeln verwenden, um SQLMI-Datenverkehr mit einer höheren Priorität als die der Regeln zum Verweigern zu ermöglichen, die den Datenverkehr von SQL Managed Instance blockieren können.

#### <a name="inbound-rules"></a>Eingangsregeln

| Port | Protocol | `Source` | Destination | Aktion |
| ---- | -------- | ------ | ----------- | ------ |
| 9000, 9003, 1438, 1440, 1452 | TCP | SqlManagement | **VirtualNetwork** | Allow |
| 9000, 9003 | TCP | CorpnetSaw | **VirtualNetwork** | Allow |
| 9000, 9003 | TCP | CorpnetPublic | **VirtualNetwork** | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Allow |
| Any | Any | **AzureLoadBalancer** | **VirtualNetwork** | Zulassen |

#### <a name="outbound-rules"></a>Ausgangsregeln

| Port | Protocol | `Source` | Destination | Aktion |
| ---- | -------- | ------ | ----------- | ------ |
| 443, 12000 | TCP  | **VirtualNetwork** | AzureCloud | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Zulassen |

## <a name="limits"></a>Einschränkungen

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>Was sind die Serviceeinschränkungen von Azure Virtual Network Manager?

* Ein Hub in einer Hub-and-Spoke-Topologie kann bis zu 250 Spokes aufweisen. 

* Eine Meshtopologie kann bis zu 250 virtuelle Netzwerke aufweisen.

* Die Subnetze in einem virtuellen Netzwerk können nicht miteinander kommunizieren, wenn sie in einer Mesh-Konfiguration denselben Adressraum haben. 

* Die maximale Anzahl der IP-Präfixe in allen Verwaltungsregeln zusammen beträgt 1000. 

* Die maximale Anzahl von Verwaltungsregeln in einer Ebene von Azure Virtual Network Manager beträgt 100. 

* Azure Virtual Network Manager bietet in der öffentlichen Vorschau keine mandantenübergreifende Unterstützung.

* Ein virtuelles Netzwerk kann Teil von bis zu zwei Meshkonfigurationen sein. 

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz über das Azure-Portal.
