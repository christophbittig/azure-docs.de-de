---
title: 'Schnellstart: Cassandra-API mit Node.js – Azure Cosmos DB'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Cassandra-API von Azure Cosmos DB eine Profilanwendung mit Node.js erstellen.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: eaba92b91e68279a5920ca272264a8a3d93f8484
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784143"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Schnellstart: Erstellen einer Cassandra-App mit dem Node.js-SDK und Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](manage-data-dotnet.md)
> * [.NET Core](manage-data-dotnet-core.md)
> * [Java v3](manage-data-java.md)
> * [Java v4](manage-data-java-v4-sdk.md)
> * [Node.js](manage-data-nodejs.md)
> * [Python](manage-data-python.md)
> * [Golang](manage-data-go.md)
>    

In dieser Schnellstartanleitung erstellen Sie ein Azure Cosmos DB-Cassandra-API-Konto und verwenden eine über GitHub geklonte Cassandra-Node.js-App, um eine Cassandra-Datenbank und einen Cassandra-Container zu erstellen. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)] Alternativ können Sie [Azure Cosmos DB ohne Azure-Abonnement testen](https://azure.microsoft.com/try/cosmosdb/) – kostenlos und ohne Verpflichtung.

Zudem benötigen Sie:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) Version v0.10.29 oder höher
* [Git-Client](https://git-scm.com/)

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Dokumentdatenbank müssen Sie mit Azure Cosmos DB ein Cassandra-Konto erstellen.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

In diesem Schritt klonen wir eine Cassandra-API-App aus GitHub, legen die Verbindungszeichenfolge fest und führen die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie eine Eingabeaufforderung. Erstellen Sie einen neuen Ordner namens `git-samples`. Schließen Sie dann die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster, z.B. git bash. Wechseln Sie mit dem Befehl `cd` in den neuen Ordner, um die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. Installieren Sie die Node.js-Abhängigkeiten mit npm.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie der Code die Datenbankressourcen erstellt, können Sie sich die folgenden Codeausschnitte ansehen. Die Codeausschnitte stammen alle aus der Datei `uprofile.js` im Ordner `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. 

* Die Werte für Benutzername und Kennwort wurden auf der Seite „Verbindungszeichenfolge“ des Azure-Portals festgelegt. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* `client` wird mit contactPoint-Informationen initialisiert. Der Kontaktpunkt (contactPoint) wird aus dem Azure-Portal abgerufen.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* `client` stellt eine Verbindung mit der Cassandra-API von Azure Cosmos DB her.

    ```javascript
    client.connect();
    ```

* Ein neuer Keyspace wird erstellt.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* Eine neue Tabelle wird erstellt.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* Schlüssel-Wert-Entitäten werden eingefügt.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Abfrage zum Abrufen aller Schlüsselwerte.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Abfrage zum Abrufen eines Schlüsselwerts.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Schließen Sie die Verbindung. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren. Die Angabe der Verbindungszeichenfolge ermöglicht Ihrer App die Kommunikation mit Ihrer gehosteten Datenbank.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Verbindungszeichenfolge** aus. 

1. Verwenden Sie die Schaltfläche :::image type="icon" source="./media/manage-data-nodejs/copy.png"::: auf der rechten Seite des Bildschirms, um den obersten Wert (den Kontaktpunkt) zu kopieren.

    :::image type="content" source="./media/manage-data-nodejs/keys.png" alt-text="Anzeigen und Kopieren von Kontaktpunkt, Benutzername und Kennwort aus dem Azure-Portal auf der Seite „Verbindungszeichenfolge“":::

1. Öffnen Sie die Datei `config.js` . 

1. Ersetzen Sie `'CONTACT-POINT` in Zeile 9 durch den Kontaktpunktwert aus dem Portal.

    Zeile 9 sollte nun in etwa wie folgt aussehen: 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Kopieren Sie den Wert für den Benutzernamen aus dem Portal, und überschreiben Sie damit `<FillMEIN>` in Zeile 2.

    Zeile 2 sollte nun in etwa wie folgt aussehen: 

    `username: 'cosmos-db-quickstart',`

1. Kopieren Sie den Wert für das Kennwort aus dem Portal, und überschreiben Sie damit `USERNAME` in Zeile 8.

    Zeile 8 sollte nun in etwa wie folgt aussehen:

    `password: '2Ggkr662ifxz2Mg==',`

1. Ersetzen Sie REGION durch die Azure-Region, in der Sie diese Ressource erstellt haben.

1. Speichern Sie die Datei `config.js`.


## <a name="run-the-nodejs-app"></a>Ausführen der Node.js-App

1. Vergewissern Sie sich im Terminalfenster, dass Sie sich in dem zuvor geklonten Beispielverzeichnis befinden:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Führen Sie Ihre Node-Anwendung aus:

    ```bash
    npm start
    ```

4. Überprüfen Sie die Ergebnisse über die Befehlszeile.

    :::image type="content" source="./media/manage-data-nodejs/output.png" alt-text="Anzeigen und Überprüfen der Ausgabe":::

    Drücken Sie STRG+C, um die Programmausführung zu beenden und das Konsolenfenster zu schließen. 

5. Öffnen Sie im Azure-Portal den **Daten-Explorer**, um diese neuen Daten abzufragen, zu ändern und zu verwenden. 

    :::image type="content" source="./media/manage-data-nodejs/data-explorer.png" alt-text="Anzeigen der Daten im Daten-Explorer"::: 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie ein Azure Cosmos DB-Konto mit Cassandra-API erstellen und eine Cassandra-Node.js-App ausführen, die eine Cassandra-Datenbank und einen Cassandra-Container erstellt. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB: Import Cassandra data](migrate-data.md) (Azure Cosmos DB: Importieren von Cassandra-Daten)