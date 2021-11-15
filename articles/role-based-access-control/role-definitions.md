---
title: 'Grundlegendes zu Azure-Rollendefinitionen: Azure RBAC'
description: Dieser Artikel enthält Informationen zu Azure-Rollendefinitionen in der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für die präzise Zugriffsverwaltung von Azure-Ressourcen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: e60c9f2e7cf4c2f2ed19612ff8e01260a5f8fcb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055102"
---
# <a name="understand-azure-role-definitions"></a>Grundlegendes zu Azure-Rollendefinitionen

Wenn Sie die Funktionsweise eine Azure-Rolle nachvollziehen oder eine eigene [benutzerdefinierte Azure-Rolle](custom-roles.md) erstellen möchten, ist es hilfreich zu verstehen, wie Rollen definiert werden. Dieser Artikel geht ausführlich auf Rollendefinitionen ein und enthält einige Beispiele.

## <a name="role-definition"></a>Rollendefinition

Eine *Rollendefinition* ist eine Sammlung von Berechtigungen. Gelegentlich wird sie auch einfach als *Rolle* bezeichnet. Eine Rollendefinition listet die ausführbaren Aktionen wie etwa Lesen, Schreiben und Löschen auf. Unter Umständen werden außerdem die Aktionen aufgeführt, die nicht zugelassen sind oder im Zusammenhang mit den zugrunde liegenden Daten stehen.

Nachstehend finden Sie ein Beispiel für die in einer Rollendefinition enthaltenen Eigenschaften, wenn diese mit Azure PowerShell angezeigt wird:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Nachstehend finden Sie ein Beispiel für die in einer Rollendefinition enthaltenen Eigenschaften, wenn diese im Azure-Portal, in der Azure CLI oder in der REST-API angezeigt wird:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

In der folgenden Tabelle wird erläutert, was die einzelnen Eigenschaften bedeuten.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| `Name`</br>`roleName` | Der Anzeigename der Rolle. |
| `Id`</br>`name` | Die eindeutige ID der Rolle. Integrierte Rollen verfügen cloudübergreifend über die gleiche Rollen-ID. |
| `IsCustom`</br>`roleType` | Gibt an, ob dies eine benutzerdefinierte Rolle ist. Legen Sie die Eigenschaft für benutzerdefinierte Rollen auf `true` oder `CustomRole` fest. Legen Sie die Eigenschaft für integrierte Rollen auf `false` oder `BuiltInRole` fest. |
| `Description`</br>`description` | Die Beschreibung der Rolle. |
| `Actions`</br>`actions` | Ein Array von Zeichenfolgen, das die Aktionen auf Steuerungsebene angibt, deren Ausführung die Rolle zulässt |
| `NotActions`</br>`notActions` | Ein Array von Zeichenfolgen, das die Aktionen auf Steuerungsebene angibt, die von den zulässigen `Actions` ausgeschlossen sind |
| `DataActions`</br>`dataActions` | Ein Array von Zeichenfolgen, das die Aktionen auf Datenebene angibt, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt |
| `NotDataActions`</br>`notDataActions` | Ein Array von Zeichenfolgen, das die Aktionen auf Datenebene angibt, die von den zulässigen `DataActions` ausgeschlossen sind |
| `AssignableScopes`</br>`assignableScopes` | Ein Array aus Zeichenfolgen zum Angeben der Bereiche, in denen die Rolle zur Zuweisung verfügbar ist. |

### <a name="actions-format"></a>Aktionsformat

Aktionen werden mit Zeichenfolgen im folgenden Format angegeben:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Der Teil `{action}` einer Aktionszeichenfolge gibt den Typ der Aktionen an, die Sie für einen Ressourcentyp ausführen können. So weist `{action}` beispielsweise folgende Teilzeichenfolgen auf:

| Aktionsteilzeichenfolge    | BESCHREIBUNG         |
| ------------------- | ------------------- |
| `*` | Das Platzhalterzeichen gewährt Zugriff auf alle Aktionen, die der Zeichenfolge entsprechen. |
| `read` | Ermöglicht Leseaktionen (GET) |
| `write` | Ermöglicht Schreibaktionen (PUT oder PATCH) |
| `action` | Ermöglicht benutzerdefinierte Aktionen wie das Neustarten von VMs (POST) |
| `delete` | Ermöglicht Löschaktionen (DELETE) |

### <a name="role-definition-example"></a>Beispiel für eine Rollendefinition

Im folgenden Codeausschnitt finden Sie die Definition für die Rolle [Mitwirkender](built-in-roles.md#contributor), so wie sie in Azure PowerShell und in der Azure CLI angezeigt wird. Die Platzhalteraktionen (`*`) unter `Actions` geben an, dass der Prinzipal, der dieser Rolle zugewiesen ist, alle Aktionen ausführen und somit alles verwalten kann. Dies schließt auch Aktionen ein, die später definiert werden, wenn Azure neue Ressourcentypen hinzufügt. Die Aktionen unter `NotActions` werden von den `Actions` abgezogen. Im Fall der Rolle [Mitwirkender](built-in-roles.md#contributor) sorgt `NotActions` dafür, dass die Rolle weder den Zugriff auf Ressourcen noch die Zuweisungen für Azure Blueprints verwalten kann.

Rolle „Mitwirkender“, so wie sie in Azure PowerShell angezeigt wird:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rolle „Mitwirkender“, so wie sie in der Azure CLI angezeigt wird:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>Aktionen auf Steuerungs- und Datenebene

Die rollenbasierte Zugriffssteuerung für Aktionen auf Steuerungsebene wird in den Eigenschaften `Actions` und `NotActions` einer Rollendefinition angegeben. Im Folgenden finden Sie einige Beispiele für Aktionen auf Steuerungsebene in Azure:

- Verwalten des Zugriffs auf ein Speicherkonto
- Erstellen, Aktualisieren oder Löschen eines Blobcontainers
- Löschen einer Ressourcengruppe und aller dazugehörigen Ressourcen

Der Zugriff auf Steuerungsebene wird nicht von Ihren Daten geerbt, wenn die Methode für die Containerauthentifizierung auf „Azure AD-Benutzerkonto“ und nicht auf „Zugriffsschlüssel“ festgelegt ist. Diese Trennung verhindert, dass Rollen mit Platzhaltern (`*`) uneingeschränkten Zugriff auf Ihre Daten erhalten. Wenn einem Benutzer also beispielsweise die Rolle [Leser](built-in-roles.md#reader) für ein Abonnement zugewiesen ist, kann er das Speicherkonto anzeigen, aber standardmäßig nicht die zugrunde liegenden Daten.

Vorher wurde die rollenbasierte Zugriffssteuerung nicht bei Datenaktionen verwendet. Die Autorisierung bei Datenaktionen variiert je nach Ressourcenanbieter. Das gleiche Autorisierungsmodell mit rollenbasierter Zugriffssteuerung, das für Aktionen auf Steuerungsebene gilt, wurde auf die Aktionen auf Datenebene erweitert.

Zur Unterstützung von Aktionen auf Datenebene wurden der Rollendefinition neue Dateneigenschaften hinzugefügt. Aktionen auf Datenebene werden in den Eigenschaften `DataActions` und `NotDataActions` angegeben. Durch Hinzufügen dieser Dateneigenschaften wird die Trennung zwischen Steuerungs- und Datenebene beibehalten. Hierdurch wird verhindert, dass aktuelle Rollenzuweisungen mit Platzhaltern (`*`) wider Erwarten über Zugriff auf Daten verfügen. Im Folgenden werden einige Aktionen auf Datenebene aufgeführt, die in `DataActions` und `NotDataActions` angegeben werden können:

- Lesen einer Liste von Blobs in einem Container
- Schreiben eines Speicherblobs in einem Container
- Löschen einer Nachricht in einer Warteschlange

Dies ist die Rollendefinition [Storage-Blobdatenleser](built-in-roles.md#storage-blob-data-reader), die Aktionen in der `Actions`-Eigenschaft und der `DataActions`-Eigenschaft umfasst. In dieser Rolle können Sie den Blobcontainer sowie die zugrunde liegenden Blobdaten lesen.

Rolle „Storage-Blobdatenleser“, so wie sie in Azure PowerShell angezeigt wird:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rolle „Storage-Blobdatenleser“, so wie sie in der Azure CLI angezeigt wird:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Den Eigenschaften `DataActions` und `NotDataActions` können nur Aktionen auf Datenebene hinzugefügt werden. Durch Festlegen der `isDataAction`-Eigenschaft auf `true` identifizieren Ressourcenanbieter, bei welchen Aktionen es sich um Datenaktionen handelt. Wie Sie eine Liste der Aktionen anzeigen, in denen `isDataAction` auf `true` festgelegt ist, erfahren Sie unter [Vorgänge für Ressourcenanbieter](resource-provider-operations.md). Bei Rollen ohne Datenaktionen sind die Eigenschaften `DataActions` und `NotDataActions` in der Rollendefinition nicht erforderlich.

Die Autorisierung für alle API-Aufrufe auf Steuerungsebene wird von Azure Resource Manager verarbeitet. Die Autorisierung für API-Aufrufe auf Datenebene wird von einem Ressourcenanbieter oder von Azure Resource Manager verarbeitet.

### <a name="data-actions-example"></a>Beispiel für Datenaktionen

Sehen Sie sich ein spezielles Beispiel an, um die Funktionsweise von Aktionen auf Steuerungs- und Datenebene besser zu verstehen. Alice wurde die Rolle [Besitzer](built-in-roles.md#owner) im Abonnementbereich zugewiesen. Bob wurde die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) in einem Speicherkontobereich zugewiesen. Die folgende Abbildung veranschaulicht dieses Beispiel.

![Erweiterte rollenbasierte Zugriffssteuerung zur Unterstützung von Aktionen auf Steuerungs- und Datenebene](./media/role-definitions/rbac-data-plane.png)

Die Rolle [Besitzer](built-in-roles.md#owner) für Alice und die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) für Bob verfügen über die folgenden Aktionen:

Besitzer

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Mitwirkender an Storage-Blobdaten

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Da Alice über eine Platzhalteraktion (`*`) im Geltungsbereich eines Abonnements verfügt, werden die Berechtigungen nach unten vererbt, um ihr die Durchführung aller Aktionen auf Steuerungsebene zu ermöglichen. Alice kann Container lesen, schreiben und löschen. Alice kann jedoch keine Aktionen auf Datenebene durchführen, ohne zusätzliche Schritte zu unternehmen. Beispielsweise kann Alice standardmäßig die Blobs innerhalb eines Containers nicht lesen. Um die Blobs lesen zu können, müsste Alice die Speicherzugriffsschlüssel abrufen und damit auf die Blobs zugreifen.

Die Berechtigungen von Bob sind ausschließlich auf `Actions` und `DataActions` beschränkt, die in der Rolle [Mitwirkenden an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) angegeben sind. Basierend auf der Rolle kann Bob Aktionen auf Steuerungs- und Datenebene durchführen. Beispielsweise kann Bob Container im angegebenen Speicherkonto lesen, schreiben und löschen und zudem die Blobs lesen, schreiben und löschen.

Weitere Informationen zur Sicherheit auf Steuerungs- und Datenebene für den Speicher finden Sie im [Azure Storage-Sicherheitsleitfaden](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>Welche Tools unterstützen die Verwendung von Azure-Rollen für Datenaktionen?

Um Datenaktionen anzuzeigen und zu verwenden, müssen Sie über die richtigen Tool- oder SDK-Versionen verfügen:

| Tool  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 oder höher |
| [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) | 2.0.30 oder höher |
| [Azure für .NET](/dotnet/azure/) | 2.8.0-preview oder höher |
| [Azure SDK für Go](/azure/go/azure-sdk-go-install) | 15.0.0 oder höher |
| [Azure für Java](/java/azure/) | 1.9.0 oder höher |
| [Azure für Python](/azure/python/) | 0.40.0 oder höher |
| [Azure SDK für Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 oder höher |

Um die Datenaktionen in der REST-API anzuzeigen und zu verwenden, müssen Sie den Parameter **api-version** auf die folgende oder eine höhere Version festlegen:

- 2018-07-01

## <a name="actions"></a>Aktionen

Die Berechtigung `Actions` gibt die Aktionen auf Steuerungsebene an, deren Ausführung die Rolle zulässt. Es handelt sich um eine Sammlung von Zeichenfolgen, mit denen sicherungsfähige Aktionen von Azure-Ressourcenanbietern identifiziert werden. Im Folgenden finden Sie einige Beispiele für Aktionen auf Steuerungsebene, die in `Actions` verwendet werden können.

> [!div class="mx-tableFixed"]
> | Aktionszeichenfolge    | BESCHREIBUNG         |
> | ------------------- | ------------------- |
> | `*/read` | Gewährt Zugriff auf Leseaktionen für alle Ressourcentypen aller Azure-Ressourcenanbieter|
> | `Microsoft.Compute/*` | Gewährt Zugriff auf alle Aktionen für alle Ressourcentypen im Microsoft.Compute-Ressourcenanbieter|
> | `Microsoft.Network/*/read` | Gewährt Zugriff auf Leseaktionen für alle Ressourcentypen im Microsoft.Network-Ressourcenanbieter|
> | `Microsoft.Compute/virtualMachines/*` | Gewährt Zugriff auf alle Aktionen für VMs und die zugehörigen untergeordneten Ressourcentypen|
> | `microsoft.web/sites/restart/Action` | Gewährt Zugriff zum Neustarten einer Web-App.|

## <a name="notactions"></a>NotActions

Die Berechtigung `NotActions` gibt die Aktionen auf Steuerungsebene an, die von den zulässigen Aktionen (`Actions`) abgezogen oder ausgeschlossen werden, die einen Platzhalter (`*`) enthalten. Verwenden Sie die Berechtigung `NotActions`, wenn sich die Aktionen, die Sie zulassen möchten, einfacher durch das Abziehen von `Actions`, die einen Platzhalter (`*`) enthalten, definieren lässt. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die Aktionen in `NotActions` von denen in `Actions` abgezogen.

`Actions - NotActions = Effective control plane permissions`

In der folgenden Tabelle werden zwei Beispiele für die effektiven Berechtigungen auf Steuerungsebene für eine [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)-Platzhalteraktion angezeigt:

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Effektive Berechtigungen auf Steuerungsebene |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *keine* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Wenn Benutzer*innen eine Rolle zugewiesen wird, die eine Aktion in `NotActions` ausschließt, und ihnen dann durch Zuweisen einer zweiten Rolle der Zugriff auf diese Aktion gewährt wird, können die Benutzer*innen die Aktion ausführen. `NotActions` ist keine Verweigerungsregel, sondern bietet lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Aktionen zu erstellen, wenn bestimmte Aktionen ausgeschlossen werden müssen.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Unterschiede zwischen NoActions und Ablehnungszuweisungen

`NotActions` und Ablehnungszuweisungen sind nicht identisch und dienen unterschiedlichen Zwecken. `NotActions` stellen eine bequeme Möglichkeit dar, um bestimmte Aktionen von einer Platzhalteraktion (`*`) auszuschließen.

Ablehnungszuweisungen blockieren Aktionen für bestimmte Benutzer, selbst wenn diesen durch eine Rollenzuweisung Zugriff erteilt wurde. Weitere Informationen finden Sie unter [Verstehen von Ablehnungszuweisungen für Azure-Ressourcen](deny-assignments.md).

## <a name="dataactions"></a>DataActions

Die Berechtigung `DataActions` gibt die Aktionen auf Datenebene an, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt. Wenn ein Benutzer z.B. über Lesezugriff auf Blobdaten auf ein Speicherkonto verfügt, kann er die Blobs in diesem Speicherkonto dann lesen. Im Anschluss finden Sie einige Beispiele für Datenaktionen, die in `DataActions` verwendet werden können.

> [!div class="mx-tableFixed"]
> | Datenaktionszeichenfolge    | BESCHREIBUNG         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Gibt eine Nachricht zurück. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Gibt eine Nachricht oder das Ergebnis beim Schreiben oder Löschen einer Nachricht zurück. |

## <a name="notdataactions"></a>NotDataActions

Die Berechtigung `NotDataActions` gibt die Aktionen auf Datenebene an, die von den zulässigen Aktionen (`DataActions`) abgezogen oder ausgeschlossen werden, die einen Platzhalter (`*`) enthalten. Verwenden Sie die Berechtigung `NotDataActions`, wenn sich die Aktionen, die Sie zulassen möchten, einfacher durch das Abziehen von `DataActions`, die einen Platzhalter (`*`) enthalten, definieren lässt. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die Aktionen in `NotDataActions` von denen in `DataActions` abgezogen. Jedem Ressourcenanbieter stehen entsprechende APIs zur Verfügung, um Datenaktionen durchzuführen.

`DataActions - NotDataActions = Effective data plane permissions`

In der folgenden Tabelle werden zwei Beispiele für die effektiven Berechtigungen auf der Datenebene für eine [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)-Platzhalteraktion gezeigt:

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Effektive Berechtigungen auf Datenebene |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *keine* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Wenn Benutzer*innen eine Rolle zugewiesen wird, die eine Datenaktion in `NotDataActions` ausschließt, und ihnen dann durch Zuweisen einer zweiten Rolle der Zugriff auf diese Datenaktion gewährt wird, können die Benutzer*innen die Datenaktion ausführen. `NotDataActions` ist keine Verweigerungsregel, sondern bietet lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Datenaktionen zu erstellen, wenn bestimmte Datenaktionen ausgeschlossen werden müssen.
>

## <a name="assignablescopes"></a>AssignableScopes

Die `AssignableScopes`-Eigenschaft gibt die Bereiche (Verwaltungsgruppen, Abonnements oder Ressourcengruppen) an, in denen diese Rollendefinition zugewiesen werden kann. Sie können die Rolle nur in den für Sie erforderlichen Verwaltungsgruppen, Abonnements oder Ressourcengruppen für die Zuweisung zur Verfügung stellen. Sie müssen mindestens eine Verwaltungsgruppe, ein Abonnement oder eine Ressourcengruppe verwenden.

Bei integrierten Rollen ist `AssignableScopes` auf den Stammbereich (`"/"`) festgelegt. Der Stammbereich gibt an, dass die Rolle für die Zuweisung in allen Bereichen verfügbar ist. Beispiele für gültige zuweisbare Bereiche:

> [!div class="mx-tableFixed"]
> | Rolle ist für die Zuweisung verfügbar | Beispiel |
> |----------|---------|
> | Ein Abonnement | `"/subscriptions/{subscriptionId1}"` |
> | Zwei Abonnements | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Netzwerkressourcengruppe | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Eine Verwaltungsgruppe | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Verwaltungsgruppe und ein Abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alle Bereiche (gilt nur für integrierte Rollen) | `"/"` |

Informationen zu `AssignableScopes` für benutzerdefinierte Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](custom-roles.md).

## <a name="next-steps"></a>Nächste Schritte

* [Integrierte Azure-Rollen](built-in-roles.md)
* [Benutzerdefinierte Azure-Rollen](custom-roles.md)
* [Vorgänge für Azure-Ressourcenanbieter](resource-provider-operations.md)
