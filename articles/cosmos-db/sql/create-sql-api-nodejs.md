---
title: 'Schnellstart: Abfragen von Daten aus einem Azure Cosmos DB-SQL-API-Konto mithilfe von Node.js'
description: Hier erfahren Sie, wie Sie mithilfe von Node.js eine App erstellen, die eine Verbindung mit einem Azure Cosmos DB-SQL-API-Konto herstellt und Daten abfragt.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/26/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 635e85223dc7a3fb2fd579fa33baa338ebf9cc50
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117673"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Schnellstart: Verwenden von Node.js zum Herstellen einer Verbindung mit einem und Abfragen von Daten aus einem Azure Cosmos DB-SQL-API-Konto
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK V4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3-Connector](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-SQL-API-Konto im Azure-Portal mithilfe einer über GitHub geklonten Node.js-App. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="walkthrough-video"></a>Video mit exemplarischer Vorgehensweise

Dieses Video enthält eine umfassende exemplarische Vorgehensweise für den Inhalt dieses Artikels.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit dem URI `https://localhost:8081` und dem Schlüssel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` verwenden.
- [Node.js 6.0.0 oder höher](https://nodejs.org/)
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Erstellen eines Azure Cosmos-Kontos

Für diese Schnellstartanleitung können Sie die Option [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) verwenden, um ein Azure Cosmos-Konto zu erstellen.

1. Navigieren Sie zur Seite [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/).

1. Wählen Sie unter dem API-Konto **SQL** die Option **Erstellung** aus. Melden Sie sich unter Verwendung Ihres Microsoft-Kontos an.

1. Nachdem Sie sich erfolgreich angemeldet haben, ist Ihr Azure Cosmos-Konto bereit. Wählen Sie **Open in the Azure portal** (Im Azure-Portal öffnen) aus, um das neu erstellte Konto zu öffnen.

Für die Option „Azure Cosmos DB kostenlos testen“ ist kein Azure-Abonnement erforderlich, und das Azure Cosmos-Konto steht für einen begrenzten Zeitraum von 30 Tagen zur Verfügung. Falls Sie das Azure Cosmos-Konto für einen längeren Zeitraum verwenden möchten, müssen Sie stattdessen [das Konto in Ihrem Azure-Abonnement erstellen](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account).

## <a name="add-a-container"></a>Hinzufügen eines Containers

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Abfragen Ihrer Daten

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie zunächst eine Node.js-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie sie aus.

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Azure Cosmos-Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren.

Wenn Sie mit der vorherigen Version des SQL JavaScript SDK vertraut sind, kennen Sie unter Umständen bereits die Begriffe _Sammlung_ und _Dokument_. Azure Cosmos DB unterstützt [mehrere API-Modelle](../introduction.md). Daher werden in [Version 2.0 und höheren Versionen des JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) die generischen Begriffe _Container_ (für eine Sammlung, einen Graph oder eine Tabelle) und _Element_ zum Beschreiben des Containerinhalts verwendet.

Das Cosmos DB JavaScript SDK heißt „@azure/cosmos“ und kann über npm installiert werden:

```bash
npm install @azure/cosmos
```

Die folgenden Codeausschnitte stammen alle aus der Datei _app.js_.

- Der Cosmos-Client (`CosmosClient`) wird aus dem npm-Paket `@azure/cosmos` importiert.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Ein neues Objekt vom Typ `CosmosClient` wird initialisiert.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Wählen Sie die Datenbank „Tasks“ aus.

  ```javascript
  const database = client.database(databaseId);
  ```

- Wählen Sie den Container bzw. die Sammlung „Items“ aus.

  ```javascript
  const container = database.container(containerId);
  ```

- Wählen Sie alle Elemente im Container „Items“ aus.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Erstellen eines neuen Elements

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Aktualisieren eines Elements

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Löschen eines Elements

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> Sowohl in der Methode „update“ als auch in der Methode „delete“ muss das Element aus der Datenbank durch Aufrufen von `container.item()` ausgewählt werden. Bei den beiden übergebenen Parametern handelt es sich um die ID und den Partitionsschlüssel des Elements. In diesem Fall ist der Partitionsschlüssel der Wert des Kategoriefelds.

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Details der Verbindungszeichenfolge Ihres Azure Cosmos-Kontos abzurufen. Kopieren Sie die Verbindungszeichenfolge in die App, damit sie eine Verbindung mit der Datenbank herstellen kann.

1. Wählen Sie in Ihrem Azure Cosmos DB-Konto im [Azure-Portal](https://portal.azure.com/) im linken Navigationsbereich die Option **Schlüssel** und anschließend **Lese-/Schreibschlüssel** aus. Mithilfe der Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms kopieren Sie im nächsten Schritt den URI und den Primärschlüssel in die Datei _app.js_.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf dem Blatt „Schlüssel“":::

2. Öffnen Sie die Datei _config.js_.

3. Kopieren Sie den URI-Wert aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und legen Sie ihn in _config.js_ als Wert des Endpunktschlüssels fest.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopieren Sie anschließend den Wert für „PRIMÄRSCHLÜSSEL“ aus dem Portal, und legen Sie ihn als Wert von `config.key` in _config.js_ fest. Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Ausführen der App

1. Führen Sie `npm install` in einem Terminal aus, um das npm-Paket „@azure/cosmos“ zu installieren.

2. Führen Sie `node app.js` in einem Terminal aus, um Ihre Node-Anwendung zu starten.

3. Die beiden Elemente, die Sie weiter oben in dieser Schnellstartanleitung erstellt haben, werden aufgeführt. Ein neues Element wird erstellt. Das Flag „isComplete“ für dieses Element wird in „true“ geändert und das Element schließlich gelöscht.

Sie können mit dieser Beispielanwendung weiter experimentieren oder zum Daten-Explorer zurückkehren und Ihre Daten ändern und mit ihnen arbeiten.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Container mit dem Daten-Explorer erstellen und eine Node.js-App ausführen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren.

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md). 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Tutorial: Migrieren Ihrer Daten zu Azure Cosmos DB mithilfe des Datenmigrationstools](../import-data.md)
