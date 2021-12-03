---
title: 'Schnellstart: Erstellen einer Dienstverbindung in Spring Cloud mit der Azure-Befehlszeilenschnittstelle'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Dienstverbindung in Spring Cloud mit der Azure-Befehlszeilenschnittstelle herstellen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 13f2cf7abc24c2f84ea95f0d3779c015680ca01d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554577"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>Schnellstart: Erstellen einer Dienstverbindung in Spring Cloud mit der Azure-Befehlszeilenschnittstelle

Die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) setzt sich aus Befehlen zum Erstellen und Verwalten von Azure-Ressourcen zusammen. Die Azure CLI ist in allen Azure-Diensten verfügbar und soll die schnelle Verwendung von Azure ermöglichen. Der Fokus liegt dabei auf der Automatisierung. In dieser Schnellstartanleitung werden die Optionen zum Erstellen einer Azure Web PubSub-Instanz mit der Azure CLI erläutert.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.30.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- In diesem Schnellstart wird davon ausgegangen, dass Sie bereits über mindestens eine in Azure ausgeführte Spring Cloud-Anwendung verfügen. Wenn Sie nicht über eine Spring Cloud-Anwendung verfügen, [erstellen Sie eine](../spring-cloud/quickstart.md).


## <a name="view-supported-target-service-types"></a>Anzeigen der unterstützten Zieldiensttypen

Sie verwenden den Azure CLI-Befehl [az spring-cloud connection](), um Dienstverbindungen mit Ihrer Spring Cloud-Anwendung zu erstellen und zu verwalten. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az spring-cloud connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

#### <a name="using-access-key"></a>[Verwenden eines Zugriffsschlüssels](#tab/Using-access-key)

Verwenden Sie den Azure CLI-Befehl [az spring-cloud connection](), um eine Dienstverbindung mit einem Blobspeicher mit Zugriffsschlüssel zu erstellen, und geben Sie dabei die folgenden Informationen an:

- **Spring Cloud-Ressourcengruppenname:** Ressourcengruppenname von Spring Cloud
- **Spring Cloud Name:** Name Ihrer Spring Cloud-Instanz
- **Spring Cloud-App-Name:** Name Ihrer Spring Cloud-App, die eine Verbindung mit dem Zieldienst herstellt
- **Zieldienst-Ressourcengruppenname:** Ressourcengruppenname des Blobspeichers
- **Speicherkontoname:** Kontoname Ihres Blobspeichers.

```azurecli-interactive
az spring-cloud connection create storage-blob --secret
```

> [!NOTE]
> Wenn Sie nicht über einen Blobspeicher verfügen, können Sie `az spring-cloud connection create storage-blob --new --secret` ausführen, um einen neuen zu erstellen und diesen direkt mit Ihrem App-Dienst zu verbinden.

#### <a name="using-managed-identity"></a>[Verwenden einer verwalteten Identität](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> Für die Verwendung einer verwalteten Identität müssen Sie über die Berechtigung für die [Azure AD-Rollenzuweisung](/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal) verfügen. Wenn Sie nicht über die Berechtigung verfügen, ist die Verbindungserstellung nicht erfolgreich. Sie können Ihren Abonnementbesitzer nach der Berechtigung fragen oder den Zugriffsschlüssel verwenden, um die Verbindung zu erstellen.

Verwenden Sie den Azure CLI-Befehl [az spring-cloud connection](), um eine Dienstverbindung mit einem Blobspeicher mit systemseitig zugewiesene verwaltete Identität zu erstellen, und geben Sie dabei die folgenden Informationen an:

- **Spring Cloud-Ressourcengruppenname:** Ressourcengruppenname von Spring Cloud
- **Spring Cloud Name:** Name Ihrer Spring Cloud-Instanz
- **Spring Cloud-App-Name:** Name Ihrer Spring Cloud-App, die eine Verbindung mit dem Zieldienst herstellt
- **Zieldienst-Ressourcengruppenname:** Ressourcengruppenname des Blobspeichers
- **Speicherkontoname:** Kontoname Ihres Blobspeichers.

```azurecli-interactive
az spring-cloud connection create storage-blob --system-identity
```

> [!NOTE]
> Wenn Sie nicht über einen Blobspeicher verfügen, können Sie `az spring-cloud connection create --system-identity --new --secret` ausführen, um einen neuen zu erstellen und diesen direkt mit Ihrem App-Dienst zu verbinden.

---

## <a name="view-connections"></a>Anzeigen von Verbindungen

Verwenden Sie den Azure CLI-Befehl [az spring-cloud connection](), um die Verbindungen mit Ihrer Spring Cloud-Anwendung aufzulisten, und geben Sie dabei die folgenden Informationen an:

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die unten stehenden Tutorials durch, um mit dem Erstellen Ihrer eigenen Anwendung mit dem Dienstconnector zu beginnen.

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud und MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud und Apache Kafka in Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)
