---
title: Linterregel – keine nicht verwendeten Parameter
description: Linterregel – keine nicht verwendeten Parameter
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 2e9c602a28b2ba19d8c0a38b1bba3ecc521e6d77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017842"
---
# <a name="linter-rule---no-unused-parameters"></a>Linterregel – keine nicht verwendeten Parameter

Diese Regel ermittelt Parameter, auf die in der Bicep-Datei nicht verwiesen wird.

## <a name="returned-code"></a>Zurückgegebener Code

`no-unused-params`

## <a name="solution"></a>Lösung

Löschen Sie alle Parameter, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Parameter, die nicht in der Vorlage verwendet werden. Das Löschen nicht verwendeter Parameter vereinfacht zudem die Bereitstellung Ihrer Vorlage, da Sie keine unnötigen Werte angeben müssen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über den Linter finden Sie unter [Verwendung des Bicep-Linters](./linter.md).
