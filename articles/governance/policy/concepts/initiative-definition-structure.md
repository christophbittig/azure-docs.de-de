---
title: Details der Struktur von Initiativendefinitionen
description: Beschreibt, wie Initiativendefinitionen von Richtlinien verwendet werden, um Richtliniendefinitionen für die Bereitstellung in Azure-Ressourcen in Ihrer Organisation zu gruppieren.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 9ef2d5a77d9ae957a81517b635d923cd80519b76
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356685"
---
# <a name="azure-policy-initiative-definition-structure"></a>Struktur von Azure Policy-Initiativendefinitionen

Mithilfe von Initiativen können Sie mehrere verwandte Richtliniendefinitionen gruppieren, um Zuweisungen und das Verwalten zu vereinfachen, indem Sie mit einer Gruppe als einzelnes Element arbeiten. Beispielsweise können Sie zusammengehörige Richtliniendefinitionen zum Markieren in einer einzelnen Initiative gruppieren. Anstatt jede Richtlinie einzeln zuzuweisen, wenden Sie die Initiative an.

Eine Richtlinieninitiativendefinition wird mithilfe von JSON erstellt. Die Richtlinieninitiativendefinition enthält Elemente für Folgendes:

- Anzeigename
- description
- metadata
- parameters
- Richtliniendefinitionen
- Richtliniengruppen (diese Eigenschaft ist Teil des Features zur [Einhaltung gesetzlicher Bestimmungen (Vorschau)](./regulatory-compliance.md))

Im folgenden Beispiel wird veranschaulicht, wie eine Initiative zur Behandlung der Tags `costCenter` und `productName` erstellt werden kann. Es werden zwei integrierte Richtlinien verwendet, um den Standardtagwert anzuwenden.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Integrierte Richtlinien und Muster von Azure Policy finden Sie unter [Azure Policy-Beispiele](../samples/index.md).

## <a name="metadata"></a>Metadaten

In der optionalen `metadata`-Eigenschaft werden Informationen zur Richtlinieninitiativendefinition gespeichert.
Kunden können alle für ihre Organisation nützlichen Eigenschaften und Werte in `metadata` definieren. Es gibt jedoch einige _allgemeine_ Eigenschaften, die von Azure Policy und integrierten Richtlinien verwendet werden.

### <a name="common-metadata-properties"></a>Allgemeine Metadateneigenschaften

- `version` (Zeichenfolge): Verfolgt Details zur Version des Inhalts einer Richtlinieninitiativendefinition nach.
- `category` (Zeichenfolge): Legt fest, unter welcher Kategorie im Azure-Portal die Richtliniendefinition angezeigt wird.

  > [!NOTE]
  > Bei einer Initiative zur [Einhaltung gesetzlicher Bestimmungen](./regulatory-compliance.md) muss `category` auf **Einhaltung gesetzlicher Bestimmungen** festgelegt sein.

- `preview` (Boolescher Wert): Flag mit einem der Werte TRUE oder FALSE, das anzeigt, ob sich die Richtlinieninitiativendefinition in der _Vorschauphase_ befindet.
- `deprecated` (Boolescher Wert): Flag mit einem der Werte TRUE oder FALSE, das anzeigt, ob die Richtlinieninitiativendefinition als _veraltet_ markiert wurde.

> [!NOTE]
> Der Azure Policy-Dienst verwendet die Eigenschaften `version`, `preview` und `deprecated`, um den Grad der Änderung einer integrierten Richtliniendefinition oder Initiative und einen Status zu übermitteln. Das Format von `version` ist `{Major}.{Minor}.{Patch}`. Bestimmte Zustände, z. B. _veraltet_ oder _Vorschau_, werden der `version`-Eigenschaft angehängt oder in eine andere Eigenschaft als **boolescher** Wert eingefügt. Weitere Informationen zu der Methodik, mit der Azure Policy die Versionen integrierter Richtlinien verwaltet, finden Sie unter [Built-in versioning](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md) (Versionsverwaltung für integrierte Richtlinien).

## <a name="parameters"></a>Parameter

Parameter vereinfachen Ihre Richtlinienverwaltung, indem sie die Anzahl von Richtliniendefinitionen reduzieren. Diese Parameter verhalten sich wie Felder in einem Formular: `name`, `address`, `city`, `state`. Diese Parameter bleiben immer gleich, allerdings ändern sich ihre Werte auf Grundlage der Einträge des Einzelnen.
Parameter funktionieren beim Erstellen von Richtlinieninitiativen genauso. Wenn Sie Parameter in eine Richtlinieninitiativendefinition einschließen, können Sie sie in den enthaltenen Richtlinien wiederverwenden.

> [!NOTE]
> Sobald eine Initiative zugewiesen wurde, können die Parameter der Initiativenebene nicht mehr geändert werden. Aus diesem Grund wird empfohlen, bei der Definition des Parameters einen **defaultValue** festzulegen.

### <a name="parameter-properties"></a>Parametereigenschaften

Ein Parameter weist die folgenden Eigenschaften auf, die in der Richtlinieninitiativendefinition verwendet werden:

- `name`: Der Name des Parameters. Wird in der Richtlinienregel von der Bereitstellungsfunktion `parameters` verwendet. Weitere Informationen finden Sie unter [Verwenden eines Parameterwerts](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Bestimmt, ob der Parameter eine **Zeichenfolge**, ein **Array**, ein **Objekt**, ein **boolescher Wert**, eine **ganze Zahl** oder vom Typ **float** oder **datetime** ist.
- `metadata`: Definiert untergeordnete Eigenschaften, die hauptsächlich vom Azure-Portal verwendet werden, um benutzerfreundliche Informationen anzuzeigen:
  - `description`: Die Erläuterung des Zwecks des Parameters. Kann verwendet werden, um Beispiele zulässiger Werte bereitzustellen.
  - `displayName`: Der Anzeigename des Parameters im Portal.
  - `strongType`: (Optional) Wird verwendet, wenn die Richtliniendefinition über das Portal zugewiesen wird. Bietet eine kontextbezogene Liste. Weitere Informationen finden Sie unter [strongType](#strongtype).
- `defaultValue`: (Optional) Legt den Wert des Parameters in einer Zuweisung fest, wenn kein Wert angegeben ist.
- `allowedValues`: (Optional) Stellt ein Array von Werten bereit, die der Parameter bei der Zuweisung akzeptiert.

Beispielsweise können Sie eine Richtlinieninitiativendefinition erstellen, um die Speicherorte von Ressourcen in den verschiedenen enthaltenen Richtliniendefinitionen einzuschränken. Ein Parameter für diese Richtlinieninitiativendefinition kann **allowedLocations** heißen. Der-Parameter ist dann für jede enthaltene Richtliniendefinition verfügbar und wird bei der Zuweisung der Richtlinieninitiative definiert.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Übergeben eines Parameterwerts an eine Richtliniendefinition

Sie deklarieren, welche Initiativenparameter Sie an welche Richtliniendefinitionen im Array [policyDefinitions](#policy-definitions) der Initiativendefinition übergeben. Der Parametername kann identisch sein, durch die Verwendung unterschiedlicher Namen in den Initiativen als in den Richtliniendefinitionen wird jedoch die Lesbarkeit des Codes verbessert.

Der zuvor definierte Initiativenparameter **init_allowedLocations** kann beispielsweise wie folgt an mehrere enthaltene Richtliniendefinitionen und ihre Parameter **sql_locations** und **vm_locations** übergeben werden:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

In diesem Beispiel wird auf den Parameter **init_allowedLocations** verwiesen, der unter [Parametereigenschaften](#parameter-properties) vorgestellt wurde.

### <a name="strongtype"></a>strongType

Innerhalb der `metadata`-Eigenschaft können Sie mit **strongType** im Azure-Portal eine Liste der Optionen mit Mehrfachauswahl angeben. **strongType** kann ein unterstützter _Ressourcentyp_ oder ein zulässiger Wert sein. Verwenden Sie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider), um festzustellen, ob ein _Ressourcentyp_ für **strongType** zulässig ist.

Es werden einige Ressourcentypen unterstützt, die von **Get-AzResourceProvider** nicht zurückgegeben werden. Diese Ressourcentypen sind:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Die zulässigen Werte für **strongType**, die keine Ressourcentypen sind, umfassen:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Richtliniendefinitionen

Der Teil `policyDefinitions` der Initiativendefinition ist ein _Array_, dessen vorhandene Richtliniendefinitionen in der Initiative enthalten sind. Wie in [Übergeben eines Parameterwerts an eine Richtliniendefinition](#passing-a-parameter-value-to-a-policy-definition) beschrieben, werden über diese Eigenschaft [Initiativenparameter](#parameters) an die Richtliniendefinition übergeben.

### <a name="policy-definition-properties"></a>Eigenschaften von Richtliniendefinitionen

Jedes _Arrayelement_, das eine Richtliniendefinition darstellt, weist die folgenden Eigenschaften auf:

- `policyDefinitionId` (Zeichenfolge): Die ID der benutzerdefinierten oder integrierten Richtliniendefinition, die eingeschlossen werden soll.
- `policyDefinitionReferenceId` (Zeichenfolge): Ein Kurzname für die enthaltene Richtliniendefinition.
- `parameters`: (Optional:) Die Name-Wert-Paare für die Übergabe eines Initiativenparameters an die enthaltene Richtliniendefinition als Eigenschaft in dieser Richtliniendefinition. Weitere Informationen finden Sie unter [Parameter](#parameters).
- `groupNames` (Array von Zeichenfolgen): (Optional:) Die Gruppe, in der die Richtliniendefinition ein Mitglied ist. Weitere Informationen finden Sie unter [Richtliniengruppen](#policy-definition-groups).

Im Folgenden finden Sie ein Beispiel für `policyDefinitions` mit zwei enthaltenen Richtliniendefinitionen, an die jeweils derselbe Initiativenparameter übergeben wird:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Richtliniendefinitionsgruppen

Richtliniendefinitionen in einer Initiativdefinition können gruppiert und kategorisiert werden. Die Azure Policy-Funktion [Einhaltung gesetzlicher Bestimmungen](./regulatory-compliance.md) (Vorschauversion) verwendet diese Eigenschaft zum Gruppieren von Definitionen in **Kontrollen** und **Compliancedomänen**. Diese Informationen sind in der _Arrayeigenschaft_ `policyDefinitionGroups` definiert. Weitere Gruppierungsdetails finden Sie in einem von Microsoft erstellten **policyMetadata**-Objekt. Weitere Informationen finden Sie unter [Metadatenobjekte](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parameter von Richtliniendefinitionsgruppen

Jedes _Arrayelement_ in `policyDefinitionGroups` muss die beiden folgenden Eigenschaften aufweisen:

- `name` (Zeichenfolge) \[erforderlich\]: Der Kurzname der **Gruppe**. Bei der Einhaltung gesetzlicher Bestimmungen die **Kontrolle**. Der Wert dieser Eigenschaft wird von `groupNames` in `policyDefinitions` verwendet.
- `category` (Zeichenfolge): Die Hierarchie, der die Gruppe angehört. Bei der Einhaltung gesetzlicher Bestimmungen die **Compliancedomäne** der Kontrolle.
- `displayName` (Zeichenfolge): Der Anzeigename für die **Gruppe** oder **Kontrolle**. Wird im Portal verwendet.
- `description` (Zeichenfolge): Eine Beschreibung des Umfangs der **Gruppe** oder **Kontrolle**.
- `additionalMetadataId` (Zeichenfolge): Der Speicherort des [policyMetadata](#metadata-objects)-Objekts, das weitere Details zur **Kontrolle** und zur **Compliancedomäne** enthält.

  > [!NOTE]
  > Kunden können auf ein vorhandenes [policyMetadata](#metadata-objects)-Objekt verweisen. Diese Objekte sind jedoch _schreibgeschützt_ und werden nur von Microsoft erstellt.

Ein Beispiel für die `policyDefinitionGroups`-Eigenschaft aus der vordefinierten NIST-Initiativendefinition sieht wie folgt aus:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Metadatenobjekte

Die von Microsoft erstellten integrierten Richtlinien zur Einhaltung gesetzlicher Bestimmungen enthalten zusätzliche Informationen zu den einzelnen Kontrollen.
Für diese Informationen gilt:

- Sie werden im Azure-Portal in der Übersicht einer **Kontrolle** für eine Initiative zur Einhaltung gesetzlicher Bestimmungen angezeigt.
- Sie sind über eine REST-API verfügbar. Weitere Informationen finden Sie unter dem Ressourcenanbieter `Microsoft.PolicyInsights` und der [policyMetadata-Vorgangsgruppe](/rest/api/policy/policymetadata/getresource).
- Sie sind über die Azure-Befehlszeilenschnittstelle verfügbar. Weitere Informationen finden Sie unter dem Befehl [az policy metadata](/cli/azure/policy/metadata).

> [!IMPORTANT]
> Metadatenobjekte zur Einhaltung gesetzlicher Bestimmungen sind _schreibgeschützt_ und können nicht von Kunden erstellt werden.

Die Metadaten für eine Richtliniengruppierung weisen die folgenden Informationen im Knoten `properties` auf:

- `metadataId`: Die **Kontrollen-ID**, auf die sich die Gruppierung bezieht.
- `category` (erforderlich): Die **Compliancedomäne**, der die **Kontrolle** angehört.
- `title` (erforderlich): Der Anzeigename für die **Kontrollen-ID**.
- `owner` (erforderlich): Gibt an, wer in Azure für die Kontrolle zuständig ist: _Kunde_, _Microsoft_, _Freigegeben_.
- `description`: Weitere Informationen zur Kontrolle.
- `requirements`: Details zur Zuständigkeit für die Implementierung der Kontrolle.
- `additionalContentUrl`: Ein Link zu weiteren Informationen zur Kontrolle. Diese Eigenschaft ist in der Regel ein Link zum Abschnitt der Dokumentation, der diese Kontrolle im Compliancestandard behandelt.

Nachstehend finden Sie ein Beispiel für das **policyMetadata**-Objekt. Diese Beispielmetadaten gehören zur Kontrolle _NIST SP 800-53 R4 AC-1_.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Siehe [Definitionsstruktur](./definition-structure.md)
- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
