---
title: Netzwerk in der App Service-Umgebung
description: Details zum Netzwerk in der App Service-Umgebung.
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89a14dc204e10231a134477650081396fc8e433f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345811"
---
# <a name="app-service-environment-networking"></a>Netzwerk in der App Service-Umgebung

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
> 


Die App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen Mandanten, die Web-Apps, API-Apps und Funktions-Apps hostet. Wenn Sie eine ASE installieren, wählen Sie das Azure Virtual Network (VNet) aus, in dem sie bereitgestellt werden soll. Der gesamte ein- und ausgehende Datenverkehr der Anwendungen findet innerhalb des von Ihnen angegebenen VNets statt. Die ASE wird in einem einzelnen Subnetz in Ihrem VNET bereitgestellt. In diesem Subnetz können keine anderen Komponenten bereitgestellt werden. Das Subnetz muss an Microsoft.Web/HostingEnvironments delegiert werden.

## <a name="addresses"></a>Adressen 

Die ASE enthält bei der Erstellung folgende Netzwerkinformationen:

| Adresstyp | description |
|--------------|-------------|
| Virtuelles ASE-Netzwerk | Das VNET, in dem die ASE bereitgestellt wird |
| ASE-Subnetz | Das Subnetz, in dem die ASE bereitgestellt wird |
| Domänensuffix | Das Domänensuffix der in dieser ASE erstellten Apps |
| Virtuelle IP-Adresse | Dies ist der VIP-Typ, der von der ASE verwendet wird. Die beiden möglichen Werte lauten „intern“ und „extern“. |
| Adresse für eingehenden Datenverkehr | Die eingehende Adresse ist die Adresse, über die Ihre Apps in dieser ASE aufgerufen werden. Wenn Sie über eine interne VIP verfügen, handelt es sich um eine Adresse in Ihrem ASE-Subnetz. Wenn die Adresse extern ist, handelt es sich um eine öffentliche Adresse. |
| Ausgehende Standardadressen | Die Apps in dieser ASE verwenden diese Adresse standardmäßig, wenn sie ausgehende Aufrufe an das Internet senden. |

Die ASEv3 enthält Details zu den Adressen, die von der ASE im Teil **IP-Adressen** des ASE-Portals verwendet werden.

![ASE-Adressen-Benutzeroberfläche](./media/networking/networking-ip-addresses.png)

Wenn Sie Ihre App Service-Pläne in Ihrer ASE hochskalieren, werden mehr Adressen aus Ihrem ASE-Subnetz verwendet. Die Anzahl der verwendeten Adressen hängt von der Anzahl ihrer App Service-Planinstanzen und dem Datenverkehr ab, den Ihre ASE empfängt. Apps in der ASE verfügen über keine dedizierten Adressen im ASE-Subnetz. Die Adressen, die von einer App im ASE-Subnetz verwendet werden, ändern sich im Laufe der Zeit.

## <a name="ports"></a>Ports

Die ASE empfängt den Anwendungsdatenverkehr an den Ports 80 und 443. Wenn diese Ports blockiert sind, können Sie Ihre Apps nicht erreichen. 

> [!NOTE]
> Port 80 muss von AzureLoadBalancer für das ASE-Subnetz zugelassen sein, damit der Datenverkehr zwischen dem Lastenausgleich und der ASE-Infrastruktur erhalten bleibt. Sie können weiterhin den Port 80-Datenverkehr zu Ihrer virtuellen ASE-IP-Adresse steuern.
> 

## <a name="extra-configurations"></a>Zusätzliche Konfigurationen

Sie können Netzwerksicherheitsgruppen (NSGs) und Routingtabellen (UDRs) ohne Einschränkungen festlegen. Sie können erzwingen, dass der gesamte ausgehende Datenverkehr von Ihrer ASE zu einem ausgehenden Firewallgerät wie der Azure Firewall getunnelt wird, und sich nicht um andere Abhängigkeiten Ihrer Anwendung kümmern zu müssen. Sie können WAF-Geräte, z. B. eine Application Gateway-Ressource, vor eingehendem Datenverkehr an Ihre ASE platzieren, um bestimmte Apps in dieser ASE verfügbar zu machen. Bei einer anderen dedizierten ausgehenden Internetadresse können Sie eine NAT Gateway-Ressource in Ihrer ASE verwenden. Bei Verwenden einer NAT Gateway-Ressource in der ASE muss diese für das ASE-Subnetz konfiguriert werden. 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>DNS-Konfiguration für ASE

Wenn Ihre ASE mit einer externen VIP erstellt wird, werden Ihre Apps automatisch in ein öffentliches DNS eingebunden. Wenn Ihre ASE mit einer internen VIP erstellt wird, müssen Sie möglicherweise das DNS dafür konfigurieren. Wenn Sie festgelegt haben, dass Azure DNS in privaten Zonen während der ASE-Erstellung automatisch konfiguriert werden, wird das DNS in Ihrem ASE-VNET konfiguriert. Wenn Sie die manuelle Konfiguration für das DNS ausgewählt haben, müssen Sie entweder Ihren eigenen DNS-Server verwenden oder es in den privaten Zonen von Azure DNS konfigurieren. Navigieren Sie zur Benutzeroberfläche **ASE Portal > IP Adressen**, um die Adressen für den eingehenden Datenverkehr Ihrer ASE zu finden. 

Wenn Sie einen eigenen DNS-Server verwenden möchten, müssen die folgenden Einträge hinzugefügt werden:

1. Erstellen Sie eine Zone für „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie in dieser Zone einen A-Eintrag, der auf * in der IP-Adresse für den eingehenden Datenverkehr verweist, die von Ihrer ASE verwendet wird.
1. Erstellen Sie in dieser Zone einen A-Eintrag, der auf @ in der IP-Adresse für den eingehenden Datenverkehr verweist, die von Ihrer ASE verwendet wird.
1. Erstellen Sie eine Zone namens „scm“ in „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie in der Zone „scm“ einen A-Eintrag, durch den „*“ auf die vom privaten ASE-Endpunkt verwendete IP-Adresse verwiesen wird.

So konfigurieren Sie DNS in privaten Azure DNS-Zonen

1. Erstellen Sie eine private Azure DNS-Zone mit dem Namen `<ASE-name>.appserviceenvironment.net`.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die IP-Adresse für den eingehenden Datenverkehr verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die IP-Adresse für den eingehenden Datenverkehr verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der *.scm auf die IP-Adresse für den eingehenden Datenverkehr verweist.

Die DNS-Einstellungen für Ihr ASE-Standarddomänensuffix schränken Ihre Apps nicht dahingehend ein, dass sie nur über diese Namen zugänglich sind. Sie können einen benutzerdefinierten Domänennamen ohne jegliche Validierung für Ihre Apps in einer ASE festlegen. Wenn Sie dann eine Zone namens *contoso.net* erstellen möchten, können Sie dies tun und auf die IP-Adresse für eingehenden Datenverkehr verweisen. Der benutzerdefinierte Domänenname funktioniert für App-Anforderungen, aber nicht für die SCM-Site. Die SCM-Site ist nur unter *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* verfügbar. 

#### <a name="dns-configuration-from-your-ase"></a>DNS-Konfiguration über die ASE

Die Apps in Ihrer ASE verwenden das DNS, mit dem Ihr VNet konfiguriert ist. Wenn Sie möchten, dass einige Apps einen anderen DNS-Server als den, mit dem Ihr VNet konfiguriert ist, verwenden, können Sie diesen mit den App-Einstellungen WEBSITE_DNS_SERVER und WEBSITE_DNS_ALT_SERVER manuell pro App festlegen. Die App-Einstellung WEBSITE_DNS_ALT_SERVER konfiguriert den sekundären DNS-Server. Der sekundäre DNS-Server wird nur verwendet, wenn keine Antwort vom primären DNS-Server vorliegt. 

## <a name="limitations"></a>Einschränkungen

Obwohl die ASE in einem Kunden-VNET bereitgestellt werden kann, gibt es einige Netzwerkfeatures, die in der ASE nicht verfügbar sind. 

* Senden von SMTP-Datenverkehr. Sie können weiterhin Warnungen per E-Mail auslösen, Ihre App kann jedoch keinen ausgehenden Datenverkehr an Port 25 senden.
* Verwenden von Network Watcher oder NSG-Flow zum Überwachen des ausgehenden Datenverkehrs

## <a name="more-resources"></a>Weitere Ressourcen

- [Referenz zu Umgebungsvariablen und App-Einstellungen](../reference-app-settings.md)