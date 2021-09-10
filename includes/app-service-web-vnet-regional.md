---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 399ce5e8714eb6935e3c2eac06ed44b712a14e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346821"
---
Die Verwendung der regionalen VNET-Integration ermöglicht der App Zugriff auf Folgendes:

* Ressourcen in einem VNET in derselben Region wie Ihre App.
* Ressourcen in VNETs, die mit dem VNET, in das Ihre App integriert ist, per Peering verbunden sind.
* Durch Dienstendpunkte geschützte Dienste.
* Ressourcen über Azure ExpressRoute-Verbindungen.
* Ressourcen in dem VNET, in das Sie integriert sind.
* Ressourcen über Verbindungen mit Peering, einschließlich von Azure ExpressRoute-Verbindungen.
* Private Endpunkte 

Wenn Sie VNET-Integration mit VNETs in derselben Region verwenden, können Sie die folgenden Azure-Netzwerkfunktionen nutzen:

* **Netzwerksicherheitsgruppen (NSGs)** : Sie können ausgehenden Datenverkehr mit einer NSG blockieren, die in Ihrem Integrationssubnetz platziert ist. Die Regeln für eingehenden Datenverkehr gelten nicht, da Sie die VNET-Integration nicht verwenden können, um eingehenden Zugriff auf Ihre App bereitzustellen.
* **Routingtabellen (UDRs)** : Sie können eine Routingtabelle im Integrationssubnetz platzieren, um ausgehenden Datenverkehr an beliebige gewünschte Ziele zu senden.

Standardmäßig leitet Ihre App nur RFC 1918-Datenverkehr in das VNET weiter. Wenn Sie den gesamten ausgehenden Datenverkehr an das VNET weiterleiten möchten, führen Sie die folgenden Schritte aus, um die Einstellung `WEBSITE_VNET_ROUTE_ALL` zu Ihrer App hinzuzufügen: 

1. Navigieren Sie in Ihrem App-Portal zur Benutzeroberfläche für die **Konfiguration**. Wählen Sie **Neue Anwendungseinstellung** aus.
1. Geben Sie `WEBSITE_VNET_ROUTE_ALL` im Feld **Name** ein, und geben Sie `1` im Feld **Wert** ein.

   ![Angeben der Anwendungseinstellung][4]

1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Wenn Sie den gesamten ausgehenden Datenverkehr in Ihr VNET weiterleiten, unterliegt dieser den NSGs und UDRs, die auf Ihr Integrationssubnetz angewandt werden. Wenn `WEBSITE_VNET_ROUTE_ALL` auf `1` festgelegt ist, wird von den IP-Adressen, die in Ihren App-Eigenschaften aufgeführt sind, weiterhin ausgehender Datenverkehr an öffentliche IP-Adressen gesendet – es sei denn, Sie stellen Routen bereit, die den Datenverkehr an einen anderen Ort leiten.
> 
> Die regionale VNET-Integration kann Port 25 nicht verwenden.

Es gibt einige Einschränkungen bei der Verwendung der VNET-Integration in VNETs in derselben Region:

* Sie können nicht über Verbindungen mit globalem Peering auf Ressourcen zugreifen.
* Das Feature ist in allen App Service-Skalierungseinheiten in den Tarifen „Premium V2“ und „Premium V3“ verfügbar. Es ist außerdem im Tarif „Standard“ verfügbar, jedoch nur in neueren App Service-Skalierungseinheiten. Wenn Sie sich auf einer älteren Skalierungseinheit befinden, können Sie das Feature nur aus einem „Premium V2“-App Service-Plan verwenden. Wenn Sie sicherstellen möchten, dass Sie das Feature in einem „Standard“-App Service-Plan verwenden können, erstellen Sie Ihre App in einem „Premium V3“-App Service-Plan. Diese Pläne werden nur für auf unseren neuesten Skalierungseinheiten unterstützt. Sie können anschließend nach Bedarf herunterskalieren.  
* Das Integrationssubnetz kann nur von einem App Service-Plan verwendet werden.
* Die Funktion kann nicht für Apps im Plan „App Service (isoliert)“ in einer App Service-Umgebung verwendet werden.
* Für das Feature ist ein nicht genutztes Subnetz vom Typ /28 oder größer in einem Azure Resource Manager-VNet erforderlich.
* Die App und das VNET müssen sich in derselben Region befinden.
* Es ist nicht möglich, ein VNET mit einer integrierten App zu löschen. Entfernen Sie die Integration, bevor Sie das VNET löschen.
* Sie können nur eine regionale VNET-Integration pro App Service-Plan verwenden. Mehrere Apps im gleichen App Service-Plan können das gleiche VNET verwenden.
* Sie können das Abonnement einer App oder eines Plans nicht ändern, solange eine App mit regionaler VNET-Integration vorhanden ist.
* Ihre App kann ohne Konfigurationsänderungen keine Adressen in Azure DNS Private Zones auflösen.

VNET-Integration hängt von der Verwendung eines dedizierten Subnetzes ab. Wenn Sie ein Subnetz bereitstellen, verliert das Azure-Subnetz von Anfang an fünf IP-Adressen. Für jede Instanz des Plans wird eine Adresse aus dem Integrationssubnetz verwendet. Wenn Sie Ihre App auf vier Instanzen skalieren, werden vier Adressen verwendet. 

Wenn Sie die Größe hoch- oder herunterskaliert haben, wird der erforderliche Adressraum für einen kurzen Zeitraum verdoppelt. Dies wirkt sich auf die tatsächlichen, verfügbaren unterstützten Instanzen für eine bestimmte Subnetzgröße aus. Die folgende Tabelle zeigt sowohl die maximal verfügbaren Adressen pro CIDR-Block als auch die Auswirkungen auf die horizontale Skalierung:

| CIDR-Blockgröße | Maximal verfügbare Adressen | Maximale horizontale Skalierung (Instanzen)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Geht davon aus, dass irgendwann ein Hoch- oder Herunterskalieren der Größe oder SKU erforderlich ist. 

Da die Subnetzgröße nach der Zuweisung nicht mehr geändert werden kann, verwenden Sie ein Subnetz, das für die Aufnahme jedweder Skalierung Ihrer App groß genug ist. Um jegliche Probleme mit der Subnetzkapazität zu vermeiden, ist die empfohlene Größe /26 mit 64 Adressen.  

Wenn Ihre Apps in einem anderen Plan ein VNET erreichen sollen, das bereits mit Apps in einem anderen Plan verbunden ist, müssen Sie ein anderes Subnetz als das von der bereits vorhandenen VNET-Integration verwendete auswählen.

Das Feature wird für Windows- und Linux-Apps vollständig unterstützt, einschließlich [benutzerdefinierten Containern](../articles/app-service/quickstart-custom-container.md). Das Verhalten ist in Windows- und Linux-Apps gleich.

### <a name="service-endpoints"></a>Dienstendpunkte

Mit der regionalen VNET-Integration können Sie Azure-Dienste erreichen, die mit Dienstendpunkten geschützt sind. Gehen Sie wie folgt vor, um auf einen durch einen Dienstendpunkt gesicherten Dienst zuzugreifen:

1. Konfigurieren Sie die Integration des regionalen VNET in Ihrer Web-App, um eine Verbindung mit einem bestimmten Subnetz für die Integration herzustellen.
1. Wechseln Sie zum Zieldienst, und konfigurieren Sie Dienstendpunkte für das Integrationssubnetz.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Mit Netzwerksicherheitsgruppen können Sie eingehenden und ausgehenden Datenverkehr an bzw. von Ressourcen in einem VNET blockieren. Eine App mit regionaler VNET-Integration kann eine [Netzwerksicherheitsgruppe][VNETnsg] verwenden, um ausgehenden Datenverkehr an Ressourcen in Ihrem VNET oder im Internet zu blockieren. Zum Blockieren von Datenverkehr an öffentliche Adressen muss die Anwendungseinstellung `WEBSITE_VNET_ROUTE_ALL` auf `1` festgelegt werden. Die Regeln für eingehenden Datenverkehr in einer NSG gelten nicht für Ihre App, weil sich die VNET-Integration nur auf ausgehenden Datenverkehr von Ihrer App auswirkt.

Um den eingehenden Datenverkehr für Ihre App zu steuern, verwenden Sie das Feature für Zugriffsbeschränkungen. Eine NSG, die auf Ihr Integrationssubnetz angewendet wird, ist unabhängig von den Routen wirksam, die auf Ihr Integrationssubnetz angewendet werden. Wenn `WEBSITE_VNET_ROUTE_ALL` auf `1` festgelegt ist und Sie über keine Routen verfügen, die sich auf den Datenverkehr der öffentlichen Adresse in Ihrem Integrationssubnetz auswirken, unterliegt der gesamte ausgehende Datenverkehr den NSGs, die Ihrem Integrationssubnetz zugewiesen sind. Wenn `WEBSITE_VNET_ROUTE_ALL` nicht festgelegt ist, werden NSGs nur auf RFC1918-Datenverkehr angewendet.

### <a name="routes"></a>Routen

Sie können mithilfe von Routingtabellen ausgehenden Datenverkehr von Ihrer App an beliebige Ziele weiterleiten. Routingtabellen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn Sie `WEBSITE_VNET_ROUTE_ALL` auf `1` setzen, sind alle ausgehenden Aufrufe betroffen. Routen, die für Ihr Integrationssubnetz festgelegt werden, wirken sich nicht auf Antworten auf eingehende App-Anforderungen aus. Gängige Ziele können Firewallgeräte oder Gateways beinhalten.

Wenn Sie den gesamten ausgehenden Datenverkehr lokal weiterleiten möchten, können Sie eine Routingtabelle verwenden, um den gesamten ausgehenden Datenverkehr an das ExpressRoute-Gateway zu senden. Wenn Sie Datenverkehr nicht an ein Gateway weiterleiten, müssen Sie unbedingt Routen im externen Netzwerk festlegen, über die Antworten zurückgesendet werden.

BGP-Routen (Border Gateway Protocol) wirken sich ebenfalls auf den App-Datenverkehr aus. Wenn Sie über BGP-Routen von einem ExpressRoute-Gateway verfügen, ist der ausgehende Datenverkehr Ihrer App betroffen. BGP-Routen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn `WEBSITE_VNET_ROUTE_ALL` auf `1` festgelegt ist, kann der gesamte ausgehende Datenverkehr von ihren BGP-Routen betroffen sein.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Nachdem Ihre App in Ihr VNET integriert wurde, verwendet sie den DNS-Server, mit dem Ihr VNET konfiguriert ist. Standardmäßig funktioniert Ihre App nicht mit Azure DNS Private Zones. Um mit Azure DNS Private Zones zu arbeiten, müssen Sie die folgenden App-Einstellungen hinzufügen:

1. `WEBSITE_DNS_SERVER` mit Wert `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` mit Wert `1`

Mit diesen Einstellungen werden alle ausgehenden Aufrufe von Ihrer App an das VNET gesendet, und Ihre App kann auf Azure DNS Private Zones zugreifen. Mit diesen Einstellungen kann Ihre App Azure DNS verwenden, indem sie die private DNS-Zone auf Workerebene abfragt.  

### <a name="private-endpoints"></a>Private Endpunkte

Wenn Sie Aufrufe an [private Endpunkte][privateendpoints] durchführen möchten, müssen Sie sicherstellen, dass Ihre DNS-Suchen zu dem privaten Endpunkt aufgelöst werden. Sie können dieses Verhalten auf eine der folgenden Arten erzwingen: 

* Integrieren mit private Azure DNS-Zonen. Wenn Ihr VNET nicht über einen benutzerdefinierten DNS-Server verfügt, erfolgt dies automatisch.
* Verwalten des privaten Endpunkts im DNS-Server, der von Ihrer App verwendet wird. Hierzu müssen Sie die Adresse des privaten Endpunkts kennen und dann den Endpunkt, den Sie erreichen möchten, mit einem A-Eintrag auf diese Adresse verweisen lassen.
* Konfigurieren Ihres eigenen DNS-Servers zur Weiterleitung an private Azure DNS-Zonen.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
