---
title: Verwalten von Arbeitsbereichen mit Terraform
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure Machine Learning-Arbeitsbereiche mit Terraform verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: 21e3f646f384695fa6a305eb45f7e3bb019a18e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560813"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>Verwalten von Azure Machine Learning-Arbeitsbereichen mithilfe von Terraform (Vorschau)

In diesem Artikel erfahren Sie, wie Sie einen Azure Machine Learning-Arbeitsbereich mithilfe von Terraform-Konfigurationsdateien erstellen und verwalten. Die vorlagenbasierten Konfigurationsdateien von [Terraform](/azure/developer/terraform/) ermöglichen es Ihnen, Azure-Ressourcen in wiederholbarer und vorhersagbarer Weise zu definieren, zu erstellen und zu konfigurieren. Terraform verfolgt den Ressourcenzustand nach und kann Ressourcen bereinigen und zerstören. 

Eine Terraform-Konfiguration ist ein Dokument, das die Ressourcen definiert, die für eine Bereitstellung benötigt werden. Sie kann auch Variablen für die Bereitstellung angeben. Variablen werden verwendet, um Eingabewerte bereitzustellen, wenn die Konfiguration verwendet wird.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.
* Eine installierte Version der [Azure CLI](/cli/azure/).
* Konfigurieren von Terraform: Befolgen Sie die Anweisungen in diesem Artikel und im Artikel [Terraform und Konfigurieren des Zugriffs auf Azure](/azure/developer/terraform/get-started-cloud-shell).

## <a name="declare-the-azure-provider"></a>Deklarieren des Azure-Anbieters

Erstellen Sie die Terraform-Konfigurationsdatei, die den Azure-Anbieter deklariert:

1. Erstellen Sie eine neue Datei mit dem Namen `main.tf`. Wenn Sie mit Azure Cloud Shell arbeiten, verwenden Sie Bash:

    ```bash
    code main.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    **main.tf**:
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="deploy-a-workspace"></a>Bereitstellen eines Arbeitsbereichs

Die folgenden Terraform-Konfigurationen können verwendet werden, um einen Azure Machine Learning-Arbeitsbereich zu erstellen. Wenn Sie einen Azure Machine Learning-Arbeitsbereich erstellen, werden verschiedene andere Dienste als Abhängigkeiten benötigt. Die Vorlage gibt auch diese [zugeordneten Ressourcen für den Arbeitsbereich an](./concept-workspace.md#resources). Je nach Bedarf können Sie die Vorlage verwenden, die Ressourcen mit öffentlicher oder privater Netzwerkkonnektivität erstellt.

# <a name="public-network-connectivity"></a>[Öffentliche Netzwerkkonnektivität](#tab/publicworkspace)

Einige Ressourcen in Azure erfordern global eindeutige Namen. Bevor Sie Ihre Ressourcen mithilfe der folgenden Vorlagen bereitstellen, legen Sie die `name`-Variable auf einen eindeutigen Wert fest.

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[Private Netzwerkkonnektivität](#tab/privateworkspace)

Die folgende Konfiguration erstellt einen Arbeitsbereich in einer isolierten Netzwerkumgebung mithilfe von Azure Private Link-Endpunkten. [Private DNS-Zonen](../dns/private-dns-privatednszone.md) sind enthalten, damit Domänennamen innerhalb des virtuellen Netzwerks aufgelöst werden können.

Einige Ressourcen in Azure erfordern global eindeutige Namen. Bevor Sie Ihre Ressourcen mithilfe der folgenden Vorlagen bereitstellen, legen Sie die `resourceprefix`-Variable auf einen eindeutigen Wert fest.

Wenn Sie Private Link-Endpunkte sowohl für Azure Container Registry als auch für Azure Machine Learning verwenden, können Azure Container Registry-Tasks nicht zum Erstellen von [Umgebungsimages](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) verwendet werden. Stattdessen können Sie Images mithilfe eines Azure Machine Learning-Computeclusters erstellen. Um den zu verwendenden Clusternamen zu konfigurieren, legen Sie das Argument [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) fest. Sie können den [öffentlichen Zugriff](./how-to-configure-private-link.md?tabs=python#enable-public-access) auf einen Arbeitsbereich mit einem Private Link-Endpunkt mithilfe des Arguments [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) erlauben.

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf**:
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

Es gibt mehrere Möglichkeiten, eine Verbindung mit Ihrem Private Link-Endpunktarbeitsbereich herzustellen. Weitere Informationen zu diesen Optionen finden Sie unter [Sicheres Herstellen einer Verbindung mit Ihrem Arbeitsbereich](./how-to-secure-workspace-vnet.md#securely-connect-to-your-workspace).

---

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>Aktuelle Einschränkungen

* Es ist derzeit nicht möglich, Terraform für die Bereitstellung von Arbeitsbereichen mit kundenseitig verwalteten Verschlüsselungsschlüsseln zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Unterstützung von Terraform in Azure finden Sie in der [Dokumentation zu Terraform in Azure](/azure/developer/terraform/).
* Beispiele für Schnellstartvorlagen für Terraform finden Sie unter [Azure-Terraform-Schnellstartvorlagen](https://github.com/Azure/terraform/tree/master/quickstart):
  
  * [101: Machine Learning-Arbeitsbereich und Compute](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) – Der minimale Satz an Ressourcen für die ersten Schritte mit Azure ML.
  * [201: Machine Learning-Arbeitsbereich, Compute und ein Satz von Netzwerkkomponenten für die Netzwerkisolation](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) – Alle Ressourcen, die erforderlich sind, um eine Produktionspilotumgebung zu erstellen, die mit HBI-Daten verwendet werden kann.
  * [202: Ähnlich wie 201, jedoch mit der Möglichkeit, vorhandene Netzwerkkomponenten einzubringen.](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet).
  
* Ausführliche Informationen zum Azure-Anbieter für Terraform finden Sie unter [Terraform-Registrierung: Azure Resource Manager-Anbieter](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs).
* Weitere Informationen zu Netzwerkkonfigurationsoptionen finden Sie unter [Schützen von Azure Machine Learning-Arbeitsbereichsressourcen mit virtuellen Netzwerken (VNets)](./how-to-network-security-overview.md).
* Alternative, auf Azure Resource Manager-Vorlagen basierende Bereitstellungen finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md).