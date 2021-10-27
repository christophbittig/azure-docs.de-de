---
title: Linterregel – keine hartcodierte Umgebungs-URL
description: Linterregel – keine hartcodierte Umgebungs-URL
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 8bb3bbd4be61259d1a11184d879f737d970399cd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165379"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Linterregel – keine hartcodierte Umgebungs-URL

Diese Regel findet alle fest kodierten URLs, die je nach Cloud-Umgebung variieren.

## <a name="returned-code"></a>Zurückgegebener Code

`no-hardcoded-env-urls`

## <a name="solution"></a>Lösung

Anstatt URLs in Ihrer Bicep-Datei fest zu kodieren, verwenden Sie die Funktion [environment](../templates/template-functions-deployment.md#environment), um diese URLs während der Bereitstellung dynamisch abzurufen. Die Umgebungsfunktion gibt je nach der Cloud-Umgebung, in der Sie bereitstellen, unterschiedliche URLs zurück.

Im folgenden Beispiel ist der Test nicht erfolgreich, weil die URL hartcodiert ist.

```bicep
var managementURL = 'https://management.azure.com'
```

Der Test ist auch bei Verwendung von concat oder urinicht erfolgreich.

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

Sie können das Problem beheben, indem Sie die fest codierte URL durch die Funktion `environment()` ersetzen.

```bicep
var galleryURL = environment().gallery
```

In einigen Fällen können Sie das Problem beheben, indem Sie eine Eigenschaft einer von Ihnen bereitgestellten Ressource abrufen. Anstatt beispielsweise den Endpunkt für Ihr Speicherkonto zu erstellen, rufen Sie ihn mit `.properties.primaryEndpoints` ab.

```bicep
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageAccountName
  location: 'westus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = sa.properties.primaryEndpoints.web
```

## <a name="configuration"></a>Konfiguration

Standardmäßig verwendet diese Regel die folgenden Einstellungen, um zu bestimmen, welche URLs nicht zulässig sind. 

```json
"analyzers": {
  "core": {
    "verbose": false,
    "enabled": true,
    "rules": {
      "no-hardcoded-env-urls": {
        "level": "warning",
        "disallowedhosts": [
          "gallery.azure.com",
          "management.core.windows.net",
          "management.azure.com",
          "database.windows.net",
          "core.windows.net",
          "login.microsoftonline.com",
          "graph.windows.net",
          "trafficmanager.net",
          "datalake.azure.net",
          "azuredatalakestore.net",
          "azuredatalakeanalytics.net",
          "vault.azure.net",
          "api.loganalytics.io",
          "asazure.windows.net",
          "region.asazure.windows.net",
          "batch.core.windows.net"
        ],
        "excludedhosts": [
          "schema.management.azure.com"
        ]
      }
    }
  }
}
```

Sie können sie anpassen, indem Sie eine bicepconfig.json-Datei hinzufügen und neue Einstellungen anwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über den Linter finden Sie unter [Verwendung des Bicep-Linters](./linter.md).
