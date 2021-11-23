---
title: 'Schnellstart: Bereitstellen Ihrer ersten Container-App'
description: Stellen Sie Ihre erste Anwendung in Azure Container Apps (Vorschauversion) bereit.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb2b24c080f7f8173280d5c31ca711d7241d04f1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552715"
---
# <a name="quickstart-deploy-your-first-container-app"></a>Schnellstart: Bereitstellen Ihrer ersten Container-App

Mit Azure Container Apps (Vorschauversion) können Sie Microservices und Containeranwendungen auf einer serverlosen Plattform ausführen. Mit Container Apps genießen Sie die Vorteile von Containern und müssen sich nicht mehr um die manuelle Konfiguration von Cloudinfrastrukturen und komplexen Containerorchestratoren kümmern.

In dieser Schnellstartanleitung erstellen Sie eine sichere Container-Apps-Umgebung und stellen Ihre erste Container-App bereit.

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli).

## <a name="setup"></a>Einrichten

Beginnen Sie mit der Anmeldung bei Azure über die Befehlszeilenschnittstelle. Führen Sie den folgenden Befehl aus, und befolgen Sie die Anweisungen, um den Authentifizierungsprozess abzuschließen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
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

Legen Sie anschließend die folgenden Umgebungsvariablen fest:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
RESOURCE_GROUP="my-container-apps"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
CONTAINERAPPS_ENVIRONMENT="my-environment"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-container-apps"
$LOCATION="canadacentral"
$LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
$CONTAINERAPPS_ENVIRONMENT="my-environment"
```

---

Wenn diese Variablen definiert sind, können Sie eine Ressourcengruppe erstellen, um die Dienste zu organisieren, die mit Ihrer neuen Container-App verknüpft sind.

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

Eine Umgebung in Azure Container Apps erstellt eine sichere Grenze für eine Gruppe von Container-Apps. Container-Apps, die in derselben Umgebung bereitgestellt werden, werden im gleichen virtuellen Netzwerk bereitgestellt und schreiben Protokolle in denselben Log Analytics-Arbeitsbereich.

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

## <a name="create-a-container-app"></a>Erstellen einer Container-App

Nachdem Sie eine Umgebung erstellt haben, können Sie Ihre erste Container-App bereitstellen. Stellen Sie mithilfe des Befehls `containerapp create` ein Containerimage in Azure Container Apps bereit.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name my-container-app `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
  --target-port 80 `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

Wenn Sie `--ingress` auf `external` festlegen, stellen Sie die Container-App für öffentliche Anforderungen zur Verfügung.

Der Befehl `create` gibt in diesem Fall den vollqualifizierten Domänennamen der Container-App zurück. Kopieren Sie diesen Speicherort in einen Webbrowser, und die folgende Meldung wird angezeigt.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Ihre erste Azure Container Apps-Bereitstellung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden möchten, können Sie die Azure Container Apps-Instanz und alle zugehörigen Dienste löschen, indem Sie die Ressourcengruppe entfernen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --name $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --name $RESOURCE_GROUP
```

---

> [!TIP]
> Treten Probleme auf? Informieren Sie uns über GitHub, indem Sie ein Problem im [Azure Container Apps-Repository](https://github.com/microsoft/azure-container-apps) öffnen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Umgebungen in Azure Container Apps](environment.md)
