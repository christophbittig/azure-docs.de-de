---
title: 'Schnellstart: Erstellen einer Azure Cosmo DB-Ressource über das Azure-Portal'
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie eine Azure Cosmos-Datenbank, einen Container sowie Elemente über das Azure-Portal erstellen.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/26/2021
ms.openlocfilehash: 1249976a38163024d8e25f46fd80cd0fa770a79b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117616"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Cosmos-Kontos, einer Datenbank, eines Containers sowie von Elementen mit dem Azure-Portal
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können Azure Cosmos DB verwenden, um schnell Schlüssel/Wert-, Dokument- und Graph-Datenbanken zu erstellen und abzufragen, und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mithilfe des Azure-Portals ein [SQL-API](../introduction.md)-Konto, eine Dokumentdatenbank und einen Container für Azure Cosmos DB erstellen und dem Container Daten hinzufügen. 

## <a name="prerequisites"></a>Voraussetzungen

Azure-Abonnement oder kostenloses Azure Cosmos DB-Testkonto
- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Hinzufügen einer Datenbank und eines Containers 

Sie können mit dem Daten-Explorer im Azure-Portal eine Datenbank und einen Container erstellen.

1. Wählen Sie auf der Azure Cosmos DB-Kontoseite im linken Navigationsbereich die Option **Daten-Explorer** und dann **Neuer Container** aus. 

    Unter Umständen müssen Sie nach rechts scrollen, um das Fenster **Container hinzufügen** anzuzeigen.

    :::image type="content" source="./media/create-cosmosdb-resources-portal/add-database-container.png" alt-text="Daten-Explorer im Azure-Portal, Bereich „Container hinzufügen“":::

1. Geben Sie im Bereich **Container hinzufügen** die Einstellungen für den neuen Container ein.

    |Einstellung|Vorgeschlagener Wert|BESCHREIBUNG
    |---|---|---|
    |**Datenbank-ID**|ToDoList|Geben Sie *ToDoList* als Namen für die neue Datenbank ein. Datenbanknamen müssen zwischen 1 und 255 Zeichen lang sein und dürfen weder `/, \\, #, ?` noch nachgestellte Leerzeichen enthalten. Aktivieren Sie die Option **Durchsatz über Container hinweg teilen**. Diese Option ermöglicht es Ihnen, den für die Datenbank bereitgestellten Durchsatz auf alle Container in der Datenbank zu verteilen. Darüber hinaus hilft sie Ihnen dabei, Kosten zu sparen. |
    | **Datenbank-Durchsatz**| Sie können **automatische Skalierung** oder **manuellen** Durchsatz bereitstellen. Mit dem manuellen Durchsatz können Sie RU/s selbst skalieren, während der durch autoskalierungsbasierte Durchsatz dem System ermöglicht, RU/s basierend auf der Nutzung zu skalieren. Wählen Sie für dieses Beispiel **Manuell** aus. <br><br> Belassen Sie den Durchsatz bei 400 Anforderungseinheiten pro Sekunde (RU/s). Wenn Sie die Latenz reduzieren möchten, können Sie den Durchsatz später hochskalieren, indem Sie die erforderlichen RU/s mit dem [Kapazitätsrechner](estimate-ru-with-capacity-planner.md) schätzen.<br><br>**Hinweis**: Diese Einstellung ist nicht verfügbar, wenn ein neuer Container in einem serverlosen Konto erstellt wird. |
    |**Container-ID**|Items|Geben Sie *Items* als Namen für den neuen Container ein. Für Container-IDs gelten dieselben Zeichenanforderungen wie für Datenbanknamen.|
    |**Partitionsschlüssel**| /category| Das in diesem Artikel beschriebene Beispiel verwendet */category* als Partitionsschlüssel.|

    Fügen Sie für dieses Beispiel keine **eindeutigen Schlüssel** hinzu, oder aktivieren Sie den **Analysespeicher** nicht. Mit eindeutigen Schlüsseln können Sie die Datenbank mit einer Datenintegritätsebene versehen, indem Sie die Eindeutigkeit von Werten per Partitionsschlüssel sicherstellen. Weitere Informationen finden Sie unter [Eindeutige Schlüssel in Azure Cosmos DB.](../unique-keys.md) [Der Analysespeicher](../analytical-store-introduction.md) wird verwendet, um umfangreiche Analysen für operative Daten ohne Auswirkungen auf Ihre Transaktionsworkloads zu ermöglichen.

1. Klicken Sie auf **OK**. Im Daten-Explorer werden die neue Datenbank und der Container angezeigt, die Sie erstellt haben.

## <a name="add-data-to-your-database"></a>Hinzufügen von Daten zu Ihrer Datenbank

Verwenden Sie den Daten-Explorer, um Ihrer neuen Datenbank Daten hinzuzufügen.

1. Erweitern Sie im **Daten-Explorer** die Datenbank **ToDoList** und dann den Container **Elemente**. Wählen Sie als Nächstes **Elemente** und dann **Neues Element** aus. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Erstellen neuer Dokumente im Daten-Explorer im Azure-Portal":::
   
1. Fügen Sie dem Dokument rechts im Bereich **Dokumente** die folgende Struktur hinzu:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Wählen Sie **Speichern** aus.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Einfügen von JSON-Daten und Auswählen von „Speichern“ im Daten-Explorer im Azure-Portal":::
   
1. Wählen Sie erneut **Neues Dokument**, und erstellen und speichern Sie ein weiteres Dokument mit einer eindeutigen `id` und weiteren gewünschten Eigenschaften und Werten. Ihre Dokumente können eine beliebige Struktur aufweisen, da Azure Cosmos DB kein Schema für Ihre Daten vorgibt.

## <a name="query-your-data"></a>Abfragen Ihrer Daten

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

Wenn Sie nur die Datenbank löschen und in Zukunft das Azure Cosmos-Konto verwenden möchten, können Sie zum Löschen der Datenbank die folgenden Schritte ausführen:

* Navigieren Sie zu Ihrem Azure Cosmos-Konto.
* Öffnen Sie **Daten-Explorer**, klicken Sie mit der rechten Maustaste auf die zu löschende Datenbank, und wählen Sie **Datenbank löschen** aus.
* Geben Sie die ID bzw. den Namen der Datenbank ein, um den Löschvorgang zu bestätigen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto, eine Datenbank und einen Container mit dem Daten-Explorer erstellen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md). 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](../import-data.md)