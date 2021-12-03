---
title: Problembehandlung bei allgemeinen Problemen mit Kubernetes mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 05/21/2021
ms.topic: article
description: Problembehandlung bei häufigen Problemen mit Azure Arc-fähigen Kubernetes-Clustern
keywords: Kubernetes, Arc, Azure, Container
ms.openlocfilehash: acbc8293f5aed737dba6ed3992fc59cac0a4fd55
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059239"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Problembehandlung bei Kubernetes mit Azure Arc-Unterstützung

Dieses Dokument enthält Anleitungen zur Problembehandlung bei Problemen mit der Konnektivität, den Berechtigungen und den Agents.

## <a name="general-troubleshooting"></a>Allgemeine Problembehandlung

### <a name="azure-cli"></a>Azure CLI

Überprüfen Sie vor dem Verwenden der CLI-Befehle `az connectedk8s` oder `az k8s-configuration`, ob die Azure CLI so eingerichtet ist, dass sie mit dem richtigen Azure-Abonnement funktioniert.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc-Agents

Alle Agents für Kubernetes mit Azure Arc-Unterstützung werden als Pods im `azure-arc`-Namespace bereitgestellt. Alle Pods sollten ausgeführt werden und ihre Integritätsprüfungen bestehen.

Überprüfen Sie zunächst die Azure Arc-Helm-Version:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Wenn das Helm-Release nicht gefunden wird oder fehlt, versuchen Sie, erneut eine [Verbindung vom Cluster mit Azure Arc herzustellen](./quickstart-connect-cluster.md).

Wenn das Helm-Release vorhanden ist und den `STATUS: deployed` aufweist, überprüfen Sie den Status des Agents mithilfe von `kubectl`:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Alle Pods sollten `STATUS` als `Running` mit `3/3` oder `2/2` unter der Spalte `READY` anzeigen. Rufen Sie Protokolle ab, und beschreiben Sie die Pods, die `Error` oder `CrashLoopBackOff` zurückgeben. Wenn Pods im Zustand `Pending` hängengeblieben sind, sind möglicherweise nicht ausreichend Ressourcen auf den Clusterknoten vorhanden. Wenn Sie [Ihren Cluster hochskalieren](https://kubernetes.io/docs/tasks/administer-cluster/), können diese Pods in den Zustand `Running` übergehen.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Verbinden von Kubernetes-Clustern mit Azure Arc

Das Herstellen einer Verbindung von Clustern mit Azure erfordert Zugriff auf ein Azure-Abonnement und `cluster-admin`-Zugriff auf einen Zielcluster. Wenn Sie den Cluster nicht erreichen können oder Sie nicht über ausreichende Berechtigungen verfügen, tritt beim Herstellen einer Verbindung zwischen Cluster und Azure Arc ein Fehler auf.

### <a name="azure-cli-is-unable-to-download-helm-chart-for-azure-arc-agents"></a>Die Azure-Befehlszeilenschnittstelle kann kein Helm-Chart für Azure Arc-Agents herunterladen.

Wenn Sie eine Helm-Version ab 3.7.0 verwenden, tritt der folgende Fehler auf, wenn `az connectedk8s connect` ausgeführt wird, um den Cluster mit Azure Arc zu verbinden:

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest

Unable to pull helm chart from the registry 'mcr.microsoft.com/azurearck8s/batch1/stable/azure-arc-k8sagents:1.4.0': Error: unknown command "chart" for "helm"
Run 'helm --help' for usage.
```

In diesem Fall müssen Sie eine frühere Version von [Helm 3](https://helm.sh/docs/intro/install/) &lt; Version 3.7.0 installieren. Führen Sie anschließend den Befehl `az connectedk8s connect` erneut aus, um den Cluster mit Azure Arc zu verbinden.

### <a name="insufficient-cluster-permissions"></a>Unzureichende Clusterberechtigungen

Wenn die bereitgestellte kubeconfig-Datei nicht über ausreichende Berechtigungen zum Installieren von Azure Arc-Agents verfügt, gibt der Azure CLI-Befehl einen Fehler zurück.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Dem Benutzer, der den Cluster mit Azure Arc verbindet, sollte die Rolle `cluster-admin` auf dem Cluster zugewiesen sein.


### <a name="unable-to-connect-openshift-cluster-to-azure-arc"></a>Herstellen einer Verbindung zwischen OpenShift-Cluster und Azure Arc nicht möglich

Wenn beim Verbinden eines OpenShift-Clusters mit Azure Arc ein Timeout für `az connectedk8s connect` auftritt und der Befehl fehlschlägt, überprüfen Sie Folgendes:

1. Der OpenShift-Cluster muss die erforderlichen Versionsvoraussetzungen erfüllen: 4.5.41 oder höher oder 4.6.35 oder höher oder 4.7.18 oder höher.

1. Vor dem Ausführen von `az connectedk8s connnect` muss der folgende Befehl im Cluster ausgeführt werden:

    ```console
    oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
    ```

### <a name="installation-timeouts"></a>Timeouts bei der Installation

Das Herstellen einer Verbindung zwischen einem Kubernetes-Cluster und Kubernetes mit Azure Arc-Unterstützung erfordert die Installation von Azure Arc-Agents im Cluster. Wenn der Cluster über eine langsame Internetverbindung ausgeführt wird, kann das Pullen des Containerimages für Agents länger dauern, als die Azure CLI-Timeouts erlauben.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problem mit Helm

Die Helm-Version `v3.3.0-rc.1` hat ein [Problem](https://github.com/helm/helm/pull/8527). Nach der Helm-Installation bzw. dem Helm-Upgrade (von der CLI-Erweiterung `connectedk8s` verwendet) wird durch das Ausführen von allen Hooks der folgende Fehler erzeugt:

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Befolgen Sie diese Schritte, um das Problem zu beheben:

1. Löschen Sie die Kubernetes-Ressource mit Azure Arc-Unterstützung im Azure-Portal.
2. Führen Sie die folgenden Befehle auf dem Computer aus:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installieren Sie eine stabile Version](https://helm.sh/docs/intro/install/) von Helm 3 auf Ihrem Computer anstelle der Release Candidate-Version.
4. Führen Sie den Befehl `az connectedk8s connect` mit den entsprechenden Werten aus, um den Cluster mit Azure Arc zu verbinden.

## <a name="configuration-management"></a>Konfigurationsverwaltung

### <a name="general"></a>Allgemein
Um Probleme mit der Konfigurationsressource zu beheben, führen Sie az-Befehle mit Angabe des Parameters `--debug` aus.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Erstellen von Konfigurationen

Schreibberechtigungen auf der Kubernetes-Ressource mit Azure Arc-Unterstützung (`Microsoft.Kubernetes/connectedClusters/Write`) sind notwendig und ausreichend, um Konfigurationen in diesem Cluster zu erstellen.

### <a name="configuration-remains-pending"></a>Konfiguration hat unverändert den Status `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

## <a name="monitoring"></a>Überwachung

Azure Monitor für Container erfordert die Ausführung des DaemonSet im privilegierten Modus. Führen Sie den folgenden Befehl aus, um einen Canonical Charmed Kubernetes-Cluster für die Überwachung einzurichten:

```console
juju config kubernetes-worker allow-privileged=true
```

## <a name="enable-custom-locations-using-service-principal"></a>Aktivieren von benutzerdefinierten Standorten per Dienstprinzipal

Wenn Sie für Ihren Cluster eine Verbindung mit Azure Arc herstellen oder für einen vorhandenen Cluster das Feature für benutzerdefinierte Standorte aktivieren, wird ggf. die folgende Warnung angezeigt:

```console
Unable to fetch oid of 'custom-locations' app. Proceeding without enabling the feature. Insufficient privileges to complete the operation.
```

Die obige Warnung tritt auf, wenn Sie für die Anmeldung bei Azure einen Dienstprinzipal verwendet haben, der nicht über Berechtigungen zum Abrufen von Informationen für die vom Azure Arc-Dienst genutzte Anwendung verfügt. Um diesen Fehler zu vermeiden, führen Sie die folgenden Schritte aus:

1. Rufen Sie die Objekt-ID der Azure AD-Anwendung ab, die vom Azure Arc-Dienst verwendet wird:

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Verwenden Sie den `<objectId>`-Wert aus dem obigen Schritt, um das Feature „Benutzerdefinierte Speicherorte“ im Cluster zu aktivieren:
    - Wenn Sie das Feature „Benutzerdefinierte Speicherorte“ während der Verbindung des Clusters mit Arc aktivieren, führen Sie den folgenden Befehl aus:

        ```console
        az connectedk8s connect -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId>   
        ```

    - Wenn Sie das Feature „Benutzerdefinierte Speicherorte“ in einem vorhandenen Azure Arc-fähigen Kubernetes-Cluster aktivieren, führen Sie den folgenden Befehl aus:

        ```console
        az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
        ```

Nachdem die obigen Berechtigungen gewährt wurden, können Sie mit dem [Aktivieren des Features für benutzerdefinierte Standorte](custom-locations.md#enable-custom-locations-on-cluster) im Cluster fortfahren.

## <a name="azure-arc-enabled-open-service-mesh"></a>Open Service Mesh mit Azure Arc-Unterstützung

Die folgenden Schritte zur Problembehandlung bieten eine Anleitung zum Überprüfen der Bereitstellung aller Open Service Mesh-Erweiterungskomponenten in Ihrem Cluster.

### <a name="1-check-osm-controller-deployment"></a>1. Überprüfen der OSM-Controller-**Bereitstellung**
```bash
kubectl get deployment -n arc-osm-system --selector app=osm-controller
```

Wenn der OSM-Controller fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="2-check-the-osm-controller-pod"></a>2. Überprüfen des OSM-Controller-**Pods**
```bash
kubectl get pods -n arc-osm-system --selector app=osm-controller
```

Wenn der OSM-Controller fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Obwohl ein Controller zu einem bestimmten Zeitpunkt den Status _Evicted_ (Entfernt) aufwies, gibt es einen anderen Controller mit den Werten `READY 1/1` und `Running` sowie `0` Neustarts.
Wenn die Spalte `READY` einen anderen Wert als `1/1` aufweist, befindet sich das Dienstmesh in einem defekten Zustand.
Wenn die Spalte `READY` den Wert `0/1` anzeigt, bedeutet dies, dass der Steuerungsebenencontainer abstürzt – es müssen die entsprechenden Protokolle abgerufen werden. Mehr dazu erfahren Sie im Abschnitt `Get OSM Controller Logs from Azure Support Center` weiter unten.
Wenn die Spalte `READY` nach dem `/` eine Zahl größer als 1 zeigt, würde dies darauf hinweisen, dass Sidecars installiert sind. OSM Controller würde höchstwahrscheinlich nicht mit angeschlossenen Seitenwagen funktionieren.

### <a name="3-check-osm-controller-service"></a>3. Überprüfen des OSM-Controller-**Diensts**
```bash
kubectl get service -n arc-osm-system osm-controller
```

Wenn der OSM-Controller fehlerfrei ist, erhalten Sie die folgende Ausgabe:
```
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> Hinweis: Die `CLUSTER-IP` kann abweichen. `NAME` und `PORT(S)` für den Dienst müssen mit den Werten in der Ausgabe übereinstimmen.

### <a name="4-check-osm-controller-endpoints"></a>4. Überprüfen der OSM-Controller-**Endpunkte**
```bash
kubectl get endpoints -n arc-osm-system osm-controller
```

Wenn der OSM-Controller fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

Wenn der Cluster des Benutzers über keine `ENDPOINTS` für `osm-controller` verfügt, würde dies darauf hinweisen, dass die Steuerungsebene fehlerhaft ist. Dies kann darauf zurückzuführen sein, dass der OSM-Controller-Pod abstürzt oder nie ordnungsgemäß bereitgestellt wurde.

### <a name="5-check-osm-injector-deployment"></a>5. Überprüfen der OSM-Injektor-**Bereitstellung**
```bash
kubectl get deployments -n arc-osm-system osm-injector
```

Wenn der OSM-Injektor fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
osm-injector   1/1     1            1           73m
```

### <a name="6-check-osm-injector-pod"></a>6. Überprüfen des OSM-Injektor-**Pods**
```bash
kubectl get pod -n arc-osm-system --selector app=osm-injector
```

Wenn der OSM-Injektor fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

Die Spalte `READY` muss `1/1` lauten. Jeder andere Wert würde auf einen fehlerhaften OSM-Injektor-Pod hindeuten.

### <a name="7-check-osm-injector-service"></a>7. Überprüfen des OSM-Injektor-**Diensts**
```bash
kubectl get service -n arc-osm-system osm-injector
```

Wenn der OSM-Injektor fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

Stellen Sie sicher, dass die für den Dienst `osm-injector` aufgeführte IP-Adresse `9090` lautet. Es darf keine `EXTERNAL-IP` vorhanden sein.

### <a name="8-check-osm-injector-endpoints"></a>8. Überprüfen der OSM-Injektor-**Endpunkte**
```bash
kubectl get endpoints -n arc-osm-system osm-injector
```

Wenn der OSM-Injektor fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

Damit OSM funktioniert, muss mindestens ein Endpunkt für `osm-injector` vorhanden sein. Die IP-Adresse Ihrer OSM-Injektor-Endpunkte lautet anders als hier gezeigt. Der Port `9090` muss identisch sein.


### <a name="9-check-validating-and-mutating-webhooks"></a>9. Überprüfen der Webhooks **Validating** und **Mutating**
```bash
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Wenn der Webhook „Validating“ fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      81m
```

```bash
kubectl get MutatingWebhookConfiguration --selector app=osm-controller
```


Wenn der Webhook „Mutating“ fehlerfrei ist, erhalten Sie eine Ausgabe ähnlich der folgenden:
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      102m
```

Überprüfen Sie den Dienst und das CA-Bundle des Webhooks **Validating**:
```
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Eine ordnungsgemäße Konfiguration des Webhooks „Validating“ würde die folgende Ausgabe ergeben:
```json
{
  "name": "osm-config-validator",
  "namespace": "arc-osm-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

Überprüfen Sie den Dienst und das CA-Bundle des Webhooks **Mutating**:
```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Eine ordnungsgemäße Konfiguration des Webhooks „Mutating“ würde die folgende Ausgabe ergeben:
```
{
  "name": "osm-injector",
  "namespace": "arc-osm-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```


Überprüfen Sie mit dem folgenden Befehl, ob der OSM-Controller dem Webhook „Validating“ (oder „Mutating“) ein CA-Bundle übergeben hat:

```bash
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

Beispielausgabe:
```bash
1845
```
Die Zahl in der Ausgabe gibt die Anzahl von Bytes oder die Größe des CA-Bundles an. Wenn dieser Wert leer ist, die Zahl 0 oder eine andere Zahl unter 1000 aufweist, deutet dies darauf hin, dass das CA-Bundle nicht ordnungsgemäß bereitgestellt ist. Ohne ein korrektes CA-Bundle würde der Webhook „Validieren“ einen Fehler auslösen und verhindern, dass Sie Änderungen an der `osm-config`-ConfigMap im Namespace `arc-osm-system` vornehmen können.

Hier sehen Sie einen Beispielfehler, wenn das CA-Bundle falsch ist:
- Ein Versuch, die `osm-config`-ConfigMap zu ändern:
  ```bash
  kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```
- Fehlerausgabe:
  ```bash
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.arc-osm-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

Verwenden Sie eine der folgenden Problemumgehungen, wenn die Konfiguration des Webhooks **Validating** ein ungültiges Zertifikat aufweist:
- Option 1. Neustart des OSM-Controllers: Damit wird der OSM-Controller neu gestartet. Beim Start wird das CA-Bundle der beiden Webhooks Mutating und Validating überschrieben.
  ```bash
  kubectl rollout restart deployment -n arc-osm-system osm-controller
  ```

- Option 2. Löschen des Webhooks „Validating“: Durch Entfernen des Validating-Webhooks sind Mutationen der `osm-config`-ConfigMap nicht mehr gültig. Jeder Patch wird durchlaufen. Der OSM-Controller muss möglicherweise neu gestartet werden, um das CA-Bundle schnell neu zu schreiben.
   ```bash
   kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm
   ```

- Option 3. Löschen und Patchen: Mit dem folgenden Befehl wird der Webhook „Validating“ gelöscht, sodass Sie beliebige Werte hinzufügen können. Es wird sofort versucht, einen Patch anzuwenden.
  ```bash
  kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm; kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```


### <a name="10-check-the-osm-config-configmap"></a>10. Überprüfen der `osm-config`-**ConfigMap**

>[!Note]
>Für den OSM-Controller ist es nicht erforderlich, dass die `osm-config`-ConfigMap im `arc-osm-system`-Namespace vorhanden ist. Der Controller verfügt über angemessene Standardwerte für die Konfiguration und kann ohne ihn arbeiten.

Überprüfen des Vorhandenseins einer Datei:
```bash
kubectl get ConfigMap -n arc-osm-system osm-config
```

Überprüfen Sie den Inhalt der `osm-config`-ConfigMap:
```bash
kubectl get ConfigMap -n arc-osm-system osm-config -o json | jq '.data'     
```
Sie erhalten folgende Ausgabe:
```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false",
}
```

Sehen Sie sich die [Dokumentation zu OSM ConfigMap](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/) an, um die `osm-config`-ConfigMap-Werte zu verstehen.

### <a name="11-check-namespaces"></a>11. Überprüfen der Namespaces

>[!Note]
>Der arc-osm-system-Namespace nimmt nie an einem Dienstmesh teil und erhält nie Bezeichnungen/Anmerkungen mit den unten stehenden Schlüsseln/Werten.

Wir verwenden den `osm namespace add` Befehl, um Namespaces mit einem bestimmten Service Mesh zu verknüpfen.
Wenn ein Kubernetes-Namespace zum Mesh gehört, muss Folgendes zutreffen:

Zeigen Sie die Anmerkungen des Namespace `bookbuyer` an:
```bash
kc get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Die folgenden Eigenschaften müssen vorhanden sein:
```
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```


Zeigen Sie die Bezeichnungen des Namespace `bookbuyer` an:
```bash
kc get namespace bookbuyer -o json | jq '.metadata.labels'
```

Die folgenden Eigenschaften müssen vorhanden sein:
```
{
  "openservicemesh.io/monitored-by": "osm"
}
```
Beachten Sie, dass Sie diese Anmerkungen auch manuell zu Ihren Namespaces hinzufügen können, wenn Sie die `osm`-CLI nicht verwenden. Wenn ein Namespace nicht mit `"openservicemesh.io/sidecar-injection": "enabled"` annotiert oder nicht mit `"openservicemesh.io/monitored-by": "osm"`dem OSM-Injektor beschriftet ist, werden keine Envoy-Sidecars hinzugefügt.

>[!Note]
>Nachdem `osm namespace add` aufgerufen wurde, werden nur **neue** Pods mit dem Sidecar „Envoy“ eingefügt. Vorhandene Pods müssen mit dem Befehl `kubectl rollout restard deployment` neu gestartet werden.


### <a name="12-verify-the-smi-crds"></a>12. Überprüfen der SMI-CRDs
Überprüfen Sie, ob der Cluster über die erforderlichen CRDs verfügt:
```bash
kubectl get crds
```

Stellen Sie sicher, dass die CRDs der OSM-Upstreamversion entsprechen. Beispiel: Wenn Sie v0.8.4 verwenden, stellen Sie sicher, dass die CRDs mit denen übereinstimmen, die im Releasebranch v0.8.4 des [OSM-OSS-Projekts](https://docs.openservicemesh.io/) verfügbar sind. Weitere Informationen finden Sie in den [Versionshinweisen zu OSM](https://github.com/openservicemesh/osm/releases).

Mit dem folgenden Befehl können Sie die Versionen der installierten CRDs abrufen:
```bash
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Wenn CRDs fehlen, verwenden Sie die folgenden Befehle, um sie im Cluster zu installieren. Stellen Sie sicher, dass Sie die Version im Befehl ersetzen:
```bash
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/access.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/specs.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/split.yaml
```
