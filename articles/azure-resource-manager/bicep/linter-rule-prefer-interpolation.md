---
title: 'Linterregel: Interpolation bevorzugen'
description: 'Linterregel: Interpolation bevorzugen'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 952359c7c258c084e19d6faf152536a3e50212b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164479"
---
# <a name="linter-rule---prefer-interpolation"></a>Linterregel: Interpolation bevorzugen

Diese Regel sucht nach Verwendungen der CONCAT-Funktion, die durch die Zeichenfolgeninterpolation ersetzt werden können.

## <a name="returned-code"></a>Zurückgegebener Code

`prefer-interpolation`

## <a name="solution"></a>Lösung

Verwenden Sie die Zeichenfolgeninterpolation anstelle der CONCAT-Funktion.

Das folgende Beispiel besteht diesen Test nicht, da die concat-Funktion verwendet wird.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

Sie können dies beheben, indem Sie CONCAT durch die Zeichenfolgeninterpolation ersetzen. Im folgenden Beispiel ist der Test erfolgreich.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Verwenden des Bicep-Linters](./linter.md).
