---
title: 'Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe von Terraform'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Terraform einen Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 11/12/2021
ms.openlocfilehash: 313967fcadf1dac7ec2451b9dc51da1fac9bc435
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488751"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe von Terraform

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe von Terraform einen Azure Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.

Mit Azure Spring Cloud lassen sich Spring Boot-Microserviceanwendungen ganz einfach und ohne Codeänderungen in Azure bereitstellen. Der Dienst verwaltet die Infrastruktur von Spring Cloud-Anwendungen und ermöglicht es Entwicklern dadurch, sich auf ihren Code zu konzentrieren. Azure Spring Cloud bietet Lebenszyklusverwaltung durch umfassende Überwachung und Diagnose, Konfigurationsverwaltung, Dienstermittlung, CI/CD-Integration, Blau-Grün-Bereitstellungen und mehr.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* [Hashicorp Terraform](https://www.terraform.io/downloads.html)
* Zwei dedizierte Subnetze für den Azure Spring Cloud-Cluster, eines für die Dienstruntime und ein weiteres für die Spring Boot-Microserviceanwendungen. Die Anforderungen an Subnetze und virtuelle Netzwerke finden Sie im Abschnitt [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Ein vorhandener Log Analytics-Arbeitsbereich für Azure Spring Cloud-Diagnoseeinstellungen und eine arbeitsbereichsbasierte Application Insights-Ressource. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md) und [Java-In-Process-Agent für Application Insights in Azure Spring Cloud](how-to-application-insights.md).
* Drei interne CIDR-Bereiche (Classless Inter-Domain Routing) (mindestens jeweils */16*), die Sie für die Verwendung durch den Azure Spring Cloud-Cluster identifiziert haben. Diese CIDR-Bereiche sind nicht direkt routingfähig und werden nur intern vom Azure Spring Cloud-Cluster verwendet. Cluster dürfen nicht *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* oder *192.0.2.0/24* für die internen Spring Cloud-CIDR-Bereiche verwenden, oder IP-Adressbereiche, die im Adressbereich des virtuellen Netzwerks des Clusters enthalten sind.
* Dienstberechtigung, die dem virtuellen Netzwerk erteilt wurde. Der Azure Spring Cloud-Ressourcenanbieter erfordert die Berechtigung vom Typ „Besitzer“ für Ihr virtuelles Netzwerk, damit ein dedizierter und dynamischer Dienstprinzipal im virtuellen Netzwerk für die weitere Bereitstellung und Wartung gewährt werden kann. Anweisungen und weitere Informationen finden Sie im Abschnitt [Erteilen der Dienstberechtigung für das virtuelle Netzwerk](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Wenn Sie Azure Firewall oder ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) verwenden, müssen Sie auch die folgenden Voraussetzungen erfüllen:
   * Regeln für Netzwerk- und vollqualifizierte Domänennamen (Fully Qualified Domain Name, FQDN). Weitere Informationen finden Sie unter [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Eine eindeutige benutzerdefinierte Route (User Defined Route, UDR), die auf die einzelnen Subnetze der Dienstruntime und der Spring Boot-Microserviceanwendungen angewendet wird. Weitere Informationen zum UDRs finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md). Die UDR sollte mit einer Route für *0.0.0.0/0* mit einem Ziel Ihres NVA konfiguriert werden, bevor der Spring Cloud-Cluster bereitgestellt wird. Weitere Informationen finden Sie im Abschnitt [Eigene Routingtabelle verwenden](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).

## <a name="review-the-configuration-file"></a>Überprüfen der Konfigurationsdatei

Die in dieser Schnellstartanleitung verwendete Konfigurationsdatei stammt aus der [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md).

```hcl
provider "azurerm" {
    features {}
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name
  resource_group_name = var.resource_group_name
  location            = var.location

  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }

  timeouts {
      create = "60m"
      delete = "2h"
  }

  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags

}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>Anwenden der Konfiguration

Führen Sie die folgenden Schritte aus, um die Konfiguration anzuwenden:

1. Speichern Sie die Datei [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) lokal, und öffnen Sie sie dann in einem Editor.

1. Bearbeiten Sie die Datei, und fügen Sie ihr folgende Werte hinzu:

   - Abonnement-ID des Azure-Kontos, in dem Sie die Bereitstellung durchführen

   - Bereitstellungsstandort aus den Regionen, in denen Azure Spring Cloud verfügbar ist (siehe [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)). Sie benötigen die Kurzform des Standortnamens. Verwenden Sie zum Abrufen dieses Werts den folgenden Befehl, um eine Liste von Azure-Standorten zu generieren, und suchen Sie dann nach dem Wert für **Name** für die von Ihnen ausgewählte Region.

   ```azurecli
   az account list-locations --output table
   ```

   - Name der Ressourcengruppe, in der Sie die Bereitstellung durchführen
   - Name Ihrer Wahl für die Spring Cloud-Bereitstellung
   - Name der Ressourcengruppe des virtuellen Netzwerks, in der Sie Ihre Ressourcen bereitstellen
   - Name des virtuellen Spokenetzwerks (z. B. *vnet-spoke*)
   - Name des Subnetzes, das von Spring Cloud App Service verwendet werden soll (z. B. *snet-app*)
   - Name des Subnetzes, das vom Spring Cloud-Laufzeitdienst verwendet werden soll (z. B. *snet-runtime*)
   - Name des Azure Log Analytics-Arbeitsbereichs
   - CIDR-Bereiche Ihres virtuellen Netzwerks, die von Azure Spring Cloud verwendet werden sollen (z. B. *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*)
   - Schlüssel-Wert-Paare, die als Tags auf alle Ressourcen angewendet werden sollen, die Tags unterstützen. Unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md) erhalten Sie weitere Informationen.

1. Führen Sie den folgenden Befehl aus, um die Terraform-Module zu initialisieren:

   ```bash
   terraform init
   ```

1. Führen Sie den folgenden Befehl aus, um den Terraform-Bereitstellungsplan zu initialisieren:

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. Führen Sie den folgenden Befehl aus, um den Terraform-Bereitstellungsplan anzuwenden:

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um die bereitgestellten Ressourcen zu überprüfen, oder Azure CLI oder ein Azure PowerShell-Skript, um die bereitgestellten Ressourcen aufzulisten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen. Wenn Sie die in diesem Artikel erstellten Ressourcen nicht mehr benötigen, löschen Sie sie mithilfe des folgenden Befehls:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe von Terraform eine Azure Spring Cloud-Instanz in einem vorhandenen virtuellen Netzwerk bereitgestellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Spring Cloud finden Sie in den folgenden Ressourcen.

- Stellen Sie eine der folgenden Beispielanwendungen an den folgenden Speicherorten bereit:
   - [Pet Clinic-App mit MySQL-Integration](https://github.com/azure-samples/spring-petclinic-microservices) (Microservices mit MySQL-Back-End).
   - [Einfaches „Hallo Welt“](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Verwenden Sie [benutzerdefinierte Domänen](tutorial-custom-domain.md) mit Azure Spring Cloud.
- Machen Sie Anwendungen in Azure Spring Cloud mithilfe von [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) für das Internet verfügbar.
- Zeigen Sie die sichere [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md) (End-to-End-Architektur) an, die auf dem [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) basiert.
