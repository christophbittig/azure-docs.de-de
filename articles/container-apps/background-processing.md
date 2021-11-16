---
title: 'Tutorial: Bereitstellen einer Hintergrundverarbeitungsanwendung mit Azure Container Apps Preview'
description: Hier erfahren Sie, wie Sie mit Azure Container Apps eine Anwendung erstellen, die kontinuierlich im Hintergrund ausgeführt wird.
services: app-service
author: jorgearteiro
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: joarteir
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5cda6e2793ca70c1d53b8a4b4ce4235fa07efd
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577667"
---
# <a name="tutorial-deploy-a-background-processing-application-with-azure-container-apps-preview"></a>Tutorial: Bereitstellen einer Hintergrundverarbeitungsanwendung mit Azure Container Apps Preview

Mit Azure Container Apps können Sie Anwendungen bereitstellen, ohne dass öffentliche Endpunkte verfügbar gemacht werden müssen. In diesem Tutorial stellen Sie eine Beispielanwendung bereit, die Nachrichten aus einer Azure Storage-Warteschlange liest und diese im Azure Log Analytics-Arbeitsbereich protokolliert. Mithilfe von Container-Apps-Skalierungsregeln kann die Anwendung je nach Länge der Azure Storage-Warteschlange hoch- und herunterskaliert werden. Wenn keine Nachrichten in der Warteschlange enthalten sind, wird die Container-App auf null herunterskaliert.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Container Apps-Umgebung für die Bereitstellung Ihrer Container-Apps
> * Erstellen einer Azure Storage-Warteschlange zum Senden von Nachrichten an die Container-App
> * Bereitstellen Ihrer Hintergrundverarbeitungsanwendung als Container-App
> * Überprüfen der Verarbeitung der Warteschlangennachrichten durch die Container-App

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* **Azure CLI:** Auf dem lokalen Computer muss mindestens Version 2.29.0 der Azure CLI installiert sein.
  * Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).
  
## <a name="setup"></a>Einrichten

In diesem Tutorial werden die folgenden Umgebungsvariablen verwendet:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

---

Erstellen Sie eine Variable für den Namen Ihres Speicherkontos.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<MY_STORAGE_ACCOUNT_NAME>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

Ersetzen Sie den Platzhalter `<storage account name>` durch Ihren eigenen Wert, bevor Sie diesen Codeausschnitt ausführen. Speicherkontonamen müssen innerhalb von Azure eindeutig sein, zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Das Speicherkonto wird in einem der folgenden Schritte erstellt.

Melden Sie sich als Nächstes über die CLI bei Azure an.

Führen Sie den folgenden Befehl aus, und befolgen Sie die Anweisungen, um den Authentifizierungsprozess abzuschließen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az login
```

---

Verwenden Sie den Befehl `upgrade`, um sicherzustellen, dass Sie die neueste Version der CLI ausführen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
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

```powershell
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

---

Nachdem die Erweiterung installiert wurde, registrieren Sie den `Microsoft.Web` Namespace.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az provider register --namespace Microsoft.Web
```

---

Sie erstellen eine Ressourcengruppe, um die Dienste im Zusammenhang mit Ihrer neuen Container-App zu organisieren. Erstellen Sie die Gruppe mit dem folgenden Befehl:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group create `
  --name $RESOURCE_GROUP `
  --location $LOCATION
```

---

Wenn die CLI aktualisiert wurde und eine neue Ressourcengruppe verfügbar ist, können Sie eine Container Apps-Umgebung erstellen und Ihre Container-App bereitstellen.

## <a name="create-an-environment"></a>Erstellen einer Umgebung

Azure Container Apps-Umgebungen fungieren als sichere Grenze für eine Gruppe von Container-Apps. Unterschiedliche Container-Apps, die in derselben Umgebung bereitgestellt wurden, werden auch im gleichen virtuellen Netzwerk bereitgestellt und schreiben Protokolle in denselben Log Analytics-Arbeitsbereich.

Azure Log Analytics wird zum Überwachen Ihrer Container-App verwendet und ist beim Erstellen einer Container Apps-Umgebung erforderlich.

Erstellen Sie mit dem folgenden Befehl einen neuen Log Analytics-Arbeitsbereich:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

Rufen Sie als Nächstes die Log Analytics-Client-ID und den geheimen Clientschlüssel ab. Stellen Sie sicher, dass Sie jede Abfrage separat ausführen, um genügend Zeit für den Abschluss der Anforderung zu geben.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"')
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
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

```powershell
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location $LOCATION
```

---

## <a name="set-up-a-storage-queue"></a>Einrichten einer Speicherwarteschlange

Erstellen eines Azure-Speicherkontos.

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

```powershell
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Rufen Sie als Nächstes die Verbindungszeichenfolge der Warteschlange ab.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
QUEUE_CONNECTION_STRING=`az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$QUEUE_CONNECTION_STRING=(az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"')
```

---

Nun können Sie die Nachrichtenwarteschlange erstellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage queue create \
  --name "myqueue" \
  --account-name $STORAGE_ACCOUNT \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage queue create `
  --name "myqueue" `
  --account-name $STORAGE_ACCOUNT `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

Nun können Sie eine Nachricht an die Warteschlange senden.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage message put \
  --content "Hello Queue Reader App" \
  --queue-name "myqueue" \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage message put `
  --content "Hello Queue Reader App" `
  --queue-name "myqueue" `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

## <a name="deploy-the-background-application"></a>Bereitstellen der Hintergrundanwendung

Erstellen Sie eine Datei mit dem Namen *queue.json*, und fügen Sie den folgenden Konfigurationscode in die Datei ein.

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
            "defaultValue": "",
            "type": "String"
        },
        "queueconnection": {
            "defaultValue": "",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
    {
        "name": "queuereader",
        "type": "Microsoft.Web/containerApps",
        "apiVersion": "2021-03-01",
        "kind": "containerapp",
        "location": "[parameters('location')]",
        "properties": {
            "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
            "configuration": {
                "activeRevisionsMode": "single",
                "secrets": [
                {
                    "name": "queueconnection",
                    "value": "[parameters('queueconnection')]"
                }]
            },
            "template": {
                "containers": [
                    {
                        "image": "mcr.microsoft.com/azuredocs/containerapps-queuereader",
                        "name": "queuereader",
                        "env": [
                            {
                                "name": "QueueName",
                                "value": "myqueue"
                            },
                            {
                                "name": "QueueConnectionString",
                                "secretref": "queueconnection"
                            }
                        ]
                    }
                ],
                "scale": {
                    "minReplicas": 1,
                    "maxReplicas": 10,
                    "rules": [
                        {
                            "name": "myqueuerule",
                            "azureQueue": {
                                "queueName": "myqueue",
                                "queueLength": 100,
                                "auth": [
                                    {
                                        "secretRef": "queueconnection",
                                        "triggerParameter": "connection"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    }]
}

```

Jetzt können Sie Ihre Container-App erstellen und bereitstellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./queue.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    queueconnection="$QUEUE_CONNECTION_STRING" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./queue.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    queueconnection="$QUEUE_CONNECTION_STRING" `
    location="$LOCATION"
```

---

Dieser Befehl stellt die Demoanwendung aus dem öffentlichen Containerimage `mcr.microsoft.com/azuredocs/containerapps-queuereader` bereit und legt die von der Anwendung verwendeten Geheimnisse und Umgebungsvariablen fest.

Die Anwendung wird wie im Abschnitt `scale` der ARM-Vorlage definiert je nach Warteschlangenlänge auf bis zu zehn Replikate hochskaliert.

## <a name="verify-the-result"></a>Überprüfen des Ergebnisses

Die Container-App, die als Hintergrundprozess ausgeführt wird, erstellt Protokolleinträge in Log Analytics, wenn Nachrichten von der Azure Storage-Warteschlange eingehen. Möglicherweise müssen Sie einige Minuten warten, bis die Analyse zum ersten Mal ankommt, bevor Sie die protokollierten Daten abfragen können.

Führen Sie den folgenden Befehl aus, um die protokollierten Meldungen anzuzeigen. Für diesen Befehl ist die Log Analytics-Erweiterung erforderlich. Stimmen Sie daher der ggf. angezeigten Aufforderung zur Installation der Erweiterung zu.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" `
  --out table
```

---

> [!TIP]
> Treten Probleme auf? Informieren Sie uns über GitHub, indem Sie ein Problem im [Azure Container Apps-Repository](https://github.com/microsoft/azure-container-apps) öffnen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sobald Sie fertig sind, bereinigen Sie Ihre Container Apps-Ressourcen, indem Sie den folgenden Befehl ausführen, um Ihre Ressourcengruppe zu löschen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group delete `
  --resource-group $RESOURCE_GROUP
```

---

Dieser Befehl löscht die gesamte Ressourcengruppe, einschließlich der Container Apps-Instanz, des Speicherkontos, des Log Analytics-Arbeitsbereichs und aller anderen Ressourcen in der Ressourcengruppe.
