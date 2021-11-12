---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 4108aca3e5f1d944005f02e56964687c75162b41
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571339"
---
### <a name="app-service-plan"></a>App Service-Plan

Erstellt den Dienstplan zum Hosten der Web-App. Geben Sie den Namen des Plans über den **hostingPlanName** -Parameter an. Der Speicherort des Plans ist mit demjenigen für die Ressourcengruppe identisch. Der Tarif und die Workergröße werden in den Parametern **sku** und **workerSize** angegeben.

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
