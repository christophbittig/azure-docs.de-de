---
title: Aktualisieren von Container Insights für Metriken | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Container Insights aktualisieren, um die Funktion für benutzerdefinierte Metriken zu aktivieren, für die das Untersuchen und Senden von Warnungen zu aggregierten Metriken unterstützt wird.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1921a0cc0aa15a5e877d64cbe2c7ad094f9e144f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347106"
---
# <a name="how-to-update-container-insights-to-enable-metrics"></a>Aktualisieren von Container Insights zum Aktivieren von Metriken

Mit Container Insights wird Unterstützung für die Erfassung von Metriken aus AKS- (Azure Kubernetes Service) und Kubernetes-Clusterknoten und -Pods mit Azure Arc-Aktivierung sowie für das Schreiben in den Azure Monitor-Metrikspeicher eingeführt. Diese Änderung soll zu einer verbesserten Zeitgenauigkeit führen, wenn Aggregatberechnungen (Avg, Count, Max, Min, Sum) in Leistungsdiagrammen dargestellt werden, und das Anheften von Leistungsdiagrammen in Dashboards des Azure-Portals und Metrikwarnungen unterstützen.

>[!NOTE]
>Diese Funktion unterstützt derzeit keine Azure Red Hat OpenShift-Cluster.
>

Im Rahmen dieses Features werden die folgenden Metriken aktiviert:

| Metriknamespace | Metrik | BESCHREIBUNG |
|------------------|--------|-------------|
| Insights.container/nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Als *Knotenmetriken* enthalten Sie *Host* als Dimension. Sie enthalten auch den<br> Namen des Knotens als Wert für die Dimension *host*. |
| Insights.container/pods | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Als *Pod-Metriken* enthalten sie Folgendes als Dimension: ControllerName, Kubernetes-Namespace, Name, Phase. |
| Insights.container/containers | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Insights.container/persistentvolumes | pvUsageExceededPercentage | |

Ein Agent in Containern ist im Release der Version **microsoft/oms:ciprod05262020** für AKS und der Version **microsoft/oms:ciprod09252020** für Kubernetes-Cluster mit Azure Arc-Aktivierung enthalten, um diese neuen Funktionen zu unterstützen. Neue Bereitstellungen von AKS enthalten diese Konfigurationsänderung und die Funktionen automatisch. Die Aktualisierung Ihres Clusters zur Unterstützung dieser Funktion kann über das Azure-Portal, Azure PowerShell oder mit der Azure CLI durchgeführt werden. Mit Azure PowerShell und der CLI können Sie dies pro Cluster oder für alle Cluster in Ihrem Abonnement aktivieren.

Bei beiden Prozessen wird die Rolle **Überwachungsmetriken veröffentlichen** dem Dienstprinzipal des Clusters oder der benutzerseitig zugewiesenen MSI für das Überwachungs-Add-On zugewiesen, sodass die vom Agent erfassten Daten für Ihre Clusterressource veröffentlicht werden können. Die Rolle „Überwachungsmetriken veröffentlichen“ verfügt nur über die Berechtigung zum Übertragen von Metriken per Pushvorgang an die Ressource. Das Ändern eines Zustands, Aktualisieren der Ressource oder Lesen von Daten ist nicht möglich. Weitere Informationen zur Rolle finden Sie unter [Herausgeber von Überwachungsmetriken](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). Die Anforderung der Rolle „Herausgeber von Überwachungsmetriken“ gilt nicht für Kubernetes-Cluster mit Azure Arc-Aktivierung.

> [!IMPORTANT]
> Das Upgrade ist für Kubernetes-Cluster mit Azure Arc-Aktivierung nicht erforderlich, da sie bereits über die mindestens erforderliche Agent-Version verfügen. Das Zuweisen der Rolle **Überwachungsmetriken veröffentlichen** zum Dienstprinzipal des Clusters oder der vom Benutzer zugewiesenen MSI für das Überwachungs-Add-On wird automatisch durchgeführt, wenn Sie das Azure-Portal, Azure PowerShell oder die Azure CLI verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Aktualisieren Ihres Clusters Folgendes sicher:

* Benutzerdefinierte Metriken sind nur in einigen Azure-Regionen verfügbar. Eine Liste der unterstützten Regionen finden Sie [hier](../essentials/metrics-custom-overview.md#supported-regions).

* Sie sind Mitglied der Rolle **[Besitzer](../../role-based-access-control/built-in-roles.md#owner)** in der AKS-Clusterressource, um die Erfassung von benutzerdefinierten Leistungsmetriken für Knoten und Pods zu ermöglichen. Diese Anforderung gilt nicht für Kubernetes-Cluster mit Azure Arc-Aktivierung.

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.59 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Aktualisieren eines Clusters über das Azure-Portal

Für vorhandene AKS-Cluster, die mit Container Insights überwacht werden, sollte nach dem Auswählen des Clusters zum Prüfen der Integrität über die Azure Monitor-Clusterübersicht oder direkt im Cluster (Option **Einblicke** im linken Bereich) oben im Portal ein Banner angezeigt werden.

![Aktualisieren des AKS-Clusterbanners im Azure-Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Wenn Sie auf **Aktivieren** klicken, wird der Prozess zum Durchführen einer Aktualisierung für den Cluster initiiert. Dieser Vorgang kann einige Sekunden dauern, und Sie können den Fortschritt im Menü unter „Benachrichtigungen“ nachverfolgen.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Aktualisieren aller Cluster per Bash in der Azure-Befehlsshell

Führen Sie die folgenden Schritte aus, um alle Cluster Ihres Abonnements per Bash in der Azure-Befehlsshell zu aktualisieren.

1. Führen Sie den folgenden Befehl mithilfe der Azure CLI aus.  Bearbeiten Sie den Wert für **subscriptionId**, indem Sie den Wert von der Seite mit der **AKS-Übersicht** für den AKS-Cluster verwenden.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Die Änderung der Konfiguration kann einige Sekunden dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Aktualisieren pro Cluster mit der Azure CLI

Führen Sie die folgenden Schritte aus, um einen bestimmten Cluster in Ihrem Abonnement mit der Azure CLI zu aktualisieren.

1. Führen Sie den folgenden Befehl mithilfe der Azure CLI aus. Bearbeiten Sie die Werte für **subscriptionId**, **resourceGroupName** und **clusterName**, indem Sie die Werte auf der Seite mit der **AKS-Übersicht** für den AKS-Cluster verwenden.  Der Wert von **clientIdOfSPN** wird zurückgegeben, wenn Sie den Befehl `az aks show` wie im Beispiel unten ausführen.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Zum Abrufen des Werts für **clientIdOfSPNOrMsi** können Sie den Befehl `az aks show` wie im Beispiel unten ausführen. Wenn das **servicePrincipalProfile**-Objekt über einen gültigen *clientid*-Wert verfügt, können Sie diesen verwenden. Ist dieser auf *msi* festgelegt, müssen Sie die Client-ID aus `addonProfiles.omsagent.identity.clientId` übergeben.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Aktualisieren aller Cluster per Azure PowerShell

Führen Sie die folgenden Schritte aus, um alle Cluster Ihres Abonnements per Azure PowerShell zu aktualisieren.

1. [Laden Sie](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) das Skript **mdm_onboarding_atscale.ps1** herunter, und speichern Sie es in einem lokalen Ordner aus unserem GitHub-Repository.
2. Führen Sie den folgenden Befehl mit Azure PowerShell aus.  Bearbeiten Sie den Wert für **subscriptionId**, indem Sie den Wert von der Seite mit der **AKS-Übersicht** für den AKS-Cluster verwenden.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Die Änderung der Konfiguration kann einige Sekunden dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Aktualisieren pro Cluster per Azure PowerShell

Führen Sie die folgenden Schritte aus, um mit Azure PowerShell einen bestimmten Cluster zu aktualisieren.

1. [Laden Sie](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) das Skript **mdm_onboarding.ps1** herunter, und speichern Sie es in einem lokalen Ordner aus unserem GitHub-Repository.

2. Führen Sie den folgenden Befehl mit Azure PowerShell aus. Bearbeiten Sie die Werte für **subscriptionId**, **resourceGroupName** und **clusterName**, indem Sie die Werte auf der Seite mit der **AKS-Übersicht** für den AKS-Cluster verwenden.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Die Änderung der Konfiguration kann einige Sekunden dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Überprüfen von Updatevorgängen

Nach dem Initiieren des Updatevorgangs mit einer der oben beschriebenen Methoden können Sie den Metrik-Explorer von Azure Monitor verwenden und über den **Metriknamespace** überprüfen, ob **insights** aufgeführt ist. Wenn ja, können Sie mit dem Einrichten von [Metrikwarnungen](../alerts/alerts-metric.md) bzw. dem Anheften Ihrer Diagramme in [Dashboards](../../azure-portal/azure-portal-dashboards.md) beginnen.  
