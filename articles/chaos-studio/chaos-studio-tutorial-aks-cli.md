---
title: Erstellen eines Experiments mit der Azure CLI, das einen AKS Chaos Mesh-Fehler mit Azure Chaos Studio verwendet
description: Erstellen eines Experiments, das einen AKS Chaos Mesh-Fehler mit der Azure CLI verwendet
author: johnkemnetz
ms.topic: how-to
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c47a6fa58a9361dc9ab26e119346951e1eef9764
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373487"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-with-the-azure-cli"></a>Erstellen eines Chaosexperiments, das einen Chaos Mesh-Fehler mit der Azure CLI verwendet

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie periodische Ausfälle von Azure Kubernetes Service-Pods in einem Namespace mit Hilfe eines Chaos-Experiments und Azure Chaos Studio verursachen. Die Durchführung dieses Experiments kann Ihnen helfen, sich gegen die Nichtverfügbarkeit von Diensten bei sporadischen Ausfällen zu schützen.

Azure Chaos Studio verwendet [Chaos Mesh](https://chaos-mesh.org/), eine kostenlose Open-Source-Chaos-Engineering-Plattform für Kubernetes, um Fehler in einen AKS-Cluster zu injizieren. Chaos Mesh-Fehler sind [service-direkte](chaos-studio-tutorial-aks-portal.md) Fehler, für die Chaos Mesh auf dem AKS-Cluster installiert sein muss. Mit den gleichen Schritten können Sie ein Experiment für einen beliebigen AKS-Chaos-Mesh-Fehler einrichten und durchführen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Ein AKS-Cluster. Wenn Sie noch keinen AKS-Cluster haben, können Sie [mit den folgenden Schritten einen erstellen](../aks/kubernetes-walkthrough-portal.md).

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie die Befehlszeilenschnittstelle lieber lokal installieren und verwenden möchten, benötigen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.30. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Chaos Mesh auf Ihrem AKS-Cluster einrichten

Bevor Sie Chaos Mesh-Fehler in Chaos Studio ausführen können, müssen Sie Chaos Mesh auf Ihrem AKS-Cluster installieren.

1. Führen Sie die folgenden Befehle in einem [Azure Cloud Shell](../cloud-shell/overview.md)-Fenster aus, in dem Sie als aktives Abonnement das Abonnement eingestellt haben, in dem Ihr AKS-Cluster bereitgestellt wird. Ersetzen Sie `$RESOURCE_GROUP` und `$CLUSTER_NAME` durch die Ressourcengruppe und den Namen Ihrer Clusterressource.

   ```azurecli-interactive
   az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   kubectl create ns chaos-testing
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
   ```

2. Überprüfen Sie, ob die Chaos Mesh-Pods installiert sind, indem Sie den folgenden Befehl ausführen:

   ```azurecli-interactive
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

1. Erstellen Sie ein Ziel, indem Sie `$RESOURCE_ID` durch die Ressourcen-ID des AKS-Clusters ersetzen, für den Sie Onboarding durchführen:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Erstellen Sie die Funktionen auf dem Ziel, indem Sie `$RESOURCE_ID` durch die Ressourcen-ID des AKS-Clusters, für den Sie Onboarding durchführen, und `$CAPABILITY` durch den [Namen der Fehlerfunktion ersetzen, die Sie aktivieren](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/$CAPABILITY?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Wenn Sie z. B. die PodChaos-Funktion aktivieren:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/PodChaos-2.1?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Dies muss für jede Funktion erfolgen, die Sie im Cluster aktivieren möchten.

Sie haben nun Ihren AKS-Cluster erfolgreich in Chaos Studio eingebunden.

## <a name="create-an-experiment"></a>Erstellen eines Experiments
Wenn Ihr AKS-Cluster nun an Bord ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Erstellen Sie eine Chaos Mesh-JSON-Spezifikation:
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
          mode: all
          duration: '600s'
          selector:
            namespaces:
              - default
        ```
    3. Entfernen Sie alles YAML außerhalb der `spec` (einschließlich des Eigenschaftsnamens der Spezifikation), und entfernen Sie die Einrückung der Spezifikationsdetails.

        ```yaml
        action: pod-failure
        mode: all
        duration: '600s'
        selector:
          namespaces:
            - default
        ```
    4. Verwenden Sie einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm), um die Chaos Mesh YAML in JSON zu konvertieren und zu minimieren.

        ```json
        {"action":"pod-failure","mode":"all","duration":"600s","selector":{"namespaces":["default"]}}
        ```
    5. Verwenden Sie ein [JSON-Zeichenfolgenescapetool wie dieses](https://www.freeformatter.com/json-escape.html), um die JSON-Spezifikation mit Escapezeichen zu versehen.
    
        ```json
        {\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}
        ```

2. Erstellen Sie Ihren JSON-Experimentcode, beginnend mit dem folgenden JSON-Beispiel. Ändern Sie den JSON-Code so, dass er dem Experiment entspricht, das Sie mithilfe der [API zum Erstellen von Experimenten](/rest/api/chaosstudio/experiments/create-or-update), der [Fehlerbibliothek](chaos-studio-fault-library.md) und der im vorherigen Schritt erstellten JSON-Spezifikation ausführen möchten.

    ```json
    {
      "location": "centralus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "AKS pod kill",
            "branches": [
              {
                "name": "AKS pod kill",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                          "key": "jsonSpec",
                          "value": "{\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}"
                      }
                    ],
                    "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Erstellen Sie das Experiment mithilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihr Experiment. Stellen Sie sicher, dass Sie den JSON-Experimentcode gespeichert und hochgeladen haben, und aktualisieren Sie `experiment.json` mit Ihrem JSON-Dateinamen.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Jedes Experiment erstellt eine entsprechende systemseitig zugewiesene verwaltete Identität. Notieren Sie sich die `principalId` für diese Identität in der Antwort für den nächsten Schritt.

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Erteilen Sie Ihrem AKS-Cluster eine Experimentiergenehmigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann.

Gewähren Sie dem Experiment mithilfe des folgenden Befehls Zugriff auf Ihre Ressourcen, und ersetzen Sie dabei `$EXPERIMENT_PRINCIPAL_ID` durch die principalId aus dem vorherigen Schritt und `$RESOURCE_ID` durch die Ressourcen-ID der Zielressource (in diesem Fall die Ressourcen-ID des AKS-Clusters). Führen Sie diesen Befehl für jede Ressource aus, die in Ihrem Experiment als Ziel verwendet wird. 

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Cluster User Role" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir Ihnen, Ihre AKS-Clusterübersicht zu öffnen und **Insights** in einem separaten Browser-Tab aufzurufen. Live-Daten für die **Active Pod Count** zeigen die Auswirkungen der Durchführung Ihres Experiments.

1. Starten Sie das Experiment mithilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihr Experiment.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. Die Antwort enthält eine Status-URL, mit der Sie den Experimentstatus während der Ausführung des Experiments abfragen können.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun ein AKS-Chaos-Mesh-Service-Direkt-Experiment durchgeführt haben, sind Sie bereit:
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)
