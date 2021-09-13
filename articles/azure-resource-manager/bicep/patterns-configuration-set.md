---
title: Muster für Konfigurationssatz
description: Beschreibt das Konfigurationssatzmuster.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: b2ccac6f646304d7d530e616a57c8490e17aba22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446833"
---
# <a name="configuration-set-pattern"></a>Muster für Konfigurationssatz

Anstatt viele einzelne Parameter zu definieren, erstellen Sie vordefinierte Wertesätze. Wählen Sie während der Bereitstellung den zu verwendenden Wertesatz aus.

## <a name="context-and-problem"></a>Kontext und Problem

Eine einzelne Bicep-Datei definiert häufig viele Ressourcen. Jede Ressource muss abhängig von der Umgebung, in der Sie sie bereitstellen, möglicherweise eine andere Konfiguration verwenden. Beispielsweise können Sie eine Bicep-Datei erstellen, die einen App Service-Plan und eine App sowie ein Speicherkonto bereitgestellt. Jede dieser Ressourcen verfügt über mehrere Optionen, die sich auf deren Kosten, Verfügbarkeit und Resilienz auswirken. Für Produktionsumgebungen sollten Sie einen Konfigurationssatz mit der Priorisierung für Hochverfügbarkeit und Resilienz verwenden. Für Nicht-Produktionsumgebungen sollten Sie einen anderen Konfigurationssatz verwenden, der die Kostenreduzierung priorisiert.

Sie können Parameter für jede Konfigurationseinstellung erstellen, allerdings hat dies einige Nachteile:

- Dieser Ansatz macht es den Vorlagenbenutzer etwas aufwändiger, da sie die Werte, die für jede Ressource verwendet werden sollen, und die Auswirkungen der einzelnen Parametereinstellungen verstehen müssen.
- Die Anzahl der Parameter in Ihrer Vorlage erhöht sich mit jeder neuen Ressource, die Sie definieren.
- Benutzer können Kombinationen von Parameterwerten auswählen, die nicht getestet wurden oder nicht ordnungsgemäß funktionieren.

## <a name="solution"></a>Lösung

Erstellen Sie einen einzelnen Parameter, um den Umgebungstyp anzugeben. Verwenden Sie eine Variable, um die Konfiguration für jede Ressource basierend auf dem Wert des Parameters automatisch auszuwählen.

> [!NOTE]
> Dieser Ansatz wird manchmal auch _T-Shirt-Dimensionierung_ genannt. Wenn Sie ein T-Shirt kaufen, haben Sie für Länge, Breite, Ärmel usw. nicht viele Optionen. Sie wählen einfach zwischen kleinen, mittleren und großen Größen und der Designer des T-Shirts hat diese Maße basierend auf diesen Größen vordefiniert.

## <a name="example"></a>Beispiel

Angenommen, Sie verfügen über eine Vorlage, die in zwei Arten von Umgebungen bereitgestellt werden kann: Nichtproduktion und Produktion. Je nach Umgebungstyp ist die Konfiguration, die Sie benötigen, anders:

| Eigenschaft | Nichtproduktionsumgebungen | Produktionsumgebungen |
|-|-|-|
| **App Service-Plan** |
| SKU-Name | S2 | P2V3 |
| Kapazität (Anzahl von Instanzen) | 1 | 3 |
| **App Service-App** |
| Always On | Disabled | Aktiviert |
| **Speicherkonto** |
| SKU-Name | Standard_LRS | Standard_ZRS |

Sie können das Konfigurationssatzmuster für diese Vorlage verwenden.

Akzeptieren Sie einen einzelnen Parameter, der den Umgebungstyp angibt, z. B. Produktion oder Nichtproduktion. Verwenden Sie den Parameterdecorator `@allowedValues`, um sicherzustellen, dass die Benutzer Ihrer Vorlage nur die Werte angeben, die Sie erwarten:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

Erstellen Sie dann eine _Zuordnungsvariable_, bei der es sich um ein Objekt handelt, das die spezifische Konfiguration abhängig vom Umgebungstyp definiert. Beachten Sie, dass die Variable über zwei Objekte mit dem Namen `Production` und `NonProduction` verfügt. Diese Namen stimmen mit den zulässigen Werten für den Parameter im vorherigen Beispiel überein:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

Wenn Sie die Ressourcen definieren, verwenden Sie die Konfigurationszuordnung, um die Ressourceneigenschaften zu definieren:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>Weitere Überlegungen

- Ziehen Sie es in Betracht, die Eigenschaften in Ihrer Zuordnungsvariablen nach Ressource zu gruppieren, um deren Definition zu vereinfachen.
- In Ihrer Zuordnungsvariablen können Sie sowohl einzelne Eigenschaftswerte (wie die `alwaysOn`-Eigenschaft im Beispiel) als auch Objektvariablen definieren, die eine Objekteigenschaft festlegen (wie die SKU-Eigenschaften im Beispiel).
- Ziehen Sie es in Betracht, einen Konfigurationssatz mit [Ressourcenbedingungen](conditional-resource-deployment.md) zu verwenden. Dadurch kann Ihr Bicep-Code bestimmte Ressourcen für bestimmte Umgebungen bereitstellen, und für andere nicht.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über das Dateimuster für freigegebene Variablen.](patterns-shared-variable-file.md)
