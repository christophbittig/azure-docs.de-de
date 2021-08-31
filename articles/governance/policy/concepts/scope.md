---
title: Erläuterungen zum Bereich in Azure Policy
description: Beschreibt das Konzept des Bereichs in Azure Resource Manager und wie es auf Azure Policy angewendet wird, um zu steuern, welche Ressourcen von Azure Policy ausgewertet werden.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: b83acebe3f24b7dac1c506a598c641035132fb30
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356575"
---
# <a name="understand-scope-in-azure-policy"></a>Erläuterungen zum Bereich in Azure Policy

Es gibt eine Reihe von Einstellungen, die bestimmen, welche Ressourcen ausgewertet werden können und welche Ressourcen von Azure Policy ausgewertet werden. Das primäre Konzept für diese Steuerungen ist der _Bereich_. Der Bereich in Azure Policy basiert auf der Funktionsweise des Bereichs in Azure Resource Manager. Eine allgemeine Übersicht finden Sie im [Abschnitt „Bereich“ der Übersicht über Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
In diesem Artikel werden die Wichtigkeit des _Bereichs_ in Azure Policy und die entsprechenden Objekte und Eigenschaften erläutert.

## <a name="definition-location"></a>Definitionsspeicherort

In Azure Policy wird der Bereich erstmals verwendet, wenn eine Richtliniendefinition erstellt wird. Die Definition kann in einer Verwaltungsgruppe oder in einem Abonnement gespeichert werden. Der Speicherort bestimmt den Bereich, dem die Initiative oder Richtlinie zugewiesen werden kann. Ressourcen müssen sich in der Ressourcenhierarchie des Definitionsspeicherorts für die Zuweisung befinden.

Für den Definitionsspeicherort gilt Folgendes:

- **Abonnement**: Der Richtliniendefinition können nur Ressourcen innerhalb dieses Abonnements zugewiesen werden.
- **Verwaltungsgruppe**: Der Richtliniendefinition können nur Ressourcen in untergeordneten Verwaltungsgruppen und untergeordneten Abonnements zugewiesen werden. Wenn Sie die Richtliniendefinition auf mehrere Abonnements anwenden möchten, muss der Speicherort eine Verwaltungsgruppe sein, die das jeweilige Abonnement enthält.

Der Speicherort sollte der Ressourcencontainer sein, der von allen vorhandenen Ressourcen gemeinsam genutzt wird, für die Sie die Richtliniendefinition verwenden möchten, sofern vorhanden. Dieser Ressourcencontainer ist in der Regel eine Verwaltungsgruppe in der Nähe der Stammverwaltungsgruppe.

## <a name="assignment-scopes"></a>Zuweisungsbereiche

Eine Zuweisung verfügt über mehrere Eigenschaften, die einen Bereich festlegen. Durch die Verwendung dieser Eigenschaften wird festgelegt, welche Ressource für Azure Policy ausgewertet werden soll und welche Ressourcen als konforme Elemente gezählt werden. Diese Eigenschaften entsprechen den folgenden Konzepten:

- Einschluss: Eine Ressourcenhierarchie oder eine einzelne Ressource sollte anhand der Definition auf Konformität überprüft werden. Die `properties.scope`-Eigenschaft für ein Zuweisungsobjekt bestimmt, was eingeschlossen und auf Konformität überprüft werden soll. Weitere Informationen finden Sie unter [Zuweisungsdefinition](./assignment-structure.md).

- Ausschluss: Eine Ressourcenhierarchie oder eine einzelne Ressource soll nicht anhand der Definition auf Konformität überprüft werden. Die _Arrayeigenschaft_ `properties.notScopes` bestimmt, was ausgeschlossen werden soll. Ressourcen in diesen Bereichen werden nicht ausgewertet und nicht in die Anzahl konformer Elemente einbezogen. Weitere Informationen finden Sie unter [Zuweisungsdefinition – Ausgeschlossene Bereiche](./assignment-structure.md#excluded-scopes).

Zusätzlich zu den Eigenschaften für die Richtlinienzuweisung ist das [Richtlinienausnahme](./exemption-structure.md)-Objekt vorhanden. Ausnahmen erweitern die Bereichsstory, indem sie eine Methode bieten, einen Teil einer Zuweisung zu identifizieren, die nicht bewertet werden soll.

- Ausnahme (Feature **während der Vorschau kostenlos**): Durch die Definition wird eine Ressourcenhierarchie oder eine einzelne Ressource auf Konformität überprüft. Sie wird jedoch nicht überprüft, wenn für sie ein Verzicht vorliegt oder wenn sie von einer anderen Methode behandelt wird. Ressourcen in diesem Zustand werden in Konformitätsberichten als **Ausnahme** angegeben, damit sie nachverfolgt werden können. Das Ausnahmeobjekt wird in der Ressourcenhierarchie oder der einzelnen Ressource als untergeordnetes Objekt erstellt. Dies bestimmt den Bereich der Ausnahme. Eine Ressourcenhierarchie oder eine einzelne Ressource kann von mehreren Zuweisungen ausgenommen werden. Die Ausnahme kann mit der `expiresOn`-Eigenschaft so konfiguriert werden, dass sie nach einem Zeitplan abläuft. Weitere Informationen finden Sie unter [Ausnahmendefinition](./exemption-structure.md).

  > [!NOTE]
  > Aufgrund der Auswirkungen des Gewährens einer Ausnahme für eine Ressourcenhierarchie oder einzelne Ressource weisen Ausnahmen zusätzliche Sicherheitsmaßnahmen auf. Es ist nicht nur der `Microsoft.Authorization/policyExemptions/write`-Vorgang für die Ressourcenhierarchie oder einzelne Ressource erforderlich, sondern der Ersteller der Ausnahme muss auch über das Verb `exempt/Action` für die Zielzuweisung verfügen.

## <a name="scope-comparison"></a>Bereichsvergleich

Die folgende Tabelle enthält einen Vergleich der Bereichsoptionen:

| | Einschluss | Ausschluss (notScopes) | Ausnahme |
|---|:---:|:---:|:---:|
|**Ressourcen werden ausgewertet** | &#10004; | - | - |
|**Resource Manager-Objekt** | - | - | &#10004; |
|**Erfordert das Ändern des Richtlinienzuweisungsobjekts** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Struktur von Richtliniendefinitionen](./definition-structure.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).