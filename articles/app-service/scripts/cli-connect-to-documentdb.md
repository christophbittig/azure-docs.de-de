---
title: 'CLI: Verbinden einer App mit Cosmos DB'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. In diesem Beispiel wird gezeigt, wie Sie eine Verbindung zwischen einer App und MongoDB (Cosmos DB) herstellen.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 6beaafe19184e9c7b27c4e533f20c023948e9209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736117"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>Herstellen einer Verbindung zwischen einer App Service-App und Cosmos DB per CLI

Mit diesem Beispielskript wird ein Azure Cosmos DB-Konto erstellt, indem die Azure Cosmos DB-API für MongoDB und eine App Service-App verwendet werden. Anschließend wird eine MongoDB-Verbindungszeichenfolge mithilfe von App-Einstellungen mit der Web-App verknüpft.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine App Service-App, die Cosmos DB-Instanz und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Erstellt eine App Service-App. |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) | Erstellt ein Cosmos DB-Konto. |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Listet die Verbindungszeichenfolgen für das angegebene Cosmos DB-Konto auf. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Erstellt oder aktualisiert eine App-Einstellung für eine App Service-App. App-Einstellungen werden als Umgebungsvariablen für Ihre App verfügbar gemacht (siehe [Referenz zu Umgebungsvariablen und App-Einstellungen](../reference-app-settings.md)). |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
