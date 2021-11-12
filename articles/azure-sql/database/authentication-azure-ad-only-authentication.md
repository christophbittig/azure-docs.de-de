---
title: Azure Active Directory – Authentifizierung
description: Dieser Artikel enthält Informationen zur reinen Azure AD-Authentifizierung (Azure Active Directory) mit Azure SQL-Datenbank und Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/03/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 050a9690f22ef34b39d89a5cd0cf4d8a6c7d340b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503051"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Reine Azure AD-Authentifizierung mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Die reine Azure AD-Authentifizierung ist ein Feature in [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md), das es dem Dienst ermöglicht, nur die Azure AD-Authentifizierung zu unterstützen. Es wird für [Azure SQL-Datenbank](sql-database-paas-overview.md) und [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) unterstützt. Wenn die reine Azure AD-Authentifizierung in der Azure SQL-Umgebung aktiviert wird, wird die SQL-Authentifizierung deaktiviert – auch für Verbindungen von SQL Server-Administratoren, -Anmeldungen und -Benutzer. Nur Benutzer, die die [Azure AD-Authentifizierung](authentication-aad-overview.md) verwenden, sind autorisiert, eine Verbindung mit dem Server oder der Datenbank herzustellen.

Die reine Azure AD-Authentifizierung kann über das Azure-Portal, über die Azure CLI, mithilfe von PowerShell oder per REST-API aktiviert oder deaktiviert werden. Des Weiteren kann die reine Azure AD-Authentifizierung im Rahmen der Servererstellung mit einer ARM-Vorlage konfiguriert werden.

Weitere Informationen zur Azure SQL-Authentifizierung finden Sie unter [Authentifizierung und Autorisierung](logins-create-manage.md#authentication-and-authorization).

## <a name="feature-description"></a>Featurebeschreibung

Wenn Sie die reine Azure AD-Authentifizierung aktivieren, wird die [SQL-Authentifizierung](logins-create-manage.md#authentication-and-authorization) auf Ebene des Servers oder der verwalteten Instanz deaktiviert und jegliche Authentifizierung verhindert, die auf Anmeldeinformationen für die SQL-Authentifizierung basiert. Benutzer*innen der SQL-Authentifizierung können keine Verbindung mit dem [logischen Server](logical-servers.md) für die Azure SQL-Datenbank oder für die verwaltete Instanz mehr herstellen – auch nicht mit deren Datenbanken. Trotz deaktivierter SQL-Authentifizierung können über Azure AD-Konten mit entsprechenden Berechtigungen weiterhin neue Anmeldenamen und Benutzer für die SQL-Authentifizierung erstellt werden. Neu erstellte SQL-Authentifizierungskonten sind nicht berechtigt, eine Verbindung mit dem Server herzustellen. Durch Aktivieren der reinen Azure AD-Authentifizierung werden keine vorhandenen Anmeldenamen und Benutzerkonten für die SQL-Authentifizierung entfernt. Durch das Feature wird lediglich verhindert, dass von diesen Konten eine Verbindung mit dem Server oder einer für diesen Server erstellten Datenbank hergestellt wird.

Sie können auch erzwingen, dass Server mit aktivierter Azure AD Authentifizierung erstellt werden, indem Sie Azure Policy verwenden. Weitere Informationen zu diesem Feature finden Sie unter [Azure Policy für reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication-policy.md).

## <a name="permissions"></a>Berechtigungen

Die reine Azure AD-Authentifizierung kann von Azure AD-Benutzern aktiviert oder deaktiviert werden, die einer [integrierten Azure AD-Rolle](../../role-based-access-control/built-in-roles.md) mit hohen Berechtigungen angehören. Hierzu zählen beispielsweise [Besitzer](../../role-based-access-control/built-in-roles.md#owner), [Mitwirkende](../../role-based-access-control/built-in-roles.md#contributor) und [globale Administratoren](../../active-directory/roles/permissions-reference.md#global-administrator) eines Azure-Abonnements. Darüber hinaus kann die reine Azure AD-Authentifizierung auch mit der Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) aktiviert oder deaktiviert werden.

Die Rollen [SQL Server-Mitwirkender](../../role-based-access-control/built-in-roles.md#sql-server-contributor) und [Mitwirkender für verwaltete SQL-Instanzen](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) sind nicht zum Aktivieren oder Deaktivieren der reinen Azure AD-Authentifizierung berechtigt. Dies entspricht dem Ansatz der [Aufgabentrennung](security-best-practice.md#implement-separation-of-duties), der dafür sorgt, dass Benutzer, die einen Azure SQL-Server oder Azure AD-Administrator erstellen können, keine Sicherheitsfeatures aktivieren oder deaktivieren können.

### <a name="actions-required"></a>Aktionen erforderlich

Der Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) werden folgende Aktionen hinzugefügt, um die Verwaltung der reinen Azure AD-Authentifizierung zu ermöglichen:

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft.Sql/servers/administrators/read: Nur für Benutzer erforderlich, die auf das Menü **Azure Active Directory** im Azure-Portal zugreifen
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

Die oben angegebenen Aktionen können auch einer benutzerdefinierten Rolle für die Verwaltung der reinen Azure AD-Authentifizierung hinzugefügt werden. Weitere Informationen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle in Azure Active Directory](../../active-directory/roles/custom-create.md).

## <a name="managing-azure-ad-only-authentication-using-apis"></a>Verwalten der reinen Azure AD-Authentifizierung mithilfe von APIs

> [!IMPORTANT]
> Der Azure AD-Administrator muss vor dem Aktivieren der reinen Azure AD-Authentifizierung festgelegt werden.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie benötigen mindestens die Azure CLI-Version **2.14.2**.

`name` entspricht dem Präfix des Server- oder Instanznamens (beispielsweise `myserver`), und `resource-group` entspricht der Ressource, zu der der Server gehört (beispielsweise `myresource`).

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

Weitere Informationen finden Sie unter [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth).

### <a name="enable-or-disable-in-sql-database"></a>Aktivieren oder Deaktivieren in SQL-Datenbank

**Aktivieren**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**Deaktivieren**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>Überprüfen des Status in SQL-Datenbank

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

Weitere Informationen finden Sie unter [az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth).

**Aktivieren**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**Deaktivieren**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>Überprüfen des Status in SQL Managed Instance

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie benötigen mindestens das Modul [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0).

`ServerName` oder `InstanceName` entspricht dem Präfix des Servernamens (beispielsweise `myserver` oder `myinstance`), und `ResourceGroupName` entspricht der Ressource, zu der der Server gehört (beispielsweise `myresource`).

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

### <a name="enable-or-disable-in-sql-database"></a>Aktivieren oder Deaktivieren in SQL-Datenbank

**Aktivieren**

Weitere Informationen finden Sie unter [Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication). Sie können auch `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full` ausführen.

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

Sie können auch den folgenden Befehl verwenden:

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

**Deaktivieren**

Weitere Informationen finden Sie unter [Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication).

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>Überprüfen des Status in SQL-Datenbank

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

Sie können auch den folgenden Befehl verwenden:

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

### <a name="enable-or-disable-in-sql-managed-instance"></a>Aktivieren oder Deaktivieren in SQL Managed Instance

**Aktivieren**

Weitere Informationen finden Sie unter [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Sie können auch den folgenden Befehl verwenden:

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

Weitere Informationen zu diesen PowerShell-Befehlen erhalten Sie durch Ausführen von `get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full`.

**Deaktivieren**

Weitere Informationen finden Sie unter [Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication).

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>Überprüfen des Status in SQL Managed Instance

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

Sie können auch den folgenden Befehl verwenden:

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

Folgende Parameter müssen definiert werden:

- `<subscriptionId>` finden Sie im Azure-Portal unter **Abonnements**.
- `<myserver>` entspricht dem Präfix des Server- oder Instanznamens (beispielsweise `myserver`).
- `<myresource>` entspricht der Ressource, zu der der Server gehört (beispielsweise `myresource`).

Die aktuelle MSAL-Version können Sie hier herunterladen: https://www.powershellgallery.com/packages/MSAL.PS.

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

Weitere Informationen finden Sie in der REST-API-Dokumentation unter [Reine Azure AD-Authentifizierungen für Server](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-database"></a>Aktivieren oder Deaktivieren in SQL-Datenbank

**Aktivieren**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Deaktivieren**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>Überprüfen des Status in SQL-Datenbank

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

Weitere Informationen finden Sie in der REST-API-Dokumentation unter [Reine Azure AD-Authentifizierungen für Managed Instance](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications).

### <a name="enable-or-disable-in-sql-managed-instance"></a>Aktivieren oder Deaktivieren in SQL Managed Instance

**Aktivieren**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Deaktivieren**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>Überprüfen des Status in SQL Managed Instance

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm)

- Geben Sie den Azure AD-Administrator für die Bereitstellung ein. Die Objekt-ID des Benutzers finden Sie im [Azure-Portal](https://portal.azure.com) unter der Ressource **Azure Active Directory**. Wählen Sie unter **Verwalten** die Option **Benutzer** aus. Suchen Sie nach dem Benutzer, den Sie als Azure AD-Administrator für Ihren Azure SQL-Server festlegen möchten. Wählen Sie den Benutzer aus. Auf der Seite **Profil** wird die **Objekt-ID** angezeigt.
- Die Mandanten-ID finden Sie auf der Seite **Übersicht** der Ressource **Azure Active Directory**.

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

### <a name="enable"></a>Aktivieren

Durch die folgende ARM-Vorlage wird die reine Azure AD-Authentifizierung in Ihrer Azure SQL-Datenbank-Instanz aktiviert. Wenn Sie die reine Azure AD-Authentifizierung deaktivieren möchten, legen Sie die Eigenschaft `azureADOnlyAuthentication` auf `false` fest.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

Weitere Informationen finden Sie unter [Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications).

## <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

### <a name="enable"></a>Aktivieren

Durch die folgende ARM-Vorlage wird die reine Azure AD-Authentifizierung in Ihrer Instanz von Azure SQL Managed Instance aktiviert. Wenn Sie die reine Azure AD-Authentifizierung deaktivieren möchten, legen Sie die Eigenschaft `azureADOnlyAuthentication` auf `false` fest.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

Weitere Informationen finden Sie unter [Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications).

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>Überprüfen der reinen Azure AD-Authentifizierung mithilfe von T-SQL

[SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly` wurde hinzugefügt, um zu überprüfen, ob die reine Azure AD-Authentifizierung für Ihren Server oder Ihre verwaltete Instanz aktiviert ist. `1` gibt an, dass das Feature aktiviert ist. `0` bedeutet, dass das Feature deaktiviert ist.

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>Bemerkungen

- Ein [SQL Server-Mitwirkender](../../role-based-access-control/built-in-roles.md#sql-server-contributor) kann einen Azure AD-Administrator festlegen oder entfernen, aber nicht die Einstellung **Authentifizierung nur bei Azure Active Directory** festlegen. Der [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) kann keinen Azure AD-Administrator festlegen oder entfernen, aber die Einstellung **Authentifizierung nur bei Azure Active Directory** festlegen. Nur Konten mit höheren Azure RBAC-Rollen oder benutzerdefinierten Rollen, die beide Berechtigungen enthalten, können einen Azure AD-Administrator festlegen oder entfernen und die Einstellung **Authentifizierung nur bei Azure Active Directory** festlegen. Hierzu zählt unter anderem die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor).
- Nach der Aktivierung oder Deaktivierung von **Authentifizierung nur bei Azure Active Directory** im Azure-Portal wird im Menü **SQL Server** der Eintrag **Aktivitätsprotokoll** angezeigt.
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Eintrag „Aktivitätsprotokoll“ im Azure-Portal":::
- Die Einstellung **Authentifizierung nur bei Azure Active Directory** kann nur von Benutzern mit den passenden Berechtigungen aktiviert oder deaktiviert werden, wenn der **Azure Active Directory Administrator** angegeben ist. Ist der Azure AD-Administrator nicht festgelegt, bleibt die Einstellung **Authentifizierung nur bei Azure Active Directory** inaktiv und kann nicht aktiviert oder deaktiviert werden. Die reine Azure AD-Authentifizierung kann auch nicht über APIs aktiviert werden, wenn der Azure AD-Administrator nicht festgelegt ist.
- Bei aktivierter reiner Azure AD-Authentifizierung kann der Azure AD-Administrator von Benutzern mit entsprechenden Berechtigungen geändert werden.
- Über das Azure-Portal können Benutzer mit entsprechenden Berechtigungen den Azure AD-Administrator ändern und die reine Azure AD-Authentifizierung aktivieren oder deaktivieren. Beide Vorgänge können mit einem einzelnen Speichervorgang im Azure-Portal ausgeführt werden. Der Azure AD-Administrator muss festgelegt werden, um die reine Azure AD-Authentifizierung aktivieren zu können.
- Ist die reine Azure AD-Authentifizierung aktiviert, kann der Azure AD-Administrator nicht entfernt werden. Es ist nicht möglich, einen Azure AD-Administrator über eine API zu entfernen, während die reine Azure AD-Authentifizierung aktiviert ist.
    - Wenn die Einstellung **Authentifizierung nur bei Azure Active Directory** aktiviert ist, ist die Schaltfläche **Administrator entfernen** im Azure-Portal inaktiv.
- Es ist möglich, den Azure AD-Administrator zu entfernen und die Einstellung **Authentifizierung nur bei Azure Active Directory** zu deaktivieren, hierzu sind allerdings die passenden Berechtigungen für die Vorgänge erforderlich. Beide Vorgänge können mit einem einzelnen Speichervorgang im Azure-Portal ausgeführt werden.
- Azure AD-Benutzer mit entsprechenden Berechtigungen können die Identität vorhandener SQL-Benutzer annehmen.
    - Der Identitätswechsel zwischen Benutzern mit SQL-Authentifizierung funktioniert weiterhin, auch wenn die reine Azure AD-Authentifizierung aktiviert ist.

### <a name="limitations-for-azure-ad-only-authentication-in-sql-database"></a>Einschränkungen für die reine Azure AD-Authentifizierung in der SQL-Datenbank

Wenn die reine Azure AD-Authentifizierung für die SQL-Datenbank aktiviert ist, werden die folgenden Features nicht unterstützt:

- [Serverrollen der Azure SQL-Datenbank](security-server-roles.md)
- [Elastische Aufträge](job-automation-overview.md)
- [SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md)
- [Change Data Capture (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) – Wenn Sie eine Datenbank als Azure AD-Benutzer in Azure SQL-Datenbank erstellen und Change Data Capture für diese aktivieren, kann ein SQL-Benutzer keine Änderungen an CDC-Artefakten deaktivieren oder vornehmen. Ein anderer Azure AD-Benutzer kann CDC jedoch für dieselbe Datenbank aktivieren oder deaktivieren. Analog dazu funktioniert, wenn Sie eine Azure SQL-Datenbank als SQL Benutzer erstellen, das Aktivieren oder Deaktivieren von CDC als Azure AD-Benutzer nicht.
- [Transaktionsreplikation](/azure/azure-sql/managed-instance/replication-transactional-overview): Die SQL-Authentifizierung ist für die Konnektivität zwischen Replikationsteilnehmern erforderlich. Wenn also die reine Azure AD-Authentifizierung aktiviert ist, wird die Transaktionsreplikation für die SQL-Datenbank nicht für Szenarios unterstützt, in denen die Transaktionsreplikation zum Pushen von Änderungen in einer Azure SQL Managed Instance, auf einem lokalen SQL Server oder in einer Azure VM SQL Server-Instanz in eine Datenbank in Azure SQL-Datenbank verwendet wird.
- [Erkenntnisse zu SQL](/azure/azure-monitor/insights/sql-insights-overview)
- EXEC AS-Anweisung für Azure AD-Gruppenmitgliedskonten

### <a name="limitations-for-azure-ad-only-authentication-in-managed-instance"></a>Einschränkungen für die reine Azure AD-Authentifizierung in Managed Instance

Wenn die reine Azure AD-Authentifizierung für Managed Instance aktiviert ist, werden die folgenden Features nicht unterstützt:

- [Transaktionsreplikation](/azure/azure-sql/managed-instance/replication-transactional-overview) 
- [SQL-Agent-Aufträge in Managed Instance](../managed-instance/job-automation-managed-instance.md) unterstützt die reine Azure AD-Authentifizierung. Azure AD-Benutzer*innen, die Mitglieder einer Azure AD-Gruppe sind, die Zugriff auf die verwaltete Instanz hat, können jedoch keine SQL-Agentaufträge besitzen.
- [Erkenntnisse zu SQL](/azure/azure-monitor/insights/sql-insights-overview)
- EXEC AS-Anweisung für Azure AD-Gruppenmitgliedskonten

Weitere Informationen zu Einschränkungen finden Sie unter [T-SQL-Unterschiede zwischen SQL Server und Azure SQL Managed Instance](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren der reinen Azure Active Directory-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [Erstellen eines Servers mit aktivierter reiner Azure AD-Authentifizierung in Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
