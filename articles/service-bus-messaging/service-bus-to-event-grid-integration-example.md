---
title: Verarbeiten von Service Bus-Ereignissen per Event Grid mit Azure Logic Apps
description: Dieser Artikel enthält Schritte zum Verarbeiten von Service Bus-Ereignissen per Event Grid mit Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e5089985b56ad271f3de41fc3c68f091beddffb
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457197"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Tutorial: Reagieren auf über Azure Event Grid empfangene Azure Service Bus-Ereignisse mit Azure Logic Apps
In diesem Tutorial erfahren Sie, wie Sie mit Azure Logic Apps auf Azure Service Bus-Ereignisse reagieren, die über Azure Event Grid empfangen wurden. 

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Empfangen von Nachrichten mithilfe von Logik-Apps
In diesem Schritt erstellen Sie eine Azure-Logik-App, die Service Bus-Ereignisse über Azure Event Grid empfängt. 

1. Erstellen Sie im Azure-Portal eine Logik-App.
    1. Wählen Sie **+Ressource erstellen**, dann **Integration** und schließlich **Logik-App** aus. 
    2. Wählen Sie Ihr Azure- **Abonnement** aus. 
    3. Wählen Sie für die **Ressourcengruppe** **Vorhandene verwenden** aus, und wählen Sie die Ressourcengruppe aus, die Sie für andere Ressourcen (wie Azure-Funktion, Service Bus-Namespace) verwendet haben, die Sie zuvor erstellt haben. 
    1. Wählen Sie unter **Typ** die Option **Verbrauch** aus. 
    1. Geben Sie einen **Namen** für die Logik-App ein.
    1. Wählen Sie die **Region** für die Logik-App aus. 
    1. Klicken Sie auf **Überprüfen + erstellen**. 
    1. Wählen Sie auf der Seite **Überprüfen und erstellen** die Option **Erstellen** aus, um die Logik-App zu erstellen. 
    1. Wählen Sie auf der Seite **Deployment complete** (Bereitstellung abgeschlossen) die Option **Zu Ressource wechseln** aus. 
1. Wählen Sie auf der Seite **Designer für Logik-Apps** unter **Vorlagen** den Eintrag **Leere Logik-App** aus. 

### <a name="add-a-step-receive-messages-from-service-bus-via-event-grid"></a>Hinzufügen eines Schritts zum Empfangen von Nachrichten von Service Bus über Event Grid
1. Führen Sie im Designer folgende Schritte aus:
    1. Suchen Sie nach **Event Grid**. 
    2. Wählen Sie **Wenn ein Ressourcenereignis eintritt – Azure Event Grid** aus. 

        ![Designer für Logik-Apps: Event Grid-Trigger auswählen](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wählen Sie **Anmelden** aus, geben Sie Ihre Azure-Anmeldeinformationen ein, und wählen Sie **Zugriff zulassen** aus. 
5. Führen Sie auf der Seite **Bei Auftreten eines Ressourcenereignisses** die folgenden Schritte aus:
    1. Wählen Sie Ihr Azure-Abonnement. 
    2. Wählen Sie für **Ressourcentyp** den Wert **Microsoft.ServiceBus.Namespaces** aus. 
    3. Wählen Sie für **Ressourcenname** Ihren Service Bus-Namespace aus. 
    4. Wählen Sie **Neuen Parameter hinzufügen** > **Suffixfilter** aus, und verschieben Sie dann den Fokus auf eine Stelle außerhalb der Dropdownliste.
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/add-new-parameter-suffix-filter.png" alt-text="Abbildung: Hinzufügen eines Suffixfilters":::
    1. Geben Sie für **Suffixfilter** den Namen Ihres Service Bus-Themenabonnements ein. 
        ![Designer für Logik-Apps: Ereignis konfigurieren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
1. Wählen Sie im Designer **+ Neuer Schritt** aus, und führen Sie die folgenden Schritte aus:
    1. Suchen Sie nach **Service Bus**.
    2. Wählen Sie in der Liste **Service Bus** aus. 
    3. Wählen Sie in der Liste **Aktionen** den Eintrag **Nachrichten abrufen** aus. 
    4. Wählen Sie **Ruft Nachrichten aus einem Themenabonnement ab (Peek-Lock)** aus. 

        ![Designer für Logik-Apps: Aktion „Nachrichten abrufen“](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Geben Sie einen **Namen für die Verbindung** ein. Beispiel: **Rufen Sie Nachrichten aus einem Themenabonnement ab**, und wählen Sie den Service Bus-Namespace aus. 

        ![Designer für Logik-Apps: Service Bus-Namespace auswählen](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wählen Sie **RootManageSharedAccessKey** und dann **Erstellen** aus.

        ![Designer für Logik-Apps: Freigegebenen Zugriffsschlüssel auswählen](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Wählen Sie Ihr **Thema** und **Abonnement** aus. 
    
        ![Screenshot, der zeigt, wo Sie Ihr Thema und Ihr Abonnement auswählen.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)

### <a name="add-a-step-to-process-and-complete-received-messages"></a>Hinzufügen eines Schritts zum Verarbeiten und Abschließen empfangener Nachrichten
In diesem Schritt fügen Sie Schritte hinzu, um die empfangene Nachricht in einer E-Mail zu senden und die Nachricht dann abzuschließen. In einem realen Szenario verarbeiten Sie eine Nachricht in der Logik-App, bevor Sie die Nachricht abschließen.

#### <a name="add-a-foreach-loop"></a>Hinzufügen einer ForEach-Schleife
1. Wählen Sie **+ Neuer Schritt** aus.
1. Suchen Sie nach **Steuerung**, und wählen Sie anschließend diese Kategorie aus.  

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-control.png" alt-text="Abbildung der Auswahl der Kategorie „Steuerung“":::
1. Wählen Sie in der Liste **Aktionen** die Aktion **For each** aus.

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-for-each.png" alt-text="Abbildung der Auswahl der Steuerung „For each“":::    
1. Wählen Sie für **Select an output from previous steps** („Ausgabe aus vorherigen Schritten auswählen“) (klicken Sie ggf. in das Textfeld) die Option **Body** („Text“) unter **Ruft Nachrichten aus einem Themenabonnement ab (Peek-Lock)** aus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-input-for-each.png" alt-text="Abbildung der Auswahl der Eingabe für „For each“":::    

#### <a name="add-a-step-inside-the-foreach-loop-to-send-an-email-with-the-message-body"></a>Hinzufügen eines Schritts innerhalb der ForEach-Schleife zum Senden einer E-Mail mit dem Nachrichtentext

1. Wählen Sie innerhalb der **ForEach**-Schleife den Befehl **Aktion hinzufügen** aus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/select-add-action.png" alt-text="Abbildung der Auswahl der Schaltfläche „Aktion hinzufügen“ innerhalb der ForEach-Schleife":::        
1. Geben Sie in das Textfeld **Connectors und Aktionen durchsuchen** den Eintrag **Office 365** ein. 
1. Wählen Sie in den Suchergebnissen **Office 365 Outlook** aus. 
1. Wählen Sie in der Liste der Aktionen **E-Mail senden (V2)** aus. 
1. Gehen Sie im Fenster **E-Mail senden (V2)** wie folgt vor: 
1. Wählen Sie innerhalb des Textfelds einen Text für **Body** (Text) aus, und führen Sie die folgenden Schritte aus:
    1. Für **To** („An“) geben Sie eine E-Mail-Adresse ein. 
    1. Für **Subject** („Betreff“) geben Sie **Vom Service Bus-Themenabonnement empfangene Nachricht** ein.  
    1. Wechseln Sie zu **Ausdruck**.
    1. Geben Sie den folgenden Ausdruck ein:
    
        ```
        base64ToString(items('For_each')?['ContentData'])
        ``` 
    1. Klicken Sie auf **OK**. 
    
        :::image type="content" source="./media/service-bus-to-event-grid-integration-example/specify-expression-email.png" alt-text="Abbildung: Ausdruck für Text der Aktivität „E-Mail senden“":::

#### <a name="add-another-action-in-the-foreach-loop-to-complete-the-message"></a>Hinzufügen einer weiteren Aktion in der ForEach-Schleife zum Abschließen der Nachricht         
1. Wählen Sie innerhalb der **ForEach**-Schleife den Befehl **Aktion hinzufügen** aus. 
    1. Wählen Sie **Service Bus** in der Liste **Recent** („Zuletzt verwendet“) aus.
    2. Wählen Sie in der Liste der Aktionen **Nachricht in einem Themenabonnement abschließen** aus. 
    3. Wählen Sie Ihr Service Bus-**Thema** aus.
    4. Wählen Sie ein **Abonnement** für das Thema aus.
    5. Wählen Sie für **Sperrtoken der Nachricht** die Option **Sperrtoken** aus dem **Dynamischen Inhalt** aus. 

        ![Logik-App-Designer: Vervollständigen der Nachricht](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wählen Sie in der Symbolleiste des Designers für Logik-Apps **Speichern** aus, um die Logik-App zu speichern. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Speichern der Logik-App":::

## <a name="test-the-app"></a>Testen der App
1. Falls Sie noch keine Testnachrichten an das Thema gesendet haben, sollten Sie die Anleitung im Abschnitt [Senden von Nachricht an das Service Bus-Thema](#send-messages-to-the-service-bus-topic) befolgen. 
1. Wechseln Sie zur Seite **Übersicht** Ihrer Logik-App und dann im unteren Bereich zur Registerkarte **Ausführungsverlauf**. Sie sehen die an das Thema gesendeten Nachrichten zu Logik-App-Ausführungen. Es kann einige Minuten dauern, bis die Ausführungen der Logik-App angezeigt werden. Wählen Sie in der Symbolleiste die Option **Aktualisieren** aus, um die Seite zu aktualisieren. 

    ![Designer für Logik-Apps – Logik-App wird ausgeführt](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Wählen Sie eine Ausführung der Logik-App aus, um die Details dafür anzuzeigen. Beachten Sie, dass in der for-Schleife fünf Nachrichten verarbeitet wurden. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Details der Logik-App-Ausführung"::: 
2. Sie sollten eine E-Mail für jede Nachricht erhalten, die von der Logik-App empfangen wird.    

## <a name="troubleshoot"></a>Problembehandlung
Führen Sie diese Schritte aus, falls keine Aufrufe angezeigt werden, nachdem Sie etwas abgewartet und eine Aktualisierung durchgeführt haben: 

1. Vergewissern Sie sich, dass die Nachrichten das Service Bus-Thema erreicht haben. Weitere Informationen finden Sie auf der Seite **Service Bus-Thema** unter dem Zähler **Eingehende Nachrichten**. In diesem Fall habe ich die Anwendung **MessageSender** zweimal ausgeführt, sodass zehn Nachrichten angezeigt werden (fünf Nachrichten pro Ausführung).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Seite „Service Bus-Thema“: Eingehende Nachrichten":::    
1. Vergewissern Sie sich, dass für das Service Bus-Abonnement **keine aktiven Nachrichten** vorhanden sind. 
    Falls auf dieser Seite keine Ereignisse angezeigt werden, sollten Sie sich vergewissern, dass auf der Seite **Service Bus-Abonnement** kein Wert für **Anzahl aktiver Nachrichten** angezeigt wird. Wenn die Anzahl für diesen Zähler größer als 0 ist, werden die Nachrichten des Abonnements aus irgendeinem Grund nicht an die Handlerfunktion (Handler für Ereignisabonnement) weitergeleitet. Vergewissern Sie sich, dass Sie das Ereignisabonnement richtig eingerichtet haben. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Anzahl aktiver Nachrichten für Service Bus-Abonnement":::    
1. Darüber hinaus werden **übermittelte Ereignisse** auf der Seite **Ereignisse** des Service Bus-Namespace angezeigt. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Seite „Ereignisse“: Übermittelte Ereignisse" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Sie können auch auf der Seite **Ereignisabonnement** verfolgen, dass die Ereignisse übermittelt wurden. Sie können auf diese Seite zugreifen, indem Sie auf der Seite **Ereignisse** das Ereignisabonnement auswählen. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Seite „Ereignisabonnement“: Übermittelte Ereignisse":::
## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Azure Event Grid](../event-grid/index.yml).
* Erfahren Sie mehr über [Azure Functions](../azure-functions/index.yml).
* Weitere Informationen zum [Logic Apps-Feature von Azure App Service](../logic-apps/index.yml)
* Weitere Informationen zu [Azure Service Bus](/azure/service-bus/)


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png