---
title: Verwenden der rollenbasierten Zugriffssteuerung in Azure API Management | Microsoft-Dokumentation
description: Informationen zum Verwenden der integrierten Rollen und zum Erstellen benutzerdefinierter Rollen in Azure API Management
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/18/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90278abd972b0a6f56e820090435f4cfb8ebbeb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643287"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Verwenden der rollenbasierten Zugriffssteuerung in Azure API Management

Azure API Management basiert auf der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure, um eine differenzierte Zugriffsverwaltung für API Management-Dienste und -Entitäten (etwa APIs und Richtlinien) zu ermöglichen. Dieser Artikel bietet Ihnen einen Überblick über die integrierten und benutzerdefinierten Rollen in API Management. Weitere Informationen zur Zugriffsverwaltung im Azure-Portal finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Integrierte Rollen

API Management bietet zurzeit drei integrierte Rollen, und es werden in naher Zukunft zwei weitere Rollen hinzugefügt. Diese Rollen können auf verschiedenen Ebenen zugewiesen werden, darunter Abonnement, Ressourcengruppe und einzelne API Management-Instanz. Beispiel: Wenn einem Benutzer die Leserolle für den API Management-Dienst (API Management Service Reader) auf Ressourcengruppenebene zugewiesen wird, besitzt der Benutzer innerhalb der Ressourcengruppe Lesezugriff auf alle Instanzen von API Management. 

Die folgende Tabelle enthält kurze Beschreibungen der integrierten Rollen. Sie können diese Rollen über das Azure-Portal oder andere Tools zuweisen, beispielsweise Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), die [Azure CLI](../role-based-access-control/role-assignments-cli.md) oder die [REST-API](../role-based-access-control/role-assignments-rest.md). Informationen zum Zuweisen integrierter Rollen finden Sie unter [Zuweisen von Azure-Rollen zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md).

| Role          | Lesezugriff<sup>[1]</sup> | Schreibzugriff<sup>[2]</sup> | Dienst erstellen, löschen, skalieren, Konfiguration von VPN und benutzerdefinierter Domäne | Zugriff auf das Legacy-Herausgeberportal | BESCHREIBUNG
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Mitwirkender des API-Verwaltungsdienstes | ✓ | ✓ | ✓ | ✓ | Superuser. Besitzt CRUD-Vollzugriff auf API Management-Dienste und -Entitäten (z.B. APIs, Richtlinien). Besitzt Zugriff auf das Legacy-Herausgeberportal. |
| Leser des API Management-Diensts | ✓ | | || Besitzt schreibgeschützten Zugriff auf API Management-Dienste und -Entitäten. |
| Operator des API Management-Diensts | ✓ | | ✓ | | Kann API Management-Dienste, jedoch keine Entitäten verwalten.|

<sup>[1] Lesezugriff auf API Management-Dienste und -Entitäten (z.B. APIs, Richtlinien)</sup>

<sup>[2] Schreibzugriff auf API Management-Dienste und -Entitäten mit Ausnahme der folgenden Vorgänge: Erstellen, Löschen und Skalieren von Instanzen; VPN-Konfiguration; Einrichten benutzerdefinierter Domänen</sup>

## <a name="custom-roles"></a>Benutzerdefinierte Rollen

Wenn keine der integrierten Rollen Ihre Anforderungen erfüllt, können benutzerdefinierte Rollen erstellt werden, um eine detailliertere Zugriffsverwaltung für API Management-Entitäten bereitzustellen. Beispielsweise können Sie eine benutzerdefinierte Rolle erstellen, die schreibgeschützten Zugriff auf einen API Management-Dienst, aber nur Schreibzugriff für eine bestimmte API besitzt. Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/custom-roles.md). 

> [!NOTE]
> Damit eine API Management-Dienstinstanz im Azure-Portal angezeigt wird, muss eine benutzerdefinierte Rolle die Aktion ```Microsoft.ApiManagement/service/read``` einschließen.

Wenn Sie eine benutzerdefinierte Rolle erstellen, ist es einfacher, mit einer der integrierten Rollen zu beginnen. Bearbeiten Sie die Attribute, und fügen Sie **Actions**, **NotActions** oder **AssignableScopes** hinzu. Speichern Sie die Änderungen anschließend als neue Rolle. Im folgenden Beispiel wird von der Leserolle für den API Management-Dienst ausgegangen und eine benutzerdefinierte Rolle namens „Calculator API Editor“ (Rechner-API-Editor) erstellt. Sie können die benutzerdefinierte Rolle einer bestimmten API zuweisen. Daher hat diese Rolle nur Zugriff auf diese API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Der Artikel [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) enthält die Liste der Berechtigungen, die auf API Management-Ebene gewährt werden können.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur rollenbasierten Zugriffssteuerung in Azure finden Sie in den folgenden Artikeln:
  * [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md)
  * [Zuweisen von Azure-Rollen zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md)
  * [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/custom-roles.md)
  * [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
