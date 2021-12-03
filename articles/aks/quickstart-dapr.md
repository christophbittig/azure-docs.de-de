---
title: Bereitstellen einer Anwendung mit der Dapr-Clustererweiterung (Vorschau) für Azure Kubernetes Service (AKS)
description: Verwenden der Dapr-Clustererweiterung (Vorschau) für Azure Kubernetes Service (AKS) zum Bereitstellen einer Anwendung
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: quickstart
ms.date: 11/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1590bcc48260e4b6729cd12cb80b047ba9fe4acb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479732"
---
# <a name="quickstart-deploy-an-application-using-the-dapr-cluster-extension-preview-for-azure-kubernetes-service-aks"></a>Schnellstart: Bereitstellen einer Anwendung mithilfe der Dapr-Clustererweiterung (Vorschau) für Azure Kubernetes Service (AKS)

In dieser Schnellstartanleitung machen Sie sich mit der Verwendung der [Dapr-Clustererweiterung][dapr-overview] in einem AKS-Cluster vertraut. Sie stellen ein Hello World-Beispiel bereit, das aus einer Python-Anwendung, die Nachrichten generiert, und einer Node-Anwendung besteht, die diese nutzt und dauerhaft speichert.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Die [Azure CLI](/cli/azure/install-azure-cli) muss installiert sein.
* Ein AKS-Cluster mit aktivierter [Dapr-Clustererweiterung][dapr-overview]

## <a name="clone-the-repository"></a>Klonen des Repositorys

Wenn Sie die Dateien, die Sie zum Bereitstellen der Beispielanwendung verwenden werden, abrufen möchten, klonen Sie das [Schnellstartrepository][hello-world-gh], und wechseln Sie in das Verzeichnis `hello-kubernetes`:

```bash
git clone https://github.com/dapr/quickstarts.git
cd quickstarts/hello-kubernetes
```

## <a name="create-and-configure-a-state-store"></a>Erstellen und Konfigurieren eines Zustandsspeichers

Dapr kann verschiedene Zustandsspeicher (Redis, CosmosDB, DynamoDB, Cassandra usw.) verwenden, um den Zustand dauerhaft zu speichern und abzurufen. In diesem Beispiel wird Redis verwendet.

### <a name="create-a-redis-store"></a>Erstellen eines Redis-Speichers

1. Öffnen Sie das [Azure-Portal][azure-portal-cache], um den Azure Redis Cache-Erstellungsflow zu starten.
2. Füllen Sie die erforderlichen Informationen aus.
3. Klicken Sie auf „Erstellen“, um die Bereitstellung Ihrer Redis-Instanz zu starten.
4. Notieren Sie sich den Hostnamen Ihrer Redis-Instanz. Diesen können Sie in Azure unter „Übersicht“ abrufen. Er sollte wie folgt aussehen: `xxxxxx.redis.cache.windows.net:6380`.
5. Nachdem Ihre Instanz erstellt wurde, müssen Sie Ihren Zugriffsschlüssel abrufen. Navigieren Sie unter „Einstellungen“ zu „Zugriffsschlüssel“, und erstellen Sie ein Kubernetes-Geheimnis zum Speichern Ihres Redis-Kennworts:

```bash
kubectl create secret generic redis --from-literal=redis-password=<your-redis-password>
```

### <a name="configure-the-dapr-components"></a>Konfigurieren der Dapr-Komponenten

Nach dem Erstellen Ihres Speichers müssen Sie die Schlüssel der Datei „redis.yaml“ im Bereitstellungsverzeichnis des Hallo Welt-Repositorys hinzufügen. Ersetzen Sie den Wert `redisHost` durch Ihre eigene Redis-Masteradresse und den Wert `redisPassword` durch Ihr eigenes Geheimnis. Weitere Informationen erhalten Sie [hier][dapr-component-secrets].

Sie müssen außerdem die folgenden beiden Zeilen unterhalb von `redisPassword` hinzufügen, um die Verbindung über TLS zu aktivieren:

```yml
- name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
- name: enableTLS
  value: true
```

### <a name="apply-the-configuration"></a>Anwenden der Konfiguration

Wenden Sie die Datei `redis.yaml` an:

```bash
kubectl apply -f ./deploy/redis.yaml
``` 

Vergewissern Sie sich außerdem in der Ausgabe, dass Ihr Zustandsspeicher erfolgreich konfiguriert wurde:

```bash
component.dapr.io/statestore created
```

## <a name="deploy-the-nodejs-app-with-the-dapr-sidecar"></a>Bereitstellen der Node.js-App mit dem Dapr-Sidecar

Wenden Sie die Bereitstellung der Node.js-App auf Ihren Cluster an:

```bash
kubectl apply -f ./deploy/node.yaml
```

> [!NOTE]
> Kubernetes-Bereitstellungen sind asynchron. Das bedeutet, dass Sie warten müssen, bis die Bereitstellung abgeschlossen ist, bevor Sie mit den nächsten Schritten fortfahren. Dazu können Sie den folgenden Befehl verwenden:
> ```bash
> kubectl rollout status deploy/nodeapp
> ```

Dadurch wird die Node.js-App in Kubernetes bereitgestellt. Die Dapr-Steuerungsebene fügt den Dapr-Sidecar automatisch in den Pod ein. In der Datei `node.yaml` sehen Sie, wie Dapr für diese Bereitstellung aktiviert wird:

* `dapr.io/enabled: true`: Damit wird die Dapr-Steuerungsebene angewiesen, einen Sidecar in diese Bereitstellung einzufügen.

* `dapr.io/app-id: nodeapp`: Hiermit wird der Dapr-Anwendung eine eindeutige ID oder ein eindeutiger Name zugewiesen, damit sie von anderen Dapr-Anwendungen Nachrichten erhalten und mit ihnen kommunizieren kann.

Rufen Sie zum Zugreifen auf Ihren Dienst die externe IP-Adresse (`EXTERNAL-IP`) über `kubectl` ab, und notieren Sie sie:

```bash
kubectl get svc nodeapp
```

### <a name="verify-the-service"></a>Überprüfen des Diensts

Führen Sie zum Aufrufen des Diensts Folgendes aus:

```bash
curl $EXTERNAL_IP/ports
```

Die Ausgabe sollte etwa folgendermaßen aussehen:

```bash
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}
```

Übermitteln Sie als Nächstes einen Auftrag an die Anwendung:

```bash
curl --request POST --data "@sample.json" --header Content-Type:application/json $EXTERNAL_IP/neworder
```

Vergewissern Sie sich, dass der Auftrag dauerhaft gespeichert wurde, indem Sie ihn anfordern:

```bash
curl $EXTERNAL_IP/order
```

Die Ausgabe sollte etwa folgendermaßen aussehen:

```bash
{ "orderId": "42" }
```

> [!TIP]
> Dies ist ein guter Zeitpunkt, um sich mit dem Dapr-Dashboard vertraut zu machen– einer praktischen Benutzeroberfläche zum Überprüfen des Status, der Informationen und Protokolle der in Dapr ausgeführten Anwendungen. Mit dem folgenden Befehl wird es unter `http://localhost:8080/` verfügbar gemacht:
> ```bash
> kubectl port-forward svc/dapr-dashboard -n dapr-system 8080:8080
> ```

## <a name="deploy-the-python-app-with-the-dapr-sidecar"></a>Bereitstellen der Python-App mit dem Dapr-Sidecar

Sehen Sie sich die Python-App kurz an. Navigieren Sie in der Schnellstartanleitung `hello-kubernetes` zum Python-App-Verzeichnis, und öffnen Sie `app.py`.

Dies ist eine einfache Python-App, die JSON-Nachrichten an `localhost:3500` sendet. Dabei handelt es sich um den standardmäßigen Lauschport für Dapr. Sie können den Endpunkt `neworder` der Node.js-Anwendung durch Posten in `v1.0/invoke/nodeapp/method/neworder` aufrufen. Die Nachricht enthält einige Daten mit einer Auftrags-ID (`orderId`), die sich einmal pro Sekunde erhöht:

```python
n = 0
while True:
    n += 1
    message = {"data": {"orderId": n}}

    try:
        response = requests.post(dapr_url, json=message)
    except Exception as e:
        print(e)

    time.sleep(1)
```

Stellen Sie die Python-App in Ihrem Kubernetes-Cluster bereit:

```bash
kubectl apply -f ./deploy/python.yaml
```

> [!NOTE]
> Wie weiter oben wartet der folgende Befehl, bis die Bereitstellung abgeschlossen ist:
> ```bash
> kubectl rollout status deploy/pythonapp
> ```

## <a name="observe-messages-and-confirm-persistence"></a>Beobachten von Nachrichten und Bestätigen der Persistenz

Nachdem nun sowohl die Node.js- als auch die Python-Anwendung bereitgestellt wurden, sehen Sie, wie Nachrichten eingehen.

Rufen Sie die Protokolle der Node.js-App ab:

```bash
kubectl logs --selector=app=node -c node --tail=-1
```

Wenn die Bereitstellungen erfolgreich waren, sollten Protokolle wie die folgenden angezeigt werden:

```bash
Got a new order! Order ID: 1
Successfully persisted state
Got a new order! Order ID: 2
Successfully persisted state
Got a new order! Order ID: 3
Successfully persisted state
```

Rufen Sie den Auftragsendpunkt der Node.js-App auf, um den neuesten Auftrag zu erhalten. Fügen Sie an die zuvor gespeicherte externe IP-Adresse „/order“ an, und führen Sie dafür eine GET-Anforderung aus. (Geben Sie sie in den Browser ein, oder verwenden Sie Postman oder `curl`.)

```bash
curl $EXTERNAL_IP/order
{"orderID":"42"}
```

In der Antwort sollte der neueste JSON-Code angezeigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den Befehl [az group delete][az-group-delete] aus, um die Ressourcengruppe, den AKS-Cluster, den Namespace und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie nach erfolgreicher Bereitstellung dieser Beispielanwendung den folgenden Artikel:
> [!div class="nextstepaction"]
> [Bereitstellung und Verwaltung von Cluster-Erweiterungen für Azure Kubernetes Service (AKS) (Vorschau)][cluster-extensions]

<!-- LINKS -->
<!-- INTERNAL -->
[cluster-extensions]: ./cluster-extensions.md
[dapr-overview]: ./dapr.md
[az-group-delete]: /cli/azure/group#az_group_delete

<!-- EXTERNAL -->
[hello-world-gh]: https://github.com/dapr/quickstarts/tree/v1.4.0/hello-kubernetes
[azure-portal-cache]: https://ms.portal.azure.com/#create/Microsoft.Cache
[dapr-component-secrets]: https://docs.dapr.io/operations/components/component-secrets/