---
title: Netzwerk in der App Service-Umgebung
description: Details zum Netzwerk in der App Service-Umgebung.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: cdedc94e64a0646c7e4ffa8c93f082d040e440c7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524073"
---
# <a name="app-service-environment-networking"></a>Netzwerk in der App Service-Umgebung

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
> 

Die App Service-Umgebung (App Service Environment, ASE) ist eine Bereitstellung von Azure App Service für einen einzelnen Mandanten zum Hosten von Windows- und Linux-Containern, Web-Apps, API-Apps, Logik-Apps und Funktions-Apps. Wenn Sie eine ASE installieren, wählen Sie die Azure Virtual Network-Instanz aus, in der sie bereitgestellt werden soll. Der gesamte ein- und ausgehende Anwendungsdatenverkehr wird innerhalb des von Ihnen angegebenen virtuellen Netzwerks abgewickelt. Die ASE wird in einem einzelnen Subnetz in Ihrem virtuellen Netzwerk bereitgestellt. In diesem Subnetz können keine anderen Komponenten bereitgestellt werden.

## <a name="subnet-requirements"></a>Subnetzanforderungen

Das Ausgangssubnetz muss an „Microsoft.Web/hostingEnvironments“ delegiert und leer sein.

Die Größe des Subnetzes kann sich auf die Skalierungsgrenzwerte der App Service-Planinstanzen innerhalb der ASE auswirken. Es wird empfohlen, einen Adressraum vom Typ `/24` (256 Adressen) für Ihr Subnetz zu verwenden, um sicherzustellen, dass genügend Adressen für die Produktion vorhanden sind.

Um ein kleineres Subnetz zu verwenden, sollten Sie die folgenden Details der ASE und des Netzwerksetups beachten.

Jedes Subnetz verfügt über fünf Adressen, die für Verwaltungszwecke reserviert sind. Neben den Verwaltungsadressen skaliert die ASE die unterstützende Infrastruktur dynamisch und verwendet je nach Konfiguration und Auslastung zwischen vier und 27 Adressen. Die verbleibenden Adressen können für Instanzen im App Service-Plan verwendet werden. Die Mindestgröße Ihres Subnetzes ist ein Adressraum vom Typ `/27` (32 Adressen).

Wenn Ihnen die Adressen ausgehen, kann dies zur Folge haben, dass die Aufskalierung Ihrer App Service-Pläne in der ASE eingeschränkt wird oder dass es bei intensivem Datenverkehr zu längeren Wartezeiten kommt, wenn die unterstützende Infrastruktur nicht skaliert werden kann.

## <a name="addresses"></a>Adressen

Die ASE enthält bei der Erstellung folgende Netzwerkinformationen:

| Adresstyp | description |
|--------------|-------------|
| Virtuelles ASE-Netzwerk | Das virtuelle Netzwerk, in dem die ASE bereitgestellt wird |
| ASE-Subnetz | Das Subnetz, in dem die ASE bereitgestellt wird |
| Domänensuffix | Das Domänensuffix der in dieser ASE erstellten Apps |
| Virtuelle IP-Adresse | Diese Einstellung ist der VIP-Typ, der von der ASE verwendet wird. Die beiden möglichen Werte lauten „intern“ und „extern“. |
| Adresse für eingehenden Datenverkehr | Die eingehende Adresse ist die Adresse, über die Ihre Apps in dieser ASE aufgerufen werden. Wenn Sie über eine interne VIP verfügen, handelt es sich um eine Adresse in Ihrem ASE-Subnetz. Wenn die Adresse extern ist, handelt es sich um eine öffentliche Adresse. |
| Ausgehende Standardadressen | Die Apps in dieser ASE verwenden diese Adresse standardmäßig, wenn sie ausgehende Aufrufe an das Internet senden. |

Die ASEv3 enthält Details zu den Adressen, die von der ASE im Teil **IP-Adressen** des ASE-Portals verwendet werden.

![ASE-Adressen-Benutzeroberfläche](./media/networking/networking-ip-addresses.png)

Wenn Sie Ihre App Service-Pläne in Ihrer ASE hochskalieren, werden mehr Adressen aus Ihrem ASE-Subnetz verwendet. Die Anzahl der verwendeten Adressen hängt von der Anzahl ihrer App Service-Planinstanzen und dem Datenverkehr ab, den Ihre ASE empfängt. Apps in der ASE verfügen über keine dedizierten Adressen im ASE-Subnetz. Die Adressen, die von einer App im ASE-Subnetz verwendet werden, ändern sich im Laufe der Zeit.

## <a name="ports-and-network-restrictions"></a>Ports und Netzwerkeinschränkungen

Damit Ihre App Datenverkehr empfangen kann, müssen Sie sicherstellen, dass die Regeln für eingehende Netzwerksicherheitsgruppen (NSGs) es dem ASE-Subnetz ermöglichen, Datenverkehr von den erforderlichen Ports zu empfangen. Neben den Ports, über die Sie Datenverkehr empfangen möchten, sollten Sie sicherstellen, dass Azure Load Balancer am Port 80 eine Verbindung mit dem ASE-Subnetz herstellen kann. Dieser wird für interne VM-Integritätsprüfungen verwendet. Datenverkehr, der am Port 80 vom virtuellen Netzwerk an Ihr ASE-Subnetz gesendet wird, kann weiterhin gesteuert werden.

Es wird allgemein empfohlen, die folgende NSG-Eingangsregel zu konfigurieren:

|Port|Quelle|Destination|
|-|-|-|
|80, 443|VirtualNetwork|ASE-Subnetzbereich|

Für die Betriebsbereitschaft von ASE ist mindestens Folgendes erforderlich:

|Port|Quelle|Destination|
|-|-|-|
|80|AzureLoadBalancer|ASE-Subnetzbereich|

Wenn Sie die erforderliche Mindestregel verwenden, benötigen Sie ggf. eine oder mehrere Regeln für den Anwendungsdatenverkehr, und bei Verwendung einer der Bereitstellungs- oder Debugoptionen müssen Sie diesen Datenverkehr auch für das ASE-Subnetz zulassen. Bei der Quelle dieser Regeln kann es sich um „VirtualNetwork“ oder um einzelne oder mehrere spezifische Client-IP-Adressen oder IP-Adressbereiche handeln. Das Ziel ist immer der ASE-Subnetzbereich.

Die normalen App-Zugriffsports sind:

|Zweck|Ports|
|-|-|
|HTTP/HTTPS|80, 443|
|FTP/FTPS|21, 990, 10001-10020|
|Remotedebuggen in Visual Studio|4022, 4024, 4026|
|Web Deploy-Dienst|8172|

## <a name="network-routing"></a>Netzwerkrouting

Sie können Routingtabellen (UDRs) ohne Einschränkung festlegen. Sie können erzwingen, dass der gesamte ausgehende Anwendungsdatenverkehr von Ihrer ASE zu einem ausgehenden Firewallgerät wie Azure Firewall getunnelt wird, und sich nicht um andere Abhängigkeiten Ihrer Anwendung kümmern zu müssen. Sie können WAF-Geräte, z. B. eine Application Gateway-Ressource, vor eingehendem Datenverkehr an Ihre ASE platzieren, um bestimmte Apps in dieser ASE verfügbar zu machen. Wenn Sie die Adresse für ausgehenden Datenverkehr Ihrer Anwendungen in einer ASE anpassen möchten, können Sie Ihrem ASE-Subnetz eine NAT Gateway-Instanz hinzufügen.

## <a name="dns"></a>DNS

In den folgenden Abschnitten werden die DNS-Aspekte sowie die Konfiguration für ein- und ausgehenden Datenverkehr Ihrer ASE beschrieben.

### <a name="dns-configuration-to-your-ase"></a>DNS-Konfiguration für ASE

Wenn Ihre ASE mit einer externen VIP erstellt wird, werden Ihre Apps automatisch in ein öffentliches DNS eingebunden. Wenn Ihre ASE mit einer internen VIP erstellt wird, müssen Sie möglicherweise das DNS dafür konfigurieren. Wenn Sie die automatische Konfiguration von Azure DNS Private Zones während der ASE-Erstellung ausgewählt haben, wird das DNS in Ihrem virtuellen ASE-Netzwerk konfiguriert. Wenn Sie die manuelle Konfiguration für das DNS ausgewählt haben, müssen Sie entweder Ihren eigenen DNS-Server verwenden oder es in den privaten Zonen von Azure DNS konfigurieren. Navigieren Sie zur Benutzeroberfläche **ASE Portal > IP Adressen**, um die Adressen für den eingehenden Datenverkehr Ihrer ASE zu finden. 

Wenn Sie einen eigenen DNS-Server verwenden möchten, müssen die folgenden Einträge hinzugefügt werden:

1. Erstellen Sie eine Zone für `<ASE-name>.appserviceenvironment.net`.
1. Erstellen Sie in dieser Zone einen A-Eintrag, der auf * in der IP-Adresse für den eingehenden Datenverkehr verweist, die von Ihrer ASE verwendet wird.
1. Erstellen Sie in dieser Zone einen A-Eintrag, der auf @ in der IP-Adresse für den eingehenden Datenverkehr verweist, die von Ihrer ASE verwendet wird.
1. Erstellen Sie eine Zone in `<ASE-name>.appserviceenvironment.net`, und nennen Sie sie „scm“.
1. Erstellen Sie in der Zone „scm“ einen A-Eintrag, durch den „*“ auf die vom privaten ASE-Endpunkt verwendete IP-Adresse verwiesen wird.

So konfigurieren Sie DNS in privaten Azure DNS-Zonen

1. Erstellen Sie eine private Azure DNS-Zone mit dem Namen `<ASE-name>.appserviceenvironment.net`.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die IP-Adresse für den eingehenden Datenverkehr verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die IP-Adresse für den eingehenden Datenverkehr verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der *.scm auf die IP-Adresse für den eingehenden Datenverkehr verweist.

Zusätzlich zu der Standarddomäne, die beim Erstellen einer App bereitgestellt wird, können Sie Ihrer App auch eine benutzerdefinierte Domäne hinzufügen. Sie können einen benutzerdefinierten Domänennamen ohne jegliche Validierung für Ihre Apps in einer ILB-ASE festlegen. Wenn Sie benutzerdefinierte Domänen verwenden, müssen Sie darauf achten, dass DNS-Einträge für sie konfiguriert sind. Sie können die oben angegebenen Schritte ausführen, um DNS-Zonen und -Einträge für einen benutzerdefinierten Domänennamen zu konfigurieren, indem Sie den Namen der Standarddomäne durch den Namen der benutzerdefinierten Domäne ersetzen. Der benutzerdefinierte Domänenname funktioniert für App-Anforderungen, aber nicht für die SCM-Site. Die SCM-Site ist nur unter *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* verfügbar.

### <a name="dns-configuration-from-your-ase"></a>DNS-Konfiguration über die ASE

Die Apps in Ihrer ASE verwenden das DNS, mit dem Ihr virtuelles Netzwerk konfiguriert ist. Wenn Sie möchten, dass einige Apps einen anderen DNS-Server verwenden als den, mit dem Ihr virtuelles Netzwerk konfiguriert ist, können Sie diesen mit den App-Einstellungen „WEBSITE_DNS_SERVER“ und „WEBSITE_DNS_ALT_SERVER“ manuell pro App festlegen. Die App-Einstellung WEBSITE_DNS_ALT_SERVER konfiguriert den sekundären DNS-Server. Der sekundäre DNS-Server wird nur verwendet, wenn keine Antwort vom primären DNS-Server vorliegt.

## <a name="limitations"></a>Einschränkungen

Die ASE kann zwar im virtuellen Netzwerk eines Kunden bereitgestellt werden, es gibt jedoch einige Netzwerkfeatures, die in der ASE nicht verfügbar sind:

* Senden von SMTP-Datenverkehr. Sie können weiterhin Warnungen per E-Mail auslösen, Ihre App kann jedoch keinen ausgehenden Datenverkehr an Port 25 senden.
* Verwenden von Network Watcher oder NSG-Flow zum Überwachen des ausgehenden Datenverkehrs

## <a name="more-resources"></a>Weitere Ressourcen

- [Referenz zu Umgebungsvariablen und App-Einstellungen](../reference-app-settings.md)