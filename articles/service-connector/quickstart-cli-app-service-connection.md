---
title: 'Schnellstart: Erstellen einer Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle herstellen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f702b28aebc15c9fb05986552e3b58d4a911c80
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100349"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>Schnellstart: Erstellen einer Dienstverbindung in App Service mit der Azure-Befehlszeilenschnittstelle

Die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) setzt sich aus Befehlen zum Erstellen und Verwalten von Azure-Ressourcen zusammen. Die Azure CLI ist in allen Azure-Diensten verfügbar und soll die schnelle Verwendung von Azure ermöglichen. Der Fokus liegt dabei auf der Automatisierung. In dieser Schnellstartanleitung werden die Optionen zum Erstellen einer Azure Web PubSub-Instanz mit der Azure CLI erläutert.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- In diesem Schnellstart wird davon ausgegangen, dass Sie bereits über mindestens einen in Azure ausgeführten App Service verfügen. [Erstellen Sie einen App Service](../app-service/quickstart-dotnetcore.md), wenn Sie über keinen App Service verfügen.


## <a name="view-supported-target-service-types"></a>Anzeigen der unterstützten Zieldiensttypen

Sie verwenden den Azure CLI-Befehl [az webapp connection](), um Dienstverbindungen mit App Service zu erstellen und zu verwalten. 

```azurecli-interactive
az webapp connection list-support-types
```

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

Verwenden Sie den Azure CLI-Befehl [az webapp connection](), um eine Dienstverbindung mit einem Blobspeicher zu erstellen, und geben Sie dabei die folgenden Informationen an:

- **Ressourcengruppenname des Quell-Computediensts:** Der Ressourcengruppenname des App Service.
- **App Service-Name:** Der Name Ihres App Services, der eine Verbindung mit dem Zieldienst herstellt.
- **Zieldienst-Ressourcengruppenname:** Ressourcengruppenname des Blobspeichers
- **Speicherkontoname:** Kontoname Ihres Blobspeichers.

```azurecli-interactive
az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> Wenn Sie nicht über einen Blobspeicher verfügen, können Sie `az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` ausführen, um einen neuen zu erstellen und diesen direkt mit Ihrem App-Dienst zu verbinden.

## <a name="view-connections"></a>Anzeigen von Verbindungen

Verwenden Sie den Azure CLI-Befehl [az webapp connection](), um die Verbindung mit Ihrem App Service aufzulisten, und geben Sie dabei die folgenden Informationen an:

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
