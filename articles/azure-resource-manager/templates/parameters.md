---
title: Parameter in Vorlagen
description: Beschreibt, wie Parameter in einer Azure Resource Manager-Vorlage (ARM-Vorlage) definiert werden.
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 5c94dc3f4d37fa6c08e29e03e88dd3ba54e68271
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625523"
---
# <a name="parameters-in-arm-templates"></a>Parameter in ARM-Vorlagen

Dieser Artikel beschreibt, wie Sie Parameter in Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) definieren und verwenden. Durch Bereitstellen verschiedener Werte für Parameter können Sie eine Vorlage für verschiedene Umgebungen wiederverwenden.

Resource Manager löst Parameterwerte vor Beginn der Bereitstellungsvorgänge auf. Jedes Vorkommen des Parameters in der Vorlage wird von Resource Manager durch den aufgelösten Wert ersetzt.

Jeder Parameter muss auf einen der [Datentypen](data-types.md) festgelegt werden.

## <a name="minimal-declaration"></a>Minimale Deklaration

Jeder Parameter benötigt mindestens einen Namen und einen Typ.

Wenn Sie eine Vorlage über das Azure-Portal bereitstellen, werden Parameternamen mit Camel-Case-Schreibweise in durch Leerzeichen getrennte Namen umgewandelt. Beispielsweise wird *demoString* im folgenden Beispiel als *Demo String* angezeigt. Weitere Informationen finden Sie unter [Verwenden einer Bereitstellungsschaltfläche zum Bereitstellen von Vorlagen aus dem GitHub-Repository](./deploy-to-azure-button.md) und [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](./deploy-portal.md).

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

## <a name="secure-parameters"></a>Sichere Parameter

Sie können Zeichenfolgen- oder Objektparameter als sicher kennzeichnen. Der Wert eines sicheren Parameters wird weder im Bereitstellungsverlauf gespeichert noch protokolliert.

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

## <a name="allowed-values"></a>Zulässige Werte

Sie können für einen Parameter zulässige Werte definieren. Diese werden in einem Array bereitgestellt. Wenn für den Parameter ein Wert übergeben wird, der nicht zu den zulässigen Werten gehört, schlägt die Bereitstellung während der Überprüfung fehl.

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

## <a name="default-value"></a>Standardwert

Sie können für einen Parameter keinen Standardwert angeben. Der Standardwert wird verwendet, wenn während der Bereitstellung kein Wert angegeben wird.

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Wenn Sie für den Parameter einen Standardwert zusammen mit anderen Eigenschaften angeben möchten, verwenden Sie folgende Syntax.

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

Ausdrücke können mit dem Standardwert verwendet werden. Im Parameterabschnitt kann weder die [reference](template-functions-resource.md#reference)-Funktion noch eine der [list](template-functions-resource.md#list)-Funktionen verwendet werden. Diese Funktionen rufen den Laufzeitstatus einer Ressource ab und können nicht vor der Bereitstellung ausgeführt werden, wenn Parameter aufgelöst werden.

In Ausdrücken dürfen keine anderen Parametereigenschaften verwendet werden.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

Sie können einen anderen Parameterwert verwenden, um einen Standardwert zu erstellen. Mit der folgenden Vorlage wird aus dem Websitenamen ein Name für den Hostplan erstellt.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

## <a name="length-constraints"></a>Längenbeschränkungen

Sie können die minimale und maximale Länge von Zeichenfolgen- und Arrayparametern angeben. Sie können eine oder beide Einschränkungen festlegen. Bei Zeichenfolgen gibt die Länge die Anzahl der Zeichen an. Bei Arrays gibt die Länge die Anzahl der Elemente im Array an.

Im folgenden Beispiel werden zwei Parameter deklariert. Ein Parameter ist für den Namen eines Speicherkontos, der 3–24 Zeichen enthalten muss. Der andere Parameter ist ein Array, das 1–5 Elemente aufweisen muss.

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

## <a name="integer-constraints"></a>Ganzzahlige Einschränkungen

Sie können die minimalen und maximalen Werte für ganzzahlige Parameter festlegen. Sie können eine oder beide Einschränkungen festlegen.

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

## <a name="description"></a>BESCHREIBUNG

Sie können einem Parameter eine Beschreibung hinzufügen, damit Benutzer Ihrer Vorlage leichter nachvollziehen können, welcher Wert bereitgestellt werden soll. Wenn Sie die Vorlage über das Portal bereitstellen, wird der Text, den Sie in der Beschreibung angeben, automatisch als Tipp für diesen Parameter verwendet. Beschreibungen sollten nur hinzugefügt werden, wenn der Text mehr Informationen bietet, als aus dem Parameternamen abgeleitet werden können.

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

## <a name="use-parameter"></a>Verwenden eines Parameters

Um auf den Wert eines Parameters zu verweisen, verwenden Sie die Funktion[parameters](template-functions-deployment.md#parameters). Im folgenden Beispiel wird ein Parameterwert für den Namen eines Schlüsseltresors verwendet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2021-06-01-preview",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

## <a name="objects-as-parameters"></a>Objekte als Parameter

Sie können in Beziehung stehende Werte organisieren, indem sie sie als Objekt übergeben. Diese Vorgehensweise verringert auch die Anzahl der Parameter in der Vorlage.

Das folgende Beispiel zeigt einen Parameter, bei dem es sich um ein Objekt handelt. Der Standardwert gibt die erwarteten Eigenschaften für das Objekt an. Diese Eigenschaften werden in der Definition der bereitzustellenden Ressource verwendet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2021-02-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

## <a name="example-templates"></a>Beispielvorlagen

In den folgenden Beispielen werden Szenarien für die Verwendung von Parametern veranschaulicht.

|Vorlage  |Beschreibung  |
|---------|---------|
|[Parameter mit Funktionen für Standardwerte](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Zeigt, wie Vorlagenfunktionen verwendet werden, wenn Sie Standardwerte für Parameter definieren. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |
|[Parameterobjekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Veranschaulicht die Verwendung eines Objekts für einen Parameter. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verfügbaren Eigenschaften für Parameter finden Sie unter [Verstehen der Struktur und Syntax von ARM-Vorlagen](./syntax.md).
* Weitere Informationen zum Übergeben von Parameterwerten als Datei finden Sie unter [Erstellen einer Resource Manager-Parameterdatei](parameter-files.md).
* Empfehlungen zum Erstellen von Parametern finden Sie unter [Bewährte Methoden: Parameter](./best-practices.md#parameters).
