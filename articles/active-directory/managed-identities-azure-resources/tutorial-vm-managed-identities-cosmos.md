---
title: Verwenden verwalteter Identitäten auf einem virtuellen Computer für den Zugriff auf Cosmos DB | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie über das Azure-Portal, die CLI, PowerShell oder eine Azure Resource Manager-Vorlage verwaltete Identitäten mit virtuellen Windows-Computern verwenden.
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: msi
ms.workload: integration
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: barclayn
ms.custom: ep-miar
ms.openlocfilehash: 7ff0ae1578a8b1d637f7baee50ed6d71a63e3996
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070107"
---
# <a name="how-to-use-managed-identities-to-connect-to-cosmos-db-from-an-azure-virtual-machine"></a>Verwenden verwalteter Identitäten zum Herstellen einer Verbindung mit Cosmos DB über einen virtuellen Azure-Computer

In diesem Artikel richten Sie einen virtuellen Computer ein, um mithilfe verwalteter Identitäten eine Verbindung mit Cosmos herzustellen. [Azure Cosmos DB](../../cosmos-db/introduction.md) ist eine vollständig verwaltete NoSQL-Datenbank für die moderne App-Entwicklung. [Verwaltete Identitäten für Azure-Ressourcen](overview.md) ermöglichen Ihren Anwendungen die Authentifizierung, wenn sie auf Dienste zugreifen, die die Azure AD-Authentifizierung mithilfe einer von Azure verwalteten Identität unterstützen.

## <a name="prerequisites"></a>Voraussetzungen

- Grundlegende Kenntnisse im Bereich verwaltete Identitäten. Wenn Sie vor dem Fortfahren mehr über verwaltete Identitäten für Azure-Ressourcen erfahren möchten, sehen Sie sich die [Übersicht](overview.md) über verwaltete Identitäten an.
- Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).
- Sie benötigen unter Umständen [PowerShell](/powershell/azure/new-azureps-module-az) oder die [CLI](/cli/azure/install-azure-cli).
- [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) oder eine andere Entwicklungsumgebung Ihrer Wahl 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie eine Ressourcengruppe namens **mi-test**. Diese Ressourcengruppe wird für alle in diesem Tutorial verwendeten Ressourcen genutzt.

- [Erstellen einer Ressourcengruppe über das Azure-Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- [Erstellen einer Ressourcengruppe mithilfe der CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md#create-resource-groups)
- [Erstellen einer Ressourcengruppe mithilfe von PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md#create-resource-groups)

## <a name="create-an-azure-vm-with-a-managed-identity"></a>Erstellen eines virtuellen Azure-Computers mit einer verwalteten Identität

Für dieses Tutorial benötigen Sie einen virtuellen Azure-Computer (Virtual Machine, VM). Erstellen Sie einen virtuellen Computer mit aktivierter systemseitig zugewiesener verwalteter Identität namens **mi-vm-01**.  Sie können auch [eine benutzerseitig zugewiesene verwaltete Identität](how-manage-user-assigned-managed-identities.md) namens **mi-ua-01** in der zuvor erstellten Ressourcengruppe (**mi-test**) erstellen. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden, können Sie sie während der Erstellung einem virtuellen Computer zuweisen.

### <a name="create-a-vm-with-a-system-assigned-managed-identity"></a>Erstellen eines virtuellen Computers mit einer systemseitig zugewiesenen verwalteten Identität

Zum Erstellen eines virtuellen Azure-Computers, auf dem die systemseitig zugewiesene verwaltete Identität aktiviert ist, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Rollenzuweisungen erforderlich.

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Suchen Sie im **Azure-Portal** nach **Virtuelle Computer**.
- Klicken Sie auf **Erstellen**.
- Geben Sie auf der Registerkarte „Grundlagen“ die erforderlichen Informationen ein.
- Wählen Sie **Next: Disks >** (Weiter: Datenträger >) aus.
- Füllen Sie die Informationen nach Bedarf weiter aus, navigieren Sie auf der Registerkarte **Verwaltung** zum Abschnitt **Identität**, und aktivieren Sie das Kontrollkästchen neben **Systemseitig zugewiesene verwaltete Identität**.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/create-vm-system-assigned-managed-identities.png" alt-text="Abbildung: Aktivieren systemseitig zugewiesener verwalteter Identitäten beim Erstellen eines virtuellen Computers":::

Weitere Informationen finden Sie in der Dokumentation zu virtuellen Azure-Computern:

- [Linux](../../virtual-machines/linux/quick-create-portal.md)
- [Windows](../../virtual-machines/windows/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Mit [New-AZVM](/powershell/module/az.compute/new-azvm) werden Ressourcen erstellt, auf die Sie verweisen, sofern sie noch nicht vorhanden sind. Um einen virtuellen Computer mit aktivierter systemseitig zugewiesener verwalteter Identität zu erstellen, übergeben Sie den Parameter **-SystemAssignedIdentity** wie nachfolgend gezeigt: 


```powershell

New-AzVm `
    -ResourceGroupName "My VM" `
    -Name "My resource group" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -SystemAssignedIdentity
    -OpenPorts 80,3389
```

- [Schnellstart: Erstellen eines virtuellen Windows-Computers in Azure mit PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
- [Schnellstart: Erstellen einer Linux-VM mit PowerShell in Azure](../../virtual-machines/linux/quick-create-powershell.md)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Erstellen Sie einen virtuellen Computer mit [az vm create](/cli/azure/vm/#az_vm_create). Im folgenden Beispiel wird der virtuelle Computer *myVM* mit einer vom System zugewiesenen verwalteten Identität erstellt. Dies wird vom Parameter `--assign-identity` gefordert. Der `--admin-username`-Parameter und der `--admin-password`-Parameter geben den Namen und das Kennwort des Administratorbenutzers für die Anmeldung am virtuellen Computer an. Aktualisieren Sie diese Werte ggf. mit den entsprechenden Werten für Ihre Umgebung: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

- [Erstellen eines virtuellen Linux-Computers mit einer systemseitig zugewiesenen verwalteten Identität](../../virtual-machines/linux/quick-create-cli.md)
- [Erstellen eines virtuellen Windows-Computers mit einer systemseitig zugewiesenen verwalteten Identität](../../virtual-machines/windows/quick-create-cli.md)

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Um die vom System zugewiesene verwaltete Identität zu aktivieren, laden Sie die Vorlage in einem Editor, suchen Sie die gewünschte Ressource `Microsoft.Compute/virtualMachines` im Abschnitt `resources`, und fügen Sie die `"identity"`-Eigenschaft in der gleichen Ebene wie die `"type": "Microsoft.Compute/virtualMachines"`-Eigenschaft hinzu. Verwenden Sie die folgende Syntax:

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

Wenn Sie fertig sind, sollten die folgenden Abschnitte dem Abschnitt `resource` der Vorlage hinzugefügt worden sein. Ihre Vorlage sollte dem folgenden Beispiel ähneln:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('myVM')]",
            "location": "[myResourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

---


### <a name="create-a-vm-with-a-user-assigned-managed-identity"></a>Erstellen eines virtuellen Computers mit einer benutzerseitig zugewiesenen verwalteten Identität

Die folgenden Schritte zeigen, wie Sie einen virtuellen Computer erstellen, für den eine benutzerseitig zugewiesene verwaltete Identität konfiguriert ist.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Derzeit wird das Zuweisen einer systemseitig zugewiesenen verwalteten Identität während der VM-Erstellung im Azure-Portal nicht unterstützt. Es wird empfohlen, einen virtuellen Computer zu erstellen und ihm dann eine benutzerseitig zugewiesene verwaltete Identität zuzuweisen.

[Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md#user-assigned-managed-identity)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie einen virtuellen Windows-Computer mit einer angegebenen systemseitig zugewiesenen verwalteten Identität.

```powershell
New-AzVm `
    -ResourceGroupName "<Your resource group>" `
    -Name "<Your VM name>" `
    -Location "East US" `
    -VirtualNetworkName "<myVnet>" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 80,3389

```

Erstellen Sie einen virtuellen Linux-Computer mit einer angegebenen systemseitig zugewiesenen verwalteten Identität.

```powershell
New-AzVm `
    -Name "<Linux VM name>" `
    -image CentOS
    -ResourceGroupName "<Your resource group>" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -Linux `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 22


```

Die benutzerseitig zugewiesene verwaltete Identität muss unter Verwendung der Ressourcen-ID ([resourceID](how-manage-user-assigned-managed-identities.md
)) angegeben werden. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az vm create --resource-group <MyResourceGroup> --name <myVM> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
```

[Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über die Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Je nach API-Version müssen [verschiedene Schritte](qs-configure-template-windows-vm.md#user-assigned-managed-identity) ausgeführt werden. Wenn Sie apiVersion 2018-06-01 verwenden, werden Ihre benutzerseitig zugewiesenen verwalteten Identitäten im userAssignedIdentities-Wörterbuchformat gespeichert, und der Wert ```<identityName>``` ist der Name einer Variablen, die Sie im Variablenabschnitt Ihrer Vorlage definieren. In der Variablen verweisen Sie auf die benutzerseitig zugewiesene verwaltete Identität, die Sie zuweisen möchten.

```json
    "variables": {
     "identityName": "my-user-assigned" 
        
    },
```

Fügen Sie unter dem resources-Element den folgenden Eintrag hinzu, um Ihrem virtuellen Computer eine benutzerseitig zugewiesene verwaltete Identität zuzuweisen. Achten Sie darauf, dass Sie ```<identityName>``` durch den Namen der vom Benutzer zugewiesenen verwalteten Identität ersetzen, die Sie erstellt haben.

```json

"resources": [
     {
         //other resource provider properties...
         "apiVersion": "2018-06-01",
         "type": "Microsoft.Compute/virtualMachines",
         "name": "[variables('vmName')]",
         "location": "[resourceGroup().location]",
         "identity": {
             "type": "userAssigned",
             "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<identityName>'))]": {}
             }
         }
     }
 ]
```

---

## <a name="create-a-cosmos-db-account"></a>Erstellen eines Cosmos DB-Kontos

Sie verfügen nun über einen virtuellen Computer mit einer benutzerseitig zugewiesenen verwalteten Identität oder einer systemseitig zugewiesenen verwalteten Identität. Jetzt benötigen Sie ein Cosmos DB-Konto, für das Sie Administratorrechte besitzen. Wenn Sie für dieses Tutorial ein Cosmos DB-Konto erstellen müssen, finden Sie in der [Schnellstartanleitung zu Cosmos DB](../..//cosmos-db/sql/create-cosmosdb-resources-portal.md) ausführliche Schritte dazu.

>[!NOTE]
> Verwaltete Identitäten können für den Zugriff auf alle Azure-Ressourcen verwendet werden, die die Azure Active Directory-Authentifizierung unterstützen. In diesem Tutorial wird davon ausgegangen, dass Ihr Cosmos DB-Konto wie unten dargestellt konfiguriert wird:

 |Einstellung|Wert|BESCHREIBUNG |
   |---|---|---|
   |Subscription|Abonnementname|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos-Konto verwenden möchten. |
   |Ressourcengruppe|Ressourcengruppenname|Wählen Sie **mi-test** oder **Neu erstellen** aus, und geben Sie einen eindeutigen Namen für die neue Ressourcengruppe ein. |
   |Kontoname|Ein eindeutiger Name|Geben Sie einen Namen ein, der Ihr Azure Cosmos-Konto identifiziert. Da *documents.azure.com* an den Namen angefügt wird, die Sie für die URI-Erstellung angeben, muss der Name eindeutig sein.<br><br>Der Name darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss 3 bis 44 Zeichen umfassen.|
   |API|Der Typ des zu erstellenden Kontos|Wählen Sie **Core (SQL)** aus, um eine Dokumentdatenbank und eine Abfrage mit SQL-Syntax zu erstellen. <br><br>[Weitere Informationen zur SQL-API](../../cosmos-db/introduction.md)|
   |Standort|Die Region, die Ihren Benutzern am nächsten liegt|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie den Standort, der Ihren Benutzern am nächsten ist, damit sie möglichst schnell auf die Daten zugreifen können.|

   > [!NOTE]
   > Wenn Sie Tests durchführen, sollten Sie den Rabatt für den Free-Tarif von Azure Cosmos DB anwenden. Mit dem Azure Cosmos DB-Tarif „Free“ erhalten Sie die ersten 1000 RUs/Sek. sowie 25 GB Speicher kostenlos in einem Konto. Weitere Informationen zum [Tarif „Free“](https://azure.microsoft.com/pricing/details/cosmos-db/) Beachten Sie, dass diese Auswahl für dieses Tutorial keinen Unterschied macht.

## <a name="grant-access"></a>Gewähren von Zugriff

An diesem Punkt sollte sowohl ein virtueller Computer, der mit einer verwalteten Identität konfiguriert ist, als auch ein Cosmos DB-Konto vorhanden sein. Bevor Sie fortfahren, müssen Sie der verwalteten Identität verschiedene Rollen zuweisen.

- Gewähren Sie zunächst mithilfe der [Azure RBAC](../../cosmos-db/role-based-access-control.md) Zugriff auf die Cosmos-Verwaltungsebene. Der verwalteten Identität muss die Rolle „DocumentDB-Kontomitwirkender“ zugewiesen sein, damit Datenbanken und Container erstellt werden können.

- Darüber hinaus müssen Sie der verwalteten Identität mithilfe der [Cosmos RBAC](../../cosmos-db/how-to-setup-rbac.md) die Rolle „Mitwirkender“ zuweisen. Die genauen Schritte finden Sie weiter unten. 

> [!NOTE] 
> Sie verwenden die Rolle **Integrierter Mitwirkender an Cosmos DB-Daten**. Zum Gewähren des Zugriffs müssen Sie die Rollendefinition der Identität zuordnen. In diesem Fall ist dies die verwaltete Identität, die dem virtuellen Computer zugeordnet ist.

# <a name="portal"></a>[Portal](#tab/azure-portal)

**Derzeit steht im Azure-Portal keine Option zur Rollenzuweisung zur Verfügung.**


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>" 
$contributorRoleDefinitionId = "00000000-0000-0000-0000-000000000002" # This is the ID of the Cosmos DB Built-in Data contributor role definition
$principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $contributorRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

Nach Abschluss der Rollenzuweisung sollten ungefähr die folgenden Ergebnisse angezeigt werden:

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/results-role-assignment.png" alt-text="Ergebnisse der Rollenzuweisung":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli

resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '00000000-0000-0000-0000-000000000002' # This is the ID of the Cosmos DB Built-in Data contributor role definition
principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId

```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

```JSON
{
  "id": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleAssignments/00000000-0000-0000-0000-000000000002",
  "name": "myRoleAssignmentId",
  "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments",
  "properties": {
    "roleDefinitionId": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleDefinitions/00000000-0000-0000-0000-000000000002",
    "scope": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/dbs/purchases/colls/redmond-purchases",
    "principalId": "myPrincipalId"
  }
}

```

---

## <a name="access-data"></a>Zugreifen auf Daten

Der Zugriff auf Cosmos unter Verwendung von verwalteten Identitäten kann mithilfe der Bibliothek „Azure.identity“ erfolgen, um die Authentifizierung in Ihrer Anwendung zu aktivieren. Sie können [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) direkt aufrufen oder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) verwenden.

Die ManagedIdentityCredential-Klasse versucht die Authentifizierung mithilfe einer verwalteten Identität, die der Bereitstellungsumgebung zugewiesen ist. Die Klasse [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme) durchläuft der Reihe nach verschiedene Authentifizierungsoptionen. Als zweite Authentifizierungsoption verwendet DefaultAzureCredential verwaltete Identitäten. 

Im folgenden Beispiel erstellen Sie eine Datenbank, einen Container und ein Element im Container und lesen das neu erstellte Element mithilfe der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers ein. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, müssen Sie die benutzerseitig zugewiesene verwaltete Identität angeben. Geben Sie dazu die Client-ID der verwalteten Identität an. 

```csharp
string userAssignedClientId = "<your managed identity client Id>";
var tokenCredential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
```

Um das folgende Beispiel verwenden zu können, benötigen Sie die folgenden NuGet-Pakete:

- Azure.Identity
- Microsoft.Azure.Cosmos
- Microsoft.Azure.Management.CosmosDB

Zusätzlich zu den oben genannten NuGet-Paketen müssen Sie **Vorabversion einbeziehen** aktivieren und dann **Azure.ResourceManager.CosmosDB** hinzufügen. 

```csharp
using Azure.Identity;
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;
using Microsoft.Azure.Cosmos;
using System;
using System.Threading.Tasks;

namespace MITest
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var subscriptionId = "Your subscription ID";
            var resourceGroupName = "You resource group";
            var accountName = "Cosmos DB Account name";
            var databaseName = "mi-test";
            var containerName = "container01";

            var tokenCredential = new DefaultAzureCredential();

            // create the management clientSS
            var managementClient = new CosmosDBManagementClient(subscriptionId, tokenCredential);

            // create the data client
            var dataClient = new CosmosClient("https://[Account].documents.azure.com:443/", tokenCredential);

            // create a new database 
            var createDatabaseOperation = await managementClient.SqlResources.StartCreateUpdateSqlDatabaseAsync(resourceGroupName, accountName, databaseName,
                new SqlDatabaseCreateUpdateParameters(new SqlDatabaseResource(databaseName), new CreateUpdateOptions()));
            await createDatabaseOperation.WaitForCompletionAsync();

            // create a new container
            var createContainerOperation = await managementClient.SqlResources.StartCreateUpdateSqlContainerAsync(resourceGroupName, accountName, databaseName, containerName,
                new SqlContainerCreateUpdateParameters(new SqlContainerResource(containerName), new CreateUpdateOptions()));
            await createContainerOperation.WaitForCompletionAsync();


            // create a new item 
            var partitionKey = "pkey";
            var id = Guid.NewGuid().ToString();
            await dataClient.GetContainer(databaseName, containerName)
                .CreateItemAsync(new { id = id, _partitionKey = partitionKey }, new PartitionKey(partitionKey));


            // read back the item
            var pointReadResult = await dataClient.GetContainer(databaseName, containerName)
                .ReadItemAsync<dynamic>(id, new PartitionKey(partitionKey));


            // run a query
            await dataClient.GetContainer(databaseName, containerName)
                .GetItemQueryIterator<dynamic>("SELECT * FROM c")
                .ReadNextAsync();
        }
    }
}

```

Sprachspezifische Beispiele mit ManagedIdentityCredential:

### <a name="net"></a>.NET

Initialisieren Sie Ihren Cosmos DB-Client:

```csharp
CosmosClient client = new CosmosClient("<account-endpoint>", new ManagedIdentityCredential());
```

[Lesen und schreiben Sie dann Daten.](../../cosmos-db/sql/sql-api-dotnet-v3sdk-samples.md)

### <a name="java"></a>Java

Initialisieren Sie Ihren Cosmos DB-Client:

```java
CosmosAsyncClient Client = new CosmosClientBuilder().endpoint("<account-endpoint>") .credential(new ManagedIdentityCredential()) .build();
```

Lesen und schreiben Sie dann Daten, wie in [diesen Beispielen](../../cosmos-db/sql/sql-api-java-sdk-samples.md) beschrieben.

### <a name="javascript"></a>JavaScript

Initialisieren Sie Ihren Cosmos DB-Client:

```javascript
const client = new CosmosClient({ "<account-endpoint>", aadCredentials: new ManagedIdentityCredential() });
```

Lesen und schreiben Sie dann Daten, wie in [diesen Beispielen](../../cosmos-db/sql/sql-api-nodejs-samples.md) beschrieben.

## <a name="clean-up-steps"></a>Bereinigungsschritte

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Portal](https://portal.azure.com) die Ressource aus, die Sie löschen möchten.

1. Klicken Sie auf **Löschen**.

1. Bestätigen Sie den Löschvorgang, wenn Sie dazu aufgefordert werden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Abschnitt wurde bewusst leer gelassen.

---

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über verwaltete Identitäten für Azure-Ressourcen:

- [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md)
- [Azure-Ressourcen-Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates)

Informieren Sie sich ausführlicher über Azure Cosmos DB:

- [Azure Cosmos DB-Ressourcenmodell](../../cosmos-db/account-databases-containers-items.md)
- [Tutorial: Erstellen einer .NET-Konsolen-App zum Verwalten von Daten in einem Azure Cosmos DB-SQL-API-Konto](../../cosmos-db/sql/sql-api-get-started.md)
