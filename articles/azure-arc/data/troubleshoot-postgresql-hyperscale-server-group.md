---
title: Problembehandlung von PostgreSQL Hyperscale-Servergruppen
description: Problembehandlung von PostgreSQL Hyperscale-Servergruppen mit einem Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b828d9e5765a180e1b42de9b96a0ee06eab085f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339390"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Problembehandlung von PostgreSQL Hyperscale-Servergruppen
In diesem Artikel werden einige Methoden beschrieben, die Sie bei der Problembehandlung Ihrer Servergruppe verwenden können. Zusätzlich zu diesem Artikel empfiehlt es sich, nachzulesen, wie Sie [Kibana](monitor-grafana-kibana.md) verwenden, um die Protokolle zu durchsuchen, oder [Grafana](monitor-grafana-kibana.md), um Metriken zu Ihrer Servergruppe zu visualisieren. 

## <a name="getting-more-details-about-the-execution-of-a-cli-command"></a>Abrufen weiterer Details zur Ausführung eines Befehls an der Befehlszeilenschnittstelle
Sie können den Parameter **--debug** jedem Befehl hinzufügen, den Sie an der Befehlszeilenschnittstelle ausführen. Dadurch werden zusätzliche Informationen zur Ausführung dieses Befehls in Ihrer Konsole angezeigt. Es sollte sich als hilfreich erweisen, Detailinformationen zu erhalten, durch die Sie das Verhalten dieses Befehls besser verstehen können.
Beispielsweise könnten Sie Folgendes ausführen
```azurecli
az postgres arc-server create -n postgres01 -w 2 --debug --k8s-namespace <namespace> --use-k8s
```

oder
```azurecli
az postgres arc-server edit -n postgres01 --extension --k8s-namespace <namespace> --use-k8s SomeExtensionName --debug
```

Außerdem können Sie den Parameter „--help“ mit jedem Befehl an der Befehlszeilenschnittstelle verwenden, um Hilfeinformationen und eine Liste mit Parametern für diesen Befehl anzuzeigen. Beispiel:
```azurecli
az postgres arc-server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Sammeln von Protokollen des Datencontrollers und Ihrer Servergruppen
Lesen Sie den Artikel zum [Abrufen von Protokollen für Azure Arc-fähige Datendienste](troubleshooting-get-logs.md).



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktive Problembehandlung mit Jupyter Notebooks in Azure Data Studio

Notebooks können Prozeduren dokumentieren, indem sie Markdowninhalte einschließen, die beschreiben, welche Aktionen wie durchzuführen sind. Notebooks können auch ausführbaren Code bereitstellen, um eine Prozedur zu automatisieren.  Dieses Muster eignet sich für zahlreiche Szenarien: von Standardbetriebsprozeduren bis hin zu Problembehandlungsanleitungen.

Beispielsweise können Sie Problembehandlung für eine PostgreSQL Hyperscale-Servergruppe ausführen, bei der möglicherweise Probleme bei der Verwendung von Azure Data Studio auftreten.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

### <a name="install-tools"></a>Installieren von Tools

Installieren Sie Azure Data Studio, `kubectl` und die Azure-Befehlszeilenschnittstelle (`az`) mit der Erweiterung `arcdata` auf dem Clientcomputer, den Sie zum Ausführen des Notebooks in Azure Data Studio verwenden. Befolgen Sie hierzu die Anweisungen unter [Installieren von Clienttools](install-client-tools.md).

### <a name="update-the-path-environment-variable"></a>Aktualisieren der PATH-Umgebungsvariablen

Stellen Sie sicher, dass diese Tools überall auf diesem Clientcomputer aufgerufen werden können. Aktualisieren Sie z. B. auf einem Windows-Clientcomputer die PATH-Systemumgebungsvariable, und fügen Sie den Ordner hinzu, in dem Sie kubectl installiert haben.

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Anmelden bei Ihrem Kubernetes-Cluster mit kubectl

Zu diesem Zweck können Sie die Beispielbefehle verwenden, die in [diesem](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) Blogbeitrag bereitgestellt werden.
Sie führen Befehle wie die folgenden aus:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Das Notebook zur Problembehandlung

Starten Sie Azure Data Studio, und öffnen Sie das Notebook zur Problembehandlung. 

Implementieren Sie die Schritte, die in [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) beschrieben werden, um die folgenden Aufgaben auszuführen:

1. Herstellen einer Verbindung mit dem Arc-Datencontroller
2. Auswählen Ihrer Postgres-Instanz mit der rechten Maustaste und Auswählen von **[Manage]** (Verwalten)
3. Auswählen des **Dashboards [Diagnose and solve problems]** (Probleme diagnostizieren und beheben).
4. Auswählen des **Links [Troubleshoot]** (Problembehandlung)

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio: Öffnen des PostgreSQL-Notebooks zur Problembehandlung":::

Das Notebook **TSG100: The Azure Arc enabled PostgreSQL Hyperscale troubleshooter** (TSG100: Problembehandlung für Azure Arc-aktiviertes PostgreSQL Hyperscale): :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio: Verwenden des PostgreSQL-Notebooks zur Problembehandlung":::

#### <a name="run-the-scripts"></a>Ausführen der Skripts
Klicken Sie oben auf die Schaltfläche „Run all“ (Alle ausführen), um das Notebook sofort auszuführen, oder durchlaufen Sie das Notebook schrittweise, und führen Sie die einzelnen Codezellen nacheinander aus.

Zeigen Sie die Ausgabe der Ausführung der Codezellen an, um potenzielle Probleme zu ermitteln.

Wir werden dem Notebook im Laufe der Zeit weitere Details hinzufügen, um häufige Probleme zu erkennen und zu beheben.

## <a name="next-step"></a>Nächster Schritt
- Weitere Informationen zum [Abrufen von Protokollen für Azure Arc-fähige Datendienste](troubleshooting-get-logs.md)
- Weitere Informationen zum [Suchen nach Protokollen mit Kibana](monitor-grafana-kibana.md)
- Weitere Informationen zur [Überwachung mit Grafana](monitor-grafana-kibana.md)
- Erstellen eigener Notebooks
