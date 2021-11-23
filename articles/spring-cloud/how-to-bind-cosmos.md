---
title: Binden von Azure Cosmos DB an Ihre Anwendung in Azure Spring Cloud
description: Erfahren Sie, wie Sie Azure Cosmos DB an Ihre Anwendung in Azure Spring Cloud binden.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 6d9a5007ba4b5bf2c5138ab584e774d81f23bd03
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493626"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-application-in-azure-spring-cloud"></a>Binden einer Azure Cosmos DB-Datenbank an Ihre Anwendung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

Mit Azure Spring Cloud können Sie ausgewählte Azure-Dienste automatisch an Ihre Anwendungen binden, statt Ihre Spring Boot-Anwendungen manuell zu konfigurieren. In diesem Artikel wird gezeigt, wie Sie Ihre Anwendung an eine Azure Cosmos DB-Datenbank binden.

Voraussetzungen:

* Eine bereitgestellte Azure Spring Cloud-Instanz. Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](./quickstart.md).
* Ein Azure Cosmos DB-Konto mit mindestens der Berechtigungsebene „Mitwirkender“

## <a name="prepare-your-java-project"></a>Vorbereiten des Java-Projekts

1. Fügen Sie eine der folgenden Abhängigkeiten in der Datei „pom.xml“ Ihrer Anwendung hinzu. Wählen Sie die für Ihren API-Typ passende Abhängigkeit aus.

    * API-Typ: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.azure.spring</groupId>
          <artifactId>azure-spring-boot-starter-cosmos</artifactId>
          <version>3.6.0</version>
      </dependency>
      ```

    * API-Typ: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-Typ: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-Typ: Azure Table

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Aktualisieren Sie die aktuelle App durch Ausführen von `az spring-cloud app deploy`, oder erstellen Sie eine neue Bereitstellung für diese Änderung, indem Sie `az spring-cloud app deployment create` ausführen.

## <a name="bind-your-app-to-the-azure-cosmos-db"></a>Binden Ihrer App an die Azure Cosmo DB

#### <a name="service-binding"></a>[Dienstbindung](#tab/Service-Binding)
Azure Cosmos DB verfügt über fünf verschiedene API-Typen, die Bindung unterstützen. Im Folgenden sehen Sie, wie diese verwendet werden:

1. Erstellen Sie eine Azure Cosmos DB-Datenbank. Weitere Informationen finden Sie in der Schnellstartanleitung zum [Erstellen einer Datenbank](../cosmos-db/create-cosmosdb-resources-portal.md).

1. Notieren Sie den Namen der Datenbank. In diesem Verfahren lautet der Datenbankname **testdb**.

1. Navigieren Sie im Azure-Portal zur Seite Ihres Azure Spring Cloud-Diensts. Navigieren Sie zum **Anwendungsdashboard**, und wählen Sie die Anwendung aus, die an Azure Cosmos DB gebunden werden soll. Dabei handelt es sich um dieselbe Anwendung, die Sie im vorherigen Schritt aktualisiert oder bereitgestellt haben.

1. Wählen Sie **Dienstbindung** und dann **Dienstbindung erstellen** aus. Wählen Sie zum Ausfüllen des Formulars die folgenden Werte aus:
   * **Azure Cosmos DB** für **Bindungstyp**
   * Den API-Typ
   * Den Datenbanknamen
   * Das Azure Cosmos DB-Konto

    > [!NOTE]
    > Verwenden Sie einen Schlüsselraum für den Datenbanknamen, wenn Sie mit Cassandra arbeiten.

1. Starten Sie die Anwendung neu, indem Sie auf der Anwendungsseite **Neu starten** auswählen.

1. Um sicherzustellen, dass der Dienst ordnungsgemäß gebunden ist, wählen Sie den Bindungsnamen aus, und überprüfen Sie dessen Details. Das Feld `property` sollte in etwa wie das folgende Beispiel aussehen:

    ```properties
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)
Im folgenden Terraform-Skript sehen Sie, wie Sie eine Azure Spring Cloud-App mithilfe der MongoDB-API von Azure Cosmos DB einrichten.

```terraform
provider "azurerm" {
  features {}
}

variable "application_name" {
  type        = string
  description = "The name of your application"
  default     = "demo-abc"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_cosmosdb_account" "cosmosdb" {
  name                = "cosmosacct-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "MongoDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    failover_priority = 0
    location          = azurerm_resource_group.example.location
  }
}

resource "azurerm_cosmosdb_mongo_database" "cosmosdb" {
  name                = "cosmos-${var.application_name}-001"
  resource_group_name = azurerm_cosmosdb_account.cosmosdb.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmosdb.name
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "${var.application_name}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

resource "azurerm_spring_cloud_app" "example" {
  name                = "${var.application_name}-app"
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
    "azure.cosmosdb.uri" : azurerm_cosmosdb_account.cosmosdb.connection_strings[0]
    "azure.cosmosdb.database" : azurerm_cosmosdb_mongo_database.cosmosdb.name
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihre Anwendung in Azure Spring Cloud an eine Azure Cosmos DB-Datenbank binden. Weitere Informationen zum Binden von Diensten an Ihre Anwendung finden Sie unter [Binden von Azure-Diensten an Ihre Azure Spring Cloud-Anwendung: Azure Cache for Redis](./how-to-bind-redis.md).
