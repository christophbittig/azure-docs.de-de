---
title: 'Schnellstart: Integrieren in Azure Database for MySQL'
description: Erklärt, wie Sie eine Azure Database for MySQL-Instanz bereitstellen und vorbereiten und dann Pet Clinic für Azure Spring Cloud konfigurieren, um sie mit nur einem Befehl als persistente Datenbank zu verwenden.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 05/13/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 86795c29f5eaaedb1ea08975a69b6afde9d755c1
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271485"
---
# <a name="quickstart-integrate-azure-spring-cloud-with-azure-database-for-mysql"></a>Schnellstart: Integrieren von Azure Spring Cloud mit Azure Database for MySQL

In der Standardkonfiguration verwendet die unter [Schnellstart: Erstellen und Bereitstellen von Apps für Azure Spring Cloud](quickstart-deploy-apps.md) bereitgestellte Pet Clinic eine In-Memory-Datenbank (HSQLDB), die beim Start mit Daten aufgefüllt wird. In diesem Schnellstart wird erklärt, wie Sie eine Azure Database for MySQL-Instanz bereitstellen und vorbereiten und dann Pet Clinic für Azure Spring Cloud konfigurieren, um sie mit nur einem Befehl als persistente Datenbank zu verwenden.

## <a name="variables-preparation"></a>Vorbereitung von Variablen

Sie verwenden die folgenden Werte. Speichern Sie sie in einer Textdatei oder Umgebungsvariablen, um Fehler zu vermeiden. Das Kennwort muss mindestens acht Zeichen lang sein und mindestens einen englischen Großbuchstaben, einen englischen Kleinbuchstaben, eine Zahl und ein nicht alphanumerisches Zeichen (!, $, #, % usw.) enthalten.

```bash
export RESOURCE_GROUP=<resource-group-name> # customize this
export MYSQL_SERVER_NAME=<mysql-server-name> # customize this
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_NAME=<admin-name> # customize this
export MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_NAME}\@${MYSQL_SERVER_NAME}
export MYSQL_SERVER_ADMIN_PASSWORD=<password> # customize this
export MYSQL_DATABASE_NAME=petclinic
```

## <a name="prepare-an-azure-database-for-mysql-instance"></a>Vorbereiten einer Azure Database for MySQL-Instanz

1. Erstellen eines Azure-Datenbank für MySQL-Servers 

    ```azcli
    az mysql server create --resource-group ${RESOURCE_GROUP} \
        --name ${MYSQL_SERVER_NAME} \
        --admin-user ${MYSQL_SERVER_ADMIN_NAME} \
        --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD} \
        --sku-name GP_Gen5_2 \
        --ssl-enforcement Disabled \
        --version 5.7
    ```

1. Lassen Sie den Zugriff durch Azure-Ressourcen zu.

    ```azcli
    az mysql server firewall-rule create --name allAzureIPs \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

1. Lassen Sie für Tests den Zugriff durch Ihren Entwicklungscomputer zu.

    ```azcli
    az mysql server firewall-rule create --name devMachine \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address <ip-address-of-your-dev-machine> \
     --end-ip-address <ip-address-of-your-dev-machine>
    ```

1. Erhöhen Sie das Verbindungstimeout.

    ```azcli
    az mysql server configuration set --name wait_timeout \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value 2147483
    ```

1. Erstellen Sie eine Datenbank auf dem MySQL-Server, und legen Sie die entsprechenden Einstellungen fest.

    ```sql
    // SUBSTITUTE values
    mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
     -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p

    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64379
    Server version: 5.6.39.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> CREATE DATABASE petclinic;
    Query OK, 1 row affected (0.10 sec)

    mysql> CREATE USER 'root' IDENTIFIED BY 'petclinic';
    Query OK, 0 rows affected (0.11 sec)

    mysql> GRANT ALL PRIVILEGES ON petclinic.* TO 'root';
    Query OK, 0 rows affected (1.29 sec)

    mysql> CALL mysql.az_load_timezone();
    Query OK, 3179 rows affected, 1 warning (6.34 sec)

    mysql> SELECT name FROM mysql.time_zone_name;
    ...

    mysql> quit
    Bye
    ```

1. Legen Sie die Zeitzone fest.

    ```azcli
    az mysql server configuration set --name time_zone \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value "US/Pacific"
    ```

## <a name="update-apps-to-use-mysql-database"></a>Aktualisieren von Apps für die Verwendung der MySQL-Datenbank

Um MySQL als Datenbank für die Beispiel-App zu aktivieren, aktualisieren Sie die App *customer-service* mit dem aktiven Profil für MySQL und den Anmeldeinformationen der Datenbank als Umgebungsvariablen.

```azcli
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="update-extra-apps"></a>Aktualisieren zusätzlicher Apps

```azcli
az spring-cloud app update --name api-gateway \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name admin-server \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name vets-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name visits-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="next-steps"></a>Nächste Schritte

* [Binden einer Azure Database for MySQL-Instanz an Ihre Azure Spring Cloud-Anwendung](how-to-bind-mysql.md)
* [Verwenden einer verwalteten Identität zum Herstellen einer Verbindung zwischen Azure SQL-Datenbank und der Azure Spring Cloud-App](./connect-managed-identity-to-azure-sql.md)
