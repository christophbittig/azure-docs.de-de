---
title: 'Tutorial: Bereitstellen von Konfigurationen mithilfe von GitOps in einem Kubernetes-Cluster mit Azure Arc-Aktivierung'
description: In diesem Tutorial wird das Anwenden von Konfigurationen auf einen Kubernetes-Cluster mit Azure Arc-Aktivierung veranschaulicht. Einen konzeptionellen Einblick in diesen Prozess finden Sie im Artikel „Konfigurationen und GitOps – Kubernetes mit Azure Arc-Aktivierung“.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: c98a912394bf660f7a2f09c25c10629fd9810434
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098301"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Tutorial: Bereitstellen von Konfigurationen mithilfe von GitOps in einem Kubernetes-Cluster mit Azure Arc-Aktivierung 

In diesem Tutorial werden Sie Konfigurationen mithilfe von GitOps auf einen Kubernetes-Cluster mit Azure Arc-Aktivierung anwenden. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Konfiguration auf einem Kubernetes-Cluster mit Azure Arc-Aktivierung mit einem Git-Beispielrepository.
> * Überprüfen Sie, ob die Konfiguration erfolgreich erstellt wurde.
> * Anwenden der Konfiguration aus einem privaten Git-Repository.
> * Überprüfen Sie die Kubernetes-Konfiguration.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein vorhandener Cluster, der mit Kubernetes mit Azure Arc-Aktivierung verbunden ist.
    - Wenn Sie noch keine Verbindung mit einem Cluster hergestellt haben, führen Sie unseren [Schnellstart zum Verbinden eines Kubernetes-Clusters mit Azure Arc-Aktivierung](quickstart-connect-cluster.md) durch.
- Ein grundlegendes Verständnis der Vorteile und der Architektur dieses Features. Weitere Informationen finden Sie im Artikel [Konfigurationen und GitOps: Kubernetes mit Azure Arc-Aktivierung](conceptual-configurations.md).
- Installieren Sie die `k8s-configuration` Azure CLI-Erweiterung der Version >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Wenn die `k8s-configuration` Erweiterung bereits installiert ist, können Sie sie mit dem folgenden Befehl auf die neueste Version aktualisieren - `az extension update --name k8s-configuration`

- Wenn sich Ihr Git-Repository außerhalb der Firewall befindet und das Git-Protokoll mit dem Parameter des Konfigurations-Repositorys verwendet wird, muss TCP an Port 9418 (`git://:9418`) für den ausgehenden Zugriff an der Firewall aktiviert werden.

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

Das in diesem Artikel verwendete [Beispielrepository](https://github.com/Azure/arc-k8s-demo) ist um die Persona eines Clusteroperators herum strukturiert. Die Manifeste in diesem Repository stellen einige Namespaces und Workloads bereit und bieten einige teamspezifische Konfigurationen. Durch Verwendung dieses Repositorys mit GitOps werden die folgenden Ressourcen in Ihrem Cluster erstellt:

* Namespaces: `cluster-config`, `team-a`, `team-b`
* Bereitstellung: `arc-k8s-demo`
* ConfigMap: `team-a/endpoints`

Der `config-agent` fragt Azure nach neuen oder aktualisierten Konfigurationen ab. Diese Aufgabe kann bis zu 5 Minuten dauern.

Führen Sie auch die Schritte unter [Anwenden der Konfiguration über ein privates Git-Repository](#apply-configuration-from-a-private-git-repository) aus, wenn Sie ein privates Repository mit der Konfiguration zuordnen.

## <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Verwenden Sie die Azure CLI-Erweiterung für `k8s-configuration`, um einen verbundenen Cluster mit dem [Git-Beispielrepository](https://github.com/Azure/arc-k8s-demo) zu verknüpfen. 
1. Benennen Sie diese Konfiguration `cluster-config`.
1. Weisen Sie den Agent an, den Operator im `cluster-config`-Namespace bereitzustellen.
1. Erteilen Sie dem Operator `cluster-admin`-Berechtigungen.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
    }
    ```

### <a name="use-a-public-git-repository"></a>Verwenden eines öffentlichen Git-Repositorys

| Parameter | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] oder git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Verwenden eines privaten Git-Repositorys mit SSH und mit Flux erstellten Schlüsseln

Fügen Sie den mit Flux generierten öffentlichen Schlüssel zum Benutzerkonto in Ihrem Git-Dienstanbieter hinzu. Wenn der Schlüssel dem Repository statt dem Benutzerkonto hinzugefügt wird, verwenden Sie in der URL `git@` anstelle von `user@`. 

Weitere Informationen finden Sie im Abschnitt [Anwenden einer Konfiguration aus einem privaten Git-Repository](#apply-configuration-from-a-private-git-repository).


| Parameter | Format | Notizen
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Verwenden eines privaten Git-Repositorys mit SSH und benutzerseitig bereitgestellten Schlüsseln

Stellen Sie Ihren eigenen privaten Schlüssel direkt oder in einer Datei bereit. Der Schlüssel muss im [PEM-Format](https://aka.ms/PEMformat) sein und mit einem Zeilenumbruch (\n) enden. 

Fügen Sie den zugeordneten öffentlichen Schlüssel zum Benutzerkonto in Ihrem Git-Dienstanbieter hinzu. Wenn der Schlüssel dem Repository statt dem Benutzerkonto hinzugefügt wird, verwenden Sie `git@` anstelle von `user@`. 

Weitere Informationen finden Sie im Abschnitt [Anwenden einer Konfiguration aus einem privaten Git-Repository](#apply-configuration-from-a-private-git-repository).  

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen |
| `--ssh-private-key` | base64-codierter Schlüssel im [PEM-Format](https://aka.ms/PEMformat) | Direktes Bereitstellen des Schlüssels |
| `--ssh-private-key-file` | Vollständiger Pfad zur lokalen Datei | Bereitstellen des vollständigen Pfads zur lokalen Datei, die den Schlüssel im PEM-Format enthält

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Verwenden eines privaten Git-Hosts mit SSH und benutzerseitig bereitgestellten bekannten Hosts

Der Flux-Operator verwaltet eine Liste gängiger Git-Hosts in seiner Datei bekannter Hosts, um das Git-Repository vor dem Einrichten der SSH-Verbindung zu authentifizieren. Wenn Sie ein *nicht gängiges* Git-Repository oder Ihren eigenen Git-Host verwenden, können Sie den Hostschlüssel bereitstellen, damit Flux Ihr Repository identifizieren kann. 

Wie private Schlüssel können Sie Ihren known_hosts-Inhalt (bekannte Hosts) direkt oder in einer Datei bereitstellen. Wenn Sie Ihren eigenen Inhalt bereitstellen, verwenden Sie die [Spezifikationen des known_hosts-Inhaltformats](https://aka.ms/KnownHostsFormat) zusammen mit einem der oben aufgeführten SSH-Schlüsselszenarien.

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen |
| `--ssh-known-hosts` | base64-codiert | Inhalte bekannter Hosts direkt bereitstellen |
| `--ssh-known-hosts-file` | Vollständiger Pfad zur lokalen Datei | Inhalte bekannter Hosts in einer lokalen Datei bereitstellen |

### <a name="use-a-private-git-repository-with-https"></a>Verwenden eines privaten Git-Repository mit HTTPS

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | HTTPS mit Standardauthentifizierung |
| `--https-user` | Unformatiert (raw) base64-codiert | HTTPS-Benutzername |
| `--https-key` | Unformatiert (raw) base64-codiert | Persönliches HTTPS-Zugriffstoken oder -Kennwort

>[!NOTE]
>* Die Chartversion 1.2.0+ des Helm-Operators unterstützt die private Authentifizierung von HTTPS Helm Release.
>* HTTPS Helm Release wird für verwaltete AKS-Cluster nicht unterstützt.
>* Wenn Sie Flux benötigen, um über Ihren Proxy auf das Git-Repository zuzugreifen, müssen Sie die Azure Arc-Agents mit den Proxyeinstellungen aktualisieren. Informationen finden Sie unter [Herstellen einer Verbindung mithilfe eines ausgehenden Proxyservers](./quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Zusätzliche Parameter

Passen Sie die Konfiguration mit den folgenden optionalen Parametern an:

| Parameter | BESCHREIBUNG |
| ------------- | ------------- |
| `--enable-helm-operator`| Switch zum Aktivieren der Unterstützung für Helm-Chartbereitstellungen. |
| `--helm-operator-params` | Chartwerte für den Helm-Operator (falls aktiviert). Beispiel: `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Chartversion für den Helm-Operator (falls aktiviert). Verwenden Sie Version 1.2.0 und höher. Standardwert: ‚1.2.0‘. |
| `--operator-namespace` | Name für den Operatornamespace. Standardwert: „default“. Max: 23 Zeichen. |
| `--operator-params` | Parameter für den Operator. Diese müssen in einfachen Anführungszeichen angegeben werden. Zum Beispiel, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>In `--operator-params` unterstützte Optionen:

| Option | Beschreibung |
| ------------- | ------------- |
| `--git-branch`  | Die Verzweigung des Git-Repositorys für Kubernetes-Manifeste. Der Standardwert ist „master“. Neuere Repositorys haben einen Stammzweig namens `main`, in diesem Fall müssen Sie `--git-branch=main` festlegen. |
| `--git-path`  | Dies ist der relative Pfad im Git-Repository für Flux zum Suchen von Kubernetes-Manifesten. |
| `--git-readonly` | Das Git-Repository wird als schreibgeschützt betrachtet. Flux wird nicht versuchen, darin zu schreiben. |
| `--manifest-generation`  | Wenn diese Option aktiviert ist, sucht Flux nach „.flux.yaml“ und führt Kustomize oder andere Manifest-Generatoren aus. |
| `--git-poll-interval`  | Dies ist die Zeitspanne, in der das Git-Repository für neue Commits abgerufen wird. Der Standardwert ist `5m` (fünf Minuten). |
| `--sync-garbage-collection`  | Wenn diese Option aktiviert ist, werden die von Flux erstellten Ressourcen gelöscht und sind nicht mehr in Git verfügbar. |
| `--git-label`  | Bezeichnung zum Nachverfolgen des Synchronisierungsprozesses. Wird verwendet, um die Git-Verzweigung zu markieren.  Der Standardwert ist `flux-sync`. |
| `--git-user`  | Benutzername für Git-Commits. |
| `--git-email`  | Für Git-Commits zu verwendende E-Mail-Adresse. 

Wenn Flux nicht in das Repository schreiben soll und `--git-user` oder `--git-email` nicht festgelegt ist, wird `--git-readonly` automatisch festgelegt.

Weitere Informationen finden Sie in der [Flux-Dokumentation](https://aka.ms/FluxcdReadme).

>[!NOTE]
> Flux synchronisiert standardmäßig über die Verzweigung `master` des Git-Repositorys. Neuere Git-Repositorys verfügen jedoch über die Stammverzweigung mit dem Namen `main`. In diesem Fall müssen Sie `--git-branch=main` in den Parametern vom Typ „--operator-params“ festlegen. 

> [!TIP]
> Sie können eine Konfiguration im Azure-Portal auf der Registerkarte **GitOps** der Kubernetes-Ressource mit Azure Arc-Aktivierung erstellen.

## <a name="validate-the-configuration"></a>Überprüfen der Konfiguration

Überprüfen Sie mithilfe der Azure CLI, ob die Konfiguration erfolgreich erstellt wurde.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Die Konfigurationsressource wird mit Compliancestatus, Nachrichten und Debuginformationen aktualisiert.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Wenn eine Konfiguration erstellt oder aktualisiert wird, geschieht im Hintergrund Folgendes:

1. Der `config-agent` von Azure Arc überwacht Azure Resource Manager auf neue oder aktualisierte Konfigurationen (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) und erkennt die neue Konfiguration `Pending`.
1. Der `config-agent` liest die Konfigurationseigenschaften und erstellt den Zielnamespace.
1. Der Azure Arc-`controller-manager` erstellt ein Kubernetes-Dienstkonto und ordnet es [ClusterRoleBinding oder RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) für die entsprechenden Berechtigungen (`cluster`- oder `namespace`-Bereich) zu. Dann stellt er eine Instanz von `flux` bereit.
1. Bei Verwendung der Option von SSH mit von Flux generierten Schlüsseln generiert `flux` einen SSH-Schlüssel und protokolliert den öffentlichen Schlüssel.
1. Der `config-agent` meldet den Status zurück an die Konfigurationsressource in Azure.

Während des Bereitstellungsprozesses wird der Status der Konfigurationsressource mehrfach geändert. Überwachen Sie den Fortschritt mit dem obigen `az k8s-configuration show ...`-Befehl:

| Phasenänderung | BESCHREIBUNG |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Hiermit werden der anfängliche Status und der Status während der Bearbeitung dargestellt. |
| `complianceStatus` -> `Installed`  | Der `config-agent` hat den Cluster erfolgreich konfiguriert und `flux` ohne Fehler bereitgestellt. |
| `complianceStatus` -> `Failed` | Beim `config-agent` ist bei der Bereitstellung von `flux` ein Fehler aufgetreten. Weitere Informationen finden Sie im `complianceStatus.message`-Antworttext. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Anwenden der Konfiguration aus einem privaten Git-Repository

Wenn Sie ein privates Git-Repository verwenden, müssen Sie den öffentlichen SSH-Schlüssel in Ihrem Repository konfigurieren. Der öffentliche SSH-Schlüssel wird entweder von Ihnen bereitgestellt oder von Flux generiert. Sie können den öffentlichen Schlüssel entweder für das spezifische Git-Repository oder den Git-Benutzer konfigurieren, der Zugriff auf das Repository hat. 

### <a name="get-your-own-public-key"></a>Abrufen Ihres eigenen öffentlichen Schlüssels

Wenn Sie Ihre eigenen SSH-Schlüssel generiert haben, verfügen Sie bereits über die privaten und öffentlichen Schlüssel.

#### <a name="get-the-public-key-using-azure-cli"></a>Abrufen des öffentlichen Schlüssels mithilfe der Azure CLI 

Verwenden Sie Folgendes in der Azure CLI, wenn die Schlüssel von Flux generiert werden.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Abrufen des öffentlichen Schlüssels über das Azure-Portal

Führen Sie die folgenden Schritte im Azure-Portal aus, wenn die Schlüssel von Flux generiert werden.

1. Navigieren Sie im Azure-Portal zur verbundenen Clusterressource.
2. Wählen Sie auf der Ressourcenseite „GitOps“ aus, um die Liste der Konfigurationen für diesen Cluster anzuzeigen.
3. Klicken Sie auf die Konfiguration, die das private Git-Repository verwendet.
4. Kopieren Sie den **öffentlichen Repositoryschlüssel** im geöffneten Kontextfenster unten auf der Seite.

#### <a name="add-public-key-using-github"></a>Hinzufügen eines öffentlichen Schlüssels mithilfe von GitHub

Nutzen Sie eine der folgenden Optionen:

* Option 1: Hinzufügen des öffentlichen Schlüssels zu Ihrem Benutzerkonto (gilt für alle Repositorys in Ihrem Konto):  
    1. Öffnen Sie GitHub, und klicken Sie in der oberen rechten Ecke der Seite auf Ihr Profilsymbol.
    2. Klicken Sie auf **Einstellungen**.
    3. Klicken Sie auf **SSH- und GPG-Schlüssel**.
    4. Klicken Sie auf **Neuer SSH-Schlüssel**.
    5. Geben Sie einen Titel an.
    6. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
    7. Klicken Sie auf **SSH-Schlüssel hinzufügen**.

* Option 2: Hinzufügen des öffentlichen Schlüssels als Bereitstellungsschlüssel zum Git-Repository (gilt nur für dieses Repository):  
    1. Öffnen Sie GitHub, und navigieren Sie zu Ihrem Repository.
    1. Klicken Sie auf **Einstellungen**.
    1. Klicken Sie auf **Bereitstellungsschlüssel**.
    1. Klicken Sie auf **Bereitstellungsschlüssel hinzufügen**.
    1. Geben Sie einen Titel an.
    1. Aktivieren Sie **Schreibzugriff gewähren**.
    1. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
    1. Klicken Sie auf **Schlüssel hinzufügen**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Hinzufügen eines öffentlichen Schlüssels mit einem Azure DevOps-Repository

Führen Sie die folgenden Schritte aus, um den Schlüssel zu Ihren SSH-Schlüsseln hinzuzufügen:

1. Klicken Sie unter **Benutzereinstellungen** oben rechts (neben dem Profilbild) auf **Öffentliche SSH-Schlüssel**.
1. Klicken Sie auf **+ Neuer Schlüssel**.
1. Geben Sie einen Namen an.
1. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
1. Klicken Sie auf **Hinzufügen**.

## <a name="validate-the-kubernetes-configuration"></a>Überprüfen der Kubernetes-Konfiguration

Nachdem der `config-agent` die `flux`-Instanz installiert hat, sollten die im Git-Repository gespeicherten Ressourcen an den Cluster weitergeleitet werden. Überprüfen Sie, ob die Namespaces, Bereitstellungen und Ressourcen erstellt wurden, mit dem folgenden Befehl:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Wir sehen, dass die Namespaces `team-a`, `team-b`, `itops` und `cluster-config` erstellt wurden.

Der `flux`-Operator wurde gemäß den Anweisungen der Konfigurationsressource im `cluster-config`-Namespace bereitgestellt:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Weitere Untersuchungen

Sie können die anderen Ressourcen, die als Teil des Konfigurationsrepositorys bereitgestellt werden, mit Folgendem untersuchen:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie eine Konfiguration über die Azure CLI oder das Azure-Portal. Nachdem Sie den Befehl zum Löschen ausgeführt haben, wird die Konfigurationsressource sofort in Azure gelöscht. Das vollständige Löschen der zugeordneten Objekte aus dem Cluster sollte innerhalb von 10 Minuten erfolgen. Wenn sich die Konfiguration beim Entfernen in einem fehlerhaften Zustand befindet, kann das vollständige Löschen der zugeordneten Objekte bis zu einer Stunde dauern.

Wenn eine Konfiguration mit dem `namespace`-Bereich gelöscht wird, wird der Namespace nicht von Azure Arc gelöscht, um zu vermeiden, dass vorhandene Workloads beschädigt werden. Bei Bedarf können Sie diesen Namespace manuell mit `kubectl` löschen.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Alle Änderungen am Cluster, die das Ergebnis von Bereitstellungen aus dem nachverfolgten Git-Repository sind, werden beim Löschen der Konfiguration nicht gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial erfahren Sie, wie Sie CI/CD mit GitOps implementieren.
> [!div class="nextstepaction"]
> [Implementieren von CI/CD mit GitOps](./tutorial-gitops-ci-cd.md)
