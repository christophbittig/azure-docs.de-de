---
title: 'Schnellstart: Senden von benutzerdefinierten Ereignissen an eine Azure-Funktion: Event Grid'
description: 'Schnellstart: Verwenden Sie Azure Event Grid und die Azure CLI oder das Portal, um ein Thema zu veröffentlichen und dieses Ereignis zu abonnieren. Als Endpunkt wird eine Azure-Funktion verwendet.'
ms.date: 09/28/2021
ms.topic: quickstart
ms.openlocfilehash: c6f0377b1b6489927de6a980bf4fc1ecb080e051
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277489"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Schnellstart: Weiterleiten von benutzerdefinierten Ereignissen an eine Azure-Funktion mit Event Grid

Azure Event Grid ist ein Ereignisdienst für die Cloud. Azure Functions ist einer der unterstützten Ereignishandler. In diesem Artikel erstellen Sie über das Azure-Portal ein benutzerdefiniertes Thema, abonnieren dieses benutzerdefinierte Thema und lösen das Ereignis zum Anzeigen des Ergebnisses aus. Sie senden Ereignisse an eine Azure-Funktion.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure-Funktion erstellen
Vor dem Abonnieren des benutzerdefinierten Themas erstellen wir zunächst eine Funktion zur Verarbeitung der Ereignisse. 

1. Erstellen Sie eine Funktions-App mithilfe der Anweisungen unter [Erstellen einer Funktions-App](../azure-functions/functions-get-started.md).
1. Wählen Sie auf der Seite **Funktions-App** im Menü auf der linken Seite die Option **Funktionen** aus. 
1. Wählen Sie in der Symbolleiste die Option **+ Erstellen** aus, um eine Funktion zu erstellen.
1. Führen Sie auf der Seite **Funktion erstellen** die folgenden Schritte aus:
    1. Dieser Schritt ist optional. Wählen Sie unter **Entwicklungsumgebung** die Entwicklungsumgebung aus, die Sie für den Funktionscode verwenden möchten. 
    1. Wählen Sie im Abschnitt **Vorlage auswählen** die Option **Azure Event Grid-Trigger** aus. 
    1. Geben Sie einen Namen für die Funktion ein. In diesem Beispiel ist dies **HandleEventsFunc**. 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" lightbox="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Auswählen des Event Grid-Triggers":::
4. Verwenden Sie die Seite **Code und testen**, um den vorhandenen Code für die Funktion anzuzeigen und zu aktualisieren. 

    :::image type="content" source="./media/custom-event-to-function/function-code-test-menu.png" alt-text="Abbildung: Auswahl des Menüs „Code und testen“ für eine Azure-Funktion":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Erstellen eines benutzerdefinierten Themas

Ein Event Grid-Thema verfügt über einen benutzerdefinierten Endpunkt für die Veröffentlichung Ihrer Ereignisse. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus, suchen Sie nach **Event Grid**, und wählen Sie **Event Grid-Themen** aus. 

    :::image type="content" source="./media/custom-event-to-function/select-event-grid-topics.png" alt-text="Abbildung: Auswahl von „Event Grid-Themen“":::
3. Wählen Sie auf der Symbolleiste der Seite **Event Grid-Themen** die Option **+ Hinzufügen** aus. 

    :::image type="content" source="./media/custom-event-to-function/add-event-grid-topic-button.png" alt-text="Abbildung: Schaltfläche „Erstellen“ zum Erstellen eines Event Grid-Themas":::
4. Gehen Sie auf der Seite **Thema erstellen** wie folgt vor:
    1. Wählen Sie Ihr Azure-**Abonnement** aus.
    2. Wählen Sie dieselbe **Ressourcengruppe** wie in den vorherigen Schritten aus.
    3. Geben Sie unter **Name** einen eindeutigen Namen für das benutzerdefinierte Thema an. Der Name des Themas muss eindeutig sein, da er durch einen DNS-Eintrag dargestellt wird. Verwenden Sie nicht den Namen, der in der Abbildung zu sehen ist. Erstellen Sie stattdessen einen eigenen Namen. Dieser muss zwischen drei und 50 Zeichen lang sein und darf nur folgende Zeichen enthalten: a–z, A–Z, 0–9 und Bindestriche (-).
    4. Wählen Sie einen **Standort** für das Event Grid-Thema aus.
    5. Klicken Sie auf **Überprüfen + erstellen**. 
    
        :::image type="content" source="./media/custom-event-to-function/create-custom-topic.png" alt-text="Abbildung: Seite „Thema erstellen“":::      
    1. Überprüfen Sie die Einstellungen auf der Seite **Überprüfen und erstellen**, und wählen Sie **Erstellen** aus. 
5. Wählen Sie nach der Erstellung des benutzerdefinierten Themas den Link **Zu Ressource wechseln** aus, um für das von Ihnen erstellte Thema die folgende Seite „Event Grid-Thema“ anzuzeigen. 

    :::image type="content" source="./media/custom-event-to-function/event-grid-topic-home-page.png" alt-text="Abbildung: Startseite für Ihr benutzerdefiniertes Event Grid-Thema":::

## <a name="subscribe-to-custom-topic"></a>Abonnieren eines benutzerdefinierten Themas

Sie abonnieren ein Event Grid-Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen.

1. Wählen Sie nun auf der Symbolleiste der Seite **Event Grid-Thema** für Ihr benutzerdefiniertes Thema die Option **+ Ereignisabonnement** aus.

    :::image type="content" source="./media/custom-event-to-function/new-event-subscription.png" alt-text="Abbildung: Auswahl von „Ereignisabonnement hinzufügen“ in der Symbolleiste":::
2. Gehen Sie auf der Seite **Ereignisabonnement erstellen** wie folgt vor:
    1. Geben Sie unter **Name** einen Namen für das Ereignisabonnement an.
    3. Wählen Sie **Azure-Funktion** als **Endpunkttyp** aus. 
    4. Wählen Sie **Endpunkt auswählen** aus. 

        :::image type="content" source="./media/custom-event-to-function/provide-subscription-values.png" alt-text="Abbildung: Werte für Ereignisabonnement":::
    5. Wählen Sie als Endpunkt der Funktion das Azure-Abonnement und die Ressourcengruppe aus, in der sich Ihre Funktions-App befindet, und wählen Sie dann die zuvor erstellte Funktions-App und Funktion aus. Klicken Sie auf **Auswahl bestätigen**.

        :::image type="content" source="./media/custom-event-to-function/provide-endpoint.png" alt-text="Abbildung: Seite „Azure-Funktion auswählen“ mit Auswahl der von Ihnen erstellten Funktion":::
    6. Dieser Schritt ist optional, wird aber für Produktionsszenarien empfohlen. Navigieren Sie auf der Seite **Ereignisabonnement erstellen** zur Registerkarte **Erweiterte Funktionen**, und legen Sie Werte für **Max. Anzahl von Ereignissen pro Batch** und **Bevorzugte Batchgröße in KB** fest. 
    
        Durch die Batchverarbeitung kann ein hoher Durchsatz erreicht werden. Legen Sie für **Max. Anzahl von Ereignissen pro Batch** die maximale Anzahl von Ereignissen fest, die ein Abonnement in einen Batch einschließen soll. „Bevorzugte Batchgröße“ legt die bevorzugte Obergrenze der Batchgröße in KB fest, kann jedoch überschritten werden, wenn ein einzelnes Ereignis größer als dieser Schwellenwert ist.
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Abbildung: Batcheinstellungen für ein Ereignisabonnement":::
    6. Wählen Sie auf der Seite **Ereignisabonnement erstellen** die Option **Erstellen** aus.

## <a name="send-an-event-to-your-topic"></a>Senden eines Ereignisses an Ihr Thema

Als Nächstes lösen wir ein Ereignis aus, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Verwenden Sie die Azure-Befehlszeilenschnittstelle oder PowerShell, um ein Testereignis an Ihr benutzerdefiniertes Thema zu senden. Üblicherweise werden die Ereignisdaten von einer Anwendung oder einem Azure-Dienst gesendet.

Im ersten Beispiel wird die Azure-Befehlszeilenschnittstelle verwendet. Es ruft die URL und den Schlüssel für das benutzerdefinierte Thema sowie Beispielereignisdaten ab. Verwenden Sie für `<topic name>` den Namen Ihres benutzerdefinierten Themas. Es werden Beispielereignisdaten erstellt. Bei dem `data`-Element des JSON-Codes handelt es sich um die Nutzlast Ihres Ereignisses. Für dieses Feld kann ein beliebiger wohlgeformter JSON-Code verwendet werden. Sie können auch das Betrefffeld zur erweiterten Weiterleitung und Filterung verwenden. CURL ist ein Hilfsprogramm zum Senden von HTTP-Anforderungen.


### <a name="azure-cli"></a>Azure CLI
1. Wählen Sie im Azure-Portal die Option **Cloud Shell** aus. Wählen Sie links oben im Cloud Shell-Fenster die Option **Bash** aus. 

    :::image type="content" source="./media/custom-event-quickstart-portal/cloud-shell-bash.png" alt-text="Abbildung: Cloud Shell – Bash-Fenster":::
1. Führen Sie den folgenden Befehl aus, um den **Endpunkt** für das Thema abzurufen: Aktualisieren Sie nach dem Kopieren und Einfügen des Befehls den Namen des Themas (**topic name**) und den Namen der Ressourcengruppe (**resource group name**), bevor Sie den Befehl ausführen. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Führen Sie den folgenden Befehl aus, um den **Schlüssel** für das benutzerdefinierte Thema abzurufen: Aktualisieren Sie nach dem Kopieren und Einfügen des Befehls den Namen des Themas (**topic name**) und den Namen der Ressourcengruppe (**resource group name**), bevor Sie den Befehl ausführen. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopieren Sie die folgende Anweisung mit der Ereignisdefinition, und drücken Sie die **EINGABETASTE**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Führen Sie den folgenden **Curl**-Befehl aus, um das Ereignis zu veröffentlichen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Im zweiten Beispiel werden ähnliche Schritte mit PowerShell ausgeführt.

1. Wählen Sie im Azure-Portal die Option **Cloud Shell** aus (navigieren Sie alternativ zu `https://shell.azure.com/`). Wählen Sie links oben im Cloud Shell-Fenster die Option **PowerShell** aus. Eine Beispielabbildung des Fensters **Cloud Shell** finden Sie im Abschnitt für die Azure-Befehlszeilenschnittstelle.
2. Legen Sie die folgenden Variablen fest. Aktualisieren Sie nach dem Kopieren und Einfügen der einzelnen Befehle den Namen des Themas (**topic name**) und den Namen der Ressourcengruppe (**resource group name**), bevor Sie den Befehl ausführen:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Führen Sie die folgenden Befehle aus, um den **Endpunkt** und die **Schlüssel** für das Thema abzurufen:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Bereiten Sie das Ereignis vor. Kopieren Sie die Anweisungen, und führen Sie sie im Cloud Shell-Fenster aus. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Verwenden Sie das Cmdlet **Invoke-WebRequest**, um das Ereignis zu senden. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-that-function-received-the-event"></a>Überprüfen, ob die Funktion das Ereignis empfangen hat
Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Navigieren Sie zu Ihrer ausgelösten Event Grid-Funktion, und öffnen Sie die Protokolle. In den Protokollen sollte eine Kopie der Datennutzlast des Ereignisses angezeigt werden. Ist dies nicht der Fall, achten Sie darauf, das Protokollfenster zuerst zu öffnen, oder klicken Sie auf „Erneute Verbindungsherstellung“, und versuchen Sie dann erneut, ein Testereignis zu senden.

:::image type="content" source="./media/custom-event-to-function/successful-function.png" alt-text="Abbildung: Ansicht „Überwachung“ der Azure-Funktion mit einem Protokoll":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie dieses Ereignis weiterverwenden möchten, können Sie die Bereinigung der in diesem Artikel erstellten Ressourcen überspringen. Löschen Sie andernfalls die Ressourcen, die Sie in diesem Artikel erstellt haben.

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus. Sollte die Option nicht angezeigt werden, wählen Sie im linken Menü die Option **Alle Dienste** und anschließend **Ressourcengruppen** aus. 
2. Wählen Sie die Ressourcengruppe aus, um die Seite **Ressourcengruppe** zu öffnen. 
3. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus. 
4. Bestätigen Sie den Löschvorgang, indem Sie den Namen der Ressourcengruppe eingeben und **Löschen** auswählen. 

    ![Ressourcengruppen](./media/custom-event-to-function/delete-resource-groups.png)

    Die andere Ressourcengruppe, die auf der Abbildung zu sehen ist, wurde vom Cloud Shell-Fensters erstellt und verwendet. Löschen Sie sie, falls Sie das Cloud Shell-Fenster nicht mehr benötigen. 

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich ausführlicher darüber informieren, welche Möglichkeiten Event Grid bietet:

- [Einführung in Azure Event Grid](overview.md)
- [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Überwachen von Änderungen an virtuellen Computern mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md)

In den folgenden Beispielen erfahren Sie, wie Sie Ereignisse in Event Grid mithilfe verschiedener Programmiersprachen veröffentlichen und nutzen: 

- [Azure Event Grid-Beispiele für .NET](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Azure Event Grid-Beispiele für Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Azure Event Grid-Beispiele für Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [Azure Event Grid-Beispiele für JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [Azure Event Grid-Beispiele für TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
