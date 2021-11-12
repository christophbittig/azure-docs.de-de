---
title: Auf- und Abskalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe
description: Auf- und Abskalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 286754a121dc2aabeeacda47dd82dd4f3a1ed83b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564516"
---
# <a name="scale-out-and-in-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Auf- und Abskalieren der PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung durch Hinzufügen weiterer Workerknoten
In diesem Dokument wird erläutert, wie Sie eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung auf- und abskalieren. Dies geschieht anhand eines Szenarios. **Wenn Sie das Szenario nicht durchlaufen möchten und sich lediglich über das Konzept des Aufskalierens informieren möchten, fahren Sie mit dem Absatz [Aufskalieren](#scale-out)** oder [Abskalieren]() fort.

Beim Aufskalieren fügen Sie Ihrer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung Postgres-Instanzen (Postgres Hyperscale-Workerknoten) hinzu.

Beim Abskalieren entfernen Sie Postgres-Instanzen (Postgres Hyperscale-Workerknoten) aus Ihrer PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Erste Schritte
Wenn Sie bereits mit dem Skalierungsmodell von Azure Arc-fähigem PostgreSQL Hyperscale oder Azure Database for PostgreSQL Hyperscale (Citus) vertraut sind, können Sie diesen Absatz überspringen. Falls nicht, empfehlen wir, in der Dokumentation zu Azure Database for PostgreSQL Hyperscale (Citus) zunächst die Seite mit Informationen zu diesem Skalierungsmodell zu lesen. Azure Database for PostgreSQL Hyperscale (Citus) ist die gleiche Technologie, die auch als Dienst in Azure gehostet wird (Platform-as-a-Service, PaaS), anstatt als Teil von Azure Arc-fähigen Data Services angeboten zu werden:
- [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
- [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
- [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
- [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

## <a name="scenario"></a>Szenario
Dieses Szenario bezieht sich auf die PostgreSQL Hyperscale-Servergruppe, die im Dokument [Erstellen einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe](create-postgresql-hyperscale-server-group.md) als Beispiel erstellt wurde.

### <a name="load-test-data"></a>Laden von Testdaten
Im Szenario wird eine Stichprobe von öffentlichen GitHub-Daten verwendet, die auf der [Citus Data-Website](https://www.citusdata.com/) verfügbar sind (Citus Data ist Teil von Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Herstellen einer Verbindung mit einer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe

##### <a name="list-the-connection-information"></a>Auflisten der Verbindungsinformationen
Stellen Sie eine Verbindung mit Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe her, indem Sie zunächst die Verbindungsinformationen abrufen: Das allgemeine Format des Befehls lautet
```azurecli
az postgres arc-server endpoint list -n <server name>  --k8s-namespace <namespace> --use-k8s
```
Zum Beispiel:
```azurecli
az postgres arc-server endpoint list -n postgres01  --k8s-namespace arc --use-k8s
```

Beispielausgabe:

```console
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@12.345.567.89:5432"
        },
        {
          "description": "Log Search Dashboard",
          "endpoint": "https://23.456.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))"
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://34.567.890.12:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Stellen Sie eine Verbindung mit dem Clienttool Ihrer Wahl her.

Führen Sie die folgende Abfrage aus, um sicherzustellen, dass Sie aktuell über mindestens zwei Hyperscale-Workerknoten verfügen, die jeweils einem Kubernetes-Pod entsprechen:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Erstellen eines Beispielschemas
Erstellen Sie zwei Tabellen, indem Sie die folgende Abfrage ausführen:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB ist der JSON-Datentyp im Binärformat in PostgreSQL. Im Datentyp wird ein flexibles Schema in einer einzelnen Spalte und mit PostgreSQL gespeichert. Das Schema verfügt über einen GIN-Index, um jeden darin enthaltenen Schlüssel und Wert zu indizieren. Ein GIN-Index beschleunigt und vereinfacht die direkte Abfrage der Nutzlast mit verschiedenen Bedingungen. Erstellen wir nun eine Reihe von Indizes, bevor wir die Daten laden:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Um Standardtabellen horizontal zu partitionieren, führen Sie eine Abfrage für jede Tabelle aus. Geben Sie die Tabelle an, die horizontal partitioniert werden soll, und den Schlüssel, den wir für die horizontale Partitionierung verwenden möchten. Wir führen eine horizontale Partitionierung sowohl für die Ereignis- als auch die Benutzertabelle nach „user_id“ durch:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Laden von Beispieldaten
Laden der Daten mit COPY... AUS DEM PROGRAM:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Abfragen der Daten
Jetzt messen Sie, wie lange eine einfache Abfrage mit zwei Knoten dauert:

```sql
SELECT COUNT(*) FROM github_events;
```
Notieren Sie sich die Ausführungszeit der Abfrage.


## <a name="scale-out"></a>Aufskalieren
Das allgemeine Format des Befehls zum Aufskalieren lautet:
```azurecli
az postgres arc-server edit -n <server group name> -w <target number of worker nodes> --k8s-namespace <namespace> --use-k8s
```


In diesem Beispiel erhöhen wir die Anzahl der Workerknoten von 2 auf 4, indem wir den folgenden Befehl ausführen:

```azurecli
az postgres arc-server edit -n postgres01 -w 4 --k8s-namespace arc --use-k8s 
```

Während Sie Knoten hinzufügen, wird für die Servergruppe der Status „Ausstehend“ angezeigt. Zum Beispiel:
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

```console
{
    "name": "postgres01",
    "replicas": 1,
    "state": "Updating",
    "workers": 4
  }
```

Sobald die Knoten verfügbar sind, wird der Hyperscale-Shardrebalancer automatisch ausgeführt, um die Daten auf die neuen Knoten zu verteilen. Die Aufskalierung ist ein Onlinevorgang. Während die Knoten hinzugefügt und die Daten neu auf die Knoten verteilt werden, bleiben die Daten für Abfragen verfügbar.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Überprüfen der neuen Zusammensetzung der Servergruppe (optional)
Verwenden Sie eine der folgenden Methoden, um zu überprüfen, ob die Servergruppe jetzt die zusätzlichen Workerknoten verwendet, die Sie hinzugefügt haben.

#### <a name="with-azure-cli-az"></a>Mit Azure CLI (az):

Führen Sie den folgenden Befehl aus:

```azurecli
az postgres arc-server list --k8s-namespace arc --use-k8s
```

Mit dem Befehl wird neben der Anzahl der Workerknoten die Liste der Servergruppen zurückgegeben, die in Ihrem Namespace erstellt wurden. Zum Beispiel:
```console
{
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
```

#### <a name="with-kubectl"></a>Mit kubectl:
Führen Sie den folgenden Befehl aus:
```console
kubectl get postgresqls -n arc
```

Mit dem Befehl wird neben der Anzahl der Workerknoten die Liste der Servergruppen zurückgegeben, die in Ihrem Namespace erstellt wurden. Zum Beispiel:
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.567.89:5432   9d
```
Beachten Sie, dass ein Pod mehr als die Anzahl der Workerknoten vorhanden ist. Der zusätzliche Pod wird zum Hosten der Postgres-Instanz verwendet, die über die Koordinatorrolle verfügt

#### <a name="with-a-sql-query"></a>Mit einer SQL-Abfrage:
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die folgende Abfrage aus:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Zurückkehren zum Szenario

Wenn Sie die Ausführungszeit der Select Count-Abfrage mit dem Beispieldataset vergleichen möchten, verwenden Sie dieselbe Count-Abfrage. Sie kann für die vier Workerknoten verwendet werden, ohne dass Änderungen an der SQL-Anweisung vorgenommen werden müssen.

```sql
SELECT COUNT(*) FROM github_events;
```
Notieren Sie sich die Ausführungszeit.


> [!NOTE]
> Je nach Ihrer Umgebung – wenn Sie z. B. Ihre Testservergruppe mit `kubeadm` auf einer VM mit einem einzelnen Knoten bereitgestellt haben – fällt die Ausführungszeit möglicherweise etwas kürzer aus. Sehen Sie sich die folgenden kurzen Videos an, um sich einen besseren Eindruck von der Art der Leistungsverbesserung zu verschaffen, die Sie mit einer Azure Arc-fähigen PostgreSQL Hyperscale-Instanz erreichen könnten:
>* [Hochleistungs-HTAP mit Azure PostgreSQL Hyperscale (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Entwickeln von HTAP-Anwendungen mit Python und Azure PostgreSQL Hyperscale (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)

## <a name="scale-in"></a>Horizontales Herunterskalieren
Zum Abskalieren (Verringern der Anzahl von Workerknoten in Ihrer Servergruppe) verwenden Sie denselben Befehl wie zum Aufskalieren, geben aber eine geringere Anzahl von Workerknoten an. Bei den entfernten Workerknoten handelt es sich um die Workerknoten, die der Servergruppe zuletzt hinzugefügt wurden. Bei Ausführung dieses Befehls verschiebt das System die Daten aus den Knoten, die entfernt werden, und verteilt sie automatisch auf die verbleibenden Knoten. 

Das allgemeine Format des Befehls zum Abskalieren lautet wie folgt:
```azurecli
az postgres arc-server edit -n <server group name> -w <target number of worker nodes> --k8s-namespace <namespace> --use-k8s
```

Die Abskalierung ist ein Onlinevorgang. Ihre Anwendungen greifen weiterhin ohne Ausfallzeiten auf die Daten zu, während die Knoten entfernt und die Daten auf die verbleibenden Knoten verteilt werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Hochskalieren und Herunterskalieren (von Speicher und virtuellen Kernen) für Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe](scale-up-down-postgresql-hyperscale-server-group-using-cli.md).
- Informieren Sie sich über das Festlegen von Serverparametern in Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe.
- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for Postgres Hyperscale zu profitieren. :
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
