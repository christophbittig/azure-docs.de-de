---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 549104e9a0721d8edb58ad57cede0e4790cad4cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455858"
---
## <a name="add-azure-cli-extensions"></a>Hinzufügen von Azure CLI-Erweiterungen

Starten Sie die Bash-Umgebung in [Azure Cloud Shell](../articles/cloud-shell/quickstart.md).

[![Starten von Cloud Shell in einem neuen Fenster](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Da diese CLI-Befehle noch nicht Teil des CLI-Kernsets sind, fügen Sie sie mit den folgenden Befehlen hinzu:

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```