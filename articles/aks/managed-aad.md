---
title: Verwenden von Azure AD in Azure Kubernetes Service
description: Erfahren Sie, wie Sie Azure AD in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 10/20/2021
ms.author: miwithro
ms.openlocfilehash: 03d6b2e101103607ec1c699ebdf814d6f41cdb76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427397"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Von AKS verwaltete Azure Active Directory-Integration

Die von AKS verwaltete Azure AD-Integration vereinfacht den Azure AD-Integrationsprozess. Zuvor mussten Benutzer eine Client- und Server-App erstellen und vom Azure AD-Mandanten Berechtigungen zum Lesen von Verzeichnissen erhalten. In der neuen Version verwaltet der AKS-Ressourcenanbieter die Client- und Server-App.

## <a name="azure-ad-authentication-overview"></a>Übersicht über die Azure AD-Authentifizierung

Clusteradministratoren können die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes RBAC) auf Grundlage einer Benutzeridentität oder Verzeichnisgruppenmitgliedschaft konfigurieren. Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][open-id-connect].

Informieren Sie sich in der [Dokumentation zu den Konzepten der Azure Active Directory-Integration](concepts-identity.md#azure-ad-integration) über den Ablauf der Azure AD-Integration.

## <a name="limitations"></a>Einschränkungen 

* Die von AKS verwaltete Azure AD-Integration kann nicht deaktiviert werden.
* Das Ändern eines integrierten, von AKS verwalteten Azure AD-Clusters in Legacy-AAD wird nicht unterstützt.
* In Clustern ohne Aktivierung der Kubernetes-RBAC wird die von AKS verwaltete Azure AD-Integration nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Azure CLI-Version 2.29.0 oder höher
* kubectl mit der Mindestversion [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) oder [kubelogin](https://github.com/Azure/kubelogin)
* Wenn Sie [Helm](https://github.com/helm/helm) verwenden, benötigen Sie mindestens Version 3.3 von Helm.

> [!Important]
> Sie müssen kubectl mit der Mindestversion 1.18.1 verwenden. Der Unterschied zwischen den Unterversionen von Kubernetes und kubectl darf höchstens 1 Versionsstufe betragen. Wenn Sie nicht die richtige Version verwenden, können Authentifizierungsprobleme auftreten.

Verwenden Sie zum Installieren von kubectl und kubelogin die folgenden Befehle:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Verwenden Sie für andere Betriebssysteme [diese Anweisungen](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="before-you-begin"></a>Voraussetzungen

Für Ihren Cluster benötigen Sie eine Azure AD-Gruppe. Diese Gruppe wird als Administratorgruppe für den Cluster registriert, um Administratorberechtigungen auf Clusterebene zu erteilen. Sie können eine vorhandene Azure AD-Gruppe verwenden oder eine neue erstellen. Notieren Sie die Objekt-ID Ihrer Azure AD-Gruppe.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Verwenden Sie den folgenden Befehl, um eine neue Azure AD-Gruppe für Ihre Clusteradministratoren zu erstellen:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Erstellen eines AKS-Clusters mit aktiviertem Azure AD

Erstellen Sie mit den folgenden CLI-Befehlen einen AKS-Cluster.

Erstellen Sie eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Erstellen Sie einen AKS-Cluster, und ermöglichen Sie den Verwaltungszugriff für Ihre Azure AD-Gruppe.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Eine erfolgreiche Erstellung eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Sobald der Cluster erstellt ist, können Sie darauf zugreifen.

## <a name="access-an-azure-ad-enabled-cluster"></a>Zugreifen auf einen Azure AD-fähigen Cluster

Bevor Sie mithilfe einer in Azure AD definierten Gruppe auf den Cluster zugreifen, benötigen Sie die integrierte Rolle [Azure Kubernetes Service-Clusterbenutzer](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role).

Rufen Sie die Benutzeranmeldeinformationen für den Zugriff auf den Cluster ab:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Befolgen Sie die Anweisungen zum Anmelden.

Verwenden Sie den Befehl „kubectl get nodes“, um die Knoten im Cluster anzuzeigen:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurieren Sie die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC)](./azure-ad-rbac.md), um zusätzliche Sicherheitsgruppen für Ihre Cluster zu konfigurieren.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Behandeln von Zugriffsproblemen mit Azure AD

> [!Important]
> In den unten beschriebenen Schritten wird die reguläre Azure AD-Gruppenauthentifizierung umgangen. Wenden Sie dies nur in Notfällen an.

Falls Sie dauerhaft blockiert werden, weil Sie nicht über Zugriff auf eine gültige Azure AD-Gruppe mit Zugriff auf Ihren Cluster verfügen, können Sie trotzdem die Administratoranmeldeinformationen abrufen, um direkt auf den Cluster zuzugreifen.

Zum Ausführen dieser Schritte benötigen Sie Zugriff auf die integrierte [Administratorrolle für Azure Kubernetes Service-Cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role).

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Aktivieren der von AKS verwalteten Azure AD-Integration in einem vorhandenen Cluster

Sie können die von AKS verwaltete Azure AD-Integration in einem vorhandenen Cluster mit Kubernetes RBAC aktivieren. Stellen Sie sicher, dass Ihre Administratorengruppe Zugriff auf Ihren Cluster behält.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Eine erfolgreiche Aktivierung eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Laden Sie die Benutzeranmeldeinformationen erneut herunter, um auf Ihren Cluster zuzugreifen, indem Sie [diese Schritte][access-cluster] ausführen.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade auf die von AKS verwaltete Azure AD-Integration

Wenn in Ihrem Cluster eine Azure AD-Legacy-Integration verwendet wird, können Sie ein Upgrade auf die von AKS verwaltete AD-Integration durchführen.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Eine erfolgreiche Migration eines von AKS verwalteten Azure AD-Clusters wird im Antworttext mit dem folgenden Abschnitt angegeben:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Aktualisieren Sie kubeconfig, um auf den Cluster zuzugreifen, indem Sie die Schritte [hier][access-cluster] ausführen.

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nicht interaktive Anmeldung per kubelogin

Es gibt einige nicht interaktive Szenarien, z. B. Continuous Integration-Pipelines, die für Kubectl derzeit nicht verfügbar sind. Sie können [`kubelogin`](https://github.com/Azure/kubelogin) verwenden, um über eine nicht interaktive Dienstprinzipalanmeldung auf den Cluster zuzugreifen.

## <a name="disable-local-accounts"></a>Deaktivieren lokaler Konten

Bei der Bereitstellung eines AKS-Clusters sind lokale Konten standardmäßig aktiviert. Selbst wenn Sie die RBAC- oder Azure Active Directory-Integration aktivieren, ist der Zugriff mit `--admin` weiterhin möglich. Dieser Befehl ist im Wesentlichen eine nicht überwachbare Hintertüroption. Vor diesem Hintergrund bietet AKS Benutzern die Möglichkeit, lokale Konten über das Flag `disable-local-accounts` zu deaktivieren. Der API für verwaltete Cluster wurde auch das Feld `properties.disableLocalAccounts` hinzugefügt, um anzugeben, ob das Feature im Cluster aktiviert ist.

> [!NOTE]
> In Clustern mit aktivierter Azure AD-Integration können Benutzer, die einer durch `aad-admin-group-object-ids` angegebenen Gruppe angehören, weiterhin mit gewöhnlichen Anmeldeinformationen (keine Administratoranmeldeinformationen) Zugriff erhalten. In Clustern, in denen die Azure AD-Integration nicht aktiviert und `properties.disableLocalAccounts` auf true festgelegt ist, können Benutzer- und Administratoranmeldeinformationen nicht abgerufen werden.

> [!NOTE]
> Nachdem Benutzer lokaler Konten in einem bereits vorhandenen AKS-Cluster deaktiviert wurden, in dem Benutzer möglicherweise lokale Konten verwendet haben, muss der Administrator [die Clusterzertifikate rotieren](certificate-rotation.md#rotate-your-cluster-certificates), um die Zertifikate zu widerrufen, auf die diese Benutzer möglicherweise Zugriff haben.  Wenn es sich um einen neuen Cluster handelt, ist keine Aktion erforderlich.

### <a name="create-a-new-cluster-without-local-accounts"></a>Erstellen eines neuen Clusters ohne lokale Konten

Verwenden Sie den Befehl [az aks create][az-aks-create] mit dem Flag `disable-local-accounts`, um einen neuen AKS-Cluster ohne lokale Konten zu erstellen:

```azurecli-interactive
az aks create -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

Wenn das Feld `properties.disableLocalAccounts` in der Ausgabe auf true festgelegt ist, sind lokale Konten deaktiviert:

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

Beim Versuch, Administratoranmeldeinformationen abzurufen, wird eine Fehlermeldung angezeigt, dass das Feature den Zugriff verhindert:

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="disable-local-accounts-on-an-existing-cluster"></a>Deaktivieren lokaler Konten in einem vorhandenen Cluster

Verwenden Sie den Befehl [az aks update][az-aks-update] mit dem Flag `disable-local-accounts`, um lokale Konten in einem vorhandenen AKS-Cluster zu deaktivieren:

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

Wenn das Feld `properties.disableLocalAccounts` in der Ausgabe auf true festgelegt ist, sind lokale Konten deaktiviert:

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

Beim Versuch, Administratoranmeldeinformationen abzurufen, wird eine Fehlermeldung angezeigt, dass das Feature den Zugriff verhindert:

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="re-enable-local-accounts-on-an-existing-cluster"></a>Reaktivieren lokaler Konten in einem vorhandenen Cluster

In AKS ist es auch möglich, lokale Konten in einem vorhandenen Cluster mit dem Flag `enable-local` zu reaktivieren:

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --enable-local
```

Wenn das Feld `properties.disableLocalAccounts` in der Ausgabe auf false festgelegt ist, sind lokale Konten aktiviert:

```output
"properties": {
    ...
    "disableLocalAccounts": false,
    ...
}
```

Sie können erfolgreich Administratoranmeldeinformationen abrufen:

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Merged "<cluster-name>-admin" as current context in C:\Users\<username>\.kube\config
```

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Verwenden von bedingtem Zugriff mit Azure AD und AKS

Wenn Sie Azure AD in Ihren AKS-Cluster integrieren, können Sie den Zugriff darauf auch mithilfe von [bedingtem Zugriff][aad-conditional-access] steuern.

> [!NOTE]
> Bedingter Azure AD-Zugriff ist eine Azure AD Premium-Funktion.

Wenn Sie eine Beispielrichtlinie für bedingten Zugriff zur Verwendung mit AKS erstellen möchten, führen Sie die folgenden Schritte aus:

1. Suchen Sie oben im Azure-Portal nach dem Dienst Azure Active Directory, und wählen Sie ihn aus.
1. Wählen Sie auf der linken Seite im Menü für Azure Active Directory *Unternehmensanwendungen* aus.
1. Wählen Sie auf der linken Seite im Menü für Unternehmensanwendungen *Bedingter Zugriff* aus.
1. Wählen Sie auf der linken Seite im Menü für „Bedingter Zugriff“ *Richtlinien* und dann *Neue Richtlinie* aus.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Hinzufügen einer Richtlinie für bedingten Zugriff":::
1. Geben Sie einen Namen für die Richtlinie ein (z. B. *AKS-Richtlinie*).
1. Wählen Sie *Benutzer und Gruppen* und dann unter *Einschließen* die Option *Benutzer und Gruppen* aus. Wählen Sie die Benutzer und Gruppen aus, auf die Sie die Richtlinie anwenden möchten. Wählen Sie für dieses Beispiel dieselbe Azure AD Gruppe aus, die Administratorzugriff auf Ihren Cluster hat.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Auswählen von Benutzern oder Gruppen zum Anwenden der Richtlinie für bedingten Zugriff":::
1. Wählen Sie *Cloud-Apps oder -Aktionen* und dann unter *Einschließen* die Option *Apps auswählen* aus. Suchen Sie nach *Azure Kubernetes Service*, und wählen Sie *Azure Kubernetes Service AAD Server* aus.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Auswählen von Azure Kubernetes Service AD Server zum Anwenden der Richtlinie für bedingten Zugriff":::
1. Klicken Sie unter *Zugriffssteuerungen* auf *Gewähren*. Wählen Sie *Zugriff gewähren* und dann *Markieren des Geräts als kompatibel erforderlich* aus.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Auswählen, dass nur kompatible Geräte für die Richtlinie für bedingten Zugriff zugelassen werden":::
1. Wählen Sie unter *Richtlinie aktivieren* die Option *Ein* und dann *Erstellen* aus.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Aktivieren der Richtlinie für bedingten Zugriff":::

Rufen Sie die Benutzeranmeldeinformationen für den Zugriff auf den Cluster ab, beispielsweise:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Befolgen Sie die Anweisungen zum Anmelden.

Verwenden Sie den Befehl `kubectl get nodes` zum Anzeigen von Knoten im Cluster:

```azurecli-interactive
kubectl get nodes
```

Befolgen Sie die Anweisungen zum erneuten Anmelden. Beachten Sie die angezeigte Fehlermeldung. Sie werden darin informiert, dass Sie erfolgreich angemeldet sind, Ihr Administrator aber verlangt, dass das Gerät Zugriff anfordert, damit es für den Zugriff auf die Ressource von Ihrem Azure AD verwaltet werden kann.

Navigieren Sie im Azure-Portal zu Azure Active Directory, wählen Sie *Unternehmensanwendungen* und dann unter *Aktivität* die Option *Anmeldedaten* aus. Beachten Sie oben den Eintrag. Für *Status* wird der Wert *Fehler* und für *Bedingter Zugriff* der Wert *Erfolg* angezeigt. Wählen Sie den Eintrag und dann in *Details* die Option *Bedingter Zugriff* aus. Wie Sie sehen können, ist Ihre Richtlinie für bedingten Zugriff aufgeführt.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Anmeldeeintrag „Fehler“ aufgrund der Richtlinie für bedingten Zugriff":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Konfigurieren von Just-In-Time-Clusterzugriff mit Azure AD und AKS

Eine weitere Option für die Clusterzugriffssteuerung ist die Verwendung von Privileged Identity Management (PIM) für Just-In-Time-Anforderungen.

>[!NOTE]
> PIM ist eine Azure AD Premium-Funktion und erfordert eine Premium P2-SKU. Weitere Informationen zu Azure AD-SKUs finden Sie im [Preisleitfaden][aad-pricing].

Führen Sie die folgenden Schritte aus, um Just-In-Time-Zugriffsanforderungen mithilfe der von AKS verwalteten Azure AD-Integration in einen AKS-Cluster zu integrieren:

1. Suchen Sie oben im Azure-Portal nach dem Dienst Azure Active Directory, und wählen Sie ihn aus.
1. Notieren Sie sich die Mandanten-ID, auf die in den restlichen Anweisungen als `<tenant-id>` verwiesen wird. :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="In einem Webbrowser wird der Azure-Portalbildschirm für Azure Active Directory mit hervorgehobener Mandanten-ID angezeigt.":::
1. Wählen Sie im Menü für Azure Active Directory auf der linken Seite unter *Verwalten* die Option *Gruppen* und anschließend *Neue Gruppe* aus.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Active Directory Gruppenbildschirm im Azure-Portal mit hervorgehobener Option „Neue Gruppe“":::
1. Wählen Sie den Gruppentyp *Sicherheit* aus, und geben Sie einen Gruppennamen ein (beispielsweise *myJITGroup*). Wählen Sie unter *Azure AD-Rollen können der Gruppe zugewiesen werden (Vorschau)* die Option *Ja* aus. Wählen Sie abschließend *Erstellen*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Bildschirm zum Erstellen einer neuen Gruppe im Azure-Portal":::
1. Daraufhin wird wieder die Seite *Gruppen* angezeigt. Wählen Sie Ihre neu erstellte Gruppe aus, und notieren Sie sich die Objekt-ID, auf die in den restlichen Anweisungen als `<object-id>` verwiesen wird.
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Azure-Portalbildschirm für die soeben erstellte Gruppe mit hervorgehobener Objekt-ID":::
1. Stellen Sie einen AKS-Cluster mit der von AKS verwalteten Azure AD-Integration bereit, indem Sie die Werte `<tenant-id>` und `<object-id>` von vorhin verwenden:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Wählen Sie im Azure-Portal auf der linken Seite im Menü für *Aktivität* die Option *Privilegierter Zugriff (Vorschau)* und anschließend *Privilegierten Zugriff aktivieren* aus.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Seite „Privilegierter Zugriff (Vorschau)“ im Azure-Portal mit hervorgehobener Option „Privilegierten Zugriff aktivieren“":::
1. Wählen Sie *Zuweisungen hinzufügen* aus, um mit der Zugriffsgewährung zu beginnen.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Bildschirm „Privilegierter Zugriff (Vorschau)“ im Azure-Portal nach der Aktivierung. Die Option „Zuweisungen hinzufügen“ ist hervorgehoben.":::
1. Wählen Sie die Rolle *Mitglied* und anschließend die Benutzer und Gruppen aus, denen Sie Clusterzugriff gewähren möchten. Diese Zuweisungen können jederzeit durch einen Gruppenadministrator geändert werden. Wenn Sie so weit sind, wählen Sie *Weiter* aus.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Mitgliedschaftsbildschirm für „Zuweisungen hinzufügen“ im Azure-Portal mit einem ausgewählten Beispielbenutzer, der als Mitglied hinzugefügt werden soll. Die Option „Weiter“ ist hervorgehoben.":::
1. Wählen Sie den Zuweisungstyp *Aktiv* und die gewünschte Dauer aus, und geben Sie eine Begründung an. Wenn Sie so weit sind, wählen Sie *Zuweisen* aus. Weitere Informationen zu Zuweisungstypen finden Sie unter [Zuweisen eines Besitzers oder Mitglieds einer Gruppe][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Einstellungsbildschirm für „Zuweisungen hinzufügen“ im Azure-Portal. Der Zuweisungstyp „Aktiv“ wurde ausgewählt, und eine Begründung wurde angegeben. Die Option „Zuweisen“ ist hervorgehoben.":::

Vergewissern Sie sich nach Einrichtung der Zuweisungen, dass der Just-In-Time-Zugriff funktioniert, indem Sie auf den Cluster zugreifen. Beispiel:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Führen Sie die Anmeldeschritte aus.

Verwenden Sie den Befehl `kubectl get nodes` zum Anzeigen von Knoten im Cluster:

```azurecli-interactive
kubectl get nodes
```

Beachten Sie die Authentifizierungsanforderung, und führen Sie die Authentifizierungsschritte aus. War der Vorgang erfolgreich, sollte eine Ausgabe wie die folgende angezeigt werden:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```
### <a name="apply-just-in-time-access-at-the-namespace-level"></a>Anwenden des Just-In-Time-Zugriffs auf Namespaceebene

1. Integrieren Sie Ihren AKS-Cluster in [Azure RBAC](manage-azure-rbac.md).
2. Ordnen Sie die Gruppe, die Sie mit Just-In-Time-Zugriff integrieren möchten, über die Rollenzuweisung einem Namespace im Cluster zu.

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```
3. Ordnen Sie die Gruppe, die Sie soeben auf Namespaceebene konfiguriert haben, dem PIM zu, um die Konfiguration abzuschließen.

### <a name="troubleshooting"></a>Problembehandlung

Es kann vorkommen, dass von `kubectl get nodes` eine Fehlermeldung wie die folgende zurückgegeben wird:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Vergewissern Sie sich in diesem Fall, dass der Administrator der Sicherheitsgruppe Ihrem Konto eine Zuweisung vom Typ *Aktiv* erteilt hat.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Verwendung der Azure RBAC-Integration für die Kubernetes-Autorisierung][azure-rbac-integration].
* Informieren Sie sich über die [Azure AD-Integration mit Kubernetes RBAC][azure-ad-rbac].
* Verwenden Sie [kubelogin](https://github.com/Azure/kubelogin), um auf Features der Azure-Authentifizierung zuzugreifen, die in Kubectl nicht verfügbar sind.
* Informieren Sie sich über die [Identitätskonzepte für AKS und Kubernetes][aks-concepts-identity].
* Erstellen Sie mit [Azure Resource Manager-Vorlagen (ARM)][aks-arm-template] von AKS verwaltete Cluster, die für Azure AD geeignet sind.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
