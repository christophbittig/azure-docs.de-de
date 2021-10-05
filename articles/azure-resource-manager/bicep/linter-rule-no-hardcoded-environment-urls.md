---
title: Linterregel – keine hartcodierte Umgebungs-URL
description: Linterregel – keine hartcodierte Umgebungs-URL
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598436"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Linterregel – keine hartcodierte Umgebungs-URL

Der Linter vereinfacht das Erzwingen von Codierungsstandards, indem er während der Entwicklung Leitfäden bereitstellt. Der aktuelle Satz von Linterregeln ist minimal und stammt aus [arm-ttk-Testfällen](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Weitere Informationen finden Sie unter [Verwenden von Biceps-Linter](./linter.md).

## <a name="code"></a>Code

`no-hardcoded-env-urls`

## <a name="description"></a>BESCHREIBUNG

Die Umgebungs-URLs in Ihrer Vorlage dürfen nicht hartcodiert werden. Verwenden Sie stattdessen die [Umgebungsfunktion](../templates/template-functions-deployment.md#environment), um diese URLs während der Bereitstellung dynamisch abzurufen. Eine Liste der blockierten URL-Hosts finden Sie in der Standardliste von `DisallowedHosts` in [bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json).

## <a name="examples"></a>Beispiele

Im folgenden Beispiel ist der Test nicht erfolgreich, weil die URL hartcodiert ist.

```bicep
var AzureURL = 'https://management.azure.com'
```

Der Test ist auch bei Verwendung von concat oder urinicht erfolgreich.

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

Im folgenden Beispiel ist der Test erfolgreich.

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>Konfiguration

Der Satz von URL-Hosts, die nicht zugelassen werden sollen, kann mithilfe der Eigenschaft „disallowedHosts“ in der Datei "bicepconfig.json" wie folgt angepasst werden:

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
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
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
