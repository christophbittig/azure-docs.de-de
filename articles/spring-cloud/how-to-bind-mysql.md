---
title: Binden einer Azure Database for MySQL-Instanz an Ihre Anwendung in Azure Spring Cloud
description: Erfahren Sie, wie Sie eine Azure Database for MySQL-Instanz an Ihre Anwendung in Azure Spring Cloud binden.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 7c48b9eee1ff727cbd3d2ab7204045d962a21f44
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490343"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-application-in-azure-spring-cloud"></a>Binden einer Azure Database for MySQL-Instanz an Ihre Anwendung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

Mit Azure Spring Cloud können Sie ausgewählte Azure-Dienste automatisch an Ihre Anwendungen binden, statt Ihre Spring Boot-Anwendung manuell zu konfigurieren. In diesem Artikel erfahren Sie, wie Sie Ihre Anwendung an Ihre Azure Database for MySQL-Instanz binden.

## <a name="prerequisites"></a>Voraussetzungen

* Eine bereitgestellte Azure Spring Cloud-Instanz
* Ein Azure Database for MySQL-Konto
* Azure CLI

Wenn Sie noch nicht über eine bereitgestellte Azure Spring Cloud-Instanz verfügen, folgen Sie den Anweisungen in [Schnellstart: Starten einer Anwendung in Azure Spring Cloud über das Azure-Portal](./quickstart.md), um Ihre erste Spring Cloud-App bereitzustellen.

## <a name="prepare-your-java-project"></a>Vorbereiten des Java-Projekts

1. Fügen Sie der Datei *pom.xml* Ihres Projekts die folgende Abhängigkeit hinzu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```

1. Entfernen Sie in der Datei *application.properties* alle `spring.datasource.*`-Eigenschaften.

1. Aktualisieren Sie die aktuelle App durch Ausführen von `az spring-cloud app deploy`, oder erstellen Sie eine neue Bereitstellung für diese Änderung, indem Sie `az spring-cloud app deployment create` ausführen.

## <a name="bind-your-app-to-the-azure-database-for-mysql-instance"></a>Binden der App an Ihre Azure Database for MySQL-Instanz

#### <a name="service-binding"></a>[Dienstbindung](#tab/Service-Binding)
1. Notieren Sie sich den Benutzernamen und das Kennwort des Administrators für Ihr Azure Database for MySQL-Konto.

1. Stellen Sie eine Verbindung mit dem Server her, erstellen Sie eine Datenbank mit dem Namen **testdb** von einem MySQL-Client, und erstellen Sie dann ein neues Konto ohne Administratorrechte.

1. Suchen Sie im Azure-Portal auf der Seite Ihres **Azure Spring Cloud**-Diensts nach dem **Anwendungs-Dashboard**, und wählen Sie dann die Anwendung aus, die Sie an Ihre Azure Database for MySQL-Instanz binden möchten.  Dabei handelt es sich um dieselbe Anwendung, die Sie im vorherigen Schritt aktualisiert oder bereitgestellt haben.

1. Wählen Sie **Dienstbindung** und dann die Schaltfläche **Dienstbindung erstellen** aus.

1. Füllen Sie das Formular aus, und wählen Sie als **Bindungstyp** die Option **Azure MySQL** aus. Verwenden Sie den gleichen Datenbanknamen wie zuvor sowie den Benutzernamen und das Kennwort, die Sie im ersten Schritt notiert haben.

1. Starten Sie die App neu. Die Bindung sollte jetzt funktionieren.

1. Um sicherzustellen, dass die Dienstbindung richtig ist, wählen Sie den Bindungsnamen aus, und überprüfen Sie die Details. Das Feld `property` sollte wie folgt aussehen:

    ```properties
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

Im folgenden Terraform-Skript sehen Sie, wie Sie eine Azure Spring Cloud-App mithilfe von Azure Database for MySQL einrichten.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

variable "administrator_login" {
  type        = string
  description = "The MySQL administrator login"
  default     = "myadmin"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "random_password" "password" {
  length           = 32
  special          = true
  override_special = "_%@"
}

resource "azurerm_mysql_server" "database" {
  name                = "mysql-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location

  administrator_login          = var.administrator_login
  administrator_login_password = random_password.password.result

  sku_name                          = "B_Gen5_1"
  storage_mb                        = 5120
  version                           = "5.7"
  auto_grow_enabled                 = true
  backup_retention_days             = 7
  geo_redundant_backup_enabled      = false
  infrastructure_encryption_enabled = false
  public_network_access_enabled     = true
  ssl_enforcement_enabled           = true
  ssl_minimal_tls_version_enforced  = "TLS1_2"
}

resource "azurerm_mysql_database" "database" {
  name                = "mysqldb-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "database" {
  name                = "mysqlfw-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  server_name         = azurerm_mysql_server.database.name
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "example-springcloudapp"
  resource_group_name = azurerm_resource_group.example.name
  service_name        = azurerm_spring_cloud_service.example.name
  is_public           = true
  https_only          = true
}

resource "azurerm_spring_cloud_java_deployment" "example" {
  name                = "default"
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  cpu                 = 2
  memory_in_gb        = 4
  instance_count      = 2
  jvm_options         = "-XX:+PrintGC"
  runtime_version     = "Java_11"

  environment_variables = {
    "spring.datasource.url" : "jdbc:mysql://${azurerm_mysql_server.database.fqdn}:3306/${azurerm_mysql_database.database.name}?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "spring.datasource.username" : "${var.administrator_login}@${azurerm_mysql_server.database.name}"
    "spring.datasource.password" : random_password.password.result
    "spring.jpa.properties.hibernate.dialect" : "org.hibernate.dialect.MySQL5InnoDBDialect"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Anwendung in Azure Spring Cloud an eine Azure Database for MySQL-Instanz binden können. Weitere Informationen zum Binden von Diensten an eine Anwendung finden Sie unter [Binden einer Azure Cosmos DB-Datenbank an eine Anwendung in Azure Spring Cloud](./how-to-bind-cosmos.md).
