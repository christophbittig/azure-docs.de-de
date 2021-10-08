---
title: Planen der Bereitstellung von Datendiensten mit Azure Arc-Unterstützung
description: Überlegungen zum Planen der Bereitstellung von Azure Arc-fähigen Datendiensten
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d9ae624ddc0a4e5a2f5d9ac38428f4f3c10a01e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832411"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>Planen der Bereitstellung von Azure Arc-fähigen Datendiensten

In diesem Artikel wird beschrieben, wie Sie die Bereitstellung von Azure Arc-fähigen Datendiensten planen.


Für die Bereitstellung von Azure Arc-fähigen Datendiensten wird zunächst ein genaues Verständnis der Datenbankworkloads sowie der geschäftlichen Anforderungen für diese Workloads benötigt. Berücksichtigen Sie z. B. Faktoren wie Verfügbarkeit, Geschäftskontinuität und Kapazitätsanforderungen für Arbeitsspeicher, CPU und Speicher für diese Workloads. Darüber hinaus ist es erforderlich, die Infrastruktur zur Unterstützung dieser Datenbankworkloads basierend auf den Geschäftsanforderungen vorzubereiten.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Azure Arc-fähigen Datendienste bereitstellen, müssen Sie die Voraussetzungen kennen und alle erforderlichen Informationen bereithalten. Die Infrastrukturumgebung muss ordnungsgemäß konfiguriert sein und über die richtige Zugriffsebene sowie über ausreichend Kapazität für Speicher, CPU und Arbeitsspeicher verfügen. Nach Berücksichtigung all dieser Punkte können Sie eine erfolgreiche Bereitstellung gewährleisten.

Lesen Sie die folgenden Abschnitte:
- [Dimensionierungsleitfaden](sizing-guidance.md)
- [Speicherkonfiguration](storage-configuration.md)
- [Konnektivitätsmodi und -anforderungen](connectivity.md)

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind:
- Sie haben die [`arcdata`-CLI-Erweiterung](install-arcdata-extension.md) installiert.
- Sie haben weitere [Clienttools](install-client-tools.md) installiert.
- Sie besitzen Zugriff auf den Kubernetes-Cluster.
- Ihre `kubeconfig`-Datei wurde konfiguriert. Sie sollte auf den Kubernetes-Cluster verweisen, in dem Sie die Bereitstellung ausführen möchten. Überprüfen Sie anhand des folgenden Befehls den aktuellen Kontext des Clusters, in dem Sie die Bereitstellung durchführen möchten:
   ```console
   kubectl cluster-info
   ```
- Sie verfügen über ein Azure-Abonnement, dem Ressourcen wie Azure Arc-Datencontroller, SQL Managed Instance-Instanzen oder PostgreSQL Hyperscale-Server mit Azure Arc-Unterstützung zugeordnet sind, die über das Abonnement abgerechnet werden.

Nachdem die Infrastruktur vorbereitet wurde, stellen Sie die Azure Arc-fähigen Datendiensten folgendermaßen bereit:
1. Erstellen Sie einen Azure Arc-fähigen Datencontrollers für eine der validierten Distributionen eines Kubernetes-Clusters.
1. Erstellen Sie eine SQL Managed Instance-Instanz mit Azure Arc-Unterstützung und/oder eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung.

> [!CAUTION]
> Einige der Datendienstebenen und -modi sind [allgemein verfügbar](release-notes.md), einige befinden sich in der Vorschau. Es wird empfohlen, allgemein verfügbare Dienste und Vorschauversionen nicht auf demselben Datencontroller zu kombinieren. Wenn Sie allgemein verfügbare Dienste und Vorschauversionen auf demselben Datencontroller kombinieren, können Sie kein direktes Upgrade ausführen. In einem solchen Szenario müssen Sie den Datencontroller und die Datendienste entfernen und neu erstellen, wenn Sie ein Upgrade durchführen möchten.

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>Übersicht: Erstellen eines Azure Arc-fähigen Datencontrollers

Azure Arc-fähige Datendienste können unter Verwendung verschiedener Ansätze für zahlreiche verschiedene Arten von Kubernetes-Clustern und verwalteten Kubernetes-Diensten erstellt werden.

Derzeit gehören die folgenden Kubernetes-Dienste und -Distributionen zu den validierten Komponenten:


- AWS Elastic Kubernetes Service (EKS)
- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service in Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- Google Cloud Kubernetes Engine (GKE)
- Open-Source-Upstreamversion von Kubernetes – in der Regel mit kubeadm bereitgestellt
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * Die unterstützte Mindestversion von Kubernetes ist v1.19. Weitere Informationen finden Sie unter [Bekannte Probleme](./release-notes.md#known-issues).
> * Die unterstützte Mindestversion von OCP ist 4.7.
> * Wenn Sie Azure Kubernetes Service verwenden, muss die Größe der Workerknoten-VM Ihres Clusters mindestens **Standard_D8s_v3** betragen, und der Cluster muss **Premium-Datenträger** nutzen. 
> * Der Cluster sollte sich nicht über mehrere Verfügbarkeitszonen hinweg ausdehnen. 
> * Weitere Informationen finden Sie unter [Bekannte Probleme](./release-notes.md#known-issues).

Unabhängig von der gewählten Option müssen Sie während der Erstellung die folgenden Informationen angeben:

- **Data controller name** (Name des Datencontrollers): Ein beschreibender Name für Ihren Datencontroller, z. B. „production-dc“, „seattle-dc“. Der Name muss den [Kubernetes-Benennungsstandards](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/) entsprechen.
- **username** (Benutzer): Ein Benutzername Ihrer Wahl für den Kibana/Grafana-Administrator.
- **password** (Kennwort): Das Kennwort für den Kibana/Grafana-Administrator.
- **Name of your Kubernetes namespace** (Name des Kubernetes-Namespace): Der Name des Kubernetes-Namespace, in dem Sie den Datencontroller erstellen möchten.
- **Connectivity mode** (Konnektivitätsmodus): Der Konnektivitätsmodus bestimmt den Grad der Konnektivität zwischen Ihrer Azure Arc-fähigen Datendienstumgebung und Azure. Derzeit ist nur der indirekte Konnektivitätsmodus allgemein verfügbar. Der direkte Konnektivitätsmodus befindet sich in der Vorschauphase.  Die Auswahl des Konnektivitätsmodus bestimmt die Optionen für die Bereitstellungsmethode.  Weitere Informationen finden Sie unter [Konnektivitätsmodi und -anforderungen](./connectivity.md).
- **Azure subscription ID** (Azure-Abonnement-ID): Die Azure-Abonnement-GUID für den Ort, an dem die Datencontrollerressource in Azure erstellt werden soll.  Alle SQL Managed Instance-Instanzen und PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung werden ebenfalls in diesem Abonnement erstellt und über dieses Abonnement abgerechnet.
- **Azure resource group name** (Azure-Ressourcengruppenname): Der Name der Ressourcengruppe, in der die Datencontrollerressource in Azure erstellt werden soll.  Alle SQL Managed Instance-Instanzen und PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung werden ebenfalls in dieser Ressourcengruppe erstellt.
- **Azure Location** (Azure-Standort): Der Azure-Standort, an dem die Metadaten der Datencontrollerressource in Azure gespeichert werden. Eine Liste der verfügbaren Regionen finden Sie unter [Globale Azure-Infrastruktur/Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc). Die Metadaten und Abrechnungsinformationen zu den Azure-Ressourcen, die vom bereitgestellten Datencontroller verwaltet werden, werden nur an dem Speicherort in Azure gespeichert, den Sie als Location-Parameter angeben. Wenn Sie die Bereitstellung im Direktverbindungsmodus durchführen, entspricht der Location-Parameter für den Datencontroller dem Speicherort der benutzerdefinierten Standortressource, die Sie als Ziel verwenden.
- **Service Principal information** (Dienstprinzipalinformationen): Wie im Artikel zu den [Voraussetzungen für den Upload](upload-metrics-and-logs-to-azure-monitor.md) beschrieben, benötigen Sie die Dienstprinzipalinformationen bei einer Bereitstellung im *direkten* Konnektivitätsmodus während der Erstellung des Azure Arc-Datencontrollers. Für den *indirekten* Konnektivitätsmodus wird der Dienstprinzipal weiterhin benötigt, um manuelle Exporte und Uploads durchzuführen, nachdem der Azure Arc-Datencontroller erstellt wurde.
- **Infrastruktur**: Zu Abrechnungszwecken muss die Infrastruktur angegeben werden, in der Sie Azure Arc-fähige Datendienste ausführen.  Folgende Optionen sind verfügbar: `alibaba`, `aws`, `azure`, `gcp`, `onpremises` oder `other`.

## <a name="additional-concepts-for-direct-connected-mode"></a>Zusätzliche Konzepte für den direkten Verbindungsmodus

Wie im Artikel [Konnektivitätsmodi und -anforderungen](./connectivity.md) beschrieben, kann der Azure Arc-Datencontroller im **direkten** oder **indirekten** Konnektivitätsmodus bereitgestellt werden. Die Bereitstellung von Azure Arc-Datendiensten im **direkten** Verbindungsmodus erfordert das Verständnis einiger zusätzlicher Konzepte und Überlegungen.
Zunächst muss der Kubernetes-Cluster, in dem die Azure Arc-fähigen Datendienste bereitgestellt werden, ein [Azure Arc-fähiger Kubernetes-Cluster](../kubernetes/overview.md) sein. Das Onboarding des Kubernetes-Clusters in Azure Arc bietet Azure-Konnektivität, die für Funktionen wie den automatischen Upload von Nutzungsinformationen, Protokollen, Metriken usw. genutzt wird. Durch die Verbindung Ihres Kubernetes-Clusters mit Azure können Sie außerdem Azure Arc-Datendienste direkt über das Azure-Portal in Ihren Cluster bereitstellen und verwalten.

Für die Verbindungsherstellung zwischen Ihrem Kubernetes-Cluster und Azure sind folgende Schritte erforderlich:
- [Verbinden Ihres Clusters mit Azure](../kubernetes/quickstart-connect-cluster.md)

Nachdem der Kubernetes-Cluster in Azure Arc eingebunden wurde, erfolgt die Bereitstellung von Azure Arc-fähigen Datendiensten für einen Azure Arc-fähigen Kubernetes-Cluster anhand dieser Schritte:
- Erstellen der Arc-Datendiensterweiterung, siehe Informationen zu [Clustererweiterungen](../kubernetes/conceptual-extensions.md)
- Erstellen eines benutzerdefinierten Standorts, siehe Informationen zu [benutzerdefinierten Standorten](../kubernetes/conceptual-custom-locations.md)
- Erstellen des Azure Arc-Datencontrollers

Alle drei Aufgaben können im Azure-Portal mithilfe des Assistenten zum Erstellen eines Azure Arc-Datencontrollers in einem einzigen Arbeitsschritt ausgeführt werden.

Nachdem der Azure Arc-Datencontroller installiert wurde, können Datendienste wie SQL Managed Instance-Instanzen oder PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung erstellt werden.


## <a name="next-steps"></a>Nächste Schritte

Es gibt mehrere Optionen zum Erstellen des Azure Arc-Datencontrollers:

> **Möchten Sie es selbst ausprobieren?**
> Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte unternehmen.
>
- [Erstellen eines Datencontrollers im direkten Konnektivitätsmodus über das Azure-Portal](create-data-controller-direct-prerequisites.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über die CLI](create-data-controller-indirect-cli.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über das Azure-Portal mithilfe eines Jupyter Notebooks in Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus mit Kubernetes-Tools wie kubectl oder oc](create-data-controller-using-kubernetes-native-tools.md)
