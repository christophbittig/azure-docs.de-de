---
title: Verwenden von Cluster Connect zum Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Verwenden von Cluster Connect zum sicheren Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung
ms.openlocfilehash: e585b67495aef1fb094d157c584c6744d993b135
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997911"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Verwenden von Cluster Connect zum Herstellen einer Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung

Mit Cluster Connect können Sie sicher eine Verbindung mit Kubernetes-Clustern mit Azure Arc-Unterstützung herstellen, ohne dass ein Eingangsport in der Firewall aktiviert werden muss. Der Zugriff auf den `apiserver` des Kubernetes-Clusters mit Azure Arc-Unterstützung ermöglicht die folgenden Szenarien:
* Aktivieren von interaktivem Debuggen und Problembehandlung.
* Bereitstellen des Clusterzugriffs auf Azure-Dienste für [benutzerdefinierte Speicherorte](custom-locations.md) und andere auf dieser Basis erstellte Ressourcen.

Eine konzeptionelle Übersicht zu diesem Feature finden Sie im Artikel [Cluster Connect: Kubernetes mit Azure Arc-Unterstützung](conceptual-cluster-connect.md).

## <a name="prerequisites"></a>Voraussetzungen   

- [Installieren oder aktualisieren Sie Azure CLI](/cli/azure/install-azure-cli) auf eine Version >= 2.16.0

- Installieren Sie die Azure CLI-Erweiterung `connectedk8s` in einer Version >= 1.1.0:

    ```console
    az extension add --name connectedk8s
    ```
  
    Wenn Sie die Erweiterung `connectedk8s` bereits installiert haben, aktualisieren Sie die Erweiterung auf die neueste Version:
    
    ```console
    az extension update --name connectedk8s
    ```

- Ein vorhandener Cluster, der mit Kubernetes mit Azure Arc-Unterstützung verbunden ist
    - Wenn Sie noch keine Verbindung mit einem Cluster hergestellt haben, verwenden Sie unseren [Schnellstart](quickstart-connect-cluster.md).
    - [Aktualisieren Sie Ihre Agents](agent-upgrade.md#manually-upgrade-agents) auf Version >= 1.1.0.

- Aktivieren Sie die folgenden Endpunkte neben den unter [Erfüllen von Netzwerkanforderungen](quickstart-connect-cluster.md#meet-network-requirements) angegebenen Endpunkten für ausgehenden Zugriff:

    | Endpunkt | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- Ersetzen Sie die Platzhalter, und führen Sie den folgenden Befehl aus, um die in diesem Dokument verwendeten Umgebungsvariablen festzulegen:

    ```console
    CLUSTER_NAME=<cluster-name>
    RESOURCE_GROUP=<resource-group-name>
    ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
    ```


## <a name="enable-cluster-connect-feature"></a>Aktivieren des „Cluster Connect“-Features

Sie können Cluster Connect auf jedem Kubernetes-Cluster mit Azure Arc-Unterstützung aktivieren, indem Sie den folgenden Befehl auf einem Computer ausführen, auf dem die `kubeconfig`-Datei auf den betreffenden Cluster verweist:

```console
az connectedk8s enable-features --features cluster-connect -n $CLUSTER_NAME -g $RESOURCE_GROUP
```

## <a name="azure-active-directory-authentication-option"></a>Authentifizierungsoption „Azure Active Directory“

1. Rufen Sie die Ihrer Azure AD-Entität zugeordnete `objectId` ab:

    - Für Azure AD-Benutzerkonto:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query objectId -o tsv)
        ```

    - Für Azure AD-Anwendung:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
        ```

1. Autorisieren Sie die AAD-Entität mit den entsprechenden Berechtigungen:

    - Wenn Sie native „ClusterRoleBinding“ oder „RoleBinding“ von Kubernetes für Autorisierungsprüfungen im Cluster verwenden, wobei die `kubeconfig`-Datei für den direkten Zugriff auf den `apiserver` Ihres Clusters verweist, können Sie eine Rollenzuweisung erstellen, die der Azure AD-Entität (Dienstprinzipal oder Benutzer) zugeordnet ist, die auf diesen Cluster zugreifen muss. Beispiel:
    
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
        ```

    - Wenn Sie Azure RBAC für Autorisierungsprüfungen im Cluster verwenden, können Sie eine Azure-Rollenzuweisung erstellen, die der Azure AD-Entität zugeordnet ist. Beispiel:

        ```console
        az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
        ```

## <a name="service-account-token-authentication-option"></a>Authentifizierungsoption „Dienstkontotoken“

1. Wenn die `kubeconfig`-Datei auf den `apiserver` Ihres Kubernetes-Clusters zeigt, erstellen Sie ein Dienstkonto in einem beliebigen Namespace (der folgende Befehl erstellt es im Standardnamespace):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Erstellen Sie „ClusterRoleBinding“ oder „RoleBinding“, um diesem [Dienstkonto die geeignete Berechtigungen für den Cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding) zu erteilen. Beispiel:

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

## <a name="access-your-cluster"></a>Zugreifen auf Ihren Cluster

1. Richten Sie die auf Cluster Connect basierende kubeconfig-Datei ein, die für den Zugriff auf Ihren Cluster, basierend auf der verwendeten Authentifizierungsoption, erforderlich ist:

    - Wenn Sie die Authentifizierungsoption „Azure Active Directory“ verwenden, rufen Sie nach der Anmeldung bei Azure CLI mithilfe der gewünschten Azure AD-Entität die `kubeconfig`-Datei von Cluster Connect ab, die für die Kommunikation mit dem Cluster von einem beliebigen Standort aus (auch von außerhalb der Firewall, die den Cluster umgibt) erforderlich ist:

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
        ```

    - Wenn Sie die Authentifizierungsoption „Dienstkonto“ verwenden, rufen Sie die `kubeconfig`-Datei von Cluster Connect ab, die für die Kommunikation mit dem Cluster von einem beliebigen Standort aus erforderlich ist:

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
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
