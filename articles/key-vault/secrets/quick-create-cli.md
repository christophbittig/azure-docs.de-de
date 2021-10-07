---
title: 'Schnellstart: Festlegen und Abrufen eines Geheimnisses aus Azure Key Vault'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI ein Geheimnis in Azure Key Vault festlegen und das Geheimnis daraus abrufen.
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cae19bc96cc689a3cb10f1c9820f95e13c9b4e4a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593711"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI

In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor in Azure Key Vault mit der Azure CLI. Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). Über die Azure-Befehlszeilenschnittstelle können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Danach speichern Sie ein Geheimnis.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Für diesen Schnellstart ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Ein Geheimnis kann dem Tresor mit einigen wenigen Zusatzschritten hinzugefügt werden. Dieses Kennwort kann dann von einer Anwendung verwendet werden. Das Kennwort soll **ExamplePassword** heißen und den Wert **hVFkk965BuUv** enthalten.

Verwenden Sie den folgenden Azure CLI-Befehl [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set), um in Key Vault ein Geheimnis namens **ExamplePassword** zu erstellen, in dem der Wert **hVFkk965BuUv** gespeichert wird:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Abrufen eines Geheimnisses aus Key Vault

Nun können Sie mit dem dazugehörigen URI auf das Kennwort verweisen, das Sie der Azure Key Vault-Instanz hinzugefügt haben. Verwenden Sie **`https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword`** , um die aktuelle Version abzurufen.

Um den im Geheimnis enthaltenen Wert als Nur-Text anzuzeigen, verwenden Sie den Azure CLI-Befehl [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show):

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Sie haben nun eine Key Vault-Instanz erstellt sowie ein Geheimnis gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Informieren Sie sich über das [Speichern von mehrzeiligen Geheimnissen in Key Vault](multiline-secrets.md).
- Sehen Sie sich die Referenz zu den [Azure CLI-Befehlen vom Typ „az keyvault“](/cli/azure/keyvault) an.
- [Azure Key Vault-Sicherheitsübersicht](../general/security-features.md)
