---
title: Fehler in Bezug auf Speicherkontonamen
description: Beschreibt Fehler, die auftreten können, wenn Sie einen Speicherkontonamen in einer Azure Resource Manager-Vorlage (ARM-Vorlage) oder einer Bicep-Datei angeben.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: 8f26efffac8768abb2279722fb1b5dbf174072f3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487755"
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep verwendet [Zeichenfolgeninterpolation](../bicep/bicep-functions-string.md#concat) mit [uniqueString](../bicep/bicep-functions-string.md#uniquestring).

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

ARM-Vorlagen verwenden [concat](../templates/template-functions-string.md#concat) mit [uniqueString](../templates/template-functions-string.md#uniquestring).

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

---

Stellen Sie sicher, dass der Name des Speicherkontos maximal 24 Zeichen lang ist. Die `uniqueString`-Funktion gibt 13 Zeichen zurück. Wenn Sie ein Präfix oder Suffix verketten möchten, geben Sie einen Wert an, der maximal 11 Zeichen lang ist.

In den folgenden Beispielen wird ein Parameter verwendet, der ein Präfix mit maximal 11 Zeichen erstellt.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

# <a name="json"></a>[JSON](#tab/json)

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

---

Anschließend verketten Sie den Parameterwert mit dem `uniqueString`-Wert, um einen Speicherkontonamen zu erstellen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

---
