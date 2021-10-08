---
title: Datei mit freigegebenen Variablen (Muster)
description: Beschreibt das Muster „Datei mit freigegebenen Variablen“.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: eb9a24c0c5b6b7bd037fe5da13fe108878f55a15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648314"
---
# <a name="shared-variable-file-pattern"></a>Datei mit freigegebenen Variablen (Muster)

Verringert die Wiederholung freigegebener Werte in Ihren Bicep-Dateien. Stattdessen laden Sie diese Werte aus einer freigegebenen JSON-Datei in Ihre Bicep-Datei. Bei Verwendung von Arrays verketten Sie die freigegebenen Werte mit bereitstellungsspezifischen Werten in Ihrem Bicep-Code.

## <a name="context-and-problem"></a>Kontext und Problem

Wenn Sie Ihren Bicep-Code schreiben, verfügen Sie möglicherweise über gängige Variablen, die Sie über eine Gruppe von Bicep-Dateien hinweg wiederverwenden. Sie könnten die Werte jedes Mal duplizieren, wenn Sie die Ressource deklarieren, z. B. durch Kopieren und Einfügen der Werte zwischen Ihren Bicep-Dateien. Dieser Ansatz ist jedoch fehleranfällig, und wenn Sie Änderungen vornehmen müssen, müssen Sie jede Ressourcendefinition aktualisieren, um sie mit den anderen Ressourcen synchron zu halten.

Darüber hinaus verfügen Sie, wenn Sie mit Variablen arbeiten, die als Arrays definiert sind, möglicherweise über einen Satz gemeinsamer Werte für mehrere Bicep-Dateien und müssen außerdem bestimmte Werte für die Ressource hinzufügen, die Sie bereitstellen. Wenn Sie die freigegebenen Variablen mit den ressourcenspezifischen Variablen kombinieren, ist es für andere schwieriger, die Unterscheidung zwischen den beiden Kategorien von Variablen zu verstehen.

## <a name="solution"></a>Lösung

Erstellen Sie eine JSON-Datei, die die Variablen enthält, die Sie freigeben müssen. Verwenden Sie die [`json()`-Funktion](bicep-functions-object.md#json) und die [`loadTextContent()`-Funktion](bicep-functions-files.md#loadtextcontent) zum Laden der Datei und zum Zugriff auf die Variablen. Verwenden Sie für Arrayvariablen die [`concat()`-Funktion](bicep-functions-array.md#concat), um die freigegebenen Werte mit den benutzerdefinierten Werten für die spezifische Ressource zu kombinieren.

## <a name="example-1-naming-prefixes"></a>Beispiel 1: Namenspräfixe

Angenommen, Sie verfügen über mehrere Bicep-Dateien, die Ressourcen definieren. Sie müssen ein konsistentes Namenspräfix für alle Ihre Ressourcen verwenden.

Definieren Sie eine JSON-Datei, die die gängigen Namenspräfixe enthält, die für Ihr gesamtes Unternehmen gelten:

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

Deklarieren Sie in Ihrer Bicep-Datei eine Variable, die die freigegebenen Namenspräfixe importiert:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

Wenn Sie Ihre Ressourcennamen definieren, verwenden Sie die Zeichenfolgeninterpolation, um die freigegebenen Namenspräfixe mit eindeutigen Namenssuffixen zu verketten:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>Beispiel 2: Netzwerksicherheitsgruppen-Regeln

Angenommen, Sie verfügen über mehrere Bicep-Dateien, die ihre eigenen Netzwerksicherheitsgruppen (NSG) definieren. Sie verfügen über einen gemeinsamen Satz von Sicherheitsregeln, die auf jede Netzwerksicherheitsgruppe angewendet werden müssen, und ferner verfügen Sie über anwendungsspezifische Regeln, die hinzugefügt werden müssen.

Definieren Sie eine JSON-Datei, die die gängigen Sicherheitsregeln enthält, die für Ihr gesamtes Unternehmen gelten:

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

Deklarieren Sie in Ihrer Bicep-Datei eine Variable, die die freigegebenen Sicherheitsregeln importiert:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

Erstellen Sie ein Variablenarray, das die benutzerdefinierten Regeln für diese spezifische Netzwerksicherheitsgruppe darstellt:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

Definieren Sie die Netzwerksicherheitsgruppen-Ressource. Verwenden Sie die `concat()`-Funktion, um die beiden Arrays miteinander zu kombinieren und die `securityRules`-Eigenschaft festzulegen:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>Weitere Überlegungen

- Wenn Sie diesen Ansatz verwenden, wird die JSON-Datei in die von Bicep generierte ARM-Vorlage aufgenommen. Die von Bicep generierten JSON-ARM-Vorlagen besitzen einen Dateigrenzwert von 4 MB, weshalb es wichtig ist, die Verwendung großer freigegebener Variablendateien zu vermeiden.
- Stellen Sie sicher, dass Ihre freigegebenen Variablenarrays nicht mit den in jeder Bicep-Datei angegebenen Arraywerten in Konflikt stehen. Wenn Sie beispielsweise das Konfigurationsgruppenmuster zum Definieren von Netzwerksicherheitsgruppen verwenden, stellen Sie sicher, dass Sie nicht mehrere Regeln haben, die dieselbe Priorität und Richtung definieren.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über das Konfigurationsgruppenmuster](patterns-configuration-set.md).
