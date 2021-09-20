---
title: Benutzerdefinierte Azure-Rollen – Azure RBAC
description: Informationen zum Erstellen benutzerdefinierter Azure-Rollen mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) für die präzise Verwaltung des Zugriffs auf Azure-Ressourcen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2021
ms.author: rolyon
ms.openlocfilehash: 9553e53cda41a4fe4d926923bdd71d7d7c5ebb15
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308392"
---
# <a name="azure-custom-roles"></a>Benutzerdefinierte Azure-Rollen

> [!IMPORTANT]
> Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn die [integrierten Azure-Rollen](built-in-roles.md) die Anforderungen Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. Genauso wie integrierte Rollen können auch benutzerdefinierte Rollen Benutzern, Gruppen und Dienstprinzipalen im Verwaltungsgruppenbereich (nur als Vorschau verfügbar) sowie im Abonnement- und Ressourcengruppenbereich zugewiesen werden.

Benutzerdefinierte Rollen können von Abonnements, die demselben Azure AD-Verzeichnis vertrauen, gemeinsam genutzt werden. Es gilt ein Limit von **5.000** benutzerdefinierte Rollen pro Verzeichnis. (Für Azure Deutschland und Azure China 21ViaNet beträgt das Limit 2.000 benutzerdefinierte Rollen.) Benutzerdefinierte Rollen können über das Azure-Portal, mit Azure PowerShell, über die Azure CLI oder mithilfe der REST-API erstellt werden.

## <a name="steps-to-create-a-custom-role"></a>Schritte zum Erstellen einer benutzerdefinierten Rolle

Dies sind die grundlegenden Schritte zum Erstellen einer benutzerdefinierten Rolle.

1. Bestimmen Sie die Berechtigungen, die Sie benötigen.

    Wenn Sie eine benutzerdefinierte Rolle erstellen, müssen Sie die Vorgänge kennen, die zum Definieren der Berechtigungen verfügbar sind. In der Regel beginnen Sie mit einer vorhandenen integrierten Rolle und ändern sie Ihren Anforderungen entsprechend. Sie fügen die Vorgänge der `Actions`- oder `NotActions`-Eigenschaft der [Rollendefinition](role-definitions.md) hinzu. Wenn Sie über Datenvorgänge verfügen, fügen Sie diese der `DataActions`- oder `NotDataActions`-Eigenschaft hinzu.

    Weitere Informationen finden Sie im nächsten Abschnitt [Ermitteln der erforderlichen Berechtigungen](#how-to-determine-the-permissions-you-need).

1. Entscheiden Sie, wie Sie die benutzerdefinierte Rolle erstellen möchten.

    Benutzerdefinierte Rollen können im [Azure-Portal](custom-roles-portal.md), mit [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md) oder der [REST-API](custom-roles-rest.md) erstellt werden.

1. Erstellen Sie die benutzerdefinierte Rolle.

    Das geht am einfachsten über das Azure-Portal. Die Schritte zum Erstellen einer benutzerdefinierten Rolle über das Azure-Portal finden Sie unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen über das Azure-Portal](custom-roles-portal.md).

1. Testen Sie die benutzerdefinierte Rolle.

    Sobald Sie über Ihre benutzerdefinierte Rolle verfügen, müssen Sie sie testen, um sicherzustellen, dass sie wie erwartet funktioniert. Wenn Sie später Anpassungen vornehmen müssen, können Sie die benutzerdefinierte Rolle aktualisieren.

## <a name="how-to-determine-the-permissions-you-need"></a>Ermitteln der erforderlichen Berechtigungen

Azure verfügt über Tausende von Berechtigungen, die Sie potenziell zu Ihrer benutzerdefinierten Rolle hinzufügen können. Nachfolgend werden einige Methoden aufgeführt, mit denen Sie die Berechtigungen ermitteln können, die Sie Ihrer benutzerdefinierten Rolle hinzufügen sollten:

- Sehen Sie sich die vorhandenen [integrierten Rollen](built-in-roles.md) an.

    Möglicherweise möchten Sie eine vorhandene Rolle ändern oder Berechtigungen kombinieren, die in mehreren Rollen verwendet werden.

- Listen Sie die Azure-Dienste auf, auf die Sie Zugriff gewähren möchten.

- Bestimmen Sie die [Ressourcenanbieter, die den Azure-Diensten zugeordnet sind](../azure-resource-manager/management/azure-services-resource-providers.md).

    Azure-Dienste stellen ihre Funktionalität und Berechtigungen über [Ressourcenanbieter](../azure-resource-manager/management/overview.md) zur Verfügung. Beispielsweise stellt der Microsoft.Compute-Ressourcenanbieter VM-Ressourcen bereit, und der Ressourcenanbieter Microsoft.Billing stellt Abonnement- und Abrechnungsressourcen zur Verfügung. Wenn Sie die Ressourcenanbieter kennen, können Sie die erforderlichen Berechtigungen für Ihre benutzerdefinierte Rolle eingrenzen und ermitteln.

    Wenn Sie mithilfe des Azure-Portals eine benutzerdefinierte Rolle erstellen, können Sie auch die Ressourcenanbieter ermitteln, indem Sie nach Schlüsselwörtern suchen. Diese Suchfunktion wird unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe des Azure-Portals](custom-roles-portal.md#step-4-permissions) beschrieben.

    ![Der Bereich „Berechtigungen hinzufügen“ mit Ressourcenanbietern](./media/shared/add-permissions-provider.png)

- Suchen Sie nach den [verfügbaren Berechtigungen](resource-provider-operations.md), um die gewünschten Berechtigungen zu ermitteln.

    Wenn Sie über das Azure-Portal eine benutzerdefinierte Rolle erstellen, können Sie anhand von Schlüsselwörtern nach Berechtigungen suchen. Sie können z.B. nach Berechtigungen im Zusammenhang mit *VMs* oder mit der *Abrechnung* suchen. Sie können auch alle Berechtigungen als CSV-Datei herunterladen und dann diese Datei durchsuchen. Diese Suchfunktion wird unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe des Azure-Portals](custom-roles-portal.md#step-4-permissions) beschrieben.

    ![Die Liste „Berechtigungen hinzufügen“](./media/shared/add-permissions-list.png)

## <a name="custom-role-example"></a>Beispiel einer benutzerdefinierten Rolle

Das folgende Beispiel zeigt, wie eine benutzerdefinierte Rolle mit Azure PowerShell im JSON-Format angezeigt aussieht. Diese benutzerdefinierte Rolle kann zum Überwachen und Neustarten virtueller Computer verwendet werden.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Im Folgenden wird die gleiche benutzerdefinierte Rolle bei Verwendung der Azure CLI veranschaulicht.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="custom-role-properties"></a>Eigenschaften der benutzerdefinierten Rolle

In der folgenden Tabelle wird erläutert, was die Eigenschaften der benutzerdefinierten Rollen bedeuten.

| Eigenschaft | Erforderlich | type | BESCHREIBUNG |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ja | String | Der Anzeigename der benutzerdefinierten Rolle. Obwohl es sich bei einer Rollendefinition um eine Ressource auf Verwaltungsgruppen- oder Abonnementebene handelt, kann eine solche Definition in mehreren Abonnements verwendet werden, die dasselbe Azure AD-Verzeichnis gemeinsam nutzen. Der Anzeigename muss im Bereich des Azure AD-Verzeichnisses eindeutig sein. Er kann Buchstaben, Ziffern, Leerzeichen und Sonderzeichen enthalten. Die maximale Anzahl von Zeichen ist 128. |
| `Id`</br>`name` | Ja | String | Die eindeutige ID der benutzerdefinierten Rolle. Für Azure PowerShell und Azure CLI wird diese ID automatisch generiert, wenn Sie eine neue Rolle erstellen. |
| `IsCustom`</br>`roleType` | Ja | String | Gibt an, ob dies eine benutzerdefinierte Rolle ist. Legen Sie die Eigenschaft für benutzerdefinierte Rollen auf `true` oder `CustomRole` fest. Legen Sie die Eigenschaft für integrierte Rollen auf `false` oder `BuiltInRole` fest. |
| `Description`</br>`description` | Ja | String | Die Beschreibung der benutzerdefinierten Rolle. Er kann Buchstaben, Ziffern, Leerzeichen und Sonderzeichen enthalten. Die maximale Anzahl von Zeichen ist 1.024. |
| `Actions`</br>`actions` | Ja | String[] | Ein Array von Zeichenfolgen, das die Verwaltungsvorgänge angibt, deren Ausführung die Rolle zulässt. Weitere Informationen finden Sie unter [Aktionen](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Nein | String[] | Ein Array von Zeichenfolgen, das die Verwaltungsvorgänge angibt, die von den zulässigen `Actions` ausgeschlossen sind. Weitere Informationen finden Sie unter [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Nein | String[] | Ein Array von Zeichenfolgen, das die Datenvorgänge angibt, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt. Wenn Sie eine benutzerdefinierte Rolle mit `DataActions`erstellen, kann diese Rolle im Verwaltungsgruppenbereich nicht zugewiesen werden. Weitere Informationen finden Sie unter [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Nein | String[] | Ein Array von Zeichenfolgen, das die Datenvorgänge angibt, die von den zulässigen `DataActions` ausgeschlossen sind. Weitere Informationen finden Sie unter [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Ja | String[] | Ein Array von Zeichenfolgen, das die Bereiche angibt, in denen die benutzerdefinierte Rolle zur Zuweisung verfügbar ist. Sie können in den `AssignableScopes` einer benutzerdefinierten Rolle nur eine einzige Verwaltungsgruppe definieren. Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase. Weitere Informationen finden Sie unter [AssignableScopes](role-definitions.md#assignablescopes). |

Bei Berechtigungszeichenfolgen wird die Groß-/Kleinschreibung nicht beachtet. Beim Erstellen Ihrer benutzerdefinierten Rollen sollten Sie sich an die vorgegebene Groß-/Kleinschreibung halten, die für Berechtigungen unter [Vorgänge für Azure-Ressourcenanbieter](resource-provider-operations.md) beschrieben ist.

## <a name="wildcard-permissions"></a>Platzhalterberechtigungen

`Actions`, `NotActions`, `DataActions` und `NotDataActions` unterstützen Platzhalter (`*`) beim Definieren von Berechtigungen. Ein Platzhalter (`*`) erweitert eine Berechtigung auf alles, was der von Ihnen angegebenen Aktionszeichenfolge entspricht. Angenommen, Sie möchten alle Berechtigungen hinzufügen, die sich auf Azure Cost Management und Exporte beziehen. Sie können alle nachfolgend aufgeführten Aktionszeichenfolgen hinzufügen:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Anstatt all diese Zeichenfolgen hinzuzufügen, können Sie jedoch auch einfach eine Platzhalterzeichenfolge hinzufügen. Die folgende Platzhalterzeichenfolge entspricht beispielsweise den oben genannten fünf Zeichenfolgen. Dies schließt auch alle zukünftigen Exportberechtigungen ein, die möglicherweise hinzugefügt werden.

```
Microsoft.CostManagement/exports/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Rollen zum Erstellen, Löschen, Aktualisieren oder Anzeigen einer benutzerdefinierten Rolle

Wie integrierte Rollen legt die `AssignableScopes`-Eigenschaft die Bereiche fest, in denen die Rolle zur Zuweisung verfügbar ist. Die `AssignableScopes`-Eigenschaft für eine benutzerdefinierte Rolle steuert auch, wer zum Erstellen, Löschen, Aktualisieren oder Anzeigen der benutzerdefinierten Rolle berechtigt ist.

| Aufgabe | Vorgang | BESCHREIBUNG |
| --- | --- | --- |
| Erstellen/Löschen einer benutzerdefinierten Rolle | `Microsoft.Authorization/ roleDefinitions/write` | Benutzer, die für alle `AssignableScopes` der benutzerdefinierten Rolle zu diesem Vorgang berechtigt sind, können benutzerdefinierte Rollen für die Verwendung in diesen Bereichen erstellen (oder löschen). Hierzu gehören beispielsweise [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministratoren](built-in-roles.md#user-access-administrator) von Verwaltungsgruppen, Abonnements und Ressourcengruppen. |
| Aktualisieren einer benutzerdefinierten Rolle | `Microsoft.Authorization/ roleDefinitions/write` | Benutzer, die für alle `AssignableScopes` der benutzerdefinierten Rolle zu diesem Vorgang berechtigt sind, können benutzerdefinierte Rollen in diesen Bereichen aktualisieren. Hierzu gehören beispielsweise [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministratoren](built-in-roles.md#user-access-administrator) von Verwaltungsgruppen, Abonnements und Ressourcengruppen. |
| Anzeigen einer benutzerdefinierten Rolle | `Microsoft.Authorization/ roleDefinitions/read` | Benutzer, die in einem Bereich zu diesem Vorgang berechtigt sind, können die benutzerdefinierten Rollen anzeigen, die für die Zuweisung in diesem Bereich verfügbar sind. Benutzerdefinierte Rollen können bei allen integrierten Rollen für die Zuweisung verfügbar sein. |

## <a name="custom-role-limits"></a>Grenzwerte bei benutzerdefinierten Rollen

In der nachstehenden Liste werden die Grenzwerte bei benutzerdefinierten Rollen beschrieben.

- Jedes Verzeichnis kann bis zu **5.000** benutzerdefinierte Rollen enthalten.
- In Azure Deutschland und Azure China 21ViaNet kann es für jedes Verzeichnis bis zu 2.000 benutzerdefinierte Rollen geben.
- `AssignableScopes` kann nicht auf den Stammbereich (`"/"`) festgelegt werden.
- In `AssignableScopes` können Sie keine Platzhalter (`*`) verwenden. Diese Platzhaltereinschränkung soll sicherstellen, dass ein Benutzer keinen Zugriff auf einen Bereich durch Aktualisieren der Rollendefinition erhalten kann.
- Sie können in den `AssignableScopes` einer benutzerdefinierten Rolle nur eine einzige Verwaltungsgruppe definieren. Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.
- Sie können nur einen Platzhalter in einer Aktionszeichenfolge verwenden.
- Benutzerdefinierte Rollen mit `DataActions` können im Verwaltungsgruppenbereich nicht zugewiesen werden.
- Azure Resource Manager überprüft nicht, ob die Verwaltungsgruppe im zuweisbaren Bereich der Rollendefinition vorhanden ist.

Weitere Informationen zu benutzerdefinierten Rollen und Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Eingabe- und Ausgabeformate

Zum Erstellen einer benutzerdefinierten Rolle über die Befehlszeile verwenden Sie in der Regel JSON, um die Eigenschaften anzugeben, die Sie für die benutzerdefinierte Rolle verwenden möchten. Abhängig von den Tools, die Sie verwenden, variieren die Eingabe- und Ausgabeformate. In diesem Abschnitt sind die Eingabe- und Ausgabeformate nach Tool aufgeführt.

### <a name="azure-powershell"></a>Azure PowerShell

Folgende Eingabe ist nötig, um eine benutzerdefinierte Rolle über Azure PowerShell zu erstellen:

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Geben Sie hingegen Folgendes ein, wenn Sie eine benutzerdefinierte Rolle über Azure PowerShell aktualisieren möchten: Beachten Sie, dass die `Id`-Eigenschaft hinzugefügt wurde. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Im Folgenden sehen Sie ein Beispiel für die Ausgabe, wenn Sie eine benutzerdefinierte Rolle mit Azure PowerShell und dem Befehl [ConvertTo-Json](/powershell/module/microsoft.powershell.utility/convertto-json) auflisten. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Folgende Eingabe ist nötig, um eine benutzerdefinierte Rolle über die Azure CLI zu erstellen oder zu aktualisieren: Dieses Format gilt auch, wenn Sie eine benutzerdefinierte Rolle mit Azure PowerShell erstellen.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Im Folgenden sehen Sie ein Beispiel für die Ausgabe, wenn Sie eine benutzerdefinierte Rolle mit der Azure CLI auflisten.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST-API

Folgende Eingabe ist nötig, um eine benutzerdefinierte Rolle über die REST-API zu erstellen oder zu aktualisieren: Dieses Format wird auch generiert, wenn Sie eine benutzerdefinierte Rolle mithilfe des Azure-Portal erstellen.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Im Folgenden sehen Sie ein Beispiel für die Ausgabe, wenn Sie eine benutzerdefinierte Rolle über die REST-API auflisten.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer benutzerdefinierten Azure-Rolle mithilfe von Azure PowerShell](tutorial-custom-role-powershell.md)
- [Tutorial: Erstellen einer benutzerdefinierten Rolle in Azure mithilfe der Azure-Befehlszeilenschnittstelle](tutorial-custom-role-cli.md)
- [Grundlegendes zu Azure-Rollendefinitionen](role-definitions.md)
- [Behandeln von Problemen bei Azure RBAC](troubleshooting.md)
