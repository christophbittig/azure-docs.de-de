---
title: Erstellen eines Experiments mit dem Azure-Portal, das einen AKS Chaos Mesh-Fehler mit Azure Chaos Studio verwendet
description: Erstellen eines Experiments, das einen AKS Chaos Mesh-Fehler mit dem Azure-Portal verwendet
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7ae8da70739ded629753bdf3e3288a6b4ed279b8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373481"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods-with-the-azure-portal"></a>Erstellen eines Chaos-Experiments mit dem Azure-Portal, das einen Chaos Mesh-Fehler verwendet, um AKS-Pods zu entfernen

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie periodische Ausfälle von Azure Kubernetes Service-Pods in einem Namespace mit Hilfe eines Chaos-Experiments und Azure Chaos Studio verursachen. Die Durchführung dieses Experiments kann Ihnen helfen, sich gegen die Nichtverfügbarkeit von Diensten bei sporadischen Ausfällen zu schützen.

Azure Chaos Studio verwendet [Chaos Mesh](https://chaos-mesh.org/), eine kostenlose Open-Source-Chaos-Engineering-Plattform für Kubernetes, um Fehler in einen AKS-Cluster zu injizieren. Chaos Mesh-Fehler sind [service-direkte](chaos-studio-tutorial-aks-portal.md) Fehler, für die Chaos Mesh auf dem AKS-Cluster installiert sein muss. Mit den gleichen Schritten können Sie ein Experiment für einen beliebigen AKS-Chaos-Mesh-Fehler einrichten und durchführen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Ein AKS-Cluster. Wenn Sie noch keinen AKS-Cluster haben, können Sie [mit den folgenden Schritten einen erstellen](../aks/kubernetes-walkthrough-portal.md).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Chaos Mesh auf Ihrem AKS-Cluster einrichten

Bevor Sie Chaos Mesh-Fehler in Chaos Studio ausführen können, müssen Sie Chaos Mesh auf Ihrem AKS-Cluster installieren.

1. Führen Sie die folgenden Befehle in einem [Azure Cloud Shell](../cloud-shell/overview.md)-Fenster aus, in dem Sie als aktives Abonnement das Abonnement eingestellt haben, in dem Ihr AKS-Cluster bereitgestellt wird. Ersetzen Sie `$RESOURCE_GROUP` und `$CLUSTER_NAME` durch die Ressourcengruppe und den Namen Ihrer Clusterressource.

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. Überprüfen Sie, ob die Chaos Mesh-Pods installiert sind, indem Sie den folgenden Befehl ausführen:

```bash
kubectl get po -n chaos-testing
```

Sie sollten eine ähnliche Ausgabe wie die folgende sehen (ein Chaos-Controller-Manager und ein oder mehrere Chaos-Dämonen):

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

Sie können auch [die Installationsanweisungen auf der Chaos Mesh-Website](https://chaos-mesh.org/docs/production-installation-using-helm/) verwenden.


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>Aktivieren Sie Chaos Studio auf Ihrem AKS-Cluster

Chaos Studio kann keine Fehler gegen eine Ressource injizieren, wenn diese Ressource nicht zuerst in Chaos Studio eingebunden wurde. Sie binden eine Ressource in Chaos Studio ein, indem Sie ein [Ziel und Fähigkeiten](chaos-studio-targets-capabilities.md) für die Ressource erstellen. AKS-Cluster haben nur einen Zieltyp (service-direct), aber andere Ressourcen können bis zu zwei Zieltypen haben - einen für service-direkte Fehler und einen für agentenbasierte Fehler. Jede Art von Chaos-Mesh-Fehler wird als Fähigkeit dargestellt (PodChaos, NetworkChaos, IOChaos, etc.).

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie in der Suchleiste nach **Chaos Studio (Vorschau)** .
3. Klicken Sie auf **Ziele** und navigieren Sie zu Ihrem AKS-Cluster.
![Zielansicht im Azure-Portal](images/tutorial-aks-targets.png)
4. Markieren Sie das Kästchen neben Ihrem AKS-Cluster und klicken Sie im Dropdown-Menü auf **Ziele aktivieren** und dann auf **Direkte Service-Ziele aktivieren**.
![Aktivierung von Zielen im Azure-Portal](images/tutorial-aks-targets-enable.png)
5. Es erscheint eine Meldung, dass die ausgewählte(n) Ressource(n) erfolgreich aktiviert wurde(n).
![Benachrichtigung, dass das Ziel erfolgreich aktiviert wurde](images/tutorial-aks-targets-enable-confirm.png)

Sie haben nun Ihren AKS-Cluster erfolgreich in Chaos Studio eingebunden. In der Ansicht **Ziele** können Sie auch die für diese Ressource aktivierten Fähigkeiten verwalten. Wenn Sie auf den Link **Aktionen verwalten** neben einer Ressource klicken, werden die für diese Ressource aktivierten Funktionen angezeigt.

## <a name="create-an-experiment"></a>Erstellen eines Experiments
Wenn Ihr AKS-Cluster nun an Bord ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Klicken Sie auf die Registerkarte **Experimente** in der Chaos Studio-Navigation. In dieser Ansicht können Sie alle Ihre Chaosexperimente sehen und verwalten. Klicken Sie auf **Experiment hinzufügen**
![Ansicht Experimente im Azure-Portal](images/tutorial-aks-add.png)
2. Geben Sie **Abonnement**, **Ressourcengruppe** und **Ort** ein, an dem Sie das Chaos-Experiment durchführen möchten. Gib deinem Experiment einen **Namen**. Klicken Sie auf **Weiter : Experimentdesigner >** 
![ Hinzufügen von grundlegenden Experimentdetails](images/tutorial-aks-add-basics.png)
3. Sie befinden sich nun im Chaos Studio Experiment Designer. Mit dem Experiment-Designer können Sie Ihr Experiment durch Hinzufügen von Schritten, Verzweigungen und Fehlern aufbauen. Geben Sie Ihrem **Schritt** und **Zweig** einen freundlichen Namen, dann klicken Sie auf **Fehler hinzufügen**.
![Versuchsplaner](images/tutorial-aks-add-designer.png)
4. Wählen Sie **AKS Chaos Mesh Pod Chaos** aus dem Dropdown-Menü, geben Sie dann in **Duration** die Anzahl der Minuten ein, die der Ausfall dauern soll, und in **jsonSpec** die unten stehenden Informationen:

    Zur Formulierung Ihres Chaos Mesh jsonSpec:
    1. Besuchen Sie die Chaos Mesh-Dokumentation für einen Fehlertyp, [zum Beispiel den PodChaos-Typ](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files).
    2. Formulieren Sie die YAML-Konfiguration für diesen Fehlertyp anhand der Chaos Mesh-Dokumentation.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: one
          duration: '30s'
          selector:
            namespaces:
              - default
        ```
    3. Entfernen Sie alles YAML außerhalb der `spec` (einschließlich des Eigenschaftsnamens der Spezifikation), und entfernen Sie die Einrückung der Spezifikationsdetails.

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. Verwenden Sie einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm), um die Chaos Mesh YAML in JSON zu konvertieren und zu minimieren.

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. Fügen Sie das minimierte JSON in das Feld **json** im Portal ein.




Klicken Sie auf **Weiter: Zielressourcen >** 
![Fehlereigenschaften](images/tutorial-aks-add-fault.png)
5. Wählen Sie Ihren AKS-Cluster und klicken Sie auf **Weiter**
![Ziel hinzufügen](images/tutorial-aks-add-targets.png)
6. Überprüfen Sie, ob Ihr Experiment korrekt aussieht, und klicken Sie dann auf **Überprüfen + Erstellen**, dann **Erstellen.** 
![Überprüfen und Experiment erstellen](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Erteilen Sie Ihrem AKS-Cluster eine Experimentiergenehmigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann.

1. Navigieren Sie zu Ihrem AKS-Cluster und klicken Sie auf **Zugangskontrolle (IAM)** .
![AKS-Übersichtsseite](images/tutorial-aks-access-resource.png)
2. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.
![Übersicht über die Zugriffssteuerung](images/tutorial-aks-access-iam.png)
3. Suchen Sie nach **Azure Kubernetes Service Cluster Admin Role** und wählen Sie die Rolle. Klicken Sie auf **Weiter**
![Zuweisung der AKS-Cluster-Admin-Rolle](images/tutorial-aks-access-role.png)
4. Klicken Sie auf **Mitglieder auswählen** und suchen Sie nach Ihrem Experimentnamen. Wählen Sie Ihr Experiment und klicken Sie auf **Auswählen**. Wenn mehrere Experimente mit demselben Namen im selben Mieter vorhanden sind, wird der Name Ihres Experiments mit zufälligen Zeichen gekürzt.
![Hinzufügen eines Experiments zur Rolle](images/tutorial-aks-access-experiment.png)
5. Klicken Sie auf **Überprüfen + Zuweisen** und dann auf **Überprüfen + Zuweisen**.

## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir Ihnen, Ihre AKS-Clusterübersicht zu öffnen und **Insights** in einem separaten Browser-Tab aufzurufen. Live-Daten für die **Active Pod Count** zeigen die Auswirkungen der Durchführung Ihres Experiments.

1. Klicken Sie in der Ansicht **Experimente** auf Ihr Experiment, und klicken Sie auf **Start** und dann auf **OK**.
![Beginn eines Experiments](images/tutorial-aks-start.png)
2. Wenn der **Status** auf **Laufend** wechselt, klicken Sie auf **Details** für den letzten Lauf unter **Historie**, um Details zum laufenden Experiment zu sehen.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun ein AKS-Chaos-Mesh-Service-Direkt-Experiment durchgeführt haben, sind Sie bereit:
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)
