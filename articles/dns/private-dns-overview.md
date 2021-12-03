---
title: Was ist privates Azure-DNS?
description: In diesem Artikel finden Sie eine Übersicht über den privaten DNS-Hostingdienst in Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 819e3f911a769f870b792ed92b699f3356139565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457695"
---
# <a name="what-is-azure-private-dns"></a>Was ist privates Azure-DNS?

Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Dienstnamens in eine IP-Adresse verantwortlich.  Azure DNS ist ein Hostingdienst für Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur ausführt. Azure DNS unterstützt nicht nur DNS-Domänen mit Internetverbindung, sondern auch private DNS-Zonen.

Privates DNS von Azure bietet einen zuverlässigen und sicheren DNS-Dienst für Ihr virtuelles Netzwerk. Privates Azure-DNS verwaltet Domänennamen im virtuellen Netzwerk und löst diese auf, ohne dass eine benutzerdefinierte DNS-Lösung konfiguriert werden muss. Durch die Verwendung privater DNS-Zonen können Sie während der Bereitstellung anstelle der von Azure bereitgestellten Namen Ihre eigenen benutzerdefinierten Domänennamen verwenden. Die Verwendung eines benutzerdefinierten Domänennamens erleichtert Ihnen die optimale Anpassung der Architektur Ihres virtuellen Netzwerks an die Anforderungen Ihres Unternehmens. Es wird eine Namensauflösung für virtuelle Computer in einem virtuellen Netzwerk und in verbundenen virtuellen Netzwerken bereitgestellt. Darüber hinaus können Sie Zonennamen mit einer Split-Horizon-Ansicht konfigurieren, sodass eine private und eine öffentliche DNS-Zone den Namen verwenden können.

Um die Datensätze einer privaten DNS-Zone in Ihrem virtuellen Netzwerk aufzulösen, müssen Sie das virtuelle Netzwerk mit der Zone verknüpfen. Verknüpfte virtuelle Netzwerke haben Vollzugriff und können alle in der privaten Zone veröffentlichten DNS-Datensätze auflösen. Sie können die automatische Registrierung für eine virtuelle Netzwerkverknüpfung aktivieren. Wenn Sie die automatische Registrierung für eine virtuelle Netzwerkverknüpfung aktivieren, werden die DNS-Datensätze für die virtuellen Computer in diesem virtuellen Netzwerk in der privaten Zone registriert. Wenn die automatische Registrierung aktiviert wird, aktualisiert Azure DNS den Zoneneintrag, sobald ein virtueller Computer erstellt, seine IP-Adresse geändert oder der virtuelle Computer gelöscht wird.

![Übersicht über DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Als bewährte Methode sollten Sie keine Domäne vom Typ *.local* für Ihre private DNS-Zone verwenden. Nicht alle Betriebssysteme unterstützen dies.

## <a name="benefits"></a>Vorteile

Privates Azure-DNS bietet folgende Vorteile:

* **Macht benutzerdefinierte DNS-Lösungen überflüssig**. Früher erstellten viele Kunden benutzerdefinierte DNS-Lösungen zum Verwalten von DNS-Zonen in ihrem virtuellen Netzwerk. Die Verwaltung von DNS-Zonen kann ab sofort mithilfe der nativen Azure-Infrastruktur durchgeführt werden, sodass das aufwendige Erstellen und Verwalten benutzerdefinierter DNS-Lösungen entfällt.

* **Verwenden aller gängigen DNS-Eintragstypen**. Azure DNS unterstützt A-, AAAA-, CNAME-, MX-, PTR-, SOA-, SRV- und TXT-Einträge.

* **Automatische Verwaltung von Hostnameneinträgen**. Parallel zum Hosten Ihrer benutzerdefinierten DNS-Einträge verwaltet Azure automatisch Hostnameneinträge für die virtuellen Computer in den angegebenen virtuellen Netzwerken. In diesem Szenario können Sie die verwendeten Domänennamen optimieren, ohne benutzerdefinierte DNS-Lösungen erstellen oder die Anwendung ändern zu müssen.

* **Hostnamenauflösung zwischen virtuellen Netzwerken**. Im Gegensatz zu von Azure bereitgestellten Hostnamen können private DNS-Zonen von virtuellen Netzwerken gemeinsam genutzt werden. Diese Möglichkeit vereinfacht netzwerkübergreifende und Dienstermittlungsszenarien wie z.B. das Peering virtueller Netzwerke.

* **Vertraute Tools und Benutzerfreundlichkeit**. Zur Reduzierung der Lernkurve verwendet dieser Dienst die bewährten Azure DNS-Tools (Azure-Portal, Azure PowerShell, Azure-Befehlszeilenschnittstelle, Azure Resource Manager-Vorlagen und REST-API).

* **Split-Horizon-DNS-Unterstützung**. Mit Azure DNS können Sie Zonen mit gleichem Namen erstellen, die innerhalb eines virtuellen Netzwerks und im öffentlichen Internet unterschiedlich aufgelöst werden. Ein typisches Szenario für Split-Horizon-DNS ist die Bereitstellung einer bestimmte Version eines Diensts für die Verwendung in Ihrem virtuellen Netzwerk .

* **In allen Azure-Regionen verfügbar**. Das Azure DNS-Feature für private Zonen ist in allen Azure-Regionen in der öffentlichen Azure-Cloud verfügbar.

## <a name="capabilities"></a>Funktionen

Privates Azure-DNS bietet die folgenden Funktionen:

* **Automatische Registrierung von virtuellen Computern in einem virtuellen Netzwerk, das mit einer privaten Zone mit aktivierter automatischer Registrierung verknüpft ist**. Virtuelle Computer werden für die private Zone als A-Einträge registriert, die auf die zugehörigen privaten IP-Adressen verweisen. Beim Löschen eines virtuellen Computers in einer virtuellen Netzwerkverknüpfung mit aktivierter automatischer Registrierung entfernt Azure DNS automatisch auch den entsprechenden DNS-Eintrag aus der verknüpften privaten Zone.

* **Die Forward-DNS-Auflösung wird über virtuelle Netzwerke hinweg unterstützt, die mit der privaten Zone verknüpft sind**. Für die DNS-Auflösung über virtuelle Netzwerke hinweg ist es nicht explizit erforderlich, ein Peering für die virtuellen Netzwerke zu konfigurieren. Möglicherweise möchten Sie jedoch für andere Szenarien (z. B. HTTP-Datenverkehr) ein Peering für virtuelle Netzwerke einrichten.

* **Reverse-DNS-Lookups werden innerhalb des Bereichs eines virtuellen Netzwerks unterstützt**. Bei einem Reverse-DNS-Lookup für eine private IP-Adresse, die einer privaten Zone zugewiesen ist, wird der FQDN zurückgegeben, der den Host-/Eintragsnamen sowie den Zonennamen als Suffix enthält.

## <a name="other-considerations"></a>Weitere Überlegungen

Für privates Azure-DNS gelten die folgenden Einschränkungen:

* Ein bestimmtes virtuelles Netzwerk kann nur mit genau einer privaten Zone verknüpft werden, wenn die automatische Registrierung von VM-DNS-Einträgen aktiviert ist. Sie können jedoch mehrere virtuelle Netzwerke mit einer einzelnen DNS-Zone verknüpfen.
* Reverse-DNS funktioniert nur für den privaten IP-Bereich im verknüpften virtuellen Netzwerk.
* Reverse-DNS für eine private IP-Adresse in einem verknüpften virtuellen Netzwerk gibt `internal.cloudapp.net` als Standardsuffix für den virtuellen Computer zurück. Für virtuelle Netzwerke, die mit einer privaten Zone mit aktivierter automatischer Registrierung verknüpft sind, gibt Reverse-DNS für eine private IP-Adresse zwei vollqualifizierte Domänennamen zurück: einen mit dem Standardsuffix `internal.cloudapp.net` und den anderen mit dem Suffix der privaten Zone.
* Die bedingte Weiterleitung wird derzeit nicht nativ unterstützt. Informationen zum Aktivieren der Auflösung zwischen Azure-Netzwerken und lokalen Netzwerken finden Sie unter [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Preise

Informationen zu den Preisen finden Sie unter [Azure DNS – Preise ](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie über [Azure PowerShell](./private-dns-getstarted-powershell.md) oder [Azure CLI](./private-dns-getstarted-cli.md) eine private Zone in Azure DNS erstellen.

* Informieren Sie sich über einige gängige [Szenarien zu privaten Zonen](./private-dns-scenarios.md), die mit privaten Zonen in Azure DNS realisiert werden können.

* Allgemeine Fragen und Antworten zu privaten Zonen in Azure DNS finden Sie unter [Häufig gestellte Fragen zu privatem Azure-DNS](./dns-faq-private.yml).

* Weitere Informationen zu DNS-Zonen und -Einträgen finden Sie unter [Übersicht über DNS-Zonen und -Einträge](dns-zones-records.md).

* Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/fundamentals/networking-overview.md) von Azure.
