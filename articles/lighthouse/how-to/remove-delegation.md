---
title: Entfernen des Zugriffs auf eine Delegierung
description: Erfahren Sie, wie Sie Zugriff auf Ressourcen entfernen können, die an einen Dienstanbieter für Azure Lighthouse delegiert worden waren.
ms.date: 09/08/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 16d92c58e08e06832781ff7d5095039cedbb344f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736601"
---
# <a name="remove-access-to-a-delegation"></a>Entfernen des Zugriffs auf eine Delegierung

Nachdem das Abonnement oder die Ressourcengruppe eines Kunden an einen Dienstanbieter für [Azure Lighthouse](../overview.md) delegiert wurde, kann die Delegierung bei Bedarf entfernt werden. Nachdem eine Delegierung entfernt wurde, gilt der Zugriff [der delegierten Azure-Ressourcenverwaltung](../concepts/architecture.md), der den Benutzern im Dienstanbietermandanten zuvor gewährt wurde, nicht mehr.

Das Entfernen einer Delegierung kann durch einem Benutzer entweder im Kundenmandanten oder im Dienstanbietermandanten ausgeführt werden, sofern der Benutzer über die entsprechenden Berechtigungen verfügt.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden.

## <a name="customers"></a>Kunden

Benutzer im Mandanten des Kunden, denen eine Rolle mit der Berechtigung `Microsoft.Authorization/roleAssignments/write` (z. B. [Besitzer](../../role-based-access-control/built-in-roles.md#owner)) zugewiesen ist, können den Dienstanbieterzugriff auf das Abonnement (oder auf Ressourcengruppen in diesem Abonnement) entfernen. Zu diesem Zweck kann ein Benutzer zur Seite [Dienstanbieter](view-manage-service-providers.md#remove-service-provider-offers) des Azure-Portals navigieren, auf dem Bildschirm **Dienstanbieterangebote** nach dem Angebot suchen und das Papierkorbsymbol in der Zeile für das Angebot auswählen.

Nach dem Bestätigten des Löschvorgangs können keine Benutzer im Mandanten des Dienstanbieters auf die zuvor delegierten Ressourcen zugreifen.

## <a name="service-providers"></a>Dienstanbieter

Benutzer in einem Mandanten, der verwaltet wird, können den Zugriff auf delegierte Ressourcen entfernen, wenn Ihnen die [Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) für die Ressourcen des Kunden erteilt wurden. Wenn diese Rolle keinem Dienstanbieterbenutzer zugewiesen wurde, kann die Delegierung nur durch einen Benutzer im Mandanten des Kunden entfernt werden.

Im Beispiel unten wird eine Zuweisung gezeigt, die die **Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste** erteilt. Diese kann während des [Onboardingvorgangs](onboard-customer.md) in einer Parameterdatei enthalten sein:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Diese Rolle kann auch in einer **Autorisierung** ausgewählt werden, wenn ein [Angebot für einen verwalteten Dienst](../../marketplace/plan-managed-service-offer.md) zum Veröffentlichen in Azure Marketplace erstellt wird.

Ein Benutzer mit dieser Berechtigung kann eine Delegierung auf eine der folgenden Arten entfernen.

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zur Seite [Meine Kunden](view-manage-customers.md).
2. Wählen Sie **Delegierungen** aus.
3. Suchen Sie die Delegierung, die Sie entfernen möchten, und wählen Sie dann das Papierkorbsymbol aus, das in der Zeile angezeigt wird.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Lighthouse-Architektur](../concepts/architecture.md).
- [Anzeigen und Verwalten von Kunden](view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
- Erfahren Sie, wie Sie eine [vorherige Delegierung aktualisieren](update-delegation.md).
