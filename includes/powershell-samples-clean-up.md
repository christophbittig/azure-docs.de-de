---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/19/2021
ms.author: cephalin
ms.openlocfilehash: a1128c7e2e8a89c8d60e629437a12e2c1378babc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455992"
---
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen voraussichtlich nicht mehr benötigen, löschen Sie die Ressourcengruppen mit folgendem PowerShell-Befehl:

```azurecli-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.