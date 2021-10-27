---
title: Linterregel – keine nicht verwendeten Variablen
description: Linterregel – keine nicht verwendeten Variablen
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 3b59b8d72d12c365952dd8eef2c65a0f851ea561
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161876"
---
# <a name="linter-rule---no-unused-variables"></a>Linterregel – keine nicht verwendeten Variablen

Diese Regel sucht nach Variablen, auf die in der Bicep-Datei nirgendwo verwiesen wird.

## <a name="returned-code"></a>Zurückgegebener Code

`no-unused-vars`

## <a name="solution"></a>Lösung

Löschen Sie alle Variablen, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Variablen, die nicht in der Vorlage verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Bicep-Linter verwenden](./linter.md).
