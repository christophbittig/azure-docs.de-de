---
title: 'Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe der Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI einen Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 11/12/2021
ms.openlocfilehash: 967303cc675ebc6537630f08e6871f5b32cf6af4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488828"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI einen Azure Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.

Mit Azure Spring Cloud lassen sich Spring Boot-Microserviceanwendungen ganz einfach und ohne Codeänderungen in Azure bereitstellen. Der Dienst verwaltet die Infrastruktur von Spring Cloud-Anwendungen und ermöglicht es Entwicklern dadurch, sich auf ihren Code zu konzentrieren. Azure Spring Cloud bietet Lebenszyklusverwaltung durch umfassende Überwachung und Diagnose, Konfigurationsverwaltung, Dienstermittlung, CI/CD-Integration, Blau-Grün-Bereitstellungen und mehr.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Zwei dedizierte Subnetze für den Azure Spring Cloud-Cluster, eines für die Dienstruntime und ein weiteres für die Spring Boot-Microserviceanwendungen. Die Anforderungen an Subnetze und virtuelle Netzwerke finden Sie im Abschnitt [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Ein vorhandener Log Analytics-Arbeitsbereich für Azure Spring Cloud-Diagnoseeinstellungen und eine arbeitsbereichsbasierte Application Insights-Ressource. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md) und [Java-In-Process-Agent für Application Insights in Azure Spring Cloud](how-to-application-insights.md).
* Drei interne CIDR-Bereiche (Classless Inter-Domain Routing) (mindestens jeweils */16*), die Sie für die Verwendung durch den Azure Spring Cloud-Cluster identifiziert haben. Diese CIDR-Bereiche sind nicht direkt routingfähig und werden nur intern vom Azure Spring Cloud-Cluster verwendet. Cluster dürfen nicht *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* oder *192.0.2.0/24* für die internen Spring Cloud-CIDR-Bereiche verwenden, oder IP-Adressbereiche, die im Adressbereich des virtuellen Netzwerks des Clusters enthalten sind.
* Dienstberechtigung, die dem virtuellen Netzwerk erteilt wurde. Der Azure Spring Cloud-Ressourcenanbieter erfordert die Berechtigung vom Typ „Besitzer“ für Ihr virtuelles Netzwerk, damit ein dedizierter und dynamischer Dienstprinzipal im virtuellen Netzwerk für die weitere Bereitstellung und Wartung gewährt werden kann. Anweisungen und weitere Informationen finden Sie im Abschnitt [Erteilen der Dienstberechtigung für das virtuelle Netzwerk](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Wenn Sie Azure Firewall oder ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) verwenden, müssen Sie auch die folgenden Voraussetzungen erfüllen:
   * Regeln für Netzwerk- und vollqualifizierte Domänennamen (Fully Qualified Domain Name, FQDN). Weitere Informationen finden Sie unter [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Eine eindeutige benutzerdefinierte Route (User Defined Route, UDR), die auf die einzelnen Subnetze der Dienstruntime und der Spring Boot-Microserviceanwendungen angewendet wird. Weitere Informationen zum UDRs finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md). Die UDR sollte mit einer Route für *0.0.0.0/0* mit einem Ziel Ihres NVA konfiguriert werden, bevor der Spring Cloud-Cluster bereitgestellt wird. Weitere Informationen finden Sie im Abschnitt [Eigene Routingtabelle verwenden](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>Überprüfen des Azure CLI-Bereitstellungsskripts

Das in dieser Schnellstartanleitung verwendete Bereitstellungsskript stammt aus der [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md).

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>Bereitstellen des Clusters

Führen Sie die folgenden Schritte aus, um den Azure Spring Cloud-Cluster mithilfe des Azure CLI-Skripts bereitzustellen:

1. Melden Sie sich mithilfe des folgenden Befehls bei Azure an:

   ```azurecli
   az login
   ```

   Nach der Anmeldung gibt dieser Befehl Informationen zu allen Abonnements aus, auf die Sie Zugriff haben. Notieren Sie sich den Namen und die ID des Abonnements, das Sie verwenden möchten.

1. Legen Sie das Zielabonnement fest:

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Registrieren Sie den Azure Spring Cloud-Ressourcenanbieter:

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. Fügen Sie der Azure CLI die erforderlichen Erweiterungen hinzu:

   ```azurecli
   az extension add --name spring-cloud
   ```

1. Wählen Sie einen Bereitstellungsstandort aus den Regionen aus, in denen Azure Spring Cloud verfügbar ist (siehe [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)).

1. Verwenden Sie den folgenden Befehl, um eine Liste der Azure-Standorte zu generieren. Notieren Sie sich den Kurzwert für **Name** für die Region, die Sie im vorherigen Schritt ausgewählt haben.

   ```azurecli
   az account list-locations --output table
   ```

1. Erstellen Sie eine Ressourcengruppe, in der die Ressource bereitgestellt werden soll:

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. Speichern Sie das Bash-Skript [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) lokal, und führen Sie es dann über die Bash-Eingabeaufforderung aus:

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. Geben Sie bei Aufforderung durch das Skript die folgenden Werte ein:

   - Die zuvor gespeicherte Azure-Abonnement-ID
   - Den zuvor gespeicherten Namen des Azure-Standorts
   - Den Namen der zuvor erstellten Ressourcengruppe
   - Den Namen der Ressourcengruppe des virtuellen Netzwerks, in der Sie Ihre Ressourcen bereitstellen
   - Name des virtuellen Spokenetzwerks (z. B. *vnet-spoke*)
   - Name des Subnetzes, das von Spring Cloud App Service verwendet werden soll (z. B. *snet-app*)
   - Den Namen des Subnetzes, das vom Spring Cloud-Laufzeitdienst verwendet werden soll (z. B. *snet-runtime*)
   - Den Namen der Ressourcengruppe für den Azure Log Analytics-Arbeitsbereich, der zum Speichern von Diagnoseprotokollen verwendet werden soll
   - Den Namen des Azure Log Analytics-Arbeitsbereichs (z. B. *la-cb5sqq6574o2a*)
   - Die CIDR-Bereiche Ihres virtuellen Netzwerks, die von Azure Spring Cloud verwendet werden sollen (z. B. *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*)
   - Schlüssel-Wert-Paare, die als Tags auf alle Ressourcen angewendet werden sollen, die Tags unterstützen. Unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md) erhalten Sie weitere Informationen. Verwenden Sie eine durch Leerzeichen getrennte Liste, um mehrere Tags anzuwenden (z. B. *environment=Dev BusinessUnit=finance*)

Nach Angabe dieser Informationen werden die Azure-Ressourcen vom Skript erstellt und bereitgestellt.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um die bereitgestellten Ressourcen zu überprüfen, oder die Azure CLI, um die bereitgestellten Ressourcen aufzulisten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen. Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht. Wenn Sie die Ressourcengruppe mithilfe der Azure CLI löschen möchten, verwenden Sie die folgenden Befehle:

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe der Azure CLI eine Azure Spring Cloud-Instanz in einem vorhandenen virtuellen Netzwerk bereitgestellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Spring Cloud finden Sie in den folgenden Ressourcen.

- Stellen Sie eine der folgenden Beispielanwendungen an den folgenden Speicherorten bereit:
   - [Pet Clinic-App mit MySQL-Integration](https://github.com/azure-samples/spring-petclinic-microservices) (Microservices mit MySQL-Back-End).
   - [Einfaches „Hallo Welt“](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Verwenden Sie [benutzerdefinierte Domänen](tutorial-custom-domain.md) mit Azure Spring Cloud.
- Machen Sie Anwendungen in Azure Spring Cloud mithilfe von [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) für das Internet verfügbar.
- Zeigen Sie die sichere [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md) (End-to-End-Architektur) an, die auf dem [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) basiert.
