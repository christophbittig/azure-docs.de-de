---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: be837e20579a9236cc634e9518c82f17e2273e6b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454187"
---
Verwenden Sie den Befehl [az webpubsub client](/cli/azure/webpubsub/client) der Azure CLI, um eine WebSocket-Clientverbindung mit dem Dienst zu erstellen, der im vorherigen Schritt erstellt wurde. Geben Sie dabei die folgenden Informationen an:

- Hubname: Eine Zeichenfolge mit 1 bis 127 Zeichen. Sie sollte mit alphabetischen Zeichen `(a-z, A-Z)` beginnen und nur alphanumerische Zeichen `(0-9, a-z, A-Z)` oder Unterstriche `(_)` enthalten.

Ein **Hub** ist ein logischer Satz der verbundenen WebSocket-Verbindungen. Ausführliche Informationen zu den Konzepten finden Sie unter [Grundlegende Azure Web PubSub-Konzepte](../key-concepts.md).

  > [!Important]
  > Ersetzen Sie &lt;your-unique-resource-name&gt; durch den Namen Ihrer Web PubSub-Instanz, die in den vorherigen Schritten erstellt wurde.

- Hubname: **myHub1**
- Ressourcengruppennamen: **myResourceGroup**
- Benutzer-ID: **user1**

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --user-id "user1"
```

Sie sehen, dass der Befehl eine WebSocket-Verbindung mit dem Web PubSub-Dienst hergestellt hat und sie eine JSON-Nachricht mit dem Hinweis erhalten haben, dass die Verbindung nun erfolgreich hergestellt und ihr eine eindeutige ID (`connectionId`) zugewiesen wurde:

```json
{"type":"system","event":"connected","userId":"user1","connectionId":"<your_unique_connection_id>"}
```
