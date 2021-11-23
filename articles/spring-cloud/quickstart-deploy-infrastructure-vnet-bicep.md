---
title: 'Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe von Bicep'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Bicep einen Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 11/12/2021
ms.openlocfilehash: ac155d1937638b4be26c74214d63d35edd009322
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488809"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>Schnellstart: Bereitstellen von Azure Spring Cloud mithilfe von Bicep

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe einer Bicep-Vorlage einen Azure Spring Cloud-Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.

Mit Azure Spring Cloud lassen sich Spring Boot-Microserviceanwendungen ganz einfach und ohne Codeänderungen in Azure bereitstellen. Der Dienst verwaltet die Infrastruktur von Spring Cloud-Anwendungen und ermöglicht es Entwicklern dadurch, sich auf ihren Code zu konzentrieren. Azure Spring Cloud bietet Lebenszyklusverwaltung durch umfassende Überwachung und Diagnose, Konfigurationsverwaltung, Dienstermittlung, CI/CD-Integration, Blau-Grün-Bereitstellungen und mehr.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Zwei dedizierte Subnetze für den Azure Spring Cloud-Cluster, eines für die Dienstruntime und ein weiteres für die Spring Boot-Microserviceanwendungen. Die Anforderungen an Subnetze und virtuelle Netzwerke finden Sie im Abschnitt [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Ein vorhandener Log Analytics-Arbeitsbereich für Azure Spring Cloud-Diagnoseeinstellungen. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md).
* Drei interne CIDR-Bereiche (Classless Inter-Domain Routing) (mindestens jeweils */16*), die Sie für die Verwendung durch den Azure Spring Cloud-Cluster identifiziert haben. Diese CIDR-Bereiche sind nicht direkt routingfähig und werden nur intern vom Azure Spring Cloud-Cluster verwendet. Cluster dürfen nicht *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* oder *192.0.2.0/24* für die internen Spring Cloud-CIDR-Bereiche verwenden, oder IP-Adressbereiche, die im Adressbereich des virtuellen Netzwerks des Clusters enthalten sind.
* Dienstberechtigung, die dem virtuellen Netzwerk erteilt wurde. Der Azure Spring Cloud-Ressourcenanbieter erfordert die Berechtigung vom Typ „Besitzer“ für Ihr virtuelles Netzwerk, damit ein dedizierter und dynamischer Dienstprinzipal im virtuellen Netzwerk für die weitere Bereitstellung und Wartung gewährt werden kann. Anweisungen und weitere Informationen finden Sie im Abschnitt [Erteilen der Dienstberechtigung für das virtuelle Netzwerk](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* Wenn Sie Azure Firewall oder ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) verwenden, müssen Sie auch die folgenden Voraussetzungen erfüllen:
   * Regeln für Netzwerk- und vollqualifizierte Domänennamen (Fully Qualified Domain Name, FQDN). Weitere Informationen finden Sie unter [Anforderungen für virtuelle Netzwerke](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Eine eindeutige benutzerdefinierte Route (User Defined Route, UDR), die auf die einzelnen Subnetze der Dienstruntime und der Spring Boot-Microserviceanwendungen angewendet wird. Weitere Informationen zum UDRs finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md). Die UDR sollte mit einer Route für *0.0.0.0/0* mit einem Ziel Ihres NVA konfiguriert werden, bevor der Spring Cloud-Cluster bereitgestellt wird. Weitere Informationen finden Sie im Abschnitt [Eigene Routingtabelle verwenden](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) des Artikels [Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](how-to-deploy-in-azure-virtual-network.md).
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>Bereitstellen mithilfe von Bicep

Führen Sie diese Schritte aus, um den Cluster bereitzustellen:

1. Erstellen Sie eine Datei vom Typ *azuredeploy.bicep* mit folgendem Inhalt:

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. Öffnen Sie ein Bash-Fenster, und führen Sie den folgenden Azure CLI-Befehl aus. Ersetzen Sie dabei die Platzhalter *\<value>* durch die folgenden Werte:

   - **resource-group:** Der Ressourcengruppenname zum Bereitstellen der Spring Cloud-Instanz
   - **springCloudInstanceName:** Der Name der Azure Spring Cloud-Ressource
   - **appInsightsName:** Der Name der Application Insights-Instanz für Azure Spring Cloud
   - **laWorkspaceResourceId:** Die Ressourcen-ID des vorhandenen Log Analytics-Arbeitsbereichs (z. B. */   subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/   Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* )
   - **springCloudAppSubnetID:** Die Ressourcen-ID des Azure Spring Cloud App-Subnetzes
   - **springCloudRuntimeSubnetID:** Die Ressourcen-ID des Azure Spring Cloud-Runtimesubnetzes
   - **springCloudServiceCidrs:** Eine durch Kommas getrennte Liste von IP-Adressbereichen (insgesamt drei) im CIDR-Format Die IP-Adressbereiche sind zum Hosten der zugrunde liegenden Azure Spring Cloud-Infrastruktur reserviert. Diese drei Bereiche sollten mindestens nicht verwendete */16*- IP-Adressbereiche sein und dürfen sich nicht mit routingfähigen Subnetz-IP-Bereichen überschneiden, die im Netzwerk verwendet werden.

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   Dieser Befehl verwendet die Bicep-Vorlage, um eine Azure Spring Cloud-Instanz in einem vorhandenen virtuellen Netzwerk zu erstellen. Mit dem Befehl wird außerdem eine arbeitsbereichsbasierte Application Insights-Instanz in einem vorhandenen Log Analytics-Arbeitsbereich von Azure Monitor erstellt.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um die bereitgestellten Ressourcen zu überprüfen, oder Azure CLI oder ein Azure PowerShell-Skript, um die bereitgestellten Ressourcen aufzulisten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen. Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht. Wenn Sie die Ressourcengruppe mithilfe der Azure CLI löschen möchten, verwenden Sie die folgenden Befehle:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe von Bicep eine Azure Spring Cloud-Instanz in einem vorhandenen virtuellen Netzwerk bereitgestellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Spring Cloud finden Sie in den folgenden Ressourcen.

- Stellen Sie eine der folgenden Beispielanwendungen an den folgenden Speicherorten bereit:
   - [Pet Clinic-App mit MySQL-Integration](https://github.com/azure-samples/spring-petclinic-microservices) (Microservices mit MySQL-Back-End).
   - [Einfaches „Hallo Welt“](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Verwenden Sie [benutzerdefinierte Domänen](tutorial-custom-domain.md) mit Azure Spring Cloud.
- Machen Sie Anwendungen in Azure Spring Cloud mithilfe von [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) für das Internet verfügbar.
- Zeigen Sie die sichere [Azure Spring Cloud-Referenzarchitektur](reference-architecture.md) (End-to-End-Architektur) an, die auf dem [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) basiert.
