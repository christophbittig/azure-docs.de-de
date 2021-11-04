---
title: Bereitstellen eines Containers für die Schlüsselbegriffserkennung in Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Stellen Sie ein Containerimage für die Schlüsselbegriffserkennung in Azure Kubernetes Service bereit, und testen Sie es in einem Webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a71910bde7718c0a60eda87ea51f6c49e473304a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095418"
---
# <a name="deploy-a-key-phrase-extraction-container-to-azure-kubernetes-service"></a>Bereitstellen eines Containers für die Schlüsselbegriffserkennung in Azure Kubernetes Service

Erfahren Sie, wie Sie einen [Docker-Container für die Schlüsselbegriffserkennung](../key-phrase-extraction/how-to/use-containers.md) in Azure Kubernetes Service (AKS) bereitstellen. In diesem Verfahren wird gezeigt, wie Sie eine Language-Ressource erstellen, ein Containerimage zuordnen und die Orchestrierung der beiden über einen Browser durchführen. Die Verwendung von Containern kann Ihre Aufmerksamkeit von der Verwaltung der Infrastruktur auf die Anwendungsentwicklung lenken. In diesem Artikel wird der Prozess zwar beispielhaft für einen Container für die Schlüsselbegriffserkennung dargestellt, Sie können ihn jedoch auch für andere Container verwenden, die von Azure Cognitive Service für Language angeboten werden.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Verfahren müssen mehrere Tools lokal installiert und ausgeführt werden. Verwenden Sie nicht Azure Cloud Shell. Sie benötigen Folgendes:

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) erstellen, bevor Sie beginnen.
* Einen Text-Editor, z.B. [Visual Studio Code](https://code.visualstudio.com/download).
* Die [Azure CLI](/cli/azure/install-azure-cli) muss installiert sein.
* Die [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) muss installiert sein.
* Eine Azure-Ressource mit dem korrekten Tarif. Nicht alle Tarife können mit diesem Container verwendet werden:
    * **Azure Language**-Ressource nur mit F0- oder Standard-Tarif
    * Eine **Azure Cognitive Services**-Ressource im S0-Tarif.

[!INCLUDE [Create a Cognitive Services Language resource](../includes/containers/create-text-analytics-resource.md)]

[!INCLUDE [Create a language container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]


## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Bereitstellen des Containers für die Schlüsselbegriffserkennung für einen AKS-Cluster

1. Öffnen Sie die Azure CLI, und melden Sie sich bei Azure an.

    ```azurecli
    az login
    ```

1. Melden Sie sich bei dem AKS-Cluster an. Ersetzen Sie `your-cluster-name` und `your-resource-group` durch die entsprechenden Werte.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Nachdem dieser Befehl ausgeführt wurde, wird eine Meldung ähnlich der folgenden ausgegeben:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Wenn Sie über mehrere Abonnements in Ihrem Azure-Konto verfügen und der `az aks get-credentials`-Befehl mit einem Fehler zurückgegeben wird, ist ein häufiges Problem, dass Sie das falsche Abonnement verwenden. Stellen Sie den Kontext Ihrer Azure CLI-Sitzung so ein, dass Sie das gleiche Abonnement verwenden, mit dem Sie die Ressourcen erstellt haben, und versuchen Sie es erneut.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öffnen Sie den Text-Editor Ihrer Wahl. In diesem Beispiel wird Visual Studio Code verwendet.

    ```console
    code .
    ```

1. Erstellen Sie im Text-Editor eine neue Datei namens *keyphrase.yaml*, und fügen Sie die folgende YAML darin ein. Achten Sie darauf, dass Sie `billing/value` und `apikey/value` durch Ihre eigenen Werte ersetzen.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Speichern Sie die Datei, und schließen Sie den Text-Editor.
1. Führen Sie den Kubernetes-Befehl `apply` mit der Datei *keyphrase.yaml* als Ziel aus:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Nachdem der Befehl die Bereitstellungskonfiguration erfolgreich angewendet hat, wird eine Meldung ähnlich der folgenden Ausgabe angezeigt:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Überprüfen Sie, ob der Pod bereitgestellt wurde:

    ```console
    kubectl get pods
    ```

    Die Ausgabe für den Ausführungsstatus des Pods:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Überprüfen Sie, ob der Dienst verfügbar ist, und rufen Sie die IP-Adresse ab.

    ```console
    kubectl get services
    ```

    Die Ausgabe für den Ausführungsstatus des *keyphrase*-Diensts im Pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

## <a name="verify-the-key-phrase-extraction-container-instance"></a>Überprüfen der Containerinstanz für die Schlüsselbegriffserkennung

1. Wählen Sie die Registerkarte **Übersicht** aus, und kopieren Sie die IP-Adresse.
1. Öffnen Sie eine neue Registerkarte im Browser, und geben Sie die IP-Adresse ein. Geben Sie zum Beispiel `http://<IP-address>:5000 (http://55.55.55.55:5000`) ein. Die Homepage des Containers wird angezeigt, in der Sie erkennen, dass der Container ausgeführt wird.

    ![Anzeigen der Homepage des Containers, um sicherzustellen, dass er ausgeführt wird](../media/swagger-container-documentation.png)

1. Klicken Sie auf den Link **Dienst-API-Beschreibung**, um die Swagger-Seite des Containers aufzurufen.

1. Wählen Sie eine der **POST**-APIs und dann **Jetzt ausprobieren** aus. Die Parameter werden angezeigt, einschließlich der folgenden Beispieleingabe:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Ersetzen Sie die Eingabe durch folgenden JSON-Code:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Legen Sie **showStats** auf `true` fest.

1. Wählen Sie **Ausführen** aus, um die Stimmung des Texts zu bestimmen.

    Das im Container enthaltene Modell erzeugt einen Bewertungsbereich von 0 bis 1, wobei 0 negativ und 1 positiv ist.

    Die zurückgegebene JSON-Antwort enthält die Stimmung für die aktualisierte Texteingabe:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Das Dokument `id` der in der Antwort enthaltenen JSON-Daten kann nun mit dem ursprünglichen Dokument `id` in Korrelation gebracht werden. Das resultierende Dokument weist ein `keyPhrases`-Array auf, das die Liste der Schlüsselbegriffe enthält, die aus dem entsprechenden Eingabedokument extrahiert wurden. Zusätzlich gibt es verschiedene Statistiken wie `characterCount` und `transactionCount` für jedes resultierende Dokument.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden weiterer [Cognitive Services-Container](../../cognitive-services-container-support.md)
* [Übersicht über die Schlüsselbegriffserkennung](../overview.md)
