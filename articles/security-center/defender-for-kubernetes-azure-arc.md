---
title: Schützen von Hybrid- und Multicloud-Kubernetes-Bereitstellungen mit Azure Defender für Kubernetes
description: Verwenden von Azure Defender für Kubernetes mit Ihren lokalen und Multicloud-Kubernetes-Clustern
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/14/2021
ms.author: memildin
ms.openlocfilehash: 2fc8c04cbe86737d924a1e61e8a2b3808082b7c9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351711"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Schützen von Kubernetes-Clustern mit Azure Arc-Unterstützung, die in lokalen und Multicloud-Umgebungen ausgeführt werden

Die **Azure Defender für Kubernetes-Clustererweiterung** kann Ihre lokalen Cluster mit denselben Funktionen zur Bedrohungserkennung zu schützen, die für Azure Kubernetes Service-Cluster angeboten werden. Aktivieren Sie [Kubernetes mit Azure Arc-Aktivierung](../azure-arc/kubernetes/overview.md) auf Ihren Clustern, und stellen Sie die Erweiterung bereit, wie auf dieser Seite beschrieben wird. 

Die Erweiterung kann auch Kubernetes-Cluster bei anderen Cloudanbietern schützen, allerdings nicht in deren Managed Kubernetes-Diensten.

> [!TIP]
> Zur Unterstützung beim Installationsvorgang haben wir einige Beispieldateien in den [Installationsbeispielen auf GitHub](https://aka.ms/kubernetes-extension-installation-examples) eingestellt.

## <a name="availability"></a>Verfügbarkeit

| Aspekt | Details |
|--------|---------|
| Releasestatus | **Vorschau**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Erforderliche Rollen und Berechtigungen | Der [Sicherheitsadministrator](../role-based-access-control/built-in-roles.md#security-admin) kann Warnungen verwerfen.<br>Der [Sicherheitsleseberechtigte](../role-based-access-control/built-in-roles.md#security-reader) kann Ergebnisse anzeigen. |
| Preise | Während der Vorschau kostenlos |
| Unterstützte Kubernetes-Distributionen | [Azure Kubernetes Service in Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS-Engine](https://github.com/Azure/aks-engine)<br> [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (Version 4.6 oder höher)<br> [VMware Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/) |
| Einschränkungen | Managed Kubernetes-Angebote wie Google Kubernetes Engine und Elastic Kubernetes Service werden von Kubernetes mit Azure Arc-Unterstützung und der Azure Defender-Erweiterung **nicht unterstützt**. [Azure Defender ist für Azure Kubernetes Service (AKS) nativ verfügbar](defender-for-kubernetes-introduction.md). Eine Verbindung des Clusters mit Azure Arc ist nicht erforderlich. |
| Umgebungen und Regionen | Die Verfügbarkeit dieser Erweiterung stimmt mit der von [Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/overview.md) überein.|

## <a name="architecture-overview"></a>Übersicht über die Architektur

Für alle Kubernetes-Cluster außer AKS müssen Sie Ihren Cluster mit Azure Arc verbinden. Anschließend kann Azure Defender für Kubernetes auf Ressourcen mit [Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/overview.md) als [Clustererweiterung](../azure-arc/kubernetes/extensions.md) bereitgestellt werden.

Die Erweiterungskomponenten sammeln Daten von Kubernetes-Überwachungsprotokollen aus allen Knoten der Steuerungsebene im Cluster und senden sie zur weiteren Analyse an das Azure Defender für Kubernetes-Back-End in der Cloud. Die Erweiterung wird bei einem Log Analytics-Arbeitsbereich registriert, der als Datenpipeline dient. Die Überwachungsprotokolldaten werden jedoch nicht im Log Analytics-Arbeitsbereich gespeichert.

Dieses Diagramm zeigt die Interaktion zwischen Azure Defender für Kubernetes und dem Kubernetes-Cluster mit Azure Arc-Unterstützung:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Ein allgemeines Architekturdiagramm, das die Interaktion zwischen Azure Defender für Kubernetes und einem Kubernetes-Cluster mit Azure Arc-Unterstützung darstellt." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Bereitstellung der Erweiterung Folgendes sicher:
- [Die Kubernetes-Cluster sind mit Azure Arc verbunden.](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Vergewissern Sie sich, [dass die in der Dokumentation zu generischen Clustererweiterungen aufgeführten Voraussetzungen erfüllt sind](../azure-arc/kubernetes/extensions.md#prerequisites).
- [Helm 3](https://helm.sh/docs/intro/install), Version 3.7.0 und höher
- Konfigurieren Sie **Port 443** auf den folgenden Endpunkten für ausgehenden Zugriff:
    - Für Cluster in Azure Government Cloud:
        - *.ods.opinsights.azure.us
        - *.oms.opinsights.azure.us
        - :::no-loc text="login.microsoftonline.us":::
    - Für Cluster in anderen Azure-Cloudbereitstellungen:
        - *.ods.opinsights.azure.com
        - *.oms.opinsights.azure.com
        - :::no-loc text="login.microsoftonline.com":::

## <a name="deploy-the-azure-defender-extension"></a>Bereitstellen der Azure Defender-Erweiterung

Sie können die Azure Defender-Erweiterung mithilfe einer Reihe von Methoden bereitstellen. Wählen Sie die relevante Registerkarte aus, um ausführliche Schritte anzuzeigen.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-security-center-recommendation"></a>Verwenden Sie die Schaltfläche „Korrigieren“ aus der Security Center Empfehlung.

Eine dedizierte Empfehlung in Azure Security Center bietet Folgendes:

- **Sichtbarkeit** der Cluster, auf denen die Defender für Kubernetes-Erweiterung bereitgestellt wird
- Schaltfläche **Korrigieren** zu deren Bereitstellung für Cluster ohne die Erweiterung

1. Öffnen Sie auf der Seite mit Azure Security Center-Empfehlungen das Sicherheitssteuerungselement **Azure Defender aktivieren**.

1. Verwenden Sie den Filter, um die Empfehlung namens **Für Azure Arc-fähige Kubernetes-Cluster muss die Azure Defender-Erweiterung installiert sein** zu finden.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center-Empfehlung zur Bereitstellung der Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Beachten Sie in der Aktionsspalte das Symbol **Korrigieren**.

1. Wählen Sie die Erweiterung aus, um die Details der fehlerfreien und fehlerhaften Ressourcen anzuzeigen: Cluster mit und ohne Erweiterung.

1. Wählen Sie in der Liste fehlerhafter Ressourcen einen Cluster aus, und klicken Sie auf **Korrigieren**, um den Bereich mit den Korrekturoptionen zu öffnen.

1. Wählen Sie den relevanten Log Analytics-Arbeitsbereich aus, und klicken Sie auf **x Ressource korrigieren**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Bereitstellen der Azure Defender-Erweiterung für Azure Arc mit der Security Center-Option zum Korrigieren":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Verwenden der Azure CLI zum Bereitstellen der Azure Defender-Erweiterung

1. Anmelden bei Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Stellen Sie sicher, dass Sie für ``<your-subscription-id>`` dieselbe Abonnement-ID verwenden, die Sie beim Herstellen einer Verbindung zwischen Ihrem Cluster und Azure Arc verwendet haben.

1. Führen Sie den folgenden Befehl aus, um die Erweiterung zusätzlich zu Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung bereitzustellen:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Im Folgenden werden alle unterstützten Konfigurationseinstellungen für den Azure Defender-Erweiterungstyp beschrieben:

    | Eigenschaft | BESCHREIBUNG |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Optional:** Vollständige Ressourcen-ID Ihres eigenen Log Analytics-Arbeitsbereichs.<br>Wenn keine Angabe vorliegt, wird der Standardarbeitsbereich der Region verwendet.<br><br>Um die vollständige Ressourcen-ID zu erhalten, zeigen Sie die Liste der Arbeitsbereiche in Ihren Abonnements im JSON-Standardformat an, indem Sie den folgenden Befehl ausführen:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Die Ressourcen-ID des Log Analytics Arbeitsbereichs weist folgende Syntax auf:<br>/subscriptions/{Ihre-Abonnement-ID}/resourceGroups/{Ihre-Ressourcengruppe}/providers/Microsoft.OperationalInsights/workspaces/{Ihr-Arbeitsbereichsname}. <br>Weitere Informationen finden Sie unter [Log Analytics-Arbeitsbereiche](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces). |
    | auditLogPath |**Optional:** Der vollständige Pfad zu den Überwachungsprotokolldateien.<br>Wenn nicht angegeben, wird der Standardpfad ``/var/log/kube-apiserver/audit.log`` verwendet.<br>Für die AKS-Engine lautet der Standardpfad ``/var/log/kubeaudit/audit.log``. |

    Der folgende Befehl zeigt ein Beispiel für die Verwendung aller optionalen Felder:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Ressourcen-Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Verwenden von Azure Resource Manager zum Bereitstellen der Azure Defender-Erweiterung

Um Azure Resource Manager zum Bereitstellen der Azure Defender-Erweiterung zu verwenden, benötigen Sie einen Log Analytics-Arbeitsbereich für Ihr Abonnement. Weitere Informationen finden Sie unter [Log Analytics-Arbeitsbereiche](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces).

Sie können die Resource Manager-Vorlage **azure-defender-extension-arm-template.json** aus den [Installationsbeispielen](https://aka.ms/kubernetes-extension-installation-examples) von Security Center verwenden.

> [!TIP]
> Wenn Sie noch nicht mit Resource Manager-Vorlagen vertraut sind, beginnen Sie hier: [Was sind Azure Resource Manager-Vorlagen?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Verwenden der REST-API zum Bereitstellen der Azure Defender-Erweiterung 

Um die REST-API zum Bereitstellen der Azure Defender-Erweiterung zu verwenden, benötigen Sie einen Log Analytics-Arbeitsbereich für Ihr Abonnement. Weitere Informationen finden Sie unter [Log Analytics-Arbeitsbereiche](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces).

> [!TIP]
> Die einfachste Möglichkeit, die API zum Bereitstellen der Azure Defender-Erweiterung zu verwenden, ist das bereitgestellte Beispiel mit **JSON zur Postman-Sammlung** aus den [Installationsbeispielen](https://aka.ms/kubernetes-extension-installation-examples) von Security Center.
- Führen Sie den folgenden PUT-Befehl aus, um den JSON-Code zur Postman-Sammlung zu ändern oder die Erweiterung manuell mit der REST-API bereitzustellen:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Hierbei gilt:

    | Name            | Geben Sie in   | Erforderlich | Typ   | BESCHREIBUNG                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Abonnement-ID | path | True     | Zeichenfolge | Abonnement-ID Ihrer Kubernetes-Ressource mit Azure Arc-Unterstützung |
    | Ressourcengruppe  | path | True     | Zeichenfolge | Name der Ressourcengruppe, in der Ihre Kubernetes-Ressource mit Azure Arc-Unterstützung enthalten ist |
    | Clustername    | path | True     | Zeichenfolge | Name Ihrer Kubernetes-Ressource mit Azure Arc-Unterstützung  |


    Zur **Authentifizierung** muss Ihr Header (wie bei anderen Azure-APIs) über ein Bearertoken verfügen. Um ein Bearertoken zu erhalten, führen Sie den folgenden Befehl aus:

    `az account get-access-token --subscription <your-subscription-id>` Verwenden Sie die folgende Struktur für den Nachrichtentext:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Im Folgenden werden die Eigenschaften beschrieben:

    | Eigenschaft | BESCHREIBUNG | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | Arbeitsbereichs-ID der Log Analytics-Ressource |
    | logAnalytics.key         | Schlüssel der Log Analytics-Ressource | 
    | auditLogPath             | **Optional:** Der vollständige Pfad zu den Überwachungsprotokolldateien. Der Standardwert ist ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Um sicherzustellen, dass die Azure Defender-Erweiterung auf Ihrem Cluster installiert ist, befolgen Sie die Schritte auf einer der folgenden Registerkarten:

### <a name="azure-portal---security-center"></a>[**Azure-Portal: Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Verwenden der Security Center-Empfehlung zum Überprüfen des Status Ihrer Erweiterung

1. Öffnen Sie auf der Seite mit Azure Security Center-Empfehlungen das Sicherheitssteuerungselement **Azure Defender aktivieren**.

1. Wählen Sie die Empfehlung namens **Für Azure Arc-fähige Kubernetes-Cluster muss die Azure Defender-Erweiterung installiert sein** aus.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center-Empfehlung zur Bereitstellung der Azure Defender-Erweiterung für Azure Arc-fähige Kubernetes-Cluster" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Überprüfen Sie, ob der Cluster, auf dem Sie die Erweiterung bereitgestellt haben, als **Fehlerfrei** aufgeführt ist.


### <a name="azure-portal---azure-arc"></a>[**Azure-Portal: Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Verwenden der Azure Arc-Seiten zum Überprüfen des Status Ihrer Erweiterung

1. Öffnen Sie **Azure Arc** im Azure-Portal.
1. Wählen Sie in der Infrastrukturliste die Option **Kubernetes-Cluster** aus, und klicken Sie dann auf den spezifischen Cluster.
1. Öffnen Sie die Erweiterungsseite. Die Erweiterungen für den Cluster werden aufgelistet. Überprüfen Sie in der Spalte **Installationsstatus**, ob die Azure Defender-Erweiterung ordnungsgemäß installiert wurde.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Azure Arc-Seite zum Überprüfen des Status aller installierten Erweiterungen in einem Kubernetes-Cluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Wählen Sie die Erweiterung aus, um weitere Informationen zu erhalten.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Vollständige Details einer Azure Arc-Erweiterung in einem Kubernetes-Cluster.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Verwenden der Azure CLI zum Überprüfen der Erweiterungsbereitstellung

1. Führen Sie in der Azure CLI den folgenden Befehl aus:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Suchen Sie in der Antwort nach "extensionType": "microsoft.azuredefender.kubernetes" und "installState": "Installed".

    > [!NOTE]
    > In den ersten Minuten wird möglicherweise "installState": "Pending" angezeigt.
    
1. Wenn der Status **Installed** angezeigt wird, überprüfen Sie, ob sich ein Pod namens „azuredefender-XXXXX“ im Zustand „Running“ befindet. Führen Sie dazu auf Ihrem Computer den folgenden Befehl aus, wobei die Datei `kubeconfig` auf Ihren Cluster zeigt:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Verwenden der REST-API zum Überprüfen der Erweiterungsbereitstellung

Gehen Sie folgendermaßen vor, um eine erfolgreiche Bereitstellung zu bestätigen oder den Status Ihrer Erweiterung zu einem beliebigen Zeitpunkt zu überprüfen:

1. Führen Sie den folgenden GET-Befehl aus:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Suchen Sie in der Antwort in "extensionType": "microsoft.azuredefender.kubernetes" nach "installState": "Installed".

    > [!TIP]
    > In den ersten Minuten wird möglicherweise "installState": "Pending" angezeigt.
    
1. Wenn der Status **Installed** angezeigt wird, überprüfen Sie, ob sich ein Pod namens „azuredefender-XXXXX“ im Zustand „Running“ befindet. Führen Sie dazu auf Ihrem Computer den folgenden Befehl aus, wobei die Datei `kubeconfig` auf Ihren Cluster zeigt:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simulieren von Sicherheitswarnungen von Azure Defender für Kubernetes

Eine vollständige Liste unterstützter Warnungen finden Sie in der [Verweistabelle aller Sicherheitswarnungen in Azure Security Center](alerts-reference.md#alerts-k8scluster).

1. Führen Sie den folgenden Befehl aus, um eine Azure Defender-Warnung zu simulieren:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    Die erwartete Antwort lautet „Keine Ressource gefunden“.

    Diese Aktivität wird von Azure Defender innerhalb von 30 Minuten erkannt, und es wird eine Sicherheitswarnung ausgelöst.

1. Öffnen Sie im Azure-Portal die Azure Security Center-Seite mit Sicherheitswarnungen, und suchen Sie nach der Warnung für die relevante Ressource:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Beispielwarnung von Azure Defender für Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Entfernen der Azure Defender-Erweiterung

Sie können die Erweiterung über das Azure-Portal, mithilfe der Azure CLI oder der REST-API entfernen. Die Vorgehensweise wird auf den Registerkarten unten erläutert.

### <a name="azure-portal---arc"></a>[**Azure-Portal – Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Verwenden des Azure-Portals zum Entfernen der Erweiterung

1. Öffnen Sie Azure Arc im Azure-Portal.
1. Wählen Sie in der Infrastrukturliste die Option **Kubernetes-Cluster** aus, und klicken Sie dann auf den spezifischen Cluster.
1. Öffnen Sie die Erweiterungsseite. Die Erweiterungen für den Cluster werden aufgelistet.
1. Wählen Sie den Cluster aus, und klicken Sie auf **Deinstallieren**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Entfernen einer Erweiterung aus Ihrem Kubernetes-Cluster mit Arc-Unterstützung." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Verwenden der Azure CLI zum Entfernen der Azure Defender-Erweiterung

1. Entfernen Sie die Azure Defender-Erweiterung für Kubernetes mit Arc-Unterstützung mithilfe der folgenden Befehle:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Das Entfernen der Erweiterung kann einige Minuten dauern. Wir empfehlen Ihnen zu warten, bevor Sie den Erfolg des Vorgangs überprüfen.

1. Führen Sie die folgenden Befehle aus, um zu überprüfen, ob die Erweiterung erfolgreich entfernt wurde:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Beim Löschen der Erweiterungsressource aus Azure Resource Manager sollte keine Verzögerung auftreten. Vergewissern Sie sich anschließend, dass auf dem Cluster keine Pods namens „azuredefender-XXXXX“ vorhanden sind. Führen Sie dazu den folgenden Befehl aus, wobei die Datei `kubeconfig` auf Ihren Cluster zeigt: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Das Löschen der Pods kann einige Minuten in Anspruch nehmen.

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Verwenden der REST-API zum Entfernen der Azure Defender-Erweiterung 

Um die Erweiterung mit der REST-API zu entfernen, führen Sie den folgenden DELETE-Befehl aus:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | Geben Sie in   | Erforderlich | Typ   | BESCHREIBUNG                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Abonnement-ID | path | True     | Zeichenfolge | Abonnement-ID Ihres Kubernetes-Clusters mit Arc-Unterstützung |
| Ressourcengruppe  | path | True     | Zeichenfolge | Ressourcengruppe Ihres Kubernetes-Clusters mit Arc-Unterstützung  |
| Clustername    | path | True     | Zeichenfolge | Name Ihres Kubernetes-Clusters mit Azure Arc-Unterstützung            |

Zur **Authentifizierung** muss Ihr Header (wie bei anderen Azure-APIs) über ein Bearertoken verfügen. Um ein Bearertoken zu erhalten, führen Sie den folgenden Befehl aus:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Die Anforderung kann mehrere Minuten in Anspruch nehmen.

---

## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, wie Sie die Azure Defender-Erweiterung für Kubernetes-Cluster mit Azure Arc-Unterstützung bereitstellen. Weitere Informationen zu den Containersicherheitsfeatures von Azure Defender und Azure Security Center finden Sie auf den folgenden Seiten:

- [Containersicherheit in Security Center](container-security.md)
- [Einführung in Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Schützen Ihrer Kubernetes-Workloads](kubernetes-workload-protections.md)
