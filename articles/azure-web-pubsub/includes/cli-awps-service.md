---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: a34e4d77d77ad34a8a0487a9efcdeb48ed510dda
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131663727"
---
### <a name="publish-messages-and-manage-the-clients"></a>Veröffentlichen von Nachrichten und Verwalten der Clients

Über die Azure CLI stehen außerdem Befehle vom Typ [az webpubsub service](/cli/azure/webpubsub/service) zum Verwalten der Clientverbindungen zur Verfügung.

Öffnen Sie einen **weiteren** CLI-Befehl, und Sie können Nachrichten an die Clients übertragen:

- Hubname: **myHub1**
- Ressourcengruppennamen: **myResourceGroup**

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --payload "Hello World"
```

Wechseln Sie zurück zum vorherigen CLI-Befehl. Sie sehen, dass der Client eine Nachricht empfangen hat:
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

Sie können auch alle verfügbaren Befehle mithilfe der Option `--help` auflisten und die aufgeführten Befehle ausprobieren.

```azurecli-interactive
az webpubsub service --help
```
