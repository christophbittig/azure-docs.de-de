---
title: 'Tutorial: Überwachen von Apache Spark-Anwendungsmetriken mit Prometheus und Grafana'
description: 'Tutorial: Erfahren Sie, wie Sie die Apache Spark-Anwendungsmetriklösung in einem Azure Kubernetes Service-Cluster (AKS) bereitstellen. Außerdem erhalten Sie Informationen zur Integration der Grafana-Dashboards.'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 1c26ed72a804335a675a2456b2908f582848f5a7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399084"
---
# <a name="monitor-apache-spark-applications-metrics-with-prometheus-and-grafana"></a>Überwachen von Apache Spark-Anwendungsmetriken mit Prometheus und Grafana

## <a name="overview"></a>Übersicht

In diesem Tutorial erfahren Sie, wie Sie die Lösung für Apache Spark-Anwendungsmetriken in einem Azure Kubernetes Service-Cluster (AKS) bereitstellen. Außerdem erhalten Sie Informationen zur Integration der Grafana-Dashboards.

Verwenden Sie diese Lösung, um die Apache Spark-Metrikdaten in Quasi-Echtzeit zu erfassen und abzufragen. Über die integrierten Grafana-Dashboards können Sie Ihre Apache Spark-Anwendung diagnostizieren und überwachen. Der Quellcode und die Konfigurationen wurden in Open-Source-Form auf GitHub freigegeben.

## <a name="prerequisites"></a>Voraussetzungen

1.  [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
2.  [Helm-Client 3.30 und höher](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)

Alternativ können Sie auch [Azure Cloud Shell](https://shell.azure.com/) verwenden. Darin sind die Azure CLI, der Helm-Client und kubectl bereits integriert.

## <a name="log-in-to-azure"></a>Anmelden an Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Erstellen einer Azure Kubernetes Service-Instanz (AKS)

Verwenden Sie den Azure CLI-Befehl, um einen Kubernetes-Cluster in Ihrem Abonnement zu erstellen.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Hinweis: Dieser Schritt kann übersprungen werden, wenn Sie bereits über einen AKS-Cluster verfügen.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Erstellen eines Dienstprinzipals und Erteilen einer Berechtigung für den Synapse-Arbeitsbereich

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Das Ergebnis sollte wie folgt aussehen:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Notieren Sie sich die appId, das Kennwort und die tenantID.

[![Screenshot zum Erteilen der Berechtigung, SRBAC](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Melden Sie sich als Synapse-Administrator beim [Azure Synapse Analytics-Arbeitsbereich](https://web.azuresynapse.net/) an.

2. Wählen Sie in Synapse Studio im linken Bereich **Verwalten > Zugriffssteuerung** aus.

3. Klicken Sie links oben auf die Schaltfläche „Hinzufügen“, um **eine Rollenzuweisung hinzuzufügen**.

4. Wählen Sie als Bereich **Arbeitsbereich** aus.

5. Wählen Sie als Rolle die Option **Operator von Synapse-Computeressourcen** aus.

6. Geben Sie unter „Benutzer auswählen“ Ihren **<Dienstprinzipalnamen>** ein, und klicken Sie auf Ihren Dienstprinzipal.

7. Klicken Sie auf **Anwenden**. (Warten Sie drei Minuten, bis die Berechtigung in Kraft tritt.)

> [!NOTE]
> Stellen Sie sicher, dass Ihr Dienstprinzipal mindestens über die Rolle „Leser“ in Ihrem Synapse-Arbeitsbereich verfügt. Navigieren Sie im Azure-Portal zur Registerkarte „Zugriffssteuerung (IAM)“, und überprüfen Sie die Berechtigungseinstellungen.

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Installieren von Connector, Prometheus-Server und Grafana-Dashboard

1. Fügen Sie dem Helm-Client das Repository „synapse-charts“ hinzu.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Installieren von Komponenten über den Helm-Client:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Name des Synapse-Arbeitsbereichs.
 - subscription_id: Abonnement-ID für den Synapse-Arbeitsbereich.
 - workspace_resource_group_name: Name der Ressourcengruppe für den Synapse-Arbeitsbereich.
 - tenant_id: Mandanten-ID für den Synapse-Arbeitsbereich.
 - service_principal_app_id: „appId“ des Dienstprinzipals.
 - service_principal_password: das von Ihnen erstellte Dienstprinzipalkennwort.

## <a name="log-in-to-grafana"></a>Anmelden an Grafana

Fordern Sie das Standardkennwort und die Adresse von Grafana an. Sie können das Kennwort in den Grafana-Einstellungen ändern.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Rufen Sie die Dienst-IP-Adresse ab. Kopieren Sie die externe IP-Adresse, und fügen Sie sie in den Browser ein. Melden Sie sich mit dem Benutzernamen „admin“ und dem Kennwort an.

## <a name="use-grafana-dashboards"></a>Verwenden von Grafana-Dashboards

Suchen Sie in der oberen linken Ecke der Grafana-Seite nach dem Synapse-Dashboard („Start“ > „Synapse Workspace/Synapse Application“). Führen Sie einen Beispielcode in Synapse Studio aus, und warten Sie einige Sekunden, bis die Metriken per Pull abgerufen wurden.

Sie können auch die Dashboards „Synapse-Arbeitsbereich/Arbeitsbereich“ und „Synapse-Arbeitsbereich/Apache Spark-Pools“ verwenden, um einen Überblick über Ihren Arbeitsbereich und Ihre Apache Spark-Pools zu erhalten.

## <a name="uninstall"></a>Deinstallieren

Entfernen Sie die Komponenten folgendermaßen über den Helm-Befehl.

```bash
helm delete <release_name> -n <namespace>
```

Löschen Sie den AKS-Cluster.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Einführung in Komponenten

Azure Synapse Analytics stellt ein [Helm-Diagramm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) bereit, das auf dem Prometheus Operator und dem Synapse-Prometheus-Connector basiert. Das Helm-Diagramm enthält den Prometheus-Server, den Grafana-Server und Grafana-Dashboards für Apache Spark-Metriken auf Anwendungsebene. Mit [Prometheus](https://prometheus.io/), einem beliebten Open-Source-Überwachungssystem, können Sie diese Metriken in Quasi-Echtzeit erfassen und [Grafana](https://github.com/grafana/grafana) zur Visualisierung verwenden.

### <a name="synapse-prometheus-connector"></a>Synapse-Prometheus-Connector

Der Synapse-Prometheus-Connector unterstützt Sie beim Herstellen einer Verbindung zwischen dem Apache Spark-Pool in Azure Synapse und dem Prometheus-Server. Er implementiert Folgendes:

1.  Authentifizierung: Hierbei handelt es sich um eine auf AAD basierende Authentifizierung, die das AAD-Token des Dienstprinzipals für die Anwendungsermittlung, die Erfassung von Metriken und andere Funktionen automatisch aktualisieren kann.
2.  Apache Spark-Anwendungsermittlung: Wenn Sie Anwendungen im Zielarbeitsbereich übermitteln, kann der Synapse-Prometheus-Connector diese Anwendungen automatisch ermitteln.
3.  Apache Spark-Anwendungsmetadaten: Grundlegende Anwendungsinformationen werden erfasst und in Prometheus exportiert.

Der Synapse-Prometheus-Connector wird als Docker-Image freigegeben, das in [Microsoft Container Registry](https://github.com/microsoft/containerregistry) gehostet wird. Er liegt im Open-Source-Format unter [Azure Synapse Apache Spark-Anwendungsmetriken](https://github.com/microsoft/azure-synapse-spark-metrics) vor.

### <a name="prometheus-server"></a>Prometheus-Server

Prometheus ist ein Open-Source-Toolkit mit Überwachungs- und Warnfunktionen. Prometheus ist ein graduiertes Projekt der Cloud Native Computing Foundation (CNCF) und stellt den De-Facto-Standard für die cloudbasierte Überwachung dar. Prometheus kann Sie bei der Erfassung, Abfrage und Speicherung gewaltiger Mengen an Zeitreihendaten unterstützen und lässt sich problemlos in Grafana integrieren. In dieser Lösung stellen wir die Prometheus-Komponente basierend auf dem Helm-Diagramm bereit.

### <a name="grafana-and-dashboards"></a>Grafana und Dashboards

Grafana ist eine Open Source-Software für Visualisierungs- und Analysefunktionen. Damit können Sie Ihre Metriken abfragen, visualisieren und untersuchen sowie Warnungen erstellen. Azure Synapse Analytics bietet eine Reihe von Grafana-Standarddashboards für die Visualisierung von Apache Spark-Metriken auf Anwendungsebene.

Das Dashboard „Synapse Workspace/Workspace“ bietet für alle Apache Spark-Pools, Anwendungsindikatoren, CPU-Kerne usw. eine Ansicht auf Arbeitsbereichsebene.

[![Screenshot mit Dashboard für Arbeitsbereich](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Das Dashboard „Synapse-Arbeitsbereich/Apache Spark-Pools“ enthält die Metriken von Apache Spark-Anwendungen, die im ausgewählten Apache Spark-Pool während des betreffenden Zeitraums ausgeführt werden.

[![Screenshot mit Dashboard für Spark-Pool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Das Dashboard „Synapse-Arbeitsbereich/Apache Spark-Anwendung“ enthält die ausgewählte Apache Spark-Anwendung.

[![Screenshot mit Dashboard für Anwendung](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Die obigen Dashboardvorlagen wurden unter [Azure Synapse Apache Spark-Anwendungsmetriken](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards) im Open-Source-Format bereitgestellt.
