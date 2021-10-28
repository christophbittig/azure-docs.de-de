---
title: Erstellen einer App Service-App mithilfe einer Terraform-Vorlage
description: Mithilfe einer Terraform-Vorlage können Sie Ihre erste Azure App Service-App innerhalb von Sekunden erstellen. Hierbei handelt es sich um eine von vielen Bereitstellungsmöglichkeiten in App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: d41b61252abd3e914239a28e78564128a4fbf32d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227148"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>Erstellen einer App Service-App mithilfe einer Terraform-Vorlage

Führen Sie erste Schritte mit [Azure App Service](overview.md) aus, indem Sie eine App mithilfe von [Terraform](/azure/developer/terraform/) in der Cloud bereitstellen. Da Sie einen kostenlosen App Service-Tarif verwenden, fallen bei dieser Schnellstartanleitung keine Kosten an.

Mit Terraform können Sie vollständige Infrastrukturbereitstellungen in Azure definieren und erstellen. Dazu lassen sich Terraform-Vorlagen in einem für Menschen lesbaren Format erstellen, die Azure-Ressourcen konsistent und reproduzierbar erstellen und konfigurieren. In diesem Artikel wird gezeigt, wie Sie eine Windows-App mit Terraform erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

Konfigurieren von Terraform: Konfigurieren Sie Terraform mithilfe einer der folgenden Optionen, sofern noch nicht geschehen:

* [Konfigurieren von Terraform in Azure Cloud Shell mit Bash](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [Konfigurieren von Terraform in Azure Cloud Shell mit PowerShell](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [Konfigurieren von Terraform in Windows mit Bash](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [Konfigurieren von Terraform in Windows mit PowerShell](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

Dank der Azure Terraform-Erweiterung für Visual Studio Code können Sie Terraform im Editor nutzen. Mit dieser Erweiterung können Sie Terraform-Konfigurationen erstellen, testen und ausführen. Die Erweiterung unterstützt auch die Visualisierung des Ressourcendiagramms. Informationen zum Konfigurieren der Azure Terraform-Erweiterung für Visual Studio Code finden Sie [in diesem Leitfaden](/azure/developer/terraform/configure-vs-code-extension-for-terraform).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage ist unten zu sehen. Sie stellt einen App Service-Plan und eine App Service-App unter Windows sowie eine „Hallo Welt“-Node.js-Beispiel-App aus dem [Azure-Beispiele](https://github.com/Azure-Samples)-Repository bereit.

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

In der Vorlage sind drei Azure-Ressourcen und eine Unterressource definiert. Weitere Details und Nutzungsinformationen finden Sie weiter unten unter den Links zur [Terraform-Registrierung des Azure-Anbieters](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs):

* [**Microsoft.Resources/resourcegroups**](/azure/templates/microsoft.resources/resourcegroups?tabs=json): Erstellen einer Ressourcengruppe, sofern noch keine vorhanden ist.
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): Erstellen einer externen Git-Bereitstellungskonfiguration.
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

Weitere Informationen zum Erstellen von Terraform-Vorlagen finden Sie in der [Learn-Dokumentation zu Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS).

## <a name="implement-the-terraform-code"></a>Implementieren des Terraform-Codes

Terraform bietet viele Funktionen für die Verwaltung, Erstellung, Bereitstellung und Aktualisierung der Infrastruktur. Die folgenden Schritte führen Sie nur durch die Bereitstellung und Zerstörung Ihrer Ressourcen. Die [Learn-Dokumentation zu Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) und die [Terraform in Azure-Dokumentation](/azure/developer/terraform/) gehen mehr in die Tiefe und sollten herangezogen werden, wenn Terraform Teil Ihrer Azure-Infrastrukturstrategie ist.

1. Erstellen Sie ein Verzeichnis, in dem der Terraform-Beispielcode getestet und ausgeführt werden soll, und legen Sie es als aktuelles Verzeichnis fest.

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. Erstellen Sie eine Datei namens `main.tf`, und fügen Sie den oben stehenden Code ein.

    ```bash
    code main.tf
    ```

1. Initialisieren Sie Terraform.

    ```bash
    terraform init
    ```

1. Erstellen Sie den Terraform-Plan.

    ```bash
    terraform plan
    ```

1. Stellen Sie die in der Konfigurationsdatei `main.tf` definierten Ressourcen bereit (Bestätigen Sie die Aktion durch Eingabe von `yes` an der Eingabeaufforderung).

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Wählen Sie im Hauptmenü des Azure-Portals **Ressourcengruppen** aus, und navigieren Sie zu der Ressourcengruppe, die Sie mit der obigen Vorlage erstellt haben. Sie erhält den Namen „myResourceGroup-“, gefolgt von einer Zeichenfolge aus zufälligen ganzen Zahlen.

1. Nun werden alle Ressourcen angezeigt, die Terraform erstellt hat (ein App Service und ein App Service-Plan).

1. Wählen Sie den **App Service** aus, und navigieren Sie zu der URL, um zu überprüfen, ob Ihre Site ordnungsgemäß erstellt wurde. Sie können stattdessen auch einfach zu `http://<app_name>.azurewebsites.net/` navigieren, wobei der App-Name „webapp-“ lautet, gefolgt von derselben Zeichenfolge zufälliger ganzer Zahlen aus der Ressourcengruppe.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe nicht mehr benötigen, [löschen Sie sie](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), oder kehren Sie zu Ihrem Terminal bzw. ihrer Befehlszeile zurück, und führen Sie `terraform destroy` aus, um alle Ressourcen zu löschen, die dieser Schnellstartanleitung zugeordnet sind.

> [!NOTE]
> Weitere Azure App Service-Terraform-Beispiele finden Sie [hier](./samples-terraform.md). [Hier](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples) können Sie sogar noch mehr Terraform-Beispiele für alle Azure-Dienste finden.
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)
> [!div class="nextstepaction"] 
> [Terraform-Beispiele für Azure App Service](./samples-terraform.md)