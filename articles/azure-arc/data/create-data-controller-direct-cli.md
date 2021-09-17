---
title: Erstellen eines Azure Arc-Datencontrollers | Direkter Verbindungsmodus
description: Hier wird erläutert, wie der Datencontroller im direkten Verbindungsmodus erstellt wird.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 042d7fd04ca3a41016e67481f81237a56e0dfc8d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737178"
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

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Erstellen einer Erweiterung für Azure Arc-fähige Datendienste

Verwenden Sie die Befehlszeilenschnittstelle „k8s-extension“, um eine Datendiensterweiterung zu erstellen.

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Legen Sie die folgenden Umgebungsvariablen fest, die dann im nächsten Schritt verwendet werden.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Erstellen der Arc-Datendiensterweiterung

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>Bereitstellen der Azure Arc-Datendiensterweiterung mithilfe einer privaten Containerregistrierung und Anmeldeinformationen

Verwenden Sie den folgenden Befehl, wenn Sie die Bereitstellung aus Ihrem privaten Repository durchführen:

```azurecli
az k8s-extension create -c "<connected cluster name>" -g "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 Beispiel:
```azurecli
az k8s-extension create -c "my-connected-cluster" -g "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Die Installation der Arc-Datendiensterweiterung kann einige Minuten dauern.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Überprüfen, ob die Arc-Datendiensterweiterung erstellt wurde

Sie können über das Portal oder durch direktes Herstellen einer Verbindung mit dem Arc-fähigen Kubernetes-Cluster überprüfen, ob die Arc-fähige Datendiensterweiterung erstellt wurde. 

#### <a name="azure-portal"></a>Azure-Portal
1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu der Ressourcengruppe, in der sich die verbundene Kubernetes-Clusterressource befindet.
1. Wählen Sie den Arc-fähigen Kubernetes-Cluster (Typ = „Kubernetes – Azure Arc“) aus, in dem die Erweiterung bereitgestellt wurde.
1. Wählen Sie im linken Navigationsbereich unter **Einstellungen** die Option „Erweiterungen“ aus.
1. Die gerade erstellte Erweiterung sollte den Zustand „Installiert“ aufweisen.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Dashboard für Erweiterungen":::

#### <a name="kubectl-cli"></a>kubectl-Befehlszeilenschnittstelle

1. Stellen Sie über ein Terminalfenster eine Verbindung mit Ihrem Kubernetes-Cluster her.
1. Führen Sie den folgenden Befehl aus, und stellen Sie sicher, dass (1) der oben genannte Namespace erstellt wurde und (2) dass sich der Pod `bootstrapper` im Zustand „Wird ausgeführt“ befindet, bevor Sie mit dem nächsten Schritt fortfahren.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Mit dem folgenden Befehl werden beispielsweise die Pods aus dem `arc`-Namespace abgerufen:

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Erstellen eines benutzerdefinierten Standorts mithilfe der CLI-Erweiterung für benutzerdefinierte Standorte

Ein benutzerdefinierter Standort ist eine Azure-Ressource, die einem Namespace in einem Kubernetes-Cluster entspricht.  Benutzerdefinierte Standorte werden als Ziel verwendet, um Ressourcen in oder aus Azure bereitzustellen. Weitere Informationen zu benutzerdefinierten Standorten finden Sie in der [Dokumentation zu benutzerdefinierten Standorten in Azure Arc-fähigen Kubernetes-Clustern](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Überprüfen, ob der benutzerdefinierte Standort erstellt wurde

Führen Sie im Terminal den folgenden Befehl aus, um die benutzerdefinierten Standorte aufzulisten, und überprüfen Sie, ob für **Bereitstellungsstatus** der Status „Erfolgreich“ angezeigt wird:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Erstellen des Azure Arc-Datencontrollers

Wechseln Sie nach dem Erstellen der Erweiterung und des benutzerdefinierten Standorts zum Azure-Portal, um den Azure Arc-Datencontroller bereitzustellen.

1. Melden Sie sich im Azure-Portal an.
1. Suchen Sie im Azure Marketplace nach „Azure Arc-Datencontroller“, und initiieren Sie den Erstellungsflow.
1. Stellen Sie im Abschnitt **Voraussetzungen** sicher, dass der Azure Arc-fähige Kubernetes-Cluster (direkter Modus) ausgewählt ist, und fahren Sie mit dem nächsten Schritt fort.
1. Wählen Sie im Abschnitt **Details zum Datencontroller** ein Abonnement und eine Ressourcengruppe aus.
1. Geben Sie einen Namen für den Datencontroller ein.
1. Wählen Sie basierend auf dem Kubernetes-Distributionsanbieter, für den Sie die Bereitstellung durchführen, ein Konfigurationsprofil aus.
1. Wählen Sie den benutzerdefinierten Standort aus, den Sie im den vorherigen Schritt erstellt haben.
1. Geben Sie Details für den Anmeldenamen und das Kennwort des Datencontrolleradministrators an.
1. Geben Sie Details zu Client-ID (ClientId), Mandanten-ID (TenantId) und geheimem Clientschlüssel für den Dienstprinzipal an, die zum Erstellen der Azure-Objekte verwendet werden. Ausführliche Anweisungen zum Erstellen eines Dienstprinzipalkontos und der Rollen, die für das Konto gewährt werden mussten, finden Sie unter [Hochladen von Nutzungsdaten, Metriken und Protokollen in Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md).
1. Klicken Sie auf **Weiter**, überprüfen Sie die Zusammenfassungsseite mit allen Details, und klicken Sie auf **Erstellen**.

## <a name="monitor-the-creation"></a>Überwachen der Erstellung

Wenn der Bereitstellungsstatus im Azure-Portal angibt, dass die Bereitstellung erfolgreich war, können Sie den Status der Arc-Datencontrollerbereitstellung im Cluster wie folgt überprüfen:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung](create-postgresql-hyperscale-server-group.md)

[Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc](create-sql-managed-instance.md)
