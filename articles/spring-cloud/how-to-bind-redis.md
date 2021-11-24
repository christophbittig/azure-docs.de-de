---
title: Binden von Azure Cache for Redis an Ihre Anwendung in Azure Spring Cloud
description: Hier erfahren Sie, wie Sie Azure Cache for Redis an Ihre Anwendung in Azure Spring Cloud binden.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 9f357526b64b34d7348114ce71840a5f4aafc394
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492495"
---
# <a name="bind-azure-cache-for-redis-to-your-application-in-azure-spring-cloud"></a>Binden von Azure Cache for Redis an Ihre Anwendung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

Mit Azure Spring Cloud können Sie ausgewählte Azure-Dienste automatisch an Ihre Anwendungen binden, statt Ihre Spring Boot-Anwendungen manuell zu konfigurieren. In diesem Artikel wird gezeigt, wie Sie Ihre Anwendung an Azure Cache for Redis binden.

## <a name="prerequisites"></a>Voraussetzungen

* Eine bereitgestellte Azure Spring Cloud-Instanz
* Eine Azure Cache for Redis-Dienstinstanz
* Die Azure Spring Cloud-Erweiterung für die Azure-Befehlszeilenschnittstelle

Wenn Sie nicht über eine bereitgestellte Azure Spring Cloud-Instanz verfügen, folgen Sie den Schritten in diesem [Schnellstart zum Bereitstellen einer Azure Spring Cloud-App](./quickstart.md).

## <a name="prepare-your-java-project"></a>Vorbereiten des Java-Projekts

1. Fügen Sie der Datei „pom.xml“ Ihres Projekts die folgende Abhängigkeit hinzu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```

1. Entfernen Sie alle Eigenschaften vom Typ `spring.redis.*` aus der Datei `application.properties`.

1. Aktualisieren Sie die aktuelle Bereitstellung mit `az spring-cloud app update`, oder erstellen Sie eine neue Bereitstellung mithilfe von `az spring-cloud app deployment create`.

## <a name="bind-your-app-to-the-azure-cache-for-redis"></a>Binden Ihrer App an Azure Cache for Redis

#### <a name="service-binding"></a>[Dienstbindung](#tab/Service-Binding)
1. Navigieren Sie im Azure-Portal zur Seite Ihres Azure Spring Cloud-Diensts. Navigieren Sie zum **Anwendungsdashboard**, und wählen Sie die Anwendung aus, die an Azure Cache for Redis gebunden werden soll. Dabei handelt es sich um dieselbe Anwendung, die Sie im vorherigen Schritt aktualisiert oder bereitgestellt haben.

1. Wählen Sie **Dienstbindung** und dann **Dienstbindung erstellen** aus. Füllen Sie das Formular aus. Wählen Sie unbedingt als **Bindungstyp** den Wert **Azure Cache for Redis**, Ihren Azure Cache for Redis-Server und die Option **Primärschlüssel** aus.

1. Starten Sie die App neu. Die Bindung sollte nun funktionieren.

1. Um sicherzustellen, dass die Dienstbindung richtig ist, wählen Sie den Bindungsnamen aus, und überprüfen Sie dessen Details. Das Feld `property` sollte wie folgt aussehen:

    ```properties
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

#### <a name="terraform"></a>[Terraform](#tab/Terraform)

Im folgenden Terraform-Skript sehen Sie, wie Sie eine Azure Spring Cloud-App mithilfe von Azure Cache for Redis einrichten.

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

resource "azurerm_redis_cache" "redis" {
  name                = "redis-${var.application_name}-001"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  capacity            = 0
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
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
    "spring.redis.host"     = azurerm_redis_cache.redis.hostname
    "spring.redis.password" = azurerm_redis_cache.redis.primary_access_key
    "spring.redis.port"     = "6380"
    "spring.redis.ssl"      = "true"
  }
}

resource "azurerm_spring_cloud_active_deployment" "example" {
  spring_cloud_app_id = azurerm_spring_cloud_app.example.id
  deployment_name     = azurerm_spring_cloud_java_deployment.example.name
}
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihre Anwendung in Azure Spring Cloud an Azure Cache for Redis binden. Weitere Informationen zum Binden von Diensten an Ihre Anwendung finden Sie unter [Binden einer Azure Database for MySQL-Instanz an Ihre Azure Spring Cloud-Anwendung](./how-to-bind-mysql.md).
