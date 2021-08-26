---
title: Bereitstellen eines Azure API Management-Gateways auf Azure Arc
description: Aktivieren von Azure Arc, um Ihr selbstgehostetes Azure API Management-Gateway bereitzustellen.
author: v-hhunter
ms.author: v-hhunter
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.openlocfilehash: aad3015ed129b438e9f359d7cb3fc89b6658e5c9
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768319"
---
# <a name="deploy-an-azure-api-management-gateway-on-azure-arc-preview"></a>Bereitstellen eines Azure API Management-Gateways auf Azure Arc (Vorschau)

Mit der Integration zwischen Azure API Management und [Azure Arc in Kubernetes](../azure-arc/kubernetes/overview.md)können Sie die API Management Gateway-Komponente als [Erweiterung in einem Azure Arc aktivierten Kubernetes-Cluster](../azure-arc/kubernetes/extensions.md) bereitstellen. 

Der Einsatz des API Management-Gateways auf einem Arc-fähigen Kubernetes-Cluster erweitert die API Management-Unterstützung für Hybrid- und Multi-Cloud-Umgebungen. Aktivieren der Bereitstellung mithilfe einer Clustererweiterung, um das Verwalten und Anwenden von Richtlinien auf Ihren Arc-aktivierten Cluster zu einem konsistenten Ablauf zu machen.

[!INCLUDE [preview](./includes/preview/preview-callout-self-hosted-gateway-azure-arc.md)]

> [!NOTE]
> Sie können das selbstgehostete Gateway auch [direkt in Kubernetes](./how-to-deploy-self-hosted-gateway-azure-kubernetes-service.md)bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

* [Verbinden Sie Ihren Kubernetes-Cluster](../azure-arc/kubernetes/quickstart-connect-cluster.md) innerhalb [einer unterstützten Azure Arc Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).
* Installieren der `k8s-extension`-Azure CLI-Erweiterung:

    ```azurecli
    az extension add --name k8s-extension
    ```
    Wenn Sie das `k8s-extension`-Modul bereits installiert haben, aktualisieren Sie es bitte auf die neueste Version:

    ```azurecli
    az extension update --name k8s-extension
    ```
* [Erstellen einer neuen Azure API Management-Dienstinstanz](./get-started-create-service-instance.md)
* [Stellen Sie eine Gateway-Ressource in Ihrer Azure API Management-Dienstinstanz bereit](./api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-api-management-gateway-extension-using-azure-cli"></a>Bereitstellen der API Management-Gateway-Erweiterung mithilfe von Azure CLI

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
1. Wählen Sie **Gateways** aus dem seitlichen Navigationsmenü.
1. Wählen Sie Ihre bereitgestellte Gateway-Ressource aus der Liste, und öffnen Sie diese.
1. Klicken Sie in der bereitgestellten Gateway-Ressource im seitlichen Navigationsmenü auf **Bereitstellung**.
1. Notieren Sie sich die Werte für **Token** und **Konfiguration-URL** für den nächsten Schritt.
1. Bereitstellender Gateway-Erweiterung mit dem `az k8s-extension create` Befehl in Azure CLI. Geben Sie die Werte `token` und `configuration URL` ein.
    * Das folgende Beispiel verwendet die `service.type='LoadBalancer'`-Erweiterungskonfiguration. Hier finden Sie weitere [verfügbare Erweiterungskonfigurationen](#available-extension-configurations).

    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> \
      --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway \
      --scope namespace --target-namespace <namespace> \
      --configuration-settings gateway.endpoint='<Configuration URL>' \
      --configuration-protected-settings gateway.authKey='<token>' \
      --configuration-settings service.type='LoadBalancer' --release-train preview
    ```

    > [!TIP]
    > Die `-protected-`-Markierung für `authKey` ist optional, wird aber empfohlen. 

1. Überprüfen Sie den Bereitstellungsstatus mit dem folgenden CLI-Befehl:
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. Navigieren Sie zurück zur Liste **Gateways**, um zu überprüfen, ob der Gateway-Status ein grünes Häkchen mit einer Knotenanzahl anzeigt. Dieser Status bedeutet, dass die bereitgestellten selbstgehosteten Gateway-Pods:
    * Erfolgreich mit dem API Management Dienst kommunizieren.
    * Über einen regulären „Herzschlag“ verfügen.

## <a name="deploy-the-api-management-gateway-extension-using-azure-portal"></a>Bereitstellen der API Management-Gateway-Erweiterung mithilfe von Microsoft Azure-Portal

1. Navigieren Sie im Azure-Portal zu Ihrem Azure Arc verbundenen Cluster.
1. Wählen Sie **Erweiterungen (Vorschau)**  >  **+ Hinzufügen von**  > **API Management Gateway (Vorschau)** im linken Menü aus.
1. Wählen Sie **Erstellen** aus.
1. Konfigurieren der Gateway-Erweiterung im Fenster **API Management Gateway installieren**:
    * Wählen Sie das Abonnement und die Ressourcengruppe für Ihre API Management Instanz aus.
    * Wählen Sie die **API Management Instanz** und den **Gateway-Namen** unter **Gateway-Details** aus. Geben Sie einen **Namespace** Bereich für Ihre Erweiterung und optional eine Reihe von **Replikaten** ein, wenn dies auf Ihrer API Management Dienstebene unterstützt wird.
    * Wählen Sie in der **Kubernetes-Konfiguration** die Standardkonfiguration oder eine andere Konfiguration für Ihr Cluster aus. Optionen finden Sie unter [Verfügbare Erweiterungskonfigurationen](#available-extension-configurations).

    :::image type="content" source="./media/how-to-deploy-self-hosted-gateway-azure-arc/deploy-gateway-extension-azure-arc.png" alt-text="Screenshot der Erweiterungsbereitstellung in Azure-Portal":::

1. Aktivieren Sie optional „Überwachen“ auf der Registerkarte **Überwachen**, um Anforderungen zur Nachverfolgung von Metriken an das Gateway und Back-End hochzuladen. Wenn es aktiviert ist, wählen Sie einen vorhandenen **Log Analytics**-Arbeitsbereich aus.
1. Wählen Sie **Überprüfen + Installieren** und dann **Installieren** aus.

## <a name="available-extension-configurations"></a>Verfügbare Erweiterungskonfigurationen

Die folgenden Erweiterungskonfigurationen sind **erforderlich**.

| Einstellung | Beschreibung |
| ------- | ----------- | 
| `gateway.endpoint` | Die Konfiguration-URL des Gateway-Endpunkts. |
| `gateway.authKey` | Token für den Zugriff auf das Gateway. | 
| `service.type` | Kubernetes-Dienstkonfiguration für das Gateway: `LoadBalancer`, `NodePort` oder `ClusterIP`. |

### <a name="log-analytics-settings"></a>Log Analytics-Einstellungen

Um das Überwachen des selbstgehosteten Gateways zu aktivieren, konfigurieren Sie die folgenden Log Analytics-Einstellungen:

| Einstellung | Beschreibung |
| ------- | ----------- | 
| `monitoring.customResourceId` | Azure Resource Manager Ressourcen-ID für die API Management Instanz. |
| `monitoring.workspaceId` | ID des Log Analytics-Arbeitsbereichs. | 
| `monitoring.ingestionKey` | Geheimnis mit dem Erfassungsschlüssel aus Log Analytics. |

> [!NOTE]
> Wenn Sie Log Analytics nicht aktiviert haben: 
> 1. Durchlaufen Sie die Schnellstartanleitung [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/logs/quick-create-workspace.md). 
> 1. Erfahren Sie, wo Sie die [Log Analytics-Agent-Einstellungen](../azure-monitor/agents/log-analytics-agent.md) finden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* Entdecken Sie alle [Azure Arc aktivierten Kubernetes-Erweiterungen](../azure-arc/kubernetes/extensions.md). 
* Weitere Informationen zu [Kubernetes mit Azure Arc-Aktivierung](../azure-arc/kubernetes/overview.md).
