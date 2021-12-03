---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung'
author: juramir
ms.author: juramir
ms.date: 10/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: acab57f774d97c2cd9710b77f6a0ebabf73e4fdb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520818"
---
In diesem Schnellstart erfahren Sie, wie Sie mit dem Chat SDK für C# für Azure Communication Services an einer Teams-Besprechung teilnehmen.

## <a name="sample-code"></a>Beispielcode
Den Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ChatTeamsInteropQuickStart).

## <a name="prerequisites"></a>Voraussetzungen 

* Eine  [Teams-Bereitstellung](/deployoffice/teams-install). 
* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).  
* Installieren Sie [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload „Entwicklung für die universelle Windows-Plattform“.  
* Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md). 
* Einen Teams-Besprechungslink.

## <a name="joining-the-meeting-chat"></a>Beitritt zum Besprechungschat 

Ein Communication Services-Benutzer kann mithilfe des Calling SDK einer Teams-Besprechung als anonymer Benutzer beitreten. Durch das Beitreten zur Besprechung wird er dem Besprechungschat als Teilnehmer hinzugefügt und kann Nachrichten mit anderen Benutzern der Besprechung austauschen. Der Benutzer hat keinen Zugriff auf Chatnachrichten, die vor seiner Teilnahme an der Besprechung gesendet wurden, und er kann nach dem Ende der Besprechung keine Nachrichten senden oder empfangen. Führen Sie die folgenden Schritte aus, um an der Besprechung teilzunehmen und mit dem Chatten zu beginnen.

## <a name="run-the-code"></a>Ausführen des Codes
Sie können den Build in Visual Studio erstellen und den Code ausführen. Beachten Sie die von uns unterstützen Lösungsplattformen `x64`, `x86` und `ARM64`. 

1. Öffnen Sie eine Instanz von PowerShell, des Windows-Terminals, einer Eingabeaufforderung oder eines gleichwertigen Tools, und navigieren Sie zu dem Verzeichnis, in dem Sie das Beispiel klonen möchten.
2. `git clone https://github.com/Azure-Samples/Communication-Services-dotnet-quickstarts.git`
3. Öffnen Sie das Projekt „ChatTeamsInteropQuickStart/ChatTeamsInteropQuickStart.csproj“ in Visual Studio.
4. Installieren Sie die folgenden NuGet-Paketversionen (oder höher):
``` csharp
Install-Package Azure.Communication.Calling -Version 1.0.0-beta.29
Install-Package Azure.Communication.Chat -Version 1.1.0
Install-Package Azure.Communication.Common -Version 1.0.1
Install-Package Azure.Communication.Identity -Version 1.0.1

```

5. Fügen Sie der Datei **ChatTeamsInteropQuickStart/MainPage.xaml.cs** die in den Voraussetzungen vermittelte Communication Services-Ressource als Verbindungszeichenfolge hinzu. 

``` csharp
//ACS resource connection string i.e = "endpoint=https://your-resource.communication.azure.net/;accesskey=your-access-key";
private const string connectionString_ = "";
```

> [!IMPORTANT]
> * Wählen Sie die richtige Plattform aus der Dropdownliste „Projektmappenplattformen“ in Visual Studio aus, <b>bevor</b> Sie den Code ausführen. z.B.: `x64`
> * Stellen Sie sicher, dass der Entwicklermodus in Windows 10 aktiviert ist [(Entwicklereinstellungen)](/windows/apps/get-started/enable-your-device-for-development).
>  
>  *Die nächsten Schritte funktionieren nicht, wenn dies nicht ordnungsgemäß konfiguriert ist.*


6. Drücken Sie F5, um das Projekt im Debugmodus zu starten.
7. Fügen Sie einen gültigen Teams-Besprechungslink in das Feld „Teams-Besprechungslink“ ein (siehe nächster Abschnitt).
8. Klicken Sie auf „Join Teams meeting“ (Teams-Besprechung beitreten), um mit dem Chatten zu beginnen.

> [!IMPORTANT]
> Sobald das aufrufende SDK die Verbindung mit der Teamsitzung hergestellt hat [siehe „Communication Services, die die Windows App aufrufen“](../../voice-video-calling/getting-started-with-calling.md), sind die Schlüsselfunktionen zur Handhabung von Chatvorgängen: StartPollingForChatMessages und SendMessageButton_Click. Beide Codeausschnitte befinden sich in „ChatTeamsInteropQuickStart\MainPage.xaml.cs“ 

```csharp
        /// <summary>
        /// Background task that keeps polling for chat messages while the call connection is stablished
        /// </summary>
        private async Task StartPollingForChatMessages()
        {
            CommunicationTokenCredential communicationTokenCredential = new(user_token_);
            chatClient_ = new ChatClient(EndPointFromConnectionString(), communicationTokenCredential);
            await Task.Run(async () =>
            {
                keepPolling_ = true;

                ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
                int previousTextMessages = 0;
                while (keepPolling_)
                {
                    try
                    {
                        CommunicationUserIdentifier currentUser = new(user_Id_);
                        AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
                        SortedDictionary<long, string> messageList = new();
                        int textMessages = 0;
                        string userPrefix;
                        await foreach (ChatMessage message in allMessages)
                        {
                            if (message.Type == ChatMessageType.Html || message.Type == ChatMessageType.Text)
                            {
                                textMessages++;
                                userPrefix = message.Sender.Equals(currentUser) ? "[you]:" : "";
                                messageList.Add(long.Parse(message.SequenceId), $"{userPrefix}{StripHtml(message.Content.Message)}");
                            }
                        }

                        //Update UI just when there are new messages
                        if (textMessages > previousTextMessages)
                        {
                            previousTextMessages = textMessages;
                            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                            {
                                TxtChat.Text = string.Join(Environment.NewLine, messageList.Values.ToList());
                            });

                        }
                        if (!keepPolling_)
                        {
                            return;
                        }

                        await SetInCallState(true);
                        await Task.Delay(3000);
                    }
                    catch (Exception e)
                    {
                        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                        {
                            _ = new MessageDialog($"An error ocurred while fetching messages in PollingChatMessagesAsync(). The application will shutdown. Details : {e.Message}").ShowAsync();
                            throw e;
                        });
                        await SetInCallState(false);
                    }
                }
            });
        }
        private async void SendMessageButton_Click(object sender, RoutedEventArgs e)
        {
            SendMessageButton.IsEnabled = false;
            ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
            _ = await chatThreadClient.SendMessageAsync(TxtMessage.Text);
            
            TxtMessage.Text = "";
            SendMessageButton.IsEnabled = true;
        }
```



## <a name="get-a-teams-meeting-link"></a>Erhalten des Teams-Besprechungslinks

Der Teams-Besprechungslink kann über Graph-APIs abgerufen werden, wie in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) ausführlich erläutert. Dieser Link wird als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die unter der [`joinWebUrl`-Eigenschaft](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) zugegriffen werden kann. 

Der erforderliche Besprechungslink kann auch der URL für den **Besprechungsbeitritt** aus der Teams-Besprechungseinladung entnommen werden.
Ein Teams-Besprechungslink sieht wie folgt aus: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. 

:::image type="content" source="../join-teams-meeting-chat-quickstart-windows.png" alt-text="Screenshot der fertigen csharp-Anwendung":::

> [!NOTE] 
> Derzeit werden für Interoperabilitätsszenarios mit Teams nur das Senden, Empfangen und Bearbeiten von Nachrichten sowie das Senden von Eingabebenachrichtigungen unterstützt. Andere Features wie Lesebestätigungen oder das Hinzufügen oder Entfernen anderer Benutzer aus der Teams-Besprechung durch Communication Services-Benutzer werden noch nicht unterstützt.
