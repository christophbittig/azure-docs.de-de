---
title: Integrieren von Azure Database für PostgreSQL mit Service Connector
description: Integrieren von Azure Database for PostgreSQL in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c8cfb866da9a57c54f443558212c5d88269fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021388"
---
# <a name="integrate-azure-database-for-postgresql-with-service-connector"></a>Integrieren von Azure Database für PostgreSQL mit Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Azure Database für PostgreSQL mithilfe des Service Connectors angezeigt. Möglicherweise können Sie weiterhin eine Verbindung mit Azure Database für PostgreSQL in anderen Programmiersprachen herstellen, ohne den Service Connector zu verwenden. Auf dieser Seite werden auch der Name und Wert der Standardumgebungsvariablen (oder Spring Boot Konfiguration) angezeigt, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net (ADO.NET) | | | ![Ja-Symbol](./media/green-check.png) | |
| Java (JDBC) | | | ![Ja-Symbol](./media/green-check.png) | |
| Java (Spring Boot) | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js (pg) | | | ![Ja-Symbol](./media/green-check.png) | |
| Python (psycopg2) | | | ![Ja-Symbol](./media/green-check.png) | |
| Python-Django | | | ![Ja-Symbol](./media/green-check.png) | |
| Go (pg) | | | ![Ja-Symbol](./media/green-check.png) | |
| PHP (nativ) | | | ![Ja-Symbol](./media/green-check.png) | |
| Ruby (ruby-pg) | | | ![Ja-Symbol](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Standard-Umgebungsvariablennamen oder Anwendungseigenschaften

### <a name="net-adonet"></a>.NET (ADO.NET) 

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | ADO.NET PostgreSQL connection string | `Server={your-postgres-server-name}.postgres.database.azure.com;Database={database-name};Port=5432;Ssl Mode=Require;User Id={username}@{servername};Password=****;` |

### <a name="java-jdbc"></a>Java (JDBC)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | JDBC PostgreSQL connection string | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require&user={username}%40{servername}l&password=****` |

### <a name="java---spring-boot-jdbc"></a>Java (Spring Boot)

**Geheimkennwort/Verbindungszeichenfolge**

| Anwendungseigenschaften | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| spring.datatsource.url | DATABASE URL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require` |
| spring.datatsource.username | Datenbankbenutzername | `{username}@{servername}` |
| spring.datatsource.password | Datenbankkennwort | `****` |

### <a name="nodejs-pg"></a>Node.js (pg) 

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
|---------|---------|---------|
| AZURE_POSTGRESQL_HOST | URL des Datenbankhosts | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Datenbankbenutzername | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_POSTGRESQL_DATABASE | Datenbankname | `{database-name}` |
| AZURE_POSTGRESQL_PORT | Portnummer  | `5432` |
| AZURE_POSTGRESQL_PORT | SSL-Option  | `true` |

### <a name="python-psycopg2"></a>Python (psycopg2)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | psycopg2 connection string | `dbname={database-name} host={your-postgres-server-name}.postgres.database.azure.com port=5432 sslmode=require user={username}@{servername} password=****` |

### <a name="python-django"></a>Python-Django

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_HOST | URL des Datenbankhosts | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Datenbankbenutzername | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_POSTGRESQL_NAME | Datenbankname | `{database-name}` |


### <a name="go-pg"></a>Go (pg)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Go pg-Verbindungszeichenfolge | `host={your-postgres-server-name}.postgres.database.azure.com dbname={database-name} sslmode=require user={username}@{servername} password=****` |


### <a name="php-native"></a>PHP (nativ)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Native Postgres Verbindungszeichenfoge für PHP | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=requrie user={username}@{servername} password=****` |

### <a name="ruby-ruby-pg"></a>Ruby (ruby-pg)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Ruby pg connection string | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=require user={username}@{servername} password=****` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
