---
title: Planen der Azure VMware Solution-Bereitstellung
description: Erfahren Sie, wie Sie Ihre Azure VMware Solution-Bereitstellung planen.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 07/07/2021
ms.openlocfilehash: 4f0f59a01ff53646a23809efa129a154bfba81ee
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444212"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Planen der Azure VMware Solution-Bereitstellung

Die Planung Ihrer Azure VMware Solution-Bereitstellung ist entscheidend für eine erfolgreiche produktionsbereite Umgebung zum Erstellen von virtuellen Computern (VMs) und für die Migration. Während des Planungsprozesses ermitteln und erfassen Sie, was für Ihre Bereitstellung erforderlich ist. Stellen Sie beim Planen sicher, dass Sie die gesammelten Informationen dokumentieren, damit Sie während des Bereitstellungsvorgangs leicht darauf zugreifen können. Eine erfolgreiche Bereitstellung führt zu einer für die Produktion vorbereiteten Umgebung zum Erstellen von virtuellen Computern (VMs) und Durchführen der Migration.

In dieser Schrittanleitung führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Identifizieren von Azure-Abonnement, Ressourcengruppe, Region und Ressourcenname
> * Identifizieren der Größenhosts und Bestimmen der Anzahl von Clustern und Hosts
> * Anfordern eines Hostkontingents für einen berechtigten Azure-Plan
> * Identifizieren des /22 CIDR IP-Segments für die Verwaltung privater Clouds
> * Identifizieren eines einzelnen Netzwerksegments
> * Definieren des VNET-Gateways
> * Definieren der VMware HCX-Netzwerksegmente

Wenn Sie fertig sind, folgen Sie den empfohlenen nächsten Schritten am Ende, um mit dem Leitfaden für erste Schritte fortzufahren.


## <a name="identify-the-subscription"></a>Identifizieren des Abonnements

Identifizieren Sie das Abonnement, das Sie zum Bereitstellen von Azure VMware Solution verwenden möchten.  Sie können ein neues Abonnement erstellen oder ein vorhandenes Abonnement verwenden.

>[!NOTE]
>Das Abonnement muss einem Microsoft Enterprise Agreement (EA) oder einem Azure-Plan für Cloud Solution Provider (CSP) zugeordnet sein. Weitere Informationen finden Sie unter den [Berechtigungskriterien](request-host-quota-azure-vmware-solution.md#eligibility-criteria).

## <a name="identify-the-resource-group"></a>Identifizieren der Ressourcengruppe

Identifizieren Sie die Ressourcengruppe, die Sie für Ihre Azure VMware Solution-Instanz verwenden möchten.  Im Allgemeinen wird eine Ressourcengruppe speziell für Azure VMware Solution erstellt, aber Sie können auch eine vorhandene Ressourcengruppe verwenden.

## <a name="identify-the-region-or-location"></a>Identifizieren der Region oder des Standorts

Identifizieren Sie die [Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware), in der Azure VMware Solution bereitgestellt werden soll. 

## <a name="define-the-resource-name"></a>Definieren des Ressourcennamens

Der Ressourcenname ist ein benutzerfreundlicher und beschreibender Name, in dem Sie Ihre private Azure VMware Solution-Cloud angeben, z. B. **MyPrivateCloud**.

>[!IMPORTANT]
>Der Name darf maximal 40 Zeichen lang sein. Wenn der Name diesen Grenzwert überschreitet, können Sie keine öffentlichen IP-Adressen für die Verwendung mit der privaten Cloud erstellen. 

## <a name="identify-the-size-hosts"></a>Identifizieren der Größenhosts

Identifizieren Sie die Größenhosts, die Sie beim Bereitstellen von Azure VMware Solution verwenden möchten.  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>Bestimmen der Anzahl von Clustern und Hosts

Ihre erste Azure VMware Solution-Bereitstellung umfasst eine private Cloud mit einem einzelnen Cluster. Sie müssen die Anzahl von Hosts definieren, die Sie im ersten Cluster für Ihre Bereitstellung bereitstellen möchten.

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>Informationen zu den Grenzwerten für die Anzahl von Hosts pro Cluster, die Anzahl von Clustern pro privater Cloud und die Anzahl der Hosts pro privater Cloud finden Sie unter [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-vmware-solution-limits).

## <a name="request-a-host-quota"></a>Anfordern eines Hostkontingents 

Es ist wichtig, frühzeitig ein Hostkontingent anzufordern, sodass Sie nach Abschluss des Planungsprozesses bereit sind, Ihre private Azure VMware Solution-Cloud bereitzustellen.
Stellen Sie vor dem Anfordern eines Hostkontingents sicher, dass Sie das Azure-Abonnement, die Ressourcengruppe und die Region identifiziert haben. Stellen Sie außerdem sicher, dass Sie die Größenhosts identifiziert haben und die Anzahl von erforderlichen Clustern und Hosts bestimmen.

Nachdem das Supportteam Ihre Anforderung eines Hostkontingents erhalten hat, dauert es bis zu fünf Werktage, bis die Anforderung bestätigt wurde und Ihre Hosts zugewiesen wurden.

- [EA-Kunden](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP-Kunden](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>Definieren des IP-Adressensegments für die Verwaltung der privaten Cloud

Für Azure VMware Solution wird ein CIDR-Netzwerk vom Typ „/22“ benötigt, z. B. `10.0.0.0/22`. Dieser Adressraum wird in kleinere Netzwerksegmente (Subnetze) unterteilt und für Azure VMware Solution-Verwaltungssegmente (u. a. vCenter-, VMware HCX-, NSX-T- und vMotion-Funktionen) verwendet. Im Diagramm werden IP-Adresssegmente für die Azure VMware Solution-Verwaltung hervorgehoben.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Diagramm: IP-Adresssegmente für die Azure VMware Solution-Verwaltung" border="false":::  

>[!IMPORTANT]
>Der CIDR-Netzwerkadressblock vom Typ „/22“ darf sich mit keinem Netzwerksegment überschneiden, das bereits lokal oder in Azure vorhanden ist. Weitere Informationen dazu, wie das CIDR-Netzwerk vom Typ „/22“ pro privater Cloud aufgeschlüsselt wird, finden Sie unter [Überlegungen zu Routing und Subnetz](tutorial-network-checklist.md#routing-and-subnet-considerations).



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>Definieren des IP-Adressensegments für VM-Workloads

Wie bei jeder VMware-Umgebung müssen die virtuellen Computer eine Verbindung mit einem Netzwerksegment herstellen.  Wenn die Produktionsbereitstellung von Azure VMware Solution erweitert wird, gibt es häufig eine Kombination aus erweiterten L2-Segmenten aus der lokalen Umgebung und lokalen NSX-T-Netzwerksegmenten. 

Identifizieren Sie für die erste Bereitstellung ein einzelnes Netzwerksegment (IP-Netzwerk), z. B. `10.0.4.0/24`. Dieses Netzwerksegment wird hauptsächlich zu Testzwecken während der ersten Bereitstellung verwendet.  Der Adressblock darf sich nicht mit netzwerkspezifischen Segmenten lokal oder innerhalb von Azure überschneiden und darf nicht innerhalb des bereits definierten Netzwerksegments vom Typ „/22“ enthalten sein. 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifizieren: IP-Adressensegment für VM-Workloads" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>Definieren des VNET-Gateways

Azure VMware Solution erfordert eine Azure Virtual Network-Instanz und eine ExpressRoute-Leitung. Legen Sie fest, ob Sie ein *vorhandenes* ODER ein *neues* ExpressRoute-VNET-Gateway verwenden möchten. Wenn Sie sich für die Verwendung eines *neuen* VNet-Gateways entscheiden, erstellen Sie dieses nach der Erstellung der privaten Cloud. Die Verwendung eines vorhandenen ExpressRoute-VNet-Gateways ist akzeptabel. Notieren Sie sich zu Planungszwecken, welches ExpressRoute-VNet-Gateway Sie verwenden. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Diagramm: An Azure VMware Solution angefügtes virtuelles Azure-Netzwerk" border="false":::

>[!IMPORTANT]
>Sie können eine Verbindung mit einem VNET-Gateway in Azure Virtual WAN herstellen, dies wird in dieser Schnellstartanleitung jedoch nicht erläutert.

## <a name="define-vmware-hcx-network-segments"></a>Definieren der VMware HCX-Netzwerksegmente

VMware HCX ist eine Plattform für die Anwendungsmobilität, die die Anwendungsmigration, den Ausgleich von Workloads und die Geschäftskontinuität zwischen Rechenzentren und Clouds vereinfacht. Sie können Ihre VMware-Workloads zu Azure VMware Solution und zu anderen verbundenen Standorten über verschiedene Migrationstypen migrieren. 

Vom VMware HCX-Connector wird eine Teilmenge virtueller Geräte (automatisiert) bereitgestellt, die mehrere IP-Segmente erfordern. Wenn Sie Ihre Netzwerkprofile erstellen, verwenden Sie die IP-Segmente. Identifizieren Sie die folgenden Punkte für die VMware HCX-Bereitstellung, die einen Anwendungsfall für ein Pilotprojekt oder kleines Produkt unterstützt.  Nehmen Sie gemäß den Anforderungen Ihrer Migration entsprechende Änderungen vor. 

- **Verwaltungsnetzwerk:** Bei der lokalen Bereitstellung von VMware HCX müssen Sie ein Verwaltungsnetzwerk für VMware HCX identifizieren.  In der Regel handelt es sich um dasselbe Verwaltungsnetzwerk, das von Ihrem lokalen VMware-Cluster verwendet wird.  Identifizieren Sie in diesem Netzwerksegment mindestens **zwei** IP-Adressen für VMware HCX. Abhängig von der Skalierung der Bereitstellung, die über den Pilot- oder den kleinen Anwendungsfall hinausgeht, benötigen Sie möglicherweise größere Zahlen.

  >[!NOTE]
  >Statt bei der Vorbereitung auf große Umgebungen das für den lokalen VMware-Cluster verwendete Verwaltungsnetzwerk zu verwenden, erstellen Sie ein neues Netzwerk vom Typ „/26“, und stellen Sie dieses Netzwerk als Portgruppe für Ihren lokalen VMware-Cluster bereit.  Anschließend können Sie bis zu 10 Dienstnetze und 60 Netzwerkextender (-1 pro Dienstnetz) erstellen. Mit privaten Azure VMware Solution-Clouds können pro Netzwerkextender **acht** Netzwerke gestreckt werden.

- **Uplink-Netzwerk:** Bei der lokalen Bereitstellung von VMware HCX müssen Sie ein Uplink-Netzwerk für VMware HCX identifizieren. Verwenden Sie dasselbe Netzwerk wie für das Verwaltungsnetzwerk. 

- **vMotion-Netzwerk:** Bei der lokalen Bereitstellung von VMware HCX müssen Sie ein vMotion-Netzwerk für VMware HCX identifizieren.  In der Regel handelt es sich um dasselbe Netzwerk, das auch von Ihrem lokalen VMware-Cluster für vMotion verwendet wird.  Identifizieren Sie in diesem Netzwerksegment mindestens **zwei** IP-Adressen für VMware HCX. Abhängig von der Skalierung der Bereitstellung, die über den Pilot- oder den kleinen Anwendungsfall hinausgeht, benötigen Sie möglicherweise größere Zahlen.

  Sie müssen das vMotion-Netzwerk über einen verteilten virtuellen Switch oder vSwitch0 verfügbar machen. Ist dies nicht der Fall, ändern Sie die Umgebung entsprechend.

  >[!NOTE]
  >Viele VMware-Umgebungen verwenden nicht geroutete Netzwerksegmente für vMotion. Dies stellt kein Problem dar.
  
- **Replikationsnetzwerk:** Bei der lokalen Bereitstellung von VMware HCX müssen Sie ein Replikationsnetzwerk definieren. Verwenden Sie das gleiche Netzwerk wie für Ihre Verwaltungs- und Uplink-Netzwerke.  Wenn der lokale Clusterhost ein dediziertes Replication-VMkernel-Netzwerk verwendet, reservieren Sie in diesem Netzwerksegment **zwei** IP-Adressen, und nutzen Sie das Replication-VMkernel-Netzwerk für das Replikationsnetzwerk.


## <a name="determine-whether-to-extend-your-networks"></a>Bestimmen, ob Ihre Netzwerke erweitert werden sollen

Sie können Netzwerksegmente optional aus der lokalen Umgebung auf Azure VMware Solution erweitern. In diesem Fall sollten Sie diese Netzwerke jetzt gemäß der folgenden Richtlinien identifizieren:

- Für die Netzwerke muss eine Verbindung mit einem [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) in Ihrer lokalen VMware-Umgebung hergestellt werden.  
- Netzwerke, die sich auf einem [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) befinden, können nicht erweitert werden.

>[!IMPORTANT]
>Diese Netzwerke werden im letzten Schritt der Konfiguration erweitert, nicht während der Bereitstellung.


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die erforderlichen Informationen nun gesammelt und dokumentiert haben, können Sie mit dem nächsten Tutorial fortfahren, um Ihre private Azure VMware Solution-Cloud zu erstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Azure VMware Solution](deploy-azure-vmware-solution.md)
