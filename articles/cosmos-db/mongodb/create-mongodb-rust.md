---
title: Verbinden einer Rust-Anwendung mit der Azure Cosmos DB-API für MongoDB
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Rust-Anwendung erstellen, die von der Azure Cosmos DB-API für MongoDB unterstützt wird.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 08/26/2021
ms.openlocfilehash: 8fab3b577177c66e8999c5e7c594b165a310276a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033694"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Verbinden einer Rust-Anwendung mit der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können. Bei dem Beispiel in diesem Artikel handelt es sich um eine einfache befehlszeilenbasierte Anwendung, die den [Rust-Treiber für MongoDB](https://github.com/mongodb/mongo-rust-driver) verwendet. Da die Azure Cosmos DB-API für MongoDB [mit dem Verbindungsprotokoll von MongoDB kompatibel](./mongodb-introduction.md) ist, kann von jedem MongoDB-Clienttreiber eine Verbindung damit hergestellt werden.

Hier erfahren Sie, wie Sie den MongoDB-Rust-Treiber verwenden, um mit der Azure Cosmos DB-API für MongoDB zu interagieren. Zu diesem Zweck werden CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren, Löschen) betrachtet, die im Beispielcode implementiert sind. Zum Abschluss können Sie die Anwendung lokal ausführen, um sie in Aktion zu sehen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit der Verbindungszeichenfolge `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` verwenden.
- [Rust](https://www.rust-lang.org/tools/install) (ab Version 1.39)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Einrichten von Azure Cosmos DB

Eine Anleitung zum Einrichten eines Azure Cosmos DB-Kontos finden Sie [hier](create-mongodb-dotnet.md). Die Anwendung benötigt die MongoDB-Verbindungszeichenfolge. Diese kann über das Azure-Portal abgerufen werden. Ausführliche Informationen finden Sie unter [Abrufen der MongoDB-Verbindungszeichenfolge zum Anpassen](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Ausführen der Anwendung

### <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens `git-samples`, und schließen Sie dann die Eingabeaufforderung.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Erstellen der Anwendung

So erstellen Sie die Binärdatei:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Konfigurieren der Anwendung 

Exportieren Sie die Verbindungszeichenfolge, die MongoDB-Datenbank und die Sammlungsnamen als Umgebungsvariablen. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Die Option `ssl=true` ist wichtig, um die Voraussetzungen für Cosmos DB zu erfüllen. Weitere Informationen finden Sie unter [Anforderungen an die Verbindungszeichenfolge](connect-mongodb-account.md#connection-string-requirements).
>

Ersetzen Sie bei der Umgebungsvariable `MONGODB_URL` die Platzhalter `<COSMOSDB_ACCOUNT_NAME>` und `<COSMOSDB_PASSWORD>`.

- `<COSMOSDB_ACCOUNT_NAME>`: Der Name des von Ihnen erstellten Azure Cosmos DB-Kontos
- `<COSMOSDB_PASSWORD>`: Der im vorherigen Schritt extrahierte Datenbankschlüssel

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Sie können für `MONGODB_DATABASE` und `MONGODB_COLLECTION` Ihre bevorzugten Werte auswählen oder die Standardwerte unverändert übernehmen.

Wechseln Sie zum Ausführen der Anwendung zum Ordner mit der Binärdatei der Anwendung:

```bash
cd target/release
```

So erstellen Sie ein `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Wenn der Vorgang erfolgreich war, wird eine Ausgabe mit der MongoDB-`_id` des neu erstellten Dokuments angezeigt:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Erstellen eines weiteren `todo`-Elements

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Auflisten aller `todo`-Elemente

```bash
./todo list all
```

Die eben erstellten Elemente sollten angezeigt werden:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Wenn Sie den Status eines Elements vom Typ `todo` aktualisieren möchten, um ihn beispielsweise in `completed` zu ändern, verwenden Sie die `todo`-ID, wie hier zu sehen:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Auflisten der abgeschlossenen `todo`-Elemente

```bash
./todo list completed
```

Es sollte nur das eben von Ihnen aktualisierte Element angezeigt werden.

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Löschen eines `todo`-Elements anhand der ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Auflisten aller `todo`-Elemente zur Bestätigung

```bash
./todo list all
```

Das Element vom Typ `todo`, das Sie eben gelöscht haben, sollte nicht vorhanden sein.

### <a name="view-data-in-data-explorer"></a>Anzeigen von Daten im Daten-Explorer

Die in Azure Cosmos DB gespeicherten Daten können im Azure-Portal angezeigt und abgefragt werden.

Melden Sie sich im [Azure-Portal](https://portal.azure.com) in Ihrem Webbrowser an, um die im vorherigen Schritt erstellten Benutzerdaten anzuzeigen, abzufragen und mit ihnen zu arbeiten.

Geben Sie im Suchfeld oben **Azure Cosmos DB** ein. Wenn sich das Blatt Ihres Cosmos-Kontos öffnet, wählen Sie Ihr Cosmos-Konto aus. Wählen Sie im linken Navigationsbereich **Daten-Explorer** aus. Erweitern Sie Ihre Sammlung im Bereich „Sammlungen“. Anschließend können Sie die Dokumente in der Sammlung anzeigen, die Daten abfragen und sogar gespeicherte Prozeduren, Trigger und UDFs erstellen und ausführen.

## <a name="review-the-code-optional"></a>Überprüfen des Codes (optional)

Wenn Sie erfahren möchten, wie die Anwendung funktioniert, können Sie sich die Codeausschnitte in diesem Abschnitt ansehen. Die folgenden Codeausschnitte stammen aus der Datei `src/main.rs`.

Die Funktion `main` ist der Einstiegspunkt für die Anwendung vom Typ `todo`. Es wird erwartet, dass die Verbindungs-URL für die Azure Cosmos DB-API für MongoDB durch die Umgebungsvariable `MONGODB_URL` angegeben wird. Eine neue Instanz von `TodoManager` wird erstellt, gefolgt von einem [`match`-Ausdruck](https://doc.rust-lang.org/book/ch06-02-match.html), der abhängig von der gewählten Benutzeraktion (`TodoManager`, `create`, `update` oder `list`) die entsprechende Methode vom Typ `delete`delegiert.

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` ist ein Element vom Typ `struct`, das eine Sammlung vom Typ [mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html) kapselt. Beim Versuch, ein Element vom Typ `TodoManager` mithilfe der Funktion `new` zu instanziieren, wird eine Verbindung mit der Azure Cosmos DB-API für MongoDB initiiert.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Am wichtigsten ist jedoch, dass `TodoManager` über hilfreiche Methoden für die Verwaltung von Elementen vom Typ `todo` verfügt. Wir gehen diese Punkte einzeln durch.

Die Methode `add_todo` akzeptiert eine vom Benutzer angegebene Beschreibung für `todo` und erstellt eine Instanz der Struktur vom Typ `Todo`: Das Framework [serde](https://github.com/serde-rs/serde) wird verwendet, um BSON-Daten in Strukturinstanzen vom Typ `Todo` zuzuordnen (serialisieren/deserialisieren). Beachten Sie, wie Feldattribute für `serde` verwendet werden, um den Serialisierungs-/Deserialisierungsprozess anzupassen. So ist beispielsweise das Feld `todo_id` im Todo-Element `struct` eine Objekt-ID (`ObjectId`) und wird in MongoDB als `_id` gespeichert.

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

Von [Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) wird ein [Dokument](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) akzeptiert, das die hinzuzufügenden Details für das Element `todo` darstellt. Beachten Sie, dass die Konvertierung von `Todo` in `Document` ein zweistufiger Prozess ist, der durch eine Kombination aus [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) und [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document) erreicht wird.

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) wird verwendet, um *alle* Elemente vom Typ `todo` abzurufen oder sie auf der Grundlage des vom Benutzer angegebenen Status (`pending` oder `completed`) zu filtern. Wie Sie sehen, wird in der Schleife `while` jedes Element vom Typ `Document`, das infolge der Suche abgerufen wird, mithilfe von [bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html) in eine Struktur vom Typ `Todo` konvertiert. Das ist das Gegenteil von dem, was in der Methode `add_todo` durchgeführt wurde.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

Der Status eines Elements vom Typ `todo` kann geändert werden (von `pending` in `completed` oder umgekehrt). `todo` wird in eine Objekt-ID vom Typ [bson::oid::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) konvertiert. Diese wird dann von der Methode [Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) verwendet, um das zu aktualisierende Dokument zu finden.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

Ein Element vom Typ `todo` kann ganz einfach mithilfe der Methode [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) gelöscht werden.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit Azure Cloud Shell ein Azure Cosmos DB-Konto für die MongoDB-API erstellen sowie eine Rust-Befehlszeilen-App erstellen und ausführen, um Elemente vom Typ `todo` zu verwalten. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren.

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
