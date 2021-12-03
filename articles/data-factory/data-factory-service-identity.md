---
title: Verwaltete Identität
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie mehr über die Verwendung verwalteter Identitäten in Azure Data Factory und Azure Synapse.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: b775c3873e28fbd6f59b8686800ccf733fb34ae6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457903"
---
# <a name="managed-identity-for-azure-data-factory-and-azure-synapse"></a>Verwaltete Identität für Azure Data Factory und Azure Synapse 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält grundlegende Informationen zur verwalteten Identität, die ehemals als verwaltete Dienstidentität (Managed Service Identity, MSI) bezeichnet wurde, sowie zu ihrer Funktionsweise in Azure Data Factory und Azure Synapse.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht

Dank verwalteter Identitäten müssen keine Anmeldeinformationen mehr verwaltet werden. Verwaltete Identitäten stellen eine Identität für die Dienstinstanz bereit, wenn eine Verbindung mit Ressourcen hergestellt wird, die die Azure AD-Authentifizierung (Azure Active Directory) unterstützen. Der Dienst kann beispielsweise eine verwaltete Identität verwenden, um auf Ressourcen wie [Azure Key Vault](../key-vault/general/overview.md) zuzugreifen. Dort können Datenadministratoren auf sichere Weise Anmeldeinformationen speichern oder auf Speicherkonten zugreifen. Der Dienst verwendet die verwaltete Identität, um Azure AD-Token zu beziehen.

Es werden zwei Arten von verwalteten Identitäten unterstützt: 

- **Systemseitig zugewiesen:** Sie können eine verwaltete Identität direkt für eine Dienstinstanz aktivieren. Wenn Sie bei der Diensterstellung eine systemseitig zugewiesene verwaltete Identität zulassen, wird in Azure AD eine Identität erstellt, die an den Lebenszyklus dieser Dienstinstanz gebunden ist. Entwurfsbedingt kann nur diese Azure-Ressource diese Identität zum Anfordern von Token von Azure AD verwenden. Daher löscht Azure automatisch die Identität, wenn die Ressource gelöscht wird. Azure Synapse Analytics setzt voraus, dass zusammen mit dem Synapse-Arbeitsbereich eine systemseitig zugewiesene verwaltete Identität erstellt wird.
- **Benutzerseitig zugewiesen**: Sie können eine verwaltete Identität auch als eigenständige Azure-Ressource erstellen. Sie können eine [benutzerseitig zugewiesene verwaltete Identität erstellen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) und diese einer oder mehreren Instanzen einer Data Factory oder eines Synapse-Arbeitsbereichs zuweisen. Bei benutzerseitig zugewiesenen verwalteten Identitäten wird die Identität getrennt von den Ressourcen verwaltet, für die sie verwendet wird.

Eine verwaltete Identität hat folgende Vorteile:

- [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md): In diesem Fall wird die verwaltete Identität für die Azure Key Vault-Authentifizierung verwendet.
- Greifen Sie mit verwalteter Identitätsauthentifizierung auf Datenspeicher oder Berechnungen zu, einschließlich Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, Databricks-Aktivität, Web-Aktivität und mehr. Überprüfen Sie die Anschluss- und Aktivitätsartikel für Details.
- Verwaltete Identitäten werden auch zum Verschlüsseln/Entschlüsseln von Daten und Metadaten mithilfe des kundenseitig verwalteten Schlüssels verwendet, der in Azure Key Vault gespeichert ist, was eine Mehrfachverschlüsselung ermöglicht. 

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität 

>[!NOTE]
> Aus Gründen der Abwärtskompatibilität wird die systemseitig zugewiesene verwaltete Identität an anderer Stelle der Dokumentation sowie auf der Benutzeroberfläche von Data Factory Studio und Synapse Studio auch als „verwaltete Identität“ bezeichnet. Der Begriff „Benutzerseitig zugewiesene verwaltete Identität“ wird explizit verwendet, wenn darauf Bezug genommen wird. 

### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> Generieren einer systemseitig zugewiesenen verwalteten Identität

Die systemseitig zugewiesene verwaltete Identität wird wie folgt generiert:

- Bei der Erstellung einer Data Factory oder eines Data Factory-Arbeitsbereichs per **Azure-Portal oder PowerShell** wird die verwaltete Identität stets automatisch erstellt.
- Wenn Sie eine Data Factory oder einen Arbeitsbereich per **SDK** erstellen, wird die verwaltete Identität nur erstellt, wenn Sie im Factory-Objekt „Identity = new FactoryIdentity()“ bzw. im Synapse-Arbeitsbereichsobjekt „Identity = new ManagedIdentity“ für die Erstellung angeben.  Ein Beispiel finden Sie unter [Erstellen einer Data Factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Wenn Sie eine Data Factory oder einen Arbeitsbereich per **REST-API** erstellen, wird die verwaltete Identität nur erstellt, wenn Sie im Anforderungstext den Abschnitt „identity“ angeben. Sehen Sie sich das Beispiel unter [REST-Schnellstart – Erstellen einer Data Factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory) an.

Sollte Ihrer Dienstinstanz nach dem Schritt [Abrufen einer verwalteten Identität](#retrieve-managed-identity) keine verwaltete Identität zugeordnet sein, können Sie explizit eine verwaltete Identität generieren, indem Sie die Instanz programmgesteuert mit dem Identitätsinitiator aktualisieren:

- [Generieren der verwalteten Identität mit PowerShell](#generate-system-assigned-managed-identity-using-powershell)
- [Generieren der verwalteten Identität mit der REST-API](#generate-system-assigned-managed-identity-using-rest-api)
- [Generieren der verwalteten Identität mithilfe einer Azure Resource Manager-Vorlage](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [Generieren einer verwalteten Identität mit dem SDK](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- Die verwaltete Identität kann nicht geändert werden. Das Aktualisieren einer Dienstinstanz, die bereits über eine verwaltete Identität verfügt, hat keine Auswirkungen, und die verwaltete Identität bleibt unverändert.
>- Wenn Sie eine Dienstinstanz, die bereits über eine verwaltete Identität verfügt, aktualisieren, ohne im Factory- oder Arbeitsbereichsobjekt den Parameter „identity“ bzw. im REST-Anforderungstext den Abschnitt „identity“ anzugeben, erhalten Sie einen Fehler.
>- Wenn Sie eine Dienstinstanz löschen, wird die zugehörige verwaltete Identität ebenfalls gelöscht.

#### <a name="generate-system-assigned-managed-identity-using-powershell"></a>Generieren einer systemseitig zugewiesenen verwalteten Identität in Azure PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

Rufen Sie den Befehl **Set-AzDataFactoryV2** auf, woraufhin die „Identity“-Felder neu generiert werden:

```powershell
PS C:\> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Rufen Sie den Befehl **New-AzSynapseWorkspace** auf. Daraufhin werden Identitätsfelder neu generiert:

```powershell
PS C:\> $creds = New-Object System.Management.Automation.PSCredential ("ContosoUser", $password)
PS C:\> New-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName> -Location <region> -DefaultDataLakeStorageAccountName <storageAccountName> -DefaultDataLakeStorageFileSystem <fileSystemName> -SqlAdministratorLoginCredential $creds

DefaultDataLakeStorage           : Microsoft.Azure.Commands.Synapse.Models.PSDataLakeStorageAccountDetails
ProvisioningState                : Succeeded
SqlAdministratorLogin            : ContosoUser
VirtualNetworkProfile            :
Identity                         : Microsoft.Azure.Commands.Synapse.Models.PSManagedIdentity
ManagedVirtualNetwork            :
PrivateEndpointConnections       : {}
WorkspaceUID                     : <workspaceUid>
ExtraProperties                  : {[WorkspaceType, Normal], [IsScopeEnabled, False]}
ManagedVirtualNetworkSettings    :
Encryption                       : Microsoft.Azure.Commands.Synapse.Models.PSEncryptionDetails
WorkspaceRepositoryConfiguration :
Tags                             :
TagsTable                        :
Location                         : <region>
Id                               : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/
                                   Microsoft.Synapse/workspaces/<workspaceName>
Name                             : <workspaceName>
Type                             : Microsoft.Synapse/workspaces
```
---

#### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>Generieren einer systemseitig zugewiesenen verwalteten Identität mit der REST-API

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

> [!NOTE]
> Wenn Sie versuchen, eine Dienstinstanz, die bereits über eine verwaltete Identität verfügt, zu aktualisieren, ohne im Factoryobjekt den Parameter **identity** bzw. im REST-Anforderungstext einen Abschnitt vom Typ **identity** anzugeben, erhalten Sie einen Fehler.

Rufen Sie die folgende API mit dem Abschnitt „identity“ im Anforderungstext auf:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Anforderungstext**: Fügen Sie Folgendes hinzu: „identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Antwort:** Die verwaltete Identität wird automatisch erstellt, und der Abschnitt „identity“ wird entsprechend aufgefüllt.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

> [!NOTE]
> Wenn Sie versuchen, eine Dienstinstanz, die bereits über eine verwaltete Identität verfügt, zu aktualisieren, ohne im Arbeitsbereichsobjekt den Parameter **identity** bzw. im REST-Anforderungstext einen Abschnitt vom Typ **identity** anzugeben, erhalten Sie einen Fehler.

Rufen Sie die folgende API mit dem Abschnitt „identity“ im Anforderungstext auf:

```
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Anforderungstext**: Fügen Sie Folgendes hinzu: „identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<workspaceName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Antwort:** Die verwaltete Identität wird automatisch erstellt, und der Abschnitt „identity“ wird entsprechend aufgefüllt.

```json
{
    "name": "<workspaceName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2021-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Synapse/workspaces/<workspaceName>",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>"
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Generieren einer systemseitig zugewiesenen verwalteten Identität mithilfe einer Azure Resource Manager-Vorlage

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**Vorlage**: Fügen Sie Folgendes hinzu: "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
**Vorlage**: Fügen Sie Folgendes hinzu: "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<workspaceName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Synapse/workspaces",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-sdk"></a>Generieren einer systemseitig zugewiesenen verwalteten Identität mit dem SDK

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Rufen Sie die Funktion zum Erstellen oder Aktualisieren mit „Identity=new FactoryIdentity()“ auf. Beispielcode unter Verwendung von .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
```csharp
Workspace workspace = new Workspace
{
    Identity = new ManagedIdentity
    {
        Type = ResourceIdentityType.SystemAssigned
    },
    DefaultDataLakeStorage = new DataLakeStorageAccountDetails
    {
        AccountUrl = <defaultDataLakeStorageAccountUrl>,
        Filesystem = <DefaultDataLakeStorageFilesystem>
    },
    SqlAdministratorLogin = <SqlAdministratorLoginCredentialUserName>
    SqlAdministratorLoginPassword = <SqlAdministratorLoginCredentialPassword>,
    Location = <region>
};
client.Workspaces.CreateOrUpdate(resourceGroupName, workspaceName, workspace);
```
---

### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> Abrufen einer systemseitig zugewiesenen verwalteten Identität

Sie können die verwaltete Identität über das Azure-Portal oder programmgesteuert abrufen. Die folgenden Abschnitte enthalten einige Beispiele.

>[!TIP]
> Sollte die verwaltete Identität nicht angezeigt werden, können Sie durch Aktualisieren der Dienstinstanz die [verwaltete Identität generieren](#generate-managed-identity).

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Abrufen einer systemseitig zugewiesenen verwalteten Identität über das Azure-Portal

Die Informationen zur verwalteten Identität finden Sie, indem Sie im Azure-Portal zur Ihrer Data Factory bzw. zu Ihrem Synapse-Arbeitsbereich navigieren und sich die Eigenschaften ansehen.

- Objekt-ID der verwalteten Identität
- Mandant der verwalteten Identität (nur relevant für Azure Data Factory)

Die Informationen zur verwalteten Identität werden auch angezeigt, wenn Sie einen verknüpften Dienst erstellen, der die Authentifizierung der verwalteten Identität unterstützt, z. B. Azure-Blob, Azure Data Lake Storage, Azure Key Vault usw.

Wenn Sie eine Berechtigung erteilen, wählen Sie in der Azure-Ressource auf der Registerkarte Zugriffskontrolle (IAM) -> Rollenzuweisung hinzufügen -> Zugriff zuweisen zu -> wählen Sie Data Factory unter System zugewiesene verwaltete Identität -> wählen Sie nach Fabrikname; oder im Allgemeinen können Sie die Objekt-ID oder den Datenfabriknamen (als verwalteten Identitätsnamen) verwenden, um diese Identität zu finden. Wenn Sie die Anwendungs-ID der verwalteten Identität abrufen müssen, können Sie PowerShell verwenden.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>Abrufen einer systemseitig zugewiesenen verwalteten Identität über Azure PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Die Prinzipal-ID und Mandanten-ID der verwalteten Identität werden wie folgt zurückgegeben, wenn Sie eine bestimmte Dienstinstanz abrufen. Verwenden Sie die **PrincipalId**, um Zugriff zu gewähren:

```powershell
PS C:\> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Sie können die Anwendungs-ID abrufen, indem Sie die vorstehende Prinzipal-ID kopieren und dann den unten gezeigten Azure Active Directory Befehl mit der Prinzipal-ID als Parameter ausführen.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
Die Prinzipal-ID und Mandanten-ID der verwalteten Identität werden wie folgt zurückgegeben, wenn Sie eine bestimmte Dienstinstanz abrufen. Verwenden Sie die **PrincipalId**, um Zugriff zu gewähren:

```powershell
PS C:\> (Get-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName>).Identity

IdentityType   PrincipalId                          TenantId                            
------------   -----------                          --------                            
SystemAssigned cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Sie können die Anwendungs-ID abrufen, indem Sie die vorstehende Prinzipal-ID kopieren und dann den unten gezeigten Azure Active Directory Befehl mit der Prinzipal-ID als Parameter ausführen.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : <workspaceName>
Id                    : cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05
Type                  : ServicePrincipal
```
---

#### <a name="retrieve-managed-identity-using-rest-api"></a>Abrufen der verwalteten Identität mit der REST-API

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Die Prinzipal-ID und Mandanten-ID der verwalteten Identität werden wie folgt zurückgegeben, wenn Sie eine bestimmte Dienstinstanz abrufen.

Rufen Sie die folgende API in der Anforderung auf:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Antwort**: Sie erhalten eine Antwort wie im nachfolgenden Beispiel gezeigt. Der Abschnitt für die Identität ist entsprechend ausgefüllt.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Fügen Sie zum Abrufen der verwalteten Identität aus einer ARM-Vorlage einen Abschnitt **outputs** im ARM JSON-Code hinzu:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
Die Prinzipal-ID und Mandanten-ID der verwalteten Identität werden wie folgt zurückgegeben, wenn Sie eine bestimmte Dienstinstanz abrufen.

Rufen Sie die folgende API in der Anforderung auf:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Antwort**: Sie erhalten eine Antwort wie im nachfolgenden Beispiel gezeigt. Der Abschnitt für die Identität ist entsprechend ausgefüllt.

```json
{
  "properties": {
    "defaultDataLakeStorage": {
      "accountUrl": "https://exampledatalakeaccount.dfs.core.windows.net",
      "filesystem": "examplefilesystem"
    },
    "encryption": {
      "doubleEncryptionEnabled": false
    },
    "provisioningState": "Succeeded",
    "connectivityEndpoints": {
      "web": "https://web.azuresynapse.net?workspace=%2fsubscriptions%2{subscriptionId}%2fresourceGroups%2f{resourceGroupName}%2fproviders%2fMicrosoft.Synapse%2fworkspaces%2f{workspaceName}",
      "dev": "https://{workspaceName}.dev.azuresynapse.net",
      "sqlOnDemand": "{workspaceName}-ondemand.sql.azuresynapse.net",
      "sql": "{workspaceName}.sql.azuresynapse.net"
    },
    "managedResourceGroupName": "synapseworkspace-managedrg-f77f7cf2-XXXX-XXXX-XXXX-c4cb7ac3cf4f",
    "sqlAdministratorLogin": "sqladminuser",
    "privateEndpointConnections": [],
    "workspaceUID": "e56f5773-XXXX-XXXX-XXXX-a0dc107af9ea",
    "extraProperties": {
      "WorkspaceType": "Normal",
      "IsScopeEnabled": false
    },
    "publicNetworkAccess": "Enabled",
    "cspWorkspaceAdminProperties": {
      "initialWorkspaceAdminObjectId": "3746a407-XXXX-XXXX-XXXX-842b6cf1fbcc"
    },
    "trustedServiceBypassEnabled": false
  },
  "type": "Microsoft.Synapse/workspaces",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}",
  "location": "eastus",
  "name": "{workspaceName}",
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47",
    "principalId": "cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05"
  },
  "tags": {}
}
```

> [!TIP] 
> Fügen Sie zum Abrufen der verwalteten Identität aus einer ARM-Vorlage einen Abschnitt **outputs** im ARM JSON-Code hinzu:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.Synapse/workspaces', parameters('<workspaceName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
---

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

Sie können benutzerseitig zugewiesene verwaltete Identitäten in Azure Active Directory erstellen, löschen und verwalten. Ausführlichere Informationen finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, müssen Sie in Ihrer Dienstinstanz für die benutzerseitig zugewiesene verwaltete Identität zunächst [Anmeldeinformationen erstellen](credentials.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Anmeldeinformationen](credentials.md)

In den folgenden Themen erfahren Sie, wann und wie Sie verwaltete Identitäten verwenden können:

- [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopieren von Daten von/zu Azure Data Lake Store mit verwalteten Identitäten für die Ressourcenauthentifizierung mit Azure](connector-azure-data-lake-store.md)

Weitere Hintergründe zu verwalteten Identitäten für Azure-Ressourcen, auf denen verwaltete Identitäten in Azure Data Factory und Azure Synapse basieren, finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).
