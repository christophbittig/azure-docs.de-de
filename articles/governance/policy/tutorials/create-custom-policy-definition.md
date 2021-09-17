---
title: 'Tutorial: Erstellen einer benutzerdefinierten Richtliniendefinition'
description: In diesem Tutorial erstellen Sie eine benutzerdefinierte Richtliniendefinition für Azure Policy, um benutzerdefinierte Geschäftsregeln für Ihre Azure-Ressourcen zu erzwingen.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: ee7939461c6d655447ebbd32079ec2e1345a89ad
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323616"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Tutorial: Erstellen einer benutzerdefinierten Richtliniendefinition

Mit einer benutzerdefinierten Richtliniendefinition können Kunden eigene Regeln für die Verwendung von Azure definieren. Mit diesen Regeln wird häufig Folgendes erzwungen:

- Sicherheitsmaßnahmen
- Kostenverwaltung
- Organisationsspezifische Regeln (z. B. Benennung oder Speicherorte)

Unabhängig davon, welche geschäftlichen Gründe für die Erstellung einer benutzerdefinierten Richtlinie vorliegen, sind die Schritte zum Definieren der neuen benutzerdefinierten Richtlinie identisch.

Sehen Sie sich vor dem Erstellen einer benutzerdefinierten Richtlinie die [Richtlinienbeispiele](../samples/index.md) an, um zu ermitteln, ob bereits eine Richtlinie vorhanden ist, die Ihre Anforderungen erfüllt.

Der Ansatz zur Erstellung einer benutzerdefinierten Richtlinie umfasst die folgenden Schritte:

> [!div class="checklist"]
> - Ermitteln Ihrer geschäftlichen Anforderungen
> - Zuordnen der einzelnen Anforderungen zu einer Azure-Ressourceneigenschaft
> - Zuordnen der Eigenschaft zu einem Alias
> - Ermitteln der zu verwendenden Auswirkung
> - Verfassen der Richtliniendefinition

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="identify-requirements"></a>Ermitteln der Anforderungen

Es ist wichtig, dass Sie sich vor dem Erstellen der Richtliniendefinition mit der Absicht der Richtlinie vertraut machen. Für dieses Tutorial verwenden wir eine gängige Sicherheitsanforderung von Unternehmen, um die erforderlichen Schritte zu verdeutlichen:

- Für jedes Speicherkonto muss HTTPS aktiviert sein.
- Für jedes Speicherkonto muss HTTP deaktiviert sein.

In Ihren Anforderungen sollten die gewünschten und unerwünschten Ressourcenzustände jeweils eindeutig angegeben werden.

Wir haben zwar den erwarteten Zustand der Ressource definiert, aber wird haben noch nicht festgelegt, was mit nicht konformen Ressourcen passieren soll. Azure Policy unterstützt viele [Effekte](../concepts/effects.md). Für dieses Tutorial definieren wir die geschäftliche Anforderung so, dass die Erstellung von Ressourcen verhindert wird, wenn sie mit den Geschäftsregeln nicht konform sind. Um dieses Ziel zu erreichen, verwenden wir die Auswirkung [Deny](../concepts/effects.md#deny) (Verweigern). Außerdem möchten wir die Möglichkeit haben, die Richtlinie für bestimmte Fälle auszusetzen. Hierfür verwenden wir die Auswirkung [Disabled](../concepts/effects.md#disabled) (Deaktiviert) und machen die Auswirkung zu einem [Parameter](../concepts/definition-structure.md#parameters) in der Richtliniendefinition.

## <a name="determine-resource-properties"></a>Ermitteln von Ressourceneigenschaften

Basierend auf der geschäftlichen Anforderung ist die Azure-Ressource, die mit Azure Policy überwacht werden soll, ein Speicherkonto. Wir wissen aber nicht, welche Eigenschaften in der Richtliniendefinition verwendet werden sollen. Da Azure Policy eine Evaluierung anhand der JSON-Darstellung der Ressource durchführt, müssen wir die Eigenschaften verstehen, die für diese Ressource verfügbar sind.

Es gibt viele Möglichkeiten, die Eigenschaften für eine Azure-Ressource zu ermitteln. In diesem Tutorial sehen wir uns die einzelnen Fälle an:

- Azure Policy-Erweiterung für VS Code
- Azure Resource Manager-Vorlagen (ARM-Vorlagen)
  - Export der vorhandenen Ressource
  - Umgebung für die Erstellung
  - Schnellstartvorlagen (GitHub)
  - Vorlagenreferenzdokumente
- Azure-Ressourcen-Explorer

### <a name="view-resources-in-vs-code-extension"></a>Anzeigen von Ressourcen in der VS Code-Erweiterung

Mit der [VS Code-Erweiterung](../how-to/extension-for-vscode.md#search-for-and-view-resources) können Sie Ressourcen in Ihrer Umgebung durchsuchen und die Resource Manager-Eigenschaften der einzelnen Ressourcen anzeigen.

### <a name="arm-templates"></a>ARM-Vorlagen

Es gibt mehrere Möglichkeiten, sich eine [ARM-Vorlage](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) anzusehen, die die zu verwaltende Eigenschaft enthält.

#### <a name="existing-resource-in-the-portal"></a>Vorhandene Ressource im Portal

Am einfachsten finden Sie Eigenschaften, indem Sie sich eine vorhandene Ressource desselben Typs ansehen. Ressourcen, die bereits mit der zu erzwingenden Einstellung konfiguriert sind, enthalten auch den Wert für den Vergleich.
Sehen Sie sich im Azure-Portal die Seite **Exportvorlage** (unter **Einstellungen**) für die jeweilige Ressource an.

> [!WARNING]
> Die vom Azure-Portal exportierte ARM-Vorlage kann nicht direkt in die Eigenschaft `deployment` für eine ARM-Vorlage in einer Richtliniendefinition vom Typ [deployIfNotExists](../concepts/effects.md#deployifnotexists) integriert werden.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Screenshot: Seite „Exportvorlage“ für eine vorhandene Ressource im Azure-Portal" border="false":::

Wenn Sie so für ein Speicherkonto vorgehen, wird eine Vorlage bereitgestellt, die der Vorlage in diesem Beispiel ähnelt:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Unter **properties** befindet sich der Wert **supportsHttpsTrafficOnly**, der auf **false** festgelegt ist. Diese Eigenschaft könnte die gesuchte Eigenschaft sein. Darüber hinaus lautet der Typ (**type**) der Ressource **Microsoft.Storage/storageAccounts**. Mit diesem Typ können wir die Richtlinie auf die Ressourcen dieses Typs beschränken.

#### <a name="create-a-resource-in-the-portal"></a>Erstellen einer Ressource im Portal

Eine weitere Möglichkeit zur Verwendung des Portals ist die Umgebung für die Ressourcenerstellung. Beim Erstellen eines Speicherkontos über das Portal können Sie auch auf der Registerkarte **Erweitert** die Option **Sicherheitstransfer erforderlich** verwenden. Diese Eigenschaft verfügt über die Optionen _Deaktiviert_ und _Aktiviert_. Für das Infosymbol wird zusätzlicher Text angezeigt, um zu bestätigen, dass es sich bei dieser Option wahrscheinlich um die gewünschte Eigenschaft handelt. Im Portal wird in diesem Fenster aber nicht der Name der Eigenschaft angegeben.

Die Registerkarte **Bewerten + erstellen** enthält unten den Link **Vorlage zur Automatisierung herunterladen**. Wenn Sie den Link auswählen, wird die Vorlage geöffnet, mit der die konfigurierte Ressource erstellt wird. In diesem Fall werden zwei wichtige Informationen angezeigt:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Hier wird der Eigenschaftentyp angegeben und außerdem bestätigt, dass es sich bei **supportsHttpsTrafficOnly** um die gewünschte Eigenschaft handelt.

#### <a name="quickstart-templates-on-github"></a>Schnellstartvorlagen auf GitHub

Unter [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) auf GitHub finden Sie Hunderte von ARM-Vorlagen, die für unterschiedliche Ressourcen bestimmt sind. Diese Vorlagen können eine gute Möglichkeit darstellen, um nach der gewünschten Ressourceneigenschaft zu suchen. Bei einigen Eigenschaften kann es erscheinen, als ob sie den gewünschten Zweck erfüllen, während sie eigentlich für die Steuerung eines anderen Sachverhalts gedacht sind.

#### <a name="resource-reference-docs"></a>Referenzdokumente zu Ressourcen

Gehen Sie wie folgt vor, um sich zu vergewissern, dass **supportsHttpsTrafficOnly** die richtige Eigenschaft ist: Überprüfen Sie die ARM-Vorlagenreferenz für die [Speicherkontoressource](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) des Speicheranbieters. Das properties-Objekt verfügt über eine Liste mit gültigen Parametern. Wenn Sie den Link [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) auswählen, wird eine Tabelle mit zulässigen Eigenschaften angezeigt. **supportsHttpsTrafficOnly** ist vorhanden, und die Beschreibung passt zu den zu erfüllenden geschäftlichen Anforderungen.

### <a name="azure-resource-explorer"></a>Azure-Ressourcen-Explorer

Eine weitere Möglichkeit zum Erkunden Ihrer Azure-Ressourcen ist der [Azure-Ressourcen-Explorer](https://resources.azure.com) (Vorschauversion). Da bei diesem Tool der Kontext Ihres Abonnements verwendet wird, müssen Sie sich auf der Website mit Ihren Azure-Anmeldeinformationen authentifizieren. Nach der Authentifizierung können Sie nach Anbietern, Abonnements, Ressourcengruppen und Ressourcen suchen.

Suchen Sie nach einer Speicherkontoressource, und sehen Sie sich die Eigenschaften an. Hier wird auch die Eigenschaft **supportsHttpsTrafficOnly** angezeigt. Wenn wir die Registerkarte **Dokumentation** wählen, ist erkennbar, dass die Beschreibung der Eigenschaft mit den zuvor ermittelten Informationen in den Referenzdokumenten übereinstimmt.

## <a name="find-the-property-alias"></a>Suchen nach dem Eigenschaftenalias

Wir haben die Ressourceneigenschaft identifiziert, aber wir müssen diese Eigenschaft einem [Alias](../concepts/definition-structure.md#aliases) zuordnen.

Es gibt verschiedene Möglichkeiten, die Aliase für eine Azure-Ressource zu ermitteln. In diesem Tutorial sehen wir uns die einzelnen Fälle an:

- Azure Policy-Erweiterung für VS Code
- Azure CLI
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>Abrufen von Aliasen in der VS Code-Erweiterung

Mit der Azure Policy-Erweiterung für die VS Code-Erweiterung können Sie ganz einfach Ihre Ressourcen durchsuchen und [Aliase ermitteln](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> Die VS Code-Erweiterung stellt nur Eigenschaften des Resource Manager-Modus zur Verfügung und zeigt keine Eigenschaften des [Ressourcenanbietermodus](../concepts/definition-structure.md#mode) an.

### <a name="azure-cli"></a>Azure CLI

In der Azure-Befehlszeilenschnittstelle wird die Befehlsgruppe `az provider` verwendet, um nach Ressourcenaliasen zu suchen. Wir filtern anhand der Details, die wir zuvor zur Azure-Ressource erhalten haben, nach dem Namespace **Microsoft.Storage**.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

In den Ergebnissen wird ein Alias angezeigt, der von den Speicherkonten unterstützt wird und den Namen **supportsHttpsTrafficOnly** hat. Das Vorhandensein dieses Alias bedeutet, dass wir die Richtlinie schreiben können, um unsere geschäftlichen Anforderungen zu erzwingen!

### <a name="azure-powershell"></a>Azure PowerShell

In Azure PowerShell wird das Cmdlet `Get-AzPolicyAlias` verwendet, um nach Ressourcenaliasen zu suchen. Wir filtern anhand der Details, die wir zuvor zur Azure-Ressource erhalten haben, nach dem Namespace **Microsoft.Storage**.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Wie in der Azure-Befehlszeilenschnittstelle auch, wird in den Ergebnissen ein Alias angezeigt, der von den Speicherkonten unterstützt wird und den Namen **supportsHttpsTrafficOnly** hat.

## <a name="determine-the-effect-to-use"></a>Ermitteln der zu verwendenden Auswirkung

Die Entscheidung, was mit Ihren nicht konformen Ressourcen passieren soll, ist fast so wichtig wie die Entscheidung, was überhaupt evaluiert werden soll. Jede mögliche Reaktion auf eine nicht konforme Ressource wird als [Auswirkung](../concepts/effects.md) bezeichnet. Mit der Auswirkung wird gesteuert, ob die nicht konforme Ressource protokolliert oder blockiert wird, über angefügte Daten verfügt oder mit einer zugeordneten Bereitstellung versehen ist, um die Ressource wieder in einen konformen Zustand zu versetzen.

Für unser Beispiel ist „Deny“ die passende Auswirkung, weil wir nicht möchten, dass in unserer Azure-Umgebung nicht konforme Ressourcen erstellt werden. Die Überwachung ist eine gute erste Wahl für eine Richtlinienauswirkung, um vor dem Festlegen einer Richtlinie auf „Deny“ zu ermitteln, welche Auswirkung damit verbunden ist. Eine Möglichkeit, die Änderung der Auswirkung pro Zuweisung zu vereinfachen, ist die Parametrisierung der Auswirkung. Genauere Informationen zur Vorgehensweise finden Sie unter [Parameter](#parameters).

## <a name="compose-the-definition"></a>Verfassen der Definition

Wir verfügen jetzt über die Eigenschaftendetails und den Alias für die geplante Verwaltung. Als Nächstes verfassen wir die eigentliche Richtlinienregel. Falls Sie mit der Richtliniensprache noch nicht vertraut sind, helfen Ihnen die Informationen zur Strukturierung der Richtliniendefinition weiter, die unter [Struktur von Richtliniendefinitionen](../concepts/definition-structure.md) angegeben sind. Hier ist eine leere Vorlage zu einer Richtliniendefinition angegeben:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadaten

Die ersten drei Komponenten sind Richtlinienmetadaten. Diese Komponenten sind einfach anzugebende Werte, da wir wissen, wofür wir die Regel erstellen. Beim [Modus](../concepts/definition-structure.md#mode) geht es hauptsächlich um Tags und den Ressourcenspeicherort. Da wir die Evaluierung nicht auf Ressourcen beschränken müssen, die Tags unterstützen, verwenden wir für **mode** den Wert _all_.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parameter

Wir haben zwar keinen Parameter für die Änderung der Evaluierung verwendet, aber wir möchten einen Parameter nutzen, um das Ändern der **Auswirkung** für die Problembehandlung zuzulassen. Wir definieren den Parameter **effectType** und beschränken ihn nur auf **Deny** und **Disabled**. Diese beiden Optionen entsprechen unseren geschäftlichen Anforderungen. Der fertige Parameterblock sieht wie im folgenden Beispiel aus:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Richtlinienregel

Das Verfassen der [Richtlinienregel](../concepts/definition-structure.md#policy-rule) ist der letzte Schritt bei der Erstellung der benutzerdefinierten Richtliniendefinition. Wir haben zwei Anweisungen für den Test identifiziert:

- Der Typ (**type**) des Speicherkontos ist **Microsoft.Storage/storageAccounts**.
- **supportsHttpsTrafficOnly** für das Speicherkonto ist nicht **true**.

Da diese beiden Anweisungen wahr sein müssen, verwenden wir **allOf** als [logischen Operator](../concepts/definition-structure.md#logical-operators). Wir übergeben den Parameter **effectType** an die Auswirkung, anstatt eine statische Deklaration zu verwenden. Die fertige Regel sieht wie in diesem Beispiel aus:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Fertige Definition

Nachdem alle drei Teile der Richtlinie festgelegt wurden, sieht die fertige Definition wie folgt aus:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Die fertige Definition kann genutzt werden, um eine neue Richtlinie zu erstellen. Vom Portal und den einzelnen SDKs (Azure CLI, Azure PowerShell und REST-API) wird die Definition auf unterschiedliche Weise akzeptiert. Sehen Sie sich also jeweils die Befehle genau an, um die richtige Nutzung sicherzustellen. Führen Sie anschließend die Zuweisung zu den passenden Ressourcen durch, indem Sie die parametrisierte Auswirkung verwenden, um die Sicherheit Ihrer Speicherkonten zu verwalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen dieses Tutorials nicht mehr benötigen, führen Sie die folgenden Schritte aus, um die erstellten Zuweisungen oder Definitionen zu löschen:

1. Klicken Sie links auf der Seite „Azure Policy“ unter **Erstellung** auf **Definitionen** (oder auf **Zuweisungen**, wenn Sie eine Zuweisung löschen möchten).

1. Suchen Sie nach der neuen Initiativ- oder Richtliniendefinition (bzw. der Zuweisung), die Sie entfernen möchten.

1. Klicken Sie mit der rechten Maustaste auf die Zeile, oder wählen Sie die Auslassungspunkte am Ende der Definition (oder Zuweisung), und wählen Sie anschließend **Definition löschen** (bzw. **Zuweisung löschen**) aus.

## <a name="review"></a>Überprüfung

In diesem Tutorial haben Sie folgende Aufgaben durchgeführt:

> [!div class="checklist"]
> - Ermitteln Ihrer geschäftlichen Anforderungen
> - Zuordnen der einzelnen Anforderungen zu einer Azure-Ressourceneigenschaft
> - Zuordnen der Eigenschaft zu einem Alias
> - Ermitteln der zu verwendenden Auswirkung
> - Verfassen der Richtliniendefinition

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes verwenden Sie Ihre benutzerdefinierte Richtliniendefinition, um eine Richtlinie zu erstellen und zuzuweisen:

> [!div class="nextstepaction"]
> [Erstellen und Zuweisen einer Richtliniendefinition](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
