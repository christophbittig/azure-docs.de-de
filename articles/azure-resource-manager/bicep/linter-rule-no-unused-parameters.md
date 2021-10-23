---
title: Linterregel – keine nicht verwendeten Parameter
description: Linterregel – keine nicht verwendeten Parameter
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: d02d13c44fd6cc6a174f629c8f3e32777bd643af
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161553"
---
# <a name="linter-rule---no-unused-parameters"></a>Linterregel – keine nicht verwendeten Parameter

Diese Regel ermittelt Parameter, auf die in der Bicep-Datei nirgendwo verwiesen wird.

## <a name="returned-code"></a>Zurückgegebener Code

`no-unused-params`

## <a name="solution"></a>Lösung

Löschen Sie alle Parameter, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Parameter, die nicht in der Vorlage verwendet werden. Das Löschen nicht verwendeter Parameter vereinfacht zudem die Bereitstellung Ihrer Vorlage, da Sie keine unnötigen Werte angeben müssen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Linter finden Sie unter [Bicep-Linter verwenden](./linter.md).
