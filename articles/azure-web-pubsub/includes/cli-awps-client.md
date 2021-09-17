---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785396"
---
### <a name="connect-to-the-service"></a>Herstellen einer Verbindung mit dem Dienst

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

Experimentieren Sie damit, und versuchen Sie, mithilfe von `joingroup <group-name>` Gruppen beizutreten und mithilfe von `sendtogroup <group-name>` Nachrichten an Gruppen zu senden:

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
