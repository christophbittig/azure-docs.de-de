---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436231"
---
## <a name="limitations"></a>Einschränkungen

Beim Exportieren aus einer Ressourcengruppe oder Ressource wird die exportierte Vorlage aus den [veröffentlichten Schemas](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) für jeden Ressourcentyp generiert. Gelegentlich weist das Schema nicht die neueste Version für einen Ressourcentyp auf. Überprüfen Sie die exportierte Vorlage, um sicherzustellen, dass sie die benötigten Eigenschaften enthält. Bearbeiten Sie bei Bedarf die exportierte Vorlage so, dass sie die benötigte API-Version verwendet.

Die Funktion „Vorlage exportieren“ unterstützt nicht das Exportieren von Azure Data Factory-Ressourcen. Informationen zum Exportieren von Data Factory-Ressourcen finden Sie unter [Kopieren oder Klonen einer Data Factory in Azure Data Factory](../articles/data-factory/copy-clone-data-factory.md).

Um über das klassische Bereitstellungsmodell erstellte Ressourcen zu exportieren, müssen Sie [sie zum Resource Manager-Bereitstellungsmodell migrieren](../articles/virtual-machines/migration-classic-resource-manager-overview.md).

Wenn beim Exportieren einer Vorlage eine Warnung angezeigt wird, die angibt, dass ein Ressourcentyp nicht exportiert wurde, können Sie die Eigenschaften für diese Ressource immer noch ermitteln. Informationen zu Ressourceneigenschaften finden Sie in der [Vorlagenreferenz](/azure/templates). Sie können den Ressourcentyp auch in der [Azure-REST-API](/rest/api/azure/) nachschlagen.

Die Ressourcengruppe, für die Sie die exportierte Vorlage erstellen, ist auf 200 Ressourcen begrenzt. Wenn Sie versuchen, eine Ressourcengruppe mit mehr als 200 Ressourcen zu exportieren, wird die Fehlermeldung `Export template is not supported for resource groups more than 200 resources` angezeigt.
