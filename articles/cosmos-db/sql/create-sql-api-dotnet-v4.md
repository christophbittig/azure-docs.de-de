---
title: Verwalten von Ressourcen für die SQL-API von Azure Cosmos DB mit Version 4 des .NET SDK
description: Verwenden Sie diese Schnellstartanleitung, um mit Version 4 des .NET SDK eine Konsolen-App zum Verwalten von Azure Cosmos DB-SQL-API-Kontoressourcen zu erstellen.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 7468f2a615c52c6d18f93eebead58b9b28f6fb08
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117609"
---
# <a name="quickstart-build-a-console-app-by-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Schnellstart: Erstellen einer Konsolen-App zum Verwalten von Azure Cosmos DB-SQL-API-Kontoressourcen mithilfe von Version 4 des .NET SDK (Vorschau)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK V4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3-Connector](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Erste Schritte mit der Azure Cosmos DB-SQL-API-Clientbibliothek für .NET. Befolgen Sie die Schritte in diesem Artikel, um das Paket .NET V4 (Azure.Cosmos) zu installieren und eine App zu erstellen. Probieren Sie dann den Beispielcode für grundlegende CRUD-Vorgänge (Create, Read, Update, Delete) für die in Azure Cosmos DB gespeicherten Daten aus.

> [!IMPORTANT]
> Das .NET V4 SDK für Azure Cosmos DB ist derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB ist die schnelle NoSQL-Datenbank von Microsoft mit offenen APIs für jede Größenordnung. Mit Azure Cosmos DB können Sie Schlüssel-Werte-, Dokument- und Graphdatenbanken schnell erstellen und abfragen. Verwenden Sie die Azure Cosmos DB-SQL-API-Clientbibliothek für .NET zum Ausführen der folgenden Aufgaben:

* Erstellen einer Azure Cosmos-Datenbank und eines Containers
* Hinzufügen von Beispieldaten zum Container
* Abfragen der Daten 
* Löschen der Datenbank.

[Quellcode der Bibliothek](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/) Sie können [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) auch ohne Azure-Abonnement kostenlos und unverbindlich testen. 
* [.NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Führen Sie `dotnet --version` aus, um zu überprüfen, welche Version in Ihrer Umgebung verfügbar ist.

## <a name="set-up"></a>Einrichten

Dieser Abschnitt führt Sie durch das Erstellen eines Azure Cosmos-Kontos und durch das Einrichten eines Projekts, das die Azure Cosmos DB-SQL-API-Clientbibliothek für .NET zum Verwalten von Ressourcen verwendet. 

Mit dem in diesem Artikel beschriebenen Beispielcode erstellen Sie eine Datenbank mit dem Namen `FamilyDatabase` und Familienmitglieder in dieser Datenbank. Jedes Familienmitglied ist ein Element und verfügt über Eigenschaften wie `Id`, `FamilyName`, `FirstName`, `LastName`, `Parents`, `Children` und `Address`. Die Eigenschaft `LastName` wird als Partitionsschlüssel für den Container verwendet. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Erstellen eines Azure Cosmos-Kontos

Wenn Sie die Option [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) verwenden, um ein Azure Cosmos-Konto zu erstellen, müssen Sie ein Azure Cosmos-Konto vom Typ **SQL-API** erstellen. Es wurde bereits ein Azure Cosmos-Testkonto für Sie erstellt. Sie müssen das Konto nicht explizit erstellen, sodass Sie diesen Abschnitt überspringen und zum nächsten Abschnitt wechseln können.

Wenn Sie über ein eigenes Azure-Abonnement verfügen oder ein kostenloses Abonnement erstellt haben, müssen Sie ein Azure Cosmos-Konto explizit erstellen. Mit dem folgenden Code wird ein Azure Cosmos-Konto mit Sitzungskonsistenz erstellt. Das Konto wird in `South Central US` und `North Central US` repliziert.  

Sie können das Azure Cosmos-Konto mithilfe von Azure Cloud Shell erstellen. Azure Cloud Shell ist eine interaktive, authentifizierte, über den Browser zugängliche Shell für die Verwaltung von Azure-Ressourcen. Sie bietet Ihnen die Flexibilität, die Shell-Umgebung auszuwählen, die sich am besten für Ihre Arbeitsweise eignet: Bash oder PowerShell. 

Verwenden Sie Bash für diesen Schnellstart. Azure Cloud Shell erfordert auch ein Speicherkonto. Sie können eines erstellen, wenn Sie dazu aufgefordert werden.

1. Klicken Sie auf die Schaltfläche **Ausprobieren** neben dem folgenden Code, wählen Sie den **Bash**-Modus und dann **Speicherkonto erstellen** aus, und melden Sie sich bei Cloud Shell an. 

1. Kopieren Sie den folgenden Code, fügen Sie ihn in Azure Cloud Shell ein, und führen Sie ihn aus. Der Name des Azure Cosmos-Kontos muss global eindeutig sein. Achten Sie also darauf, den `mysqlapicosmosdb`-Wert zu aktualisieren, bevor Sie den Befehl ausführen.

   ```azurecli-interactive

   # Set variables for the new SQL API account, database, and container
   resourceGroupName='myResourceGroup'
   location='southcentralus'

   # The Azure Cosmos account name must be globally unique, so be sure to update the `mysqlapicosmosdb` value before you run the command
   accountName='mysqlapicosmosdb'

   # Create a resource group
   az group create \
       --name $resourceGroupName \
       --location $location

   # Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
   az cosmosdb create \
       --resource-group $resourceGroupName \
       --name $accountName \
       --kind GlobalDocumentDB \
       --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
       --default-consistency-level "Session" \
       --enable-multiple-write-locations true

   ```

Die Erstellung des Azure Cosmos-Kontos dauert eine Weile. Nachdem der Vorgang erfolgreich war, wird die Bestätigungsausgabe angezeigt. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und überprüfen Sie, ob das Azure Cosmos-Konto mit dem angegebenen Namen vorhanden ist. Nachdem die Ressource erstellt wurde, können Sie das Azure Cloud Shell-Fenster schließen. 

### <a name="create-a-net-app"></a><a id="create-dotnet-core-app"></a>Erstellen einer .NET-App

Erstellen Sie eine .NET-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Öffnen Sie auf Ihrem lokalen Computer die Windows-Eingabeaufforderung oder ein Terminalfenster. Sie führen alle Befehle in den nächsten Abschnitten über die Eingabeaufforderung oder das Terminal aus.  

Führen Sie den folgenden `dotnet new`-Befehl aus, um eine neue App mit dem Namen `todo` zu erstellen. Mit dem Parameter `--langVersion` wird die `LangVersion`-Eigenschaft in der erstellten Projektdatei festgelegt.

   ```bash
   dotnet new console --langVersion:8 -n todo
   ```

Verwenden Sie die folgenden Befehle, um Ihr Verzeichnis in den neu erstellten App-Ordner zu ändern und die Anwendung zu erstellen:

   ```bash
   cd todo
   dotnet build
   ```

Die erwartete Ausgabe aus dem Build sollte etwa wie folgt aussehen:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installieren des Azure Cosmos DB-Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl `dotnet add package` die Azure Cosmos DB-Clientbibliothek für .NET Core:

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieren der Anmeldeinformationen für Ihr Azure Cosmos-Konto aus dem Azure-Portal

Die Beispielanwendung muss sich bei Ihrem Azure Cosmos-Konto authentifizieren. Für die Authentifizierung übergeben Sie die Anmeldeinformationen für das Azure Cosmos-Konto an die Anwendung. Führen Sie zum Abrufen der Anmeldeinformationen für Ihr Azure Cosmos-Konto die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto.

1. Öffnen Sie den Bereich **Schlüssel**, und kopieren Sie den **URI**- und den **PRIMÄRSCHLÜSSEL**-Wert für Ihr Konto. Den URI- und den Schlüsselwert fügen Sie in der nächsten Prozedur einer Umgebungsvariablen hinzu.

## <a name="learn-the-object-model"></a><a id="object-model"></a>Kennenlernen des Objektmodells

Bevor Sie mit dem Erstellen der Anwendung fortfahren, befassen wir uns noch mit der Hierarchie der Ressourcen in Azure Cosmos DB und dem Objektmodell, das verwendet wird, um diese Ressourcen zu erstellen und darauf zuzugreifen. Azure Cosmos DB erstellt Ressourcen in der folgenden Reihenfolge:

* Azure Cosmos-Konto 
* Datenbanken 
* Container 
* Items

Weitere Informationen zur Hierarchie von Entitäten finden Sie im Artikel [Azure Cosmos DB-Ressourcenmodell](../account-databases-containers-items.md). Für die Interaktion mit diesen Ressourcen verwenden Sie die folgenden .NET-Klassen:

* `CosmosClient`. Diese Klasse bietet eine clientseitige logische Darstellung für den Azure Cosmos DB-Dienst. Das Clientobjekt wird zum Konfigurieren und Ausführen von Anforderungen für den Dienst verwendet.
* `CreateDatabaseIfNotExistsAsync`. Mit dieser Methode wird in einem asynchronen Vorgang eine Datenbankressource erstellt (falls nicht vorhanden) oder abgerufen (wenn bereits vorhanden). 
* `CreateContainerIfNotExistsAsync`. Mit dieser Methode wird in einem asynchronen Vorgang ein Container erstellt (falls nicht vorhanden) oder abgerufen (wenn bereits vorhanden). Sie können den Statuscode in der Antwort überprüfen, um zu ermitteln, ob der Container neu erstellt wurde (201) oder ein vorhandener Container zurückgegeben wurde (200). 
* `CreateItemAsync`. Mit dieser Methode wird ein Element im Container erstellt.
* `UpsertItemAsync`. Mit dieser Methode wird ein Element im Container erstellt (falls nicht vorhanden) oder ersetzt (wenn bereits vorhanden). 
* `GetItemQueryIterator`. Mit dieser Methode wird unter Verwendung einer SQL-Anweisung mit parametrisierten Werten eine Abfrage für Elemente unter einem Container in einer Azure Cosmos-Datenbank erstellt. 
* `DeleteAsync`. Mit dieser Methode wird die angegebene Datenbank aus Ihrem Azure Cosmos-Konto gelöscht.

 ## <a name="configure-code-examples"></a><a id="code-examples"></a>Konfigurieren von Codebeispielen

Mit dem in diesem Artikel beschriebenen Beispielcode wird in Azure Cosmos DB eine Familiendatenbank („FamilyDatabase“) erstellt. Die Familiendatenbank enthält Familiendetails wie Name, Adresse, Standort, Eltern, Kinder und Haustiere. 

Definieren Sie die Eigenschaften eines Familienelements, bevor Sie Ihr Azure Cosmos-Konto mit den Daten auffüllen. Erstellen Sie eine neue Klasse mit dem Namen `Family.cs` auf der Stammebene Ihrer Beispielanwendung, und fügen Sie ihr den folgenden Code hinzu:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives-and-define-the-client-object"></a>Hinzufügen der using-Direktiven und Definieren des Clientobjekts

Öffnen Sie im Projektverzeichnis die Datei *Program.cs* im Editor, und fügen Sie die folgenden `using`-Direktiven am Anfang der Anwendung hinzu:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Fügen Sie in der `Program`-Klasse die folgenden globalen Variablen hinzu. Zu diesen Variablen gehören der Endpunkt und die Autorisierungsschlüssel, der Name der Datenbank und der von Ihnen erstellte Container. Ersetzen Sie die Werte für den Endpunkt und die Autorisierungsschlüssel gemäß Ihrer Umgebung. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Ersetzen Sie abschließend die Methode `Main` wie folgt:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Erstellen einer Datenbank 

Definieren Sie die `CreateDatabaseAsync`-Methode in der `program.cs`-Klasse. Mit dieser Methode wird die Datenbank `FamilyDatabase` erstellt, falls sie noch nicht vorhanden ist.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Erstellen eines Containers

Definieren Sie die `CreateContainerAsync`-Methode in der `Program`-Klasse. Mit dieser Methode wird der Container `FamilyContainer` erstellt, falls er noch nicht vorhanden ist. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Erstellen eines Elements

Erstellen Sie ein Familienelement, indem Sie die `AddItemsToContainerAsync`-Methode mit dem folgenden Code hinzufügen. Zum Erstellen eines Elements können Sie die Methode `CreateItemAsync` oder `UpsertItemAsync` verwenden.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Abfragen der Elemente

Nachdem Sie ein Element eingefügt haben, können Sie eine Abfrage zum Abrufen der Details der Familie „Andersen“ ausführen. Im folgenden Codebeispiel wird gezeigt, wie die Abfrage direkt mithilfe der SQL-Abfrage ausgeführt wird. Die SQL-Abfrage zum Abrufen der Details der Familie „Andersen“ lautet `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieren Sie die `QueryItemsAsync`-Methode in der `Program`-Klasse, und fügen Sie ihr den folgenden Code hinzu:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Ersetzen eines Elements 

Lesen Sie ein Familienelement, und aktualisieren Sie es, indem Sie die `ReplaceFamilyItemAsync`-Methode mit dem folgenden Code hinzufügen:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Löschen eines Elements 

Löschen Sie ein Familienelement, indem Sie die `DeleteFamilyItemAsync`-Methode mit dem folgenden Code hinzufügen:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Löschen der Datenbank 

Sie können die Datenbank löschen, indem Sie die `DeleteDatabaseAndCleanupAsync`-Methode mit dem folgenden Code hinzufügen:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Speichern Sie die Datei *Program.cs*, nachdem Sie alle erforderlichen Methoden hinzugefügt haben. 

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung zum Erstellen der Azure Cosmos DB-Ressourcen aus:

   ```bash
   dotnet run
   ```

Die folgende Ausgabe wird generiert, wenn Sie die Anwendung ausführen:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Sie können überprüfen, ob die Daten erstellt wurden, indem Sie sich beim Azure-Portal anmelden und die erforderlichen Elemente in Ihrem Azure Cosmos-Konto anzeigen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Azure Cosmos-Konto und die entsprechende Ressourcengruppe nicht mehr benötigen, können Sie sie über die Azure-Befehlszeilenschnittstelle (Azure CLI) oder Azure PowerShell entfernen. Der folgende Befehl zeigt, wie die Ressourcengruppe mit der Azure CLI gelöscht wird:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos-Konto, eine Datenbank und einen Container mithilfe einer .NET Core-App erstellen. Sie können jetzt weitere Daten in Ihr Azure Cosmos-Konto importieren, indem Sie die Anweisungen in folgendem Artikel befolgen: 

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](../import-data.md)
