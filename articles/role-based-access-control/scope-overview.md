---
title: Grundlegendes zum Bereich von Azure RBAC
description: Erfahren Sie mehr über den Bereich der rollenbasierten Zugriffssteuerung (Role-Based Access Control) in Azure (Azure RBAC), und wie Sie den Bereich für eine Ressource bestimmen können.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/09/2021
ms.author: rolyon
ms.openlocfilehash: 3fc3d614ef26235325e0b9a9e8fee68d2bf919a5
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340416"
---
# <a name="understand-scope-for-azure-rbac"></a>Grundlegendes zum Bereich von Azure RBAC

Ein *Bereich* ist der Ressourcensatz, für den Zugriffsberechtigungen gelten. Wenn Sie eine Rolle zuweisen, müssen Sie den Bereich kennen, damit Sie den Zugriff eines Sicherheitsprinzipals auf das Notwendige beschränken können. Durch Einschränken des Bereichs begrenzen Sie die Ressourcen, die gefährdet sind, wenn der Sicherheitsprinzipal einmal kompromittiert wird.

## <a name="scope-levels"></a>Bereichsebenen

In Azure können Sie auf vier Ebenen einen Bereich angeben: [Verwaltungsgruppe](../governance/management-groups/overview.md), Abonnement, [Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups) und Ressource. Bereiche sind in einer Beziehung zwischen über- und untergeordneten Elementen strukturiert. Mit jeder Hierarchieebene wird der Bereich spezifischer. Sie können Rollen auf jeder dieser Bereichsebenen zuweisen. Die von Ihnen ausgewählte Ebene bestimmt, wie umfassend die Rolle angewendet wird. Niedrigere Ebenen erben die Rollenberechtigungen von höheren Ebenen. 

![Bereich für eine Rollenzuweisung](./media/scope-overview/rbac-scope-no-label.png)

Verwaltungsgruppen sind eine Ebene des Bereichs oberhalb von Abonnements, aber Verwaltungsgruppen unterstützen komplexere Hierarchien. Das folgende Diagramm zeigt ein Beispiel einer Hierarchie aus Verwaltungsgruppen und Abonnements, die Sie definieren können. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../governance/management-groups/overview.md).

![Verwaltungsgruppen- und Abonnementhierarchie](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Bereichsformat

Wenn Sie Rollen mithilfe der Befehlszeile zuweisen, müssen Sie den Bereich angeben. Für Befehlszeilentools ist der Bereich eine potenziell lange Zeichenfolge, die den genauen Bereich der Zuweisung identifiziert. Im Azure-Portal wird dieser Bereich in der Regel als *Ressourcen-ID* aufgeführt.

Der Bereich besteht aus einer Reihe von Bezeichnern, die durch den Schrägstrich (/) getrennt werden. Sie können sich diese Zeichenfolge als Ausdruck für die folgende Hierarchie vorstellen, wobei Text ohne Platzhalter (`{}`) feste Bezeichner sind:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` ist die ID des zu verwendenden Abonnements (eine GUID).
- `{resourcesGroupName}` ist der Name der enthaltenden Ressourcengruppe.
- `{providerName}` ist der Name des [Ressourcenanbieters](../azure-resource-manager/management/azure-services-resource-providers.md), der die Ressource verarbeitet, und `{resourceType}` und `{resourceSubType*}` identifizieren dann weitere Ebenen innerhalb dieses Ressourcenanbieters.
- `{resourceName}` der letzte Teil der Zeichenfolge, die eine bestimmte Ressource identifiziert.

Verwaltungsgruppen liegen eine Ebene oberhalb von Abonnements und haben den umfassendsten (am wenigsten spezifischen) Bereich. Rollenzuweisungen auf dieser Ebene gelten für Abonnements innerhalb der Verwaltungsgruppe. Der Bereich für eine Verwaltungsgruppe weist das folgende Format auf:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Bereichsbeispiele

> [!div class="mx-tableFixed"]
> | `Scope` | Beispiel |
> | --- | --- |
> | Verwaltungsgruppe | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscription | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Resource | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Ermitteln des Bereichs für eine Ressource

Es ist recht einfach, den Bereich für eine Verwaltungsgruppe, ein Abonnement oder eine Ressourcengruppe zu ermitteln. Sie müssen lediglich den Namen und die Abonnement-ID kennen. Die Bestimmung des Bereichs für eine Ressource ist jedoch etwas komplizierter. Hier finden Sie einige Möglichkeiten, den Bereich für eine Ressource zu ermitteln.

- Öffnen Sie im Azure-Portal die Ressource, und überprüfen Sie dann die Eigenschaften. Die Ressource sollte die **Ressourcen-ID** auflisten, in der Sie den Bereich ermitteln können. Hier sind z. B. die Ressourcen-IDs für ein Speicherkonto.

    ![Ressourcen-IDs für ein Speicherkonto im Azure-Portal](./media/scope-overview/scope-resource-id.png)

- Eine andere Möglichkeit ist, eine Rolle im Azure-Portal vorübergehend im Ressourcenbereich zuzuweisen und dann mit [Azure PowerShell](role-assignments-list-powershell.md) oder [Azure CLI](role-assignments-list-cli.md) die Rollenzuweisung aufzulisten. In der Ausgabe wird der Bereich als Eigenschaft aufgelistet.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="scope-and-arm-templates"></a>Bereich und ARM-Vorlagen

Eine Rollenzuweisung ist ein spezieller Typ in Azure Resource Manager, der als *Erweiterungsressource* bezeichnet wird. Eine Erweiterungsressource ist eine Ressource, die einer anderen Ressource Funktionen hinzufügt. Sie sind immer als Erweiterung (wie ein untergeordnetes Element) einer anderen Ressource vorhanden. Beispielsweise ist eine Rollenzuweisung im Abonnementbereich eine Erweiterungsressource des Abonnements. Der Name einer Rollenzuweisung ist immer der Name der Ressource, die Sie erweitern, plus `/Microsoft.Authorization/roleAssignments/{roleAssignmentId}`. Beim Zuweisen von Rollen mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) müssen Sie den Bereich in der Regel nicht angeben. Der Grund dafür ist, dass das Bereichsfeld immer die ID der Ressource ist, die Sie erweitern. Der Bereich kann anhand der ID der Rollenzuweisung selbst bestimmt werden. Die folgende Tabelle zeigt Beispiele für eine Rollenzuweisungs-ID und den entsprechenden Bereich:

> [!div class="mx-tableFixed"]
> | Rollenzuweisungs-ID | `Scope` |
> | --- | --- |
> | `/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}` |
> | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg` |

Weitere Informationen zum Bereich und zu ARM-Vorlagen finden Sie unter [Zuweisen von Azure-Rollen mit Azure Resource Manager-Vorlagen](role-assignments-template.md). Eine vollständige Liste der Erweiterungsressourcentypen finden Sie unter [Ressourcentypen, die Funktionen anderer Ressourcen erweitern](../azure-resource-manager/management/extension-resource-types.md).

## <a name="next-steps"></a>Nächste Schritte

- [Schritte zum Zuweisen einer Azure-Rolle](role-assignments-steps.md)
- [Ressourcenanbieter für Azure-Dienste](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Was sind Azure-Verwaltungsgruppen?](../governance/management-groups/overview.md)
