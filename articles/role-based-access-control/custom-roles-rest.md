---
title: Erstellen oder Aktualisieren von benutzerdefinierten Rollen in Azure über die REST-API – Azure RBAC
description: Erfahren Sie, wie Sie benutzerdefinierte Rollen in Azure mithilfe der REST-API und Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) auflisten, erstellen, aktualisieren oder löschen.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5c7e816f15400a28e8b10f4aea7a2315c89048be
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352922"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Erstellen oder Aktualisieren von benutzerdefinierten Rollen in Azure über die REST-API

> [!IMPORTANT]
> Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn die [integrierten Azure-Rollen](built-in-roles.md) die Anforderungen Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen mithilfe der REST-API auflisten, erstellen, aktualisieren oder löschen.

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen

Verwenden Sie zum Auflisten aller benutzerdefinierten Rollen in einem Verzeichnis die [Rollendefinitionen – Liste](/rest/api/authorization/roledefinitions/list)-REST-API.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersetzen Sie *{Filter}* mit dem Rollentyp.

    > [!div class="mx-tableFixed"]
    > | Filtern | BESCHREIBUNG |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Auf dem CustomRole-Typ basierender Filter |

## <a name="list-custom-roles-at-a-scope"></a>Auflisten benutzerdefinierter Rollen in einem Bereich

Verwenden Sie zum Auflisten benutzerdefinierter Rollen in einem Bereich die [Rollendefinitionen – Liste](/rest/api/authorization/roledefinitions/list)-REST-API.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* mit dem Bereich, für den Sie die Rollen auflisten möchten.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{Filter}* mit dem Rollentyp.

    > [!div class="mx-tableFixed"]
    > | Filtern | BESCHREIBUNG |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Auf dem CustomRole-Typ basierender Filter |

## <a name="list-a-custom-role-definition-by-name"></a>Auflisten einer Definition einer benutzerdefinierten Rolle nach Namen

Verwenden Sie die [Rollendefinitionen – Abrufen](/rest/api/authorization/roledefinitions/get)-REST-API zum Abrufen von Informationen zu einer benutzerdefinierten Rolle nach ihrem Anzeigenamen.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* mit dem Bereich, für den Sie die Rollen auflisten möchten.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{Filter}* mit dem Anzeigenamen für die Rolle.

    > [!div class="mx-tableFixed"]
    > | Filtern | BESCHREIBUNG |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Verwenden Sie die URL-codierte Form des genauen Anzeigenamens der Rolle. Beispiel: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'`. |

## <a name="list-a-custom-role-definition-by-id"></a>Auflisten einer Definition einer benutzerdefinierten Rolle nach ID

Verwenden Sie die [Rollendefinitionen – Abrufen](/rest/api/authorization/roledefinitions/get)-REST-API zum Abrufen von Informationen zu einer benutzerdefinierten Rolle nach ihrem eindeutigen Bezeichner.

1. Verwenden Sie die [Rollendefinitionen – Liste](/rest/api/authorization/roledefinitions/list)-REST-API zum Abrufen des GUID-Bezeichners für die Rolle.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* mit dem Bereich, für den Sie die Rollen auflisten möchten.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{roledefinitionid}* durch den GUID-Bezeichner der Rollendefinition.

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle zu erstellen, verwenden Sie die [Rollendefinitionen – Erstellen oder Aktualisieren](/rest/api/authorization/roledefinitions/createorupdate)-REST-API. Um diese API aufzurufen, müssen Sie als Benutzer angemeldet sein, dem eine Rolle zugewiesen ist, die über die `Microsoft.Authorization/roleDefinitions/write`-Berechtigung für alle `assignableScopes` verfügt. Unter den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über diese Berechtigung.

1. Überprüfen Sie die Liste der [Ressourcenanbietervorgänge](resource-provider-operations.md), die zum Erstellen von Berechtigungen für die benutzerdefinierte Rolle verfügbar sind.

1. Verwenden Sie ein GUID-Tool, um einen eindeutigen Bezeichner zu generieren, der für den benutzerdefinierten Rollenbezeichner verwendet wird. Das Format des Bezeichners ist: `00000000-0000-0000-0000-000000000000`

1. Beginnen Sie mit folgender Anforderung und folgendem Text:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Ersetzen Sie im URI *{scope}* durch die ersten `assignableScopes` der benutzerdefinierten Rolle.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{roleDefinitionId}* durch den GUID-Bezeichner der benutzerdefinierten Rolle.

1. Ersetzen Sie im Anforderungstext *{roleDefinitionId}* mit dem GUID-Bezeichner.

1. Wenn `assignableScopes` ein Abonnement oder eine Ressourcengruppe ist, ersetzen Sie die *{subscriptionId}* - oder *{resourceGroup}* -Instanz durch Ihre Bezeichner.

1. Wenn `assignableScopes` eine Verwaltungsgruppe ist, ersetzen Sie die *{GroupID}* Instanz durch ihren Verwaltungsgruppenbezeichner. Das Hinzufügen von Verwaltungsgruppen zu `assignableScopes` befindet sich derzeit in der Vorschauphase.

1. Fügen Sie in der `actions`-Eigenschaft die Aktionen hinzu, deren Ausführung die Rolle zulässt.

1. Fügen Sie in der `notActions`-Eigenschaft die Aktionen hinzu, die von den zulässigen `actions` ausgeschlossen sind.

1. Geben Sie in den Eigenschaften `roleName` und `description` einen eindeutigen Rollennamen und eine Beschreibung an. Weitere Informationen zu den Eigenschaften finden Sie unter [Benutzerdefinierte Rollen in Azure](custom-roles.md).

    Es folgt ein Beispiel für einen Anforderungstext:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle zu aktualisieren, verwenden Sie die [Rollendefinitionen – Erstellen oder Aktualisieren](/rest/api/authorization/roledefinitions/createorupdate)-REST-API. Um diese API aufzurufen, müssen Sie als Benutzer angemeldet sein, dem eine Rolle zugewiesen ist, die über die `Microsoft.Authorization/roleDefinitions/write`-Berechtigung für alle `assignableScopes` verfügt. Unter den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über diese Berechtigung.

1. Verwenden Sie die [Rollendefinitionen – Liste](/rest/api/authorization/roledefinitions/list)- oder [Rollendefinitionen – Abrufen](/rest/api/authorization/roledefinitions/get)-REST-API zum Abrufen von Informationen zu der benutzerdefinierten Rolle. Weitere Informationen finden Sie im oben stehenden Abschnitt [Auflisten benutzerdefinierter Rollen](#list-custom-roles).

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Ersetzen Sie im URI *{scope}* durch die ersten `assignableScopes` der benutzerdefinierten Rolle.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{roleDefinitionId}* durch den GUID-Bezeichner der benutzerdefinierten Rolle.

1. Erstellen Sie basierend auf den Informationen über die benutzerdefinierte Rolle einen Anforderungstext mit folgendem Format:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Aktualisieren Sie den Anforderungstext mit den Änderungen, die Sie an der benutzerdefinierten Rolle vornehmen möchten.

    Im Folgenden sehen Sie ein Beispiel für einen Anforderungstext mit einer neu hinzugefügten Diagnoseeinstellungenaktion:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Löschen einer benutzerdefinierten Rolle

Verwenden Sie zum Löschen einer benutzerdefinierten Rolle die [Rollendefinitionen – Löschen](/rest/api/authorization/roledefinitions/delete)-REST-API. Um diese API aufzurufen, müssen Sie als Benutzer angemeldet sein, dem eine Rolle zugewiesen ist, die über die `Microsoft.Authorization/roleDefinitions/delete`-Berechtigung für alle `assignableScopes` verfügt. Unter den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über diese Berechtigung.

1. Verwenden Sie die [Rollendefinitionen – Liste](/rest/api/authorization/roledefinitions/list)- oder [Rollendefinitionen – Abrufen](/rest/api/authorization/roledefinitions/get)-REST-API zum Abrufen des GUID-Bezeichners der benutzerdefinierten Rolle. Weitere Informationen finden Sie im oben stehenden Abschnitt [Auflisten benutzerdefinierter Rollen](#list-custom-roles).

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* mit dem Bereich, in dem Sie die benutzerdefinierte Rolle löschen möchten.

    > [!div class="mx-tableFixed"]
    > | `Scope` | type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Verwaltungsgruppe |

1. Ersetzen Sie *{roleDefinitionId}* durch den GUID-Bezeichner der benutzerdefinierten Rolle.

## <a name="next-steps"></a>Nächste Schritte

- [Benutzerdefinierte Azure-Rollen](custom-roles.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](role-assignments-rest.md)
- [Azure REST-API-Referenz](/rest/api/azure/)
