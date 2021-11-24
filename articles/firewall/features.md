---
title: Azure Firewall Standard-Features
description: Erfahren Sie mehr über Azure Firewall-Features.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 0c9b871197085a6a220482c3b9d1d35f25d5b427
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136764"
---
# <a name="azure-firewall-standard-features"></a>Azure Firewall Standard-Features

[Azure Firewall](overview.md) Standard ist ein verwalteter, Cloud-basierter Netzwerksicherheitsdienst, der Ihre virtuellen Azure-Netzwerkressourcen schützt.

:::image type="content" source="media/features/firewall-standard.png" alt-text="Azure Firewall Standard-Features":::

Azure Firewall bietet die folgenden Features:

- Integrierte Hochverfügbarkeit
- Verfügbarkeitszonen
- Uneingeschränkte Cloudskalierbarkeit
- FQDN-Anwendungsfilterregeln
- Filterregeln für den Netzwerkdatenverkehr
- FQDN-Tags
- Diensttags
- Bedrohungsanalyse
- DNS-Proxy
- Benutzerdefinierter DNS
- FQDN in Netzwerkregeln
- Bereitstellungz ohne öffentliche IP-Adresse im Forced Tunnel Mode
- SNAT-Unterstützung für ausgehenden Datenverkehr
- DNAT-Unterstützung für eingehenden Datenverkehr
- Mehrere öffentliche IP-Adressen
- Azure Monitor-Protokollierung
- Tunnelerzwingung
- Webkategorien
- Zertifizierungen

## <a name="built-in-high-availability"></a>Integrierte Hochverfügbarkeit

Hochverfügbarkeit ist integriert, sodass keine zusätzlichen Lastenausgleichsmodule erforderlich sind und Sie nichts konfigurieren müssen.

## <a name="availability-zones"></a>Verfügbarkeitszonen

Zur Erhöhung der Verfügbarkeit kann Azure Firewall während der Bereitstellung so konfiguriert werden, dass mehrere Verfügbarkeitszonen abgedeckt werden. Mit Verfügbarkeitszonen erhöht sich die Verfügbarkeit auf eine Betriebszeit von 99,99 %. Weitere Informationen finden Sie in der [Vereinbarung zum Servicelevel (SLA) für Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Die SLA für 99,99 % Betriebszeit wird angeboten, wenn zwei oder mehr Verfügbarkeitszonen ausgewählt werden.

Unter Verwendung der Standard-SLA des Diensts von 99,95 % können Sie Azure Firewall außerdem aus Gründen der Nähe einer bestimmten Zone zuordnen.

Für eine Firewall, die in einer Verfügbarkeitszone bereitgestellt wird, fallen keine zusätzlichen Kosten an. Es fallen jedoch zusätzliche Kosten für die ein- und ausgehenden Datenübertragungen an, die mit Verfügbarkeitszonen verbunden sind. Weitere Informationen finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

Verfügbarkeitszonen für Azure Firewall sind in Regionen verfügbar, die Verfügbarkeitszonen unterstützen. Weitere Informationen siehe [Regionen, die Verfügbarkeitszonen in Azure unterstützen](../availability-zones/az-region.md)

> [!NOTE]
> Verfügbarkeitszonen können nur während der Bereitstellung konfiguriert werden. Für eine vorhandene Firewall können keine Verfügbarkeitszonen konfiguriert werden.

Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?](../availability-zones/az-overview.md).

## <a name="unrestricted-cloud-scalability"></a>Uneingeschränkte Cloudskalierbarkeit

Azure Firewall kann entsprechend Ihren Anforderungen aufskaliert werden, um einem sich ändernden Netzwerkdatenverkehr zu entsprechen, sodass Sie nicht für Ihre Spitzenlasten budgetieren müssen.

## <a name="application-fqdn-filtering-rules"></a>FQDN-Anwendungsfilterregeln

Sie können den ausgehenden HTTP/S-Datenverkehr oder Azure SQL-Datenverkehr auf eine angegebene Liste vollqualifizierter Domänennamen (FQDNs) einschließlich Platzhalter beschränken. Dieses Feature erfordert keine TLS-Terminierung.

## <a name="network-traffic-filtering-rules"></a>Filterregeln für den Netzwerkdatenverkehr

Sie können Netzwerkfilterregeln zum *Zulassen* oder *Verweigern* nach Quell- und Ziel-IP-Adresse, Port und Protokoll zentral erstellen. Azure Firewall ist vollständig zustandsbehaftet, sodass zwischen legitimen Paketen für verschiedene Arten von Verbindungen unterschieden werden kann. Regeln werden übergreifend für mehrere Abonnements und virtuelle Netzwerke erzwungen und protokolliert.

Azure Firewall unterstützt die zustandsbehaftete Filterung von Layer 3- und Layer 4-Netzwerkprotokollen. Layer 3-IP-Protokolle können gefiltert werden, indem Sie in der Netzwerkregel ein **Beliebiges** Protokoll und dann den Platzhalter **\*** für den Port auswählen.

## <a name="fqdn-tags"></a>FQDN-Tags

[FQDN-Tags](fqdn-tags.md) erleichtern das Zulassen des Netzwerkdatenverkehrs von bekannten Azure-Diensten durch die Firewall. Angenommen, Sie möchten Netzwerkdatenverkehr von Windows Update durch die Firewall zulassen. Sie erstellen eine entsprechende Anwendungsregel, und schließen das Windows Update-Tag ein. Jetzt kann der Netzwerkdatenverkehr von Windows Update durch Ihre Firewall fließen.

## <a name="service-tags"></a>Diensttags

Ein [Diensttag](service-tags.md) steht für eine Gruppe von IP-Adresspräfixen und soll die Komplexität bei der Erstellung von Sicherheitsregeln verringern. Sie können weder ein eigenes Diensttag erstellen noch angeben, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

## <a name="threat-intelligence"></a>Bedrohungsanalyse

Das Filtern auf Basis von [Threat Intelligence](threat-intel.md) kann für Ihre Firewall aktiviert werden, damit diese Sie vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

## <a name="dns-proxy"></a>DNS-Proxy

Wenn der DNS-Proxy aktiviert ist, kann Azure Firewall DNS-Abfrage von einem oder mehreren virtuellen Netzwerken verarbeiten und an den gewünschten DNS-Server weiterleiten. Diese Funktion ist für eine zuverlässige FQDN-Filterung in Netzwerkregeln unerlässlich. Sie können den DNS-Proxy in den Einstellungen von Azure Firewall und Firewall-Richtlinien aktivieren. Weitere Informationen zum DNS-Proxy finden Sie unter [Azure Firewall DNS-Einstellungen](dns-settings.md).

## <a name="custom-dns"></a>Benutzerdefinierter DNS

Mit Custom DNS können Sie Azure Firewall so konfigurieren, dass Ihr eigener DNS-Server verwendet wird, während gleichzeitig sichergestellt wird, dass die ausgehenden Abhängigkeiten der Firewall weiterhin über Azure DNS aufgelöst werden. Sie können einen einzelnen DNS-Server oder mehrere Server in den DNS-Einstellungen von Azure Firewall und Firewall-Richtlinien konfigurieren. Weitere Informationen über benutzerdefiniertes DNS finden Sie unter [Azure Firewall DNS-Einstellungen](dns-settings.md).

Azure Firewall kann Namen auch über Azure Private DNS auflösen. Das virtuelle Netzwerk, in dem sich die Azure Firewall befindet, muss mit der Azure Private Zone verbunden sein. Weitere Informationen finden Sie unter [Verwendung von Azure Firewall als DNS-Forwarder mit Private Link](https://github.com/adstuart/azure-privatelink-dns-azurefirewall).

## <a name="fqdn-in-network-rules"></a>FQDN in Netzwerkregeln

Sie können vollständig qualifizierte Domänennamen (FQDNs) in Netzwerkregeln verwenden, die auf der DNS-Auflösung in Azure Firewall und Firewall-Richtlinien basieren. 

Die in Ihren Regelsammlungen angegebenen FQDNs werden anhand der DNS-Einstellungen Ihrer Firewall in IP-Adressen übersetzt. Mit dieser Funktion können Sie ausgehenden Datenverkehr anhand von FQDNs mit beliebigen TCP/UDP-Protokollen filtern (einschließlich NTP, SSH, RDP und mehr). Da diese Funktion auf der DNS-Auflösung basiert, wird dringend empfohlen, den DNS-Proxy zu aktivieren, um sicherzustellen, dass die Namensauflösung mit Ihren geschützten virtuellen Maschinen und der Firewall konsistent ist.

## <a name="deploy-azure-firewall-without-public-ip-address-in-forced-tunnel-mode"></a>Azure Firewall ohne öffentliche IP-Adresse im Modus "Erzwungener Tunnel" einrichten

Der Azure Firewall-Dienst benötigt für den Betrieb eine öffentliche IP-Adresse. Auch wenn sie sicher sind, ziehen es einige Einrichtungen vor, eine öffentliche IP-Adresse nicht direkt ins Internet zu stellen. 

In solchen Fällen können Sie Azure Firewall im Modus Erzwungener Tunnel einsetzen. Mit dieser Konfiguration wird eine Verwaltungs-NIC erstellt, die von der Azure Firewall für ihre Operationen verwendet wird. Das Tenant Datapath-Netzwerk kann ohne öffentliche IP-Adresse konfiguriert werden, und der Internet-Datenverkehr kann zu einer anderen Firewall getunnelt oder vollständig blockiert werden.

Der erzwungene Tunnelmodus kann während der Laufzeit nicht konfiguriert werden. Sie können die Firewall entweder neu bereitstellen oder die Stopp- und Startfunktion verwenden, um eine vorhandene Azure Firewall im erzwungenen Tunnelmodus neu zu konfigurieren. Firewalls, die in Secure Hubs eingesetzt werden, werden immer im Modus Erzwungener Tunnel eingesetzt.

## <a name="outbound-snat-support"></a>SNAT-Unterstützung für ausgehenden Datenverkehr

Alle IP-Adressen für ausgehenden Datenverkehr des virtuellen Netzwerks werden in die öffentliche IP-Adresse der Azure Firewall übersetzt (Source Network Address Translation). Sie können Datenverkehr aus Ihrem virtuellen Netzwerk an Remoteziele im Internet identifizieren und zulassen. Azure Firewall verfügt nicht über SNAT, wenn die Ziel-IP ein privater IP-Bereich gemäß [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) ist. 

Wenn Ihre Organisation einen öffentlichen IP-Adressbereich für private Netzwerke verwendet, leitet Azure Firewall den Datenverkehr per SNAT an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet weiter. Sie können Azure Firewall so konfigurieren, dass Ihr öffentlicher IP-Adressbereich **nicht** per SNAT weitergeleitet wird. Weitere Informationen finden Sie unter [Azure Firewall SNAT – private Adressbereiche](snat-private-range.md).

Sie können die SNAT-Port Auslastung in Azure Firewall-Metriken überwachen. Weitere Informationen finden Sie in der [Dokumentation zu Firewall-Protokollen und -Metriken](logs-and-metrics.md#metrics)in unserer Empfehlung zur Verwendung von SNAT-Ports.

## <a name="inbound-dnat-support"></a>DNAT-Unterstützung für eingehenden Datenverkehr

Der eingehende Internet-Netzwerkdatenverkehr zur öffentlichen IP-Adresse Ihrer Firewall wird in die privaten IP-Adressen in Ihren virtuellen Netzwerken übersetzt (Ziel-Netzwerkadressübersetzung, DNAT) und gefiltert.

## <a name="multiple-public-ip-addresses"></a>Mehrere öffentliche IP-Adressen

Sie können Ihrer Firewall [mehrere öffentliche IP-Adressen](deploy-multi-public-ip-powershell.md) (bis zu 250) zuordnen.

Dies ermöglicht die folgenden Szenarien:

- **DNAT:** Sie können mehrere Standardportinstanzen auf Ihre Back-End-Server übersetzen. Wenn Sie beispielsweise über zwei öffentliche IP-Adressen verfügen, können Sie den TCP-Port 3389 (RDP) für beide IP-Adressen übersetzen.
- **SNAT:** Für ausgehende SNAT-Verbindungen stehen mehr Ports zur Verfügung, sodass die Gefahr einer Überlastung des SNAT-Ports verringert wird. Aktuell wählt Azure Firewall die öffentliche IP-Quelladresse für eine Verbindung nach dem Zufallsprinzip aus. Wenn Sie in Ihrem Netzwerk über eine nachgeschaltete Filterung verfügen, müssen Sie alle öffentlichen IP-Adressen zulassen, die mit Ihrer Firewall verbunden sind. Verwenden Sie ggf. ein [Präfix für öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-address-prefix.md), um diese Konfiguration zu vereinfachen.

## <a name="azure-monitor-logging"></a>Azure Monitor-Protokollierung

Alle Ereignisse sind in Azure Monitor integriert, sodass Sie Protokolle in einem Speicherkonto archivieren sowie Ereignisse an Ihren Event Hub streamen oder an Azure Monitor-Protokolle senden können. Azure Monitor-Protokollbeispiele finden Sie unter [Azure Monitor-Protokolle für Azure Firewall](./firewall-workbook.md).

Weitere Informationen finden Sie im [Tutorial: Überwachen von Azure Firewall-Protokollen und -Metriken](./firewall-diagnostics.md). 

Azure Firewall Workbook bietet einen flexiblen Bereich für die Azure Firewall-Datenanalyse. Sie können damit umfangreiche visuelle Berichte innerhalb des Azure-Portals erstellen. Weitere Informationen finden Sie unter [Überwachen von Protokollen mit Azure Firewall Workbook](firewall-workbook.md).

## <a name="forced-tunneling"></a>Tunnelerzwingung

Sie können Azure Firewall so konfigurieren, dass der gesamte Internetdatenverkehr an den festgelegten nächsten Hop weitergeleitet wird, statt dass er direkt ins Internet verläuft. So verfügen Sie vielleicht beispielsweise über eine lokale Edgefirewall oder ein anderes virtuelles Netzwerkgerät (Network Virtual Appliance, NVA), die bzw. das den Netzwerkverkehr erst verarbeitet, bevor er ans Internet übergeben wird. Weitere Informationen finden Sie unter [Azure Firewall-Tunnelerzwingung](forced-tunneling.md).

## <a name="web-categories"></a>Webkategorien

Mithilfe von Webkategorien können Administratoren den Benutzerzugriff auf Websitekategorien, z. B. Glücksspiel- oder Social Media-Websites, zulassen oder verweigern. Webkategorien sind in Azure Firewall Standard enthalten. Azure Firewall Premium bietet jedoch feinere Anpassungsmöglichkeiten. Im Gegensatz zu den Funktionen für Webkategorien in der Standard-SKU, bei denen der Abgleich der Kategorie anhand des FQDN erfolgt, werden die Kategorien bei der Premium-SKU anhand der gesamten URL abgeglichen (sowohl HTTP- als auch HTTPS-Datenverkehr). Weitere Informationen zu Azure Firewall Premium finden Sie unter [Features von Azure Firewall Premium](premium-features.md).

Wenn Azure Firewall z. B. eine HTTPS-Anforderung für `www.google.com/news` abfängt, wird die folgende Kategorisierung erwartet: 

- Firewall Standard: Nur der Teil mit dem vollqualifizierten Domänennamen (FQDN) wird untersucht, und `www.google.com` wird als *Suchmaschine* kategorisiert. 

- Firewall Premium: Die gesamte URL wird untersucht, sodass `www.google.com/news` als *News* kategorisiert wird.

Die Kategorien werden basierend auf dem Schweregrad in **Haftung**, **Hohe Bandbreite**, **Geschäftliche Nutzung**, **Produktivitätsverlust**, **Allgemeines Surfen** und **Nicht kategorisiert** unterteilt.

### <a name="category-exceptions"></a>Kategorieausnahmen

Sie können Ausnahmen für Ihre Webkategorisierungsregeln erstellen. Erstellen Sie innerhalb der Regelsammlungsgruppe eine separate Sammlung mit Zulassungs- oder Verweigerungsregeln mit einer höheren Priorität. Sie können beispielsweise eine Regelsammlung konfigurieren, die `www.linkedin.com` mit Priorität 100 zulässt, und eine Regelsammlung, die **Soziale Netzwerke** mit der Priorität 200 verweigert. Dadurch wird eine Ausnahme für die vordefinierte Webkategorie **Soziale Netzwerke** erstellt.



## <a name="certifications"></a>Zertifizierungen

Azure Firewall ist mit PCI (Payment Card Industry), SOC (Service Organization Controls), ISO (International Organization for Standardization) und ICSA Labs konform. Weitere Informationen finden Sie unter [Azure Firewall-Konformitätszertifizierungen](compliance-certifications.md).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Firewall Premium-Features](premium-features.md)