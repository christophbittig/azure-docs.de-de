---
title: Beenden der Überwachung Ihres Kubernetes-Hybridclusters | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung Ihres Kubernetes-Clusters mit Container Insights beenden können.
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b8f548b8e9440804ae6a7ff293c35e7107cfc4c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708929"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Beenden der Überwachung Ihres Hybridclusters

Die Überwachung Ihres Kubernetes-Clusters kann nach der Aktivierung wieder beendet werden, wenn Sie den Cluster nicht mehr mit Container Insights überwachen möchten. Dieser Artikel erläutert die Vorgehensweise für eine der folgenden Umgebungen:

- AKS-Engine in Azure und Azure Stack
- OpenShift-Version 4 und höher
- Kubernetes mit Azure Arc-Unterstützung (Vorschau)

## <a name="how-to-stop-monitoring-using-helm"></a>Beenden der Überwachung mithilfe von Helm

Die folgenden Schritte gelten für folgende Umgebungen:

- AKS-Engine in Azure und Azure Stack
- OpenShift-Version 4 und höher

1. Führen Sie den folgenden Helm-Befehl aus, um zunächst das auf Ihrem Cluster installierte Container Insights-Helm-Chartrelease zu identifizieren.

    ```
    helm list
    ```

    Die Ausgabe ähnelt der folgenden:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* stellt das Helm-Chartrelease für Container Insights dar.

2. Führen Sie den folgenden Helm-Befehl aus, um das Chartrelease zu löschen.

    `helm delete <releaseName>`

    Beispiel:

    `helm delete azmon-containers-release-1`

    Dadurch wird das Release aus dem Cluster entfernt. Sie können dies überprüfen, indem Sie den `helm list`-Befehl ausführen:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Die Änderung der Konfiguration kann einige Minuten dauern. Da Helm Ihre Releases auch nach dem Löschen nachverfolgt, können Sie den Verlauf eines Clusters überwachen und ein Release mit `helm rollback` sogar wiederherstellen.

## <a name="how-to-stop-monitoring-on-azure-arc-enabled-kubernetes"></a>Beenden der Überwachung in Kubernetes mit Azure Arc-Unterstützung

### <a name="using-powershell"></a>PowerShell

1. Laden Sie das Skript herunter, und speichern Sie es in einem lokalen Ordner, der Ihren Cluster über die folgenden Befehle mit dem Überwachungs-Add-on konfiguriert:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Konfigurieren Sie die `$azureArcClusterResourceId`-Variable, indem Sie die entsprechenden Werte für `subscriptionId`, `resourceGroupName` und `clusterName` festlegen, die die Ressourcen-ID Ihrer Kubernetes-Clusterressource mit Azure Arc-Aktivierung darstellen.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurieren Sie die `$kubeContext`-Variable mit dem **kube-context** Ihres Clusters mit dem Befehl `kubectl config get-contexts`. Wenn Sie den aktuellen Kontext verwenden möchten, legen Sie den Wert auf `""` fest.

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Führen Sie den folgenden Befehl aus, um die Überwachung des Clusters zu beenden.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Verwenden eines Dienstprinzipals
Das Skript *disable-monitoring.ps1* verwendet die interaktive Geräteanmeldung. Wenn Sie die nicht interaktive Anmeldung bevorzugen, können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen Dienstprinzipal erstellen, der über die erforderlichen Berechtigungen verfügt (siehe [Voraussetzungen](container-insights-enable-arc-enabled-clusters.md#prerequisites)). Zur Verwendung des Dienstprinzipals müssen Sie die Parameter „$servicePrincipalClientId“, „$servicePrincipalClientSecret“ und „$tenantId“ mit Werten des Dienstprinzipals übergeben, den Sie für das Skript „enable-monitoring.ps1“ nutzen möchten.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc-connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Beispiel:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Verwenden von Bash

1. Laden Sie das Skript herunter, und speichern Sie es in einem lokalen Ordner, der Ihren Cluster über die folgenden Befehle mit dem Überwachungs-Add-on konfiguriert:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Konfigurieren Sie die `azureArcClusterResourceId`-Variable, indem Sie die entsprechenden Werte für `subscriptionId`, `resourceGroupName` und `clusterName` festlegen, die die Ressourcen-ID Ihrer Kubernetes-Clusterressource mit Azure Arc-Aktivierung darstellen.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurieren Sie die `kubeContext`-Variable mit dem **kube-context** Ihres Clusters mit dem Befehl `kubectl config get-contexts`.

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Um die Überwachung Ihres Clusters zu beenden, werden je nach Bereitstellungsszenario verschiedene Befehle bereitgestellt.

    Führen Sie den folgenden Befehl aus, um die Überwachung des Clusters mit dem aktuellen Kontext zu beenden.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Führen Sie den folgenden Befehl aus, um die Überwachung des Clusters durch Angabe eines Kontexts zu beenden.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Verwenden eines Dienstprinzipals
Das Bash-Skript *disable-monitoring.sh* verwendet die interaktive Geräteanmeldung. Wenn Sie die nicht interaktive Anmeldung bevorzugen, können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen Dienstprinzipal erstellen, der über die erforderlichen Berechtigungen verfügt (siehe [Voraussetzungen](container-insights-enable-arc-enabled-clusters.md#prerequisites)). Zur Verwendung des Dienstprinzipals müssen Sie die Werte „--client-id“, „--client-secret“ und  „--tenant-id“ des Dienstprinzipals übergeben, den Sie für das Bash-Skript *enable-monitoring.sh* nutzen möchten.

```bash
subscriptionId="<subscription Id of the Azure Arc-connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Beispiel:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Nächste Schritte

Wenn der Log Analytics-Arbeitsbereich nur als Unterstützung für die Clusterüberwachung erstellt wurde und nicht mehr benötigt wird, müssen Sie ihn manuell löschen. Wie ein Arbeitsbereich gelöscht wird, erfahren Sie unter [Löschen eines Log Analytics-Arbeitsbereichs](../logs/delete-workspace.md).
