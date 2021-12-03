---
title: Anzeigen der Konfiguration einer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung
titleSuffix: Azure Arc-enabled data services
description: Anzeigen der Konfiguration einer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 0789e9bb28868ce4d75aa48beb956fcb4dd74031
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345801"
---
# <a name="show-the-configuration-of-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Anzeigen der Konfiguration einer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung

In diesem Artikel wird erläutert, wie Sie die Konfiguration Ihrer Servergruppe(n) anzeigen. Dies geschieht durch die Beantwortung einiger Fragen, die Sie sich möglicherweise bereits selbst gestellt haben. Manchmal gibt es mehrere gültige Antworten. In diesem Artikel werden die gängigsten oder nützlichsten Artikel erläutert. Diese Fragen werden nach Themen gruppiert:

- Aus Sicht von Kubernetes
- Aus Sicht eines Datendiensts mit Azure Arc-Unterstützung

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Aus Sicht von Kubernetes

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-pods-are-they-using"></a>Welche Postgres-Servergruppen werden bereitgestellt, und wie viele Pods verwenden sie?

Listen Sie die Kubernetes-Ressourcen vom Typ „Postgres“ auf. Führen Sie den folgenden Befehl aus:

```console
kubectl get postgresqls -n <namespace>
```

In der Ausgabe dieses Befehls wird die Liste der erstellten Servergruppen angezeigt. Für jede Servergruppe wird die Anzahl der Pods angegeben. Zum Beispiel:

```output
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          20.101.12.221:5432   12d
```

Dieses Beispiel zeigt, dass eine Servergruppe erstellt wird. Sie wird auf fünf Pods ausgeführt: einem Koordinator und vier Workern.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Welche Pods werden von Azure Arc-aktivierten PostgreSQL Hyperscale-Servergruppen verwendet?

Führen Sie Folgendes aus:

```console
kubectl get pods -n <namespace>
```

Der Befehl gibt die Liste der Pods zurück. Die Pods, die von Ihren Servergruppen verwendet werden, werden basierend auf den Namen angezeigt, die Sie diesen Servergruppen gegeben haben. Zum Beispiel:

```console
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-4jrtl   1/1     Running   0          12d
control-kz8gh        2/2     Running   0          12d
controldb-0          2/2     Running   0          12d
logsdb-0             3/3     Running   0          12d
logsui-qjkgz         3/3     Running   0          12d
metricsdb-0          2/2     Running   0          12d
metricsdc-4jslw      2/2     Running   0          12d
metricsdc-4tl2g      2/2     Running   0          12d
metricsdc-fkxv2      2/2     Running   0          12d
metricsdc-hs4h5      2/2     Running   0          12d
metricsdc-tvz22      2/2     Running   0          12d
metricsui-7pcch      2/2     Running   0          12d
postgres01c0-0       3/3     Running   0          2d19h
postgres01w0-0       3/3     Running   0          2d19h
postgres01w0-1       3/3     Running   0          2d19h
postgres01w0-2       3/3     Running   0          2d19h
postgres01w0-3       3/3     Running   0          2d19h
```

### <a name="what-pod-is-used-for-what-role-in-the-server-group"></a>Welcher Pod wird für welche Rolle in der Servergruppe verwendet?

Jeder Podname, der mit dem Suffix `c` versehen ist, stellt einen Koordinatorknoten dar. Jeder Knotenname, dem ein `w` angehängt ist, ist ein Workerknoten, z. B. die fünf Pods, die die Servergruppe hosten:

- `postgres01c0-0` ist der Koordinatorknoten.
- `postgres01w0-0` ist ein Workerknoten.
- `postgres01w0-1` ist ein Workerknoten.
- `postgres01w0-2` ist ein Workerknoten.
- `postgres01w0-3` ist ein Workerknoten.

Sie können vorerst das Zeichen `0` ignorieren, das hinter `c` und `w` angezeigt wird (ServerGroupName`c0`-x oder ServerGroupName`w0`-x). Es wird eine Notation verwendet, wenn das Produkt Hochverfügbarkeitserfahrungen bietet.

### <a name="what-is-the-status-of-the-pods"></a>Wie lautet der Status der Pods?

Führen Sie `kubectl get pods -n <namespace>` aus, und sehen Sie sich die Spalte `STATUS` an.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Welche persistenten Volumeansprüche (Persistent Volume Claims, PVCs) werden verwendet?

Um zu verstehen, welche PVCs allgemein und welche PVCs für Daten, Protokolle und Sicherungen verwendet werden, führen Sie Folgendes aus:

```console
kubectl get pvc -n <namespace>
```

Standardmäßig gibt das Präfix des Namens eines PVCs seine Verwendung an:

- `backups-`...: ist ein für Sicherungen verwendeter PVC
- `data-`...: ist ein für Datendateien verwendeter PVC
- `logs-`...: ist ein für Transaktionsprotokolle/WAL-Dateien verwendeter PVC

Zum Beispiel:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-server-group"></a>Wie viel Arbeitsspeicher und wie viele virtuelle Kerne werden verwendet, und welche Erweiterungen wurden für eine Servergruppe erstellt?

Verwenden Sie kubectl, um Postgres-Ressourcen zu beschreiben. Dazu benötigen Sie deren Art (Name der Kubernetes-Ressource (CRD) für Postgres in Azure Arc) sowie den Namen der Servergruppe.

Das allgemeine Format des Befehls lautet folgendermaßen:

```console
kubectl describe <CRD name>/<server group name> -n <namespace>
```

Zum Beispiel:

```console
kubectl describe postgresql/postgres01 -n arc
```

Mit diesem Befehl wird die Konfiguration der Servergruppe angezeigt:

```output
Name:         postgres01
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1beta2
Kind:         PostgreSql
Metadata:
  Creation Timestamp:  2021-10-13T01:09:25Z
  Generation:          29
  Managed Fields:
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:dev:
        f:engine:
          .:
          f:extensions:
          f:version:
        f:scale:
          .:
          f:replicas:
          f:workers:
        f:scheduling:
          .:
          f:default:
            .:
            f:resources:
              .:
              f:requests:
                .:
                f:memory:
          f:roles:
            .:
            f:coordinator:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
            f:worker:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
        f:services:
          .:
          f:primary:
            .:
            f:port:
            f:type:
        f:storage:
          .:
          f:backups:
            .:
            f:volumes:
          f:data:
            .:
            f:volumes:
          f:logs:
            .:
            f:volumes:
    Manager:      OpenAPI-Generator
    Operation:    Update
    Time:         2021-10-22T22:37:51Z
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:IsValid:
      f:spec:
        f:scale:
          f:syncReplicas:
      f:status:
        .:
        f:logSearchDashboard:
        f:metricsDashboard:
        f:observedGeneration:
        f:podsStatus:
        f:primaryEndpoint:
        f:readyPods:
        f:state:
    Manager:         unknown
    Operation:       Update
    Time:            2021-10-22T22:37:53Z
  Resource Version:  1541521
  UID:               23565e53-2e7a-4cd6-8f80-3a79397e1d7a
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
    Roles:
      Coordinator:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
      Worker:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
  Services:
    Primary:
      Port:  5432
      Type:  LoadBalancer
  Storage:
    Backups:
      Volumes:
        Size:  5Gi
    Data:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
    Logs:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
Status:
  Log Search Dashboard:  https://12.235.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
  Metrics Dashboard:     https://12.346.578.99:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01
  Observed Generation:   29
  Pods Status:
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-1
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01c0-0
    Role:                    coordinator
    Conditions:
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-3
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-0
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-2
    Role:                    worker
  Primary Endpoint:          20.101.12.221:5432
  Ready Pods:                5/5
  Running Version:           v1.1.0_2021-10-12_patching_0bcb7bcaf
  State:                     Ready
Events:                      <none>
```

#### <a name="interpret-the-configuration-information"></a>Interpretieren der Konfigurationsinformationen

Benennen wir einige bestimmte relevante Punkte in der Beschreibung der oben gezeigten `servergroup`. Welche Informationen erhalten wir zu dieser Servergruppe?

- Es handelt sich bei ihr um Version 12 von Postgres, und sie führt eine Citus-Erweiterung aus:

   ```output
   Spec:
     Dev:  false
     Engine:
       Extensions:
         Name:   citus
       Version:  12
   ```

- Sie wurde am 13. Oktober 2021 erstellt:

   ```output
     Metadata:
     Creation Timestamp:  2021-10-13T01:09:25Z
   ```

- Sie verwendet vier Workerknoten:

   ```output
        Scale:
          Replicas:       1
          Sync Replicas:  0
          Workers:        4
   ```

- Ressourcenkonfiguration: In diesem Beispiel werden dem Koordinator und den Workern 256Mi Arbeitsspeicher garantiert. Der Koordinator und die Workerknoten können nicht mehr als 1Gi Arbeitsspeicher verwenden. Sowohl der Koordinator als auch die Worker erhalten garantiert einen virtuellen Kern und können nicht mehr als zwei virtuelle Kerne nutzen.

   ```console
        Scheduling:
       Default:
         Resources:
           Requests:
             Memory:  256Mi
       Roles:
         Coordinator:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
         Worker:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
   ```

- Wie lautet der Status der Servergruppe? Ist sie für meine Anwendungen verfügbar?

   Ja, alle Pods (Koordinatorknoten und alle vier Workerknoten sind bereit)

   ```console
   Ready Pods:                5/5
   ```

## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Aus Sicht eines Datendiensts mit Azure Arc-Unterstützung

Verwenden Sie Az-CLI-Befehle.

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-workers-are-they-using"></a>Welche Postgres-Servergruppen werden bereitgestellt, und wie viele Worker verwenden sie?

Führen Sie den folgenden Befehl aus.

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s
   ```

Er listet die bereitgestellten Servergruppen auf.

   ```output
   [
     {
       "name": "postgres01",
       "replicas": 1,
       "state": "Ready",
       "workers": 4
     }
   ]
   ```

Er zeigt außerdem an, wie viele Workerknoten die Servergruppe verwendet. Jeder Workerknoten wird auf einem Pod bereitgestellt, dem Sie einen Pod hinzufügen müssen, der zum Hosten des Koordinatorknotens verwendet wird.

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Wie viel Arbeitsspeicher und wie viele virtuelle Kerne werden verwendet, und welche Erweiterungen wurden für eine Gruppe erstellt?

Führen Sie einen der folgenden Befehlen aus.

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

Gibt die Informationen in einem Format und als Inhalt zurück, die der von kubectl zurückgegebenen Ausgabe ähnlich sind. Verwenden Sie das Tool Ihrer Wahl, um mit dem System zu interagieren.

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu den Konzepten von Azure Arc-fähigem PostgreSQL Hyperscale](concepts-distributed-postgres-hyperscale.md)
- [Informationen zum horizontalen Hochskalieren einer Servergruppe (Hinzufügen von Workerknoten)](scale-out-in-postgresql-hyperscale-server-group.md)
- [Informationen zum zentralen Hoch- oder Herunterskalieren einer Servergruppe (Erhöhen oder Verringern des Arbeitsspeichers und/oder der virtuellen Kerne)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Informationen zur Speicherkonfiguration](storage-configuration.md)
- [Informationen zum Überwachen einer Datenbankinstanz](monitor-grafana-kibana.md)
- [Verwenden von PostgreSQL-Erweiterungen in Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Konfigurieren der Sicherheit für Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe](configure-security-postgres-hyperscale.md)
