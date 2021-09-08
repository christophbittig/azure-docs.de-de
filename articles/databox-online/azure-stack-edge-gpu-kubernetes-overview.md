---
title: Übersicht über Kubernetes-Cluster auf Microsoft Azure Stack Edge Pro-Geräten | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Kubernetes auf Ihrem Azure Stack Edge Pro-Gerät implementiert wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 22a66ca16d8d2cb7ade82cb665f50cc9aae4b27b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355817"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes auf Ihrem Azure Stack Edge Pro-GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes ist eine beliebte Open-Source-Plattform zur Orchestrierung von Containeranwendungen. Dieser Artikel enthält eine Übersicht über Kubernetes und beschreibt, wie Kubernetes auf Ihrem Azure Stack Edge Pro-Gerät funktioniert. 

## <a name="about-kubernetes"></a>Informationen zu Kubernetes 

Kubernetes ist eine unkomplizierte, zuverlässige Plattform, mit der containerbasierte Anwendungen und die zugehörigen Netzwerk- und Speicherkomponenten verwaltet werden können. Mit Kubernetes können Sie Container-Apps im Handumdrehen erstellen, bereitstellen und skalieren.

Da es sich um eine offene Plattform handelt, können Sie Anwendungen mit Kubernetes in Ihrer bevorzugten Programmiersprache, mit Ihren bevorzugten Betriebssystembibliotheken und mit Ihrem bevorzugten Messagingbus erstellen. Zur Planung und Bereitstellung von Releases lässt sich Kubernetes in vorhandene CI/CD-Tools (Continuous Integration/Continuous Delivery) integrieren.

Weitere Informationen finden Sie unter [Funktionsweise von Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes auf Azure Stack Edge Pro-Geräten

Sie können einen Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät erstellen, indem Sie die Computerolle konfigurieren. Wenn die Computerolle konfiguriert ist, wird der Kubernetes-Cluster mitsamt der Master- und Workerknoten für Sie bereitgestellt und konfiguriert. Dieser Cluster wird dann für die Workloadbereitstellung über `kubectl`, IoT Edge oder Azure Arc verwendet.

Das Azure Stack Edge Pro-Gerät ist als Konfiguration mit einem Knoten verfügbar, die den Infrastrukturcluster darstellt. Der Kubernetes-Cluster wird separat vom Infrastrukturcluster implementiert und zusätzlich zum Infrastrukturcluster bereitgestellt. Der Infrastrukturcluster stellt den dauerhaften Speicher für Ihr Azure Stack Edge Pro-Gerät bereit, während der Kubernetes-Cluster ausschließlich für die Anwendungsorchestrierung verantwortlich ist. 

Der Kubernetes-Cluster verfügt in diesem Fall über einen Masterknoten und einen Workerknoten. Bei den Kubernetes-Knoten innerhalb eines Clusters handelt es sich um virtuelle Computer, in denen Ihre Anwendungen und Cloudworkflows ausgeführt werden. 

Der Kubernetes-Masterknoten ist dafür verantwortlich, den gewünschten Zustand für Ihren Cluster aufrechtzuerhalten. Darüber hinaus steuert der Masterknoten den Workerknoten, der wiederum die Containeranwendungen ausführt. 

Das folgende Diagramm veranschaulicht die Implementierung von Kubernetes auf einem Azure Stack Edge Pro-Gerät mit einem Knoten. Das Gerät mit einem Knoten ist nicht hoch verfügbar. Wenn der Knoten ausfällt, ist das Gerät also nicht mehr verfügbar. Der Kubernetes-Cluster fällt ebenfalls aus.

![Kubernetes-Architektur für ein Azure Stack Edge Pro-Gerät mit einem Knoten](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Weitere Informationen zur Kubernetes-Clusterarchitektur finden Sie unter [Grundlegende Kubernetes-Konzepte](https://kubernetes.io/docs/concepts/architecture/).

Die Master- und Workerknoten sind virtuelle Computer, die CPU und Arbeitsspeicher beanspruchen. Bei der Bereitstellung von Kubernetes-Workloads ist es wichtig, die Computeanforderungen für die Master- und Worker-VMs zu verstehen.

|Kubernetes-VM-Typ|CPU- und Arbeitsspeicheranforderung|
|---------|---------|
|Master-VM|4 Kerne, 4 GB RAM|
|Worker-VM|12 Kerne, 32 GB RAM|
<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Bereitstellung von Speichervolumes

Zum Unterstützen von Anwendungsworkloads können Sie Speichervolumes für persistente Daten auf Ihren Azure Stack Edge Pro-Gerätefreigaben bereitstellen. Statische und dynamische Volumes können verwendet werden. 

Weitere Informationen finden Sie in den Speicherbereitstellungsoptionen für Anwendungen unter [Kubernetes-Speicher für Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Netzwerk

Ein Kubernetes-Netzwerk ermöglicht es Ihnen, die Kommunikation zwischen den verschiedenen Elementen zu konfigurieren. Dies umfasst sowohl die Netzwerkkommunikation zwischen Containern als auch zwischen Pods, zwischen Pods und Diensten sowie zwischen dem Internet und dem Dienst. Weitere Informationen finden Sie im Netzwerkmodell unter [Kubernetes-Netzwerk für Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Aktualisierungen

Sobald neue Kubernetes-Versionen verfügbar sind, kann Ihr Cluster über die Standardupdates aktualisiert werden, die für Ihr Azure Stack Edge Pro-Gerät verfügbar sind. Die erforderlichen Schritte für das Upgrade sind unter [Anwenden von Updates auf Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-install-update.md) beschrieben.

## <a name="access-monitoring"></a>Zugriff und Überwachung

Der Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät erlaubt die rollenbasierte Kubernetes-Zugriffssteuerung (Kubernetes RBAC). Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Kubernetes auf einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-kubernetes-rbac.md).

Zudem können Sie die Integrität Ihres Clusters und Ihrer Ressourcen über das Kubernetes-Dashboard überwachen. Darüber hinaus sind Containerprotokolle verfügbar. Weitere Informationen finden Sie unter [Überwachen der Integrität des Kubernetes-Clusters auf Ihrem Azure Stack Edge Pro-Gerät über das Kubernetes-Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Außerdem ist Azure Monitor als Add-On verfügbar, um Integritätsdaten von Containern, Knoten und Controllern zu sammeln. Weitere Informationen finden Sie unter [Übersicht über Azure Monitor](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Anwendungsverwaltung

Nachdem ein Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät erstellt wurde, können Sie die auf diesem Cluster bereitgestellten Anwendungen mit einer der folgenden Methoden verwalten:

- Nativer Zugriff über `kubectl`
- IoT Edge 
- Azure Arc

Diese Methoden werden in den folgenden Abschnitten näher beschrieben.


### <a name="kubernetes-and-kubectl"></a>Kubernetes und kubectl

Nachdem der Kubernetes-Cluster bereitgestellt wurde, können Sie die auf dem Cluster bereitgestellten Anwendungen lokal über einen Clientcomputer verwalten. Verwenden Sie ein natives Tool wie *kubectl* über die Befehlszeile, um mit den Anwendungen zu interagieren. 

Weitere Informationen zur Bereitstellung eines Kubernetes-Clusters finden Sie unter [Bereitstellen eines Kubernetes-Clusters auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md). Informationen zur Verwaltung finden Sie unter [Verwenden von kubectl zum Verwalten des Kubernetes-Clusters auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes und IoT Edge

Kubernetes lässt sich auch in IoT Edge-Workloads auf Azure Stack Edge Pro-Geräten integrieren. Dabei bietet Kubernetes die Skalierbarkeit und das Ökosystem, IoT Edge bietet das IoT-zentrische Ökosystem. Die Kubernetes-Ebene wird als Infrastrukturebene für die Bereitstellung von Azure IoT Edge-Workloads verwendet. Die Modullebensdauer und der Netzwerklastenausgleich werden von Kubernetes verwaltet, die Edge-Anwendungsplattform von IoT Edge.

Weitere Informationen zur Bereitstellung von Anwendungen in Ihrem Kubernetes-Cluster über IoT Edge finden Sie hier: 

- [Verfügbarmachen von zustandslosen Anwendungen auf Azure Stack Edge Pro-Geräten über IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes und Azure Arc

Azure Arc ist ein Hybridverwaltungstool, mit dem Sie Anwendungen in Ihren Kubernetes-Clustern bereitstellen können. Darüber hinaus ermöglicht Azure Arc die Verwendung von Azure Monitor für Container, um Ihre Cluster anzuzeigen und zu überwachen. Weitere Informationen finden Sie unter [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md). Informationen zu den Preisen von Azure Arc finden Sie unter [Azure Arc-Preise](https://azure.microsoft.com/services/azure-arc/#pricing).

Ab März 2021 steht Kubernetes mit Azure Arc-Unterstützung für die Benutzer allgemein zur Verfügung, und es fallen Standardnutzungsgebühren an. Als einem unserer geschätzten Kunden der Vorschauversion wird Ihnen Kubernetes mit Azure Arc-Unterstützung für Azure Stack Edge-Geräte kostenlos zur Verfügung gestellt. Wenn Sie dieses Vorschauangebot nutzen möchten, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. Wählen Sie unter **Problemtyp** die Option **Abrechnung** aus.
2. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
3. Wählen Sie unter **Dienst** die Option **Meine Dienste** und dann **Azure Stack Edge** aus.
4. Wählen Sie unter **Ressource** Ihre Ressource aus.
5. Geben Sie unter **Zusammenfassung** eine Beschreibung Ihres Problems ein.
6. Wählen Sie unter **Problemtyp** die Option **Unerwartete Gebühren** aus.
7. Wählen Sie unter **Problemuntertyp** die Option **Ich möchte meine Kosten für meine kostenlose Testversion verstehen** aus.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über Kubernetes-Speicher auf einem [Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-storage.md).
- Machen Sie sich mit dem Kubernetes-Netzwerkmodell auf einem [Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-networking.md) vertraut.
- Bereitstellen von [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) im Azure-Portal.
