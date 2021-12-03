---
title: Netzwerkoptionen von Azure Functions
description: Enthält eine Übersicht über alle Netzwerkoptionen, die in Azure Functions verfügbar sind.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: c954f411d14aaa0f33bf4a3423a67ae64204f932
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892848"
---
# <a name="azure-functions-networking-options"></a>Netzwerkoptionen von Azure Functions

In diesem Artikel werden die Netzwerkfunktionen beschrieben, die für die Hostingoptionen von Azure Functions zur Verfügung stehen. Alle folgenden Netzwerkoptionen geben Ihnen die Möglichkeit zum Zugreifen auf Ressourcen ohne Verwendung von Adressen, die über das Internet geroutet werden können, oder zum Einschränken des Internetzugriffs auf eine Funktions-App.

Die Hostingmodelle weisen verschiedene verfügbare Ebenen der Netzwerkisolation auf. Durch Auswahl der jeweils richtigen Ebene lassen sich Ihre Anforderungen an die Netzwerkisolation erfüllen.

Sie können Funktions-Apps auf verschiedene Arten hosten:

* Sie können unter Tarifoptionen wählen, die in einer mehrinstanzenfähigen Infrastruktur ausgeführt werden und über unterschiedliche Ebenen von Konnektivitäts- und Skalierungsoptionen für virtuelle Netzwerke verfügen:
    * Im [Verbrauchstarif](consumption-plan.md) wird als Reaktion auf Last dynamisch skaliert, und der Tarif bietet Optionen für eine minimale Netzwerkisolation.
    * Im [Premium-Tarif](functions-premium-plan.md) wird ebenfalls dynamisch skaliert, der Tarif ermöglicht aber eine umfassendere Netzwerkisolation.
    * Beim [Azure App Service-Plan](dedicated-plan.md) wird eine feste Skalierung verwendet, und er weist eine ähnliche Netzwerkisolation wie der Premium-Tarif auf.
* Sie können Funktionen in einer [App Service-Umgebung](../app-service/environment/intro.md) ausführen. Mit dieser Methode werden die Funktionen in Ihrem virtuellen Netzwerk bereitgestellt und eine umfassende Netzwerksteuerung und -isolation ermöglicht.

## <a name="matrix-of-networking-features"></a>Matrix der Netzwerkfunktionen

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Einschränkungen für eingehenden Zugriff

Mit Zugriffseinschränkungen können Sie eine nach Priorität sortierte Liste mit IP-Adressen definieren, über die der Zugriff auf Ihre App zugelassen bzw. abgelehnt wird. Die Liste kann IPv4- und IPv6-Adressen oder bestimmte Subnetze eines virtuellen Netzwerks mit [Dienstendpunkten](#use-service-endpoints) enthalten. Wenn mindestens ein Eintrag vorhanden ist, enthält die Liste am Ende einen impliziten Eintrag vom Typ „Alle ablehnen“. IP-Einschränkungen können für alle Hostingoptionen für Funktionen verwendet werden.

Zugriffseinschränkungen sind für [Premium](functions-premium-plan.md), [Verbrauch](consumption-plan.md) und [App Service](dedicated-plan.md) verfügbar.

> [!NOTE]
> Mit den geltenden Netzwerkbeschränkungen können Sie nur in Ihrem virtuellen Netzwerk bereitstellen, oder wenn Sie die IP-Adresse des Computers, mit dem Sie auf das Azure-Portal zugreifen, der Liste der sicheren Empfänger hinzugefügt haben. Sie können die Funktion jedoch weiterhin über das Portal verwalten.

Weitere Informationen finden Sie unter [Azure App Service – statische Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md).

## <a name="private-endpoint-connections"></a>Private Endpunktverbindungen

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Konfigurieren Sie zum Aufrufen anderer Dienste, die über eine Verbindung mit einem privaten Endpunkt verfügen (z. B. Speicher oder Service Bus), Ihre App so, dass sie [ausgehende Aufrufe an private Endpunkte](#private-endpoints) ausführt.

### <a name="use-service-endpoints"></a>Verwenden von Dienstendpunkten

Die Verwendung von Dienstendpunkten ermöglicht das Einschränken des Zugriffs auf ausgewählte Subnetze virtueller Azure-Netzwerke. Erstellen Sie zum Einschränken des Zugriffs auf ein bestimmtes Subnetz eine Einschränkungsregel vom Typ **Virtual Network**. Anschließend können Sie das Abonnement, das virtuelle Netzwerk und das Subnetz auswählen, für das Sie den Zugriff zulassen oder ablehnen möchten. 

Falls für das von Ihnen ausgewählte Subnetz nicht bereits Dienstendpunkte mit Microsoft.Web aktiviert sind, wird dies automatisch durchgeführt, sofern Sie nicht das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. In einem Szenario, bei dem Sie Dienstendpunkte in der App aktivieren möchten, aber nicht im Subnetz, hängt es vor allem davon ab, ob Sie über die Berechtigungen für die Aktivierung im Subnetz verfügen. 

Falls die Dienstendpunkte im Subnetz bei Ihnen von einer anderen Person aktiviert werden müssen, sollten Sie das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. Ihre App wird für Dienstendpunkte konfiguriert, weil damit zu rechnen ist, dass diese später im Subnetz aktiviert werden. 

![Screenshot: Bereich „Add IP Restriction“ (IP-Einschränkung hinzufügen) mit Auswahl des Typs „Virtual Network“](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Sie können Dienstendpunkte nicht nutzen, um den Zugriff auf Apps einzuschränken, die in einer App Service-Umgebung ausgeführt werden. Wenn Ihre App in einer App Service-Umgebung enthalten ist, können Sie den Zugriff darauf steuern, indem Sie IP-Zugriffsregeln anwenden. 

Informationen zum Einrichten von Dienstendpunkten Sie unter [Tutorial: Einrichten von privatem Websitezugriff für Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integration in ein virtuelles Netzwerk

Durch die Integration des virtuellen Netzwerks kann die Funktions-App auf Ressourcen eines virtuellen Netzwerks zugreifen.
Azure Functions unterstützt zwei Arten der Integration virtueller Netzwerke:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Bei der Integration virtueller Netzwerke in Azure Functions wird die gemeinsame Infrastruktur mit App Service-Web-Apps verwendet. Weitere Informationen zu den beiden Arten der Integration virtueller Netzwerke finden Sie unter:

* [Regionale Integration des virtuellen Netzwerks](../app-service/overview-vnet-integration.md#regional-virtual-network-integration)
* [Integration des virtuellen Netzwerks mit erforderlichem Gateway](../app-service/overview-vnet-integration.md#gateway-required-virtual-network-integration)

Informationen zum Einrichten der Integration virtueller Netzwerke finden Sie unter [Aktivieren der VNet-Integration](#enable-vnet-integration).

## <a name="enable-vnet-integration"></a>Aktivieren der VNET-Integration

1. Wechseln Sie im Portal der Funktions-App zum Blatt **Netzwerk**. Wählen Sie unter **VNET-Integration** die Option **Zum Konfigurieren hier klicken** aus.

1. Wählen Sie **VNET hinzufügen** aus.

    :::image type="content" source="./media/functions-networking-options/vnet-int-function-app.png" alt-text="Auswählen der VNET-Integration":::

1. Die Dropdownliste enthält alle virtuellen Azure Resource Manager-Netzwerke in Ihrem Abonnement in derselben Region. Wählen Sie das VNET aus, das Sie integrieren möchten.

    :::image type="content" source="./media/functions-networking-options/vnet-int-add-vnet-function-app.png" alt-text="Auswählen des VNETs":::

    * Der Premium-Plan von Functions unterstützt nur regionale VNet-Integration. Wenn sich das VNet in derselben Region befindet, erstellen Sie ein neues Subnetz, oder wählen Sie ein leeres bereits vorhandenes Subnetz aus.
    * Wenn Sie ein VNET in einer anderen Region auswählen möchten, müssen Sie über ein VNET-Gateway verfügen, für das Point-to-Site aktiviert ist. Die regionsübergreifende VNet-Integration wird nur für dedizierte Pläne unterstützt.

Während der Integration wird Ihre App neu gestartet. Wenn die Integration abgeschlossen ist, werden Details zu dem VNET angezeigt, in das Sie integriert sind. Standardmäßig ist „Route All“ (Gesamten Datenverkehr weiterleiten) aktiviert, und der ganze Datenverkehr wird an Ihr VNet geroutet.

Wenn Sie nur Ihren privaten Datenverkehr ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)-Datenverkehr) routen möchten, führen Sie die in der [App Service-Dokumentation](../app-service/overview-vnet-integration.md#application-routing) beschriebenen Schritte aus.

## <a name="regional-virtual-network-integration"></a>Regionale Integration des virtuellen Netzwerks

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

> [!NOTE]
> Wenn Sie den gesamten ausgehenden Datenverkehr in Ihr VNET weiterleiten, unterliegt dieser den NSGs und UDRs, die auf Ihr Integrationssubnetz angewandt werden. Wenn das VNet integriert ist, wird der ausgehende Datenverkehr Ihrer Funktions-App zu öffentlichen IP-Adressen immer noch von den Adressen gesendet, die in den Eigenschaften Ihrer App aufgeführt sind – es sei denn, Sie stellen Routen bereit, die den Datenverkehr an einen anderen Ort leiten.
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

## <a name="subnets"></a>Subnetze

VNET-Integration hängt von der Verwendung eines dedizierten Subnetzes ab. Wenn Sie ein Subnetz bereitstellen, verliert das Azure-Subnetz von Anfang an fünf IP-Adressen. Für jede Instanz des Plans wird eine Adresse aus dem Integrationssubnetz verwendet. Wenn Sie Ihre App auf vier Instanzen skalieren, werden vier Adressen verwendet. 

Wenn Sie die Größe hoch- oder herunterskaliert haben, wird der erforderliche Adressraum für einen kurzen Zeitraum verdoppelt. Dies wirkt sich auf die tatsächlichen, verfügbaren unterstützten Instanzen für eine bestimmte Subnetzgröße aus. Die folgende Tabelle zeigt sowohl die maximal verfügbaren Adressen pro CIDR-Block als auch die Auswirkungen auf die horizontale Skalierung:

| CIDR-Blockgröße | Maximal verfügbare Adressen | Maximale horizontale Skalierung (Instanzen)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Geht davon aus, dass irgendwann ein Hoch- oder Herunterskalieren der Größe oder SKU erforderlich ist. 

Da die Subnetzgröße nach der Zuweisung nicht mehr geändert werden kann, verwenden Sie ein Subnetz, das für die Aufnahme jedweder Skalierung Ihrer App groß genug ist. Um Probleme mit der Subnetzkapazität für Functions Premium-Pläne zu vermeiden, sollten Sie /24 mit 256 Adressen für Windows und /26 mit 64 Adressen für Linux verwenden. 

Wenn Ihre Apps in einem anderen Plan ein VNET erreichen sollen, das bereits mit Apps in einem anderen Plan verbunden ist, müssen Sie ein anderes Subnetz als das von der bereits vorhandenen VNET-Integration verwendete auswählen.

Das Feature wird für Windows- und Linux-Apps vollständig unterstützt, einschließlich [benutzerdefinierten Containern](../app-service/configure-custom-container.md). Das Verhalten ist in Windows- und Linux-Apps gleich.

### <a name="service-endpoints"></a>Dienstendpunkte

Um eine höhere Sicherheitsstufe zu gewährleisten, können Sie eine Reihe von Azure-Diensten auf ein virtuelles Netzwerk beschränken, indem Sie Dienstendpunkte verwenden. Mit der regionalen VNET-Integration kann Ihre Funktions-App Azure-Dienste erreichen, die mit Dienstendpunkten geschützt sind. Diese Konfiguration wird für alle [Pläne](functions-scale.md#networking-features) unterstützt, welche die Integration in ein virtuelles Netzwerk unterstützen. Gehen Sie wie folgt vor, um auf einen durch einen Dienstendpunkt gesicherten Dienst zuzugreifen:

1. Konfigurieren Sie die Integration des regionalen VNet in Ihre Funktions-App, um eine Verbindung mit einem bestimmten Subnetz herzustellen.
1. Wechseln Sie zum Zieldienst, und konfigurieren Sie Dienstendpunkte für das Integrationssubnetz.

Weitere Informationen finden Sie unter [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Mit Netzwerksicherheitsgruppen können Sie eingehenden und ausgehenden Datenverkehr an bzw. von Ressourcen in einem VNET blockieren. Eine App mit regionaler VNet-Integration kann eine [Netzwerksicherheitsgruppe][VNETnsg] verwenden, um ausgehenden Datenverkehr an Ressourcen in Ihrem VNet oder im Internet zu blockieren. Um Datenverkehr an öffentliche Adressen zu blockieren, muss VNet-Integration mit der Option „Route All“ (Gesamten Datenverkehr weiterleiten) aktiviert sein. Die Regeln für eingehenden Datenverkehr in einer NSG gelten nicht für Ihre App, weil sich die VNET-Integration nur auf ausgehenden Datenverkehr von Ihrer App auswirkt.

Um den eingehenden Datenverkehr für Ihre App zu steuern, verwenden Sie das Feature für Zugriffsbeschränkungen. Eine NSG, die auf Ihr Integrationssubnetz angewendet wird, ist unabhängig von den Routen wirksam, die auf Ihr Integrationssubnetz angewendet werden. Wenn VNet-Integration mit aktivierter Option „Route all“ (Gesamten Datenverkehr weiterleiten) erfolgt ist und Sie über keine Routen verfügen, die sich auf den Datenverkehr der öffentlichen Adresse in Ihrem Integrationssubnetz auswirken, unterliegt der gesamte ausgehende Datenverkehr den NSGs, die Ihrem Integrationssubnetz zugewiesen sind. Ist „Route All“ (Gesamten Datenverkehr weiterleiten) nicht aktiviert, werden NSGs nur auf RFC1918-Datenverkehr angewendet.

### <a name="routes"></a>Routen

Sie können mithilfe von Routingtabellen ausgehenden Datenverkehr von Ihrer App an beliebige Ziele weiterleiten. Routingtabellen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn „Route All“ (Gesamten Datenverkehr weiterleiten) aktiviert ist, sind alle ausgehenden Aufrufe betroffen. Wenn [Route All](../app-service/overview-vnet-integration.md#application-routing) (Gesamten Datenverkehr weiterleiten) deaktiviert ist, ist nur privater Datenverkehr (RFC1918) von Ihren Routingtabellen betroffen. Routen, die für Ihr Integrationssubnetz festgelegt werden, wirken sich nicht auf Antworten auf eingehende App-Anforderungen aus. Gängige Ziele können Firewallgeräte oder Gateways beinhalten.

Wenn Sie den gesamten ausgehenden Datenverkehr lokal weiterleiten möchten, können Sie eine Routingtabelle verwenden, um den gesamten ausgehenden Datenverkehr an das ExpressRoute-Gateway zu senden. Wenn Sie Datenverkehr nicht an ein Gateway weiterleiten, müssen Sie unbedingt Routen im externen Netzwerk festlegen, über die Antworten zurückgesendet werden.

BGP-Routen (Border Gateway Protocol) wirken sich ebenfalls auf den App-Datenverkehr aus. Wenn Sie über BGP-Routen von einem ExpressRoute-Gateway verfügen, ist der ausgehende Datenverkehr Ihrer App betroffen. BGP-Routen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn Ihre Funktions-App in ein VNet mit aktivierter Option „Route All“ (Gesamten Datenverkehr weiterleiten) integriert ist, können sich Ihre BGP-Routen auf den gesamten ausgehenden Datenverkehr auswirken.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Nachdem Ihre App in Ihr VNET integriert wurde, verwendet sie den DNS-Server, mit dem Ihr VNET konfiguriert ist. Standardmäßig funktioniert Ihre App nicht mit Azure DNS Private Zones. Um mit Azure DNS Private Zones zu arbeiten, müssen Sie die folgenden App-Einstellungen hinzufügen:

- `WEBSITE_VNET_ROUTE_ALL` mit Wert `1`

Diese Einstellung sendet alle ausgehenden Aufrufe von Ihrer App an das VNet, und Ihre App kann auf eine private Azure DNS-Zone zugreifen. Mit diesen Einstellungen kann Ihre App Azure DNS verwenden, indem sie die private DNS-Zone auf Workerebene abfragt.  

### <a name="private-endpoints"></a>Private Endpunkte

Wenn Sie Aufrufe an [private Endpunkte][privateendpoints] durchführen möchten, müssen Sie sicherstellen, dass Ihre DNS-Suchen zu dem privaten Endpunkt aufgelöst werden. Sie können dieses Verhalten auf eine der folgenden Arten erzwingen: 

* Integrieren mit private Azure DNS-Zonen. Wenn Ihr VNET nicht über einen benutzerdefinierten DNS-Server verfügt, erfolgt dies automatisch.
* Verwalten des privaten Endpunkts im DNS-Server, der von Ihrer App verwendet wird. Hierzu müssen Sie die Adresse des privaten Endpunkts kennen und dann den Endpunkt, den Sie erreichen möchten, mit einem A-Eintrag auf diese Adresse verweisen lassen.
* Konfigurieren Ihres eigenen DNS-Servers zur Weiterleitung an [private Azure DNS-Zonen](#azure-dns-private-zones).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Einschränken Ihres Speicherkontos auf ein virtuelles Netzwerk 

Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Queue- und Table Storage unterstützt. Sie können dieses Speicherkonto durch eines ersetzen, das mit Dienstendpunkten oder privaten Endpunkten geschützt ist. 

Dieses Feature wird für alle von Windows Virtual Network unterstützten SKUs im Plan „Dedicated“ (App Service) und für die Premium-Pläne unterstützt. Es wird ebenfalls mit privatem DNS für SKUs unterstützt, die Linux-VNets unterstützen. Der Verbrauchstarif und benutzerdefiniertes DNS für Linux-Pläne werden nicht unterstützt. Informationen zum Einrichten einer Funktion mit einem Speicherkonto, das auf ein privates Netzwerk beschränkt ist, finden Sie unter [Einschränken des Speicher Kontos für ein virtuelles Netzwerk](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network).

## <a name="use-key-vault-references"></a>Verwenden von Key Vault-Verweisen

Sie können mithilfe von Azure Key Vault-Verweisen Geheimnisse aus Ihrer Azure Key Vault-Instanz in Ihrer Azure Functions-Anwendung verwenden, ohne dass Codeänderungen erforderlich sind. Azure Key Vault ist ein Dienst, der eine zentralisierte Verwaltung von Geheimnissen mit voller Kontrolle über Zugriffsrichtlinien und Überprüfungsverlauf ermöglicht.

Wenn für die App die VNet-Integration konfiguriert ist, können [Key Vault-Verweise](../app-service/app-service-key-vault-references.md) zum Abrufen von Geheimnissen aus einem Tresor mit Netzwerkeinschränkung verwendet werden.

## <a name="virtual-network-triggers-non-http"></a>Trigger für virtuelle Netzwerke (nicht HTTP)

HTTP-fremde Triggerfunktionen können aktuell auf zwei Arten von einem virtuellen Netzwerk aus verwendet werden:

+ Sie können Ihre Funktions-App in einem Premium-Plan ausführen und die Triggerunterstützung für virtuelle Netzwerke aktivieren.
+ Sie können Ihre Funktions-App in einem App Service-Plan oder in einer App Service-Umgebung ausführen.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium-Plan mit Triggern für virtuelle Netzwerke

Bei Verwendung eines Premium-Plans können Sie HTTP-fremde Triggerfunktionen mit Diensten verbinden, die innerhalb eines virtuellen Netzwerks ausgeführt werden. Hierzu müssen Sie für Ihre Funktions-App die Triggerunterstützung für virtuelle Netzwerke aktivieren. Die Einstellung **Runtime Scale Monitoring** (Überwachung der Runtimeskalierung) finden Sie im [Azure-Portal](https://portal.azure.com) unter **Konfiguration** > **Einstellungen der Funktionsruntime**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Trigger für virtuelle Netzwerke können auch mithilfe des folgenden Azure CLI-Befehls aktiviert werden:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Das Aktivieren von Triggern für virtuelle Netzwerke kann sich auf die Leistung Ihrer Anwendung auswirken, da die Instanzen Ihres App Service-Plans Ihre Trigger überwachen müssen, um den Zeitpunkt einer erforderlichen Skalierung zu bestimmen. Diese Auswirkung ist wahrscheinlich nur geringfügig.

Trigger für virtuelle Netzwerke werden ab Version 2.x der Functions-Runtime unterstützt. Folgende HTTP-fremde Triggertypen werden unterstützt:

| Durchwahl | Mindestversion |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 oder höher |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 oder höher|

> [!IMPORTANT]
> Wenn Sie die Triggerunterstützung für virtuelle Netzwerke aktivieren, werden nur die in der obigen Tabelle aufgeführten Triggertypen dynamisch mit Ihrer Anwendung skaliert. Sie können zwar weiterhin Trigger verwenden, die nicht in der Tabelle aufgeführt sind, diese werden jedoch nicht über die Anzahl vorab aufgewärmter Instanzen hinaus skaliert. Die vollständige Triggerliste finden Sie unter [Trigger und Bindungen](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service-Plan und App Service-Umgebung mit Triggern für virtuelle Netzwerke

Wenn Ihre Funktions-App entweder in einem App Service-Plan oder in einer App Service-Umgebung ausgeführt wird, können Sie HTTP-fremde Triggerfunktionen verwenden. Damit Ihre Funktionen ordnungsgemäß ausgelöst werden, muss eine Verbindung mit einem virtuellen Netzwerk bestehen. Außerdem muss auf die in der Triggerverbindung definierte Ressource zugegriffen werden können.

Angenommen, Sie möchten Azure Cosmos DB so konfigurieren, dass nur Datenverkehr aus einem virtuellen Netzwerk akzeptiert wird. In diesem Fall müssen Sie Ihre Funktions-App in einem App Service-Plan bereitstellen, der die VNET-Integration mit diesem virtuellen Netzwerk ermöglicht. Die Integration ermöglicht das Auslösen einer Funktion durch diese Azure Cosmos DB-Ressource.

## <a name="hybrid-connections"></a>Hybridverbindungen

[Hybrid Connections](../azure-relay/relay-hybrid-connections-protocol.md) ist ein Feature von Azure Relay, das Sie zum Zugreifen auf Anwendungsressourcen in anderen Netzwerken verwenden können. Es ermöglicht den Zugriff von Ihrer App auf einen Anwendungsendpunkt. Sie können es nicht verwenden, um auf Ihre Anwendung zuzugreifen. Hybrid Connections steht für Funktionen unter Windows in allen Tarifen (mit Ausnahme des Verbrauchstarifs) zur Verfügung.

Bei der Verwendung in Azure Functions entspricht jede Hybridverbindung einer Kombination aus einem einzelnen TCP-Host und einem Port. Dies bedeutet, dass sich der Hybridverbindungsendpunkt in einem beliebigen Betriebssystem und einer beliebigen Anwendung befinden kann, solange der Zugriff über einen TCP-Überwachungsport erfolgt. Das Feature „Hybrid Connections“ verfügt nicht über Informationen zum Anwendungsprotokoll oder zum abzurufenden Inhalt und benötigt diese Informationen auch nicht. Es ermöglicht lediglich den Netzwerkzugriff.

Weitere Informationen finden Sie in der [App Service-Dokumentation zu Hybrid Connections](../app-service/app-service-hybrid-connections.md). Diese Konfigurationsschritte unterstützen auch Azure Functions.

>[!IMPORTANT]
> Hybrid Connections wird nur in Windows-Tarifen unterstützt. Linux wird nicht unterstützt.

## <a name="outbound-ip-restrictions"></a>IP-Einschränkungen für ausgehenden Datenverkehr

IP-Einschränkungen für ausgehenden Datenverkehr sind in einem Premium-Plan, einem App Service-Plan oder in einer App Service-Umgebung verfügbar. Sie können ausgehende Einschränkungen für das virtuelle Netzwerk konfigurieren, wo Ihre App Service-Umgebung bereitgestellt wird.

Wenn Sie eine Funktions-App in einen Premium-Tarif oder einen App Service-Plan mit einem virtuellen Netzwerk integrieren, kann die App standardmäßig weiterhin ausgehende Aufrufe ins Internet vornehmen. Durch die Integration Ihrer Funktions-App in ein VNet mit aktivierter Option „Route All“ (Gesamten Datenverkehr weiterleiten) erzwingen Sie, dass der gesamte ausgehende Datenverkehr an Ihr virtuelles Netzwerk gesendet wird, in dem Netzwerksicherheitsgruppen-Regeln zum Einschränken des Datenverkehrs verwendet werden können.

Informationen zum Steuern der ausgehenden IP-Adresse mithilfe eines virtuellen Netzwerks finden Sie unter [Tutorial: Steuern der ausgehenden IP-Adresse von Azure Functions mit einem Azure Virtual Network-NAT-Gateway](functions-how-to-use-nat-gateway.md). 

## <a name="automation"></a>Automation
Die folgenden APIs ermöglichen es Ihnen, regionale Integrationen virtueller Netzwerke programmgesteuert zu verwalten:

+ **Azure CLI**: Verwenden Sie die Befehle [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration), um Integrationen regionaler virtueller Netzwerk hinzuzufügen, aufzulisten oder zu entfernen.  
+ **ARM-Vorlagen**: Die Integration regionaler virtueller Netzwerke kann mithilfe einer Azure Resource Manager-Vorlage aktiviert werden. Ein vollständiges Beispiel finden Sie in [dieser Functions-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Netzwerk und zu Azure Functions:

* [Tutorial zum Einstieg in die Integration des virtuellen Netzwerks](./functions-create-vnet.md)
* [Häufig gestellte Fragen zu Netzwerken in Functions](./functions-networking-faq.yml)
* [Weitere Informationen zur Integration des virtuellen Netzwerks mit App Service und Functions](../app-service/overview-vnet-integration.md)
* [Weitere Informationen zu virtuellen Netzwerken in Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivieren weiterer Netzwerkfunktionen und Steuerung mit App Service-Umgebungen](../app-service/environment/intro.md)
* [Verbinden mit einzelnen lokalen Ressourcen ohne Änderungen an der Firewall mithilfe von Hybrid Connections](../app-service/app-service-hybrid-connections.md)


<!--Links-->
[VNETnsg]: ../virtual-network/network-security-groups-overview.md
[privateendpoints]: ../app-service/networking/private-endpoint.md