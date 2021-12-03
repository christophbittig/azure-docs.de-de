---
title: Übersicht über die App Service-Umgebung
description: Übersicht über die Azure App Service-Umgebung
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.custom: references_regions
ms.openlocfilehash: 7530925ac23d8b6a25d5d74cdf5a6531cddb0edf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524016"
---
# <a name="app-service-environment-overview"></a>Übersicht über die App Service-Umgebung

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
> 

Die Azure App Service-Umgebung ist ein Feature von Azure App Service, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Apps mit umfangreicher Skalierung bereitstellt. Über diese Funktion können folgende Elemente gehostet werden:

- Windows-Web-Apps
- Linux-Web-Apps
- Docker-Container (Windows und Linux)
- Functions
- Logic Apps (Standard)

App Service-Umgebungen (App Service Environments, ASEs) sind ideal geeignet für Anwendungsworkloads mit folgenden Anforderungen:

- Großes Ausmaß.
- Isolierung und sicherer Netzwerkzugriff
- Hohe Speicherauslastung
- Viele Anforderungen pro Sekunde (Requests Per Second, RPS). Sie können mehrere ASEs innerhalb einer einzelnen Azure-Region oder über mehrere Azure-Regionen verteilt einrichten. Durch diese Flexibilität eignet sich eine ASE hervorragend für die horizontale Skalierung zustandsloser Anwendungen mit vielen Anforderungen pro Sekunde (RPS).

ASEs hosten Anwendungen von nur einem Kunden in einem seiner virtuellen Netzwerke. Kunden haben präzise Kontrolle über den eingehenden und ausgehenden Netzwerkdatenverkehr der Anwendung. Anwendungen können schnelle, sichere Verbindungen über VPNs mit lokalen Unternehmensressourcen einrichten.

## <a name="usage-scenarios"></a>Verwendungsszenarios

Die App Service-Umgebung umfasst viele Anwendungsfälle, einschließlich:

- Interne Branchenanwendungen
- Anwendungen, die mehr als 30 App Service-Planinstanzen benötigen
- Ein System mit einem Mandanten, um interne Compliance- oder Sicherheitsanforderungen zu erfüllen
- Netzwerkisoliertes Hosten von Anwendungen
- Multi-Tier-Anwendungen

Es gibt viele Netzwerkfeatures, mit denen Apps im mehrinstanzenfähigen App Service netzwerkisolierte Ressourcen erreichen oder selbst netzwerkisoliert werden können. Diese Features werden auf Anwendungsebene aktiviert. Bei einer ASE ist keine zusätzliche Konfiguration für die Apps erforderlich, damit sie im virtuellen Netzwerk vorhanden sein können. Die Apps werden in einer netzwerkisolierten Umgebung bereitgestellt, die sich bereits in einem virtuellen Netzwerk befindet. Die ASE hostet nicht nur netzwerkisolierte Apps, sie ist auch ein System mit nur einem Mandanten. Es gibt keine anderen Kunden, die die ASE verwenden. Wenn Sie wirklich eine vollständige Isolation benötigen, können Sie Ihre ASE auch auf dedizierter Hardware bereitstellen lassen.

## <a name="dedicated-environment"></a>Dedizierte Umgebung

Die ASE ist eine Bereitstellung von Azure App Service für einen einzelnen Mandanten, die in Ihrem virtuellen Netzwerk ausgeführt wird. 

Anwendungen werden in App Service-Plänen gehostet, die in einer App Service-Umgebung erstellt werden. Der App Service-Plan ist im Wesentlichen ein Bereitstellungsprofil für einen Anwendungshost. Wenn Sie Ihren App Service-Plan aufskalieren, erstellen Sie auf jedem Host mit allen Apps in diesem App Service-Plan mehr Anwendungshosts. Eine einzelne ASEv3 kann insgesamt bis zu 200 App Service-Planinstanzen in allen App Service-Plänen zusammen haben. Ein einzelner isolierter v2-App Service-Plan kann selbst bis zu 100 Instanzen enthalten.

## <a name="virtual-network-support"></a>Unterstützung für virtuelle Netzwerke

Das ASE-Feature ist eine Bereitstellung von Azure App Service in einem einzelnen Subnetz im virtuellen Netzwerk eines Kunden. Wenn Sie eine App in einer ASE bereitstellen, wird die App über die Eingangsadresse verfügbar gemacht, die der ASE zugewiesen ist. Wenn Ihre ASE mit einer internen virtuellen IP (VIP) bereitgestellt wird, ist die Eingangsadresse für alle Apps eine Adresse im ASE-Subnetz. Wenn Ihre ASE mit einer externen VIP bereitgestellt wird, ist die Eingangsadresse eine Internetadresse, und Ihre Apps befinden sich im öffentlichem DNS.

Die Anzahl der von einer ASEv3-Instanz in ihrem Subnetz verwendeten Adressen hängt davon ab, wie viele Instanzen Ihnen für wie viel Datenverkehr zur Verfügung stehen. Es gibt Infrastrukturrollen, die abhängig von der Anzahl der App Service-Pläne und der Last automatisch skaliert werden. Die empfohlene Größe für Ihr ASEv3-Subnetz ist ein `/24`-CIDR-Block mit 256 Adressen, da dieser eine bis zu ihrem Grenzwert aufskalierte ASEv3-Instanz hosten kann.

Für die Apps in einer ASE sind keine Funktionen erforderlich, die für den Zugriff auf Ressourcen in demselben virtuellen Netzwerk aktiviert sind, in dem sich die ASE befindet. Wenn das virtuelle ASE-Netzwerk mit einem anderen Netzwerk verbunden ist, können die Apps in der ASE auf Ressourcen in diesen erweiterten Netzwerken zugreifen. Der Datenverkehr kann durch die Benutzerkonfiguration im Netzwerk blockiert werden.

Die mehrinstanzenfähige Version von Azure App Service enthält zahlreiche Features, mit denen Ihre Apps eine Verbindung mit Ihren verschiedenen Netzwerken herstellen können. Mit diesen Netzwerkfeatures können Ihre Apps so agieren, als würden sie in einem virtuellen Netzwerk bereitgestellt. Die Apps in einer ASEv3-Instanz benötigen keine Konfiguration im virtuellen Netzwerk. Die Verwendung einer ASE hat gegenüber dem mehrinstanzenfähigen Dienst den Vorteil, dass alle Steuerungen des Netzwerkzugriffs auf die gehosteten ASE-Apps außerhalb der Anwendungskonfiguration ausgeführt werden. Im mehrinstanzenfähigen Dienst müssen Sie die Features für die Apps auf App-Basis aktivieren und RBAC oder Richtlinien verwenden, um Konfigurationsänderungen zu verhindern.

## <a name="feature-differences"></a>Featureunterschiede

ASEv3 unterscheidet sich in einigen Punkten von früheren ASE-Versionen. Für ASEv3 gilt:

- Es gibt keine Netzwerkabhängigkeiten im virtuellen Netzwerk des Kunden. Sie können den gesamten eingehenden und ausgehenden Datenverkehr nach Bedarf schützen. Ausgehender Datenverkehr kann auch nach Bedarf weitergeleitet werden. 
- Die für Zonenredundanz aktivierte Bereitstellung ist möglich. Zonenredundanz kann nur während der ASEv3-Erstellung und nur in Regionen festgelegt werden, in denen alle ASEv3-Abhängigkeiten zonenredundant sind. 
- Die Bereitstellung für eine dedizierte Hostgruppe ist möglich. Hostgruppenbereitstellungen sind nicht zonenredundant. 
- Die Skalierung ist viel schneller als mit ASEv2. Die Skalierung erfolgt zwar nicht sofort wie im mehrinstanzenfähigen Dienst, ist aber viel schneller.
- Front-End-Skalierungsanpassungen sind nicht mehr erforderlich. Die ASEv3-Front-Ends werden automatisch skaliert, um die Anforderungen zu erfüllen, und werden auf besseren Hosts bereitgestellt. 
- Die Skalierung blockiert nicht mehr andere Skalierungsvorgänge innerhalb der ASEv3-Instanz. Für eine Kombination aus Betriebssystem und Größe kann nur ein einziger Skalierungsvorgang wirksam sein. Während Ihr Windows Small- App Service-Plan skaliert wird, könnten Sie z. B. einen Skalierungsvorgang starten, der gleichzeitig auf einem Windows Medium-System oder einem beliebigen anderen als einem Windows Small-System ausgeführt wird. 
- Apps in einer internen VIP-ASEv3 können über globales Peering erreicht werden. Der Zugriff über globales Peering war in früheren Versionen nicht möglich.

Einige in früheren Versionen der ASE verfügbare Features sind in ASEv3 nicht verfügbar. Folgendes ist in ASEv3 nicht möglich:

- Senden von SMTP-Datenverkehr. Sie können weiterhin Warnungen per E-Mail auslösen, Ihre App kann jedoch keinen ausgehenden Datenverkehr auf Port 25 senden.
- Bereitstellen Ihrer Apps mit FTP
- Verwenden des Remotedebuggens mit Ihren Apps
- Upgrade noch von ASEv2
- Überwachen Ihres Datenverkehrs mit Network Watcher oder NSG-Flow
- Konfigurieren einer IP-basierten TLS/SSL-Bindung mit Ihren Apps
- Konfigurieren des Suffixes einer benutzerdefinierten Domäne

## <a name="pricing"></a>Preise

Bei ASEv3 gibt es je nach Art der ASE-Bereitstellung ein anderes Preismodell. Es gibt drei verschiedene Preismodelle:

- **ASEv3**: Wenn die ASE leer ist, fällt eine Gebühr an, als ob Sie eine Instanz von Windows I1v2 hätten. Die Gebühr für eine Instanz ist keine additive Gebühr, Sie wird jedoch nur angewendet, wenn die ASE leer ist.
- **Zonenredundante ASEv3**: Es werden mindestens neun Instanzen in Rechnung gestellt. Wenn Sie über neun oder mehr App Service Planinstanzen verfügen, fällt keine zusätzliche Gebühr für die Unterstützung von Verfügbarkeitszonen an. Wenn die zonenredundante ASE weniger als neun Instanzen (beliebiger Größe) in App Service-Plänen enthält, wird der Unterschied zwischen neun Instanzen und der Anzahl der ausgeführten Instanzen als zusätzliche Windows I1v2-Instanzen berechnet.
- **Dedizierter Host-ASEv3**: Bei der Bereitstellung eines dedizierten Hosts werden Ihnen zwei dedizierte Hosts gemäß unserer Preisgestaltung bei der ASEv3-Erstellung in Rechnung gestellt. Bei der Skalierung wird dann ein kleiner Prozentsatz des isolierten V2-Tarifs pro Core berechnet.

Die Preise für reservierte Instanzen für isolierte V2 sind verfügbar und werden unter [Anwenden von Reservierungsrabatten auf Azure App Service](../../cost-management-billing/reservations/reservation-discount-app-service.md) beschrieben. Die Preisgestaltung, zusammen mit der Preisgestaltung für reservierte Instanzen, ist bei der [App Service Preisgestaltung](https://azure.microsoft.com/pricing/details/app-service/windows/) unter dem **Plan für isolierte V2** verfügbar.

## <a name="regions"></a>Regions

Die ASEv3 ist in den folgenden Regionen verfügbar.

|Normale und dedizierte ASEv3-Regionen|AZ-ASEv3-Regionen|
|---------------------------------------|------------------|
|Australien (Osten)|Australien (Osten)|
|Australien, Südosten|Brasilien Süd|
|Brasilien Süd|Kanada, Mitte|
|Kanada, Mitte|USA (Mitte)|
|Indien, Mitte|East US|
|USA (Mitte)|USA (Ost) 2|
|Asien, Osten|Frankreich, Mitte|
|East US|Deutschland, Westen-Mitte|
|USA (Ost) 2|Japan, Osten|
|Frankreich, Mitte|Nordeuropa|
|Deutschland, Westen-Mitte|USA Süd Mitte|
|Japan, Osten|Asien, Südosten|
|Korea, Mitte|UK, Süden|
|USA Nord Mitte|Europa, Westen|
|Nordeuropa|USA, Westen 2|
|Norwegen, Osten| |
|Südafrika, Norden| |
|USA Süd Mitte| |
|Asien, Südosten| |
|Schweiz, Norden| |
|Vereinigte Arabische Emirate, Norden| |
|UK, Süden| |
|UK, Westen| |
|USA, Westen-Mitte| |
|Europa, Westen| |
|USA (Westen)| |
|USA, Westen 2| |
|USA, Westen 3| |
|US Gov Texas| |
|US Gov Arizona| |
|US Government, Virginia| |

## <a name="app-service-environment-v2"></a>App Service-Umgebung v2

Es gibt drei Versionen der App Service-Umgebung: ASEv1, ASEv2 und ASEv3. Die oben aufgeführten Informationen basieren auf ASEv3. Weitere Informationen zu ASEv2 finden Sie unter [Einführung in die App Service-Umgebung v2](./intro.md).
