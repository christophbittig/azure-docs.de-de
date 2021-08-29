---
title: Azure SQL und SQL Server
description: Dieser Artikel enthält Informationen dazu, wie Sie den Connector zum Verschieben von Daten zwischen Azure MS SQL und serverlosen Apache Spark-Pools verwenden.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 05/19/2020
ms.custom: has-adal-ref
ms.openlocfilehash: da64e27b5ae4675b313007c9b185db9194607f2c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428828"
---
# <a name="azure-sql-database-and-sql-server-connector-for-apache-spark"></a>Azure SQL-Datenbank und SQL Server-Connector für Apache Spark
Der Apache Spark-Connector für Azure SQL-Datenbank und SQL Server ermöglicht es diesen Datenbanken, als Eingabedatenquellen und Ausgabedatensenken für Apache Spark-Aufträge zu fungieren. So können Echtzeittransaktionsdaten in der Big Data-Analyse genutzt und Ergebnisse für Ad-hoc-Abfragen oder Berichterstellung dauerhaft gespeichert werden.

Im Vergleich zum integrierten JDBC-Connector bietet dieser Connector die Möglichkeit, Daten per Massenvorgang in SQL-Datenbanken einzufügen. Dies führt zu einer erheblichen Leistungssteigerung: Daten können gegenüber einer zeilenweisen Einfügung 10- bis 20-mal schneller eingefügt werden. Der Spark-Connector für SQL Server und Azure SQL-Datenbank unterstützt auch Azure AD-[Authentifizierung](/sql/connect/spark/connector#azure-active-directory-authentication) (Azure Active Directory), sodass Sie über Azure Synapse Analytics eine sichere Verbindung mit Ihren Azure SQL-Datenbank-Instanzen herstellen können. 

In diesem Artikel wird beschrieben, wie Sie die DataFrame-API verwenden, um mithilfe des MS SQL-Connectors eine Verbindung mit SQL-Datenbanken herzustellen. Dieser Artikel enthält ausführliche Beispiele für die Verwendung der PySpark-API. Alle unterstützten Argumente und Beispiele zum Herstellen einer Verbindung mit SQL-Datenbanken mithilfe des MS SQL-Connectors finden Sie im Artikel mit [SQL-Beispielen für Azure-Daten](https://github.com/microsoft/sql-server-samples#azure-data-sql-samples-repository).


  
## <a name="connection-details"></a>Verbindungsdetails
In diesem Beispiel verwenden Sie die Microsoft Spark-Hilfsprogramme, um das Abrufen von Geheimnissen aus einer vorkonfigurierten Key Vault-Instanz zu vereinfachen. Weitere Informationen zu Microsoft Spark-Hilfsprogrammen finden Sie unter [Einführung in Microsoft Spark-Hilfsprogramme](../microsoft-spark-utilities.md).

```python
servername = "<< server name >>"
dbname = "<< database name >>"
url = servername + ";" + "databaseName=" + dbname + ";"
dbtable = "<< table name >> "
user = "<< username >>" 
password = mssparkutils.credentials.getSecret('azure key vault name','secret name')
```

> [!NOTE]
> Derzeit wird für den Azure SQL-Connector weder ein verknüpfter Dienst noch AAD-Passthrough unterstützt.

## <a name="use-the-azure-sql-and-sql-server-connector"></a>Verwenden des Azure SQL- und SQL Server-Connectors

### <a name="read-data"></a>Lesen von Daten
```python
#Read from SQL table using MS SQL Connector
print("read data from SQL server table  ")
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("user", user) \
        .option("password", password).load()

jdbcDF.show(5)
```

### <a name="write-data"></a>Schreiben von Daten
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", dbtable) \
    .option("user", user) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("MSSQL Connector write failed", error)

print("MSSQL Connector write(overwrite) succeeded  ")
```
### <a name="append-data"></a>Anfügen von Daten
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="azure-active-directory-authentication"></a>Authentifizierung über Azure Active Directory

### <a name="python-example-with-service-principal"></a>Python-Beispiel mit Dienstprinzipal
```python
import adal

# Located in App Registrations from Azure Portal
tenant_id = "<< tenant id >> "

# Located in App Registrations from Azure Portal
resource_app_id_url = "https://database.windows.net/"

# Authority
authority = "https://login.windows.net/" + tenant_id

context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>Python-Beispiel mit Active Directory-Kennwort
```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zum SQL Server- und Azure SQL-Connector](/sql/connect/spark/connector)
- [Anzeigen von SQL-Beispielen für Azure-Daten](https://github.com/microsoft/sql-server-samples)
