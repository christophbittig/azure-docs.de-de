---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: af26bc94e96c9d4bd1fdf59d5479bdc7181221f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298511"
---
Mit Azure Resource Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens "Parameters", der alle Parameterwerte enthält. Sie sollten einen Parameter für die Werte definieren, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die konstant sind. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren.

Verwenden Sie beim Definieren von Parametern das Feld **allowedValues** , um anzugeben, welche Werte ein Benutzer während der Bereitstellung angeben kann. Verwenden Sie das Feld **defaultValue** , um dem Parameter einen Wert zuweisen, wenn kein Wert während der Bereitstellung angegeben wird.

Nachfolgend wird jeder der in der Vorlage verwendeten Parameter beschrieben.

### <a name="sitename"></a>siteName

Der Name der Web-App, die Sie erstellen möchten.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName

Der Name des App Service-Plans zum Hosten der Web-App.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku

Der Tarif für den Hostingplan.

```json
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind, und weist einen Standardwert von `S1` zu, wenn kein Wert angegeben wird.

### <a name="workersize"></a>workerSize

Die Instanzgröße des Hostingplans (klein, mittel oder groß).

```json
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind(`0`, `1` oder `2`), und weist einen Standardwert von `0` zu, wenn kein Wert angegeben wird. Die Werte entsprechen klein, mittel und groß.
