---
title: Veröffentlichen von Revisionen mit GitHub-Aktionen in der Vorschau der Azure Container Anwendungen
description: Informationen zum automatischen Erstellen neuer Revisionen mithilfe von GitHub-Aktionen in der Vorschau der Azure Container Anwendungen
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: f345c3138891cef9d83000d1e64b950cd33747b4
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179553"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Veröffentlichen von Revisionen mit GitHub-Aktionen in der Vorschau der Azure Container Anwendungen

Mit Azure Container Anwendungen können Sie GitHub-Aktionen verwenden, um [Revisionen](revisions.md) in Ihrer Container-Anwendung zu veröffentlichen. Wenn Commits per Push an Ihr GitHub-Repository übertragen werden, wird eine GitHub-Aktion ausgelöst, die das [Container](containers.md)-Image in der Containerregistrierung aktualisiert. Nachdem der Container in der Registrierung aktualisiert wurde, erstellen die Azure Container Anwendungen eine neue Revision basierend auf dem aktualisierten Containerimage.

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="Änderungen an einem GitHub-Repository lösen eine Aktion zum Erstellen einer neuen Revision aus.":::

Die GitHub-Aktion wird durch Commits an einen bestimmten Branch in Ihrem Repository ausgelöst. Beim Erstellen des Integrationslinks entscheiden Sie, welcher Branch die Aktion auslöst.

## <a name="authentication"></a>Authentifizierung

Wenn Sie eine GitHub-Aktions-Integration hinzufügen oder entfernen, können Sie sich authentifizieren, indem Sie entweder ein [persönliches GitHub-Zugriffstoken](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) übergeben oder die interaktive GitHub-Anmeldung verwenden. Die interaktive Benutzeroberfläche öffnet ein Formular in Ihrem Webbrowser und bietet Ihnen die Möglichkeit, sich bei GitHub anzumelden. Nach erfolgreicher Authentifizierung wird ein Token an die CLI übergeben, das von GitHub für den Rest der aktuellen Sitzung verwendet wird.

- Verwenden Sie zum Übergeben eines persönlichen Zugriffstokens den Parameter `--token` und geben Sie einen Tokenwert an.
- Wenn Sie die interaktive Anmeldung verwenden möchten, verwenden Sie den Parameter `--login-with-github` ohne Wert.

## <a name="add"></a>Hinzufügen

Der Befehl `containerapp github-action add` erstellt eine GitHub-Aktions-Integration in Ihrer Container-Anwendung.

Wenn Sie GitHub-Aktionen zum ersten Mal an Ihre Container-Anwendung anfügen, müssen Sie einen Dienstprinzipalkontext bereitstellen. Die folgende Anweisung zeigt Ihnen, wie Sie einen Dienstprinzipal erstellen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> `
  --sdk-auth
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

Der Rückgabewert dieses Befehls sind JSON-Nutzdaten, die `tenantId`, `cliendId` und `clientSecret` des Dienstprinzipals enthalten.

Das folgende Beispiel zeigt, wie Sie eine Integration unter Verwendung eines persönlichen Zugriffstokens hinzufügen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-user-name <REGISTRY_USER_NAME> \
  --registry-password <REGISTRY_PASSWORD> \
  --service-principal-client-id <CLIENT_ID> \
  --service-principal-client-secret <CLIENT_SECRET> \
  --service-principal-tenant-id <TENANT_ID> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action add `
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" `
  --docker-file-path "./dockerfile" `
  --branch <BRANCH_NAME> `
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-user-name <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="show"></a>Anzeigen

Der Befehl `containerapp github-action show` gibt die GitHub-Aktions-Konfigurationseinstellungen für eine Container-Anwendung zurück.

In diesem Beispiel wird gezeigt, wie Sie eine Integration unter Verwendung des persönlichen Zugriffstokens hinzufügen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

Dieser Befehl gibt JSON-Nutzdaten mit den Konfigurationseinstellungen für die GitHub-Aktions-Integration zurück.

## <a name="delete"></a>Löschen

Der Befehl `containerapp github-action remove` entfernt die GitHub-Aktionen aus der Container-Anwendung.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action remove \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action remove `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.
