---
title: 'Schnellstart: Beitreten zu einer Teams-Besprechung von einer Windows-App aus'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure Communication Services Calling SDK für Windows einer Teams-Besprechung beitreten
author: aurighet
ms.author: aurighet
ms.date: 05/13/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 62a13298e62a9c7f1cfe7a0643f9db2a1dfc9d15
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520751"
---
In dieser Schnellstartanleitung erfahren Sie, wie Sie mit dem Azure Communication Services Calling SDK für Windows einer Teams-Besprechung beitreten.

## <a name="prerequisites"></a>Voraussetzungen

- Eine funktionierende [Windows-App für Communication Services-Anrufe](../../getting-started-with-calling.md).
- Eine [Teams-Bereitstellung](/deployoffice/teams-install)

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Hinzufügen und Aktivieren der Teams-Benutzeroberflächen-Steuerelemente

Ersetzen Sie den Code in „MainPage.xaml“ durch den folgenden Codeausschnitt. Das Textfeld dient zur Eingabe des Teams-Besprechungskontexts, und die Schaltfläche wird verwendet, um der angegebenen Besprechung beizutreten:

```xml
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox PlaceholderText="Please enter the Teams meeting link." TextWrapping="Wrap" x:Name="TeamsLinkTextBox" Margin="10,10,10,10" />
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <Button Content="Join Teams Meeting" Click="JoinButton_ClickAsync" x:Name="JoinButton" Margin="10,10,10,10" />
            <Button Content="Leave Meeting" Click="LeaveButton_ClickAsync" x:Name="LeaveButton" Margin="10,10,10,10" />
        </StackPanel>
        <TextBlock TextWrapping="Wrap" x:Name="CallStatusTextBlock" Margin="10,10,10,10" />
        <TextBlock TextWrapping="Wrap" x:Name="RecordingStatusTextBlock" Margin="10,10,10,10" />
    </StackPanel>
</Page>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivieren der Steuerelemente der Teams-Benutzeroberfläche

Ersetzen Sie den Inhalt von `MainPage.xaml.cs` durch folgenden Codeausschnitt:

```csharp
using System;
using System.Threading.Tasks;

using Windows.UI.Core;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    public sealed partial class MainPage : Page
    {
        string user_token_ = "<User_Access_Token>";

        Call call_;
        CallClient call_client;
        CallAgent call_agent;        

        public MainPage()
        {
            this.InitializeComponent();
            call_client = new CallClient();
        }

        private async void JoinButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            if (!await ValidateInput())
            {
                return;
            }

            //
            //  Create CallAgent
            //
            CommunicationTokenCredential token_credential;
            try
            {
                token_credential = new CommunicationTokenCredential(user_token_);
                CallAgentOptions call_agent_options = new CallAgentOptions();
                call_agent = await call_client.CreateCallAgent(token_credential, call_agent_options);
            }
            catch
            {
                await new MessageDialog("It was not possible to create call agent. Please check if token is valid.").ShowAsync();
                return;
            }

            //
            //  Join a Teams meeting
            //
            try
            {
                JoinCallOptions joinCallOptions = new JoinCallOptions();
                TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(TeamsLinkTextBox.Text);
                call_ = await call_agent.JoinAsync(teamsMeetingLinkLocator, joinCallOptions);
            }
            catch
            {
                await new MessageDialog("It was not possible to join the Teams meeting. Please check if Teams Link is valid.").ShowAsync();
                return;
            }

            //
            //  Set up call callbacks
            //
            call_.OnStateChanged += Call_OnStateChangedAsync;
            call_.OnIsRecordingActiveChanged += Call_OnIsRecordingActiveChangedAsync;
        }

        private async void Call_OnStateChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                CallStatusTextBlock.Text = call_.State.ToString();
            });
        }
        
        private async void Call_OnIsRecordingActiveChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                RecordingStatusTextBlock.Text = call_.IsRecordingActive ? "Recording is active." : "Recording is inactive.";
            });
        }
        
        private async void LeaveButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            try
            {
                await call_.HangUp(new HangUpOptions());
            }
            catch
            {
                await new MessageDialog("It was not possible to leave the Teams meeting.").ShowAsync();
            }
        }
        
        private async Task<bool> ValidateInput()
        {
            if (user_token_.StartsWith("<"))
            {
                await new MessageDialog("Please enter token in source code.").ShowAsync();
                return false;
            }
        
            if (TeamsLinkTextBox.Text.Trim().Length == 0 || !TeamsLinkTextBox.Text.StartsWith("http"))
            {
                await new MessageDialog("Please enter Teams meeting link.").ShowAsync();
                return false;
            }
        
            return true;
        }
    }
}
```

## <a name="get-the-teams-meeting-link"></a>Abrufen des Teams-Besprechungslinks

Der Teams-Besprechungslink kann mithilfe der Graph-APIs abgerufen werden. Dies wird in der [Graph-Dokumentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) erläutert.
Das Communication Services-SDK für Telefonie akzeptiert einen vollständigen Teams-Besprechungslink. Dieser Link wird als Teil der `onlineMeeting`-Ressource zurückgegeben, auf die unter der [`joinWebUrl`-Eigenschaft](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) zugegriffen werden kann. Die erforderlichen Besprechungsinformationen können auch der URL für den **Besprechungsbeitritt** aus der Teams-Besprechungseinladung entnommen werden.

## <a name="launch-the-app-and-join-teams-meeting"></a>Starten der App und Beitreten zur Teams-Besprechung

Sie können Ihre App in Visual Studio erstellen und ausführen, indem Sie **Debuggen** > **Debuggen starten** auswählen oder die Tastenkombination (F5) verwenden.

Fügen Sie den Teams-Kontext in das Textfeld ein, und klicken Sie auf *Join Teams Meeting* (Teams-Besprechung beitreten), um der Teams-Besprechung über Ihre Communication Services-Anwendung beizutreten.
