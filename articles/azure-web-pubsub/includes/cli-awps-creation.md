---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 3671a5d221afa89c776dca1bc3a17b965b621c5b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423988"
---
Verwenden Sie den Azure CLI-Befehl [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create), um eine Web PubSub-Instanz in der Ressourcengruppe aus dem vorherigen Schritt zu erstellen. Geben Sie dabei die folgenden Informationen an:

- Ressourcenname: eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf

  > [!Important]
  > Jede Web PubSub-Ressource muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen &lt;your-unique-resource-name&gt; durch den Namen Ihrer Web PubSub-Instanz.

- Ressourcengruppennamen: **myResourceGroup**
- Standort: **EastUS**
- SKU: **Free_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

In der Ausgabe dieses Befehls werden die Eigenschaften der neu erstellten Ressource angezeigt. Beachten Sie die beiden folgenden Eigenschaften:

- **Resource Name**: Der Name, den Sie oben für den Parameter `--name` angegeben haben.
- **hostName**: Im Beispiel lautet der Hostname `<your-unique-resource-name>.webpubsub.azure.com/`.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für die neue Ressource autorisiert.
