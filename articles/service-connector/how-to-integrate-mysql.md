---
title: Integrieren von Azure Database for MySQL mit Service Connector
description: Integrieren von Azure Database for MySQL in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce2839d614298d33f5bb1697c328b258463b6269
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094951"
---
# <a name="integrate-azure-database-for-mysql-with-service-connector"></a>Integrieren von Azure Database for MySQL mit Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Azure Database for MySQL bei Verwendung von Service Connector angezeigt. Möglicherweise können Sie auch in anderen Programmiersprachen eine Verbindung mit Azure Database for MySQL herstellen, ohne Service Connector zu verwenden. Diese Seite zeigt auch den Standardumgebungsvariablennamen und -wert (oder die Spring Boot-Konfiguration) an, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .NET (MySqlConnector) | | | ![Ja-Symbol](./media/green-check.png) | |
| Java (JDBC) | | | ![Ja-Symbol](./media/green-check.png) | |
| Java (Spring Boot) | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js (mysql) | | | ![Ja-Symbol](./media/green-check.png) | |
| Python (mysql-connector-python) | | | ![Ja-Symbol](./media/green-check.png) | |
| Python-Django | | | ![Ja-Symbol](./media/green-check.png) | |
| Go (go-sql-driver for mysql) | | | ![Ja-Symbol](./media/green-check.png) | |
| PHP (mysqli) | | | ![Ja-Symbol](./media/green-check.png) | |
| Ruby (mysql2) | | | ![Ja-Symbol](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Standard-Umgebungsvariablennamen oder Anwendungseigenschaften

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector) 

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | ADO.NET-MySQL-Verbindungszeichenfolge | `Server={MySQLName}.mysql.database.azure.com;Database={MySQLDbName};Port=3306;SSL Mode=Required;User Id={MySQLUsername};Password={TestDbPassword}` |

### <a name="java-jdbc"></a>Java (JDBC)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | JDBC-MySQL-Verbindungszeichenfolge | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required&user={MySQLUsername}&password={Uri.EscapeDataString(TestDbPassword)}` |

### <a name="java---spring-boot-jdbc"></a>Java (Spring Boot)

**Geheimkennwort/Verbindungszeichenfolge**

| Anwendungseigenschaften | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| spring.datatsource.url | Spring Boot-JDBC-Datenbank-URL | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required` |
| spring.datatsource.username | Datenbankbenutzername | `{MySQLUsername}@{MySQLName}` |
| spring.datatsource.password | Datenbankkennwort | `****` |

### <a name="nodejs-mysql"></a>Node.js (mysql) 

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Host-URL der Datenbank  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Benutzername der Datenbank | `MySQLDbName` |
| AZURE_MYSQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_MYSQL_DATABASE | Datenbankname  | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PORT | Portnummer | `3306` |
| AZURE_MYSQL_SSL | SSL-Option | `true` |

### <a name="python-mysql-connector-python"></a>Python (mysql-connector-python)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_MYSQL_HOST | Host-URL der Datenbank  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_NAME | Datenbankname | `{MySQLDbName}` |
| AZURE_MYSQL_PASSWORD | Datenbankkennwort  | `****` |
| AZURE_MYSQL_USER | Benutzername der Datenbank  | `{MySQLUsername}@{MySQLName}` |

### <a name="python-django"></a>Python-Django

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_MYSQL_HOST | Host-URL der Datenbank  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Benutzername der Datenbank | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_MYSQL_NAME | Datenbankname | `MySQLDbName` |


### <a name="go-go-sql-driver-for-mysql"></a>Go (go-sql-driver for mysql)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Go-sql-driver-Verbindungszeichenfolge | `{MySQLUsername}@{MySQLName}:{Password}@tcp({ServerHost}:{Port})/{Database}?tls=true` |


### <a name="php-mysqli"></a>PHP (mysqli)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Host-URL der Datenbank | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Benutzername der Datenbank | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_MYSQL_DBNAME | Datenbankname | `{MySQLDbName}` |
| AZURE_MYSQL_PORT | Portnummer  | `3306` |
| AZURE_MYSQL_FLAG | SSL oder andere Flags | `MYSQLI_CLIENT_SSL` |

### <a name="ruby-mysql2"></a>Ruby (mysql2)

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Host-URL der Datenbank  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Benutzername der Datenbank | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Datenbankkennwort | `****` |
| AZURE_MYSQL_DATABASE | Datenbankname | `{MySQLDbName}` |
| AZURE_MYSQL_SSLMODE | SSL-Option | `required` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
