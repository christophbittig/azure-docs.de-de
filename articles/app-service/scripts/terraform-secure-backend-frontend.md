---
title: 'Terraform: Bereitstellen von Front-End- und Back-End-Web-App mit sicherer Verbindung (mit VNET-Integration und privatem Endpunkt)'
description: Hier erfahren Sie, wie Sie den Terraform-Anbieter für App Service verwenden, um zwei sicher verbundene Web-Apps mit privatem Endpunkt und VNET-Integration bereitzustellen.
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 99afad4cad2cddb706957c8a8b860c6dd55d3b71
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257155"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Erstellen von zwei sicher verbundenen Web-Apps mit privatem Endpunkt und VNET-Integration

In diesem Artikel erfahren Sie anhand eines Beispiels, wie Sie einen [privaten Endpunkt](../networking/private-endpoint.md) und eine regionale [VNET-Integration](../overview-vnet-integration.md) verwenden, um zwei Web-Apps (Front-End und Back-End) sicher zu verbinden. Dazu müssen folgende Schritte ausgeführt werden:
- Bereitstellen eines VNET
- Erstellen des ersten Subnetzes für die Integration
- Erstellen des zweiten Subnetzes für den privaten Endpunkt und Festlegen eines bestimmten Parameters zum Deaktivieren von Netzwerkrichtlinien
- Bereitstellen eines App Service-Plans vom Typ „PremiumV2“ oder „PremiumV3“ (für das Feature „Privater Endpunkt“ erforderlich)
- Erstellen der Front-End-Web-App mit spezifischen App-Einstellungen für die Verwendung der privaten DNS-Zone ([Ausführlichere Informationen](../overview-vnet-integration.md#azure-dns-private-zones))
- Herstellen einer Verbindung zwischen der Front-End-Web-App und dem Integrationssubnetz
- Erstellen der Back-End-Web-App
- Erstellen der privaten DNS-Zone mit dem Namen der Private Link-Zone für die Web-App (privatelink.azurewebsites.net)
- Verknüpfen dieser Zone mit dem VNET
- Erstellen des privaten Endpunkts für die Back-End-Web-App im Endpunktsubnetz und Registrieren von DNS-Namen (Website und SCM) in der zuvor erstellten privaten DNS-Zone

## <a name="how-to-use-terraform-in-azure"></a>Verwenden von Terraform in Azure

Informationen zur Verwendung von Terrraform mit Azure finden Sie in der [Azure-Dokumentation](/azure/developer/terraform/).

## <a name="the-complete-terraform-file"></a>Vollständige Terraform-Datei

Um diese Datei verwenden zu können, müssen Sie die Namenseigenschaft der Ressourcen „frontwebapp“ und „backwebapp“ ändern. (Der Web-App-Name muss weltweit eindeutig sein.) 

```hcl
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~>2.0"
    }
  }
}
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Nächste Schritte


> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/developer/terraform/)