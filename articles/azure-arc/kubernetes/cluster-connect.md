---
title: Verwenden von Cluster Connect zum Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Verwenden von Cluster Connect zum sicheren Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung
ms.openlocfilehash: 65070f8850bb18be5c142c658190ebe9da41f7a0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767905"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Verwenden von Cluster Connect zum Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung

Mit Cluster Connect können Sie sicher eine Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung herstellen, ohne dass ein Eingangsport in der Firewall aktiviert werden muss. Der Zugriff auf den `apiserver` des Kubernetes-Clusters mit Azure Arc-Unterstützung ermöglicht die folgenden Szenarien:
* Aktivieren von interaktivem Debuggen und Problembehandlung.
* Bereitstellen des Clusterzugriffs auf Azure-Dienste für [benutzerdefinierte Speicherorte](custom-locations.md) und andere auf dieser Basis erstellte Ressourcen.

Eine konzeptionelle Übersicht zu diesem Feature finden Sie im Artikel [Cluster Connect: Kubernetes mit Azure Arc-Unterstützung](conceptual-cluster-connect.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen   

- [Installieren oder aktualisieren Sie Azure CLI](/cli/azure/install-azure-cli) auf eine Version >= 2.16.0

- Installieren Sie die Azure CLI-Erweiterung `connectedk8s` in einer Version >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Wenn Sie die Erweiterung `connectedk8s` bereits installiert haben, aktualisieren Sie die Erweiterung auf die neueste Version:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Ein vorhandener Cluster, der mit Kubernetes mit Azure Arc-Aktivierung verbunden ist.
    - Wenn Sie noch keine Verbindung mit einem Cluster hergestellt haben, verwenden Sie unseren [Schnellstart](quickstart-connect-cluster.md).
    - [Aktualisieren Sie Ihre Agents](agent-upgrade.md#manually-upgrade-agents) auf Version >= 1.1.0.

- Aktivieren Sie Cluster Connect auf jedem Kubernetes-Cluster mit Azure Arc-Unterstützung, indem Sie den folgenden Befehl auf einem Computer ausführen, auf dem die `kubeconfig`-Datei auf den betreffenden Cluster verweist:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Aktivieren Sie die folgenden Endpunkte neben den unter [Erfüllen von Netzwerkanforderungen](quickstart-connect-cluster.md#meet-network-requirements) angegebenen Endpunkten für ausgehenden Zugriff:

    | Endpunkt | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Verbrauch

Zwei Authentifizierungsoptionen werden mit dem Cluster Connect-Feature unterstützt: 
* Azure Active Directory (Azure AD) 
* Dienstkontotoken

### <a name="option-1-azure-active-directory"></a>Option 1: Azure Active Directory

1. Erstellen Sie mit der `kubeconfig`-Datei, die auf den `apiserver` Ihres Kubernetes-Clusters zeigt, ein ClusterRoleBinding oder RoleBinding für die Azure AD-Entität (Dienstprinzipal oder Benutzer), die Zugriff benötigt:

    **Für Benutzer:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Für Azure AD-Anwendung:**

    1. Rufen Sie die Ihrer Azure AD-Anwendung zugeordnete `objectId` ab:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Erstellen Sie ein ClusterRoleBinding oder RoleBinding für die Azure AD-Entität (Dienstprinzipal oder Benutzer), die auf diesen Cluster zugreifen muss:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Nach der Anmeldung bei Azure CLI mithilfe der gewünschten Azure AD-Entität rufen Sie die Cluster Connect-`kubeconfig`-Datei für die Kommunikation mit dem Cluster von einem beliebigen Standort (auch von außerhalb der Firewall, die den Cluster umgibt) auf:

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Verwenden Sie `kubectl`, um Anforderungen an den Cluster zu senden:

    ```console
    kubectl get pods
    ```
    
    Nun sollte eine Antwort aus dem Cluster mit der Liste aller Pods im `default`-Namespace angezeigt werden.

### <a name="option-2-service-account-bearer-token"></a>Option 2: Bearertoken des Dienstkontos

1. Wenn die `kubeconfig`-Datei auf den `apiserver` Ihres Kubernetes-Clusters zeigt, erstellen Sie ein Dienstkonto in einem beliebigen Namespace (der folgende Befehl erstellt es im Standardnamespace):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Erstellen Sie ClusterRoleBinding oder RoleBinding, um diesem [Dienstkonto die entsprechenden Berechtigungen für den Cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding) zu erteilen:

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Rufen Sie das Token des Dienstkontos mit den folgenden Befehlen ab.

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Rufen Sie die Cluster Connect-`kubeconfig`-Datei für die Kommunikation mit dem Cluster von einem beliebigen Standort (auch von außerhalb der Firewall, die den Cluster umgibt) auf:

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Verwenden Sie `kubectl`, um Anforderungen an den Cluster zu senden:

    ```console
    kubectl get pods
    ```

    Nun sollte eine Antwort aus dem Cluster mit der Liste aller Pods im `default`-Namespace angezeigt werden.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Wenn beim Senden von Anforderungen an den Kubernetes-Cluster die verwendete Azure AD-Entität Teil von mehr als 200 Gruppen ist, tritt der folgende Fehler auf, da dies eine bekannte Einschränkung ist:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

So umgehen Sie diesen Fehler:
1. Erstellen Sie einen [Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli), der weniger wahrscheinlich Mitglied von mehr als 200 Gruppen ist.
1. [Melden Sie sich](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) bei Azure CLI mit dem Dienstprinzipal an, bevor Sie den `az connectedk8s proxy`-Befehl ausführen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Einrichten von [Azure AD RBAC](azure-rbac.md) auf Ihren Clustern