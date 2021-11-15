---
title: 'Tutorial: Bereitstellen einer Dapr-Anwendung in Azure Container Apps mithilfe einer ARM-Vorlage'
description: Bereitstellen einer Dapr-Anwendung in Azure Container Apps mithilfe einer ARM-Vorlage
services: container-apps
author: asw101
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aawislan
ms.custom: ignite-fall-2021
ms.openlocfilehash: d38af387910551903d2781863deb3de3fcd36160
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026917"
---
# <a name="tutorial-deploy-a-dapr-application-to-azure-container-apps-using-an-arm-template"></a>Tutorial: Bereitstellen einer Dapr-Anwendung in Azure Container Apps mithilfe einer ARM-Vorlage

[Dapr](https://dapr.io/) (Distributed Application Runtime) ist eine Runtime, mit der resiliente, zustandslose und zustandsreiche Microservices erstellt werden können. In diesem Tutorial wird eine Dapr-Beispielanwendung in Azure Container Apps bereitgestellt.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Container Apps-Umgebung für Ihre Container-Apps
> * Erstellen eines Azure Blob Storage Zustandsspeichers für die Container-App
> * Stellen Sie zwei Apps bereit, die Nachrichten erzeugen und nutzen, und speichern Sie sie mithilfe des Zustandsspeichers.
> * Überprüfen Sie die Interaktion zwischen den beiden Microservices.

Azure Container Apps bietet eine vollständig verwaltete Version der Dapr-APIs beim Erstellen von Microservices. Wenn Sie Dapr in Azure Container Apps verwenden, können Sie Sidecars für die Ausführung neben Ihren Microservices aktivieren, die einen umfangreichen Satz von Funktionen bieten. Zu den verfügbaren Dapr-APIs gehören [Dienst-zu-Dienst-Aufrufe](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/), [Pub/Sub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/), [Ereignisbindungen](https://docs.dapr.io/developing-applications/building-blocks/bindings/), [Zustandsspeicher](https://docs.dapr.io/developing-applications/building-blocks/state-management/) und [Akteure.](https://docs.dapr.io/developing-applications/building-blocks/actors/)

In diesem Tutorial stellen Sie die gleichen Anwendungen aus dem Dapr-[Hallo Welt](https://github.com/dapr/quickstarts/tree/master/hello-kubernetes) Schnellstart bereit, der aus einer Client-App (Python) besteht, die Nachrichten generiert, und einer Dienst-App (Node), die diese Nachrichten in einem konfigurierten Zustandsspeicher nutzt und speichert. Das folgende Architekturdiagramm veranschaulicht die Komponenten, aus denen sich dieses Tutorial zusammensetzt:

:::image type="content" source="media/microservices-dapr/azure-container-apps-microservices-dapr.png" alt-text="Architekturdiagramm für Dapr Hallo Welt-Microservices in Azure Container Apps":::

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Vorbereitung

In diesem Leitfaden werden die folgenden Umgebungsvariablen verwendet:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
$STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

---

Der obige Codeausschnitt kann verwendet werden, um die Umgebungsvariablen mit bash, zsh oder PowerShell festzulegen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<storage account name>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

Wählen Sie einen Namen für `STORAGE_ACCOUNT` aus. Der Hostname wird in einem späteren Schritt erstellt. Speicherkontonamen müssen *innerhalb von Azure eindeutig* und zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.

## <a name="setup"></a>Einrichten

Beginnen Sie mit der Anmeldung bei Azure über die Befehlszeilenschnittstelle.

Führen Sie den folgenden Befehl aus, und befolgen Sie die Anweisungen, um den Authentifizierungsprozess abzuschließen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

Stellen Sie sicher, dass Sie die neueste Version der CLI über den Upgradebefehl ausführen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az upgrade
```

---

Installieren Sie als Nächstes die Azure Container Apps-Erweiterung in der CLI.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl 
```

---

Die Erweiterung wurde installiert. Registrieren Sie nun den Namespace `Microsoft.Web`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az provider register --namespace Microsoft.Web
```

---

Erstellen Sie eine Ressourcengruppe, um die Dienste im Zusammenhang mit Ihrer neuen Container-App zu organisieren.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group create `
  --name $RESOURCE_GROUP `
  --location "$LOCATION"
```

---

Wenn die CLI aktualisiert wurde und eine neue Ressourcengruppe verfügbar ist, können Sie eine Container Apps-Umgebung erstellen und Ihre Container-App bereitstellen.

## <a name="create-an-environment"></a>Erstellen einer Umgebung

Azure Container Apps-Umgebungen fungieren als Isolationsgrenzen zwischen einer Gruppe von Container-Apps. Container-Apps, die in derselben Umgebung bereitgestellt werden, werden im gleichen virtuellen Netzwerk bereitgestellt und schreiben Protokolle in denselben Log Analytics-Arbeitsbereich.

Azure Log Analytics wird zum Überwachen Ihrer Container-App verwendet und ist beim Erstellen einer Container Apps-Umgebung erforderlich.

Erstellen Sie mit dem folgenden Befehl einen neuen Log Analytics-Arbeitsbereich:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

Rufen Sie als Nächstes die Log Analytics-Client-ID und den geheimen Clientschlüssel ab.

# <a name="bash"></a>[Bash](#tab/bash)

Stellen Sie sicher, dass Sie jede Abfrage separat ausführen, um genügend Zeit für den Abschluss der Anforderung zu geben.

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Stellen Sie sicher, dass Sie jede Abfrage separat ausführen, um genügend Zeit für den Abschluss der Anforderung zu geben.

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

---

Einzelne Container-Apps werden in einer Azure Container Apps-Umgebung bereitgestellt. Führen Sie den folgenden Befehl aus, um die Umgebung zu erstellen:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location "$LOCATION"
```

---

## <a name="set-up-a-state-store"></a>Einrichten eines Zustandsspeichers

### <a name="create-an-azure-blob-storage-account"></a>Erstellen eines Azure Blob Storage-Kontos

Verwenden Sie den folgenden Befehl, um ein neues Azure-Speicherkonto zu erstellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location "$LOCATION" `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Sobald Ihr Azure Blob-Speicherkonto erstellt wurde, werden die folgenden Werte für die nachfolgenden Schritte in diesem Tutorial benötigt.

* `storage_account_name` ist der Wert der Variable `STORAGE_ACCOUNT`, die Sie oben ausgewählt haben.

* `storage_container_name` ist der oben definierte Wert von `STORAGE_ACCOUNT_CONTAINER` (z. B. `mycontainer`). Dapr erstellt einen Container mit diesem Namen, wenn er noch nicht in Ihrem Azure-Speicherkonto vorhanden ist.

Verwenden Sie den folgenden Befehl, um den Speicherkontoschlüssel abzurufen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_KEY=`az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_KEY=(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv)
```

---

### <a name="create-azure-resource-manager-arm-templates"></a>Azure Resource Manager-Vorlagen erstellen

Zwei Azure Resource Manager-Vorlagen erstellen

Die ARM-Vorlage verfügt über die Container-App-Definition und eine Dapr-Komponentendefinition.

Das folgende Beispiel zeigt, wie Ihre ARM-Vorlage aussehen sollte, wenn sie für Ihr Azure Blob-Speicherkonto konfiguriert ist.

Speichern Sie die folgende Datei als *serviceapp.json*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "type": "String"
        },
        "storage_account_name": {
            "type": "String"
        },
        "storage_account_key": {
            "type": "String"
        },
        "storage_container_name": {
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "nodeapp",
            "type": "Microsoft.Web/containerApps",
            "apiVersion": "2021-03-01",
            "kind": "containerapp",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "ingress": {
                        "external": true,
                        "targetPort": 3000
                    },
                    "secrets": [
                        {
                            "name": "storage-key",
                            "value": "[parameters('storage_account_key')]"
                        }
                    ]
                },
                "template": {
                    "containers": [
                        {
                            "image": "dapriosamples/hello-k8s-node:latest",
                            "name": "hello-k8s-node",
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 1
                    },
                    "dapr": {
                        "enabled": true,
                        "appPort": 3000,
                        "appId": "nodeapp",
                        "components": [
                            {
                                "name": "statestore",
                                "type": "state.azure.blobstorage",
                                "version": "v1",
                                "metadata": [
                                    {
                                        "name": "accountName",
                                        "value": "[parameters('storage_account_name')]"
                                    },
                                    {
                                        "name": "accountKey",
                                        "secretRef": "storage-key"
                                    },
                                    {
                                        "name": "containerName",
                                        "value": "[parameters('storage_container_name')]"
                                    }
                                ]
                            }
                        ]
                    }
                }
            }
        }
    ]
}
```

> [!NOTE]
> Container Apps unterstützt derzeit nicht das systemeigene [Dapr-Komponentenschema](https://docs.dapr.io/operations/components/component-schema/). Im obigen Beispiel wird das unterstützte Schema verwendet.
>
> Befolgen Sie in einer Produktionsanwendung die Anweisungen zur [Geheimnisverwaltung](https://docs.dapr.io/operations/components/component-secrets), um Ihre Geheimnisse sicher zu verwalten.

Speichern Sie die folgende Datei als *clientapp.json*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "pythonapp",
            "type": "Microsoft.Web/containerApps",
            "apiVersion": "2021-03-01",
            "kind": "containerapp",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {},
                "template": {
                    "containers": [
                        {
                            "image": "dapriosamples/hello-k8s-python:latest",
                            "name": "hello-k8s-python",
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 1
                    },
                    "dapr": {
                        "enabled": true,
                        "appId": "pythonapp"
                    }
                }
            }
        }
    ]
}
```

## <a name="deploy-the-service-application-http-web-server"></a>Bereitstellen der Dienstanwendung (HTTP-Webserver)

Navigieren Sie zu dem Verzeichnis, in dem Sie die ARM-Vorlagendatei gespeichert haben, und führen Sie den folgenden Befehl aus, um die Dienstcontainer-App bereitzustellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create \
  --resource-group "$RESOURCE_GROUP" \
  --template-file ./serviceapp.json \
  --parameters \
      environment_name="$CONTAINERAPPS_ENVIRONMENT" \
      location="$LOCATION" \
      storage_account_name="$STORAGE_ACCOUNT" \
      storage_account_key="$STORAGE_ACCOUNT_KEY" \
      storage_container_name="$STORAGE_ACCOUNT_CONTAINER"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az deployment group create `
  --resource-group "$RESOURCE_GROUP" `
  --template-file ./serviceapp.json `
  --parameters `
      environment_name="$CONTAINERAPPS_ENVIRONMENT" `
      location="$LOCATION" `
      storage_account_name="$STORAGE_ACCOUNT" `
      storage_account_key="$STORAGE_ACCOUNT_KEY" `
      storage_container_name="$STORAGE_ACCOUNT_CONTAINER"
```

---

Dieser Befehl stellt den Dienst-App-Server (Knoten) auf `targetPort: 3000` (Port der App) zusammen mit dem zugehörigen Dapr-Sidecar bereit, der mit `"appId": "nodeapp",` und dapr `"appPort": 3000,` für die Dienstermittlung und den Aufruf konfiguriert ist. Ihr Zustandsspeicher wird mithilfe des Objekts `components` von `"type": "state.azure.blobstorage"` konfiguriert, wodurch der Sidecar den Zustand beibehalten kann.

## <a name="deploy-the-client-application-headless-client"></a>Bereitstellen der Clientanwendung (Kopfloser Client)

Führen Sie den folgenden Befehl aus, um die Clientcontainer-App bereitzustellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./clientapp.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./clientapp.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    location="$LOCATION"
```

---

Dieser Befehl stellt `pythonapp` bereit, der auch mit einem Dapr-Sidecar ausgeführt wird, der zum Suchen und sicheren Aufrufen des Dapr-Sidecars für verwendet `nodeapp` wird. Da diese App kopflos ist, gibt es weder `targetPort` das Starten eines Servers noch die Notwendigkeit, den Eingangsdatenlauf zu aktivieren.

## <a name="verify-the-result"></a>Überprüfen des Ergebnisses

### <a name="confirm-successful-state-persistence"></a>Bestätigen der erfolgreichen Zustandspersistenz

Sie können überprüfen, ob die Dienste ordnungsgemäß funktionieren, indem Sie Daten in Ihrem Azure-Speicherkonto anzeigen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Ihrem Browser, und navigieren Sie zu Ihrem Speicherkonto.

1. Klicken Sie auf der linken Seite auf **Container**.

1. Wählen Sie **MyContainer**.

1. Vergewissern Sie sich, dass die Datei mit dem Namen `order` im Container angezeigt wird.

1. Klicken Sie auf die Datei.

1. Klicken Sie auf die Registerkarte **Bearbeiten.**

1. Klicken Sie auf die Schaltfläche **Aktualisieren,** um Updates zu sehen.

### <a name="view-logs"></a>Protokolle anzeigen

Daten, die über eine Container-App protokolliert werden, werden in der benutzerdefinierten Tabelle `ContainerAppConsoleLogs_CL` im Log Analytics-Arbeitsbereich gespeichert. Sie können Protokolle über das Azure-Portal oder mit der CLI anzeigen. Möglicherweise müssen Sie einige Minuten warten, bis die Analyse zum ersten Mal ankommt, bevor Sie die protokollierten Daten abfragen können.

Verwenden Sie den folgenden CLI-Befehl, um Protokolle in der Befehlszeile anzuzeigen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" `
  --out table
```

---

Die folgende Ausgabe veranschaulicht den Typ der Antwort, die vom CLI-Befehl erwartet wird.

```console
ContainerAppName_s    Log_s                            TableName      TimeGenerated
--------------------  -------------------------------  -------------  ------------------------
nodeapp               Got a new order! Order ID: 61    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Got a new order! Order ID: 62    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Got a new order! Order ID: 63    PrimaryResult  2021-10-22T22:45:44.618Z
```

> [!TIP]
> Treten Probleme auf? Informieren Sie uns über GitHub, indem Sie ein Problem im [Azure Container Apps-Repository](https://github.com/microsoft/azure-container-apps) öffnen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sobald Sie fertig sind, bereinigen Sie Ihre Container-App-Ressourcen, indem Sie den folgenden Befehl ausführen, um Ihre Ressourcengruppe zu löschen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
    --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
    --resource-group $RESOURCE_GROUP
```

---

Dieser Befehl löscht sowohl Container-Apps, das Speicherkonto, die Container-Apps-Umgebung und alle anderen Ressourcen in der Ressourcengruppe.

> [!NOTE]
> Da `pythonapp` kontinuierlich Aufrufe von `nodeapp` mit Nachrichten vornimmt, die in Ihrem konfigurierten Zustandsspeicher gespeichert werden, ist es wichtig, diese Bereinigungsschritte auszuführen, um laufende abrechenbare Vorgänge zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Anwendungslebenszyklusverwaltung](application-lifecycle-management.md)
