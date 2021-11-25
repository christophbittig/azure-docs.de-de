---
title: Fehler wegen ungültiger Vorlage
description: Hier wird beschrieben, wie Fehler aufgrund ungültiger Vorlagen beim Bereitstellen Bicep-Dateien oder von Azure Resource Manager-Vorlagen (ARM-Vorlagen) behoben werden.
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.openlocfilehash: d5bd7494300ac8861ebd85478e380132acbac007
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550663"
---
# <a name="resolve-errors-for-invalid-template"></a>Beheben von Fehlern aufgrund ungültiger Vorlagen

In diesem Artikel wird beschrieben, wie Fehler aufgrund ungültiger Vorlagen für Bicep-Dateien und Azure Resource Manager-Vorlagen (ARM-Vorlagen) behoben werden.

## <a name="symptom"></a>Symptom

Wenn eine Vorlage bereitgestellt wird, erhalten Sie folgenden Fehler:

```Output
Code=InvalidTemplate
Message=<varies>
```

Die Fehlermeldung hängt vom Typ des Fehlers ab.

## <a name="cause"></a>Ursache

Dieser Fehler kann aus verschiedenen Arten von Fehlern entstehen. Normalerweise handelt es sich um einen Syntax- oder Strukturfehler in der Vorlage.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Lösung 1 – Syntaxfehler

Wenn Sie eine Fehlermeldung erhalten, die auf eine fehlerhafte Vorlagenüberprüfung hinweist, ist in Ihrer Vorlage möglicherweise ein Syntaxproblem vorhanden.

```Output
Code=InvalidTemplate
Message=Deployment template validation failed
```

Syntaxfehler können auftreten, da Vorlagenausdrücke viele Elemente enthalten. Die Namenszuweisung für ein Speicherkonto umfasst beispielsweise Paare aus einfachen oder doppelten Anführungszeichen, geschweiften Klammern, eckigen Klammern und runden Klammern. Ausdrücke enthalten auch Funktionen und Zeichen wie Dollarzeichen, Kommas und Punkte.


# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

---

Wenn Sie diese Art von Fehler erhalten, überprüfen Sie die Ausdruckssyntax. Verwenden Sie zum Identifizieren von Vorlagenfehlern [Visual Studio Code](https://code.visualstudio.com) mit der neuesten [Bicep-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) oder [Azure Resource Manager-Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Lösung 2 – falsche Segmentlängen

Ein weiterer Fehler vom Typ „Ungültige Vorlage“ tritt auf, wenn der Ressourcenname nicht im richtigen Format vorliegt. Informationen zum Beheben dieses Fehlers finden Sie unter [Beheben von Fehlern bei nicht übereinstimmenden Namen und Typen](error-invalid-name-segments.md).

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>Lösung 3 – Parameter ungültig

Sie können die zulässigen Werte eines Parameters in einer Vorlage angeben. Wenn Sie bei der Bereitstellung einen Wert angeben, der keinem der zulässigen Werte entspricht, erhalten Sie eine Fehlermeldung der folgenden Art:

```Output
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Überprüfen Sie die Vorlage auf die zulässigen Werte des Parameters, und verwenden Sie während der Bereitstellung einen zulässigen Wert. Weitere Informationen finden Sie unter zulässige Werte in [Bicep](../bicep/parameters.md#allowed-values) oder [ARM-Vorlagen](../templates/parameters.md#allowed-values).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Lösung 4 – zu viele Zielressourcengruppen

Dieser Fehler wird in früheren Bereitstellungen möglicherweise angezeigt, weil in einer einzigen Bereitstellung nur fünf Zielressourcengruppen vorhanden sein durften. Im Mai 2020 wurde dieses Limit auf 800 Ressourcengruppen erhöht. Weitere Informationen finden Sie unter Bereitstellen für mehrere Ressourcengruppen in [Bicep](../bicep/deploy-to-resource-group.md#deploy-to-multiple-resource-groups) oder [ARM-Vorlagen](../templates/deploy-to-resource-group.md#deploy-to-multiple-resource-groups).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Lösung 5 – Ringabhängigkeit erkannt

Sie erhalten diesen Fehler, wenn Ressourcen auf eine Weise voneinander abhängig sind, die das Starten der Bereitstellung verhindert. Eine Kombination aus Abhängigkeiten bewirkt, dass zwei oder mehr Ressourcen auf andere Ressourcen warten, die sich ebenfalls im Wartezustand befinden. Beispielsweise kann „resource1“ von „resource3“, „resource2“ von „resource1“ und „resource3“ von „resource2“ abhängig sein. Dieses Problem lässt sich normalerweise beheben, indem nicht benötigte Abhängigkeiten entfernt werden.

Bicep erstellt eine implizite Abhängigkeit, wenn eine Ressource den symbolischen Namen einer anderen Ressource verwendet. Eine explizite Abhängigkeit mit `dependsOn` ist in der Regel nicht erforderlich. Weitere Informationen finden Sie unter [Bicep-Abhängigkeiten](../bicep/resource-declaration.md#dependencies).

So beheben Sie eine Ringabhängigkeit

1. Suchen Sie in Ihrer Vorlage nach der Ressource, die in der Ringabhängigkeit angegeben ist.
1. Sehen Sie sich für diese Ressource die `dependsOn`-Eigenschaft und alle Vorkommen der Funktion `reference` an, um zu ermitteln, von welchen Ressourcen sie abhängig ist.
1. Überprüfen Sie diese Ressourcen, um zu ermitteln, von welchen Ressourcen sie abhängig sind. Verfolgen Sie die Abhängigkeiten, bis Sie auf eine Ressource stoßen, die von der Originalressource abhängig ist.
1. Überprüfen Sie für die an der Ringabhängigkeit beteiligten Ressourcen gründlich alle Vorkommen der `dependsOn`-Eigenschaft, um alle Abhängigkeiten zu identifizieren, die nicht benötigt werden. Entfernen Sie diese Abhängigkeiten. Wenn Sie unsicher sind, ob eine Abhängigkeit erforderlich ist, können Sie versuchen, sie zu entfernen.
1. Stellen Sie die Vorlage erneut bereit.

Das Entfernen von Werten aus der `dependsOn`-Eigenschaft kann zu Fehlern beim Bereitstellen der Vorlage führen. Fügen Sie die Abhängigkeit wieder in die Vorlage ein, wenn ein Fehler auftritt.

Falls sich die Ringabhängigkeit mit dieser Vorgehensweise nicht beseitigen lässt, können Sie erwägen, einen Teil Ihrer Bereitstellungslogik in untergeordnete Ressourcen zu verschieben (z.B. Erweiterungen oder Konfigurationseinstellungen). Konfigurieren Sie diese untergeordneten Ressourcen so, dass sie nach den an der Ringabhängigkeit beteiligten Ressourcen bereitgestellt werden. Nehmen wir beispielsweise an, Sie stellen zwei virtuelle Computer bereit, müssen aber Eigenschaften festlegen, die auf den jeweils anderen verweisen. Sie können diese in der folgenden Reihenfolge bereitstellen:

1. VM1
1. VM2
1. Die Erweiterung auf VM1 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM1 fest, die sie von VM2 abruft.
1. Die Erweiterung auf VM2 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM2 fest, die sie von VM1 abruft.

Diese Vorgehensweise funktioniert auch für App Service-Apps. Erwägen Sie, Konfigurationswerte in eine untergeordnete Ressource der App-Ressource zu verschieben. Sie können zwei Web-Apps in der folgenden Reihenfolge bereitstellen:

1. webapp1
1. webapp2
1. Die Konfiguration für „webapp1“ ist von „webapp1“ und „webapp2“ abhängig. Sie enthält App-Einstellungen mit Werten aus „webapp2“.
1. Die Konfiguration für „webapp2“ ist von „webapp1“ und „webapp2“ abhängig. Sie enthält App-Einstellungen mit Werten aus „webapp1“.
