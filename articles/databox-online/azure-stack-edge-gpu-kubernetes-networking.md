---
title: Kubernetes-Netzwerke auf Azure Stack Edge Pro GPU-Geräten
description: Hier wird die Funktionsweise von Kubernetes-Netzwerken auf Azure Stack Edge Pro GPU-Geräten beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/24/2021
ms.author: alkohli
ms.openlocfilehash: ece1d03787308613961fe56087f8b621a753c856
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572573"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Kubernetes-Netzwerke auf Azure Stack Edge Pro GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wenn Sie die Computerolle konfigurieren, wird auf Ihrem Azure Stack Edge Pro GPU-Gerät ein Kubernetes-Cluster erstellt. Sobald der Kubernetes-Cluster erstellt wurde, können innerhalb des Kubernetes-Clusters Containeranwendungen in Pods bereitgestellt werden. Für die Pods in Ihrem Kubernetes-Cluster werden unterschiedliche Netzwerkfunktionen genutzt. 

In diesem Artikel werden die Netzwerkfunktionen in einem Kubernetes-Cluster sowohl allgemein als auch im Kontext Ihres Azure Stack Edge Pro GPU-Geräts beschrieben. 

## <a name="networking-requirements"></a>Netzwerkanforderungen

Nachfolgend sehen Sie ein Beispiel einer typischen App mit zwei Ebenen, die im Kubernetes-Cluster bereitgestellt wird.

- Die App verfügt über ein Webserver-Front-End und eine Datenbankanwendung im Back-End. 
- Jedem Pod ist eine IP-Adresse zugewiesen. Diese IP-Adressen können sich beim Neustart und Failover des Pods jedoch ändern. 
- Jede App umfasst mehrere Pods, und für den Datenverkehr der Podreplikate sollte ein Lastenausgleich implementiert sein. 

![Kubernetes-Netzwerkanforderungen](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

Für das oben gezeigte Szenario gelten die folgenden Netzwerkanforderungen:

 - Auf die Anwendung mit externem Zugriff muss über einen Namen oder eine IP-Adresse von einem Anwendungsbenutzer außerhalb des Kubernetes-Clusters zugegriffen werden können. 
 - Die Anwendungen innerhalb des Kubernetes-Clusters (z. B. die Front-End- und Back-End-Pods) müssen miteinander kommunizieren können.

Um beide Anforderungen zu erfüllen, wird ein Kubernetes-Dienst eingeführt. 


## <a name="networking-services"></a>Netzwerkdienste

Es gibt zwei Arten von Kubernetes-Diensten: 

- **Cluster-IP-Dienst**: Sie können sich diesen Dienst als einen konstanten Endpunkt für die Anwendungspods vorstellen. Auf Pods, die mit diesen Diensten verknüpft sind, kann nicht von außerhalb des Kubernetes-Clusters zugegriffen werden. Die für diese Dienste verwendete IP-Adresse stammt aus dem Adressraum im privaten Netzwerk. 
    
    Informationen dazu, wie Sie die Pods im Kubernetes-Cluster für den Zugriff wie andere Pods und nicht als extern verfügbar gemachten Lastenausgleichsdienst verfügbar machen, finden Sie unter [Verfügbarmachen des Kubernetes-Diensts als Cluster-IP-Dienst für die interne Kommunikation]().

- **Lastenausgleichs-IP**: Dieser Dienst ist mit dem Cluster-IP-Dienst vergleichbar, die verknüpfte IP-Adresse stammt jedoch aus dem externen Netzwerk, und es kann von außerhalb des Kubernetes-Clusters darauf zugegriffen werden.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Kubernetes-Netzwerkkonfiguration

Die IP-Adressen, die für Kubernetes-Knoten und die externen Dienste verwendet werden, werden auf der Seite **Compute** auf der lokalen Benutzeroberfläche des Geräts angegeben.

![Kubernetes-IP-Adresszuweisung auf der lokalen Benutzeroberfläche](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

Die IP-Adresszuweisung gilt für:

- **IP-Adressen für Kubernetes-Knoten**: Dieser IP-Adressbereich wird für Kubernetes-Master- und -Workerknoten verwendet. Diese IP-Adressen werden für die Kommunikation zwischen Kubernetes-Knoten verwendet.
- **IP-Adressen externer Dienste für Kubernetes**: Dieser IP-Adressbereich wird für externe Dienste (auch als Lastenausgleichsdienste bezeichnet) verwendet, auf die von außerhalb des Kubernetes-Clusters zugegriffen werden kann.

## <a name="kubernetes-networking-components"></a>Kubernetes-Netzwerkkomponenten

Zur Bereitstellung der Netzwerkfunktionen auf Ihrem Azure Stack Edge Pro GPU-Gerät werden die Komponenten Calico, Metallb und Core DNS installiert. 

- **Calico** weist jedem Pod eine IP-Adresse aus einem privaten IP-Adressbereich zu und konfiguriert Netzwerkfunktionen für diese Pods, um die Kommunikation zwischen Pods auf verschiedenen Knoten zu ermöglichen. 
- **Metallb** wird in einem Pod innerhalb des Clusters ausgeführt und weist Diensten vom Typ `load balancer` eine IP-Adresse zu. Die IP-Adressen für den Lastenausgleich werden aus dem IP-Adressbereich des Diensts ausgewählt, der über die lokale Benutzeroberfläche angegeben wird. 
- **Core DNS**: Mit diesem Add-On wird ein Dienst zur Zuordnung von DNS-Einträgen für Cluster-IP-Adressen konfiguriert.

Wenn Sie eine Verbindung mit der PowerShell-Schnittstelle Ihres Geräts herstellen, können Sie die oben genannten Netzwerkkomponenten sehen, die in Ihrem Kubernetes-Cluster ausgeführt werden.

## <a name="network-interfaces-switches"></a>Netzwerkschnittstellen, Switches 

Ihr Gerät ist als 1-Knoten-Konfiguration verfügbar, die den Infrastrukturcluster darstellt. Der Kubernetes-Cluster wird separat vom Infrastrukturcluster implementiert und zusätzlich zum Infrastrukturcluster bereitgestellt. Der Kubernetes-Cluster verfügt über einen Masterknoten und einen Workerknoten. Beide Kubernetes-Knoten sind virtuelle Computer, auf denen Ihre Anwendungen und Cloudworkflows ausgeführt werden.

Die Master- und Worker-VMs verfügen jeweils über zwei Netzwerkschnittstellen: eine, die eine Verbindung mit dem internen virtuellen Switch herstellt, und eine andere, die eine Verbindung mit dem externen virtuellen Switch herstellt. 

- **Externer virtueller Switch**: Dieser Switch wird erstellt, wenn ein Geräteport für Compute über die Seite **Compute** auf der lokalen Benutzeroberfläche aktiviert wird. Dies ist der Switch, den Sie für Ihre Computeinfrastruktur verwenden. Dieser Switch wird beispielsweise für die virtuellen Computer verwendet, die Sie auf Ihrem Gerät bereitstellen. 
- **Interner virtueller Switch**: Dieser Switch wird als Teil der werkseitigen Standardeinstellungen auf Ihrem Gerät erstellt. Der interne virtuelle Switch verwendet die Netzwerkadressenübersetzung (Network Address Translation, NAT), um den Datenverkehr über den Port weiterzuleiten, der mit einem Standardgateway konfiguriert ist. Dieser Switch leitet beispielsweise alle IoT-Runtime-Anforderungen von VMs an das Azure-Portal weiter. 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>Netzwerkrouten 

Für die Kubernetes-VMs auf Ihrem Gerät können Sie den Datenverkehr durch Hinzufügen einer neuen Routenkonfiguration weiterleiten. Eine Routenkonfiguration ist ein Routingtabelleneintrag, der die folgenden Felder enthält:

| Parameter     | BESCHREIBUNG                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| Destination   | Entweder eine IP-Adresse oder ein IP-Adresspräfix.                                            |
| Präfixlänge | Die Präfixlänge, die der Adresse oder dem Adressbereich im Ziel entspricht. |
| Nächster Hop      | Die IP-Adresse, an die das Paket weitergeleitet wird.                                         |
| Schnittstelle     | Die Netzwerkschnittstelle, über die das IP-Paket weitergeleitet wird.                                       |
| Metrik        | Die Routingmetrik bestimmt die bevorzugte Netzwerkschnittstelle, die zum Erreichen des Ziels verwendet wird. |


## <a name="change-routing-on-compute-network"></a>Ändern des Routings im Computenetzwerk

Verwenden Sie das Cmdlet `Add-HcsNetRoute`, um das Routing auf den Kubernetes-Worker- und Master-VMs zu ändern. Sehen Sie sich das Layout im folgenden Diagramm an. 

![Netzwerkdiagramm für Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- Port 2 ist mit dem Internet verbunden und der gewünschte Pfad für ausgehenden Datenverkehr. 
- Sie haben Compute an Port 3 aktiviert und dadurch einen externen virtuellen Switch auf dieser Netzwerkschnittstelle erstellt. 
- Port 3 ist mit einem privaten Netzwerk verbunden, das über Kameras und andere Sensoren verfügt, die Rohdaten zur Verarbeitung an das Azure Stack Edge-Gerät übertragen. 


Wenn ein Gateway in Ihrer Umgebung im privaten Netzwerk konfiguriert ist, sollten Sie benutzerdefinierte Routen für die Kubernetes-Master- und Worker-VMs festlegen, damit sie nur für den relevanten Datenverkehr mit Ihrem Gateway kommunizieren können. Dadurch haben Sie die Kontrolle über den Datenverkehr, der über das Computenetzwerk übertragen wird, im Gegensatz zu den anderen Ports, die Sie möglicherweise auf Ihrem Azure Stack Edge-Gerät konfiguriert haben. Angenommen, der gesamte andere Internetdatenverkehr soll über die anderen physischen Ports auf Ihrem Gerät übertragen werden. In diesem Fall kann der Internetdatenverkehr über Port 2 übertragen werden. 

Sie sollten auch die folgenden Überlegungen berücksichtigen:

- Wenn Sie über ein flaches Subnetz verfügen, müssen Sie diese Routen nicht zum privaten Netzwerk hinzufügen. Sie können diese Routen (optional) hinzufügen, wenn sich in Ihrem privaten Netzwerk mehrere Subnetze befinden.
- Sie können diese Routen nur den Kubernetes-Master- und Worker-VMs und nicht dem Gerät (Windows-Host) hinzufügen.
- Die Kubernetes-Computefunktion muss erst konfiguriert werden, wenn Sie diese Route hinzufügen. Sie können Routen auch hinzufügen oder aktualisieren, nachdem die Kubernetes-Computefunktion konfiguriert wurde. 
- Sie können eine neue Routenkonfiguration nur über die PowerShell-Schnittstelle des Geräts und nicht über die lokale Benutzeroberfläche hinzufügen.
- Stellen Sie sicher, dass die von Ihnen verwendete Netzwerkschnittstelle eine statische Konfiguration aufweist.

## <a name="add-a-route-configuration"></a>Hinzufügen einer Routenkonfiguration

Um dem privaten Netzwerk eine neue benutzerdefinierte Route hinzuzufügen, verwenden Sie das Cmdlet wie folgt:

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
Hier sehen Sie eine Beispielausgabe.

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.21.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

Der oben gezeigte Befehl erstellt einen Eintrag in der Routingtabelle, der das Zielsubnetz 192.168.21.0/24 definiert, den nächsten Hop als 192.168.20.1 angibt und diesem Routingeintrag eine Routingmetrik von 100 zuweist. Je niedriger die Routingmetrik, desto höher ist die Priorität, die der Route zugewiesen ist.

## <a name="check-route-configuration"></a>Überprüfen der Routenkonfiguration

Verwenden Sie das folgende Cmdlet, um nach allen benutzerdefinierten Routenkonfigurationen zu suchen, die Sie auf Ihrem Gerät hinzugefügt haben. Diese Routen enthalten nicht alle Systemrouten oder Standardrouten, die bereits auf dem Gerät vorhanden sind.

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>Entfernen einer Routenkonfiguration

Verwenden Sie das folgende Cmdlet, um eine Routenkonfiguration zu entfernen, die Sie auf Ihrem Gerät hinzugefügt haben.

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>Routing mit mehreren Netzwerkschnittstellen

Wenn mehrere Geräteports verbunden sind, wird standardmäßiges NIC-Teaming oder Switch Embedded Teaming (SET), mit dem Sie mehrere physische Netzwerkadapter zu einem einzigen virtuellen Netzwerkadapter in einer Hyper-V-Umgebung zusammenfassen können, nicht unterstützt.


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zum Konfigurieren der Kubernetes-Netzwerkfunktionen auf Ihrem Azure Stack Edge Pro GPU-Gerät zu erhalten:

- [Externes Bereitstellen einer zustandslosen Anwendung auf Ihrem Azure Stack Edge Pro GPU-Gerät über IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)

- [Externes Bereitstellen einer zustandslosen Anwendung auf Ihrem Azure Stack Edge Pro GPU-Gerät über kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)