---
title: 'Linterregel: Administratorbenutzername darf nicht literal sein'
description: 'Linterregel: Administratorbenutzername darf kein Literal sein'
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: fe13ed227b0f7c7d5a7b738ed675f6c5d54eded2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167785"
---
# <a name="linter-rule---admin-user-name-should-not-be-literal"></a>Linterregel: Administratorbenutzername darf nicht literal sein

Diese Regel ermittelt, ob ein Administratorbenutzername auf einen Literalwert festgelegt ist.

## <a name="returned-code"></a>Zurückgegebener Code

`adminusername-should-not-be-literal`

## <a name="solution"></a>Lösung

Verwenden Sie weder einen Literalwert noch einen Ausdruck, der zu einem Literalwert ausgewertet wird. Erstellen Sie stattdessen einen Parameter für den Benutzernamen, und weisen Sie ihn dem Administratorbenutzernamen zu.

Das folgende Beispiel besteht diesen Test nicht, weil der Benutzername ein Literalwert ist.

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: 'adminUsername'
    }
  }
}
```

Das nächste Beispiel besteht diesen Test nicht, weil der Ausdruck zu einem Literalwert ausgewertet wird, wenn der Standardwert verwendet wird.

```bicep
var defaultAdmin = 'administrator'
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: defaultAdmin
    }
  }
}
```

Dieses Beispiel besteht den Test.

```bicep
@secure()
param adminUsername string
param location string
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: adminUsername
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Bicep-Linter verwenden](./linter.md).
