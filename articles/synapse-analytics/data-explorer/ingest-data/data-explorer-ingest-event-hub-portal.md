---
title: Aufnehmen von Daten aus dem Event Hub im Azure Synapse Data Explorer (Vorschau)
description: In diesem Abschnitt erfahren Sie, wie Sie Daten aus dem Event Hub in Azure Synapse Data Explorer aufnehmen (laden).
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 624658dda4f78270e6e3da75920c2fe76e112fb6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478837"
---
# <a name="ingest-data-from-event-hub-into-azure-synapse-data-explorer"></a>Aufnehmen von Daten aus dem Event Hub in den Azure Synapse Data Explorer

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [1-Klick](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

Azure Synapse Data Explorer ermöglicht die Datenaufnahme (das Laden von Daten) aus Event Hubs. Dabei handelt es sich um eine Big-Data-Streaming-Plattform und einen Ereignisverarbeitungsdienst. [Event Hubs](/azure/event-hubs/event-hubs-about) kann Millionen von Ereignissen pro Sekunde nahezu in Echtzeit verarbeiten. In diesem Abschnitt erstellen Sie einen Event Hub, stellen zu ihm eine Verbindung von Azure Synapse Data Explorer aus her und zeigen den Datenfluss durch das System an.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Erstellen einer Zieltabelle, an die Event Hubs Daten senden
    1. Wählen Sie im Synapse Studio im linken Bereich **Entwickeln** aus.
    1. Wählen Sie unter **KQL-Skripts** die Option **&plus;** (Neue Ressource hinzufügen) > **KQL-Skript** aus. Im rechten Bereich können Sie Ihr Skript benennen.
    1. Wählen Sie im Menü **Verbinden mit** den Eintrag *contosodataexplorer* aus.
    1. Wählen Sie im Menü **Datenbank verwenden** die Option *TestDatenbank* aus.
    1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
        ```
    
        > [!TIP]
        > Vergewissern Sie sich, dass die Tabelle erstellt wurde. Wählen Sie im linken Fensterbereich **Daten** aus, wählen Sie das Menü mit weiteren Optionen *contosodataexplorer* aus und wählen Sie dann **Aktualisieren**. Erweitern Sie unter *contosodataexplorer* **Tabellen** und stellen Sie sicher, dass die Tabelle *Testtabelle* in der Liste angezeigt wird.

    1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.
    
        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
        ```

- Es wird empfohlen, eine [vom Benutzer zugewiesene verwaltete Identität](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity) oder eine [vom System zugewiesene verwaltete Identität](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) für die Datenverbindung zu verwenden (optional).
- [Eine Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), die Daten generiert und an einen Event Hub sendet. Laden Sie die Beispiel-App auf Ihr System herunter.
- [Visual Studio-2019](https://visualstudio.microsoft.com/vs/) zum Ausführen der Beispielapp

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-an-event-hub"></a>Erstellen eines Event Hubs

Erstellen Sie einen Event Hub, indem Sie im Azure-Portal eine Azure Resource Manager-Vorlage verwenden.

1. Verwenden Sie für die Event Hub-Erstellung die folgende Schaltfläche, um die Bereitstellung zu starten. Klicken Sie mit der rechten Maustaste, und wählen Sie **In neuem Fenster öffnen**, damit Sie die restlichen Schritte in diesem Artikel ausführen können.

    [![Schaltfläche zum Bereitstellen in Azure](../media/ingest-data-event-hub/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.eventhub%2Fevent-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Mit der Schaltfläche **In Azure bereitstellen** gelangen Sie zum Azure-Portal.

    ![Erstellen eines Event Hub-Formulars](../media/ingest-data-event-hub/deploy-to-azure.png)

1. Wählen Sie das Abonnement aus, in dem Sie den Event Hub erstellen möchten, und erstellen Sie eine Ressourcengruppe mit dem Namen *test-hub-rg*.

    ![Erstellen einer Ressourcengruppe](../media/ingest-data-event-hub/create-resource-group.png)

1. Füllen Sie das Formular mit den folgenden Informationen aus.

    Verwenden Sie für alle nicht in der folgenden Tabelle aufgeführten Einstellungen die jeweiligen Standardwerte.

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Event Hub verwenden möchten.|
    | Resource group | *test-hub-rg* | Erstellen Sie eine neue Ressourcengruppe. |
    | Position | *USA, Westen* | Wählen Sie für diesen Artikel die Option *USA, Westen* aus. Wählen Sie für ein Produktionssystem die Region aus, die Ihre Anforderungen am besten erfüllt. Erstellen Sie den Event-Hub-Namensraum am gleichen Standort wie den Azure-Synapse-Data-Explorer-Cluster, um eine optimale Leistung zu erzielen (besonders wichtig für Event Hub-Namensräume mit hohem Durchsatz).
    | Namespacename | Ein eindeutiger Namespacename | Wählen Sie einen eindeutigen Namen, der Ihren Namespace identifiziert. Beispiel: *mytestnamespace*. Der Domänenname *servicebus.windows.net* wird an den von Ihnen angegebenen Namen angefügt. Der Name darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben beginnen und mit einem Buchstaben oder einer Zahl enden. Der Wert muss zwischen 6 und 50 Zeichen umfassen.
    | Event Hub-Name | *test-hub* | Der Event Hub befindet sich unter dem Namespace, der einen eindeutigen Bereichscontainer bereitstellt. Der Name des Event Hubs muss innerhalb des Namespaces eindeutig sein. |
    | Name der Consumergruppe | *test-group* | Durch Consumergruppen können mehrere verarbeitende Anwendungen jeweils über eine separate Ansicht des Ereignisdatenstroms verfügen. |
    | | |

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Überprüfen Sie die **Zusammenfassung** der erstellten Ressourcen. Wählen Sie **Erstellen** aus, wodurch bestätigt wird, dass Sie Ressourcen in Ihrem Abonnement erstellen.

    :::image type="content" source="../media/ingest-data-event-hub/review-create.png" alt-text="Screenshot vom Azure-Portal zum Überprüfen und Erstellen eines Event-Hub-Namensraumes, Event Hubs und einer Consumergruppe.":::

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um den Bereitstellungsvorgang zu überwachen. Es kann einige Minuten dauern, bis die Bereitstellung erfolgreich abgeschlossen ist, aber Sie können jetzt mit dem nächsten Schritt fortfahren.

    ![Symbol „Benachrichtigungen“](../media/ingest-data-event-hub/notifications.png)

### <a name="authentication-considerations"></a>Überlegungen zur Authentifizierung

Abhängig vom Identitätstyp, den Sie für die Authentifizierung beim Event Hub verwenden, benötigen Sie möglicherweise einige zusätzliche Konfigurationen.

- Wenn Sie sich beim Event Hub mit einer vom Benutzer zugewiesenen verwalteten Identität authentifizieren, gehen Sie zu Ihrem Event Hub > **Netzwerk**, wählen Sie dann unter **Zugang gewähren aus** **Alle Netzwerke** und speichern Sie die Änderungen.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-all-networks.png" alt-text="Screenshot der Event-Hub-Netzwerkseite mit der Auswahl, den Zugriff auf alle Netzwerke zuzulassen.":::

- Wenn Sie sich beim Event Hub mit einer vom System zugewiesenen verwalteten Identität authentifizieren, gehen Sie zu Ihrem Event Hub > **Neztwerk** und erlauben Sie dann entweder den Zugriff von allen Netzwerken aus oder wählen Sie unter **Zugriff gewähren aus** **Ausgewählte Netzwerke** aus, wählen Sie **Vertrauenswürdigen Microsoft-Diensten diese Firewall umgehen lassen** und speichern Sie die Änderungen.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-trusted-services.png" alt-text="Screenshot der Event-Hub-Netzwerkseite mit der Auswahl, den Zugriff auf vertrauenswürdige Dienste zuzulassen.":::

## <a name="connect-to-the-event-hub"></a>Herstellen einer Verbindung mit dem Event Hub

Als Nächstes stellen Sie über den Data Explorer Pool eine Verbindung mit dem Event Hub her. Nach Einrichtung dieser Verbindung werden beim Event Hub eingehende Daten an die Tabelle gestreamt, die Sie weiter oben in diesem Artikel erstellt haben.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um zu überprüfen, ob die Bereitstellung des Event Hubs erfolgreich verlaufen ist.

1. Wählen Sie unter dem von Ihnen erstellten Data Explorer Pool **Datenbanken** > **TestDatenbank** aus.

    :::image type="content" source="../media/ingest-data-event-hub/select-test-database.png" alt-text="Auswählen der Testdatenbank":::

1. Wählen Sie **Datenverbindungen** und dann **Datenverbindung hinzufügen** aus.

    :::image type="content" source="../media/ingest-data-event-hub/event-hub-connection.png" alt-text="Wählen Sie Datenaufnahme aus und fügen Sie dann die Datenverbindung hinzu.":::

### <a name="create-a-data-connection-preview"></a>Erstellen einer Datenverbindung (Vorschau)

Füllen Sie das Formular mit den folgenden Informationen aus, und wählen Sie dann **Erstellen** aus.

:::image type="content" source="../media/ingest-data-event-hub/data-connection-pane.png" alt-text="Bereich „Datenverbindung“ in Event Hub - Azure Synapse Data Explorer.":::

**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
|---|---|---|
| Name der Datenverbindung | *test-hub-connection* | Der Name der Verbindung, die Sie in Azure Synapse Data Explorer erstellen möchten.|
| Subscription |      | Die Abonnement-ID, unter der sich die Event Hub-Ressource befindet Dieses Feld wird automatisch ausgefüllt. |
| Event Hub-Namespace | Ein eindeutiger Namespacename | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
| Event Hub | *test-hub* | Der von Ihnen erstellte Event Hub |
| Consumergruppe | *test-group* | Die Consumergruppe, die in dem von Ihnen erstellten Event Hub definiert ist |
| Ereignissystemeigenschaften | Auswählen relevanter Eigenschaften | Die [Event Hub-Systemeigenschaften](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Wenn pro Ereignisnachricht mehrere Datensätze vorhanden sind, werden die Systemeigenschaften dem ersten Datensatz hinzugefügt. Beim Hinzufügen von Systemeigenschaften [erstellen](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) oder [aktualisieren](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) Sie das Tabellenschema und die [Zuordnung](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context), um die ausgewählten Eigenschaften einzubeziehen. |
| Komprimierung | *None* | Der Komprimierungstyp der Event Hub-Nachrichtennutzlast. Unterstützte Komprimierungstypen: *Ohne, GZip*.|
| Verwaltete Identität | Systemseitig zugewiesen | Die verwaltete Identität, die von Data Explorer für den Zugriff zum Lesen im Event Hub verwendet wird.<br /><br />**Hinweis**:<br />Wenn die Datenverbindung erstellt wird:<br/>\- *Vom System zugewiesene* Identitäten werden automatisch erstellt, wenn sie nicht vorhanden sind.<br />\- Der verwalteten Identität wird automatisch die Rolle *Azure Event Hubs-Datenempfänger* zugewiesen, und sie wird Ihrem Data Explorer-Cluster hinzugefügt. Es wird empfohlen, zu überprüfen, ob die Rolle zugewiesen und die Identität dem Cluster hinzugefügt wurde. |

#### <a name="target-table"></a>Zieltabelle

Es stehen zwei Routingoptionen für erfasste Daten zur Verfügung: *statisch* und *dynamisch*.
In diesem Artikel verwenden Sie statisches Routing, für das der Tabellenname, das Datenformat und die Zuordnung als Standardwerte angegeben werden müssen. Enthält die Event Hub-Nachricht Datenroutinginformationen, überschreiben diese Routinginformationen die Standardeinstellungen.

1. Füllen Sie die folgenden Routingeinstellungen aus:

    :::image type="content" source="../media/ingest-data-event-hub/default-routing-settings.png" alt-text="Standardroutingeinstellungen für die Aufnahme von Daten in Event Hub - Azure Synapse Data Explorer.":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Tabellenname | *TestTable* | Die Tabelle, die Sie unter **TestDatabase** erstellt haben. |
    | Datenformat | *JSON* | Die unterstützen Formate sind Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO und W3CLOG. |
    | Zuordnung | *TestMapping* | Die [Zuordnung](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context), die Sie in **TestDatabase** erstellt haben, um eingehende Daten den Spaltennamen und Datentypen von **TestTable** zuzuordnen. Für „JSON“, „MULTILINE JSON“ und „AVRO“ erforderlich, für andere Formate optional.|

    > [!NOTE]
    >
    > * Sie müssen nicht alle **Standardroutingeinstellungen** angeben. Es ist auch zulässig, nur einen Teil der Einstellungen anzugeben.
    > * Nur Ereignisse, die nach dem Erstellen der Datenverbindung in die Warteschlange eingereiht werden, werden erfasst.

1. Klicken Sie auf **Erstellen**.

### <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die [Systemeigenschaften](data-explorer-ingest-event-hub-overview.md#system-properties) in das Tabellenschema und die Zuordnung einschließen.

## <a name="copy-the-connection-string"></a>Verbindungszeichenfolge kopieren

Wenn Sie die in den Voraussetzungen angegebene [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) ausführen, benötigen Sie die Verbindungszeichenfolge für den Event Hub-Namespace.

1. Wählen Sie unter dem von Ihnen erstellten Event Hub-Namespace zuerst **Freigegebene Zugriffsrichtlinien** und dann **RootManageSharedAccessKey** aus.

    ![SAS-Richtlinien](../media/ingest-data-event-hub/shared-access-policies.png)

1. Kopieren Sie den Wert für **Verbindungszeichenfolge – Primärschlüssel**. Dieser wird im nächsten Abschnitt eingefügt.

    ![Verbindungszeichenfolge.](../media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Verwenden Sie die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), die Sie heruntergeladen haben, um die Daten zu generieren.

1. Öffnen Sie die Beispiel-App-Projektmappe in Visual Studio.
1. Ändern Sie in der Datei *program.cs* die Konstante `eventHubName` in den Namen des Event Hubs und die Konstante `connectionString` in die Verbindungszeichenfolge, die Sie aus dem Event Hub-Namespace kopiert haben.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Erstellen Sie die App, und führen Sie sie aus. Die App sendet Nachrichten an den Event Hub und gibt alle zehn Sekunden den Status aus.
1. Nachdem die App einige Nachrichten gesendet hat, fahren Sie mit dem nächsten Schritt fort: Überprüfen Sie die in den Event Hub und in die Testtabelle fließenden Daten.

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

Anhand der von der App generierten Daten können Sie den Datenfluss vom Event Hub zur Tabelle in Ihrem Cluster nachvollziehen.

1. Im Azure-Portal sehen Sie unter Ihrem Event Hub den Anstieg der Aktivität während der App-Ausführung.

    ![Graph des Event Hubs.](../media/ingest-data-event-hub/event-hub-graph.png)

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```Kusto
    TestTable
    | count
    ```

1. Führen Sie die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```Kusto
    TestTable
    ```

    Das Resultset sollte wie auf der folgenden Abbildung aussehen:

    ![Nachrichten-Resultset](../media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    >
    > * Azure Synapse Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist. Die Standardrichtlinie für die Batchverarbeitung ist so konfiguriert, dass ein Batch versiegelt wird, wenn eine der folgenden Bedingungen für den Batch zutrifft: eine maximale Verzögerungszeit von 5 Minuten, eine Gesamtgröße von 1 G oder 1.000 Blobs. Daher kann es zu Wartezeit kommen. Weitere Informationen finden Sie unter [Batching-Richtlinie](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context).
    > * Die Event Hub-Erfassung beinhaltet die Event Hub-Antwortzeit von zehn Sekunden oder 1 MB.
    > * Um die Verzögerung bei der Antwortzeit zu reduzieren, konfigurieren Sie Ihre Tabelle für die Unterstützung von Streaming. Weitere Informationen finden Sie unter [Streaming ingestion policy](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context) (Richtlinie für die Streamingerfassung).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht vorhaben, Ihren Event Hub erneut zu verwenden, bereinigen Sie **test-hub-rg**, um Kosten zu vermeiden.

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe.

    Wenn das linke Menü reduziert ist, wählen Sie ![Schaltfläche „Erweitern“](../media/ingest-data-event-hub/expand.png) , um es zu erweitern.

   ![Auswählen der zu löschenden Ressourcengruppe](../media/ingest-data-event-hub/delete-resources-select.png)

1. Wählen Sie unter **test-resource-group** die Option **Ressourcengruppe löschen** aus.

1. Geben Sie im neuen Fenster den Namen der zu löschenden Ressourcengruppe (*test-hub-rg*) ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Analysieren mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data-Explorer-Pools](../data-explorer-monitor-pools.md)
