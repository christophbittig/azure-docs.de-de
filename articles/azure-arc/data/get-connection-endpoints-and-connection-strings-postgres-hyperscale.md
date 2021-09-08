---
title: Abrufen von Verbindungsendpunkten und Erstellen von Verbindungszeichenfolgen für eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
titleSuffix: Azure Arc-enabled data services
description: Abrufen von Verbindungsendpunkten und Erstellen von Verbindungszeichenfolgen für eine Arc-fähige PostgreSQL Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 964b7fcca00afb91a457203d2ed53b885a254d5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339396"
---
# <a name="get-connection-endpoints-and-form-the-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Abrufen von Verbindungsendpunkten und Erstellen von Verbindungszeichenfolgen für eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung

In diesem Artikel wird erläutert, wie Sie die Verbindungsendpunkte für Ihre Servergruppe abrufen und Verbindungszeichenfolgen erstellen, die Sie mit Ihren Anwendungen und/oder Tools verwenden können.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Abrufen von Verbindungsendpunkten:

Führen Sie den folgenden Befehl aus:
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Beispiel:
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```

Es wird die Liste der Endpunkte angezeigt: der PostgreSQL-Endpunkt, den Sie zum Verbinden Ihrer Anwendung verwenden, und die verwendeten Datenbank-, Kibana- und Grafana-Endpunkte für die Protokollanalyse und Überwachung. Beispiel: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Verwenden Sie diese Endpunkte für die folgenden Zwecke:
- Erstellen von Verbindungszeichenfolgen und Herstellen einer Verbindung mit Ihren Clienttools oder -anwendungen
- Zugriff auf die Grafana- und Kibana-Dashboards über Ihren Browser

Beispielsweise können Sie den Endpunkt _PostgreSQL-Instanz_ verwenden, um über psql eine Verbindung mit Ihrer Servergruppe herzustellen. Zum Beispiel:
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
> _ERROR: (401)_ 
> _Reason: Unauthorized_
> _HTTP response headers: HTTPHeaderDict({'Date': 'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '_ 
> _Basic realm="Login_ credentials required", Bearer error="invalid_token", error_description="The token is expired"'})_ (Fehlermeldung 401, dass das Token abgelaufen ist) Wenn dies passiert, müssen Sie wie weiter oben erläutert erneut eine Verbindung mit azdata herstellen.

## <a name="from-cli-with-kubectl"></a>Über die CLI mit kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Mit diesen Befehlen wird eine Ausgabe ähnlich der folgenden erzeugt. Diese Informationen können Sie verwenden, um Ihre Verbindungszeichenfolgen zu erstellen:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 

## <a name="form-connection-strings"></a>Erstellen von Verbindungszeichenfolgen:
Verwenden Sie die folgende Tabelle mit Vorlagen für Verbindungszeichenfolgen für Ihre Servergruppe. Sie können sie kopieren und einfügen sowie nach Bedarf anpassen:

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


