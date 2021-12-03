---
title: Erste Schritte mit der Azure Cosmos DB-API für MongoDB und Python
description: Stellt ein Python-Codebeispiel vor, mit dem Sie eine Verbindung herstellen und Abfragen über die Azure Cosmos DB-API für MongoDB durchführen können.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 10/22/2021
ms.openlocfilehash: af59ce8dc6fa3fc4a2f62e3bc00e185cb8be1bbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271198"
---
# <a name="quickstart-get-started-using-azure-cosmos-db-api-for-mongodb-and-python"></a>Schnellstart: Erste Schritte mit der Azure Cosmos DB-API für MongoDB und Python
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Dieser [Schnellstart](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) veranschaulicht folgende Vorgehensweisen:
1. Erstellen eines [Kontos für die Azure Cosmos DB-API für MongoDB](mongodb-introduction.md) 
2. Herstellen einer Verbindung mit Ihrem Konto über PyMongo
3. Erstellen einer Beispieldatenbank und -sammlung
4. Ausführen von CRUD-Vorgängen in der Beispielsammlung

## <a name="prerequisites-to-run-the-sample-app"></a>Voraussetzungen für das Ausführen der Beispiel-App

* [Python](https://www.python.org/downloads/) 3.9 und höher (Es empfiehlt sich, den in diesem Artikel beschriebenen [Beispielcode](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) mit dieser empfohlenen Version auszuführen. Er kann jedoch auch mit älteren Versionen von Python 3 verwendet werden.)
* Installation von [PyMongo](https://pypi.org/project/pymongo/) auf Ihrem Computer

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

## <a name="get-the-code"></a>Abrufen des Codes

Laden Sie den Python-Beispielcode [aus dem Repository](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) herunter, oder verwenden Sie den „git clone“:

```shell
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started
```

## <a name="retrieve-your-connection-string"></a>Abrufen der Verbindungszeichenfolge

Wenn Sie den Beispielcode ausführen, müssen Sie die Verbindungszeichenfolge Ihres API-Kontos für MongoDB eingeben. Sie finden sie mithilfe der folgenden Schritte:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Cosmos DB-Konto aus.

2. Wählen Sie im linken Navigationsbereich die Option **Verbindungszeichenfolge** und anschließend **Lese-/Schreibschlüssel** aus. Kopieren Sie die primäre Verbindungszeichenfolge mithilfe der Kopierschaltfläche im rechten Bildschirmbereich.

> [!WARNING]
> Checken Sie Kennwörter oder andere sensible Daten niemals in den Quellcode ein.


## <a name="run-the-code"></a>Ausführen des Codes

```shell
python run.py
```

## <a name="understand-how-it-works"></a>Funktionsweise

### <a name="connecting"></a>Verbindung

Der folgende Code fordert den Benutzer zur Eingabe der Verbindungszeichenfolge auf. Es wird grundsätzlich davon abgeraten, die Verbindungszeichenfolge im Code zu speichern, da sie von jedem für Lese- und Schreibvorgänge in Ihrer Datenbank verwendet werden kann.

```python
CONNECTION_STRING = getpass.getpass(prompt='Enter your primary connection string: ') # Prompts user for connection string
```

Der folgende Code erstellt eine Clientverbindung mit Ihrer API für MongoDB und testet ihre Gültigkeit.

```python
client = pymongo.MongoClient(CONNECTION_STRING)
try:
    client.server_info() # validate connection string
except pymongo.errors.ServerSelectionTimeoutError:
    raise TimeoutError("Invalid API for MongoDB connection string or timed out when attempting to connect")
```

### <a name="resource-creation"></a>Ressourcenerstellung
Mit dem folgenden Code werden die Beispieldatenbank und die Sammlung erstellt, die zum Ausführen von CRUD-Vorgängen verwendet werden. Beim programmgesteuerten Erstellen von Ressourcen wird empfohlen, die Befehle der API-Erweiterung für MongoDB zu verwenden (wie hier gezeigt), weil diese Befehle den Ressourcendurchsatz (RU/s) festlegen und Sharding konfigurieren können. 

Das implizite Erstellen von Ressourcen ist möglich. Dabei werden jedoch standardmäßig die empfohlenen Werte für den Durchsatz und kein Sharding verwendet.

```python
# Database with 400 RU throughput that can be shared across the DB's collections
db.command({'customAction': "CreateDatabase", 'offerThroughput': 400})
```

```python
 # Creates a unsharded collection that uses the DBs shared throughput
db.command({'customAction': "CreateCollection", 'collection': UNSHARDED_COLLECTION_NAME})
```

### <a name="writing-a-document"></a>Schreiben eines Dokuments
Im Folgenden wird ein Beispieldokument eingefügt, das im gesamten weiteren Beispiel verwendet wird. Wir erhalten den eindeutigen _id-Feldwert, sodass wir es in nachfolgenden Vorgängen abfragen können.

```python
"""Insert a sample document and return the contents of its _id field"""
document_id = collection.insert_one({SAMPLE_FIELD_NAME: randint(50, 500)}).inserted_id
```

### <a name="readingupdating-a-document"></a>Lesen/Aktualisieren eines Dokuments
Die folgenden Abfragen, Updates und erneuten Abfragen für das zuvor eingefügte Dokument.

```python
print("Found a document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))

collection.update_one({"_id": document_id}, {"$set":{SAMPLE_FIELD_NAME: "Updated!"}})
print("Updated document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))
```

### <a name="deleting-a-document"></a>Löschen eines Dokuments
Abschließend löschen wir das erstellte Dokument aus der Sammlung.
```python
"""Delete the document containing document_id from the collection"""
collection.delete_one({"_id": document_id})
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie gelernt, wie Sie ein API-Konto für MongoDB sowie eine Datenbank und eine Sammlung mit Code erstellen und CRUD-Vorgänge ausführen. 

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md).

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
