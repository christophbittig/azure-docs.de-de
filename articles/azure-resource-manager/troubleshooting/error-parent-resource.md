---
title: Fehler bei übergeordneten Ressourcen
description: Hier wird beschrieben, wie bei der Arbeit mit einer übergeordneten Ressource in einer Azure Resource Manager-Vorlage Fehler behoben werden können.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: bc1409a85eaaf8c7fb86880dc6db7d692a6101d7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095191"
---
# <a name="resolve-errors-for-parent-resources"></a>Beheben von Fehlern bei übergeordneten Ressourcen

In diesem Artikel werden die Fehler beschrieben, die bei der Bereitstellung einer Ressource auftreten können, die von einer übergeordneten Ressource abhängig ist.

## <a name="symptom"></a>Symptom

Wenn Sie eine Ressource bereitstellen, die einer anderen Ressource untergeordnet ist, kann der folgende Fehler auftreten:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Ursache

Wenn eine Ressource einer anderen untergeordnet ist, muss die übergeordnete Ressource vor dem Erstellen der untergeordneten Ressource bereits vorhanden sein. Der Name der untergeordneten Ressource definiert die Verbindung mit der übergeordneten Ressource. Der Name der untergeordneten Ressource weist das Format `<parent-resource-name>/<child-resource-name>` auf. Zum Beispiel könnte eine SQL-Datenbank wie folgt definiert werden:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Wenn Sie den Server und die Datenbank in der gleichen Vorlage bereitstellen, jedoch auf dem Server keine Abhängigkeit angeben, beginnt die Datenbankbereitstellung möglicherweise vor der Bereitstellung des Servers.

Falls die übergeordnete Ressource bereits vorhanden ist und nicht in der gleichen Vorlage bereitgestellt wird, erhalten Sie diesen Fehler, wenn Resource Manager die untergeordnete Ressource nicht mit der übergeordneten Ressource verknüpfen kann. Dieser Fehler kann auftreten, wenn die untergeordnete Ressource nicht das richtige Format hat oder wenn die untergeordnete Ressource in einer Ressourcengruppe bereitgestellt wird, die sich von der Ressourcengruppe für die übergeordnete Ressource unterscheidet.

## <a name="solution"></a>Lösung

Nehmen Sie eine Abhängigkeit auf, um diesen Fehler zu beheben, wenn die übergeordnete und die untergeordnete Ressource in der gleichen Vorlage bereitgestellt werden.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Legen Sie keine Abhängigkeit fest, um diesen Fehler zu beheben, wenn die übergeordnete Ressource bereits zuvor in einer anderen Vorlage bereitgestellt wurde. Stellen Sie stattdessen die untergeordnete Ressource in der gleichen Ressourcengruppe bereit, und geben Sie den Namen der übergeordneten Ressource an.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Weitere Informationen finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in Azure Resource Manager-Vorlagen](../templates/resource-dependency.md).
