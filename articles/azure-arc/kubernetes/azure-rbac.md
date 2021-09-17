---
title: Azure RBAC für Kubernetes-Cluster mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Verwenden von Azure RBAC für die Berechtigungsprüfungen in Azure Arc-fähigen Kubernetes-Clustern.
ms.openlocfilehash: 2607f82e0935ead0b6013bae963e22616c340b80
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105038"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Integrieren von Azure Active Directory in Azure Arc-fähige Kubernetes-Cluster

Die Kubernetes-Objekttypen [„ClusterRoleBinding“ und „RoleBinding“](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) bieten Unterstützung bei der nativen Definition der Autorisierung in Kubernetes. Mit dieser Funktion können Sie Azure Active Directory (Azure AD) und Rollenzuweisungen in Azure verwenden, um die Berechtigungsprüfungen auf dem Cluster zu steuern. Das bedeutet, dass Sie jetzt mit Azure-Rollenzuweisungen detailliert steuern können, wer Ihre Kubernetes-Objekte – z. B. die Bereitstellung, den Pod und Dienst – lesen, schreiben und löschen kann.

Eine konzeptionelle Übersicht zu dieser Funktion finden Sie in dem Artikel [Azure RBAC auf Azure Arc-fähigem Kubernetes](conceptual-azure-rbac.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Installieren oder aktualisieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) auf die Version 2.16.0 oder höher.

- Installieren Sie die Azure-Befehlszeilenschnittstellen-Erweiterung `connectedk8s` der Version 1.1.0 oder höher:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Wenn die Erweiterung `connectedk8s` bereits installiert ist, kann sie mit dem folgenden Befehl auf die neueste Version aktualisiert werden: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Verbinden eines vorhandenen Azure Arc-fähigen Kubernetes-Clusters:
    - Wenn Sie noch keine Verbindung mit einem Cluster hergestellt haben, verwenden Sie unseren [Schnellstart](quickstart-connect-cluster.md).
    - [Führen Sie für Ihre Agents ein Upgrade](agent-upgrade.md#manually-upgrade-agents) auf Version 1.1.1.0 oder höher aus.

> [!NOTE]
> Diese Funktion kann für Managed Kubernetes-Angebote von Cloudanbietern wie Elastic Kubernetes-Dienst oder Google Kubernetes-Engine eingerichtet werden, bei denen der Benutzer keinen Zugriff auf den API-Server des Clusters hat. Für Azure Kubernetes Service-Cluster (AKS) ist dieses [Feature nativ verfügbar](../../aks/manage-azure-rbac.md) und erfordert nicht, dass der AKS-Cluster mit Azure Arc verbunden ist.

## <a name="set-up-azure-ad-applications"></a>Einrichten von Azure AD-Anwendungen

### <a name="create-a-server-application"></a>Das Erstellen einer Serveranwendung

1. Das Erstellen einer neuen Azure AD-Anwendung und deren Wert `appId` abrufen. Dieser Wert wird in späteren Schritten als `serverApplicationId` verwendet.

    ```azurecli
    CLUSTERNAME="<clusterName>"
    SERVER_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Server" --identifier-uris "https://${CLUSTERNAME}Server" --query appId -o tsv)
    echo $SERVER_APP_ID
    ```

1. Das Aktualisieren der Gruppenmitgliedschaftsansprüche für die Anwendung:

    ```azurecli
    az ad app update --id "${SERVER_APP_ID}" --set groupMembershipClaims=All
    ```

1. Das Erstellen eines Dienstprinzipals und dessen Feldwert `password` abrufen. Dieser Wert wird später als `serverApplicationSecret` benötigt, wenn Sie diese Funktion auf dem Cluster aktivieren.

    ```azurecli
    az ad sp create --id "${SERVER_APP_ID}"
    SERVER_APP_SECRET=$(az ad sp credential reset --name "${SERVER_APP_ID}" --credential-description "ArcSecret" --query password -o tsv)
    ```

1. Erteilen Sie der Anwendung die API-Berechtigungen „Anmelden und Benutzerprofil lesen“:

    ```azurecli
    az ad app permission add --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > Dieser Schritt muss von einem Azure-Mandantenadministrator ausgeführt werden.
    > 
    > Es wird empfohlen für jeden Cluster eine andere Serveranwendung zu erstellen, um diese Funktion in der Produktion zu verwenden.

### <a name="create-a-client-application"></a>Das Erstellen einer Clientanwendung

1. Das Erstellen einer neuen Azure AD-Anwendung und deren Wert `appId` abrufen. Dieser Wert wird in späteren Schritten als `clientApplicationId` verwendet.

    ```azurecli
    CLIENT_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Client" --native-app --reply-urls "https://${CLUSTERNAME}Client" --query appId -o tsv)
    echo $CLIENT_APP_ID
    ```

2. Erstellen Sie einen Dienstprinzipal für diese Clientanwendung:

    ```azurecli
    az ad sp create --id "${CLIENT_APP_ID}"
    ```

3. Das Abrufen des Werts `oAuthPermissionId` für die Serveranwendung:

    ```azurecli
    az ad app show --id "${SERVER_APP_ID}" --query "oauth2Permissions[0].id" -o tsv
    ```

4. Gewähren Sie der Clientanwendung die benötigten Berechtigungen:

    ```azurecli
    az ad app permission add --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}" --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}"
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Erstellen einer Rollenzuweisung für die Serveranwendung

Die Serveranwendung benötigt die Berechtigungen `Microsoft.Authorization/*/read`, um zu überprüfen, ob der Benutzer, der die Anforderung sendet, für die in der Anforderung enthaltenen Kubernetes-Objekte autorisiert ist.

1. Das Erstellen einer Datei namens *accessCheck.json* mit den folgenden Inhalten:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Das Ersetzen von `<subscription-id>` durch die tatsächliche Abonnement-ID.

2. Das Ausführen des folgenden Befehls, um die neue benutzerdefinierte Rolle zu erstellen:

    ```azurecli
    ROLE_ID=$(az role definition create --role-definition ./accessCheck.json --query id -o tsv)
    ```

3. Das Erstellen einer Rollenzuweisung auf der Serveranwendung als `assignee`, durch die Verwendung der von Ihnen erstellten Rolle:

    ```azurecli
    az role assignment create --role "${ROLE_ID}" --assignee "${SERVER_APP_ID}" --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-the-cluster"></a>Das Aktivieren von Azure RBAC auf dem Cluster

Das Aktivieren von der rollenbasierte Zugriffskontrolle (RBAC) auf Ihrem Arc-fähigen Kubernetes-Cluster, indem Sie den folgenden Befehl ausführen:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id "${SERVER_APP_ID}" --app-secret "${SERVER_APP_SECRET}"
```
    
> [!NOTE]
> Stellen Sie vor der Ausführung des obigen Befehls sicher, dass die `kubeconfig`-Datei auf dem Computer auf den Cluster verweist, für den Sie die Azure RBAC-Funktion aktivieren möchten.
>
> Verwenden Sie `--skip-azure-rbac-list` mit dem vorherigen Befehl, um eine kommagetrennte Liste mit Benutzernamen, E-Mails und OpenID-Verbindungen zu erhalten, die den Berechtigungsprüfungen unterzogen werden. Verwenden Sie hierzu native Kubernetes `ClusterRoleBinding`- und `RoleBinding`-Objekte anstelle von Azure RBAC.

### <a name="generic-cluster-where-no-reconciler-is-running-on-the-apiserver-specification"></a>Ein generischer Cluster, ohne ausgeführte Synchronisierung in der API-Server-Spezifikation

1. Stellen Sie über SSH eine Verbindung mit jedem Masterknoten des Clusters her, folgen Sie den nachstehenden Schritten:

    1. Öffnen Sie das `apiserver`-Manifest im Bearbeitungsmodus:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Fügen Sie unter `volumes` die folgende Spezifikation hinzu:
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Fügen Sie unter `volumeMounts` die folgende Spezifikation hinzu:

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Fügen Sie die folgenden `apiserver`-Argumente hinzu:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Wenn Sie das Kubernetes-Cluster mit der Version 1.19.0 oder höher verwenden, müssen Sie auch das folgende `apiserver`-Argument festlegen:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Speichern und schließen Sie den Editor, um den `apiserver`-Pod zu aktualisieren.


### <a name="cluster-created-by-using-cluster-api"></a>Mit Cluster-API erstellte Cluster

1. Kopieren Sie das Wächtergeheimnis, das die Authentifizierungs- und Autorisierungs-Webhookkonfigurationsdateien aus dem Workloadcluster enthält, auf Ihren Computer:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Ändern Sie das Feld `namespace` in der Datei *azure-arc-guard-manifests.yaml* in den Namespace innerhalb des Verwaltungsclusters in dem Sie die benutzerdefinierten Ressourcen zur Erstellung von Workloadclustern hinzufügen.

1. Wenden Sie dieses Manifest an:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Bearbeiten Sie das Objekt `KubeadmControlPlane` durch Ausführung von `kubectl edit kcp <clustername>-control-plane`:
    
    1. Fügen Sie unter `files` den folgenden Codeausschnitt hinzu:
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Fügen Sie unter `apiServer` > `extraVolumes` den folgenden Codeausschnitt hinzu:
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Fügen Sie unter `apiServer` > `extraArgs` den folgenden Codeausschnitt hinzu:
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Wählen Sie speichern und schließen, um das Objekt `KubeadmControlPlane` zu aktualisieren. Warten Sie, bis diese Änderungen auf dem Workloadcluster angezeigt werden.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Erstellen von Rollenzuweisungen für Benutzer zum Zugriff auf den Cluster

Die Besitzer der Azure Arc-fähigen Kubernetes-Ressource können entweder integrierte Rollen oder benutzerdefinierte Rollen verwenden, um anderen Benutzern den Zugriff auf die Kubernetes-Cluster zu gewähren.

### <a name="built-in-roles"></a>Integrierte Rollen

| Role | BESCHREIBUNG |
|---|---|
| [Anzeigeberechtigter für Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Ermöglicht schreibgeschützten Zugriff, um die meisten Objekte in einem Namespace anzuzeigen. Mit dieser Rolle können keine Geheimnisse angezeigt werden. Dies liegt daran, dass die `read`-Berechtigung für Geheimnisse den Zugriff auf die `ServiceAccount`-Anmeldeinformationen im Namespace aktivieren würde. Diese Anmeldeinformationen würden wiederum den API-Zugriff über den Wert `ServiceAccount` (eine Form der Rechteauswertung) ermöglichen. |
| [Schreibberechtigter für Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Ermöglicht Lese-/Schreibzugriff auf die meisten Objekte in einem Namespace. Diese Rolle lässt das Anzeigen oder Ändern von Rollen oder Rollenbindungen nicht zu. Diese Rolle erlaubt jedoch den Zugriff auf die Geheimnisse und das Ausführen von Pods als beliebigen `ServiceAccount`-Wert im Namespace. Sie kann verwendet werden, um die API-Zugriffsebenen eines beliebigen `ServiceAccount`-Wertes im Namespace zu erzielen. |
| [Azure Arc Kubernetes-Administrator](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Mit dieser Rolle wird Administratorzugriff gewährt. Sie soll innerhalb eines Namespace durch `RoleBinding` vergeben werden. Wenn Sie sie in `RoleBinding` verwenden, wird der Lese-/Schreibzugriff auf die meisten Ressourcen in einem Namespace gewährt, einschließlich der Möglichkeit zum Erstellen von Rollen und Rollenbindungen innerhalb des Namespace. Diese Rolle lässt keinen Schreibzugriff auf das Ressourcenkontingent oder den Namespace selbst zu. |
| [Azure Arc Kubernetes-Clusteradministrator](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Es ermöglicht den Administratoren-Zugriff, um beliebige Aktionen für beliebige Ressourcen auszuführen. Wenn Sie sie in `ClusterRoleBinding` verwenden, wird die vollständige Steuerung über jede Ressource in dem Cluster und in allen Namespaces gewährt. Wenn Sie sie in `RoleBinding` verwenden, wird die vollständige Steuerung über jede Ressource im Namespace der Rollenbindung, einschließlich des Namespace selbst gewährt.|

Sie können Rollenzuweisungen, bezogen auf dem Arc-fähigen Kubernetes-Cluster im Azure-Portal in dem Bereich **Zugriffssteuerung (IAM)** der Cluster-Ressource, erstellen. Sie können auch die folgenden Azure CLI-Befehle verwenden:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

Bei diesen Befehlen kann `AZURE-AD-ENTITY-ID` ein Benutzername (z. B. `testuser@mytenant.onmicrosoft.com`) oder sogar der `appId`-Wert eines Dienstprinzipals sein.

Hier sehen Sie ein weiteres Beispiel für die Erstellung einer Rollenzuweisung, die auf einen bestimmten Namespace innerhalb des Clusters bezogen ist:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Sie können auf den Cluster bezogene Rollenzuweisungen erstellen, indem Sie entweder das Azure-Portal oder die Azure CLI verwenden. Rollenzuweisungen, die auf Namespaces bezogen sind, können aber nur mithilfe der CLI erstellt werden.

### <a name="custom-roles"></a>Benutzerdefinierte Rollen

Sie können Ihre eigene Rollendefinition für die Verwendung in den Rollenzuweisungen erstellen.

Das folgende Beispiel zeigt eine Rollendefinition, die zulässt, dass der Benutzer die Bereitstellungen nur lesen kann. Weitere Informationen finden Sie in der [vollständigen Liste der Datenaktionen, die zum Erstellen einer Rollendefinition verwendet werden können](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Kopieren Sie das nachstehende JSON-Objekt in eine Datei mit dem Namen *custom-role.json*. Ersetzen Sie den Platzhalter `<subscription-id>` durch die tatsächliche Abonnement-ID. Die benutzerdefinierte Rolle verwendet eine der Datenaktionen. Sie ermöglicht Ihnen die Anzeige aller Bereitstellungen in dem Bereich (Cluster oder Namespace), in dem die Rollenzuweisung erstellt wird.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Das Erstellen Sie die Rollendefinition, indem Sie den folgenden Befehl aus dem Ordner ausführen, in dem Sie die Datei *custom-role.json* gespeichert haben:

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Das Erstellen einer Rollenzuweisung unter Verwendung dieser benutzerdefinierten Rollendefinition:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Konfigurieren von kubectl mit Benutzeranmeldeinformationen

Es gibt zwei Möglichkeiten, die *kubeconfig*-Datei abzurufen, die Sie für den Zugriff auf den Cluster benötigen:

- Verwenden Sie die [Cluster Connect](cluster-connect.md)-Funktion (`az connectedk8s proxy`) des Azure Arc-fähigen Kubernetes-Clusters.
- Der Clusteradministrator gibt die *kubeconfig*-Datei für jeden anderen Benutzer frei.

### <a name="if-youre-accessing-the-cluster-by-using-the-cluster-connect-feature"></a>Wenn Sie mit der Clusterverbindung-Funktion auf den Cluster zugreifen

Führen Sie den folgenden Befehl aus, um den Proxy-Prozess zu starten:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Nachdem der Proxy-Prozess ausgeführt wird, können Sie eine weitere Registerkarte in Ihrer Konsole öffnen, um [Anforderungen an den Cluster zu senden](#send-requests-to-the-cluster).

### <a name="if-the-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Wenn der Clusteradministrator die kubeconfig-Datei für Sie freigegeben hat 

1. Führen Sie den folgenden Befehl aus, um Anmeldeinformationen für den Benutzer festzulegen:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Öffnen Sie die *kubeconfig*-Datei, die Sie vorher erstellt haben. Überprüfen Sie unter `contexts`, dass der mit dem Cluster verknüpfte Kontext auf die Benutzeranmeldeinformationen verweist, die Sie im vorherigen Schritt erstellt haben.

1. Hinzufügen der **config-mode**-Einstellung unter `user` > `config`:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="send-requests-to-the-cluster"></a>Versenden von Anforderungen an den Cluster

1. Führen Sie einen beliebigen `kubectl`-Befehl aus. Beispiel:
   * `kubectl get nodes` 
   * `kubectl get pods`

1. Wenn Sie zur Durchführung einer browserbasierten Authentifizierung aufgefordert werden, kopieren Sie die URL (`https://microsoft.com/devicelogin`) für die Geräteanmeldung, und öffnen Sie diese in Ihrem Webbrowser.

1. Geben Sie den auf Ihrer Konsole aufgedruckten Code ein. Kopieren Sie den Code und fügen Sie ihn auf Ihrem Endgerät in die Eingabeaufforderung für die Geräteauthentifizierung ein.

1. Geben Sie den Benutzernamen (`testuser@mytenant.onmicrosoft.com`) und das zugehörige Kennwort ein.

1. Falls eine Fehlermeldung, wie die folgende, angezeigt wird, sind Sie nicht berechtigt auf die angeforderte Ressource zuzugreifen:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Ein Administrator muss eine neue Rollenzuweisung erstellen, die diesem Benutzer den Zugriff auf die Ressource gewährt.

## <a name="use-conditional-access-with-azure-ad"></a>Das Verwenden von bedingtem Zugriff mit dem Azure AD

Wenn Sie das Azure AD mit Ihrem Arc-fähigen Kubernetes-Cluster integrieren, können Sie den Zugriff darauf auch mithilfe des [bedingten Zugriffs](../../active-directory/conditional-access/overview.md) steuern.

> [!NOTE]
> Bedingter Azure AD-Zugriff ist eine Azure AD Premium-Funktion.

Um eine Beispielrichtlinie für den bedingten Zugriff zu erstellen, die mit dem Cluster verwendet werden soll, führen Sie die folgenden Schritte aus:

1. Suchen Sie oben im Azure-Portal nach dem Dienst **Azure Active Directory Domain Services** und wählen Sie ihn aus.
1. Wählen Sie auf der linken Seite im Menü „Azure Active Directory Domain Services“ **Unternehmensanwendungen** aus.
1. Wählen Sie auf der linken Seite im Menü „Unternehmensanwendungen“ **Bedingter Zugriff** aus.
1. Wählen Sie auf der linken Seite im Menü „Bedingter Zugriff“ **Richtlinien** > **Neue Richtlinie** aus.
    
    [ ![Screenshot: Die Schaltfläche zum Hinzufügen einer Richtlinie für den bedingten Zugriff.](./media/azure-rbac/conditional-access-new-policy.png) ](./media/azure-rbac/conditional-access-new-policy.png#lightbox)

1. Geben Sie einen Namen für die Richtlinie, z. B. **arc-k8s-Richtlinie**, ein.
1. Wählen Sie **Benutzer und Gruppen**. Wählen Sie unter **Einschließen** die Option **Benutzer und Gruppen auswählen** aus. Wählen Sie dann die Benutzer und Gruppen aus, auf die Sie die Richtlinie anwenden möchten. Wählen Sie für dieses Beispiel dieselbe Azure AD Gruppe aus, die Administratorzugriff auf Ihren Cluster hat.

    [ ![Screenshot: Das Auswählen von Benutzern oder Gruppen, um die Richtlinie für den bedingten Zugriff anzuwenden.](./media/azure-rbac/conditional-access-users-groups.png) ](./media/azure-rbac/conditional-access-users-groups.png#lightbox)

1. Wählen Sie **Cloud-Apps oder -aktionen** aus. Wählen Sie unter **Einschließen** die Option **Apps auswählen** aus. Suchen Sie dann nach der Serveranwendung, die Sie zuvor erstellt haben, und wählen Sie sie aus.

    [ ![Screenshot: Das Auswählen einer Serveranwendung zum Anwenden der Richtlinie für den bedingten Zugriff.](./media/azure-rbac/conditional-access-apps.png) ](./media/azure-rbac/conditional-access-apps.png#lightbox)

1. Klicken Sie unter **Zugriffssteuerungen** auf **Gewähren**. Wählen Sie **Zugriff gewähren** > **Markieren des Geräts als konform erforderlich** aus.

    [ ![Screenshot: Das Auswählen, dass nur konforme Geräte für die Richtlinie für den bedingten Zugriff zugelassen werden.](./media/azure-rbac/conditional-access-grant-compliant.png) ](./media/azure-rbac/conditional-access-grant-compliant.png#lightbox)
    
1. Wählen Sie unter **Richtlinie aktivieren** die Option **An** > **Erstellen** aus.

    [ ![Screenshot: Das Aktivieren der Richtlinie für den bedingten Zugriff.](./media/azure-rbac/conditional-access-enable-policies.png) ](./media/azure-rbac/conditional-access-enable-policies.png#lightbox)

Ein erneutes Zugreifen auf den Cluster. Das Ausführen des `kubectl get nodes`-Befehls zum Anzeigen der Knoten im Cluster:

```console
kubectl get nodes
```

Befolgen Sie die Anweisungen zum erneuten Anmelden. Eine Fehlermeldung zeigt an, dass Sie erfolgreich angemeldet sind. Ihr Administrator fordert aber an, dass das Zugriff anfordernde Gerät von Ihrem Azure AD verwaltet werden muss, damit es auf die Ressource zugreifen kann. Folgen Sie diesen Schritten:

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory**.
1. Wählen Sie **Unternehmensanwendungen**. Wählen Sie dann unter **Aktivität** die Option **Anmeldungen** aus. 
1. Ein Eintrag oben zeigt einen **Fehler** für den **Status** und den **Erfolg** für den **bedingten Zugriff** an. Wählen Sie den Eintrag und dann unter **Details** die Option **Bedingter Zugriff** aus. Wie Sie sehen können, ist Ihre Richtlinie für den bedingten Zugriff aufgeführt.

   [ ![Screenshot: Ein Fehler ist beim Anmeldeeintrag aufgrund der Richtlinie für den bedingten Zugriff aufgetreten.](./media/azure-rbac/conditional-access-sign-in-activity.png) ](./media/azure-rbac/conditional-access-sign-in-activity.png#lightbox)

## <a name="configure-just-in-time-cluster-access-with-azure-ad"></a>Das Konfigurieren von Just-In-Time-Clusterzugriff mit Azure AD

Eine weitere Option für die Clusterzugriffssteuerung ist die Verwendung von Privileged Identity Management (PIM) für Just-In-Time-Anforderungen.

>[!NOTE]
> Das PIM ist eine Azure AD Premium-Funktion, die eine Premium P2-SKU erfordert. Weitere Informationen zu Azure AD-SKUs finden Sie im [Preisleitfaden](https://azure.microsoft.com/pricing/details/active-directory/).

Führen Sie die folgenden Schritte aus, um die Just-In-Time-Zugriffsanforderungen für Ihren Cluster zu konfigurieren:

1. Suchen Sie oben im Azure-Portal nach dem Dienst **Azure Active Directory Domain Services** und wählen Sie ihn aus.
1. Notieren Sie die Mandanten-ID. Im nachfolgenden Teil dieser Anleitung bezeichnen wir diese ID als `<tenant-id>`.

    [ ![Screenshot: Die Azure Active Directory Domain Services Mandantendetails.](./media/azure-rbac/jit-get-tenant-id.png) ](./media/azure-rbac/jit-get-tenant-id.png#lightbox)

1. Wählen Sie im Menü „Azure Active Directory Domain Services“ auf der linken Seite unter **Verwalten** die Option **Gruppen** > **Neue Gruppe** aus.

    [ ![Screenshot: Das Auswählen für die Erstellung einer neuen Gruppe.](./media/azure-rbac/jit-create-new-group.png) ](./media/azure-rbac/jit-create-new-group.png#lightbox)

1. Stellen Sie sicher, dass die **Sicherheit** als **Gruppentyp** ausgewählt ist.  Die Eingabe eine Gruppennamens, z. B. **myJITGroup**. Wählen Sie unter **Azure AD-Rollen können der Gruppe zugewiesen werden (Vorschau)** die Option **Ja** aus. Wählen Sie abschließend **Erstellen**.

    [ ![Screenshot: Die Details für die neue Gruppe.](./media/azure-rbac/jit-new-group-created.png) ](./media/azure-rbac/jit-new-group-created.png#lightbox)

1. Sie werden zur Seite **Gruppen** zurückgeleitet. Wählen Sie Ihre neu erstellte Gruppe aus, und notieren Sie die Objekt-ID. Im nachfolgenden Teil dieser Anleitung bezeichnen wir diese ID als `<object-id>`.

    [ ![Screenshot: Der Objektbezeichner für die erstellte Gruppe ](./media/azure-rbac/jit-get-object-id.png) ](./media/azure-rbac/jit-get-object-id.png#lightbox)

1. Zurück im Azure-Portal, wählen Sie auf der linken Seite im Menü **Aktivität** die Option **Privilegierter Zugriff (Vorschau)** aus. Wählen Sie dann **Privilegierten Zugriff aktivieren** aus.

    [ ![Screenshot: Die Auswahl zum Aktivieren des privilegierten Zugriffs.](./media/azure-rbac/jit-enabling-priv-access.png) ](./media/azure-rbac/jit-enabling-priv-access.png#lightbox)

1. Wählen Sie **Zuweisungen hinzufügen** aus, um mit der Zugriffsgewährung zu beginnen.

    [ ![Screenshot: Die Schaltfläche zum Hinzufügen der aktiven Zuweisungen.](./media/azure-rbac/jit-add-active-assignment.png) ](./media/azure-rbac/jit-add-active-assignment.png#lightbox)

1. Wählen Sie eine Rolle für das **Mitglied** und anschließend die Benutzer und Gruppen aus, denen Sie Clusterzugriff gewähren möchten. Ein Gruppenadministrator kann diese Zuweisungen jederzeit ändern. Wenn Sie so weit sind, wählen Sie **Weiter** aus.

    [ ![Screenshot: Die Zuweisungen hinzufügen.](./media/azure-rbac/jit-adding-assignment.png) ](./media/azure-rbac/jit-adding-assignment.png#lightbox)

1. Wählen Sie einen Zuweisungstyp von **Aktiv** und die gewünschte Dauer aus, und geben Sie eine Begründung an. Wenn Sie so weit sind, wählen Sie **Zuweisen** aus. Weitere Informationen zu Zuweisungstypen finden Sie unter [Zuweisen eines Besitzers oder Mitglieds einer Gruppe](../../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group).

    [ ![Screenshot: Das Auswählen von Eigenschaften für eine Zuweisung.](./media/azure-rbac/jit-set-active-assignment.png) ](./media/azure-rbac/jit-set-active-assignment.png#lightbox)

Überprüfen Sie sich nach dem Einrichtung der Zuweisungen, dass der Just-In-Time-Zugriff funktioniert, indem Sie auf den Cluster zugreifen. Verwenden Sie dafür z. B. den `kubectl get nodes`-Befehl zum Anzeigen der Knoten im Cluster:

```console
kubectl get nodes
```

Beachten Sie die Authentifizierungsanforderung, und führen Sie die Authentifizierungsschritte aus. Wenn der Authentifizierungsvorgang erfolgreich war, sollte eine Ausgabe wie die folgende angezeigt werden:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME      STATUS   ROLES    AGE      VERSION
node-1    Ready    agent    6m36s    v1.18.14
node-2    Ready    agent    6m42s    v1.18.14
node-3    Ready    agent    6m33s    v1.18.14
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Stellen Sie eine sichere Verbindung mit dem Cluster über [Cluster Connect](cluster-connect.md) her.
