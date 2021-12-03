---
title: 'Schnellstart: Tabellen-API mit .NET: Azure Cosmos DB'
description: Dieser Schnellstart zeigt, wie Sie aus einer .NET-Anwendung unter Verwendung des Azure.Data.Tables-SDK auf die Tabellen-API von Azure Cosmos DB zugreifen können.
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/26/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: e76341f8d3d0e7b7c67be6a19ab3fb8c5b902794
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324173"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Schnellstart: Erstellen einer Tabellen-API-App per .NET SDK und Azure Cosmos DB

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Dieser Schnellstart zeigt, wie Sie aus einer .NET-Anwendung auf die [Tabellen-API](introduction.md) von Azure Cosmos DB zugreifen können.  Die Tabellen-API von Azure Cosmos DB ist ein schemaloser Datenspeicher, der es Anwendungen ermöglicht, strukturierte NoSQL-Daten in der Cloud zu speichern.  Da Daten in einem schemalosen Entwurf gespeichert werden, werden der Tabelle automatisch neue Eigenschaften (Spalten) hinzugefügt, wenn der Tabelle ein Objekt mit einem neuen Attribut hinzugefügt wird.

.NET-Anwendungen können auf die Tabellen-API von Cosmos DB mit dem NuGet-Paket [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) zugreifen.  Das [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/)-Paket ist eine [.NET Standard 2.0](/dotnet/standard/net-standard)-Bibliothek, die sowohl mit .NET Framework- (4.7.2 und höher) als auch mit .NET Core-Anwendungen (2.0 und höher) funktioniert.

## <a name="prerequisites"></a>Voraussetzungen

Die Beispielanwendung ist in [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1) geschrieben. Die Prinzipien gelten jedoch sowohl für .NET Framework- als auch für .NET Core-Anwendungen.  Sie können [Visual Studio](https://www.visualstudio.com/downloads/), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) oder [Visual Studio Code](https://code.visualstudio.com/) als IDE verwenden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>Beispielanwendung

Die Beispielanwendung für dieses Tutorial kann aus dem Repository [https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet) geklont oder heruntergeladen werden.  Sowohl eine Starter- als auch eine vollständige App sind im Beispielrepository enthalten.

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

Die Beispielanwendung verwendet Wetterdaten als Beispiel, um die Funktionen der Tabellen-API zu zeigen. Objekte, die Wetterbeobachtungen darstellen, werden mithilfe der Tabellen-API gespeichert und abgerufen, einschließlich der Speicherung von Objekten mit zusätzlichen Eigenschaften, um die schemalosen Funktionen der Tabellen-API zu zeigen.

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="Screenshot: Fertiggestellte Anwendung mit Daten, die in einer Cosmos DB-Tabelle mit der Tabellen-API gespeichert werden." lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1\. Erstellen eines Azure Cosmos DB-Kontos

Zunächst müssen Sie ein Tabellen-API-Konto von Cosmos DB erstellen, das die in Ihrer Anwendung verwendeten Tabellen enthält.  Hierfür können Sie das Azure-Portal, die Azure CLI oder Azure PowerShell verwenden.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an, und führen Sie die folgenden Schritte aus, um ein Cosmos DB-Konto zu erstellen.

| Anweisungen    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="Screenshot: Verwenden des Suchfelds in der oberen Toolleiste, um nach Cosmos DB-Konten in Azure zu suchen." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="Screenshot: Position der Schaltfläche „Erstellen“ auf der Kontoseite von Cosmos DB in Azure." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="Screenshot: Option „Azure-Tabelle“ als richtige auszuwählende Option." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-dotnet/create-cosmos-db-acct-4.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="Screenshot: Ausfüllen der Felder auf der Seite zum Erstellen des Cosmos DB-Kontos." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB-Konten werden mit dem Befehl [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) erstellt. Sie müssen die Option `--capabilities EnableTable` zum Aktivieren des Tabellenspeichers in Ihrer Cosmos DB-Instanz verwenden.  Da alle Azure-Ressourcen in einer Ressourcengruppe enthalten sein müssen, erstellt der folgende Codeausschnitt auch eine Ressourcengruppe für das Cosmos DB-Konto.

Cosmos DB-Kontonamen müssen zwischen 3 und 44 Zeichen lang sein und dürfen nur Kleinbuchstaben, Zahlen und das Bindestrichzeichen (-) enthalten.  Cosmos DB-Kontonamen müssen außerdem in Azure eindeutig sein.

Azure CLI-Befehle können in [Azure Cloud Shell](https://shell.azure.com) oder auf einer Arbeitsstation mit [installierter Azure CLI](/cli/azure/install-azure-cli) ausgeführt werden.

Es dauert in der Regel einige Minuten, bis der Vorgang der Erstellung des Cosmos DB-Kontos abgeschlossen ist.

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Cosmos DB-Konten werden mit dem Cmdlet [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) erstellt. Sie müssen die Option `-ApiKind "Table"` zum Aktivieren des Tabellenspeichers in Ihrer Cosmos DB-Instanz verwenden.  Da alle Azure-Ressourcen in einer Ressourcengruppe enthalten sein müssen, erstellt der folgende Codeausschnitt auch eine Ressourcengruppe für das Azure Cosmos DB-Konto.

Azure Cosmos DB-Kontonamen müssen zwischen 3 und 44 Zeichen lang sein und dürfen nur Kleinbuchstaben, Zahlen und das Bindestrichzeichen (-) enthalten.  Azure Cosmos DB-Kontonamen müssen außerdem in Azure eindeutig sein.

Azure PowerShell-Befehle können in [Azure Cloud Shell](https://shell.azure.com) oder auf einer Arbeitsstation mit [installierter Azure PowerShell-Instanz](/powershell/azure/install-az-ps) ausgeführt werden.

Es dauert in der Regel einige Minuten, bis der Vorgang der Erstellung des Cosmos DB-Kontos abgeschlossen ist.

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2\. Erstellen einer Tabelle

Nun müssen Sie eine Tabelle in Ihrem Cosmos DB-Konto erstellen, das von Ihrer Anwendung verwendet wird.  Im Gegensatz zu einer herkömmlichen Datenbank müssen Sie nur den Namen der Tabelle angeben, nicht die Eigenschaften (Spalten) in der Tabelle.  Wenn Daten in die Tabelle geladen werden, werden die Eigenschaften (Spalten) bei Bedarf automatisch erstellt.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Führen Sie im [Azure-Portal](https://portal.azure.com/) die folgenden Schritte aus, um eine Tabelle in Ihrem Cosmos DB-Konto zu erstellen.

| Anweisungen    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](./includes/create-table-dotnet/create-cosmos-table-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="Screenshot: Verwenden des Suchfelds in der oberen Toolleiste, um nach Ihrem Cosmos DB-Konten zu suchen." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](./includes/create-table-dotnet/create-cosmos-table-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="Screenshot: Position der Schaltfläche „Tabelle hinzufügen“." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](./includes/create-table-dotnet/create-cosmos-table-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="Screenshot: Dialogfeld „Neue Tabelle“ für eine Cosmos DB-Tabelle." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Tabellen in Cosmos DB werden mit dem Befehl [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) erstellt.

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Tabellen in Cosmos DB werden mit dem Cmdlet [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) erstellt.

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3\. Abrufen der Cosmos DB-Verbindungszeichenfolge

Für den Zugriff auf Ihre Tabellen in Cosmos DB benötigt Ihre App die Tabellenverbindungszeichenfolge für das CosmosDB-Storage Konto.  Die Verbindungszeichenfolge kann mithilfe des Azure-Portals, der Azure CLI oder mit Azure PowerShell abgerufen werden.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

| Anweisungen    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](./includes/create-table-dotnet/get-cosmos-connection-string-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="Screenshot: Position des Links „Verbindungszeichenfolgen“ auf der Cosmos DB-Seite." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](./includes/create-table-dotnet/get-cosmos-connection-string-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="Screenshot: In Ihrer Anwendung auszuwählende und zu verwendende Verbindungszeichenfolge." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) mit der Option `--type connection-strings`, um die Verbindungszeichenfolge für den primären Tabellenspeicher mithilfe der Azure CLI abzurufen.  Dieser Befehl verwendet eine [JMESPath-Abfrage](https://jmespath.org/), um nur die Verbindungszeichenfolge der primären Tabelle anzuzeigen.

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey), um die Verbindungszeichenfolge für den primären Tabellenspeicher mit Azure PowerShell abzurufen.

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

Die Verbindungszeichenfolge für Ihr Cosmos DB-Konto gilt als App-Geheimnis und muss wie jedes andere App-Geheimnis oder Kennwort geschützt werden.  In diesem Beispiel wird das [Secret Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) verwendet, um die Verbindungszeichenfolge während der Entwicklung zu speichern und für die Anwendung verfügbar zu machen.  Auf das Secret Manager-Tool kann über Visual Studio oder über die .NET CLI zugegriffen werden.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Um das Secret Manager-Tool aus Visual Studio öffnen, klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann im Kontextmenü **Benutzergeheimnisse verwalten** aus.  Dadurch wird die Datei *secrets.json* für das Projekt geöffnet.  Ersetzen Sie den Inhalt der Datei durch den unten angegebenen JSON-Code, und ersetzen Sie die Verbindungszeichenfolge Ihrer Cosmos DB-Tabelle.

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

Um Secret Manager verwenden zu können, müssen Sie das Tool zunächst mit dem Befehl `dotnet user-secrets init` für Ihr Projekt initialisieren.

```dotnetcli
dotnet user-secrets init
```

Verwenden Sie dann den Befehl `dotnet user-secrets set`, um die Verbindungszeichenfolge der Cosmos DB-Tabelle als Geheimnis hinzuzufügen.

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4\. Installieren des NuGet-Pakets „Azure.Data.Tables“

Um aus einer .NET-Anwendung auf die Tabellen-API von Cosmos DB zuzugreifen, installieren Sie das NuGet-Paket [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5\. Konfigurieren des Tabellenclients in „Startup.cs“

Das Azure SDK kommuniziert mit Azure mithilfe von Clientobjekten, um verschiedene Vorgänge in Azure auszuführen.  Das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt ist das Objekt, das für die Kommunikation mit der Tabellen-API von Cosmos DB verwendet wird.

Eine Anwendung erstellt in der Regel ein einzelnes [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt pro Tabelle, das in der gesamten Anwendung verwendet wird.  Es wird empfohlen, Abhängigkeitsinjektion (Dependency Injection, DI) zu verwenden und das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt als Singleton zu registrieren, um dies zu erreichen.  Weitere Informationen zur Verwendung der Abhängigkeitsinjektion mit dem Azure SDK finden Sie unter [Abhängigkeitsinjektion mit dem Azure SDK für .NET](/dotnet/azure/sdk/dependency-injection).

Bearbeiten Sie in der Datei `Startup.cs` der Anwendung die ConfigureServices()-Methode so, dass sie dem folgenden Codeausschnitt entspricht:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

Sie müssen auch am Anfang der Datei „Startup.cs“ die folgende using-Anweisung hinzufügen.

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6\. Implementieren von Cosmos DB-Tabellenvorgängen

Alle Cosmos DB-Tabellenvorgänge für die Beispiel-App werden in der `TableService`-Klasse implementiert, die sich im Verzeichnis *Services* befindet.  Sie müssen die Namespaces `Azure` und `Azure.Data.Tables` am Anfang dieser Datei importieren, um mit Objekten im `Azure.Data.Tables` SDK-Paket zu arbeiten.

```csharp
using Azure;
using Azure.Data.Tables;
```

Fügen Sie am Anfang der `TableService`-Klasse eine Membervariable für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt und einen Konstruktor hinzu, damit das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt in die Klasse eingefügt werden kann.

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>Abrufen von Zeilen aus einer Tabelle

Die [TableClient](/dotnet/api/azure.data.tables.tableclient)-Klasse enthält eine Methode namens [Query](/dotnet/api/azure.data.tables.tableclient.query), mit der Sie Zeilen aus der Tabelle auswählen können.  Da in diesem Beispiel keine Parameter an die Methode übergeben werden, werden alle Zeilen in der Tabelle ausgewählt.

Die Methode nimmt auch einen generischen Parameter vom Typ [ITableEntity](/dotnet/api/azure.data.tables.itableentity) an, der angibt, als welche Modellklasse die Daten zurückgegeben werden. In diesem Fall wird die integrierte Klasse [TableEntity](/dotnet/api/azure.data.tables.itableentity) verwendet, was bedeutet, dass die `Query`-Methode eine `Pageable<TableEntity>`-Sammlung als Ergebnis zurückgibt.

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

Die im Paket `Azure.Data.Tables` definierte Klasse [TableEntity](/dotnet/api/azure.data.tables.itableentity) verfügt über Eigenschaften für die Partitionsschlüssel- und Zeilenschlüsselwerte in der Tabelle.  Zusammen bilden diese beiden Werte einen eindeutigen Schlüssel für die Zeile in der Tabelle.  In dieser Beispielanwendung wird der Name der Wetterstation (Stadt) im Partitionsschlüssel gespeichert, und das Datum bzw. die Uhrzeit der Beobachtung wird im Zeilenschlüssel gespeichert.  Alle anderen Eigenschaften (Temperatur, Luftfeuchtigkeit, Windgeschwindigkeit) werden in einem Wörterbuch im `TableEntity`-Objekt gespeichert.

Es ist üblich, ein [TableEntity](/dotnet/api/azure.data.tables.tableentity)-Objekt einem Objekt Ihrer eigenen Definition zuzuordnen.  Die Beispielanwendung definiert zu diesem Zweck eine `WeatherDataModel`-Klasse im Verzeichnis *Models*.  Diese Klasse verfügt über Eigenschaften für den Stationsnamen und das Beobachtungsdatum, denen der Partitionsschlüssel und der Zeilenschlüssel zugeordnet wird, und bietet aussagekräftigere Eigenschaftsnamen für diese Werte.  Anschließend wird ein Wörterbuch verwendet, um alle anderen Eigenschaften des Objekts zu speichern.  Dies ist ein gängiges Muster bei der Arbeit mit Tabellenspeicher, da eine Zeile eine beliebige Anzahl von Eigenschaften aufweisen kann und wir möchten, dass unsere Modellobjekte in der Lage sind, alle diese Eigenschaften zu erfassen.  Diese Klasse enthält auch Methoden zum Auflisten der Eigenschaften für die Klasse.

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

Die `MapTableEntityToWeatherDataModel`-Methode wird verwendet, um ein [TableEntity](/dotnet/api/azure.data.tables.itableentity)-Objekt einem `WeatherDataModel`-Objekt zuzuordnen.  Das [TableEntity](/dotnet/api/azure.data.tables.itableentity)-Objekt enthält eine [Keys](/dotnet/api/azure.data.tables.tableentity.keys)-Eigenschaft, um alle in der Tabelle enthaltenen Eigenschaftsnamen für das Objekt (effektiv die Spaltennamen für diese Zeile in der Tabelle) abzurufen.  Die Methode `MapTableEntityToWeatherDataModel` ordnet die Eigenschaften `PartitionKey`, `RowKey`, `Timestamp` und `Etag` direkt zu und verwendet dann die Eigenschaft `Keys`, um über die anderen Eigenschaften im Objekt `TableEntity` zu iterieren und diese dem Objekt `WeatherDataModel` zuzuordnen, abzüglich der Eigenschaften, die bereits direkt zugeordnet wurden.

Bearbeiten Sie den Code in der `MapTableEntityToWeatherDataModel`-Methode so, dass er dem folgenden Codeblock entspricht.

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>Filtern von Zeilen, die von einer Tabelle zurückgegeben werden

Um die von einer Tabelle zurückgegebenen Zeilen zu filtern, können Sie eine Filterzeichenfolge im OData-Stil an die [Query](/dotnet/api/azure.data.tables.tableclient.query)-Methode übergeben. Wenn Sie beispielsweise alle Wetterwerte für Chicago zwischen 1. Juli 2021 Mitternacht und 2. Juli 2021 Mitternacht (einschließlich) abrufen möchten, übergeben Sie die folgende Filterzeichenfolge.

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

Sie können alle OData-Filteroperatoren auf der OData-Website im Abschnitt [Filter System Query Option](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/) (Systemabfrageoption filtern) anzeigen.

In der Beispielanwendung ist das `FilterResultsInputModel`-Objekt so konzipiert, dass alle vom Benutzer bereitgestellten Filterkriterien erfasst werden.

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

Wenn dieses Objekt an die `GetFilteredRows`-Methode in der `TableService`-Klasse übergeben wird, wird eine Filterzeichenfolge für jeden Eigenschaftswert erstellt, der nicht NULL ist.  Anschließend wird eine kombinierte Filterzeichenfolge erstellt, indem alle Werte mit einer „and“-Klausel verbunden werden.  Diese kombinierte Filterzeichenfolge wird an die [Query](/dotnet/api/azure.data.tables.tableclient.query)-Methode für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt übergeben, und nur Zeilen, die der Filterzeichenfolge entsprechen, werden zurückgegeben.  Sie können eine ähnliche Methode in Ihrem Code verwenden, um für Ihre Anwendung erforderliche geeignete Filterzeichenfolgen zu erstellen.

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>Einfügen von Daten mithilfe eines TableEntity-Objekts

Die einfachste Möglichkeit zum Hinzufügen von Daten zu einer Tabelle ist die Verwendung eines [TableEntity](/dotnet/api/azure.data.tables.itableentity)-Objekts.  In diesem Beispiel werden Daten aus einem Eingabemodellobjekt einem [TableEntity](/dotnet/api/azure.data.tables.itableentity)-Objekt zugeordnet.  Die Eigenschaften des Eingabeobjekts, die den Namen der Wetterstation und das Datum/die Uhrzeit der Beobachtung darstellen, werden den Eigenschaften [PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) bzw. [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) zugeordnet, die zusammen einen eindeutigen Schlüssel für die Zeile in der Tabelle bilden.  Anschließend werden die zusätzlichen Eigenschaften des Eingabemodellobjekts Wörterbucheigenschaften für das TableEntity-Objekt zugeordnet.  Schließlich wird die [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity)-Methode für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt verwendet, um Daten in die Tabelle einzufügen.

Ändern Sie die `InsertTableEntity`-Klasse in der Beispielanwendung so, dass sie den folgenden Code enthält.

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>Ausführen eines Upserts von Daten mithilfe eines TableEntity-Objekts

Wenn Sie versuchen, eine Zeile in eine Tabelle mit einer Kombination aus Partitionsschlüssel und Zeilenschlüssel einzufügen, die bereits in dieser Tabelle vorhanden ist, erhalten Sie einen Fehler.  Aus diesem Grund ist es häufig vorzuziehen, beim Hinzufügen von Zeilen zu einer Tabelle [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) anstelle der AddEntity-Methode zu verwenden.  Wenn die angegebene Kombination aus Partitionsschlüssel und Zeilenschlüssel bereits in der Tabelle vorhanden ist, aktualisiert die [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity)-Methode die vorhandene Zeile.  Andernfalls wird die Zeile der Tabelle hinzugefügt.

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>Einfügen oder Upsert von Daten mit variablen Eigenschaften

Einer der Vorteile der Tabellen-API von Cosmos DB besteht darin, dass, wenn ein Objekt, das in eine Tabelle geladen wird, neue Eigenschaften enthält, diese Eigenschaften automatisch der Tabelle hinzugefügt wird und die Werte in Cosmos DB gespeichert werden.  Es besteht keine Notwendigkeit, DDL-Anweisungen wie ALTER TABLE auszuführen, um Spalten hinzuzufügen, wie es bei einer herkömmlichen Datenbank der Fall ist.

Dieses Modell bietet Ihrer Anwendung Flexibilität beim Umgang mit Datenquellen, die im Laufe der Zeit Daten hinzufügen oder ändern können, oder wenn unterschiedliche Eingaben unterschiedliche Daten für Ihre Anwendung bereitstellen. In der Beispielanwendung können wir eine Wetterstation simulieren, die nicht nur die Basiswetterdaten, sondern auch einige zusätzliche Werte sendet. Wenn ein Objekt mit diesen neuen Eigenschaften zum ersten Mal in der Tabelle gespeichert wird, werden der Tabelle automatisch die entsprechenden Eigenschaften (Spalten) hinzugefügt.

In der Beispielanwendung basiert die `ExpandableWeatherObject`-Klasse auf einem internen Wörterbuch, um alle Eigenschaften des Objekts zu unterstützen.  Diese Klasse stellt ein typisches Muster für den Fall dar, dass ein Objekt einen beliebigen Satz von Eigenschaften enthalten muss.

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Um ein solches Objekt mit der Tabellen-API einzufügen oder ein Upsert auszuführen, ordnen Sie die Eigenschaften des erweiterbaren Objekts einem [TableEntity](/dotnet/api/azure.data.tables.tableentity)-Objekt zu und verwenden die [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity)- bzw. [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity)-Methoden für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt.

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>Aktualisieren einer Entität

Entitäten können aktualisiert werden, indem die [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity)-Methode für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt aufgerufen wird.  Da eine Entität (Zeile), die mit der Tabellen-API gespeichert wird, beliebige Eigenschaften enthalten kann, ist es häufig hilfreich, ein Aktualisierungsobjekt zu erstellen, das auf einem Wörterbuchobjekt basiert, das dem zuvor beschriebenen `ExpandableWeatherObject` ähnelt.  In diesem Fall besteht der einzige Unterschied im Hinzufügen einer `Etag`-Eigenschaft, die bei Aktualisierungen für Parallelitätssteuerung verwendet wird.

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

In der Beispiel-App wird dieses Objekt an die `UpdateEntity`-Methode in der `TableService`-Klasse übergeben.  Diese Methode lädt zuerst die vorhandene Entität aus der Tabellen-API, indem die [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity)-Methode für den [TableClient](/dotnet/api/azure.data.tables.tableclient) verwendet wird.  Anschließend wird dieses Entitätsobjekt aktualisiert und die `UpdateEntity`-Methode verwendet, um die Aktualisierungen in der Datenbank zu speichern.  Beachten Sie, wie die Methode [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) das aktuelle Etag des Objekts verwendet, um sicherzustellen, dass sich das Objekt seit dem ersten Laden nicht geändert hat.  Wenn Sie die Entität unabhängig davon aktualisieren möchten, können Sie den Wert `Etag.Any` an die `UpdateEntity`-Methode übergeben.

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>Entfernen einer Entität

Um eine Entität aus einer Tabelle zu entfernen, rufen Sie die [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity)-Methode für das [TableClient](/dotnet/api/azure.data.tables.tableclient)-Objekt mit dem Partitionsschlüssel und Zeilenschlüssel des Objekts auf.

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7\. Ausführen des Codes

Führen Sie die Beispielanwendung aus, um mit der Tabellen-API von Cosmos DB zu interagieren.  Wenn Sie die Anwendung zum ersten Mal ausführen, sind keine Daten vorhanden, weil die Tabelle leer ist.  Verwenden Sie eine der Schaltflächen oben in der Anwendung, um der Tabelle Daten hinzuzufügen.

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="Screenshot: Anwendung mit der Position der Schaltflächen zum Einfügen von Daten in Cosmos DB mithilfe der Tabellen-API." lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

Wenn Sie die Schaltfläche **Insert using Table Entity** (Mit Tabellenentität einfügen) auswählen, wird ein Dialogfeld geöffnet, in dem Sie eine neue Zeile mithilfe eines `TableEntity`-Objekts einfügen oder ein Upsert ausführen können.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="Screenshot: Anwendung mit dem Dialogfeld zum Einfügen von Daten mithilfe eines TableEntity-Objekts." lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

Wenn Sie auf die Schaltfläche **Insert using Expandable Data** (Mit erweiterbaren Daten einfügen) klicken, wird ein Dialogfeld geöffnet, in dem Sie ein Objekt mit benutzerdefinierten Eigenschaften einfügen können. Dies zeigt, wie die Tabellen-API von Cosmos DB der Tabelle bei Bedarf automatisch Eigenschaften (Spalten) hinzufügt.  Verwenden Sie die Schaltfläche *Add Custom Field* (Benutzerdefiniertes Feld hinzufügen), um mindestens eine neue Eigenschaft hinzuzufügen und diese Funktion zu veranschaulichen.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="Screenshot: Anwendung mit dem Dialogfeld zum Einfügen von Daten mithilfe eines Objekts mit benutzerdefinierten Feldern." lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

Verwenden Sie die Schaltfläche **Insert Sample Data** (Beispieldaten einfügen), um einige Beispieldaten in Ihre Cosmos DB-Tabelle zu laden.

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="Screenshot: Anwendung mit der Position der Schaltfläche zum Einfügen von Beispieldaten." lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

Wählen Sie im oberen Menü das Element **Filter Results** (Filterergebnisse) aus, um zur Seite „Filter Results“ zu gelangen.  Füllen Sie auf dieser Seite die Filterkriterien aus, um zu zeigen, wie eine Filterklausel erstellt und an die Tabellen-API von Cosmos DB übergeben werden kann.

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="Screenshot: Anwendung mit der Seite „Filterergebnisse“ und Hervorhebung des Menüelements, das zum Navigieren zur Seite verwendet wird." lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Beispielanwendung beendet haben, sollten Sie alle Azure-Ressourcen im Zusammenhang mit diesem Artikel aus Ihrem Azure-Konto entfernen.  Sie können dazu die Ressourcengruppe löschen.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Eine Ressourcengruppe kann über das [Azure-Portal](https://portal.azure.com/) wie folgt gelöscht werden.

| Anweisungen    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](./includes/create-table-dotnet/remove-resource-group-1.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="Screenshot: Suchen nach einer Ressourcengruppe." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](./includes/create-table-dotnet/remove-resource-group-2.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="Screenshot: Position der Schaltfläche „Ressourcengruppe löschen“." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](./includes/create-table-dotnet/remove-resource-group-3.md)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="Screenshot: Bestätigungsdialogfeld zum Löschen einer Ressourcengruppe." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Um eine Ressourcengruppe über die Azure-Befehlszeilenschnittstelle zu löschen, verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete) mit dem Namen der zu löschenden Ressourcengruppe.  Wenn Sie eine Ressourcengruppe löschen, werden auch alle in der Ressourcengruppe enthaltenen Azure-Ressourcen entfernt.

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Um eine Ressourcengruppe mit Azure PowerShell zu löschen, verwenden Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) mit dem Namen der zu löschenden Ressourcengruppe.  Wenn Sie eine Ressourcengruppe löschen, werden auch alle in der Ressourcengruppe enthaltenen Azure-Ressourcen entfernt.

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, eine Tabelle mit dem Daten-Explorer erstellen und eine App ausführen.  Jetzt können Sie Ihre Daten mit der Table-API abfragen.  

> [!div class="nextstepaction"]
> [Importieren von Daten in die Table-API](table-import.md)
