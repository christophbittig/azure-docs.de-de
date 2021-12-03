---
title: Gewähren der Berechtigung zum Zugreifen auf einen Azure-Schlüsseltresor für Anwendungen mit Azure RBAC | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Schlüssel, Geheimnisse und Zertifikate mithilfe der rollenbasierten Zugriffssteuerung in Azure bereitstellen.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c92b17158beee9d1f6c60becb858564555bf56bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048442"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure

> [!NOTE]
> Der Key Vault-Ressourcenanbieter unterstützt zwei Ressourcentypen: **Tresore** und **verwaltete HSMs**. Die in diesem Artikel beschriebene Zugriffssteuerung gilt nur für **Tresore**. Weitere Informationen zur Zugriffssteuerung für verwaltetes HSM finden Sie unter [Zugriffssteuerung für verwaltetes HSM](../managed-hsm/access-control.md).

Die rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC) ist ein Autorisierungssystem, das auf [Azure Resource Manager](../../azure-resource-manager/management/overview.md) basiert und eine präzise Verwaltung des Zugriffs auf Azure-Ressourcen ermöglicht.

Azure RBAC ermöglicht Benutzern das Verwalten von Berechtigungen für Schlüssel, Geheimnisse und Zertifikate. Es bietet einen Ort, an dem alle Berechtigungen für alle Schlüsseltresore verwaltet werden können. 

Das Azure RBAC-Modell bietet die Möglichkeit, Berechtigungen für verschiedene Bereichsebenen festzulegen: Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Einzelressourcen.  Azure RBAC für Key Vault bietet außerdem die Möglichkeit, separate Berechtigungen für einzelne Schlüssel, Geheimnisse und Zertifikate zu verwalten.

Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Bewährte Methoden für einzelne Schlüssel, Geheimnisse und Zertifikate

Es wird empfohlen, einen Schlüsseltresor pro Anwendung und Umgebung (Entwicklung, Präproduktion und Produktion) zu verwenden.

Die Berechtigungen für einzelne Schlüssel, Geheimnisse und Zertifikate sollten nur in bestimmten Szenarien verwendet werden:

-   Das Freigeben einzelner Geheimnisse zwischen mehreren Anwendungen, z. B. muss eine Anwendung auf Daten aus der anderen Anwendung zugreifen
-   Eine mandantenübergreifende Verschlüsselung mit Kundenschlüssel, z. B. ein ISV, der einen Schlüssel aus einem Kundenschlüsseltresor verwendet, um seine Daten zu verschlüsseln

Weitere Informationen zu Azure Key Vault-Verwaltungsrichtlinien finden Sie unter:

- [Bewährte Methoden bei Azure Key Vault](best-practices.md)
- [Grenzwerte des Azure Key Vault-Diensts](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>In Azure integrierte Rollen für Key Vault-Vorgänge auf Datenebene

> [!NOTE]
> Die Rolle `Key Vault Contributor` ist für Vorgänge auf Verwaltungsebene zur Verwaltung von Schlüsseltresoren vorgesehen. Sie lässt nicht den Zugriff auf Schlüssel, Geheimnisse und Zertifikate zu.

| Integrierte Rolle | BESCHREIBUNG | id |
| --- | --- | --- |
| Key Vault-Administrator| Ausführen beliebiger Vorgänge auf Datenebene für einen Schlüsseltresor und alle darin enthaltenen Objekte aus (einschließlich Zertifikate, Schlüssel und Geheimnisse). Kann keine Key Vault-Ressourcen oder Rollenzuweisungen verwalten. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault-Zertifikatbeauftragter | Ausführen beliebiger Aktionen für die Zertifikate eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault-Kryptografiebeauftragter | Ausführen beliebiger Aktionen für die Schlüssel eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault Crypto Service Encryption-Benutzer | Lesen von Metadaten von Schlüsseln und Ausführen von Vorgängen zum Packen/Entpacken. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault-Kryptografiebenutzer  | Ausführen kryptografischer Vorgänge mithilfe von Schlüsseln. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault-Leser | Lesen von Metadaten von Schlüsseltresoren und deren Zertifikaten, Schlüsseln und Geheimnissen. Sensible Werte, z. B. der Inhalt von Geheimnissen oder Schlüsselmaterial, können nicht gelesen werden. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault-Geheimnisbeauftragter| Ausführen beliebiger Aktionen für die Geheimnisse eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault-Geheimnisbenutzer | Lesen der Inhalte von Geheimnissen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 4633458b-17de-408a-b874-0445c86b69e6 |

Weitere Informationen zu den Definitionen von in Azure integrierten Rollen finden Sie unter [In Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Verwenden von Geheimnis-, Schlüssel- und Zertifikatberechtigungen von Azure RBAC mit Key Vault

Das neue Azure RBAC-Berechtigungsmodell für Key Vault bietet eine Alternative zum Berechtigungsmodell für den Tresor mit Zugriffsrichtlinien. 

### <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement. Sollten Sie über kein Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Zum Hinzufügen von Rollenzuweisungen benötigen Sie Berechtigungen vom Typ `Microsoft.Authorization/roleAssignments/write` und `Microsoft.Authorization/roleAssignments/delete` (beispielsweise als [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../../role-based-access-control/built-in-roles.md#owner)).

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Aktivieren von Azure RBAC-Berechtigungen für einen Schlüsseltresor

> [!NOTE]
> Das Ändern des Berechtigungsmodells erfordert die Berechtigung „Microsoft.Authorization/roleAssignments/write“, die in den Rollen [Besitzer](../../role-based-access-control/built-in-roles.md#owner) und [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) enthalten ist. Klassische Administratorrollen für ein Abonnement wie „Dienstadministrator“ und „Co-Admin“ werden nicht unterstützt.

1.  Aktivieren von Azure RBAC-Berechtigungen für einen neuen Schlüsseltresor:

    ![Aktivieren von Azure RBAC-Berechtigungen – neuer Tresor](../media/rbac/image-1.png)

2.  Aktivieren von Azure RBAC-Berechtigungen für einen vorhandenen Schlüsseltresor:

    ![Aktivieren von Azure RBAC-Berechtigungen – vorhandener Tresor](../media/rbac/image-2.png)

> [!IMPORTANT]
> Wenn Sie das Azure RBAC-Berechtigungsmodell festlegen, werden alle Berechtigungen aus Zugriffsrichtlinien ungültig. Dies kann zu Ausfällen führen, wenn keine äquivalenten Azure-Rollen zugewiesen sind.

### <a name="assign-role"></a>Zuweisen einer Rolle

> [!Note]
> Es wird empfohlen, in Skripts die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, funktionieren somit Ihre Skripts weiterhin. In diesem Dokument wird der Rollenname nur zur besseren Lesbarkeit verwendet.

Führen Sie den folgenden Befehl aus, um eine Rollenzuweisung zu erstellen:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

Informationen zum Zuweisen von Rollen über das Azure-Portal finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).  Im Azure-Portal ist der Bildschirm „Azure-Rollenzuweisungen“ für alle Ressourcen auf der Registerkarte „Zugriffssteuerung (IAM)“ verfügbar.

### <a name="resource-group-scope-role-assignment"></a>Rollenzuweisung im Ressourcengruppenbereich

1. Navigieren Sie zu der Ressourcengruppe, in der sich Ihr Schlüsseltresor befindet.

    ![Rollenzuweisung – Ressourcengruppe](../media/rbac/image-4.png)

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | „Leseberechtigter für Schlüsseltresore“ |
    | Zugriff zuweisen zu | Aktueller Benutzer |
    | Members | Nach E-Mail-Adresse suchen |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

Die obige Rollenzuweisung bietet die Möglichkeit, Key Vault-Objekte in einem Schlüsseltresor aufzulisten.

### <a name="key-vault-scope-role-assignment"></a>Rollenzuweisung im Key Vault-Bereich

1. Wechseln Sie zur Registerkarte „Key Vault“ \> „Zugriffssteuerung (IAM)“.
1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | „Geheimnisbeauftragter für Schlüsseltresore“ |
    | Zugriff zuweisen zu | Aktueller Benutzer |
    | Members | Nach E-Mail-Adresse suchen |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

### <a name="secret-scope-role-assignment"></a>Rollenzuweisung im Geheimnisbereich

1. Öffnen Sie ein zuvor erstelltes Geheimnis.

1. Klicken Sie auf die Registerkarte „Zugriffssteuerung (IAM)“.

    ![Rollenzuweisung – Geheimnis](../media/rbac/image-8.png)

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | „Geheimnisbeauftragter für Schlüsseltresore“ |
    | Zugriff zuweisen zu | Aktueller Benutzer |
    | Members | Nach E-Mail-Adresse suchen |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

### <a name="test-and-verify"></a>Testen und Überprüfen

> [!NOTE]
> Browser verwenden Caching, deshalb ist nach dem Entfernen von Rollenzuweisungen eine Seitenaktualisierung erforderlich.<br>
> Warten Sie einige Minuten, bis die Rollenzuweisungen aktualisiert wurden.

1. Überprüfen Sie das Hinzufügen eines neuen Geheimnisses ohne die Rolle „Key Vault Secrets Officer“ auf der Ebene des Schlüsseltresors.

Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM)“ für den Schlüsseltresor, und entfernen Sie die Rollenzuweisung „Key Vault Secrets Officer“ für diese Ressource.

![Entfernen der Rollenzuweisung – Key Vault](../media/rbac/image-9.png)

Navigieren Sie zum zuvor erstellten Geheimnis. Alle Eigenschaften des Geheimnisses werden angezeigt.

![Ansicht eines Geheimnisses mit Zugriff](../media/rbac/image-10.png)

Erstellen Sie ein neues Geheimnis („Geheimnisse“ \> „+ Generieren/Importieren“). Folgender Fehler sollte angezeigt werden:

   ![Erstellen eines neuen Geheimnisses](../media/rbac/image-11.png)

2.  Überprüfen Sie die Geheimnisbearbeitung ohne die Rolle „Key Vault Secret Officer“ auf Geheimnisebene.

-   Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM)“ für das zuvor erstellte Geheimnis, und entfernen Sie die Rollenzuweisung „Key Vault Secrets Officer“ für diese Ressource.

-   Navigieren Sie zum zuvor erstellten Geheimnis. Die Eigenschaften des Geheimnisses werden angezeigt.

   ![Ansicht eines Geheimnisses ohne Zugriff](../media/rbac/image-12.png)

3. Überprüfen Sie das Lesen von Geheimnissen ohne die Leser-Rolle auf Schlüsseltresorebene.

-   Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM)“ für die Schlüsseltresor-Ressourcengruppe, und entfernen Sie die Rollenzuweisung „Key Vault Reader“.

-   Nach dem Navigieren zur Registerkarte „Geheimnisse“ für den Schlüsseltresor sollte der nachstehende Fehler angezeigt werden:

   ![Registerkarte „Geheimnis“ – Fehler](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Erstellen von benutzerdefinierten Rollen 

[Befehl „az role definition create“](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Weitere Informationen zum Erstellen von benutzerdefinierten Rollen finden Sie unter:

[Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Bekannte Grenzwerte und Leistung

-   RBAC auf der Key Vault-Datenebene wird in Szenarien mit mehreren Mandanten (wie etwa bei Azure Lighthouse) nicht unterstützt.
-   2\.000 Azure-Rollenzuweisungen pro Abonnement
-   Latenz der Rollenzuweisungen: Bei der aktuellen erwarteten Leistung dauert es bis zu 10 Minuten (600 Sekunden) nach dem Ändern einer Rollenzuweisung, bis dies auf die Rolle angewandt wird.

## <a name="learn-more"></a>Weitere Informationen
1. Weisen Sie die Rolle „[ROLENAME]“ einer der folgenden Optionen im Bereich „[MANAGEMENTGROUP | SUBSCRIPTION | RESOURCEGROUP | RESOURCE]“ zu: [USER | GROUP | SERVICEPRINCIPAL | MANAGEDIDENTITY].


- [RBAC in Azure – Übersicht](../../role-based-access-control/overview.md)
- [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md)
- [Tutorial zu benutzerdefinierten Rollen](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Bewährte Methoden zum Verwenden von Key Vault](best-practices.md)
