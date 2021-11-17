---
title: Vergleich von Container-Apps mit anderen Azure-Containeroptionen
description: Hier erfahren Sie, wann Sie Azure Container Apps verwenden sollten und inwiefern die Lösung mit anderen Containeroptionen wie Azure Container Instances, Azure App Service, Azure Functions und Azure Kubernetes Service vergleichbar ist.
services: container-apps
author: jeffhollan
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/03/2021
ms.author: jehollan
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac05794b42e91060e604a9d6921547150eb6706
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309994"
---
# <a name="comparing-container-apps-with-other-azure-container-options"></a>Vergleich von Container-Apps mit anderen Azure-Containeroptionen

Es stehen zahlreiche Optionen für Teams zur Verfügung, um cloudnative und containerisierte Anwendungen in Azure zu entwickeln und bereitzustellen. Dieser Artikel hilft Ihnen zu verstehen, welche Szenarien und Anwendungsfälle sich am besten für Azure Container Apps eignen und wie sie im Vergleich zu anderen Containeroptionen in Azure abschneiden, darunter:  
- [Azure App Service](#azure-app-service)
- [Azure Container Instances](#azure-container-instances)
- [Azure Kubernetes Service](#azure-kubernetes-service)
- [Azure-Funktionen](#azure-functions)
- [Azure Spring Cloud](#azure-spring-cloud)

Es gibt keine perfekte Lösung für jeden Anwendungsfall und jedes Team. Die folgenden Erläuterungen bieten allgemeine Hinweise und Empfehlungen als Ausgangspunkt, um die beste Lösung für Ihr Team und Ihre Anforderungen zu finden.

> [!IMPORTANT]
> Azure Container Apps befindet sich derzeit in der öffentlichen Vorschau, während die anderen Optionen allgemein verfügbar sind (GA).


## <a name="container-option-comparisons"></a>Vergleich der Containeroptionen

### <a name="azure-container-apps"></a>Azure Container Apps
Mit Azure Container Apps können Sie serverlose Microservices basierend auf Containern erstellen. Zu den besonderen Merkmalen von Container Apps gehören:

* Optimiert für die Ausführung von universellen Containern, insbesondere für Anwendungen, die sich über viele in Containern bereitgestellte Microservices erstrecken
* Basiert auf Kubernetes und Open-Source-Technologien wie [Dapr](https://dapr.io/), [KEDA](https://keda.sh/) und [envoy](https://www.envoyproxy.io/)
* Unterstützung für Apps im Kubernetes-Stil und Microservices mit Features wie [Dienstermittlung](connect-apps.md) und [Datenverkehrstrennung](revisions.md)
* Ermöglicht ereignisgesteuerte Anwendungsarchitekturen durch Unterstützung einer Skalierung auf Grundlage des Datenverkehrs und des Abrufs von [Ereignisquellen wie z. B. Warteschlangen](scale-app.md), einschließlich [Skalierung auf 0](scale-app.md)
* Unterstützung von zeitintensiven Prozessen und Fähigkeit zur Ausführung von [Hintergrundaufgaben](background-processing.md)

Azure Container Apps bietet keinen direkten Zugriff auf die zugrunde liegenden Kubernetes-APIs. Wenn Sie Zugriff auf die Kubernetes-APIs und die Steuerungsebene benötigen, sollten Sie [Azure Kubernetes Service](../aks/intro-kubernetes.md) verwenden. Wenn Sie jedoch Anwendungen im Kubernetes-Stil erstellen möchten und keinen direkten Zugriff auf sämtliche nativen Kubernetes-APIs und die Clusterverwaltung benötigen, bietet Container Apps eine vollständig verwaltete Benutzeroberfläche basierend auf Best Practices. Aus diesen Gründen ziehen es viele Teams vor, containerbasierte Microservices mit Azure Container Apps zu entwickeln.

### <a name="azure-app-service"></a>Azure App Service
Azure App Service bietet ein vollständig verwaltetes Hosting für Webanwendungen, einschließlich Websites und Web-APIs. Diese Webanwendungen können mithilfe von Code oder Containern bereitgestellt werden. Azure App Service ist für Webanwendungen optimiert. Azure App Service ist in andere Azure-Dienste wie Azure Container Apps oder Azure Functions integriert. Beim Erstellen von Web-Apps ist Azure App Service eine ideale Option.

### <a name="azure-container-instances"></a>Azure Container Instances
Azure Container Instances (ACI) bietet einen einzelnen Pod mit Hyper-V-isolierten Containern nach Bedarf. Im Vergleich zu Container Apps kann man sie als „Baustein“-Option auf niedrigerer Ebene betrachten. Konzepte wie Skalierung, Lastausgleich und Zertifikate sind bei ACI-Containern nicht enthalten. Um beispielsweise auf fünf Containerinstanzen zu skalieren, erstellen Sie fünf verschiedene Containerinstanzen. Azure Container Apps bieten viele anwendungsspezifische Konzepte auf der Grundlage von Containern, darunter Zertifikate, Revisionen, Skalierung und Umgebungen. Benutzer interagieren häufig über andere Dienste mit Azure Container Instances. Beispielsweise kann Azure Kubernetes Service über [virtuelle Knoten](../aks/virtual-nodes.md) auf ACI aufsetzen und eine Orchestrierung und Skalierung unterstützen. Wenn Sie einen weniger starren Baustein benötigen, der nicht auf Szenarien ausgerichtet ist, für die Azure Container Apps optimiert wurde, ist Azure Container Instances eine ideale Option.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Azure Kubernetes Service bietet eine vollständig verwaltete Kubernetes-Option in Azure. Es unterstützt den direkten Zugriff auf die Kubernetes-API und führt beliebige Kubernetes-Workloads aus. Der vollständige Cluster befindet sich in Ihrem Abonnement, wobei Sie die Kontrolle und Verantwortung für die Clusterkonfigurationen und -vorgänge haben. Teams, die nach einer vollständig verwalteten Version von Kubernetes in Azure suchen, ist Azure Kubernetes Service die ideale Option.

### <a name="azure-functions"></a>Azure-Funktionen
Azure Functions ist eine serverlose FaaS-Lösung (Functions-as-a-Service). Sie ist für die Ausführung ereignisgesteuerter Funktionen unter Verwendung des Funktionsprogrammiermodells optimiert. Azure Functions weist viele Gemeinsamkeiten mit Azure Container Apps auf, was die Skalierung und die Integration in Ereignisse betrifft, ist aber für kurzlebige Funktionen optimiert, die entweder als Code oder als Container bereitgestellt werden. Das Programmiermodell von Azure Functions bietet Produktivitätsvorteile für Teams, die Ereignisse auslösen und andere Datenquellen als Bindungsziele verwenden möchten. Für die Entwicklung von Funktionen im FaaS-Stil ist Azure Functions die ideale Option. Das Azure Functions-Programmiermodell steht als Basiscontainerimage zur Verfügung und ist somit auf andere containerbasierte Computeplattformen portierbar, sodass Teams den Code wiederverwenden können, wenn sich die Umgebungsanforderungen ändern.

### <a name="azure-spring-cloud"></a>Azure Spring Cloud
Mit Azure Spring Cloud lassen sich Spring Boot-Microserviceanwendungen ganz einfach und ohne Codeänderungen in Azure bereitstellen. Der Dienst verwaltet die Infrastruktur von Spring Cloud-Anwendungen und ermöglicht es Entwicklern dadurch, sich auf ihren Code zu konzentrieren. Azure Spring Cloud bietet Lebenszyklusverwaltung durch umfassende Überwachung und Diagnose, Konfigurationsverwaltung, Dienstermittlung, CI/CD-Integration, Blau-Grün-Bereitstellungen und mehr. Wenn Ihr Team oder Ihre Organisation vornehmlich Spring nutzt, ist Azure Spring Cloud die ideale Option.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen Ihrer ersten Container-App](get-started.md)
