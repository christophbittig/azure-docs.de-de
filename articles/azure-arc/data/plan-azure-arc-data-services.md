---
title: Planen einer Bereitstellung von Datendiensten mit Azure Arc-Unterstützung
description: In diesem Artikel werden die Überlegungen zur Planung einer Bereitstellung von Datendiensten mit Azure Arc-Unterstützung erläutert.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 15688b89b319b884707e9e6d8faa9a13de3233e0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563338"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Planen einer Bereitstellung von Datendiensten mit Azure Arc-Unterstützung

In diesem Artikel wird beschrieben, wie Sie die Bereitstellung von Azure Arc-fähigen Datendiensten planen.

Wenn Sie mit der Planung der Bereitstellung von Datendiensten mit Azure Arc-Unterstützung beginnen, ist es wichtig, dass Sie Ihre Datenbankworkloads und geschäftlichen Anforderungen für diese Workloads genau verstehen. Berücksichtigen Sie z. B. Faktoren wie Verfügbarkeit, Geschäftskontinuität und Kapazitätsanforderungen für Arbeitsspeicher, CPU und Speicher für die Workloads. Außerdem müssen Sie die Infrastruktur sorgfältig vorbereiten, um die Datenbankworkloads basierend auf Ihren Geschäftsanforderungen zu unterstützen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen Sie sich vergewissern, dass Sie bestimmte Voraussetzungen erfüllt haben und über die notwendigen Hintergrundinformationen verfügen. Um eine erfolgreiche Bereitstellung zu gewährleisten, muss Ihre Infrastrukturumgebung richtig konfiguriert sein, d. h. über die richtige Zugriffsebene und die entsprechende Kapazität für Speicher, CPU und Arbeitsspeicher verfügen. 

Lesen Sie die folgenden Artikel:

- [Dimensionierungsleitfaden](sizing-guidance.md)
- [Speicherkonfiguration](storage-configuration.md)
- [Konnektivitätsmodi und -anforderungen](connectivity.md)

Verifizieren Sie Folgendes:

- Die [`arcdata` CLI-Erweiterung](install-arcdata-extension.md) ist installiert.
- Die anderen [Clienttools](install-client-tools.md) sind installiert.
- Sie haben Zugriff auf den Kubernetes-Cluster.
- Ihre *kubeconfig*-Datei ist konfiguriert. Sie muss auf den Kubernetes-Cluster verweisen, in dem Sie die Bereitstellung ausführen möchten. Führen Sie den folgenden Befehl aus, um den aktuellen Kontext des Clusters zu überprüfen:
   ```console
   kubectl cluster-info
   ```  
- Sie verfügen über ein Azure-Abonnement, dem Ressourcen wie Azure Arc-Datencontroller, SQL Managed Instance-Instanzen oder PostgreSQL Hyperscale-Server mit Azure Arc-Unterstützung zugeordnet sind, die über das Abonnement abgerechnet werden.

Nachdem Sie die Infrastruktur vorbereitet haben, stellen Sie die Datendienste mit Azure Arc-Unterstützung folgendermaßen bereit:
1. Erstellen Sie einen Datencontroller mit Azure Arc-Unterstützung für eine der validierten Distributionen eines Kubernetes-Clusters.
1. Erstellen Sie eine SQL Managed Instance-Instanz mit Azure Arc-Unterstützung und/oder eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung.

> [!CAUTION]
> Einige der Datendienstebenen und -modi sind [allgemein verfügbar](release-notes.md), einige befinden sich in der Vorschau. Es wird empfohlen, allgemein verfügbare Dienste und Vorschauversionen nicht auf demselben Datencontroller zu kombinieren. Wenn Sie allgemein verfügbare Dienste und Vorschauversionen auf demselben Datencontroller kombinieren, können Sie kein direktes Upgrade ausführen. In einem solchen Szenario müssen Sie den Datencontroller und die Datendienste entfernen und neu erstellen, wenn Sie ein Upgrade durchführen möchten.

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>Übersicht: Erstellen eines Datencontrollers mit Azure Arc-Unterstützung

Sie können Datendienste mit Azure Arc-Unterstützung auf verschiedenen Arten von Kubernetes-Clustern erstellen und Sie können Managed Kubernetes-Dienste mit verschiedenen Ansätzen erstellen.

Derzeit gehören die folgenden Kubernetes-Dienste und -Distributionen zu den validierten Komponenten:

- Amazon Elastic Kubernetes Service (Amazon EKS)
- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service in Azure Stack HCI
- Azure Red Hat OpenShift
- Google Kubernetes Engine (GKE)
- Open-Source-Upstreamversion von Kubernetes (in der Regel mit „kubeadm“ bereitgestellt)
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * Die unterstützte Mindestversion von Kubernetes ist v1.19. Weitere Informationen finden Sie im Abschnitt „Bekannte Probleme“ der [Versionshinweise&nbsp; zu Datendiensten mit Azure Arc-Unterstützung](./release-notes.md#known-issues).
> * Die unterstützte Mindestversion von OCP ist 4.7.
> * Wenn Sie Azure Kubernetes Service verwenden, muss die Größe der Workerknoten-VM Ihres Clusters mindestens „Standard_D8s_v3“ betragen, und der Cluster muss Premium-Disks nutzen. 
> * Der Cluster sollte sich nicht über mehrere Verfügbarkeitszonen hinweg ausdehnen. 
> * Weitere Informationen finden Sie im Abschnitt „Bekannte Probleme“ der [Versionshinweise&nbsp; zu Datendiensten mit Azure Arc-Unterstützung](./release-notes.md#known-issues).

Beim Erstellen von Datendiensten mit Azure Arc-Unterstützung müssen Sie unabhängig von der von Ihnen ausgewählten Dienst- oder Verteilungsoption die folgenden Informationen bereitstellen:

- **Name des Datencontrollers**: Ein beschreibender Name für Ihren Datencontroller (z. B. *production-dc* oder *seattle-dc*). Der Name muss den [Kubernetes-Benennungsstandards](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/) entsprechen.

- **Benutzername**: Der Benutzername für den Kibana/Grafana-Administrator.
- **Kennwort**: Das Kennwort für den Kibana/Grafana-Administrator.
- **Name Ihres Kubernetes-Namespace**: Der Name des Kubernetes-Namespace, in dem Sie den Datencontroller erstellen möchten.
- **Konnektivitätsmodus**: Bestimmt den Grad der Konnektivität zwischen Ihrer Datendienstumgebung mit Azure Arc-Unterstützung und Azure. Der *indirekte* Konnektivitätsmodus ist allgemein verfügbar, und der *direkte* Konnektivitätsmodus befindet sich in der Vorschau. Die Auswahl des Konnektivitätsmodus bestimmt die Optionen für die Bereitstellungsmethode. Weitere Informationen finden Sie unter [Konnektivitätsmodi und -anforderungen](./connectivity.md).
- **Azure-Abonnement-ID**: Die Azure-Abonnement-GUID für den Ort, an dem die Datencontrollerressource in Azure erstellt werden soll. Alle SQL Managed Instance-Instanzen und Azure Database for PostgreSQL-Hyperscale-Servergruppen mit Azure Arc-Unterstützung werden ebenfalls in diesem Abonnement erstellt und über dieses abgerechnet.
- **Azure-Ressourcengruppenname**: Der Name der Ressourcengruppe, in der die Datencontrollerressource in Azure erstellt werden soll. Alle SQL Managed Instance-Instanzen und Azure Database for PostgreSQL-Hyperscale-Servergruppen mit Azure Arc-Unterstützung werden ebenfalls in dieser Ressourcengruppe erstellt.
- **Azure-Standort**: Der Azure-Standort, an dem die Metadaten der Datencontrollerressource in Azure gespeichert werden. Eine Liste der verfügbaren Regionen finden Sie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) für die globale Azure-Infrastruktur. Die Metadaten und Abrechnungsinformationen zu den Azure-Ressourcen, die vom bereitgestellten Datencontroller verwaltet werden, werden nur an dem Speicherort in Azure gespeichert, den Sie als Location-Parameter angeben. Wenn Sie im direkten Konnektivitätsmodus bereitstellen, entspricht der Location-Parameter für den Datencontroller dem Standort der gewünschten benutzerdefinierten Standortressource.
- **Informationen zum Azure-Dienstprinzipal**: 
   - Wenn Sie die Bereitstellung im **indirekten** Konnektivitätsmodus ausführen, benötigen Sie Dienstprinzipalinformationen, um Nutzungs- und Metrikdaten hochzuladen. Weitere Informationen finden Sie im Abschnitt „Zuweisen von Rollen zum Dienstprinzipal“ von [Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure](upload-metrics-and-logs-to-azure-monitor.md). 

- **Infrastruktur**: Zu Abrechnungszwecken muss die Infrastruktur angegeben werden, in der Sie Datendienste mit Azure Arc-Unterstützung ausführen. Die Optionen sind:
- `alibaba`
- `aws`
- `azure`
- `gcp`
- `onpremises`
- `other`

## <a name="additional-concepts-for-direct-connectivity-mode"></a>Zusätzliche Konzepte für den direkten Konnektivitätsmodus

Wie unter [Konnektivitätsmodi und -anforderungen](./connectivity.md) beschrieben, können Sie den Azure Arc-Datencontroller entweder im **direkten** oder **indirekten** Konnektivitätsmodus bereitstellen. Die Bereitstellung von Azure Arc-Datendiensten im direkten Verbindungsmodus erfordert zusätzliche Konzepte und Überlegungen:

* Zunächst muss der Kubernetes-Cluster, in dem die Azure Arc-fähigen Datendienste bereitgestellt werden, ein [Azure Arc-fähiger Kubernetes-Cluster](../kubernetes/overview.md) sein. Indem Sie Ihren Kubernetes-Cluster mit Azure verbinden, können Sie Azure Arc-Datendienste direkt aus dem Azure-Portal in Ihrem Cluster bereitstellen und verwalten, Ihre Nutzung, Protokolle und Metriken automatisch in Azure hochladen und mehrere andere Azure-Vorteile nutzen. Informationen dazu finden Sie unter [Verbinden Ihres Clusters mit Azure](../kubernetes/quickstart-connect-cluster.md).

* Nachdem der Kubernetes-Cluster Azure Arc aktiviert ist, stellen Sie die Azure Arc-fähigen Datendienste wie folgt bereit:
   1. Erstellen Sie die Azure Arc-Datendiensterweiterung. Informationen dazu finden Sie unter [Clustererweiterungen in Kubernetes mit Azure Arc-Unterstützung](../kubernetes/conceptual-extensions.md).
   1. Erstellen eines benutzerdefinierten Standorts. Informationen dazu finden Sie unter [„Benutzerdefinierte Speicherorte“ auf Basis von Kubernetes mit Azure Arc-Unterstützung](../kubernetes/conceptual-custom-locations.md).
   1. Erstellen des Azure Arc-Datencontrollers.

   Alle drei Schritte können im Azure-Portal mithilfe des Assistenten zum Erstellen eines Azure Arc-Datencontrollers in einem einzigen Arbeitsschritt ausgeführt werden.

Nachdem der Azure Arc-Datencontroller installiert wurde, können Sie Azure Arc unterstützte Datendienste wie SQL Managed Instance oder PostgreSQL-Hyperscale erstellen und auf sie zugreifen.


## <a name="next-steps"></a>Nächste Schritte

Sie haben mehrere zusätzliche Optionen zum Erstellen des Azure Arc-Datencontrollers:

> **Möchten Sie es selbst ausprobieren?**
> Mit [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in AKS, Amazon EKS oder GKE oder auf einer Azure-VM können Sie sofort loslegen.
>
- [Erstellen eines Datencontrollers im direkten Konnektivitätsmodus über das Azure-Portal](create-data-controller-direct-prerequisites.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über die CLI](create-data-controller-indirect-cli.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über das Azure-Portal mithilfe eines Jupyter Notebooks in Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus mit Kubernetes-Tools wie kubectl oder oc](create-data-controller-using-kubernetes-native-tools.md)
