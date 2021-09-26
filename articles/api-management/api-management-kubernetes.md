---
title: Verwenden von API Management mit in Azure Kubernetes Service bereitgestellten Microservices | Microsoft-Dokumentation
description: In diesem Artikel werden die Optionen zum Bereitstellen von API Management mit AKS beschrieben.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: danlep
ms.openlocfilehash: 8d2fd83052ee2979bf2a14a949c6e80348767256
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594197"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Verwenden von API Management mit in Azure Kubernetes Service bereitgestellten Microservices

Microservices eignen sich optimal für die Erstellung von APIs. Mit [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) können Sie schnell eine [auf Microservices basierende Architektur](/azure/architecture/guide/architecture-styles/microservices) in der Cloud bereitstellen und betreiben. Anschließend können Sie [Azure API Management](https://aka.ms/apimrocks) (API Management) nutzen, um ihre Microservices als APIs für die interne und externe Nutzung zu veröffentlichen. In diesem Artikel werden die Optionen zum Bereitstellen von API Management mit AKS beschrieben. Dabei werden Grundkenntnisse über Kubernetes, API Management und Azure-Netzwerke vorausgesetzt. 

## <a name="background"></a>Hintergrund

Bei der Veröffentlichung von Microservices als APIs für die Nutzung kann es schwierig sein, die Kommunikation zwischen den Microservices und den Clients, die sie nutzen, zu verwalten. Es gibt eine Vielzahl von Aspekten, die bereichsübergreifend bedacht werden müssen, z. B Authentifizierung, Autorisierung, Drosselung, Zwischenspeicherung, Transformation und Überwachung. Diese Aspekte müssen unabhängig davon berücksichtigt werden, ob die Microservices für interne oder externe Clients verfügbar gemacht werden. 

Das [API-Gateway](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)-Muster hilft dabei, diese Aspekte zu berücksichtigen und einzubeziehen. Ein API-Gateway dient als Front Door für die Microservices, entkoppelt Clients von Ihren Microservices, fügt eine zusätzliche Sicherheitsebene hinzu und verringert die Komplexität ihrer Microservices, da die Behandlung von bereichsübergreifenden Problemen entfällt. 

[Azure API Management](https://aka.ms/apimrocks) ist eine sofort einsetzbare Lösung zum Erfüllen ihrer Anforderungen an ein API-Gateway. Sie können schnell ein konsistentes und modernes Gateway für Ihre Microservices erstellen und sie als APIs veröffentlichen. Als API-Verwaltungslösung für den gesamten Lebenszyklus bietet sie außerdem zusätzliche Funktionen wie ein Self-Service-Entwicklerportal für die API-Erkennung, API-Lebenszyklusverwaltung und API-Analyse.

In Kombination bieten AKS und API Management eine Plattform für die Bereitstellung, Veröffentlichung, Sicherung, Überwachung und Verwaltung Ihrer auf Microservices basierenden APIs. In diesem Artikel werden einige Optionen für die Bereitstellung von AKS in Verbindung mit API Management erläutert. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Services und APIs

In einem Kubernetes-Cluster werden Container in [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) bereitgestellt. Diese sind kurzlebig und haben einen Lebenszyklus. Wenn ein Workerknoten ausfällt, gehen die Pods, die auf dem Knoten ausgeführt werden, verloren. Daher kann sich die IP-Adresse eines Pods jederzeit ändern. Wir können uns nicht darauf verlassen, dass eine Kommunikation mit dem Pod stattfindet. 

Um dieses Problem zu lösen, wurde mit Kubernetes das Konzept der [Dienste](https://kubernetes.io/docs/concepts/services-networking/service/) eingeführt. Kubernetes Service ist eine Abstraktionsschicht, die eine logische Gruppe von Pods definiert und es ermöglicht, externen Datenverkehr verfügbar zu machen sowie Lastenausgleich und Dienstermittlung für diese Pods durchzuführen. 

Wenn Sie bereit sind, Ihre Microservices als APIs über das API Management zu veröffentlichen, müssen Sie bedenken, wie Sie Ihre Services in Kubernetes den APIs in API Management zuordnen können. Es gibt dafür keine festen Regeln. Es hängt davon ab, wie Sie Ihre Geschäftsfunktionen und -domänen am Anfang entworfen und in Microservices partitioniert haben. Wenn beispielsweise die Pods hinter einem Dienst für alle Vorgänge einer bestimmten Ressource (z. B. Kunde) verantwortlich sind, kann der Dienst einer einzigen API zugeordnet werden. Wenn Vorgänge auf einer Ressource in mehrere Microservices partitioniert werden (z. B. GetOrder, PlaceOrder), dann können mehrere Dienste in logisch zu einer einzigen API in API Management zusammengefasst werden (siehe Abbildung 1). 

Die Zuordnungen können auch weiterentwickelt werden. Da API Management eine „Fassade“ vor den Microservices erstellt, können wir unsere Microservices im Laufe der Zeit umgestalten und auf die richtige Größe anpassen. 

![Zuordnen von Diensten zu APIs](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Bereitstellen von API Management vor AKS

Es gibt einige Optionen für die Bereitstellung von API Management vor einem AKS-Cluster. 

Ein AKS-Cluster wird immer in einem virtuellen Netzwerk (VNET) bereitgestellt, eine API Management-Instanz muss jedoch nicht in einem VNET bereitgestellt werden. Wenn sich API Management nicht innerhalb des VNET-Clusters befindet, muss der AKS-Cluster öffentliche Endpunkte veröffentlichen, mit denen API Management eine Verbindung herstellen kann. In diesem Fall muss die Verbindung zwischen API Management und AKS gesichert werden. Mit anderen Worten: Es muss sichergestellt werden, dass der Zugriff auf den Cluster nur über API Management möglich ist. Schauen wir uns die Optionen an. 

### <a name="option-1-expose-services-publicly"></a>Option 1: Öffentlich Verfügbarmachen von Diensten

Dienste in einem AKS-Cluster können mit den [Diensttypen](../aks/concepts-network.md) NodePort, LoadBalancer oder ExternalName öffentlich verfügbar gemacht werden. In diesem Fall sind Dienste direkt über das öffentliche Internet zugänglich. Nach der Bereitstellung von API Management vor dem Cluster muss sichergestellt werden, dass der gesamte eingehende Datenverkehr API Management durchläuft, indem Authentifizierung in den Microservices angewendet wird. Beispielsweise kann API Management ein Zugriffstoken in jede Anforderung an den Cluster einbinden. Jeder Microservice ist dafür verantwortlich, das Token vor der Verarbeitung der Anforderung zu überprüfen. 


Dies dürfte die einfachste Option sein, um API Management vor AKS bereitzustellen, vor allem dann, wenn Sie bereits eine Authentifizierungslogik in Ihren Microservices implementiert haben. 

![Dienste direkt veröffentlichen](./media/api-management-aks/direct.png)

Vorteile:
* Einfache Konfiguration auf der Seite von API Management, da sie nicht in das VNET des Clusters eingefügt werden muss
* Keine Änderung auf der AKS-Seite, wenn Sie die Dienste bereits öffentlich verfügbar gemacht haben und die Microservices bereits eine Authentifizierungslogik enthalten

Nachteile:
* Potenzielles Sicherheitsrisiko aufgrund öffentlicher Sichtbarkeit von Dienstendpunkten
* Kein einzelner Einstiegspunkt für eingehenden Clusterdatenverkehr
* Kompliliert Microservices mit doppelter Authentifizierungslogik

### <a name="option-2-install-an-ingress-controller"></a>Option 2: Installieren eines Eingangscontrollers

Obwohl Option 1 einfacher sein mag, gibt es jedoch beträchtliche Nachteile, wie oben erwähnt. Wenn sich eine API Management-Instanz nicht im Cluster-VNET befindet, stellt die gegenseitige TLS-Authentifizierung (mTLS) eine zuverlässige Methode dar, um sicherzustellen, dass der Datenverkehr zwischen einer API Management-Instanz und einem AKS-Cluster in beide Richtungen sicher und vertrauenswürdig ist. 

Die gegenseitige TLS-Authentifizierung wird von API Management [nativ unterstützt](./api-management-howto-mutual-certificates.md) und kann in Kubernetes durch [Installieren eines Eingangscontrollers](../aks/ingress-own-tls.md) (Abbildung 3) aktiviert werden. Dadurch wird die Authentifizierung im Eingangscontroller durchgeführt, wodurch die Microservices vereinfacht werden. Außerdem können Sie die IP-Adressen von API Management zur Liste der zulässigen Adressen für eingehenden Datenverkehr hinzufügen, um sicherzustellen, dass nur API Management Zugriff auf den Cluster hat.  

 
![Veröffentlichen über einen Eingangscontroller](./media/api-management-aks/ingress-controller.png)


Vorteile:
* Einfache Konfiguration auf der Seite von API Management, da sie nicht in das VNET des Clusters eingefügt werden muss, und mTLS wird nativ unterstützt
* Zentralisiert den Schutz für eingehenden Clusterdatenverkehr auf der Ebene des Eingangscontrollers
* Verringert das Sicherheitsrisiko, indem die Anzahl öffentlich sichtbarer Clusterendpunkte minimiert wird

Nachteile:
* Erhöht die Komplexität der Clusterkonfiguration aufgrund des Mehraufwands für die Installation, Konfiguration und Wartung des Eingangscontrollers und für die Verwaltung der für mTLS verwendeten Zertifikate
* Sicherheitsrisiko aufgrund öffentlicher Sichtbarkeit von Endpunkten des Eingangscontrollers


Wenn Sie APIs mit API Management veröffentlichen, ist es einfach und üblich, auf diese APIs mithilfe von Abonnementschlüsseln sicher zuzugreifen. Entwickler, die die veröffentlichten APIs nutzen möchten, müssen einen gültigen Abonnementschlüssel in HTTP-Anforderungen einschließen, wenn sie Aufrufe an diese APIs senden. Andernfalls werden die Aufrufe sofort vom API Management-Gateway abgelehnt. Sie werden nicht an die Back-End-Dienste weitergeleitet.

Um einen Abonnementschlüssel für den API-Zugriff zu erhalten, ist ein Abonnement erforderlich. Ein Abonnement ist im Wesentlichen ein benannter Container für ein Paar von Abonnementschlüsseln. Entwickler, die die veröffentlichten APIs verarbeiten müssen, können Abonnements erhalten. Sie benötigen keine Genehmigung durch API-Herausgeber. API-Herausgeber können Abonnements für API-Nutzer auch direkt erstellen.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Option 3: Bereitstellen von APIM im Cluster-VNET

In einigen Fällen sind die Optionen 1 und 2 für Kunden mit gesetzlichen Einschränkungen oder strengen Sicherheitsanforderungen aufgrund öffentlich sichtbarer Endpunkte möglicherweise keine praktikablen Lösungen. In anderen Fällen befinden sich der AKS-Cluster und die Anwendungen, die die Microservices nutzen, möglicherweise im selben VNET. Daher gibt es keinen Grund, den Cluster öffentlich verfügbar zu machen, da der gesamte API-Datenverkehr innerhalb des VNETs verbleibt. In diesen Szenarien können Sie API Management im Cluster-VNET bereitstellen. [Die Tarife „Developer“ und „Premium“ von API Management](https://aka.ms/apimpricing) unterstützen VNet-Bereitstellung. 

Es gibt zwei Modi für die [Bereitstellung von API Management in einem VNET](./api-management-using-with-vnet.md) – extern und intern. 

Wenn sich API-Consumer nicht im Cluster-VNET befinden, sollte der externe Modus (Abbildung 4) verwendet werden. In diesem Modus wird das API Management-Gateway in das Cluster-VNET eingefügt, ist aber über einen externen Lastenausgleich über das öffentliche Internet zugänglich. Es hilft, den Cluster komplett auszublenden und dennoch externen Clients die Nutzung der Microservices zu ermöglichen. Zusätzlich können Sie Azure-Netzwerkfunktionen wie Netzwerksicherheitsgruppen (Network Security Groups, NSG) nutzen, um den Netzwerkdatenverkehr einzuschränken.

![Externer VNET-Modus](./media/api-management-aks/vnet-external.png)

Wenn sich alle API-Consumer im Cluster-VNET befinden, kann der interne Modus (Abbildung 5) verwendet werden. In diesem Modus wird das API Management-Gateway in das Cluster-VNET eingefügt und ist nur innerhalb dieses VNETs über einen internen Lastenausgleich zugänglich. Ein Zugriff auf das API Management-Gateway oder den AKS-Cluster über das öffentliche Internet ist nicht möglich. 

![Interner VNET-Modus](./media/api-management-aks/vnet-internal.png)

 In beiden Fällen ist der AKS-Cluster nicht öffentlich sichtbar. Im Vergleich zu Option 2 ist der Eingangscontroller möglicherweise nicht erforderlich. Je nach Ihrem Szenario und Ihrer Konfiguration ist möglicherweise noch eine Authentifizierung zwischen API Management und Ihren Microservices erforderlich. Wenn beispielsweise ein Service Mesh eingeführt wird, ist immer eine gegenseitige TLS-Authentifizierung erforderlich. 

Vorteile:
* Die sicherste Option, da der AKS-Cluster keinen öffentlichen Endpunkt hat
* Vereinfacht die Clusterkonfiguration, da es keinen öffentlichen Endpunkt gibt
* Möglichkeit zum Ausblenden von API Management und AKS innerhalb des VNETs mit dem internen Modus
* Möglichkeit zum Steuern des Netzwerkdatenverkehrs mit Azure-Netzwerkfunktionen wie Netzwerksicherheitsgruppen (Network Security Groups, NSG)

Nachteile:
* Erhöht die Komplexität der Bereitstellung und Konfiguration von API Management für den Einsatz innerhalb des VNETs

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Netzwerkkonzepte für Anwendungen in Azure Kubernetes Service (AKS)](../aks/concepts-network.md).
* Weitere Informationen finden Sie in [Verwenden von API Management mit virtuellen Netzwerken](./api-management-using-with-vnet.md).
