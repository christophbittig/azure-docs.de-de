---
author: craigshoemaker
ms.service: app-service
ms.topic: include
ms.date: 10/25/2021
ms.author: cshoe
ms.openlocfilehash: 67821760435fdac37c51400190c1afe44996d014
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094856"
---
### <a name="get-fully-qualified-domain-name"></a>Abrufen eines vollqualifizierten Domänennamens

Der Befehl `az containerapp show` gibt den vollqualifizierten Domänennamen einer Container-App zurück.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --query configuration.ingress.fqdn
```

---

Ersetzen Sie in diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

Der von diesem Befehl zurückgegebene Wert ähnelt einem Domänennamen wie im folgenden Beispiel:

```console
myapp.happyhill-70162bb9.canadacentral.azurecontainerapps.io
```
