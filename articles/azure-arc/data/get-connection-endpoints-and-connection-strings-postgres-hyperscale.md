---
title: Abrufen von Verbindungsendpunkten und Erstellen von Verbindungsstrings für Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe
titleSuffix: Azure Arc-enabled data services
description: Rufen Sie Verbindungsendpunkte ab & erstellen Sie Verbindungszeichenfolgen für Ihre Azure Arc unterstützte PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 35a7a184ae3b699d5ed4d25c1cb6b78269dcea11
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279296"
---
# <a name="get-connection-endpoints--create-the-connection-strings-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Rufen Sie Verbindungsendpunkte ab & erstellen Sie die Verbindungszeichenfolgen für Ihre Azure Arc unterstützte PostgreSQL Hyperscale-Servergruppe

In diesem Artikel wird erläutert, wie Sie die Verbindungsendpunkte für Ihre Servergruppe abrufen und Verbindungszeichenfolgen erstellen, die Sie mit Ihren Anwendungen und/oder Tools verwenden können.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Abrufen von Verbindungsendpunkten:

Führen Sie den folgenden Befehl aus:
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Beispiel:
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace arc --use-k8s
```

Sie gibt die Liste der Endpunkte zurück: den PostgreSQL-Endpunkt, das Dashboard für die Protokollsuche (Kibana) und das Metrikdashboard (Grafana). Beispiel: 

```output
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

Verwenden Sie diese Endpunkte für die folgenden Zwecke:

- Erstellen von Verbindungszeichenfolgen und Herstellen einer Verbindung mit Ihren Clienttools oder -anwendungen
- Zugriff auf die Grafana- und Kibana-Dashboards über Ihren Browser

Beispielsweise können Sie den Endpunkt _PostgreSQL-Instanz_ verwenden, um über psql eine Verbindung mit Ihrer Servergruppe herzustellen:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Das Kennwort des am Endpunkt _PostgreSQL-Instanz_ angegebenen _postgres_-Benutzers ist das Kennwort, das Sie beim Bereitstellen der Servergruppe angegeben haben.


## <a name="from-cli-with-kubectl"></a>Über die CLI mit kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Zum Beispiel:
```azurecli
kubectl get postgresqls/postgres01 -n arc
```

Mit diesen Befehlen wird eine Ausgabe ähnlich der folgenden erzeugt. Diese Informationen können Sie verwenden, um Ihre Verbindungszeichenfolgen zu erstellen:

```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   3/3          12.345.567.89:5432   9d
```

## <a name="form-connection-strings"></a>Erstellen von Verbindungszeichenfolgen

Verwenden Sie die unten angegebenen Verbindungszeichenfolgenbeispiele für Ihre Servergruppe. Kopieren, fügen Sie sie ein und passen Sie sie nach Bedarf an:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Aufskalieren](scale-out-in-postgresql-hyperscale-server-group.md) der Servergruppe (Hinzufügen von Workerknoten).
- Erfahren Sie mehr über das [zentrale Hoch- oder Herunterskalieren](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) der Servergruppe (Vergrößern/Verkleinern von Arbeitsspeicher/virtuellen Kernen).
