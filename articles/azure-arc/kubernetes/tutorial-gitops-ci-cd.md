---
title: 'Tutorial: Implementieren von Continuous Integration und Continuous Delivery (CI/CD) mit GitOps unter Verwendung von Kubernetes-Clustern mit Azure Arc-Unterstützung'
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie eine CI/CD-Lösung mithilfe von GitOps und Kubernetes-Clustern mit Azure Arc-Unterstützung einrichten. Einen konzeptionellen Einblick in diesen Workflow finden Sie im Artikel „CI/CD-Workflow unter Verwendung von GitOps – Kubernetes mit Azure Arc-Unterstützung“.
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: b73cc2a6b04d5e851611841f01890a60de71daab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039732"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Tutorial: Implementieren von Continuous Integration und Continuous Delivery (CI/CD) mit GitOps unter Verwendung von Kubernetes-Clustern mit Azure Arc-Unterstützung

In diesem Tutorial richten Sie eine CI/CD-Lösung mithilfe von GitOps und Kubernetes-Clustern mit Azure Arc-Unterstützung ein. Unter Verwendung der Azure Vote-Beispiel-App führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Kubernetes-Clusters mit Azure Arc-Unterstützung
> * Verbinden Ihrer Anwendung und GitOps-Repositorys mit Azure Repos.
> * Importieren von CI/CD-Pipelines.
> * Verbinden von Azure Container Registry (ACR) mit Azure DevOps und Kubernetes.
> * Erstellen von Gruppen aus Umgebungsvariablen.
> * Bereitstellen der `dev`- und `stage`-Umgebungen.
> * Testen der Anwendungsumgebungen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie mit Azure DevOps, Azure Repos und Pipelines sowie der Azure CLI vertraut sind.

* Melden Sie sich bei [Azure DevOps Services](https://dev.azure.com/) an.
* Schließen Sie das [vorherige Tutorial](./tutorial-use-gitops-connected-cluster.md) ab, um sich mit den Schritten zum Bereitstellen von GitOps für Ihre CI/CD-Umgebung vertraut zu machen.
* Machen Sie sich mit dem [Nutzen und der Architektur](./conceptual-configurations.md) dieser Funktion vertraut.
* Überprüfen Sie, ob Folgendes vorhanden ist:
  * Ein [verbundener Kubernetes-Cluster mit Azure Arc-Unterstützung](./quickstart-connect-cluster.md#3-connect-an-existing-kubernetes-cluster), dessen Name **arc-cicd-cluster** lautet
  * Eine verbundene ACR-Instanz (Azure Container Registry) mit [AKS-Integration](../../aks/cluster-container-registry-integration.md) oder [einer anderen Clusterauthentifizierung als AKS](../../container-registry/container-registry-auth-kubernetes.md).
  * Die Berechtigungen „Buildadministrator“ und „Projektadministrator“ für [Azure Repos](/azure/devops/repos/get-started/what-is-repos) und [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Installieren Sie die folgenden Kubernetes-CLI-Erweiterungen mit Azure Arc-Unterstützung mit mindestens der Version 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Führen Sie die folgenden Befehle aus, um diese Erweiterungen auf die aktuelle Version zu aktualisieren:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importieren von Anwendungs- und GitOps-Repositorys in Azure Repos

Importieren Sie ein [Anwendungsrepository](./conceptual-gitops-ci-cd.md#application-repo) und ein [GitOps-Repository](./conceptual-gitops-ci-cd.md#gitops-repo) in Azure Repos. Verwenden Sie für dieses Tutorial die folgenden Beispielrepositorys:

* das Anwendungsrepository **arc-cicd-demo-src**
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Dieses Repository enthält die Azure Vote-Beispiel-App, die Sie mithilfe von GitOps bereitstellen.
* das GitOps-Repository **arc-cicd-demo-gitops**
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Dieses Repository dient als Grundlage für Ihre Clusterressourcen, die für die Azure Vote-App verwendet werden.

Weitere Informationen zum [Importieren von Git-Repositorys](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Durch den Import und die Verwendung von zwei separaten Repositorys als Anwendungs- und GitOps-Repository lässt sich die Sicherheit verbessern. Außerdem werden die Abläufe vereinfacht. Darüber hinaus lassen sich die Berechtigungen und die Transparenz des Anwendungs- und GitOps-Repositorys einzeln optimieren.
> Für den Clusteradministrator können die Änderungen am Anwendungscode z. B. möglicherweise für den gewünschten Zustand des Clusters irrelevant sein. Ein Anwendungsentwickler wiederum muss die spezifischen Parameter für die einzelnen Umgebungen nicht kennen. Für ihn sind gegebenenfalls einige Testwerte für die Parameter ausreichend.

## <a name="connect-the-gitops-repo"></a>Verbinden des GitOps-Repositorys

Verbinden Sie das Anwendungsrepository für eine kontinuierliche Bereitstellung Ihrer App über GitOps mit Ihrem Cluster. Ihr GitOps-Repository **arc-cicd-demo-gitops** enthält die grundlegenden Ressourcen, um Ihre App in Ihrem **arc-cicd-cluster**-Cluster bereitzustellen und auszuführen.

Das anfängliche GitOps-Repository umfasst lediglich ein [Manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) zum Erstellen der Namespaces **dev** und **stage** für die Bereitstellungsumgebungen.

Durch die von Ihnen erstellte GitOps-Verbindung werden automatisch folgende Schritte ausgeführt:
* Synchronisieren des Manifests im Manifestverzeichnis.
* Aktualisieren des Clusterstatus.

Der CI/CD-Workflow füllt das Manifestverzeichnis mit zusätzlichen Manifesten zum Bereitstellen der App.


1. [Erstellen Sie eine neue GitOps-Verbindung](./tutorial-use-gitops-connected-cluster.md) mit Ihrem neu importierten Repository **arc-cicd-demo-gitops** in Azure Repos.

   ```azurecli
   az k8s-configuration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/<Your project>/_git/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Stellen Sie sicher, dass Flux *nur* das Verzeichnis `arc-cicd-cluster/manifests` als Basispfad verwendet. Definieren Sie den Pfad mithilfe des folgenden Operatorparameters:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Wenn Sie eine HTTPS-Verbindungszeichenfolge verwenden und Verbindungsprobleme auftreten, stellen Sie sicher, dass Sie das Benutzernamenpräfix in der URL auslassen. Bei `https://alice@dev.azure.com/contoso/project/_git/arc-cicd-demo-gitops` muss z. B. `alice@` entfernt werden. Der Benutzer wird stattdessen mit `--https-user` angegeben (z. B. `--https-user alice`).

1. Überprüfen Sie den Status der Bereitstellung im Azure-Portal.
   * Bei erfolgreicher Bereitstellung wurden in Ihrem Cluster die Namespaces `dev` und `stage` erstellt.

## <a name="import-the-cicd-pipelines"></a>Importieren der CI/CD-Pipelines

Nachdem Sie eine GitOps-Verbindung synchronisiert haben, müssen Sie die CI/CD-Pipelines importieren, mit denen die Manifeste erstellt werden.

Das Anwendungsrepository enthält einen Ordner `.pipeline` mit den Pipelines, die Sie für PRs, CI und CD verwenden. Importieren Sie die drei Pipelines aus dem Beispielrepository, und benennen Sie sie um:

| Pipelinedateiname | BESCHREIBUNG |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Die PR-Pipeline der Anwendung mit dem Namen **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Die CI-Pipeline der Anwendung mit dem Namen **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Die CD-Pipeline der Anwendung mit dem Namen **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Verbinden von ACR
Sowohl Ihre Pipelines als auch Ihr Cluster verwenden ACR zum Speichern und Abrufen von Docker-Images.

### <a name="connect-acr-to-azure-devops"></a>Verbinden von ACR mit Azure DevOps
Während des CI-Prozesses stellen Sie Ihre Anwendungscontainer in einer Registrierung bereit. Erstellen Sie dazu zunächst eine Azure-Dienstverbindung:

1. Öffnen Sie in Azure DevOps auf der Seite mit den Projekteinstellungen die Seite **Dienstverbindungen**. Öffnen Sie in TFS über das Symbol **Einstellungen** in der oberen Menüleiste die Seite **Dienste**.
2. Wählen Sie **+ Neue Dienstverbindung** und dann den Typ der Dienstverbindung aus, den Sie benötigen.
3. Geben Sie die Parameter für die Dienstverbindung ein. Verwenden Sie für dieses Tutorial Folgendes:
   * Geben Sie für die Dienstverbindung den Namen **arc-demo-acr** ein. 
   * Wählen Sie als Ressourcengruppe **myResourceGroup** aus.
4. Wählen Sie die Option **Allen Pipelines die Zugriffsberechtigung gewähren** aus. 
   * Mit dieser Option werden YAML-Pipelinedateien für Dienstverbindungen autorisiert. 
5. Wählen Sie **OK** aus, um die Verbindung zu erstellen.

### <a name="connect-acr-to-kubernetes"></a>Verbinden von ACR mit Kubernetes
Ermöglichen Sie es Ihrem Kubernetes-Cluster, Images aus ACR zu pullen. Bei einer privaten Konfiguration ist eine Authentifizierung erforderlich.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Verbinden von ACR mit vorhandenen AKS-Clustern

Integrieren Sie eine vorhandene ACR-Instanz über den folgenden Befehl mit vorhandenen AKS-Clustern:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Erstellen eines Geheimnisses für Imagepullvorgänge

Um Nicht-AKS- und lokale Cluster mit ACR zu verbinden, erstellen Sie ein Geheimnis für den Imagepullvorgang. Kubernetes verwendet Geheimnisse für Imagepullvorgänge, um die erforderlichen Informationen für die Authentifizierung Ihrer Registrierung zu speichern.

Verwenden Sie den folgenden `kubectl`-Befehl, um ein Geheimnis für Imagepullvorgänge zu erstellen. Wiederholen Sie diesen Schritt für die Namespaces `dev` und `stage`.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Um zu vermeiden, dass für jeden Pod ein Geheimnis für Imagepullvorgänge festgelegt werden muss, kann das Geheimnis für Imagepullvorgänge gegebenenfalls zum Dienstkonto in den Namespaces `dev` und `stage` hinzugefügt werden. Weitere Informationen finden Sie im [Kubernetes-Tutorial](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

## <a name="create-environment-variable-groups"></a>Erstellen von Gruppen aus Umgebungsvariablen

### <a name="app-repo-variable-group"></a>Variablengruppe des Anwendungsrepositorys
[Erstellen Sie eine Variablengruppe](/azure/devops/pipelines/library/variable-groups) mit dem Namen **az-vote-app-dev**. Legen Sie die folgenden Werte fest:

| Variable | Wert |
| -------- | ----- |
| AZ_ACR_NAME | (Ihre ACR-Instanz, z. B. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (Ihre Azure-Dienstverbindung, in diesem Fall die weiter oben in diesem Tutorial genannte Verbindung **arc-demo-acr**) |
| AZURE_VOTE_IMAGE_REPO | Der vollständige Pfad zum Repository der Azure Vote-App (z. B. azurearctest.azurecr.io/azvote) |
| ENVIRONMENT_NAME | Entwicklung |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_FOLDER | `azure-vote-manifests` |
| MANIFESTS_REPO | `arc-cicd-demo-gitops` |
| ORGANIZATION_NAME | Dies ist der Name der Azure DevOps-Organisation. |
| PROJECT_NAME | Dies ist der Name des GitOps-Projekts in Azure DevOps. |
| REPO_URL | Dies ist die vollständige URL für das GitOps-Repository. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

### <a name="stage-environment-variable-group"></a>Stagen der Gruppe aus Umgebungsvariablen

1. Klonen Sie die Variablengruppe **az-vote-app-dev**.
1. Ändern Sie den Namen in **az-vote-app-stage**.
1. Stellen Sie sicher, dass die folgenden Werte für die entsprechenden Variablen festgelegt sind:

| Variable | Wert |
| -------- | ----- |
| ENVIRONMENT_NAME | Phase |
| TARGET_NAMESPACE | `stage` |

Sie können die `dev`- und `stage`-Umgebungen jetzt bereitstellen.

## <a name="give-more-permissions-to-the-build-service"></a>Erteilen von weiteren Berechtigungen für den Builddienst
Die CD-Pipeline verwendet das Sicherheitstoken des ausgeführten Builds zur Authentifizierung beim GitOps-Repository. Für die Pipeline sind weitere Berechtigungen erforderlich, um einen neuen Branch zu erstellen, Änderungen zu pushen und Pull Requests zu erstellen.

1. Wechseln Sie von der Hauptseite des Azure DevOps-Projekts zu `Project settings`.
1. Wählen Sie `Repositories` aus.
1. Wählen Sie `<GitOps Repo Name>` aus.
1. Wählen Sie `Security` aus. 
1. Lassen Sie für `<Project Name> Build Service (<Organization Name>)` die Optionen `Contribute`, `Contribute to pull requests` und `Create branch` zu.

Weitere Informationen finden Sie unter
- [Erteilen von VC-Berechtigungen für den Builddienst](/azure/devops/pipelines/scripts/git-commands?preserve-view=true&tabs=yaml&view=azure-devops#version-control )
- [Verwalten der Berechtigungen für das Builddienstkonto](/azure/devops/pipelines/process/access-tokens?preserve-view=true&tabs=yaml&view=azure-devops#manage-build-service-account-permissions)


## <a name="deploy-the-dev-environment-for-the-first-time"></a>Erstmaliges Bereitstellen der Entwicklungsumgebung
Nachdem Sie die CI- und CD-Pipelines erstellt haben, führen Sie die CI-Pipeline aus, um die App erstmalig bereitzustellen.

### <a name="ci-pipeline"></a>CI-Pipeline

Bei der ersten Ausführung der CI-Pipeline wird möglicherweise ein Ressourcenautorisierungsfehler beim Lesen des Dienstverbindungsnamens ausgegeben.
1. Überprüfen Sie, ob auf die Variable AZURE_SUBSCRIPTION zugegriffen wird.
1. Autorisieren Sie die Verwendung.
1. Führen Sie die Pipeline erneut aus.

Die CI-Pipeline:
* Stellt sicher, dass die Anwendungsänderung alle automatisierten Qualitätsprüfungen für die Bereitstellung besteht.
* Führt zusätzliche Validierungsschritte durch, die in der PR-Pipeline nicht abgeschlossen werden konnten.
    * Bei GitOps veröffentlicht die Pipeline zudem die Artefakte für den Commit, die durch die CD-Pipeline bereitgestellt werden.
* Überprüft, ob das Docker-Image geändert wurde und das neue Image per Push übertragen wird.

### <a name="cd-pipeline"></a>CD-Pipeline
Während der ersten Ausführung der CD-Pipeline werden Sie aufgefordert, der Pipeline Zugriff auf das GitOps-Repository zu geben. Wählen Sie „Anzeigen“ aus, wenn angegeben wird, dass die Pipeline die Berechtigung für den Zugriff auf eine Ressource benötigt. Wählen Sie dann „Zulassen“ aus, um die Berechtigung zum Verwenden des GitOps-Repositorys für die aktuellen und zukünftigen Pipelineläufe zu erteilen.

Bei erfolgreicher Ausführung der CI-Pipeline wird das Abschließen des Bereitstellungsvorgangs durch die CD-Pipeline ausgelöst. Die einzelnen Umgebungen werden inkrementell bereitgestellt.

> [!TIP]
> Falls die CD-Pipeline nicht automatisch ausgelöst wird:
> 1. Überprüfen Sie, ob der Name mit dem Branchauslöser in [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) übereinstimmt
>    * Dort sollte `arc-cicd-demo-src CI` stehen.
> 1. Führen Sie die CI-Pipeline erneut aus.

Nachdem die Vorlagen- und Manifeständerungen für das GitOps-Repository generiert wurden, erstellt die CD-Pipeline einen Commit, führt einen Push aus und erstellt einen PR zur Genehmigung.
1. Öffnen Sie den PR-Link in der `Create PR`-Taskausgabe.
1. Überprüfen Sie die Änderungen am GitOps-Repository. Folgendes sollte angezeigt werden:
   * High-Level-Änderungen der Helm-Vorlage.
   * Low-Level-Kubernetes-Manifeste, die die zugrunde liegenden Änderungen am gewünschten Zustand zeigen. Diese Manifeste werden über Flux bereitgestellt.
1. Wenn alles gut aussieht, bestätigen Sie den PR, und führen Sie ihn aus.

1. Nach ein paar Minuten erkennt Flux die Änderung und startet die Bereitstellung.
1. Leiten Sie den Port lokal mithilfe von `kubectl` weiter, und stellen Sie sicher, dass die App korrekt funktioniert. Verwenden Sie dazu folgenden Befehl:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Rufen Sie die Azure Vote-App in Ihrem Browser unter `http://localhost:8080/` auf.

1. Stimmen Sie für Ihre Favoriten ab, und bereiten Sie sich darauf vor, einige Änderungen an der App vorzunehmen.

## <a name="set-up-environment-approvals"></a>Einrichten von Genehmigungen für Umgebungen
Bei der App-Bereitstellung können Sie nicht nur Änderungen am Code oder an Vorlagen vornehmen, sondern unbeabsichtigt auch Vorgänge ausführen, aufgrund derer ein ungültiger Zustand des Clusters ausgelöst wird.

Wenn es in der Entwicklungsumgebung nach der Bereitstellung zu einer Unterbrechung kommt, sollten Sie daher mithilfe von Genehmigungen verhindern, dass diese Probleme in spätere Umgebungen übertragen werden.

1. Navigieren Sie in Ihrem Azure DevOps-Projekt zur Umgebung, die geschützt werden soll.
1. Navigieren Sie zu **Genehmigungen und Prüfungen** für die Ressource.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie die genehmigenden Personen und eine optionale Nachricht ein.
1. Wählen Sie erneut **Erstellen** aus, um den Vorgang zum Hinzufügen der manuellen Genehmigungsprüfung abzuschließen.

Weitere Einzelheiten finden Sie im Tutorial zum [Definieren von Genehmigungen und Prüfungen](/azure/devops/pipelines/process/approvals).

Bei der nächsten Ausführung der CD-Pipeline wird die Pipeline angehalten, nachdem der GitOps-PR erstellt wurde. Überprüfen Sie, ob die Änderung ordnungsgemäß synchronisiert wurde und die grundlegende Funktionalität bietet. Bestätigen Sie die Überprüfung in der Pipeline, damit die Änderung für die nächste Umgebung übernommen wird.

## <a name="make-an-application-change"></a>Ändern der Anwendung

Mit diesem Basissatz an Vorlagen und Manifesten für den Zustand des Clusters nehmen Sie nun eine geringfügige Änderung an der App vor.

1. Bearbeiten Sie im Repository **arc-cicd-demo-src** die Datei [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg).

2. Da für die Abstimmung zwischen „Cats“ und „Dogs“ nicht genügend Stimmen abgegeben werden, soll nun zwischen „Tabs“ und „Spaces“ abgestimmt werden, um die Anzahl von Stimmen zu erhöhen.

3. Committen Sie die Änderung in einen neuen Branch, führen Sie dann einen Push durch, und erstellen Sie einen Pull Request.
   * Dies ist der typische Entwicklerflow, der den CI/CD-Lebenszyklus startet.

## <a name="pr-validation-pipeline"></a>PR-Validierungspipeline

Die PR-Pipeline ist die erste Schutzebene gegen eine fehlerhafte Änderung. Zu den üblichen Qualitätsprüfungen für Anwendungscode zählen das Linten und eine statische Analyse. Aus GitOps-Sicht müssen Sie dieselbe Qualität für die resultierende Infrastruktur sicherstellen, die bereitgestellt werden soll.

In den Dockerfile- und Helm-Diagrammen der Anwendung kann das Linten auf ähnliche Weise für die Anwendung verwendet werden.

Beim Linten wird u. a. Folgendes ermittelt:
* Falsch formatierte YAML-Dateien
* Empfehlungen für bewährte Methoden, z. B. das Festlegen von CPU- und Speichergrenzen für Ihre Anwendung.

> [!NOTE]
> Für ein möglichst umfangreiches Helm-Linten in einer echten Anwendung müssen Sie Werte ersetzen, die den in einer echten Umgebung verwendeten Werten am ehesten entsprechen.

Die bei einer Pipelineausführung ermittelten Fehler werden im Abschnitt mit den Testergebnissen der Ausführung angezeigt. Hier können Sie Folgendes tun:
* Nachverfolgen nützlicher Statistiken für die Fehlertypen.
* Suchen nach dem ersten Commit, bei dem die Fehler ermittelt wurden.
* Überwachungslinks zu Codeabschnitten, die den Fehler verursacht haben.

Nachdem die Pipelineausführung abgeschlossen wurde, ist eine fehlerfreie Qualität des Anwendungscodes und der Vorlage für die Bereitstellung sichergestellt. Sie können den PR jetzt bestätigen und abschließen. Die CI-Pipeline wird erneut ausgeführt, und die Vorlagen und Manifeste werden erneut generiert, bevor die CD-Pipeline ausgelöst wird.

> [!TIP]
> Vergessen Sie in einer realen Umgebung nicht, Branchrichtlinien festzulegen, um sicherzustellen, dass der PR Ihre Qualitätsprüfungen besteht. Weitere Informationen finden Sie im Artikel zum [Festlegen von Branchrichtlinien](/azure/devops/repos/git/branch-policies).

## <a name="cd-process-approvals"></a>CD-Prozessgenehmigungen

Bei erfolgreicher Ausführung der CI-Pipeline wird das Abschließen des Bereitstellungsvorgangs durch die CD-Pipeline ausgelöst. Ähnlich wie zuvor können Sie Genehmigungen für die CD-Pipeline konfigurieren, die Sie für jede Umgebung inkrementell bereitstellen. Dieses Mal müssen Sie jede Bereitstellungsumgebung genehmigen.

1. Genehmigen Sie die Bereitstellung für die `dev`-Umgebung.
1. Nachdem die Vorlagen- und Manifeständerungen für das GitOps-Repository generiert wurden, erstellt die CD-Pipeline einen Commit, führt einen Push aus und erstellt einen PR zur Genehmigung.
1. Öffnen Sie den PR-Link im Task.
1. Überprüfen Sie die Änderungen am GitOps-Repository. Folgendes sollte angezeigt werden:
   * High-Level-Änderungen der Helm-Vorlage.
   * Low-Level-Kubernetes-Manifeste, die die zugrunde liegenden Änderungen am gewünschten Zustand zeigen.
1. Wenn alles gut aussieht, bestätigen Sie den PR, und führen Sie ihn aus.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist.
1. Navigieren Sie zur Anwendungsseite, und überprüfen Sie, ob in der Abstimmungs-App jetzt „Tabs“ und „Spaces“ angezeigt werden.
   * Leiten Sie den Port lokal mithilfe von `kubectl` weiter, und stellen Sie sicher, dass die App korrekt funktioniert. Verwenden Sie dazu folgenden Befehl: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Rufen Sie die Azure Vote-App in Ihrem Browser unter http://localhost:8080/ auf, und überprüfen Sie, ob die Auswahl für die Abstimmung in „Tabs“ und „Spaces“ geändert wurde. 
1. Wiederholen Sie die Schritte 1-7 für die `stage`-Umgebung.

Die Bereitstellung ist jetzt abgeschlossen. Der CI/CD-Workflow ist damit beendet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie die Ressourcen wie folgt:

1. Löschen Sie die Verbindung für die Azure Arc-GitOps-Konfiguration:
   ```azurecli
   az k8s-configuration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Entfernen Sie den Namespace `dev`:
   * `kubectl delete namespace dev`

3. Entfernen Sie den Namespace `stage`:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen vollständigen CI/CD-Workflow eingerichtet, bei dem von der Anwendungsentwicklung bis hin zur Bereitstellung DevOps implementiert wird. Bei Änderungen an der App werden automatisch Validierungs- und Bereitstellungsschritte ausgelöst, die manuell genehmigt werden müssen.

Fahren Sie mit unserem konzeptionellen Artikel fort, um mehr über GitOps und Konfigurationen mit Kubernetes mit Azure Arc-Unterstützung zu erfahren.

> [!div class="nextstepaction"]
> [CI/CD-Workflow mit GitOps: Kubernetes mit Azure Arc-Unterstützung](./conceptual-gitops-ci-cd.md)
