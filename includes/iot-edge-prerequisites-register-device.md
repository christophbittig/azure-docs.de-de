---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aa4ddb316efa3ea4c1bb12d4c6d7b541628bb0e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576762"
---
### <a name="device-registration"></a>Geräteregistrierung

Sie können die Schritte zum Registrieren Ihres Geräts über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** ausführen. Bei jedem Hilfsprogramm gibt es eigene Voraussetzungen:

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein [IoT-Hub](../articles/iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ein [IoT-Hub](../articles/iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Ein [IoT-Hub](../articles/iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.
* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 oder höher lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.

---
