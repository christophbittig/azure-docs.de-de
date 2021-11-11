---
title: Funktionsweise von Auswirkungen
description: Die Azure Policy-Definitionen haben verschiedene Auswirkungen, mit denen festgelegt wird, wie die Konformität verwaltet und gemeldet wird.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: bbcdce83fad513c85ab45f4c38c936b345828ef3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449676"
---
# <a name="understand-azure-policy-effects"></a>Grundlegendes zu Azure Policy-Auswirkungen

Jede Richtliniendefinition in Azure Policy hat eine einzelne Auswirkung. Diese Auswirkung bestimmt, was geschieht, wenn die Richtlinienregel auf eine Übereinstimmung ausgewertet wird. Die Auswirkungen für eine neue Ressource, eine aktualisierte Ressource oder eine vorhandene Ressource sind hierbei unterschiedlich.

Derzeit werden in einer Richtliniendefinition diese Auswirkungen unterstützt:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [Modify](#modify)

Die folgenden Auswirkungen sind _veraltet_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> Verwenden Sie anstelle der Auswirkung **EnforceOPAConstraint** oder **EnforceRegoPolicy** die Auswirkungen _audit_ und _deny_ mit dem Ressourcenanbietermodus `Microsoft.Kubernetes.Data`. Die integrierten Richtliniendefinitionen wurden aktualisiert. Wenn vorhandene Richtlinienzuweisungen dieser integrierten Richtliniendefinitionen geändert werden, muss der Parameter _effect_ in einen Wert aus der aktualisierten Liste _allowedValues_ geändert werden.

## <a name="order-of-evaluation"></a>Reihenfolge der Auswertung

Anforderungen zum Erstellen oder Aktualisieren einer Ressource werden von Azure Policy zuerst ausgewertet. Azure Policy erstellt eine Liste aller Zuweisungen, die auf die Ressource zutreffen, und wertet dann die Ressource anhand jeder Definition aus. Bei Verwendung eines [Resource Manager-Modus](./definition-structure.md#resource-manager-modes) werden von Azure Policy einige der Auswirkungen verarbeitet, bevor die Anforderung an den entsprechenden Ressourcenanbieter übergeben wird. Durch diese Reihenfolge wird eine unnötige Verarbeitung durch einen Ressourcenanbieter verhindert, wenn eine Ressource nicht den konfigurierten Governancevorgaben von Azure Policy entspricht. Bei Verwendung eines [Ressourcenanbietermodus](./definition-structure.md#resource-provider-modes) verwaltet der Ressourcenanbieter die Auswertung und das Ergebnis und gibt die Ergebnisse an Azure Policy zurück.

- Zuerst wird **Deaktiviert** überprüft, um zu ermitteln, ob die Richtlinienregel ausgewertet werden soll.
- **Append** und **Modify** werden dann ausgewertet. Die Anforderung kann durch beide geändert werden, deshalb kann eine durchgeführte Änderung die Auslösung der Auswirkungen „Audit“ oder „Deny“ verhindern. Diese Auswirkungen sind nur in einem Resource Manager-Modus verfügbar.
- Anschließend wird **deny** ausgewertet. Durch die Auswertung von „deny“ vor „audit“ wird eine zweimalige Protokollierung einer unerwünschten Ressource verhindert.
- **audit** wird zuletzt ausgewertet.

Nachdem vom Ressourcenanbieter ein Erfolgscode für eine Anforderung im Resource Manager-Modus zurückgegeben wurde, werden **AuditIfNotExists** und **DeployIfNotExists** ausgewertet, um zu bestimmen, ob eine zusätzliche Konformitätsprotokollierung oder -aktion erforderlich ist.

Außerdem schränken `PATCH`-Anforderungen, die nur Felder mit Bezug zu `tags` ändern, die Richtlinienauswertung auf Richtlinien mit Bedingungen ein, die Felder mit Bezug zu `tags` überprüfen.

## <a name="append"></a>Anfügen

„append“ wird verwendet, um der angeforderten Ressource während der Erstellung oder Aktualisierung zusätzliche Felder hinzuzufügen. Ein häufiges Beispiel ist die Angabe der zulässigen IP-Adressen für eine Speicherressource.

> [!IMPORTANT]
> „Append“ ist für die Verwendung mit Nicht-Tag-Eigenschaften vorgesehen. Während „Append“ während einer Erstellungs- oder Aktualisierungsanforderung Tags zu einer Ressource hinzufügen kann, wird empfohlen, stattdessen die Auswirkung [Modify](#modify) für Tags zu verwenden.

### <a name="append-evaluation"></a>Auswertung von „append“

Die Auswertung von „append“ erfolgt vor der Anforderungsverarbeitung durch einen Ressourcenanbieter während der Erstellung oder Aktualisierung einer Ressource. Mit „append“ werden Felder zur Ressource hinzugefügt, wenn die **if**-Bedingung der Richtlinienregel erfüllt ist. Wenn die Auswirkung „append“ einen Wert in der ursprünglichen Anforderung mit einem anderen Wert überschreiben würde, fungiert sie als Auswirkung „deny“ und lehnt die Anforderung ab. Um einen neuen Wert an ein vorhandenes Array anzufügen, verwenden Sie die **\[\*\]** -Version des Alias.

Wenn eine Richtliniendefinition mit Auswirkung „append“ im Rahmen eines Auswertungszyklus ausgeführt wird, werden keine Änderungen an bereits vorhandenen Ressourcen durchgeführt. Stattdessen werden alle Ressourcen mit Erfüllung der **if**-Bedingung als nicht konform markiert.

### <a name="append-properties"></a>Eigenschaften von „append“

Die Auswirkung „append“ weist nur ein Array **details** auf, das erforderlich ist. Da es sich bei **details** um ein Array handelt, kann es entweder ein einzelnes **field/value**-Paar oder mehrere Wertepaare verwenden. Eine Liste der gültigen Felder finden Sie unter [Struktur von Azure Policy-Definitionen](definition-structure.md#fields).

### <a name="append-examples"></a>Beispiele für „append“

Beispiel 1: Einzelnes **field/value**-Paar, das einen Nicht- **\[\*\]** 
[Alias](definition-structure.md#aliases)mit einem **Arraywert** (value) verwendet, um IP-Regeln für ein Speicherkonto festzulegen. Wenn der Nicht- **\[\*\]** -Alias ein Array ist, wird der **value** durch den Effekt als gesamtes Array angefügt. Wenn das Array bereits vorhanden ist, wird durch den Konflikt ein deny-Ereignis ausgelöst.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Beispiel 2: Einzelnes **field/value**-Paar, das einen **\[\*\]** -[Alias](definition-structure.md#aliases) mit einem Arraywert (**value**) verwendet, um IP-Regeln für ein Speicherkonto festzulegen. Durch die Verwendung des **\[\*\]** -Alias fügt der Effekt den **value** an ein Array an, das möglicherweise bereits vorhanden ist. Ist das Array noch nicht vorhanden, wird es erstellt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>Audit

Die Auswirkung „audit“ wird verwendet, um ein Warnungsereignis im Aktivitätsprotokoll zu erstellen, wenn eine nicht konforme Ressource ausgewertet wird. Die Anforderung wird jedoch nicht beendet.

### <a name="audit-evaluation"></a>Auswertung von „audit“

„audit“ ist die letzte Auswirkung, die von Azure Policy während der Erstellung oder Aktualisierung einer Ressource überprüft wird. Bei einem Resource Manager-Modus wird die Ressource von Azure Policy an den Ressourcenanbieter gesendet. Beim Auswerten einer Erstellungs- oder Aktualisierungsanforderung für eine Ressource fügt Azure Policy dem Aktivitätsprotokoll einen `Microsoft.Authorization/policies/audit/action`-Vorgang hinzu und markiert die Ressource als nicht konform. Während eines Standard-Konformitätsauswertungszyklus wird nur der Konformitätsstatus für die Ressource aktualisiert.

### <a name="audit-properties"></a>Eigenschaften von „audit“

Bei Verwendung eines Resource Manager-Modus verfügt die Auswirkung „audit“ über keine zusätzlichen Eigenschaften für die Verwendung in der Bedingung **then** der Richtliniendefinition.

Bei Verwendung des Ressourcenanbietermodus `Microsoft.Kubernetes.Data` verfügt die Auswirkung „audit“ über folgende zusätzliche Untereigenschaften von **details**: Die Verwendung von `templateInfo` ist für neue oder aktualisierte Richtliniendefinitionen erforderlich, da `constraintTemplate` veraltet ist.

- **templateInfo** (erforderlich)
  - Kann nicht mit "`constraintTemplate`" verwendet werden.
  - **sourceType** (erforderlich)
    - Definiert den Typ der Quelle für die Einschränkungsvorlage. Zulässige Werte: _PublicURL_ oder _Base64Encoded_.
    - Wenn _PublicURL_, gepaart mit der Eigenschaft `url` zur Angabe des Speicherorts der Einschränkungsvorlage verwendet wird. Auf den Speicherort muss öffentlich zugegriffen werden können.

      > [!WARNING]
      > Verwenden Sie keine SAS-URIs oder Token in `url` oder andere Elemente, die ein Geheimnis offenlegen könnten.

    - Wenn _Base64Encoded_, gepaart mit der Eigenschaft `content` zur Angabe der Base64-codierten Einschränkungsvorlage verwendet wird. Unter [Erstellen einer Richtliniendefinition aus einer Einschränkungsvorlage](../how-to/extension-for-vscode.md) erfahren Sie, wie Sie eine benutzerdefinierte Definition aus einer vorhandenen [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) GateKeeper v3-[Einschränkungsvorlage](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) erstellen.
- **constraint** (optional)
  - Kann nicht mit "`templateInfo`" verwendet werden.
  - Die CRD-Implementierung der Einschränkungsvorlage. Verwendet Parameter, die über **values** als `{{ .Values.<valuename> }}`übergeben werden. In „Beispiel 2“ weiter unten lauten diese Werte `{{ .Values.excludedNamespaces }}` und `{{ .Values.allowedContainerImagesRegex }}`.
- **namespaces** (optional)
  - Ein _Array_ von [Kubernetes-Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/), auf das die Richtlinienauswertung beschränkt werden soll.
  - Ein leerer oder fehlender Wert bewirkt, dass die Richtlinienauswertung alle Namespaces einschließt, mit Ausnahme der in _excludedNamespaces_ definierten Namespaces.
- **excludedNamespaces** (erforderlich)
  - Ein _Array_ von [Kubernetes-Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/), die von der Richtlinienauswertung ausgeschlossen werden sollen.
- **labelSelector** (erforderlich)
  - Ein _Objekt_, das die Eigenschaften _matchLabels_ (Objekt) und _matchExpression_ (Array) umfasst, um festlegen zu können, welche Kubernetes-Ressourcen in die Richtlinienauswertung einbezogen werden sollen, die den angegebenen [Bezeichnungen und Selektoren](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) entsprechen.
  - Ein leerer oder fehlender Wert bewirkt, dass die Richtlinienauswertung alle Bezeichnungen und Selektoren einschließt, mit Ausnahme der in _excludedNamespaces_ definierten Namespaces.
- **apiGroups** (erforderlich bei Verwendung von _templateInfo_)
  - Ein _Array_, der die [API-Gruppen](https://kubernetes.io/docs/reference/using-api/#api-groups) enthält, die übereinstimmen müssen. Ein leeres Array (`[""]`) ist die API-Kerngruppe, während `["*"]` alle API-Gruppen einbezieht.
- **kinds** (erforderlich bei Verwendung von _templateInfo_)
  - Ein _Array_, das die [Art](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#required-fields) von Kubernetes-Objekten festlegt, auf die die Auswertung beschränkt werden soll.
- **values** (optional)
  - Definiert Parameter und Werte, die an die Einschränkung übergeben werden. Jeder Wert muss in der CRD der Einschränkungsvorlage vorhanden sein.
- **constraintTemplate** (veraltet)
  - Kann nicht mit "`templateInfo`" verwendet werden.
  - Muss beim Erstellen oder Aktualisieren einer Richtliniendefinition durch `templateInfo` ersetzt werden.
  - Die Einschränkungsvorlage CustomResourceDefinition (CRD), die neue Einschränkungen definiert. Die Vorlage definiert die Rego-Logik, das Einschränkungsschema und die Einschränkungsparameter, die über **values** von Azure Policy übergeben werden.

### <a name="audit-example"></a>Beispiel für „audit“

Beispiel 1: Verwenden der Auswirkung „audit“ für Resource Manager-Modi:

```json
"then": {
    "effect": "audit"
}
```

Beispiel 2: Verwenden der Auswirkung „audit“ für den Ressourcenanbietermodus `Microsoft.Kubernetes.Data`. Durch die zusätzlichen Informationen in **details.templateInfo** werden die Verwendung von _PublicURL_ deklariert und `url` auf den Speicherort der Einschränkungsvorlage festgelegt, die in Kubernetes verwendet werden soll, um die zulässigen Containerimages einzuschränken.

```json
"then": {
    "effect": "audit",
    "details": {
        "templateInfo": {
            "sourceType": "PublicURL",
            "url": "https://store.policy.core.windows.net/kubernetes/container-allowed-images/v1/template.yaml",
        },
        "values": {
            "imageRegex": "[parameters('allowedContainerImagesRegex')]"
        },
        "apiGroups": [""],
        "kinds": ["Pod"]
    }
}
```

## <a name="auditifnotexists"></a>Auswirkung „AuditIfNotExists“

Die Auswirkung „AuditIfNotExists“ ermöglicht die Überwachung von Ressourcen, die zwar mit der Ressource, die die Bedingung **if** erfüllt, _zusammenhängen_, für die aber keine Eigenschaften in den Details (**details**) der Bedingung **then** angegeben sind.

### <a name="auditifnotexists-evaluation"></a>Auswertung von „AuditIfNotExists“

„AuditIfNotExists“ wird ausgeführt, nachdem ein Ressourcenanbieter eine Anforderung zum Erstellen oder Aktualisieren einer Ressource verarbeitet hat und ein Erfolgsstatuscode zurückgegeben wurde. Die Überprüfung findet statt, wenn keine entsprechenden Ressourcen vorhanden sind oder wenn die über **ExistenceCondition** definierten Ressourcen nicht als TRUE ausgewertet werden. Bei neuen und aktualisierten Ressourcen fügt Azure Policy dem Aktivitätsprotokoll einen `Microsoft.Authorization/policies/audit/action`-Vorgang hinzu und markiert die Ressource als nicht konform. Bei Auslösung ist die Ressource, die die **if**-Bedingung erfüllt hat, die Ressource, die als nicht konform markiert wird.

### <a name="auditifnotexists-properties"></a>Eigenschaften von „AuditIfNotExists“

Die **details**-Eigenschaft der Auswirkung „AuditIfNotExists“ umfasst die folgenden Untereigenschaften zur Definition der entsprechenden Ressourcen für den Abgleich.

- **Type** (erforderlich)
  - Gibt den Typ der entsprechenden abzugleichenden Ressource an.
  - Wenn es sich bei **details.type** um einen Ressourcentyp unterhalb der Bedingungsressource **if** handelt, fragt die Richtlinie Ressourcen dieses **Typs** innerhalb des Bereichs der ausgewerteten Ressource ab. Andernfalls erfolgen Abfragen der Richtlinie innerhalb der gleichen Ressourcengruppe wie die ausgewertete Ressource.
- **Name** (optional)
  - Gibt den exakten Namen der Ressource für den Abgleich an und führt dazu, dass die Richtlinie nicht alle Ressourcen des angegebenen Typs, sondern eine bestimmte Ressource abruft.
  - Wenn die Werte der Bedingung für **if.field.type** und **then.details.type** übereinstimmen, wird **Name**_erforderlich_ und muss `[field('name')]` sein (oder `[field('fullName')]` für eine untergeordnete Ressource).
    Allerdings sollte stattdessen ein [audit](#audit)-Effekt in Erwägung gezogen werden.
- **ResourceGroupName** (optional)
  - Hierdurch ist es möglich, für den Abgleich eine Ressource aus einer anderen Ressourcengruppe festzulegen.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Standardmäßig wird die Ressourcengruppe der **if**-Bedingungsressource verwendet.
- **ExistenceScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Bereich fest, aus dem die entsprechende Ressource für den Abgleich abgerufen werden soll.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Für _ResourceGroup_ bedeutet dies eine Beschränkung auf die Ressourcengruppe der **if** -Bedingungsressource oder die in **ResourceGroupName** angegebene Ressourcengruppe.
  - Für _Subscription_ wird das gesamte Abonnement nach der entsprechenden Ressource abgefragt. Der Zuweisungsbereich sollte für die ordnungsgemäße Auswertung auf das Abonnement oder höher festgelegt werden. 
  - Die Standardeinstellung ist _ResourceGroup_.
- **EvaluationDelay** (optional)
  - Hiermit wird angegeben, wann die Existenz der zugehörigen Ressourcen ausgewertet werden soll. Die Verzögerung wird nur bei Auswertungen verwendet, die das Ergebnis einer Erstell- oder Aktualisierungsanforderung für die Ressource sind.
  - Zu den zulässigen Werten zählen `AfterProvisioning`, `AfterProvisioningSuccess` und `AfterProvisioningFailure` sowie eine Dauer gemäß ISO 8601 zwischen 0 und 360 Minuten.
  - Die _AfterProvisioning_-Werte untersuchen die Bereitstellungsergebnisse der Ressource, die in der IF-Bedingung der Richtlinienregel ausgewertet wurde. `AfterProvisioning` wird unabhängig vom Ergebnis nach der Bereitstellung ausgeführt. Wenn für die Bereitstellung mehr als sechs Stunden benötigt werden, wird diese bei Festlegung von _AfterProvisioning_-Auswertungsverzögerungen als Fehler behandelt.
  - Der Standardwert ist `PT10M` (zehn Minuten).
  - Das Festlegen einer langen Auswertungsverzögerung kann dazu führen, dass der aufgezeichnete Compliancestatus der Ressource bis zur nächsten [Auswertungsauslösung](../how-to/get-compliance-data.md#evaluation-triggers) nicht aktualisiert wird.
- **ExistenceCondition** (optional)
  - Sofern nicht angegeben, erfüllt jede Ressource mit entsprechendem **type** die Bedingung der Auswirkung und löst keine Überwachung aus.
  - Verwendet dieselbe Sprachsyntax wie die Richtlinienregel für die **if**-Bedingung, wird jedoch für jede entsprechende Ressource einzeln ausgeführt.
  - Wenn eine übereinstimmende Ressource als TRUE ausgewertet wird, ist die Auswirkung erfüllt und löst keine Überwachung aus.
  - [field()] kann verwendet werden, um auf Äquivalenz mit Werten in der **if**-Bedingung zu überprüfen.
  - Beispielsweise könnte so überprüft werden, ob die übergeordnete Ressource (in der **if**-Bedingung) sich am selben Ressourcenstandort befindet wie die übereinstimmende Ressource.

### <a name="auditifnotexists-example"></a>Beispiel für „AuditIfNotExists“

Beispiel: Wertet Virtual Machines aus, um zu bestimmen, ob die Antischadsoftware-Erweiterung vorhanden ist, und überprüft, wenn sie fehlt.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Verweigern

Mit „deny“ werden Ressourcenanforderungen abgelehnt, welche die über eine Richtliniendefinition festgelegten Standards nicht erfüllen. Für die Anforderung wird anschließend ein Fehler ausgegeben.

### <a name="deny-evaluation"></a>Auswertung von „deny“

Beim Erstellen oder Aktualisieren einer entsprechenden Ressource in einem Resource Manager-Modus wird die Anforderung durch „deny“ verhindert, bevor sie an den Ressourcenanbieter gesendet wird. Für die Anforderung wird `403 (Forbidden)` zurückgegeben. Im Portal kann „Verboten“ als Status für die Bereitstellung angezeigt werden, die durch die Richtlinienzuweisung verhindert wurde. Bei Verwendung eines Ressourcenanbietermodus wird die Auswertung der Ressource vom Ressourcenanbieter verwaltet.

Während der Auswertung vorhandener Ressourcen werden Ressourcen, die einer „deny“-Richtliniendefinition entsprechen, als nicht konform markiert.

### <a name="deny-properties"></a>Eigenschaften von „deny“

Bei Verwendung eines Resource Manager-Modus verfügt die Auswirkung „deny“ über keine zusätzlichen Eigenschaften für die Verwendung in der Bedingung **then** der Richtliniendefinition.

Bei Verwendung des Ressourcenanbietermodus `Microsoft.Kubernetes.Data` verfügt die Auswirkung „deny“ über folgende zusätzliche Untereigenschaften von **details**: Die Verwendung von `templateInfo` ist für neue oder aktualisierte Richtliniendefinitionen erforderlich, da `constraintTemplate` veraltet ist.

- **templateInfo** (erforderlich)
  - Kann nicht mit "`constraintTemplate`" verwendet werden.
  - **sourceType** (erforderlich)
    - Definiert den Typ der Quelle für die Einschränkungsvorlage. Zulässige Werte: _PublicURL_ oder _Base64Encoded_.
    - Wenn _PublicURL_, gepaart mit der Eigenschaft `url` zur Angabe des Speicherorts der Einschränkungsvorlage verwendet wird. Auf den Speicherort muss öffentlich zugegriffen werden können.

      > [!WARNING]
      > Verwenden Sie keine SAS-URIs oder Token in `url` oder andere Elemente, die ein Geheimnis offenlegen könnten.

    - Wenn _Base64Encoded_, gepaart mit der Eigenschaft `content` zur Angabe der Base64-codierten Einschränkungsvorlage verwendet wird. Unter [Erstellen einer Richtliniendefinition aus einer Einschränkungsvorlage](../how-to/extension-for-vscode.md) erfahren Sie, wie Sie eine benutzerdefinierte Definition aus einer vorhandenen [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) GateKeeper v3-[Einschränkungsvorlage](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) erstellen.
- **constraint** (optional)
  - Kann nicht mit "`templateInfo`" verwendet werden.
  - Die CRD-Implementierung der Einschränkungsvorlage. Verwendet Parameter, die über **values** als `{{ .Values.<valuename> }}`übergeben werden. In „Beispiel 2“ weiter unten lauten diese Werte `{{ .Values.excludedNamespaces }}` und `{{ .Values.allowedContainerImagesRegex }}`.
- **namespaces** (optional)
  - Ein _Array_ von [Kubernetes-Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/), auf das die Richtlinienauswertung beschränkt werden soll.
  - Ein leerer oder fehlender Wert bewirkt, dass die Richtlinienauswertung alle Namespaces einschließt, mit Ausnahme der in _excludedNamespaces_ definierten Namespaces.
- **excludedNamespaces** (erforderlich)
  - Ein _Array_ von [Kubernetes-Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/), die von der Richtlinienauswertung ausgeschlossen werden sollen.
- **labelSelector** (erforderlich)
  - Ein _Objekt_, das die Eigenschaften _matchLabels_ (Objekt) und _matchExpression_ (Array) umfasst, um festlegen zu können, welche Kubernetes-Ressourcen in die Richtlinienauswertung einbezogen werden sollen, die den angegebenen [Bezeichnungen und Selektoren](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) entsprechen.
  - Ein leerer oder fehlender Wert bewirkt, dass die Richtlinienauswertung alle Bezeichnungen und Selektoren einschließt, mit Ausnahme der in _excludedNamespaces_ definierten Namespaces.
- **apiGroups** (erforderlich bei Verwendung von _templateInfo_)
  - Ein _Array_, der die [API-Gruppen](https://kubernetes.io/docs/reference/using-api/#api-groups) enthält, die übereinstimmen müssen. Ein leeres Array (`[""]`) ist die API-Kerngruppe, während `["*"]` alle API-Gruppen einbezieht.
- **kinds** (erforderlich bei Verwendung von _templateInfo_)
  - Ein _Array_, das die [Art](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#required-fields) von Kubernetes-Objekten festlegt, auf die die Auswertung beschränkt werden soll.
- **values** (optional)
  - Definiert Parameter und Werte, die an die Einschränkung übergeben werden. Jeder Wert muss in der CRD der Einschränkungsvorlage vorhanden sein.
- **constraintTemplate** (veraltet)
  - Kann nicht mit "`templateInfo`" verwendet werden.
  - Muss beim Erstellen oder Aktualisieren einer Richtliniendefinition durch `templateInfo` ersetzt werden.
  - Die Einschränkungsvorlage CustomResourceDefinition (CRD), die neue Einschränkungen definiert. Die Vorlage definiert die Rego-Logik, das Einschränkungsschema und die Einschränkungsparameter, die über **values** von Azure Policy übergeben werden. Es wird empfohlen, das neuere `templateInfo` zu verwenden, um `constraintTemplate` zu ersetzen.

### <a name="deny-example"></a>Beispiel für „deny“

Beispiel 1: Verwenden der Auswirkung „deny“ für Resource Manager-Modi:

```json
"then": {
    "effect": "deny"
}
```

Beispiel 2: Verwenden der Auswirkung „deny“ für den Ressourcenanbietermodus `Microsoft.Kubernetes.Data`. Durch die zusätzlichen Informationen in **details.templateInfo** werden die Verwendung von _PublicURL_ deklariert und `url` auf den Speicherort der Einschränkungsvorlage festgelegt, die in Kubernetes verwendet werden soll, um die zulässigen Containerimages einzuschränken.

```json
"then": {
    "effect": "deny",
    "details": {
        "templateInfo": {
            "sourceType": "PublicURL",
            "url": "https://store.policy.core.windows.net/kubernetes/container-allowed-images/v1/template.yaml",
        },
        "values": {
            "imageRegex": "[parameters('allowedContainerImagesRegex')]"
        },
        "apiGroups": [""],
        "kinds": ["Pod"]
    }
}
```

## <a name="deployifnotexists"></a>Auswirkung „DeployIfNotExists“

Ähnlich wie „AuditIfNotExists“ führt eine „DeployIfNotExists“-Richtliniendefinition eine Vorlagenbereitstellung durch, wenn die Bedingung erfüllt ist.

> [!NOTE]
> [Geschachtelte Vorlagen](../../../azure-resource-manager/templates/linked-templates.md#nested-template) werden mit **deployIfNotExists** unterstützt, [verknüpfte Vorlagen](../../../azure-resource-manager/templates/linked-templates.md#linked-template) werden derzeit jedoch nicht unterstützt.

### <a name="deployifnotexists-evaluation"></a>Auswertung von „DeployIfNotExists“

„DeployIfNotExists“ wird nach einer konfigurierbaren Verzögerung ausgeführt, wenn ein Ressourcenanbieter eine Anforderung zum Erstellen oder Aktualisieren eines Abonnements oder einer Ressource verarbeitet hat und ein Erfolgsstatuscode zurückgegeben wurde. Eine Vorlagenbereitstellung findet statt, wenn keine entsprechenden Ressourcen vorhanden sind oder wenn die über **ExistenceCondition** definierten Ressourcen nicht als TRUE ausgewertet werden. Die Dauer der Bereitstellung hängt von der Komplexität der in der Vorlage enthaltenen Ressourcen ab.

Während eines Auswertungszyklus führen Richtliniendefinitionen mit der Auswirkung „DeployIfNotExists“ dazu, dass übereinstimmende Ressourcen als nicht konform markiert werden. Es wird aber keine Aktion für diese Ressource ausgeführt. Bestehende, nicht konforme Ressourcen können mit einem [Wartungstask](../how-to/remediate-resources.md) bereinigt werden.

### <a name="deployifnotexists-properties"></a>Eigenschaften von „DeployIfNotExists“

Die **details**-Eigenschaft der Auswirkung „DeployIfNotExists“ umfasst alle Untereigenschaften, die die entsprechenden Ressourcen für den Abgleich und die auszuführende Vorlagenbereitstellung definieren.

- **Type** (erforderlich)
  - Gibt den Typ der entsprechenden abzugleichenden Ressource an.
  - Zunächst wird versucht, eine Ressource unterhalb der **if**-Bedingungsressource abzurufen, anschließend wird eine Abfrage innerhalb derselben Ressourcengruppe wie der **if**-Bedingungsressource durchgeführt.
- **Name** (optional)
  - Gibt den exakten Namen der Ressource für den Abgleich an und führt dazu, dass die Richtlinie nicht alle Ressourcen des angegebenen Typs, sondern eine bestimmte Ressource abruft.
  - Wenn die Werte der Bedingung für **if.field.type** und **then.details.type** übereinstimmen, wird **Name**_erforderlich_ und muss `[field('name')]` sein (oder `[field('fullName')]` für eine untergeordnete Ressource).
- **ResourceGroupName** (optional)
  - Hierdurch ist es möglich, für den Abgleich eine Ressource aus einer anderen Ressourcengruppe festzulegen.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Standardmäßig wird die Ressourcengruppe der **if**-Bedingungsressource verwendet.
  - Wenn eine Vorlagenbereitstellung durchgeführt wird, erfolgt diese in der Ressourcengruppe, die über diesen Wert angegeben wurde.
- **ExistenceScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Bereich fest, aus dem die entsprechende Ressource für den Abgleich abgerufen werden soll.
  - Diese Einstellung ist nicht anwendbar, wenn **type** eine Ressource unterhalb der **if**-Bedingungsressource angibt.
  - Für _ResourceGroup_ bedeutet dies eine Beschränkung auf die Ressourcengruppe der **if** -Bedingungsressource oder die in **ResourceGroupName** angegebene Ressourcengruppe.
  - Für _Subscription_ wird das gesamte Abonnement nach der entsprechenden Ressource abgefragt. Der Zuweisungsbereich sollte für die ordnungsgemäße Auswertung auf das Abonnement oder höher festgelegt werden. 
  - Die Standardeinstellung ist _ResourceGroup_.
- **EvaluationDelay** (optional)
  - Hiermit wird angegeben, wann die Existenz der zugehörigen Ressourcen ausgewertet werden soll. Die Verzögerung wird nur bei Auswertungen verwendet, die das Ergebnis einer Erstell- oder Aktualisierungsanforderung für die Ressource sind.
  - Zu den zulässigen Werten zählen `AfterProvisioning`, `AfterProvisioningSuccess` und `AfterProvisioningFailure` sowie eine Dauer gemäß ISO 8601 zwischen 0 und 360 Minuten.
  - Die _AfterProvisioning_-Werte untersuchen die Bereitstellungsergebnisse der Ressource, die in der IF-Bedingung der Richtlinienregel ausgewertet wurde. `AfterProvisioning` wird unabhängig vom Ergebnis nach der Bereitstellung ausgeführt. Wenn für die Bereitstellung mehr als sechs Stunden benötigt werden, wird diese bei Festlegung von _AfterProvisioning_-Auswertungsverzögerungen als Fehler behandelt.
  - Der Standardwert ist `PT10M` (zehn Minuten).
  - Das Festlegen einer langen Auswertungsverzögerung kann dazu führen, dass der aufgezeichnete Compliancestatus der Ressource bis zur nächsten [Auswertungsauslösung](../how-to/get-compliance-data.md#evaluation-triggers) nicht aktualisiert wird.
- **ExistenceCondition** (optional)
  - Sofern nicht angegeben, erfüllt jede entsprechende Ressource mit **type** die Bedingung der Auswirkung und löst keine Bereitstellung aus.
  - Verwendet dieselbe Sprachsyntax wie die Richtlinienregel für die **if**-Bedingung, wird jedoch für jede entsprechende Ressource einzeln ausgeführt.
  - Wenn eine übereinstimmende Ressource als TRUE ausgewertet wird, ist die Auswirkung erfüllt und löst keine Bereitstellung aus.
  - [field()] kann verwendet werden, um auf Äquivalenz mit Werten in der **if**-Bedingung zu überprüfen.
  - Beispielsweise könnte so überprüft werden, ob die übergeordnete Ressource (in der **if**-Bedingung) sich am selben Ressourcenstandort befindet wie die übereinstimmende Ressource.
- **roleDefinitionIds** (erforderlich)
  - Diese Eigenschaft muss ein Array von Zeichenfolgen enthalten, das mit der Rollen-ID der rollenbasierten Zugriffssteuerung übereinstimmt, auf die das Abonnement zugreifen kann. Weitere Informationen finden Sie unter [Korrigieren nicht konformer Ressourcen](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (optional)
  - Zulässige Werte sind _Subscription_ und _ResourceGroup_.
  - Legt den Typ der auszulösenden Bereitstellung fest. Mit _Subscription_ wird eine [Bereitstellung auf Abonnementebene](../../../azure-resource-manager/templates/deploy-to-subscription.md) und mit _ResourceGroup_ eine Bereitstellung in einer Ressourcengruppe angegeben.
  - Bei der Bereitstellung auf Abonnementebene muss in _Deployment_ eine _location_-Eigenschaft angegeben werden.
  - Die Standardeinstellung ist _ResourceGroup_.
- **Deployment** (erforderlich)
  - Diese Eigenschaft muss die vollständige Vorlagenbereitstellung enthalten, so wie sie an die PUT-API `Microsoft.Resources/deployments` übergeben würde. Weitere Informationen finden Sie im Artikel zur [REST-API für die Bereitstellung](/rest/api/resources/deployments).
  - Geschachtelte `Microsoft.Resources/deployments`-Elemente innerhalb der Vorlage sollten eindeutige Namen verwenden, um Konflikte zwischen mehreren Richtlinienauswertungen zu vermeiden. Der Name der übergeordneten Bereitstellung kann über `[concat('NestedDeploymentName-', uniqueString(deployment().name))]` als Teil des Namens der geschachtelte Bereitstellung verwendet werden.

  > [!NOTE]
  > Alle Funktionen innerhalb der **Deployment**-Eigenschaft werden als Komponenten der Vorlage – nicht der Richtlinie – ausgewertet. Eine Ausnahme ist die Eigenschaft **parameters**, die Werte von der Richtlinie an die Vorlage übergibt. Der in diesem Abschnitt unter einem Vorlagenparameternamen angegebene Wert für **value** wird verwendet, um diese Wertübergabe durchzuführen (siehe _fullDbName_ im DeployIfNotExists-Beispiel).

### <a name="deployifnotexists-example"></a>Beispiel für „DeployIfNotExists“

Beispiel: Wertet SQL Server-Datenbanken aus, um zu bestimmen, ob „transparentDataEncryption“ aktiviert ist.
Falls nicht, wird eine Bereitstellung zur Aktivierung dieser Option durchgeführt.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "evaluationDelay": "AfterProvisioning",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Disabled

Diese Auswirkung ist in Testsituationen oder nach dem Parametrisieren der Auswirkung durch die Richtliniendefinition hilfreich. Aufgrund dieser Flexibilität kann eine einzelne Zuweisung deaktiviert werden, statt alle Zuweisungen dieser Richtlinie zu deaktivieren.

Eine Alternative zur Auswirkung „Deaktiviert“ stellt **enforcementMode** dar (wird in der Richtlinienzuweisung festgelegt).
Wenn für **enforcementMode**  der Wert _Disabled_ festgelegt ist, werden Ressourcen dennoch ausgewertet. Eine Protokollierung, z.B. Aktivitätsprotokolle, und die Richtlinienauswirkung treten nicht auf. Weitere Informationen finden Sie unter [Richtlinienzuweisung – Erzwingungsmodus](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Diese Auswirkung wird bei einer Richtliniendefinition mit dem _Modus_`Microsoft.Kubernetes.Data` verwendet. Sie dient zum Übergeben von Gatekeeper v3-Zugangskontrollregeln, die mit [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) definiert wurden, an [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) für Kubernetes-Cluster in Azure.

> [!IMPORTANT]
> Die Richtliniendefinitionen der eingeschränkten Vorschauversion mit der Auswirkung **EnforceOPAConstraint** und die zugehörige Kategorie **Kubernetes Service** sind _veraltet_. Verwenden Sie stattdessen die Auswirkungen _audit_ und _deny_ mit dem Ressourcenanbietermodus `Microsoft.Kubernetes.Data`.

### <a name="enforceopaconstraint-evaluation"></a>Auswertung von EnforceOPAConstraint

Der Zugangscontroller des Open Policy Agent bewertet jede neue Anforderung an den Cluster in Echtzeit.
Alle 15 Minuten erfolgt ein vollständiger Scan des Clusters, dessen Ergebnisse Azure Policy gemeldet werden.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint-Eigenschaften

Die Eigenschaft **details** der Auswirkung EnforceOPAConstraint hat die Untereigenschaften, die die Gatekeeper v3-Zugangskontrollregel beschreiben.

- **constraintTemplate** (erforderlich)
  - Die Einschränkungsvorlage CustomResourceDefinition (CRD), die neue Einschränkungen definiert. Die Vorlage definiert die Rego-Logik, das Einschränkungsschema und die Einschränkungsparameter, die über **values** von Azure Policy übergeben werden.
- **constraint** (erforderlich)
  - Die CRD-Implementierung der Einschränkungsvorlage. Verwendet Parameter, die über **values** als `{{ .Values.<valuename> }}`übergeben werden. Im folgenden Beispiel sind die Werte `{{ .Values.cpuLimit }}` und `{{ .Values.memoryLimit }}`.
- **values** (optional)
  - Definiert Parameter und Werte, die an die Einschränkung übergeben werden. Jeder Wert muss in der CRD der Einschränkungsvorlage vorhanden sein.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint-Beispiel

Beispiel: Gatekeeper v3-Zugangskontrollregel, um Ressourcenlimits für CPU- und Arbeitsspeicher für einen Container in Kubernetes festzulegen.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Diese Auswirkung wird bei einer Richtliniendefinition mit dem _Modus_`Microsoft.ContainerService.Data` verwendet. Sie dient zum Übergeben von Gatekeeper v2-Zugangskontrollregeln, die mit [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) definiert wurden, an [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) für [Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> Die Richtliniendefinitionen der eingeschränkten Vorschauversion mit der Auswirkung **EnforceRegoPolicy** und der zugehörigen Kategorie **Kubernetes Service** sind _veraltet_. Verwenden Sie stattdessen die Auswirkungen _audit_ und _deny_ mit dem Ressourcenanbietermodus `Microsoft.Kubernetes.Data`.

### <a name="enforceregopolicy-evaluation"></a>Auswertung von EnforceRegoPolicy

Der Zugangscontroller des Open Policy Agent bewertet jede neue Anforderung an den Cluster in Echtzeit.
Alle 15 Minuten erfolgt ein vollständiger Scan des Clusters, dessen Ergebnisse Azure Policy gemeldet werden.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy-Eigenschaften

Die Eigenschaft **details** der Auswirkung EnforceRegoPolicy hat die Untereigenschaften, die die Gatekeeper v2-Zugangskontrollregel beschreiben.

- **policyId** (erforderlich)
  - Ein eindeutiger Name, der als Parameter an die Rego-Zugangskontrollregel übergeben wird.
- **policy** (erforderlich)
  - Gibt den URI der Rego-Zugangskontrollregel an.
- **policyParameters** (optional)
  - Definiert Parameter und Werte, die an die Rego-Richtlinie übergeben werden.

### <a name="enforceregopolicy-example"></a>Beispiel für EnforceRegoPolicy

Beispiel: Gatekeeper v2-Zugangskontrollregel, um nur die angegebenen Containerimages in AKS zuzulassen.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Ändern

„Modify“ wird verwendet, um Eigenschaften oder Tags während der Erstellung oder Aktualisierung eines Abonnements oder einer Ressource hinzuzufügen, zu aktualisieren oder zu entfernen. Ein häufiges Beispiel ist die Aktualisierung von Tags für Ressourcen wie „costCenter“. Bestehende, nicht konforme Ressourcen können mit einem [Wartungstask](../how-to/remediate-resources.md) bereinigt werden. Eine einzelne Modify-Regel kann eine beliebige Anzahl von Vorgängen aufweisen.

Die folgenden Vorgänge werden von „Modify“ unterstützt:

- Hinzufügen, Ersetzen oder Entfernen von Ressourcentags. Für Tags sollte `mode` bei einer Modify-Richtlinie auf _Indexed_ festgelegt sein, es sei denn, die Zielressource ist eine Ressourcengruppe.
- Hinzufügen oder Ersetzen des Werts eines verwalteten Identitätstyps (`identity.type`) von virtuellen Computern und VM-Skalierungsgruppen
- Hinzufügen oder Ersetzen der Werte bestimmter Aliase
  - Verwenden Sie `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    in Azure PowerShell **4.6.0** oder höher, um eine Liste der Aliase abzurufen, die mit „Modify“ verwendet werden können.

> [!IMPORTANT]
> Wenn Sie Tags verwalten, wird empfohlen, „Modify“ anstelle von „Append“ zu verwenden, da „Modify“ zusätzliche Vorgangstypen und die Möglichkeit bietet, vorhandene Ressourcen zu korrigieren. Allerdings wird „Append“ empfohlen, wenn Sie keine verwaltete Identität erstellen können oder der Alias für die Ressourceneigenschaft von „Modify“ noch nicht unterstützt wird.

### <a name="modify-evaluation"></a>Auswertung von „Modify“

Die Auswertung von „Modify“ erfolgt vor der Anforderungsverarbeitung durch einen Ressourcenanbieter während der Erstellung oder Aktualisierung einer Ressource. Die Modify-Vorgänge werden auf den Anforderungsinhalt angewendet, wenn die **if**-Bedingung der Richtlinienregel erfüllt wird. Jeder Modify-Vorgang kann eine Bedingung angeben, die bestimmt, wann er angewendet wird. Vorgänge mit Bedingungen, die _false_ ergeben, werden übersprungen.

Wenn ein Alias angegeben wird, werden die folgenden zusätzlichen Überprüfungen durchgeführt, um sicherzustellen, dass der Anforderungsinhalt vom Modify-Vorgang nicht in einer Weise geändert wird, dass er vom Ressourcenanbieter abgelehnt wird:

- Die Eigenschaft, der der Alias zugeordnet ist, wird in der API-Version der Anforderung als „Modifiable“ gekennzeichnet.
- Der Tokentyp im Modify-Vorgang entspricht dem erwarteten Tokentyp für die Eigenschaft in der API-Version der Anforderung.

Wenn eine dieser Überprüfungen fehlschlägt, greift die Richtlinienauswertung auf den angegebenen **conflictEffect** zurück.

> [!IMPORTANT]
> Es wird empfohlen, dass Definitionen von „Ändern“, die Aliase enthalten, _Überprüfung_ **Konfliktauswirkung** verwenden. Damit wird vermieden, dass Anfragen mit API-Versionen fehlschlagen, bei denen die zugeordnete Eigenschaft nicht änderbar ist. Wenn sich derselbe Alias zwischen API-Versionen unterschiedlich verhält, können bedingte Modify-Vorgänge verwendet werden, um den für die einzelnen API-Versionen verwendeten Modify-Vorgang zu bestimmen.

Wenn eine Richtliniendefinition mit Auswirkung „Modify“ im Rahmen eines Auswertungszyklus ausgeführt wird, werden keine Änderungen an bereits vorhandenen Ressourcen durchgeführt. Stattdessen werden alle Ressourcen mit Erfüllung der **if**-Bedingung als nicht konform markiert.

### <a name="modify-properties"></a>Eigenschaften von „Modify“

Die **details**-Eigenschaft der Modify-Auswirkung enthält alle Untereigenschaften, die die für die Bereinigung erforderlichen Berechtigungen definieren, und die **Vorgänge**, mit denen Tagwerte hinzugefügt, aktualisiert oder entfernt werden.

- **roleDefinitionIds** (erforderlich)
  - Diese Eigenschaft muss ein Array von Zeichenfolgen enthalten, das mit der Rollen-ID der rollenbasierten Zugriffssteuerung übereinstimmt, auf die das Abonnement zugreifen kann. Weitere Informationen finden Sie unter [Korrigieren nicht konformer Ressourcen](../how-to/remediate-resources.md#configure-policy-definition).
  - Die definierte Rolle muss alle Vorgänge einbeziehen, die der Rolle [Mitwirkender](../../../role-based-access-control/built-in-roles.md#contributor) erteilt wurden.
- **conflictEffect** (optional)
  - Bestimmt, welche Richtliniendefinition verwendet wird, wenn dieselbe Eigenschaft durch mehrere Richtliniendefinitionen geändert wird oder wenn der Modify-Vorgang für den angegebenen Alias nicht funktioniert.
    - Bei neuen oder aktualisierten Ressourcen hat die Richtliniendefinition mit _deny_ Vorrang. Von Richtliniendefinitionen mit _audit_ werden alle Vorgänge (**operations**) übersprungen. Sind mehrere Richtliniendefinitionen mit _deny_ vorhanden, wird die Anforderung als Konflikt abgelehnt. Enthalten alle Richtliniendefinitionen _audit_, wird keiner der Vorgänge (**operations**) der in Konflikt stehenden Richtliniendefinitionen verarbeitet.
    - Falls bei vorhandenen Ressourcen mehrere Richtliniendefinition _deny_ enthalten, lautet der Konformitätsstatus _Konflikt_. Ist _deny_ in maximal einer Richtliniendefinition enthalten, wird von den einzelnen Zuweisungen jeweils der Konformitätsstatus _Nicht konform_ zurückgegeben.
  - Verfügbare Werte: _audit_, _deny_, _disabled_
  - Standardwert: _deny_
- **operations** (erforderlich)
  - Ein Array aller Tagvorgänge, die auf übereinstimmenden Ressourcen ausgeführt werden sollen.
  - Eigenschaften:
    - **operation** (erforderlich)
      - Definiert, welche Maßnahme bei einer übereinstimmenden Ressource ergriffen werden sollen. Optionen sind: _addOrReplace_, _Add_, _Remove_. _Add_ verhält sich ähnlich wie die [Append](#append)-Auswirkung.
    - **field** (erforderlich)
      - Das Tag, das hinzugefügt, ersetzt oder entfernt werden soll. Tagnamen müssen derselben Namenskonvention für andere [Felder](./definition-structure.md#fields) entsprechen.
    - **value** (optional)
      - Der Wert, auf den das Tag festgelegt werden soll.
      - Diese Eigenschaft ist erforderlich, wenn **operation**_addOrReplace_ oder _Add_ ist.
    - **condition** (optional)
      - Eine Zeichenfolge mit einem Azure Policy-Sprachausdruck und [Richtlinienfunktionen](./definition-structure.md#policy-functions), der _true_ oder _false_ ergibt.
      - Die folgenden Richtlinienfunktionen werden nicht unterstützt: `field()`, `resourceGroup()`, `subscription()`

### <a name="modify-operations"></a>Vorgänge für „Modify“

Das **operations**-Eigenschaftenarray ermöglicht es, mehrere Tags auf unterschiedliche Weise aus einer einzelnen Richtliniendefinition zu ändern. Jeder Vorgang besteht aus den Eigenschaften **operation**, **field** und **value**. „Operation“ bestimmt, wie die Wartungsaufgabe mit den Tags verfährt, „field“ bestimmt, welches Tag geändert wird und „value“ definiert die neue Einstellung für dieses Tag. Im folgenden Beispiel werden die folgenden Tag-Änderungen vorgenommen:

- Legt das `environment`-Tag auf „Test“ fest, auch wenn es bereits mit einem anderen Wert vorhanden ist.
- Entfernt das Tag `TempResource`.
- Legt das `Dept`-Tag auf den Richtlinienparameter _DeptName_ fest, der bei der Richtlinienzuweisung konfiguriert wurde.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Die **operation**-Eigenschaft hat die folgenden Optionen:

|Vorgang |BESCHREIBUNG |
|-|-|
|addOrReplace |Fügt die definierte Eigenschaft oder das definierte Tag und den Wert zur Ressource hinzu, auch wenn die Eigenschaft oder das Tag bereits mit einem anderen Wert vorhanden ist. |
|Hinzufügen |Fügt der Ressource die definierte Eigenschaft oder das definierte Tag und den Wert hinzu. |
|Remove (Entfernen) |Entfernt die definierte Eigenschaft oder das definierte Tag aus der Ressource. |

### <a name="modify-examples"></a>Beispiele für „Modify“

Beispiel 1: Fügen Sie das `environment`-Tag hinzu, und ersetzen Sie vorhandene `environment`-Tags durch „Test“:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Beispiel 2: Entfernen Sie das `env`-Tag, und fügen Sie das `environment`-Tag hinzu oder ersetzen Sie vorhandene `environment`-Tags durch einen parametrisierten Wert:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

Beispiel 3: Sicherstellen, dass ein Speicherkonto keinen öffentlichen Zugriff auf Blobs zulässt. Der Modify-Vorgang wird nur bei der Auswertung von Anforderungen mit einer API-Version größer oder gleich 2019-04-01 angewendet:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Schichten von Richtliniendefinitionen

Eine Ressource kann von mehreren Zuweisungen betroffen sein. Diese Zuweisungen können für denselben Bereich oder verschiedene Bereiche gelten. Für jede dieser Zuweisungen ist wahrscheinlich auch eine andere Auswirkung definiert. Die Bedingung und die Auswirkung für jede Richtlinie werden unabhängig ausgewertet. Beispiel:

- Richtlinie 1
  - Schränkt den Ressourcenstandort auf „USA, Westen“ ein
  - Abonnement A zugewiesen
  - Auswirkung „deny“
- Richtlinie 2
  - Schränkt den Ressourcenstandort auf „USA, Osten“ ein
  - Ressourcengruppe B im Abonnement A zugewiesen
  - Auswirkung „audit“

Dies würde zu folgendem Ergebnis führen:

- Jede Ressource, die sich bereits in Ressourcengruppe B und am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 konform und gemäß Richtlinie 1 nicht konform.
- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 nicht konform und auch gemäß Richtlinie 1 nicht konform, wenn sie sich nicht am Standort „USA, Westen“ befindet.
- Jede neue Ressource in Abonnement A, die nicht am Standort „USA, Westen“ erstellt wird, wird von Richtlinie 1 abgelehnt.
- Jede neue Ressource in Abonnement A und Ressourcengruppe B am Standort „USA, Westen“ wird erstellt und ist gemäß Richtlinie 2 nicht konform.

Wenn sowohl Richtlinie 1 als auch Richtlinie 2 die Auswirkung „deny“ verwenden, ändert sich die Situation folgendermaßen:

- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Osten“ befindet, ist gemäß Richtlinie 2 nicht konform.
- Jede Ressource, die sich bereits in Ressourcengruppe B und nicht am Standort „USA, Westen“ befindet, ist gemäß Richtlinie 1 nicht konform.
- Jede neue Ressource in Abonnement A, die nicht am Standort „USA, Westen“ erstellt wird, wird von Richtlinie 1 abgelehnt.
- Jede neue Ressource in Ressourcengruppe B von Abonnement A wird abgelehnt.

Jede Zuweisung wird einzeln ausgewertet. Daher ist es nicht möglich, dass eine Ressource aufgrund unterschiedlicher Bereiche „durch das Netz schlüpfen“ kann. Das Schichten von Richtliniendefinitionen führt **kumulativ zur stärksten Einschränkung**. Wenn beispielsweise sowohl Richtlinie 1 als auch Richtlinie 2 die Auswirkung „deny“ verwenden, wird eine Ressource durch die sich überschneidenden und konfliktverursachenden Richtliniendefinitionen blockiert. Wenn die Ressource dennoch im Zielbereich erstellt werden soll, überprüfen Sie die Ausnahmen für jede Zuweisung, um sicherzustellen, dass die richtigen Richtlinienzuweisungen auf die richtigen Bereiche angewendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](definition-structure.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
