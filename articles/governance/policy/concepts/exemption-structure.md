---
title: Details der Struktur von Richtlinienausnahmen
description: Hier wird die Definition von Richtlinienausnahmen beschrieben, die von Azure Policy verwendet wird, um Ressourcen von der Auswertung von Initiativen oder Definitionen auszuschließen.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4c38f872a17869b957bce415204042582454dfd9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356687"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy-Ausnahmenstruktur

Die Funktion für Azure Policy-Ausnahmen (Vorschau) wird verwendet, um eine Ressourcenhierarchie oder eine einzelne Ressource von der Bewertung von Initiativen oder Definitionen _auszunehmen_. Ressourcen, die _ausgenommen_ sind, werden auf die allgemeine Konformität angerechnet, können aber nicht ausgewertet werden oder einen vorübergehenden Verzicht aufweisen. Weitere Informationen finden Sie in der [Übersicht zu Bereichen in Azure Policy](./scope.md). Azure Policy-Ausnahmen funktionieren nur mit [Resource Manager-Modi](./definition-structure.md#resource-manager-modes), nicht mit [Ressourcenanbieter-Modi](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Die Funktion ist während der **Vorschau** kostenfrei. Ausführliche Preisinformationen finden Sie in der [Azure Policy-Preisübersicht](https://azure.microsoft.com/pricing/details/azure-policy/). Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eine Richtlinienausnahme wird mithilfe von JSON erstellt. Die Richtlinienausnahme enthält Elemente für Folgendes:

- Anzeigename
- description
- metadata
- Richtlinienzuweisung
- Richtliniendefinitionen in einer Initiative
- Ausnahmekategorie
- expiration

> [!NOTE]
> Eine Richtlinienausnahme wird als untergeordnetes Objekt in der Ressourcenhierarchie oder der einzelnen Ressource erstellt, für die die Ausnahme gewährt wird. Das Ziel wird daher nicht in die Ausnahmedefinition eingeschlossen.

Der folgende JSON-Code zeigt ein Beispiel für eine Richtlinienausnahme in der Kategorie **Verzicht** bei einer Ressource für eine Initiativenzuweisung mit dem Namen `resourceShouldBeCompliantInit`. Die Ressource ist nur von zwei der Richtliniendefinitionen in der Initiative _ausgenommen_, der benutzerdefinierten Richtliniendefinition `customOrgPolicy` (Verweis `requiredTags`) und der integrierten Richtliniendefinition „Zulässige Standorte“ (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c`, Referenz `allowedLocations`):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Codeausschnitt der entsprechenden Initiative mit den übereinstimmenden `policyDefinitionReferenceIds`, die von der Richtlinienausnahme verwendet werden:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Anzeigename und Beschreibung

Mit **displayName** und **description**, geben Sie die Richtlinienausnahme und den Kontext für ihre Verwendung mit der spezifischen Ressource an. **displayName** hat eine maximale Länge von _128_ Zeichen, und **description** hat eine maximale Länge von _512_ Zeichen.

## <a name="metadata"></a>Metadaten

Mit der Eigenschaft **metadata** können Sie untergeordnete Eigenschaften erstellen, die zum Speichern relevanter Informationen benötigt werden. Im obigen Beispiel enthalten die Eigenschaften **requestedBy**, **approvedBy**, **approvedOn** und **ticketRef** Kundenwerte mit Informationen dazu, wer die Ausnahme wann angefordert hat, sowie ein internes Nachverfolgungsticket für die Anforderung. Diese **metadata**-Eigenschaften sind Beispiele, aber sie sind nicht erforderlich, und **metadata** ist nicht auf diese untergeordneten Eigenschaften beschränkt.

## <a name="policy-assignment-id"></a>Richtlinienzuweisungs-ID

Dieses Feld muss den vollständigen Pfadname einer Richtlinien- oder Initiativenzuweisung enthalten.
`policyAssignmentId` ist eine Zeichenfolge und kein Array. Diese Eigenschaft definiert, von welcher Zuweisung die übergeordnete Ressourcenhierarchie oder einzelne Ressource _ausgenommen_ ist.

## <a name="policy-definition-ids"></a>Richtliniendefinitions-IDs

Wenn die `policyAssignmentId` sich auf eine Initiativenzuweisung bezieht, kann mit der `policyDefinitionReferenceIds`-Eigenschaft angegeben werden, für welche Richtliniendefinitionen in der Initiative eine Ausnahme für die Objektressource gilt. Da die Ressource von einer oder mehreren enthaltenen Richtliniendefinition ausgenommen sein kann, ist diese Eigenschaft ein _Array_. Die Werte müssen den Werten in der Initiativendefinition in den `policyDefinitions.policyDefinitionReferenceId`-Feldern entsprechen.

## <a name="exemption-category"></a>Ausnahmekategorie

Es gibt zwei Ausnahmekategorien, mit deren Hilfe Ausnahmen gruppiert werden können:

- **Gemindert**: Die Ausnahme wird gewährt, da die Absicht der Richtlinie durch eine andere Methode erfüllt wird.
- **Verzicht**: Die Ausnahme wird gewährt, da der Zustand der Nichtkonformität der Ressource vorübergehend akzeptiert wird. Ein weiterer Grund, diese Kategorie zu verwenden, ist für eine Ressource oder Ressourcenhierarchie, die von einer oder mehreren Definitionen in einer Initiative, aber nicht von der gesamten Initiative ausgeschlossen werden sollte.

## <a name="expiration"></a>Ablauf

Mit der `expiresOn`-Eigenschaft legen Sie fest, wann eine Ressourcenhierarchie oder eine einzelne Ressource nicht mehr von einer Zuweisung _ausgenommen_ wird. Diese optionale Eigenschaft muss im Universal ISO 8601-DateTime-Format `yyyy-MM-ddTHH:mm:ss.fffffffZ` angegeben werden.

> [!NOTE]
> Die Richtlinienausnahmen werden nicht gelöscht, wenn das durch `expiresOn` definierte Datum erreicht wird. Das Objekt wird für Aufzeichnungszwecke beibehalten, aber die Ausnahme wird nicht mehr berücksichtigt.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Die Azure RBAC-Berechtigungen, die zum Verwalten von Richtlinienausnahmeobjekten erforderlich sind, befinden sich in der Vorgangsgruppe `Microsoft.Authorization/policyExemptions`. Die integrierten Rollen [Mitwirkender bei Ressourcenrichtlinien](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) und [Sicherheitsadministrator](../../../role-based-access-control/built-in-roles.md#security-admin) verfügen beide über die Berechtigungen `read` und `write`. [Policy Insights-Datenschreiber (Vorschau)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) verfügt über die Berechtigung `read`.

Das Gewähren von Ausnahmen hat weitreichende Auswirkungen, daher weisen Ausnahmen zusätzliche Sicherheitsmaßnahmen auf. Es ist nicht nur der `Microsoft.Authorization/policyExemptions/write`-Vorgang für die Ressourcenhierarchie oder einzelne Ressource erforderlich, sondern der Ersteller der Ausnahme muss auch über das Verb `exempt/Action` für die Zielzuweisung verfügen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Struktur von Richtliniendefinitionen](./definition-structure.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).