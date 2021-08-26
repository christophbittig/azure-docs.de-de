---
title: 'Tutorial: Verwalten der Tag-Governance'
description: In diesem Tutorial verwenden Sie die Auswirkung „modify“ von Azure Policy, um ein Tag-Governancemodell für neue und bereits vorhandene Ressourcen zu erstellen und zu erzwingen.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: d8f106a63ec65952a5f6a8d68d49bc07279b5be3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324991"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Tutorial: Verwalten der Tag-Governance mit Azure Policy

[Tags](../../../azure-resource-manager/management/tag-resources.md) sind ein wichtiges Hilfsmittel, um Ihre Azure-Ressourcen in einer Taxonomie zu strukturieren. Bei Einhaltung der [bewährten Methoden für die Tagverwaltung](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources) können Tags als Grundlage für die Anwendung Ihrer Geschäftsrichtlinien mit Azure Policy oder für die [Nachverfolgung von Kosten mit Cost Management](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources) herangezogen werden.
Es ist wichtig, Tags für Ihre Azure-Ressourcen schnell hinzufügen, ändern und entfernen zu können – ganz gleich, auf welche Weise oder zu welchem Zweck Sie Tags verwenden. Informationen dazu, ob Ihre Azure-Ressource Tagging unterstützt, finden Sie unter [Tagging-Support](../../../azure-resource-manager/management/tag-support.md).

Die Auswirkung [modify](../concepts/effects.md#modify) von Azure Policy unterstützt die Governance von Tags in jeder Phase der Ressourcengovernance. **modify** ist in folgenden Fällen hilfreich:

- Sie stehen am Anfang der Cloudnutzung und verfügen über keine Tag-Governance.
- Sie verfügen bereits über tausende von Ressourcen ohne Tag-Governance.
- Sie verfügen über eine Taxonomie, die geändert werden muss.

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> - Ermitteln Ihrer geschäftlichen Anforderungen
> - Zuordnen der einzelnen Anforderungen zu einer Richtliniendefinition
> - Gruppieren der Tagrichtlinien in einer Initiative

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um dieses Tutorial durcharbeiten zu können. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="identify-requirements"></a>Ermitteln der Anforderungen

Wie bei jeder guten Implementierung von Governancekontrollen müssen sich die Anforderungen an Ihren geschäftlichen Anforderungen orientieren und vor der Erstellung technischer Kontrollen klar sein. In diesem szenariobasierten Tutorial wird von folgenden geschäftlichen Anforderungen ausgegangen:

- Zwei erforderliche Tags für alle Ressourcen: _CostCenter_ (Kostenstelle) und _Env_ (Umgebung)
- _CostCenter_ muss für alle Container und Einzelressourcen vorhanden sein.
  - Ressourcen erben von dem Container, in dem sie sich befinden, dies kann jedoch individuell überschrieben werden.
- _Env_ muss für alle Container und Einzelressourcen vorhanden sein.
  - Die Umgebung wird von Ressourcen anhand des Containerbenennungsschemas bestimmt und kann nicht überschrieben werden.
  - Alle Ressourcen in einem Container gehören der gleichen Umgebung an.

## <a name="configure-the-costcenter-tag"></a>Konfigurieren des Tags „CostCenter“

Aufgrund der spezifischen Bedingungen einer mit Azure Policy verwalteten Azure-Umgebung machen die Anforderungen des Tags _CostCenter_ Folgendes erforderlich:

- Ablehnen von Ressourcengruppen ohne das Tag _CostCenter_
- Ändern von Ressourcen, um das Tag _CostCenter_ aus der übergeordneten Ressourcengruppe hinzuzufügen, sollte es nicht vorhanden sein

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Ablehnen von Ressourcengruppen ohne das Tag „CostCenter“

Da die Kostenstelle (_CostCenter_) für eine Ressourcengruppe nicht anhand des Namens der Ressourcengruppe bestimmt werden kann, muss das Tag in der Anforderung für die Ressourcengruppenerstellung definiert werden. Die folgende Richtlinienregel mit der Auswirkung [deny](../concepts/effects.md#deny) verhindert die Erstellung oder Aktualisierung von Ressourcengruppen ohne das Tag _CostCenter_:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Da diese Richtlinienregel auf eine Ressourcengruppe ausgerichtet ist, muss der Modus (_mode_) in der Richtliniendefinition „All“ lauten (anstelle von „Indexed“).

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Ändern von Ressourcen, um das Tag „CostCenter“ zu erben, falls es nicht vorhanden ist

Die zweite Anforderung für _CostCenter_ besteht darin, dass alle Ressourcen, bei denen dieses Tag fehlt, das Tag von der übergeordneten Ressourcengruppe erben sollen. Ist das Tag bereits für die Ressource definiert, darf es nicht geändert werden, auch wenn es sich möglicherweise von der übergeordneten Ressourcengruppe unterscheidet. In der folgenden Richtlinienregel wird [modify](../concepts/effects.md#modify) verwendet:

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

In dieser Richtlinienregel wird anstelle des Vorgangs **addOrReplace** der Vorgang **add** verwendet, da der Tagwert bei der [Korrektur](../how-to/remediate-resources.md) vorhandener Ressourcen nicht geändert werden soll, falls er bereits vorhanden ist. Darüber hinaus wird die Vorlagenfunktion `[resourcegroup()]` verwendet, um den Tagwert aus der übergeordneten Ressourcengruppe abzurufen.

> [!NOTE]
> Da diese Richtlinienregel auf Ressourcengruppen mit Tagunterstützung ausgerichtet ist, muss der Modus (_mode_) in der Richtliniendefinition „Indexed“ lauten. Durch diese Konfiguration wird auch sichergestellt, dass diese Richtlinie Ressourcengruppen überspringt.

## <a name="configure-the-env-tag"></a>Konfigurieren des Tags „Env“

Aufgrund der spezifischen Bedingungen einer mit Azure Policy verwalteten Azure-Umgebung machen die Anforderungen des Tags _Env_ Folgendes erforderlich:

- Ändern des Tags _Env_ für die Ressourcengruppe auf der Grundlage des Benennungsschemas der Ressourcengruppe
- Ändern des Tags _Env_ für alle Ressourcen in der Ressourcengruppe, sodass es dem Tag der übergeordneten Ressourcengruppe entspricht

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Ändern des Tags „Env“ von Ressourcengruppen auf der Grundlage des Namens

Eine Richtlinie vom Typ [modify](../concepts/effects.md#modify) wird für jede Umgebung in Ihrer Azure-Umgebung benötigt. Die Änderungsrichtlinie sieht jeweils in etwa wie die folgende Richtliniendefinition aus:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Da diese Richtlinienregel auf eine Ressourcengruppe ausgerichtet ist, muss der Modus (_mode_) in der Richtliniendefinition „All“ lauten (anstelle von „Indexed“).

Diese Richtlinie deckt nur Ressourcengruppen mit dem exemplarischen Benennungsschema für Produktionsressourcen (`prd-`) ab. Komplexere Benennungsschemas können mit mehreren Bedingungen vom Typ **match** erreicht werden (anstelle einer einzelnen Bedingung vom Typ **like** wie in diesem Beispiel).

### <a name="modify-resources-to-inherit-the-env-tag"></a>Ändern von Ressourcen, um das Tag „Env“ zu erben

Aufgrund der geschäftlichen Anforderungen müssen alle Ressourcen über das Tag _Env_ verfügen, über das auch die jeweilige übergeordnete Ressourcengruppe verfügt. Da dieses Tag nicht überschrieben werden kann, verwenden wir den Vorgang **addOrReplace** mit der Auswirkung [modify](../concepts/effects.md#modify). Die exemplarische Änderungsrichtlinie sieht wie die folgende Regel aus:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Da diese Richtlinienregel auf Ressourcengruppen mit Tagunterstützung ausgerichtet ist, muss der Modus (_mode_) in der Richtliniendefinition „Indexed“ lauten. Durch diese Konfiguration wird auch sichergestellt, dass diese Richtlinie Ressourcengruppen überspringt.

Diese Richtlinienregel sucht nach allen Ressourcen, die nicht über den Wert des Tags _Env_ der zugehörigen übergeordneten Ressourcengruppe verfügen oder bei denen das Tag _Env_ fehlt. Bei entsprechenden Ressourcen wird das Tag _Env_ auf den Wert der übergeordneten Ressourcengruppe festgelegt, auch wenn das Tag bereits mit einem anderen Wert für die Ressource vorhanden war.

## <a name="assign-the-initiative-and-remediate-resources"></a>Zuweisen der Initiative und Korrigieren von Ressourcen

Fassen Sie die obigen Tagrichtlinien nach der Erstellung zu einer einzelnen Initiative für die Tag-Governance zusammen, und weisen Sie sie einer Verwaltungsgruppe oder einem Abonnement zu. Die Initiative und die darin enthaltenen Richtlinien analysieren daraufhin die Konformität bereits vorhandener Ressourcen und ändern Anforderungen für neue oder aktualisierte Ressourcen, die der Eigenschaft **if** in der Richtlinienregel entsprechen. Vorhandene, nicht konforme Ressourcen werden durch die Richtlinie jedoch nicht automatisch mit den definierten Tagänderungen aktualisiert.

Bei der Richtlinie **modify** werden genau wie bei Richtlinien vom Typ [deployIfNotExists](../concepts/effects.md#deployifnotexists) Korrekturaufgaben verwendet, um vorhandene, nicht konforme Ressourcen zu ändern. Gehen Sie wie unter [Korrigieren nicht konformer Ressourcen mit Azure Policy](../how-to/remediate-resources.md) beschrieben vor, um Ihre nicht konformen Ressourcen vom Typ **modify** zu ermitteln und die Tags gemäß Ihrer definierten Taxonomie zu korrigieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen dieses Tutorials nicht mehr benötigen, führen Sie die folgenden Schritte aus, um die erstellten Zuweisungen oder Definitionen zu löschen:

1. Klicken Sie links auf der Seite „Azure Policy“ unter **Erstellung** auf **Definitionen** (oder auf **Zuweisungen**, wenn Sie eine Zuweisung löschen möchten).

1. Suchen Sie nach der neuen Initiativ- oder Richtliniendefinition (bzw. der Zuweisung), die Sie entfernen möchten.

1. Klicken Sie mit der rechten Maustaste auf die Zeile, oder wählen Sie die Auslassungspunkte am Ende der Definition (oder Zuweisung), und wählen Sie anschließend **Definition löschen** (bzw. **Zuweisung löschen**) aus.

## <a name="review"></a>Überprüfung

In diesem Tutorial wurden folgende Aufgaben behandelt:

> [!div class="checklist"]
> - Ermitteln Ihrer geschäftlichen Anforderungen
> - Zuordnen der einzelnen Anforderungen zu einer Richtliniendefinition
> - Gruppieren der Tagrichtlinien in einer Initiative

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md)
