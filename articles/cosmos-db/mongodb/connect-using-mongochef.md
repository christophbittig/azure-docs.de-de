---
title: Verwenden von Studio 3T zum Herstellen einer Verbindung mit der API für MongoDB von Azure Cosmos DB
description: Erfahren Sie, wie Sie eine Verbindung mit einer Azure Cosmos DB-API für MongoDB mithilfe von Studio 3T herstellen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: seodec18
ms.openlocfilehash: aab468bbc53eec0d6caacc02956b58080f430031
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029328"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Herstellen einer Verbindung mit einem Azure Cosmos-Konto mithilfe von Studio 3T
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Um eine Verbindung mit einer Azure Cosmos DB-API für MongoDB mithilfe von Studio 3T herzustellen, müssen Sie:

* [Studio 3T](https://studio3t.com/) herunterladen und installieren.
* Rufen Sie die Informationen zur [Verbindungszeichenfolge](connect-mongodb-account.md) für Ihr Azure Cosmos-Konto ab.

## <a name="create-the-connection-in-studio-3t"></a>Sie müssen die Verbindung in Studio 3T herstellen

Führen Sie die folgenden Schritte aus, um dem Studio 3T-Verbindungs-Manager Ihrem Azure Cosmos-Konto hinzuzufügen:

1. Rufen Sie die Verbindungsinformationen Ihrer Azure Cosmos DB-API für das MongoDB-Konto ab, indem Sie den Anweisungen im Artikel [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md) folgen.

    :::image type="content" source="./media/connect-using-mongochef/connection-string-blade.png" alt-text="Screenshot der Seite „Verbindungszeichenfolge“":::

2. Klicken Sie auf **Connect** (Verbinden), um den Verbindungs-Manager zu öffnen, und klicken Sie anschließend auf **New Connection** (Neue Verbindung).

    :::image type="content" source="./media/connect-using-mongochef/connection-manager.png" alt-text="Screenshot des Studio 3T-Verbindungs-Managers mit hervorgehobener Schaltfläche für eine neue Verbindung":::
3. Geben Sie im Fenster **Neue Verbindung** auf der Registerkarte **Server** den HOST (FQDN) des Azure Cosmos-Kontos sowie den PORT ein.

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-server-tab.png" alt-text="Screenshot der Serverregisterkarte im Verbindungs-Manager von Studio 3T":::
4. Wählen Sie im Fenster **New Connection** (Neue Verbindung) auf der Registerkarte **Authentication** (Authentifizierung) den Authentifizierungsmodus **Basic (MONGODB-CR or SCRAM-SHA-1)** (Einfach (MONGODB-CR oder SCRAM-SHA-1)) aus, und geben Sie Benutzername und Kennwort ein.  Übernehmen Sie den Standardwert für Authentifizierungs-DB („admin“), oder geben Sie einen eigenen Wert ein.

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-authentication-tab.png" alt-text="Screenshot der Authentifizierungsregisterkarte im Studio 3T-Verbindungs-Manager":::
5. Aktivieren Sie im Fenster **New Connection** (Neue Verbindung) auf der Registerkarte **SSL** das Kontrollkästchen **Use SSL protocol to connect** (Verbindung über SSL-Protokoll herstellen) und das Optionsfeld **Accept server self-signed SSL certificates** (Selbstsignierte SSL-Serverzertifikate akzeptieren).

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-ssl-tab.png" alt-text="Screenshot der SSL-Registerkarte im Verbindungs-Manager von Studio 3T":::
6. Klicken Sie auf die Schaltfläche **Test Connection** (Verbindung testen), um die Verbindungsinformationen zu überprüfen. Klicken Sie auf **OK**, um zum Fenster für neue Verbindungen zurückzukehren, und klicken Sie anschließend auf **Save** (Speichern).

    :::image type="content" source="./media/connect-using-mongochef/test-connection-results.png" alt-text="Screenshot des Fensters „Test Connection“ (Verbindung testen) von Studio 3T":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Verwenden von Studio 3T zum Erstellen von Datenbanken, Sammlungen und Dokumenten
Führen Sie die folgenden Schritte aus, um Datenbanken, Sammlungen und Dokumente mithilfe von Studio 3T zu erstellen:

1. Markieren Sie die Verbindung im **Verbindungs-Manager**, und klicken Sie auf **Connect** (Verbinden).

    :::image type="content" source="./media/connect-using-mongochef/connect-account.png" alt-text="Screenshot des Verbindungs-Managers von Studio 3T":::
2. Klicken Sie mit der rechten Maustaste auf den Host, und wählen Sie **Add Database**(Datenbank hinzufügen) aus.  Geben Sie einen Datenbanknamen an, und klicken Sie auf **OK**.

    :::image type="content" source="./media/connect-using-mongochef/add-database.png" alt-text="Screenshot der Studio 3T-Option „Add Database“ (Datenbank hinzufügen)":::
3. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Add Collection**(Sammlung hinzufügen) aus.  Geben Sie einen Sammlungsnamen an, und klicken Sie auf **Create**(Erstellen).

    :::image type="content" source="./media/connect-using-mongochef/add-collection.png" alt-text="Screenshot der Studio 3T-Option „Add Collection“ (Sammlung hinzufügen)":::
4. Klicken Sie auf das Menüelement **Collection** (Sammlung) und anschließend auf **Add Document** (Dokument hinzufügen).

    :::image type="content" source="./media/connect-using-mongochef/add-document.png" alt-text="Screenshot des Studio 3T-Menüelements „Add Document“ (Dokument hinzufügen)":::
5. Fügen Sie im Dialogfeld zum Hinzufügen von Dokumenten Folgendes ein, und klicken Sie dann auf **Add Document**(Dokument hinzufügen).

    ```json
    {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
            {
                "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
                "pets": [{ "givenName": "Fluffy" }]
            }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. Fügen Sie ein weiteres Dokument mit dem folgenden Inhalt hinzu:

    ```json
    {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                "givenName": "Lisa",
                "gender": "female",
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }
    ```

7. Führen Sie eine Beispielabfrage aus. Suchen Sie z.B. nach Familien mit dem Nachnamen „Andersen“, und geben Sie übergeordnete Felder und Felder mit Angaben zum Staat zurück.

    :::image type="content" source="./media/connect-using-mongochef/query-document.png" alt-text="Screenshot der MongoChef-Abfrageergebnisse":::

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Robo 3T](connect-using-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](nodejs-console-app.md) mit der API für MongoDB von Azure Cosmos DB.
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)