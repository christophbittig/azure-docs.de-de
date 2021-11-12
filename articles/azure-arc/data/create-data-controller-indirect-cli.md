---
title: Erstellen eines Datencontrollers mithilfe der Befehlszeilenschnittstelle
description: Erstellen Sie mithilfe der Befehlszeilenschnittstelle einen Azure Arc-Datencontroller in einem typischen Kubernetes-Cluster mit mehreren Knoten, den Sie bereits erstellt haben.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 836ca4cca73b71f98415c05fd89227f53332a265
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555379"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>Erstellen eines Azure Arc-Datencontrollers mithilfe der Befehlszeilenschnittstelle


## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Übersicht im Thema [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md).

### <a name="install-tools"></a>Installieren von Tools

Um den Datencontroller mithilfe der Befehlszeilenschnittstelle zu erstellen, müssen Sie die Erweiterung `arcdata` für die Azure-Befehlszeilenschnittstelle (az) installieren. 

[Installieren der [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Unabhängig davon, welche Zielplattform Sie wählen, müssen Sie die folgenden Umgebungsvariablen vor der Erstellung des Data Controllers festlegen. Diese Umgebungsvariablen werden nach der Erstellung des Datencontrollers zu den Anmeldeinformationen, die für den Zugriff auf die Dashboards für Metriken und Protokolle verwendet werden.


### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Nachfolgend finden Sie zwei Sätze von Umgebungsvariablen, die für den Zugriff auf die Dashboards für Metriken und Protokolle erforderlich sind.

#### <a name="windows-powershell"></a>Windows PowerShell

```powershell
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

#### <a name="linux-or-macos"></a>Linux oder macOS

```console
export AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
export AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
export AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
export AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```


Sie müssen eine Verbindung mit einem Kubernetes-Cluster herstellen und sich authentifizieren sowie einen vorhandenen Kubernetes-Kontext auswählen, bevor Sie mit der Erstellung des Azure Arc-Datencontrollers beginnen. Wie Sie eine Verbindung mit einem Kubernetes-Cluster oder -Dienst herstellen, ist von Fall zu Fall unterschiedlich. Informieren Sie sich in der Dokumentation zur verwendeten Kubernetes-Distribution oder zum verwendeten Kubernetes-Dienst darüber, wie eine Verbindung mit dem Kubernetes-API-Server hergestellt wird.

Mit den folgenden Befehlen können Sie überprüfen, ob Sie über eine aktuelle Kubernetes-Verbindung verfügen, und den aktuellen Kontext bestätigen.

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Erstellen des Azure Arc-Datencontrollers

> [!NOTE]
> In den folgenden Beispielen können Sie verschiedene Werte für den Parameter `--namespace` des Befehls `az arcdata dc create` verwenden. Achten Sie jedoch darauf, dass Sie diesen Namespacenamen für den Parameter `--namespace` in allen anderen unten aufgeführten Befehlen verwenden.

- [Erstellen eines Azure Arc-Datencontrollers mithilfe der Befehlszeilenschnittstelle](#create-azure-arc-data-controller-using-the-cli)
  - [Voraussetzungen](#prerequisites)
    - [Linux oder macOS](#linux-or-macos)
    - [Windows PowerShell](#windows-powershell)
  - [Erstellen des Azure Arc-Datencontrollers](#create-the-azure-arc-data-controller)
    - [Erstellen im Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)
    - [Erstellen in AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
    - [Erstellen in Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
      - [Erstellen eines benutzerdefinierten Bereitstellungsprofils](#create-custom-deployment-profile)
      - [Erstellen eines Datencontrollers](#create-data-controller)
    - [Erstellen auf der Red Hat OpenShift Container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
      - [Bestimmen der Speicherklasse](#determine-storage-class)
      - [Erstellen eines benutzerdefinierten Bereitstellungsprofils](#create-custom-deployment-profile-1)
      - [Festlegen der Speicherklasse](#set-storage-class)
      - [Festlegen von LoadBalancer (optional)](#set-loadbalancer-optional)
      - [Erstellen eines Datencontrollers](#create-data-controller-1)
    - [Erstellen in der Open-Source-Upstreamversion von Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
    - [Erstellen im AWS Elastic Kubernetes Service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
    - [Erstellen im Google Cloud Kubernetes Engine Service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)
  - [Überwachen des Erstellungsstatus](#monitoring-the-creation-status)
  - [Beheben von Problemen bei der Erstellung](#troubleshooting-creation-problems)

### <a name="create-on-azure-kubernetes-service-aks"></a>Erstellen im Azure Kubernetes Service (AKS)

Das AKS-Bereitstellungsprofil nutzt standardmäßig die Speicherklasse `managed-premium`. Die Speicherklasse `managed-premium` funktioniert nur, wenn Sie über VMs verfügen, die mithilfe von VM-Images mit Premium-Datenträgern bereitgestellt wurden.

Wenn Sie `managed-premium` als Speicherklasse verwenden möchten, können Sie den folgenden Befehl ausführen, um den Datencontroller zu erstellen. Ersetzen Sie die Platzhalter im Befehl durch den Namen Ihrer Ressourcengruppe, die Abonnement-ID und den Azure-Standort.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

Wenn Sie nicht sicher sind, welche Speicherklasse Sie verwenden sollen, verwenden Sie die Speicherklasse `default`, die unabhängig vom verwendeten VM-Typ unterstützt wird. Eine Einschränkung besteht jedoch darin, dass sie nicht die höchste Leistung bietet.

Wenn Sie die Speicherklasse `default` verwenden möchten, können Sie den folgenden Befehl ausführen:

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-aks-on-azure-stack-hci"></a>Erstellen in AKS in Azure Stack HCI

#### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>Konfigurieren des Speichers (Azure Stack HCI mit AKS-HCI)

Wenn Sie Azure Stack HCI mit AKS-HCI verwenden, erstellen Sie mit `fsType` eine benutzerdefinierte Speicherklasse.

   ```json
   fsType: ext4
   ```

Verwenden Sie diesen Typ, um den Datencontroller bereitzustellen. Umfassende Anweisungen finden Sie unter [Erstellen einer benutzerdefinierten Speicherklasse für einen „AKS in Azure Stack HCI“-Datenträger](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk).

Das Bereitstellungsprofil nutzt standardmäßig die Speicherklasse mit dem Namen `default` und den Diensttyp `LoadBalancer`.

Sie können den folgenden Befehl ausführen, um den Datencontroller unter Verwendung der Speicherklasse `default` und des Diensttyps `LoadBalancer` zu erstellen.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.


### <a name="create-on-azure-red-hat-openshift-aro"></a>Erstellen in Azure Red Hat OpenShift (ARO)

#### <a name="create-custom-deployment-profile"></a>Erstellen eines benutzerdefinierten Bereitstellungsprofils

Verwenden Sie das Profil `azure-arc-azure-openshift` für Azure RedHat Open Shift.

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Erstellen eines Datencontrollers

Führen Sie den folgenden Befehl zum Erstellen des Datencontrollers aus.

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Erstellen auf der Red Hat OpenShift Container Platform (OCP)

#### <a name="determine-storage-class"></a>Bestimmen der Speicherklasse

Sie müssen außerdem bestimmen, welche Speicherklasse verwendet werden soll, indem Sie den folgenden Befehl ausführen.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Erstellen eines benutzerdefinierten Bereitstellungsprofils

Erstellen Sie eine neue benutzerdefinierte Datei für das Bereitstellungsprofil auf Grundlage des Bereitstellungsprofils `azure-arc-openshift`, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden das Verzeichnis `custom` im aktuellen Arbeitsverzeichnis und die benutzerdefinierte Bereitstellungsprofildatei `control.json` in diesem Verzeichnis erstellt.

Verwenden Sie das Profil `azure-arc-openshift` für die OpenShift Container Platform.

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Festlegen der Speicherklasse 

Legen Sie nun die gewünschte Speicherklasse fest, indem Sie `<storageclassname>` im folgenden Befehl durch den Namen der gewünschten Speicherklasse ersetzen, die Sie mithilfe des oben angegebenen Befehls `kubectl get storageclass` ermittelt haben.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Festlegen von LoadBalancer (optional)

Das `azure-arc-openshift`-Bereitstellungsprofil verwendet standardmäßig `NodePort` als Diensttyp. Wenn Sie einen OpenShift-Cluster mit integriertem Lastenausgleich verwenden, können Sie die Konfiguration mithilfe des folgenden Befehls so ändern, dass der Diensttyp `LoadBalancer` verwendet wird:

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="create-data-controller"></a>Erstellen eines Datencontrollers

Nun können Sie den Datencontroller mit dem folgenden Befehl erstellen.

> [!NOTE]
>   Der Parameter `--path` muss auf das _Verzeichnis_ mit der Datei „control.json“ und nicht auf die Datei „control.json“ selbst verweisen.

> [!NOTE]
>   Bei der Bereitstellung auf der OpenShift Container Platform müssen Sie einen Wert für den Parameter `--infrastructure` angeben.  Folgende Optionen sind möglich: `aws`, `azure`, `alibaba`, `gcp` oder `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Erstellen in der Open-Source-Upstreamversion von Kubernetes (kubeadm)

Das kubeadm-Bereitstellungsprofil nutzt standardmäßig eine Speicherklasse mit dem Namen `local-storage` und den Diensttyp `NodePort`. Wenn dies akzeptabel ist, können Sie die nachfolgenden Anweisungen überspringen, mit deren Hilfe die gewünschte Speicherklasse und der Diensttyp festgelegt werden, und den Befehl `az arcdata dc create` unten direkt ausführen.

Wenn Sie Ihr Bereitstellungsprofil anpassen möchten, um eine bestimmte Speicherklasse und/oder einen bestimmten Diensttyp anzugeben, erstellen Sie zunächst eine neue benutzerdefinierte Bereitstellungsprofildatei auf Grundlage des kubeadm-Bereitstellungsprofils, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden das Verzeichnis `custom` im aktuellen Arbeitsverzeichnis und die benutzerdefinierte Bereitstellungsprofildatei `control.json` in diesem Verzeichnis erstellt.

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom 
```

Sie können die verfügbaren Speicherklassen mithilfe des folgenden Befehls ermitteln.

```console
kubectl get storageclass
```

Legen Sie nun die gewünschte Speicherklasse fest, indem Sie `<storageclassname>` im folgenden Befehl durch den Namen der gewünschten Speicherklasse ersetzen, die Sie mithilfe des oben angegebenen Befehls `kubectl get storageclass` ermittelt haben.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Das kubeadm-Bereitstellungsprofil nutzt standardmäßig `NodePort` als Diensttyp. Wenn Sie einen Kubernetes-Cluster mit integriertem Lastenausgleich verwenden, können Sie die Konfiguration mithilfe des folgenden Befehls ändern.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

Nun können Sie den Datencontroller mit dem folgenden Befehl erstellen.

> [!NOTE]
>   Bei der Bereitstellung auf der OpenShift Container Platform müssen Sie einen Wert für den Parameter `--infrastructure` angeben.  Folgende Optionen sind möglich: `aws`, `azure`, `alibaba`, `gcp` oder `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Erstellen im AWS Elastic Kubernetes Service (EKS)

Standardmäßig lauten die EKS-Speicherklasse `gp2` und der Diensttyp `LoadBalancer`.

Führen Sie den folgenden Befehl aus, um den Datencontroller mit dem bereitgestellten EKS-Bereitstellungsprofil zu erstellen.

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Erstellen im Google Cloud Kubernetes Engine Service (GKE)

Standardmäßig lauten die GKE-Speicherklasse `standard` und der Diensttyp `LoadBalancer`.

Führen Sie den folgenden Befehl aus, um den Datencontroller mit dem bereitgestellten GKE-Bereitstellungsprofil zu erstellen.

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Das Erstellen des Controllers dauert einige Minuten. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Bei den folgenden Beispielbefehlen wird davon ausgegangen, dass Sie einen Datencontroller und Kubernetes-Namespace mit dem Namen `arc` erstellt haben. Wenn Sie einen anderen Namespace-/Datencontrollernamen verwendet haben, können Sie `arc` durch diesen Namen ersetzen.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie einen Befehl wie den folgenden ausführen. Dies ist besonders bei der Problembehandlung hilfreich.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).
