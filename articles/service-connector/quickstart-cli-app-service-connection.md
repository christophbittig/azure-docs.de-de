---
title: 'Schnellstart: Erstellen einer Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle herstellen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5e4864db07dbf7618e5b8e03a29e08c1344d7ebd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553399"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>Schnellstart: Erstellen einer Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle

Die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) setzt sich aus Befehlen zum Erstellen und Verwalten von Azure-Ressourcen zusammen. Die Azure CLI ist in allen Azure-Diensten verfügbar und soll die schnelle Verwendung von Azure ermöglichen. Der Fokus liegt dabei auf der Automatisierung. In dieser Schnellstartanleitung werden die Optionen zum Erstellen einer Azure Web PubSub-Instanz mit der Azure CLI erläutert.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.30.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- In diesem Schnellstart wird davon ausgegangen, dass Sie bereits über mindestens einen in Azure ausgeführten App Service verfügen. [Erstellen Sie einen App Service](../app-service/quickstart-dotnetcore.md), wenn Sie über keinen App Service verfügen.

## <a name="view-supported-target-service-types"></a>Anzeigen der unterstützten Zieldiensttypen

Sie verwenden den Azure CLI-Befehl [az webapp connection](/cli/azure/webapp/connection), um Dienstverbindungen mit App Service zu erstellen und zu verwalten. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

#### <a name="using-access-key"></a>[Verwenden eines Zugriffsschlüssels](#tab/Using-access-key)

Verwenden Sie den Azure CLI-Befehl [az webapp connection](/cli/azure/webapp/connection), um eine Dienstverbindung mit einem Blobspeicher mit Zugriffsschlüssel zu erstellen, und geben Sie dabei die folgenden Informationen an:

- **Ressourcengruppenname des Quell-Computediensts:** Der Ressourcengruppenname des App Service.
- **App Service-Name:** Der Name Ihres App Services, der eine Verbindung mit dem Zieldienst herstellt.
- **Zieldienst-Ressourcengruppenname:** Ressourcengruppenname des Blobspeichers
- **Speicherkontoname:** Kontoname Ihres Blobspeichers.

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> Wenn Sie nicht über einen Blobspeicher verfügen, können Sie `az webapp connection create storage-blob --new --secret` ausführen, um einen neuen zu erstellen und diesen direkt mit Ihrem App-Dienst zu verbinden.

#### <a name="using-managed-identity"></a>[Verwenden einer verwalteten Identität](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> Für die Verwendung einer verwalteten Identität müssen Sie über die Berechtigung für die [Azure AD-Rollenzuweisung](/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal) verfügen. Wenn Sie nicht über die Berechtigung verfügen, ist die Verbindungserstellung nicht erfolgreich. Sie können Ihren Abonnementbesitzer nach der Berechtigung fragen oder den Zugriffsschlüssel verwenden, um die Verbindung zu erstellen.

Verwenden Sie den Azure CLI-Befehl [az webapp connection](/cli/azure/webapp/connection), um eine Dienstverbindung mit einem Blobspeicher mit systemseitig zugewiesene verwaltete Identität zu erstellen, und geben Sie dabei die folgenden Informationen an:

- **Ressourcengruppenname des Quell-Computediensts:** Der Ressourcengruppenname des App Service.
- **App Service-Name:** Der Name Ihres App Services, der eine Verbindung mit dem Zieldienst herstellt.
- **Zieldienst-Ressourcengruppenname:** Ressourcengruppenname des Blobspeichers
- **Speicherkontoname:** Kontoname Ihres Blobspeichers.

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> Wenn Sie nicht über einen Blobspeicher verfügen, können Sie `az webapp connection create storage-blob --new --system-identity` ausführen, um einen neuen zu erstellen und diesen direkt mit Ihrem App-Dienst zu verbinden.

---

## <a name="view-connections"></a>Anzeigen von Verbindungen

Verwenden Sie den Azure CLI-Befehl [az webapp connection](/cli/azure/webapp/connection), um die Verbindung mit Ihrem App Service aufzulisten, und geben Sie dabei die folgenden Informationen an:

- **Ressourcengruppenname des Quell-Computediensts:** Der Ressourcengruppenname des App Service.
- **App Service-Name:** Der Name Ihres App Services, der eine Verbindung mit dem Zieldienst herstellt.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die unten stehenden Tutorials durch, um mit dem Erstellen Ihrer eigenen Anwendung mit dem Dienstconnector zu beginnen.

> [!div class="nextstepaction"]
> [Tutorial: WebApp + Storage mit Azure CLI](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutorial: WebApp + PostgreSQL mit Azure CLI](./tutorial-django-webapp-postgres-cli.md)
