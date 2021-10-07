---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2d3c7cdcebc244aefb6ef5b2ceb7b931344a8960
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593710"
---
Verwenden Sie den Azure CLI-Befehl [az keyvault create](/cli/azure/keyvault#az_keyvault_create), um in der Ressourcengruppe aus dem vorherigen Schritt eine Key Vault-Instanz zu erstellen. Dazu müssen einige Informationen angegeben werden:

- Name des Schlüsseltresors: Eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf.

  > [!Important]
  > Jeder Schlüsseltresor muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen \<your-unique-keyvault-name\> durch den Namen Ihres Schlüsseltresors.

- Ressourcengruppennamen: **myResourceGroup**
- Standort: **EastUS**

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

In der Ausgabe dieses Befehls werden die Eigenschaften des neu erstellten Schlüsseltresors angezeigt. Beachten Sie die beiden folgenden Eigenschaften:

- **Tresorname**: Der Name, den Sie oben für den Parameter „--name“ angegeben haben.
- **Tresor-URI**: In dem Beispiel ist dies „https://&lt;Ihr-eindeutiger-Schlüsseltresor-Name&gt;.vault.azure.net/“. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.
