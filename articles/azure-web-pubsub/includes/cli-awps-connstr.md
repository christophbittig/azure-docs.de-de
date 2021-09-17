---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783496"
---
> [!Important]
> Eine Verbindungszeichenfolge enthält die Autorisierungsinformationen, die Ihre Anwendung für den Zugriff auf den Azure Web PubSub-Dienst benötigt. Der Zugriffsschlüssel in der Verbindungszeichenfolge ähnelt einem Stammkennwort für Ihren Dienst. Achten Sie darauf, die Zugriffsschlüssel immer gut zu schützen. Verwenden Sie Azure Key Vault zum sicheren Verwalten und Rotieren Ihrer Schlüssel. Geben Sie Zugriffsschlüssel nicht an andere Benutzer weiter, vermeiden Sie das Hartcodieren, und speichern Sie die Schlüssel nicht als Klartext, auf den andere Benutzer Zugriff haben. Rotieren Sie die Schlüssel, wenn Sie glauben, dass sie möglicherweise gefährdet sind.

Verwenden Sie den Azure CLI-Befehl [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key), um die Verbindungszeichenfolge (**ConnectionString**) des Diensts abzurufen.

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
