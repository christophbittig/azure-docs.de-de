---
title: Fehler in Bezug auf Speicherkontonamen
description: Beschreibt Fehler, die auftreten können, wenn Sie einen Speicherkontonamen in einer Azure Resource Manager-Vorlage (ARM-Vorlage) oder einer Bicep-Datei angeben.
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.openlocfilehash: 78bf097ccaf5f826dc20a11ee36375111b1a7354
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095178"
---
# <a name="resolve-errors-for-storage-account-names"></a>Beheben von Fehlern bei Speicherkontonamen

In diesem Artikel werden Benennungsfehler beschrieben, die auftreten können, wenn Sie ein Speicherkonto mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) oder einer Bicep-Datei bereitstellen.

## <a name="symptom"></a>Symptom

Wenn Ihr Speicherkontoname unzulässige Zeichen enthält, z. B. einen Großbuchstaben oder ein Ausrufezeichen, erhalten Sie einen Fehler:

```Output
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Für Speicherkonten müssen Sie einen Ressourcennamen angeben, der in Azure eindeutig ist. Wenn Sie keinen eindeutigen Namen angeben, erhalten Sie einen Fehler:

```Output
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Wenn Sie ein Speicherkonto mit dem gleichen Namen wie ein vorhandenes Speicherkonto in Ihrem Abonnement, aber an einem anderen Speicherort bereitstellen, erhalten Sie einen Fehler. Der Fehler gibt an, dass das Speicherkonto bereits an einem anderen Speicherort vorhanden ist. Löschen Sie das vorhandene Speicherkonto, oder verwenden Sie den gleichen Speicherort wie für das vorhandene Speicherkonto.

## <a name="cause"></a>Ursache

Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Großbuchstaben oder Sonderzeichen sind unzulässig. Der Name muss eindeutig sein.

## <a name="solution"></a>Lösung

Sie können einen eindeutigen Namen erstellen, indem Sie Ihre Benennungskonvention mit dem Ergebnis der `uniqueString`-Funktion verketten.

ARM-Vorlagen verwenden [concat](../templates/template-functions-string.md#concat) mit [uniqueString](../templates/template-functions-string.md#uniquestring).

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Bicep verwendet [Zeichenfolgeninterpolation](../bicep/bicep-functions-string.md#concat) mit [uniqueString](../bicep/bicep-functions-string.md#uniquestring).

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

Stellen Sie sicher, dass der Name des Speicherkontos maximal 24 Zeichen lang ist. Die `uniqueString`-Funktion gibt 13 Zeichen zurück. Wenn Sie ein Präfix oder Suffix verketten möchten, geben Sie einen Wert an, der maximal 11 Zeichen lang ist.

In den folgenden Beispielen wird ein Parameter verwendet, der ein Präfix mit maximal 11 Zeichen erstellt.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The prefix value for the storage account name."
    }
  }
}
```

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

Anschließend verketten Sie den Parameterwert mit dem `uniqueString`-Wert, um einen Speicherkontonamen zu erstellen.

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```
