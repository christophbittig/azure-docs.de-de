---
title: 'Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Spring Cloud-Cluster mithilfe einer ARM-Vorlage in einem vorhandenen virtuellen Netzwerk bereitstellen.
services: azure-resource-manager
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-java
ms.author: rhudson
ms.date: 11/12/2021
ms.openlocfilehash: c34d2d9f88f97b80892b4329def4ca84f5f7b70c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488860"
---
# <a name="quickstart-provision-azure-spring-cloud-using-an-arm-template"></a>Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um einen Azure Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitzustellen.

Mit Azure Spring Cloud lassen sich Spring Boot-Microserviceanwendungen ganz einfach und ohne Codeänderungen in Azure bereitstellen. Der Dienst verwaltet die Infrastruktur von Spring Cloud-Anwendungen und ermöglicht es Entwicklern dadurch, sich auf ihren Code zu konzentrieren. Azure Spring Cloud bietet Lebenszyklusverwaltung durch umfassende Überwachung und Diagnose, Konfigurationsverwaltung, Dienstermittlung, CI/CD-Integration, Blau-Grün-Bereitstellungen und mehr.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Zwei dedizierte Subnetze für den Azure Spring Cloud-Cluster, eines für die Dienstruntime und ein weiteres für die Spring Boot-Microserviceanwendungen. Die Anforderungen an Subnetze und virtuelle Netzwerke finden Sie im Abschnitt [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Ein vorhandener Log Analytics-Arbeitsbereich für Azure Spring Cloud-Diagnoseeinstellungen und eine arbeitsbereichsbasierte Application Insights-Ressource. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md) und [Java-In-Process-Agent für Application Insights in Azure Spring Cloud](how-to-application-insights.md).
* Drei interne CIDR-Bereiche (Classless Inter-Domain Routing) (mindestens jeweils */16*), die Sie für die Verwendung durch den Azure Spring Cloud-Cluster identifiziert haben. Diese CIDR-Bereiche sind nicht direkt routingfähig und werden nur intern vom Azure Spring Cloud-Cluster verwendet. Cluster dürfen nicht *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* oder *192.0.2.0/24* für die internen Spring Cloud-CIDR-Bereiche verwenden, oder IP-Adressbereiche, die im Adressbereich des virtuellen Netzwerks des Clusters enthalten sind.
* Dienstberechtigung, die dem virtuellen Netzwerk erteilt wurde. Der Azure Spring Cloud-Ressourcenanbieter erfordert die Berechtigung vom Typ „Besitzer“ für Ihr virtuelles Netzwerk, damit ein dedizierter und dynamischer Dienstprinzipal im virtuellen Netzwerk für die weitere Bereitstellung und Wartung gewährt werden kann. Anweisungen und weitere Informationen finden Sie im Abschnitt [Erteilen der Dienstberechtigung für das virtuelle Netzwerk](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Wenn Sie Azure Firewall oder ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) verwenden, müssen Sie auch die folgenden Voraussetzungen erfüllen:

   * Regeln für Netzwerk- und vollqualifizierte Domänennamen (Fully Qualified Domain Name, FQDN). Weitere Informationen finden Sie unter [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Eine eindeutige benutzerdefinierte Route (User Defined Route, UDR), die auf die einzelnen Subnetze der Dienstruntime und der Spring Boot-Microserviceanwendungen angewendet wird. Weitere Informationen zum UDRs finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md). Die UDR sollte mit einer Route für *0.0.0.0/0* mit einem Ziel Ihres NVA konfiguriert werden, bevor der Spring Cloud-Cluster bereitgestellt wird. Weitere Informationen finden Sie im Abschnitt [Eigene Routingtabelle verwenden](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt aus der [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md).

:::code language="json" source="~/azure-spring-cloud-reference-architecture/ARM/brownfield-deployment/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [Microsoft.AppPlatform/Spring](/azure/templates/microsoft.appplatform/spring): Erstellen einer Azure Spring Cloud-Instanz.
* [Microsoft.Insights/components](/azure/templates/microsoft.insights/components): Erstellen eines Application Insights-Arbeitsbereichs.

Informationen zu Azure CLI-, Terraform- und Bicep-Bereitstellungen finden Sie im [Azure Spring Cloud Reference Architecture](https://github.com/Azure/azure-spring-cloud-reference-architecture)-Repository auf GitHub.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt eine Azure Spring Cloud-Instanz in einem vorhandenen Virtual Network und eine arbeitsbereichsbasierte Application Insights-Instanz in einem vorhandenen Azure Monitor Log Analytics-Arbeitsbereich.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

2. Geben Sie Werte für die folgenden Felder ein:

   - **Ressourcengruppe:** Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die **Ressourcengruppe** ein, und wählen Sie dann **OK** aus.
   - **springCloudInstanceName:** Geben Sie den Namen der Azure Spring Cloud-Ressource ein.
   - **appInsightsName:** Geben Sie den Namen der Application Insights-Instanz für Azure Spring Cloud ein.
   - **laWorkspaceResourceId:** Geben Sie die Ressourcen-ID des vorhandenen Log Analytics-Arbeitsbereichs ein (z. B. */subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* ).
   - **springCloudAppSubnetID:** Geben Sie die resourceID des Azure Spring Cloud App-Subnetzes ein.
   - **springCloudRuntimeSubnetID:** Geben Sie die resourceID des Azure Spring Cloud-Runtimesubnetzes ein.
   - **springCloudServiceCidrs:** Geben Sie eine durch Kommas getrennte Liste von IP-Adressbereichen (insgesamt 3) im CIDR-Format ein. Die IP-Adressbereiche sind zum Hosten der zugrunde liegenden Azure Spring Cloud-Infrastruktur reserviert. Diese drei Bereiche sollten mindestens nicht verwendete */16*- IP-Adressbereiche sein und dürfen sich nicht mit routingfähigen Subnetz-IP-Bereichen überschneiden, die im Netzwerk verwendet werden.
   - **tags:** Geben Sie benutzerdefinierte Tags ein.

3. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um die bereitgestellten Ressourcen zu überprüfen, oder Azure CLI oder ein Azure PowerShell-Skript, um die bereitgestellten Ressourcen aufzulisten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen. Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht. Wenn Sie die Ressourcengruppe mit der Azure CLI oder Azure PowerShell löschen möchten, verwenden Sie die folgenden Befehle:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe einer ARM-Vorlage eine Azure Spring Cloud-Instanz in einem vorhandenen virtuellen Netzwerk bereitgestellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Spring Cloud und zum Azure Resource Manager finden Sie in den folgenden Ressourcen.

- Stellen Sie eine der folgenden Beispielanwendungen an den folgenden Speicherorten bereit:
   - [Pet Clinic-App mit MySQL-Integration](https://github.com/azure-samples/spring-petclinic-microservices) (Microservices mit MySQL-Back-End).
   - [Einfaches „Hallo Welt“](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Verwenden Sie [benutzerdefinierte Domänen](tutorial-custom-domain.md) mit Azure Spring Cloud.
- Machen Sie Anwendungen in Azure Spring Cloud mithilfe von [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) für das Internet verfügbar.
- Zeigen Sie die sichere [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md) (End-to-End-Architektur) an, die auf dem [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) basiert.
- Hier finden Sie weitere Informationen zum [Azure-Ressourcen-Manager](../azure-resource-manager/management/overview.md).
