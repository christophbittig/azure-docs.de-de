---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 2b12b25b973d9e56de0241c1e088e2409137b086
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785397"
---
### <a name="publish-messages-and-manage-the-clients"></a>Veröffentlichen von Nachrichten und Verwalten der Clients

Über die Azure CLI stehen außerdem Befehle vom Typ [az webpubsub service](/cli/azure/webpubsub/service) zum Verwalten der Clientverbindungen zur Verfügung.

Öffnen Sie einen **weiteren** CLI-Befehl, und Sie können Nachrichten an die Clients übertragen:

- Hubname: **myHub1**
- Ressourcengruppennamen: **myResourceGroup**

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1 --payload "Hello World"
```

Wechseln Sie zurück zum vorherigen CLI-Befehl. Sie sehen, dass der Client eine Nachricht empfangen hat:
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

Sie können auch alle verfügbaren Befehle mithilfe der Option `--help` auflisten und die aufgeführten Befehle ausprobieren.

```azurecli-interactive
az webpubsub service --help
```
