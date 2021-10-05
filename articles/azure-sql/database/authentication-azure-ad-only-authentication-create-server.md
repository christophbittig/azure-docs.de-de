---
title: Erstellen eines Servers mit aktivierter reiner Azure Active Directory-Authentifizierung
description: In diesem Artikel wird Schritt für Schritt die Erstellung eines logischen Azure SQL-Servers bzw. einer verwalteten Instanz mit aktivierter reiner Azure AD-Authentifizierung (Azure Active Directory) beschrieben. Die Herstellung der Konnektivität per SQL-Authentifizierung ist hierbei also deaktiviert.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/31/2021
ms.openlocfilehash: 1519573670b3c97e1c47404ed457bf68c488108e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643192"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>Erstellen eines Servers mit aktivierter reiner Azure AD-Authentifizierung in Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Die in diesem Artikel behandelte **reine Azure AD-Authentifizierung** befindet sich in der **Public Preview-Phase**. Ausführliche Informationen zu diesem Feature finden Sie unter [Reine Azure AD-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication.md). Die reine Azure AD-Authentifizierung ist für Azure Synapse Analytics derzeit nicht verfügbar.

In dieser Schrittanleitung sind die Schritte zum Erstellen eines [logischen Servers](logical-servers.md) für Azure SQL-Datenbank oder einer [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) mit aktivierter [reiner Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md) während der Bereitstellung beschrieben. Mit dem Feature für die reine Azure AD-Authentifizierung wird verhindert, dass Benutzer eine Verbindung mit dem Server oder der verwalteten Instanz per SQL-Authentifizierung herstellen. Die Verbindung kann also nur über die Azure AD-Authentifizierung hergestellt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Bei Verwendung der Azure CLI wird Version 2.26.1 oder höher benötigt. Weitere Informationen zur Installation und zur neuesten Version finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).
- Bei Verwendung von PowerShell wird das Modul [Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) oder höher benötigt.
- Wenn Sie eine verwaltete Instanz mit der Azure CLI, mit PowerShell oder mit der REST-API bereitstellen, müssen zunächst ein virtuelles Netzwerk und ein Subnetz erstellt werden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks für Azure SQL Managed Instance](../managed-instance/virtual-network-subnet-create-arm-template.md).

## <a name="permissions"></a>Berechtigungen

Um einen logischen Server oder eine verwaltete Instanz bereitstellen zu können, benötigen Sie die entsprechenden Berechtigungen zum Erstellen dieser Ressourcen. Azure-Benutzer mit höherem Berechtigungsgrad, z. B. [Besitzer](../../role-based-access-control/built-in-roles.md#owner), [Mitwirkende](../../role-based-access-control/built-in-roles.md#contributor), [Dienstadministratoren](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) und [Co-Admins](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) von Abonnements, sind zum Erstellen eines SQL-Servers oder einer verwalteten Instanz berechtigt. Verwenden Sie zum Erstellen dieser Ressourcen mit der Azure RBAC-Rolle mit den geringstmöglichen Rechten die Rolle [Mitwirkender von SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) für SQL-Datenbank und [Mitwirkender für verwaltete SQL-Instanzen](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) für eine verwaltete Instanz.

Die Azure RBAC-Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) verfügt nicht über einen ausreichend hohen Berechtigungsgrad für die Erstellung eines Servers oder einer Instanz mit aktivierter reiner Azure AD-Authentifizierung. Die Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) ist erforderlich, um das Feature für die reine Azure AD-Authentifizierung nach der Erstellung des Servers bzw. der Instanz verwalten zu können.

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>Durchführen der Bereitstellung mit aktivierter reiner Azure AD-Authentifizierung

Der folgende Abschnitt enthält Beispiele und Skripts zur Erstellung eines logischen Servers oder einer verwalteten Instanz, wobei ein Azure AD-Administrator für den Server bzw. die Instanz festgelegt ist und während der Servererstellung die reine Azure AD-Authentifizierung aktiviert wird. Weitere Informationen zu diesem Feature finden Sie unter [Reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md).

In unseren Beispielen aktivieren wir die reine Azure AD-Authentifizierung während der Erstellung eines Servers oder einer verwalteten Instanz mit einem systemseitig zugewiesenen Serveradministrator und Kennwort. Hierdurch wird der Zugriff des Serveradministrators verhindert, wenn die reine Azure AD-Authentifizierung aktiviert ist, sodass nur der Azure AD-Administrator auf die Ressource zugreifen kann. Optional können Sie den APIs Parameter hinzufügen, um während der Servererstellung Ihren eigenen Serveradministrator und Ihr Kennwort einzubeziehen. Das Kennwort kann aber erst zurückgesetzt werden, nachdem Sie die reine Azure AD-Authentifizierung deaktiviert haben.

Sie sollten andere verfügbare APIs verwenden, um die vorhandenen Eigenschaften nach der Erstellung des Servers oder der verwalteten Instanz zu ändern. Weitere Informationen finden Sie unter [Verwalten der reinen Azure AD-Authentifizierung mithilfe von APIs](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis) und [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](authentication-aad-configure.md).

> [!NOTE]
> Wenn für die reine Azure AD-Authentifizierung „false“ festgelegt ist (Standardeinstellung), muss bei der Erstellung des Servers oder der verwalteten Instanz in allen APIs ein Serveradministrator mit zugehörigem Kennwort angegeben werden.

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Der Azure CLI-Befehl `az sql server create` wird verwendet, um einen neuen logischen Server bereitzustellen. Mit dem unten angegebenen Befehl wird ein neuer Server bereitgestellt, für den die reine Azure AD-Authentifizierung aktiviert ist.

Die SQL-Administratoranmeldung für den Server wird automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Servererstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator für den Server ist das Konto, das Sie für `<AzureADAccount>` festgelegt haben, und hierüber kann der Server verwaltet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`
- `<AzureADAccountSID>`: Die Azure AD-Objekt-ID für den Benutzer.
- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihren logischen Server
- `<ServerName>`: Verwenden Sie einen eindeutigen Namen für den Server.

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

Weitere Informationen finden Sie unter [az sql server create](/cli/azure/sql/server#az_sql_server_create).

Informationen zum Überprüfen des Serverstatus nach der Erstellung finden Sie unter dem folgenden Befehl:

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Der PowerShell-Befehl `New-AzSqlServer` wird verwendet, um einen neuen logischen Azure SQL-Server bereitzustellen. Mit dem unten angegebenen Befehl wird ein neuer Server bereitgestellt, für den die reine Azure AD-Authentifizierung aktiviert ist. 

Die SQL-Administratoranmeldung für den Server wird automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Servererstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator für den Server ist das Konto, das Sie für `<AzureADAccount>` festgelegt haben, und hierüber kann der Server verwaltet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihren logischen Server
- `<Location>`: Standort des Servers, z. B. `West US` oder `Central US`.
- `<ServerName>`: Verwenden Sie einen eindeutigen Namen für den Server.
- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

Weitere Informationen finden Sie unter [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

Die REST-API zum [Erstellen oder Aktualisieren von Servern (Servers: Create Or Update)](/rest/api/sql/2020-11-01-preview/servers/create-or-update) kann für die Erstellung eines logischen Servers verwendet werden, wobei während der Bereitstellung die reine Azure AD-Authentifizierung aktiviert wird. 

Mit dem unten angegebenen Skript wird ein logischer Server bereitgestellt, der Azure AD-Administrator als `<AzureADAccount>` festgelegt und die reine Azure AD-Authentifizierung aktiviert. Die SQL-Administratoranmeldung für den Server wird ebenfalls automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Bereitstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator `<AzureADAccount>` kann nach Abschluss der Bereitstellung zum Verwalten des Servers verwendet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<tenantId>`: Kann ermittelt werden, indem Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrer **Azure Active Directory**-Ressource navigieren. Im Bereich **Übersicht** sollte Ihre **Mandanten-ID** angezeigt werden.
- `<subscriptionId>`: Ihre Abonnement-ID können Sie über das Azure-Portal ermitteln.
- `<ServerName>`: Verwenden Sie einen eindeutigen Namen für den Server.
- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihren logischen Server
- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`
- `<Location>`: Standort des Servers, z. B. `westus2` oder `centralus`.
- `<objectId>`: Kann ermittelt werden, indem Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrer **Azure Active Directory**-Ressource navigieren. Suchen Sie im Bereich **Benutzer** nach dem Azure AD-Benutzer und der zugehörigen **Objekt-ID**.

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

Sie können das folgende Skript verwenden, um den Serverstatus zu überprüfen:

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm-template)

Weitere Informationen und ARM-Vorlagen finden Sie unter [Azure Resource Manager-Vorlagen für Azure SQL-Datenbank und verwaltete SQL-Instanzen](arm-templates-content-guide.md).

Informationen zum Bereitstellen eines logischen Servers, für den ein Azure AD-Administrator festgelegt und die reine Azure AD-Authentifizierung aktiviert ist, mithilfe einer ARM-Vorlage finden Sie in unserer Schnellstartvorlage für [logische Azure SQL-Server mit reiner Azure AD-Authentifizierung](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth).

Sie können auch die folgende Vorlage verwenden. Verwenden Sie eine [benutzerdefinierte Bereitstellung im Azure-Portal](https://portal.azure.com/#create/Microsoft.Template), und **erstellen Sie im Editor Ihre eigene Vorlage**. **Speichern** Sie als Nächstes die Konfiguration, nachdem Sie das Beispiel eingefügt haben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Der Azure CLI-Befehl `az sql mi create` wird verwendet, um eine neue Azure SQL Managed Instance bereitzustellen. Mit dem unten angegebenen Befehl wird eine neue verwaltete Instanz bereitgestellt, für die die reine Azure AD-Authentifizierung aktiviert ist.

> [!NOTE]
> Für das Skript müssen als Voraussetzung ein virtuelles Netzwerk und ein Subnetz erstellt werden.

Die SQL-Administratoranmeldung für die verwaltete Instanz wird automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Bereitstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator ist das Konto, das Sie für `<AzureADAccount>` festgelegt haben, und kann nach Abschluss der Bereitstellung zum Verwalten der Instanz verwendet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`
- `<AzureADAccountSID>`: Die Azure AD-Objekt-ID für den Benutzer.
- `<managedinstancename>`: Name der verwalteten Instanz, die Sie erstellen möchten.
- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihre verwaltete Instanz. Die Ressourcengruppe sollte auch das erstellte virtuelle Netzwerk und das Subnetz enthalten.
- Der Parameter `subnet` muss mit den Angaben für `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` und `<SubnetName>` aktualisiert werden. Ihre Abonnement-ID können Sie über das Azure-Portal ermitteln.

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

Weitere Informationen finden Sie unter [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Der PowerShell-Befehl `New-AzSqlInstance` wird verwendet, um eine neue Azure SQL Managed Instance bereitzustellen. Mit dem unten angegebenen Befehl wird eine neue verwaltete Instanz bereitgestellt, für die die reine Azure AD-Authentifizierung aktiviert ist.

> [!NOTE]
> Für das Skript müssen als Voraussetzung ein virtuelles Netzwerk und ein Subnetz erstellt werden.

Die SQL-Administratoranmeldung für die verwaltete Instanz wird automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Bereitstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator ist das Konto, das Sie für `<AzureADAccount>` festgelegt haben, und kann nach Abschluss der Bereitstellung zum Verwalten der Instanz verwendet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<managedinstancename>`: Name der verwalteten Instanz, die Sie erstellen möchten.
- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihre verwaltete Instanz. Die Ressourcengruppe sollte auch das erstellte virtuelle Netzwerk und das Subnetz enthalten.
- `<Location>`: Standort des Servers, z. B. `West US` oder `Central US`.
- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`
- Der Parameter `SubnetId` muss mit den Angaben für `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` und `<SubnetName>` aktualisiert werden. Ihre Abonnement-ID können Sie über das Azure-Portal ermitteln.


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

Weitere Informationen finden Sie unter [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

Die REST-API zum [Erstellen oder Aktualisieren von verwalteten Instanzen (Managed Instances – Create Or Update)](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) kann zum Erstellen einer verwalteten Instanz verwendet werden, wobei während der Bereitstellung die reine Azure AD-Authentifizierung aktiviert ist.

> [!NOTE]
> Für das Skript müssen als Voraussetzung ein virtuelles Netzwerk und ein Subnetz erstellt werden.

Mit dem unten angegebenen Skript wird eine verwaltete Instanz bereitgestellt, der Azure AD-Administrator als `<AzureADAccount>` festgelegt und die reine Azure AD-Authentifizierung aktiviert. Die SQL-Administratoranmeldung für die Instanz wird ebenfalls automatisch erstellt, und das Kennwort wird nach dem Zufallsprinzip festgelegt. Da die Herstellung der Konnektivität für die SQL-Authentifizierung bei dieser Bereitstellung deaktiviert ist, wird die SQL-Administratoranmeldung nicht verwendet.

Der Azure AD-Administrator `<AzureADAccount>` kann nach Abschluss der Bereitstellung zum Verwalten der Instanz verwendet werden.

Ersetzen Sie im Beispiel die folgenden Werte:

- `<tenantId>`: Kann ermittelt werden, indem Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrer **Azure Active Directory**-Ressource navigieren. Im Bereich **Übersicht** sollte Ihre **Mandanten-ID** angezeigt werden.
- `<subscriptionId>`: Ihre Abonnement-ID können Sie über das Azure-Portal ermitteln.
- `<instanceName>`: Verwenden Sie einen eindeutigen Namen für die verwaltete Instanz.
- `<ResourceGroupName>`: Name der Ressourcengruppe für Ihren logischen Server
- `<AzureADAccount>`: Kann ein Azure AD-Benutzer bzw. eine -Gruppe sein. Beispiel: `DummyLogin`
- `<Location>`: Standort des Servers, z. B. `westus2` oder `centralus`.
- `<objectId>`: Kann ermittelt werden, indem Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrer **Azure Active Directory**-Ressource navigieren. Suchen Sie im Bereich **Benutzer** nach dem Azure AD-Benutzer und der zugehörigen **Objekt-ID**.
- Der Parameter `subnetId` muss mit den Angaben für `<ResourceGroupName>`, `Subscription ID`, `<VNetName>` und `<SubnetName>` aktualisiert werden.


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

Führen Sie den Befehl `GET` aus, um die Ergebnisse zu überprüfen:

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm-template)

Verwenden Sie die folgende Vorlage, um eine neue verwaltete Instanz, ein virtuelles Netzwerk und ein Subnetz bereitzustellen, wobei für die Instanz ein Azure AD-Administrator festgelegt und die reine Azure AD-Authentifizierung aktiviert ist.

Verwenden Sie eine [benutzerdefinierte Bereitstellung im Azure-Portal](https://portal.azure.com/#create/Microsoft.Template), und **erstellen Sie im Editor Ihre eigene Vorlage**. **Speichern** Sie als Nächstes die Konfiguration, nachdem Sie das Beispiel eingefügt haben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>Gewähren von Berechtigungen der Art „Verzeichnisleseberechtigte“

Nachdem die Bereitstellung für Ihre verwaltete Instanz abgeschlossen ist, bemerken Sie ggf., dass die verwaltete Instanz für den Zugriff auf Azure Active Directory Berechtigungen vom Typ **Lesen** benötigt. Leseberechtigungen können gewährt werden, indem im Azure-Portal von einer Person mit ausreichenden Berechtigungen auf die angezeigte Meldung geklickt wird. Weitere Informationen finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="Screenshot: Active Directory-Administratormenü im Azure-Portal mit erforderlichen Leseberechtigungen":::

## <a name="limitations"></a>Einschränkungen

- Die Erstellung eines Servers oder einer Instanz mit dem Azure-Portal, wobei während der Bereitstellung die reine Azure AD-Authentifizierung aktiviert ist, wird derzeit nicht unterstützt.
- Damit das Kennwort des Serveradministrators zurückgesetzt werden kann, muss die reine Azure AD-Authentifizierung deaktiviert werden.
- Wenn die reine Azure AD-Authentifizierung deaktiviert ist, müssen Sie bei Verwendung aller APIs einen Server mit einem Serveradministrator und einem Kennwort erstellen.

## <a name="next-steps"></a>Nächste Schritte

- Falls Sie bereits über einen SQL-Server oder eine verwaltete Instanz verfügen und nur die reine Azure AD-Authentifizierung aktivieren möchten, helfen Ihnen die Informationen unter [Tutorial: Aktivieren der reinen Azure Active Directory-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication-tutorial.md) weiter.
- Weitere Informationen zum Feature für die reine Azure AD-Authentifizierung finden Sie unter [Reine Azure AD-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication.md).
- Wenn Sie die Servererstellung mit aktivierter reiner Azure AD-Authentifizierung erzwingen möchten, lesen Sie unter [Azure Policy für die reine Azure Active Directory-Authentifizierung mit Azure SQL](authentication-azure-ad-only-authentication-policy.md) nach.