---
title: 'Zuweisen von Azure-Rollen mithilfe der REST-API: Azure RBAC'
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten über die REST-API mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) verwalten.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 0c59db98f3f38a7e715c0dce77c397f0e2562343
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356664"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Zuweisen von Azure-Rollen mithilfe der REST-API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In diesem Artikel wird die Zuweisung von Rollen mithilfe der REST-API beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Zuweisen einer Azure-Rolle

Um eine Rolle zuzuweisen, verwenden Sie die REST-API [Assignments - Create](/rest/api/authorization/roleassignments/create) (Rollenzuweisungen – Erstellen), und geben Sie den Sicherheitsprinzipal, die Rollendefinition und den Bereich an. Zum Aufrufen dieser API benötigen Sie Zugriff auf die Aktion `Microsoft.Authorization/roleAssignments/write`. Von den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über Zugriff auf diese Aktion.

1. Verwenden Sie die REST API unter [Rollendefinitionen – Auflisten](/rest/api/authorization/roledefinitions/list) oder lesen Sie [Integrierte Rollen in Azure](built-in-roles.md), um den Bezeichner für die zuzuweisende Rollendefinition zu ermitteln.

1. Verwenden Sie ein GUID-Tool, um einen eindeutigen Bezeichner zu generieren, der für den Bezeichner der Rollenzuweisung verwendet wird. Das Format des Bezeichners ist: `00000000-0000-0000-0000-000000000000`

1. Beginnen Sie mit folgender Anforderung und folgendem Text:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* durch den Bereich für die Rollenzuweisung.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

    Im vorherigen Beispiel ist „microsoft.web“ ein Ressourcenanbieter, der auf eine App Service-Instanz verweist. Analog dazu können Sie einen beliebigen anderen Ressourcenanbieter verwenden und den Bereich angeben. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md) und unter unterstützten [Vorgängen für Azure-Ressourcenanbieter](resource-provider-operations.md).  

1. Ersetzen Sie *{roleAssignmentId}* durch den GUID-Bezeichner der Rollenzuweisung.

1. Ersetzen Sie innerhalb des Anforderungstexts *{scope}* durch den Bereich für die Rollenzuweisung.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Ersetzen Sie *{roleDefinitionId}* durch den Bezeichner der Rollendefinition.

1. Ersetzen Sie *{principalId}* durch den Objektbezeichner des Benutzers, der Gruppe oder des Dienstprinzipals, der bzw. die der Rolle zugewiesen wird.

Mit der folgenden Anforderung und dem folgenden Text wird einem Benutzer im Abonnementbereich die Rolle [Benutzer mit Leseberechtigung für Sicherungsfunktionen](built-in-roles.md#backup-reader) zugewiesen:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Nachfolgend sehen Sie ein Beispiel für die Ausgabe:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>Neuer Dienstprinzipal

Wenn Sie einen neuen Dienstprinzipal erstellen und sofort versuchen, diesem eine Rolle zuzuweisen, kann die Rollenzuweisung in einigen Fällen fehlschlagen. Wenn Sie z. B. eine neue verwaltete Identität erstellen und dann versuchen, diesem Dienstprinzipal eine Rolle zuzuweisen, kann die Rollenzuweisung fehlschlagen. Der Grund für diesen Fehler ist wahrscheinlich eine Replikationsverzögerung. Der Dienstprinzipal wird in einer Region erstellt, die Rollenzuweisung kann aber in einer anderen Region stattfinden, in die der Dienstprinzipal noch nicht repliziert wurde.

Um dieses Szenario zu berücksichtigen, verwenden Sie die [Rollenzuweisungen: Erstellen](/rest/api/authorization/roleassignments/create) der Rest-API, und legen Sie die `principalType`-Eigenschaft auf `ServicePrincipal` fest. Sie müssen auch `apiVersion` auf `2018-09-01-preview` oder höher festlegen.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Auflisten von Azure-Rollenzuweisungen mithilfe der REST-API](role-assignments-list-rest.md)
- [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST-API-Referenz](/rest/api/azure/)
- [Erstellen oder Aktualisieren von benutzerdefinierten Rollen in Azure über die REST-API](custom-roles-rest.md)
