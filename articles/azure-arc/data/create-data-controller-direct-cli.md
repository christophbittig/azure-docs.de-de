---
title: Erstellen eines Azure Arc-Datencontrollers | Direkter Verbindungsmodus
description: Hier wird erläutert, wie der Datencontroller im direkten Verbindungsmodus erstellt wird.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/12/2021
ms.topic: overview
ms.openlocfilehash: 53cec5c48c65352c50fe115b0dc9160e72229b9a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486103"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>Erstellen eines Azure Arc-Datencontrollers im direkten Verbindungsmodus mithilfe der CLI

In diesem Artikel wird beschrieben, wie Sie den Azure Arc-Datencontroller mithilfe der CLI während der aktuellen Vorschau dieses Features im **direkten** Verbindungsmodus erstellen. 


## <a name="complete-prerequisites"></a>Erfüllen der Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie die Voraussetzungen unter [Bereitstellen eines Datencontrollers: direkter Verbindungsmodus (Voraussetzungen)](create-data-controller-direct-prerequisites.md) erfüllen.

Bei der Erstellung eines Azure Arc Datencontrollers im **direkten** Verbindungsmodus sind die folgenden Schritte auszuführen:

1. Erstellen einer Erweiterung für Azure Arc-fähige Datendienste 
1. Erstellen eines benutzerdefinierten Standorts
1. Erstellen Sie den Datencontroller.

> [!NOTE]
> Derzeit kann dieser Schritt nur über das Portal ausgeführt werden. Ausführliche Informationen finden Sie in den [Versionshinweisen](release-notes.md). 

## <a name="step-1-create-an-azure-arc-enabled-data-services-extension"></a>Schritt 1: Erstellen einer Erweiterung für Azure Arc-fähige Datendienste

Verwenden Sie die Befehlszeilenschnittstelle „k8s-extension“, um eine Datendiensterweiterung zu erstellen.

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Legen Sie die folgenden Umgebungsvariablen fest, die dann im nächsten Schritt verwendet werden.

#### <a name="linux"></a>Linux

``` terminal
## variables for Azure location, extension and namespace
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export clusterName=<name of your connected kubernetes cluster>
export location=<Azure location>
export adsExtensionName="ads-ext" 
export namespace="arcds"

## variables for Metrics and Monitoring dashboard credentials
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
## variables for Azure location, extension and namespace
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:clusterName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:adsExtensionName="<name of Data controller extension" 
$ENV:namespace="namespace where extension and data controller will be deployed"

## variables for Metrics and Monitoring dashboard credentials
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

### <a name="create-the-arc-data-services-extension"></a>Erstellen der Arc-Datendiensterweiterung

#### <a name="linux"></a>Linux

```bash
az k8s-extension create --cluster-name ${clusterName} --resource-group ${resourceGroup} --name ${adsExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace ${namespace} --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper

az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${resourceName} --name ${adsExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell

az k8s-extension create --cluster-name $ENV:clusterName --resource-group $ENV:resourceGroup --name $ENV:adsExtensionName --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace $ENV:namespace --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper

az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --name $ENV:adsExtensionName --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>Bereitstellen der Azure Arc-Datendiensterweiterung mithilfe einer privaten Containerregistrierung und Anmeldeinformationen

Verwenden Sie den folgenden Befehl, wenn Sie die Bereitstellung aus Ihrem privaten Repository durchführen:

```azurecli
az k8s-extension create --cluster-name "<connected cluster name>" --resource-group "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 Beispiel:
```azurecli
az k8s-extension create --cluster-name "my-connected-cluster" --resource-group "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Die Installation der Arc-Datendiensterweiterung kann einige Minuten dauern.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Überprüfen, ob die Arc-Datendiensterweiterung erstellt wurde

Sie können über das Portal oder durch direktes Herstellen einer Verbindung mit dem Kubernetes-Cluster mit Azure Arc-Unterstützung den Status der Bereitstellung der Azure Arc-fähigen Datendiensterweiterung überprüfen. 

#### <a name="check-status-from-azure-portal"></a>Überprüfen des Status über das Azure-Portal
1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu der Ressourcengruppe, in der sich die verbundene Kubernetes-Clusterressource befindet.
1. Wählen Sie den Kubernetes-Cluster mit Azure Arc-Unterstützung (Typ = „Kubernetes – Azure Arc“) aus, in dem die Erweiterung bereitgestellt wurde.
1. Wählen Sie im linken Navigationsbereich unter **Einstellungen** die Option **Erweiterungen** aus.
1. Die zuvor erstellte Erweiterung sollte den Zustand „Installiert“ aufweisen.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Dashboard für Erweiterungen":::

#### <a name="check-status-using-kubectl-cli"></a>Überprüfen des Status mithilfe der kubectl-Befehlszeilenschnittstelle

1. Stellen Sie über ein Terminalfenster eine Verbindung mit Ihrem Kubernetes-Cluster her.
1. Führen Sie den folgenden Befehl aus, und stellen Sie sicher, dass (1) der oben genannte Namespace erstellt wurde und (2) dass sich der Pod `bootstrapper` im Zustand „Wird ausgeführt“ befindet, bevor Sie mit dem nächsten Schritt fortfahren.

``` console
kubectl get pods --name <name of namespace used in the json template file above>
```

Mit dem folgenden Beispiel werden etwa die Pods aus dem `arc`-Namespace abgerufen:

```console
#Example:
kubectl get pods --name arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>Abrufen der verwalteten Identität und Gewähren von Rollen

Der verwalteten Identität, die während der Erstellung der Arc-Datendiensterweiterung erstellt wird, müssen bestimmte Rollen zugewiesen werden, damit die Nutzung und/oder Metriken automatisch hochgeladen werden.

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Abrufen der verwalteten Identität der Arc-Datencontrollererweiterung

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="2-assign-role-to-the-managed-identity"></a>(2) Zuweisen einer Rolle zur verwalteten Identität

Führen Sie den folgenden Befehl aus, um die Rollen **Mitwirkender** und **Herausgeber von Überwachungsmetriken** zuzuweisen:
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Contributor" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Monitoring Metrics Publisher" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

```

## <a name="step-2-create-a-custom-location-using-customlocation-cli-extension"></a>Schritt 2: Erstellen eines benutzerdefinierten Standorts mithilfe der CLI-Erweiterung ```customlocation```

Ein benutzerdefinierter Standort ist eine Azure-Ressource, die einem Namespace in einem Kubernetes-Cluster entspricht.  Benutzerdefinierte Standorte werden als Ziel verwendet, um Ressourcen in oder aus Azure bereitzustellen. Weitere Informationen zu benutzerdefinierten Standorten finden Sie in der [Dokumentation zu benutzerdefinierten Standorten in Azure Arc-fähigen Kubernetes-Clustern](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation

export hostClusterId=$(az connectedk8s show --resource-group ${resourceGroup} --name ${clusterName} --query id -o tsv)
export extensionId=$(az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${clusterName} --cluster-type connectedClusters --name ${adsExtensionName} --query id -o tsv)

az customlocation create --resource-group ${resourceGroup} --name ${clName} --namespace ${namespace} --host-resource-id ${hostClusterId} --cluster-extension-ids ${extensionId} --location ${location}
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"


$ENV:hostClusterId=(az connectedk8s show --resource-group $ENV:resourceGroup --name $ENV:clusterName --query id -o tsv)
$ENV:extensionId=(az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --cluster-type connectedClusters --name $ENV:adsExtensionName --query id -o tsv)

az customlocation create --resource-group $ENV:resourceGroup --name $ENV:clName --namespace $ENV:namespace --host-resource-id $ENV:hostClusterId --cluster-extension-ids $ENV:extensionId
```

## <a name="validate--the-custom-location-is-created"></a>Überprüfen, ob der benutzerdefinierte Standort erstellt wurde

Führen Sie im Terminal den folgenden Befehl aus, um die benutzerdefinierten Standorte aufzulisten, und überprüfen Sie, ob für **Bereitstellungsstatus** der Status „Erfolgreich“ angezeigt wird:

```azurecli
az customlocation list -o table
```

## <a name="step-3-create-the-azure-arc-data-controller"></a>Schritt 3: Erstellen des Azure Arc-Datencontrollers

Stellen Sie nach dem Erstellen der Erweiterung und des benutzerdefinierten Standorts wie folgt den Azure Arc-Datencontroller bereit:

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

Wenn Sie den Azure Arc-Datencontroller mithilfe einer benutzerdefinierten Konfigurationsvorlage erstellen möchten, führen Sie die unter [Erstellen von benutzerdefinierten Konfigurationsvorlagen](create-custom-configuration-template.md) beschriebenen Schritte aus, und geben Sie wie folgt den Pfad zur Datei an:

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

## <a name="monitor-the-status-of-azure-arc-data-controller-deployment"></a>Überwachen des Bereitstellungsstatus für den Azure Arc-Datencontroller

Der Bereitstellungsstatus des Arc-Datencontrollers im Cluster kann wie folgt überwacht werden:

```console
kubectl get datacontrollers --name arc
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung](create-postgresql-hyperscale-server-group.md)

[Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc](create-sql-managed-instance.md)
