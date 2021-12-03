---
title: Ungültige Ressourcennamen- und -typsegmente
description: Beschreibt, wie ein Fehler behoben wird, wenn der Ressourcenname und -typ nicht dieselbe Anzahl von Segmenten haben.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: c4b04c78f84dada1e41289d1523b9ee2c49bd4e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404341"
---
# <a name="resolve-errors-for-resource-name-and-type-mismatch"></a>Beheben von Fehlern bei Ressourcennamen- und -typkonflikten

In diesem Artikel wird beschrieben, wie Sie den Fehler beheben, wenn das Format des Ressourcennamens nicht mit dem Format des Ressourcentyps übereinstimmt.

## <a name="symptom"></a>Symptom

Beim Bereitstellen einer Vorlage erhalten Sie einen Fehler mit dem Fehlercode **InvalidTemplate**. Die Meldung gibt an, dass Ressourcentyp und -name nicht übereinstimmen. Es wird vorgeschlagen, die Anzahl der Segmente im Namen zu korrigieren.

## <a name="cause"></a>Ursache

Ein Ressourcentyp enthält den Namespace des Ressourcenanbieters und mindestens ein Segment für Typen. Jedes Segment stellt eine Ebene in der Ressourcenhierarchie dar und wird durch einen Schrägstrich getrennt.

```
{resource-provider-namespace}/{type-segment-1}/{type-segment-2}
```

Der Ressourcenname enthält mindestens ein Segment, mehrere werden durch Schrägstriche getrennt. Die Anzahl der Segmente muss mit der Anzahl im Ressourcentyp übereinstimmen.

```
{name-segment-1}/{name-segment-2}
```

Wenn Ressourcentyp und -name eine andere Anzahl von Segmenten enthalten, erhalten Sie diesen Fehler.

## <a name="solution"></a>Lösung

Stellen Sie sicher, dass Sie die Ebene des Ressourcentyps verstehen. Eine Schlüsseltresorressource hat beispielsweise den vollqualifizierten Ressourcentyp `Microsoft.KeyVault/vaults`. Sie können den Namespace des Ressourcenanbieters (**Microsoft.KeyVault**) ignorieren und sich auf den Typ **vaults** (Tresore) konzentrieren. Er enthält Segment.

Ein Schlüsseltresorgeheimnis ist eine untergeordnete Ressource des Tresors. Es verfügt über den vollqualifizierten Ressourcentyp `Microsoft.KeyVault/vaults/secrets`. Dieser Ressourcentyp enthält zwei Segmente (**vaults/secrets**).

Um einen Namen für den Schlüsseltresor anzugeben, geben Sie nur ein Segment an, z. B. `examplevault123`. Um einen Namen für das Geheimnis anzugeben, geben Sie zwei Segmente an, z. B. `examplevault123/examplesecret`. Das erste Segment gibt den Schlüsseltresor an, in dem dieses Geheimnis gespeichert ist.

Das folgende Beispiel zeigt ein gültiges Format für den Ressourcennamen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
}
```

---

Wenn Sie einen Namen mit mehr als einem Segment angegeben haben, wird ein **Fehler** angezeigt.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'contoso/examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "contoso/examplevault123",
  ...
}
```

---

Wenn Sie eine untergeordnete Ressource in der übergeordneten Ressource schachteln, geben Sie nur das zusätzliche Segment an. Der vollständige Ressourcentyp und -name enthalten weiterhin die Werte aus der übergeordneten Ressource, sie werden jedoch für Sie erstellt. Im folgenden Beispiel ist der Typ `secrets`, und der Name lautet `examplesecret`.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
  resource kvsecret 'secrets' = {
    name: 'examplesecret'
    properties: {
     value: secretValue
    }
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
  "resources": [
    {
      "type": "secrets",
      "apiVersion": "2019-09-01",
      "name": "examplesecret",
      "properties": {
        "value": "[parameters('secretValue')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
      ]
    }
  ]
}
```

---

Wenn Sie die untergeordnete Ressource außerhalb des übergeordneten Elements definieren, geben Sie den vollständigen Ressourcentyp an. Geben Sie für JSON den vollständigen Ressourcennamen an.

Verwenden Sie für Bicep die Eigenschaft `parent`, und geben Sie den symbolischen Namen der übergeordneten Ressource an. Wenn Sie die übergeordnete Eigenschaft verwenden, wird der vollständige Name für Sie erstellt, also geben Sie den Namen der untergeordneten Ressource als einzelnes Segment an.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kvsecret 'Microsoft.KeyVault/vaults/secrets@2021-06-01-preview' = {
  name: 'examplesecret'
  parent: kv
  properties: {
     value: secretValue
  }
}

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults/secrets",
  "apiVersion": "2019-09-01",
  "name": "examplevault123/examplesecret",
  "properties": {
    "value": "[parameters('secretValue')]"
  },
  "dependsOn": [
    "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
  ]
},
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
```

---

Weitere Informationen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen in Bicep](../bicep/child-resource-name-type.md) oder [Festlegen von Name und Typ für untergeordnete Ressourcen in ARM-Vorlagen](../templates/child-resource-name-type.md).