---
title: Erstellen eines Datencontrollers unter Verwendung von Kubernetes-Tools
description: Erstellen eines Datencontrollers unter Verwendung von Kubernetes-Tools
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2380a10d9a4a054a5d83b2c1096797e41bba7d0f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563604"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Erstellen eines Azure Arc-Datencontrollers unter Verwendung von Kubernetes-Tools


## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Übersicht im Thema [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md).

Zum Erstellen des Azure Arc-Datencontrollers mithilfe von Kubernetes-Tools müssen die Kubernetes-Tools installiert sein.  In den Beispielen in diesem Artikel wird `kubectl` verwendet, allerdings können auch ähnliche Ansätze mit anderen Kubernetes-Tools verfolgt werden, z. B. dem Kubernetes-Dashboard, `oc` oder `helm`, wenn Sie mit diesen Tools und Kubernetes-YAML/JSON-Dateien vertraut sind.

[Installieren des kubectl-Tools](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Einige der unten aufgeführten Schritte zum Erstellen des Azure Arc-Datencontrollers erfordern Administratorberechtigungen für Kubernetes-Cluster.  Wenn Sie kein Kubernetes-Clusteradministrator sind, müssen Sie den Kubernetes-Clusteradministrator bitten, diese Schritte für Sie auszuführen.

### <a name="cleanup-from-past-installations"></a>Bereinigen früherer Installationen

Wenn Sie in der Vergangenheit den Azure Arc-Datencontroller auf demselben Cluster installiert und den Azure Arc-Datencontroller gelöscht haben, sind möglicherweise noch Objekte auf Clusterebene vorhanden, die noch gelöscht werden müssten. Führen Sie die folgenden Befehle aus, um Objekte des Azure Arc-Datencontrollers auf Clusterebene zu löschen:

```console
# Cleanup azure arc data service artifacts

# Note: not all of these objects will exist in your environment depending on which version of the Arc data controller was installed

# Custom resource definitions (CRD)
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

# Cluster roles and role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole arc:cr-arc-metricsdc-reader
kubectl delete clusterrole arc:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job
kubectl delete clusterrole {namespace}:cr-upgrade-worker
kubectl delete clusterrolebinding {namespace}:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding {namespace}:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job
kubectl delete clusterrolebinding {namespace}:crb-upgrade-worker

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get clusterrolebinding'

# API services
# Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

# June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

# GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get mutatingwebhookconfiguration'
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-{namespace}

```

## <a name="overview"></a>Übersicht

Das Erstellen des Azure Arc-Datencontrollers umfasst die folgenden allgemeinen Schritte:

   > [!IMPORTANT]
   > Einige der folgenden Schritte erfordern Administratorberechtigungen für Kubernetes-Cluster.

1. Erstellen Sie die benutzerdefinierten Ressourcendefinitionen für den Arc-Datencontroller, die Azure SQL Managend Instance-Instanz und für PostgreSQL Hyperscale. 
1. Erstellen Sie einen Namespace, in dem der Datencontroller erstellt wird. 
1. Erstellen Sie den Bootstrapperdienst, einschließlich der Replikatgruppe, des Dienstkontos, der Rolle und der Rollenbindung.
1. Erstellen Sie ein Geheimnis für den Benutzernamen und das Kennwort des Datencontrolleradministrators.
1. Erstellen Sie den Webhook-Bereitstellungsauftrag, die Clusterrolle und die Clusterrollenbindung. 
1. Erstellen Sie den Datencontroller.


## <a name="create-the-custom-resource-definitions"></a>Erstellen der benutzerdefinierten Ressourcendefinitionen

Führen Sie den folgenden Befehl aus, um die benutzerdefinierten Ressourcendefinitionen zu erstellen.  

   > [!IMPORTANT]
   > Erfordert Administratorberechtigungen für Kubernetes-Cluster.

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Erstellen eines Namespace, in dem der Datencontroller erstellt wird

Führen Sie einen mit dem folgenden vergleichbaren Befehl aus, um einen neuen dedizierten Namespace zu erstellen, in dem der Datencontroller erstellt wird.  In diesem Beispiel und den restlichen Beispielen in diesem Artikel wird der Namespacename `arc` verwendet. Wenn Sie sich für einen anderen Namen entscheiden, müssen Sie diesen durchgehend verwenden.

```console
kubectl create namespace arc
```
Wenn Sie OpenShift verwenden, müssen Sie die Anmerkungen `openshift.io/sa.scc.supplemental-groups` und `openshift.io/sa.scc.uid-range` für den Namespace mithilfe von `kubectl edit namespace <name of namespace>` bearbeiten.  Ändern Sie diese vorhandenen Anmerkungen so, dass sie mit diesen _spezifischen_ UID- und fsGroup-IDs/-Bereichen übereinstimmen.

```console
openshift.io/sa.scc.supplemental-groups: 1000700001/10000
openshift.io/sa.scc.uid-range: 1000700001/10000
```

Wenn dieser Namespace von anderen Personen verwendet wird, bei denen es sich nicht um Clusteradministratoren handelt, empfiehlt es sich, eine Namespace-Administratorrolle zu erstellen und diesen Benutzern die Rolle mithilfe einer Rollenbindung zuzuweisen.  Der Namespaceadministrator sollte über vollständige Berechtigungen für den Namespace verfügen.  Stärker granulare Rollen und Beispielrollenbindungen finden Sie im [Azure Arc GitHub Repository](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/rbac).

## <a name="create-the-bootstrapper-service"></a>Erstellen des Bootstrapperdiensts

Der Bootstrapperdienst verarbeitet eingehende Anforderungen zum Erstellen, Bearbeiten und Löschen benutzerdefinierter Ressourcen, beispielsweise eines Datencontrollers, von SQL Managed Instance-Instanzen oder von PostgreSQL Hyperscale-Servergruppen.

Führen Sie den folgenden Befehl aus, um einen Bootstrapperdienst, ein Dienstkonto für den Bootstrapperdienst sowie eine Rolle und eine Rollenbindung für das Konto des Bootstrapperdiensts zu erstellen.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Überprüfen Sie mithilfe des folgenden Befehls, ob der Bootstrapperpod ausgeführt wird.  Sie müssen den Befehl möglicherweise mehrmals ausführen, bis sich der Status in `Running` ändert.

```console
kubectl get pod --namespace arc
```

Mit der Vorlagendatei „bootstrapper.yaml“ wird standardmäßig das Bootstrapper-Containerimage aus der Microsoft Container Registry (MCR) gepullt.  Wenn von Ihrer Umgebung nicht direkt auf die Microsoft Container Registry zugegriffen werden kann, können Sie wie folgt verfahren:
- Befolgen Sie die Schritte, mit denen Sie [Containerimages aus der Microsoft Container Registry pullen und in eine private Containerregistrierung pushen](offline-deployment.md) können.
- [Erstellen Sie ein Geheimnis für Imagepullvorgänge](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) für Ihre private Containerregistrierung.
- Fügen Sie dem Bootstrappercontainer ein Geheimnis für Imagepullvorgänge hinzu. Ein Beispiel sehen Sie unten.
- Ändern Sie den Imagespeicherort für das Bootstrapperimage. Ein Beispiel sehen Sie unten.

Das folgende Beispiel setzt voraus, dass Sie ein Geheimnis für Imagepullvorgänge namens `arc-private-registry` erstellt haben.

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:v1.1.0_2021-11-02 #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-secrets-for-the-metrics-and-logs-dashboards"></a>Erstellen von Geheimnissen für die Dashboards mit Metriken und Protokollen

Sie können einen Benutzernamen und ein Kennwort angeben, die für die Authentifizierung bei den Dashboards mit Metriken und Protokollen als Administrator verwendet werden. Wählen Sie ein sicheres Kennwort aus, und geben Sie es nur an Personen weiter, die diese Berechtigungen besitzen müssen.

Ein Kubernetes-Geheimnis wird als Base64-codierte Zeichenfolge gespeichert – eine für den Benutzernamen und eine für das Kennwort.

Sie können ein Onlinetool verwenden, um den gewünschten Benutzernamen und das zugehörige Kennwort mit einer Base64-Codierung zu versehen, oder Sie können abhängig von Ihrer Plattform integrierte CLI-Tools verwenden.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Nachdem Sie die Benutzernamen und Kennwörter codiert haben, können Sie basierend auf der [Vorlagendatei](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) eine Datei erstellen und die Benutzernamen und Kennwörter durch Ihre eigenen ersetzen.

Führen Sie dann den folgenden Befehl aus, um das Geheimnis zu erstellen.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-webhook-deployment-job-cluster-role-and-cluster-role-binding"></a>Erstellen Sie den Webhook-Bereitstellungsauftrag, die Clusterrolle und die Clusterrollenbindung.

Erstellen Sie zunächst eine lokale Kopie der [Vorlagendatei](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/web-hook.yaml) auf Ihrem Computer, damit Sie einige Einstellungen ändern können.

Bearbeiten Sie die Datei, und ersetzen Sie `{{namespace}}` an allen Stellen durch den Namen des Namespace, den Sie im vorherigen Schritt erstellt haben. **Speichern Sie die Datei .**

Führen Sie den folgenden Befehl aus, um die Clusterrolle und die Clusterrollenbindungen zu erstellen.  

   > [!IMPORTANT]
   > Erfordert Administratorberechtigungen für Kubernetes-Cluster.

```console
kubectl create -n arc -f <path to the edited template file on your computer>
```


## <a name="create-the-data-controller"></a>Erstellen des Datencontrollers

Nun können Sie den Datencontroller selbst erstellen.

Erstellen Sie zunächst eine lokale Kopie der [Vorlagendatei](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) auf Ihrem Computer, damit Sie einige Einstellungen ändern können.

Bearbeiten Sie die folgenden Einstellungen nach Bedarf:

**ERFORDERLICH**
- **location**: Ändern Sie diese Einstellung in den Azure-Standort, an dem die _Metadaten_ zum Datencontroller gespeichert werden.  Die Liste der verfügbaren Azure-Standorte finden Sie im Artikel mit der [Übersicht zum Erstellen eines Datencontrollers](create-data-controller.md).
- **resourceGroup**: Die Azure-Ressourcengruppe, in der Sie die Azure-Ressource für den Datencontroller im Azure Resource Manager erstellen möchten.  Diese Ressourcengruppe sollte in der Regel bereits vorhanden sein, wird jedoch erst benötigt, wenn Sie die Daten in Azure hochladen.
- **subscription**: Die Azure-Abonnement-GUID für das Abonnement, in dem Sie die Azure-Ressourcen erstellen möchten.

**ÜBERPRÜFUNG UND GGF. ÄNDERUNG DER STANDARDWERTE EMPFOHLEN**
- **storage..className**: Die Speicherklasse, die für die Daten- und Protokolldateien des Datencontrollers verwendet werden soll.  Wenn Sie nicht sicher sind, welche Speicherklassen in Ihrem Kubernetes-Cluster verfügbar sind, können Sie den folgenden Befehl ausführen: `kubectl get storageclass`.  Der Standardwert ist `default`. Dabei wird davon ausgegangen, dass eine Speicherklasse mit dem Namen `default` vorhanden ist, und _nicht_, dass eine Speicherklasse vorhanden ist, die der Standardspeicherklasse entspricht.  Hinweis: Es gibt zwei className-Einstellungen, die auf die gewünschte Speicherklasse festgelegt werden müssen – eine für Daten und eine für Protokolle.
- **serviceType**: Ändern Sie den Diensttyp in `NodePort`, wenn Sie keinen LoadBalancer verwenden.
- **Sicherheit:** Ersetzen Sie bei Azure Red Hat OpenShift oder bei der Red Hat OpenShift-Containerplattform die Einstellungen vom Typ `security:` durch die folgenden Werte in der YAML-Datei des Datencontrollers.

```yml
  security:
    allowDumps: false
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
```

**OPTIONAL**
- **name:** Der Standardname des Datencontrollers lautet `arc`. Sie können ihn jedoch ändern.
- **displayName**: Legen Sie diesen Wert auf denselben Wert fest wie das name-Attribut am Anfang der Datei.
- **registry**: Die Microsoft Container Registry ist die Standardregistrierung.  Wenn Sie die Images aus der Microsoft Container Registry pullen und [in eine private Containerregistrierung pushen](offline-deployment.md), geben Sie hier die IP-Adresse oder den DNS-Namen Ihrer Registrierung ein.
- **dockerRegistry**: Das Geheimnis für Imagepullvorgänge, das verwendet wird, um die Images ggf. aus einer privaten Containerregistrierung zu pullen.
- **repository**: Das Standardrepository der Microsoft Container Registry lautet `arcdata`.  Wenn Sie eine private Containerregistrierung verwenden, geben Sie den Pfad für den Ordner/das Repository mit den Containerimages der Azure Arc-fähigen Datendienste ein.
- **imageTag**: Das aktuelle Tag der letzten Version wird standardmäßig in der Vorlage verwendet, Sie können es jedoch ändern, wenn Sie eine ältere Version verwenden möchten.

Das folgende Beispiel zeigt die vollständige YAML-Datei eines Datencontrollers. Aktualisieren Sie das Beispiel für Ihre Umgebung basierend auf Ihren Anforderungen und den oben genannten Informationen.

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-arc-controller
---
apiVersion: arcdata.microsoft.com/v1
kind: DataController
metadata:
  generation: 1
  name: arc-dc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-arc-controller
  docker:
    imagePullPolicy: Always
    imageTag: v1.1.0_2021-11-02
    registry: mcr.microsoft.com
    repository: arcdata
  infrastructure: other #Must be a value in the array [alibaba, aws, azure, gcp, onpremises, other]
  security:
    allowDumps: true #Set this to false if deploying on OpenShift
    allowNodeMetricsCollection: true #Set this to false if deploying on OpenShift
    allowPodMetricsCollection: true #Set this to false if deploying on OpenShift
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc-dc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

Speichern Sie die bearbeitete Datei auf Ihrem lokalen Computer, und führen Sie den folgenden Befehl aus, um den Datencontroller zu erstellen:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Das Erstellen des Controllers dauert einige Minuten. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Bei den folgenden Beispielbefehlen wird davon ausgegangen, dass Sie einen Datencontroller und Kubernetes-Namespace mit dem Namen `arc` erstellt haben.  Wenn Sie einen anderen Namespace-/Datencontrollernamen verwendet haben, können Sie `arc` durch diesen Namen ersetzen.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie einen Befehl wie den folgenden ausführen.  Dies ist besonders bei der Problembehandlung hilfreich.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer verwalteten SQL-Instanz mithilfe nativer Kubernetes-Tools](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Erstellen einer PostgreSQL Hyperscale-Servergruppe mithilfe nativer Kubernetes-Tools](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
