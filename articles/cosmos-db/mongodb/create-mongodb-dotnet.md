---
title: Erstellen einer Web-API mithilfe der Azure Cosmos DB-API für MongoDB und dem .NET SDK
description: Stellt ein .NET-Codebeispiel vor, mit dem Sie eine Verbindung herstellen und Abfragen über die API für MongoDB von Azure Cosmos DB durchführen können.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b528804385b9b47f6c586b49f2a33a4c688753b2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250196"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Erstellen einer .NET-Web-API mit der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Diese Schnellstartanleitung veranschaulicht folgende Vorgehensweisen:
1. Erstellen eines [Kontos für die Azure Cosmos DB-API für MongoDB](mongodb-introduction.md) 
2. Erstellen einer Produktkatalog-Web-API mit dem [.NET-Treiber für MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/)
3. Importieren von Beispieldaten

## <a name="prerequisites-to-run-the-sample-app"></a>Voraussetzungen für das Ausführen der Beispiel-App

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie kostenlos ein Azure-Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Sie können [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) auch ohne Azure-Abonnement kostenlos und unverbindlich testen.

Falls Sie noch nicht über Visual Studio verfügen, laden Sie [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) mit der beim Setup installierten Workload **ASP.NET und Webentwicklung** herunter.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>Kennenlernen des Objektmodells

Bevor Sie mit dem Erstellen der Anwendung fortfahren, befassen wir uns noch mit der Hierarchie der Ressourcen in der API für MongoDB und dem Objektmodell, das verwendet wird, um diese Ressourcen zu erstellen und darauf zuzugreifen. Die API für MongoDB erstellt Ressourcen in der folgenden Reihenfolge:

* Konto für die Azure Cosmos DB-API für MongoDB
* Datenbanken 
* Sammlungen 
* Dokumente

Weitere Informationen zur Hierarchie von Entitäten finden Sie im Artikel [Azure Cosmos DB-Ressourcenmodell](../account-databases-containers-items.md).

## <a name="install-the-sample-app-template"></a>Installieren der Beispiel-App-Vorlage

Dieses Beispiel ist eine dotnet-Projektvorlage, die installiert werden kann, um eine lokale Kopie zu erstellen. Führen Sie die folgenden Befehle in einem Befehlsfenster aus:

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

Die obenstehenden Befehle haben folgende Konsequenzen:

1. Erstellen Sie für das Beispiel das Verzeichnis *C:\cosmos-samples*. Wählen Sie einen für Ihr Betriebssystem geeigneten Ordner aus.
1. Ändern Sie Ihr aktuelles Verzeichnis in den Ordner *C:\cosmos-samples*.
1. Installieren Sie die Projektvorlage, und machen Sie sie über die dotnet-CLI global verfügbar.
1. Erstellen Sie mithilfe der Projektvorlage eine lokale Beispiel-App.

Wenn Sie die dotnet-CLI nicht verwenden möchten, können Sie die [Projektvorlagen auch als ZIP-Datei herunterladen](https://github.com/Azure/azure-cosmos-dotnet-templates). Dieses Beispiel befindet sich im Ordner `Templates/APIForMongoDBQuickstart-WebAPI`.

## <a name="review-the-code"></a>Überprüfen des Codes

Die folgenden Schritte sind optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, sehen Sie sich die folgenden Codeausschnitte an. Andernfalls fahren Sie mit [Aktualisieren der Anwendungseinstellungen fort](#update-the-application-settings).

### <a name="setup-connection"></a>Einrichten der Verbindung

Der folgende Codeausschnitt stammt aus der Datei *Services/MongoService.cs*.

* Die folgende Klasse repräsentiert den Client und wird vom .NET Framework in die Dienste eingefügt, die sie nutzen:

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>Einrichten des Produktkatalog-Datendiensts

Die folgenden Codeausschnitte stammen aus der Datei *Services/ProductService.cs*.

* Der folgende Code ruft die Datenbank und die Sammlung ab und erstellt diese, wenn sie noch nicht vorhanden sind:

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* Der folgende Code ruft ein Dokument nach SKU ab, einem eindeutigen Produktbezeichner:

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* Der folgende Code erstellt ein Produkt und fügt es in die Sammlung ein:

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* Der folgende Code sucht und aktualisiert ein Produkt:

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    Auf ähnliche Weise können Sie Dokumente mithilfe der Methode [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) löschen.

## <a name="update-the-application-settings"></a>Aktualisieren der Anwendungseinstellungen

Kopieren Sie im Azure-Portal die Verbindungszeichenfolge:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Cosmos DB-Konto aus. Wählen Sie dann im linken Navigationsbereich die Option **Verbindungszeichenfolge** und anschließend **Lese-/Schreibschlüssel** aus. Im nächsten Schritt verwenden Sie die Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms, um die primäre Verbindungszeichenfolge in die Datei *appsettings.json* zu kopieren.

2. Öffnen Sie die Datei *appsettings.json*.

3. Kopieren Sie den Wert für die **primäre Verbindungszeichenfolge** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und legen Sie ihn als Wert der Eigenschaft **DatabaseSettings.MongoConnectionString** in der Datei **appsettings.json** fest.

4. Überprüfen Sie den Wert für den **Datenbanknamen** in der Eigenschaft **DatabaseSettings.DatabaseName** in der Datei **appsettings.json**.

5. Überprüfen Sie den Wert für den **Sammlungsnamen** in der Eigenschaft **DatabaseSettings.ProductCollectionName** in der Datei **appsettings.json**.

> [!WARNING]
> Checken Sie Kennwörter oder andere sensible Daten niemals in den Quellcode ein.

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Cosmos DB aktualisiert.

## <a name="load-sample-data"></a>Laden von Beispieldaten

Beginnen Sie mit dem [Herunterladen](https://www.mongodb.com/try/download/database-tools) von [mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport), einem CLI-Tool, das problemlos kleine Mengen an JSON-, CSV- oder TSV-Daten importieren kann. Wir verwenden mongoimport, um die Beispielproduktdaten zu laden, die im Ordner `Data` dieses Projekts bereitgestellt wurden.

Kopieren Sie im Azure-Portal die Verbindungsinformationen, und geben Sie sie im folgenden Befehl ein: 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) das Konto für Ihre Azure Cosmos DB-API für MongoDB aus. Wählen Sie dann im linken Navigationsbereich die Option **Verbindungszeichenfolge** und anschließend **Lese-/Schreibschlüssel** aus. 

1. Kopieren Sie den Wert für **HOST** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und geben Sie ihn statt **\<HOST\>** ein.

1. Kopieren Sie den Wert für **PORT** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und geben Sie ihn statt **\<PORT\>** ein.

1. Kopieren Sie den Wert für **BENUTZERNAME** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und geben Sie ihn statt **\<USERNAME\>** ein.

1. Kopieren Sie den Wert für **KENNWORT** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und geben Sie ihn statt **\<PASSWORD\>** ein.

1. Überprüfen Sie den Wert für **Datenbankname**, und aktualisieren Sie ihn, wenn Sie die Datenbank mit einem anderen Namen als `cosmicworks` erstellt haben.

1. Überprüfen Sie den Wert für **Sammlungsname**, und aktualisieren Sie ihn, wenn Sie die Sammlung mit einem anderen Namen als `products` erstellt haben.

> [!Note]
> Wenn Sie diesen Schritt überspringen möchten, können Sie Dokumente mit dem richtigen Schema erstellen, indem Sie den POST-Endpunkt verwenden, der im Rahmen dieses Web-API-Projekts bereitgestellt wurde.

## <a name="run-the-app"></a>Ausführen der App

Drücken Sie in Visual Studio die Tastenkombination STRG+F5, um die App auszuführen. Der Standardbrowser wird mit der App gestartet.

Wenn Sie die CLI bevorzugen, führen Sie den folgenden Befehl in einem Befehlsfenster aus, um die Beispiel-App zu starten. Dieser Befehl installiert auch die Projektabhängigkeiten und erstellt das Projekt, startet jedoch nicht automatisch den Browser.

```bash
dotnet run
```

Wenn die Anwendung ausgeführt wird, navigieren Sie zu `https://localhost:5001/swagger/index.html`, um sich die [Swagger-Dokumentation](https://swagger.io/) für die Web-API anzusehen und Beispielanforderungen zu übermitteln.

Wählen Sie die API aus, die Sie testen möchten, und wählen Sie „Try it out“ (Ausprobieren) aus.

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="Screenshot der Seite zum Ausprobieren von API-Endpunkten auf der Swagger-Benutzeroberfläche für die Web-API":::

Geben Sie alle erforderlichen Parameter ein, und wählen Sie „Execute“ (Ausführen) aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Konto für die API für MongoDB erstellen, eine Datenbank und eine Sammlung mit Code erstellen und eine Web-API-App ausführen. Jetzt können Sie weitere Daten in Ihre Datenbank importieren. 

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md).

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
