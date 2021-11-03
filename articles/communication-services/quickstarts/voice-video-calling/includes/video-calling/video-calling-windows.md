---
ms.openlocfilehash: e90438e0987f26aa57264825cd2b2bae24f1c12e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253761"
---
In diesem Schnellstartanleitung erfahren Sie, wie Sie einen 1:1-Videoanruf mithilfe des Calling SDK von Azure Communication Services für Windows aufbauen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installieren Sie [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload „Entwicklung für die universelle Windows-Plattform“. 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen einer Communication Services-Ressource](../../../create-communication-resource.md)
- Ein [Benutzerzugriffstoken](../../../access-tokens.md) für Ihren Azure Communication Service

## <a name="setting-up"></a>Einrichten

### <a name="creating-the-project"></a>Erstellen des Projekts

Erstellen Sie in Visual Studio ein neues Projekt mit der Vorlage **Leere App (Universelle Windows-App)** , um eine einseitige UWP-App (Universelle Windows-Plattform) einzurichten.

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="Screenshot des Fensters „Neues Projekt“ in Visual Studio.":::

### <a name="install-the-package"></a>Installieren des Pakets

Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wechseln Sie zu `Manage Nuget Packages`, um `[Azure.Communication.Calling](https://www.nuget.org/packages/Azure.Communication.Calling)` zu installieren. Stellen Sie sicher, dass die Vorab veröffentlichte enthalten und überprüft ist und Ihre Paketquelle von https://www.nuget.org/api/v2/ ist. 

### <a name="request-access"></a>Anfordern des Zugriffs

Wechseln Sie zu `Package.appxmanifest`, und klicken Sie auf `Capabilities`.
Aktivieren Sie `Internet (Client & Server)`, um ein- und ausgehenden Zugriff auf das Internet zu erhalten. Aktivieren Sie `Microphone`, um auf den Audiofeed des Mikrofons zuzugreifen. Aktivieren Sie `WebCam`, um auf die Kamera des Geräts zuzugreifen. 

Fügen Sie ihrem `Package.appxmanifest` den folgenden Code hinzu, indem Sie mit einem Rechtsklick auf Ihrer Maus „Code anzeigen“ auswählen. 
```xml
<Extensions>
<Extension Category="windows.activatableClass.inProcessServer">
<InProcessServer>
<Path>RtmMvrUap.dll</Path>
<ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
</InProcessServer>
</Extension>
</Extensions>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Wir müssen ein einfaches Layout konfigurieren, um unsere Logik anzufügen. Um einen ausgehenden Anruf zu tätigen, benötigen wir ein `TextBox`-Element, um die Benutzer-ID des Angerufenen bereitzustellen. Außerdem benötigen wir die Schaltflächen `Start Call` und `Hang Up`. Darüber hinaus müssen wir eine Vorschau des lokalen Videos anzeigen und das Remotevideo des anderen Teilnehmers rendern. Daher benötigen wir zwei Elemente zum Anzeigen der Videostreams.

Öffnen Sie in Ihrem Projekt `MainPage.xaml`, und ersetzen Sie den Inhalt durch folgende Implementierung. 

```C#
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
        <StackPanel>
            <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
            <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
            <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
        </StackPanel>
        <StackPanel Orientation="Vertical" HorizontalAlignment="Center">
            <MediaElement x:Name="RemoteVideo" AutoPlay="True" Stretch="UniformToFill"/>
            <MediaElement x:Name="LocalVideo" AutoPlay="True"  Stretch="UniformToFill" HorizontalAlignment="Right"  VerticalAlignment="Bottom"/>
        </StackPanel>
    </StackPanel>
</Page>
```

Öffnen Sie `App.xaml.cs` (mit einem Rechtsklick auf Ihrer Maus und „Code anzeigen“ auswählen), und fügen Sie oben diese Zeile hinzu:
```C#
using CallingQuickstart;
```

Öffnen der `MainPage.xaml.cs` (mit einem Rechtsklick auf Ihrer Maus und „Code anzeigen“ auswählen), und ersetzen Sie den Inhalt mit der folgenden Implementierung: 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.WinRT.Communication;
using Azure.Communication.Calling;
using System.Diagnostics;
using System.Threading.Tasks;
using System.Collections.Generic;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgentAndDeviceManager();
        }
        
        private async void InitCallAgentAndDeviceManager()
        {
            // Initialize call agent and Device Manager
        }

        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Authenticate the client and start call
        }
        
        private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
        {
            // Accept an incoming call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient callClient;
        CallAgent callAgent;
        Call call;
        DeviceManager deviceManager;
        LocalVideoStream[] localVideoStream;
    }
}
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features des Azure Communication Services Calling SDK:

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | „CallClient“ ist der Haupteinstiegspunkt des Calling SDK.|
| CallAgent | CallAgent dient zum Starten und Verwalten von Anrufen. |
| CommunicationTokenCredential | „CommunicationTokenCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.| 
| CommunicationUserIdentifier | „CommunicationUserIdentifier“ wird zur Darstellung der Identität des Benutzers verwendet, wie u. a. die folgenden: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Zum Initialisieren eines `CallAgent` benötigen Sie ein Benutzerzugriffstoken. In der Regel wird dieses Token von einem Dienst mit einer für die Anwendung spezifischen Authentifizierung generiert. Weitere Informationen zu Benutzerzugriffstoken finden Sie im Handbuch zu [Benutzerzugriffstoken](../../../access-tokens.md). 

Ersetzen Sie für den Schnellstart `<USER_ACCESS_TOKEN>` durch ein Benutzerzugriffstoken, das für Ihre Azure Communication Service-Ressource generiert wurde.

Sobald Sie einen Token haben, initialisieren Sie damit eine `CallAgent`-Instanz, die es uns ermöglicht, Anrufe zu tätigen und zu empfangen. Um auf die Kameras auf dem Gerät zugreifen zu können, benötigen wir auch eine Geräte-Manager-Instanz. 

```C#
private async void InitCallAgentAndDeviceManager()
{
    CallClient callClient = new CallClient();
    deviceManager = await callClient.GetDeviceManager();

    CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
    
    CallAgentOptions callAgentOptions = new CallAgentOptions()
    {
        DisplayName = "<DISPLAY_NAME>"
    };
    callAgent = await callClient.CreateCallAgent(token_credential, callAgentOptions);
    callAgent.OnCallsUpdated += Agent_OnCallsUpdated;
    callAgent.OnIncomingCall += Agent_OnIncomingCall;
}
```

## <a name="start-a-call-with-video"></a>Starten eines Anrufs mit Video

So starten Sie einen Anruf mit Video. Wir müssen die Kameras mit der Gerätemanager-Instanz aufzählen und `LocalVideoStream` konstruieren. Wir müssen `VideoOptions` mit `LocalVideoStream` festlegen und mit `startCallOptions` übergeben, um die Anfangsoptionen für den Anruf festzulegen. Durch Anfügen von `LocalVideoStream` an `MediaElement` können wir die Vorschau des lokalen Videos sehen. 

```C#
private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }

    StartCallOptions startCallOptions = new StartCallOptions();
    startCallOptions.VideoOptions = new VideoOptions(localVideoStream);
    ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
    {
        new CommunicationUserIdentifier(CalleeTextBox.Text)
    };

    call = await callAgent.StartCallAsync(callees, startCallOptions);
}
```

## <a name="accept-an-incoming-call"></a>Annehmen eines eingehenden Anrufs

Um einen eingehenden Anruf mit Video zu beantworten, übergeben Sie `LocalVideoStream` an `acceptCallOptions`. 

```C#
private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    acceptCallOptions.VideoOptions = new VideoOptions(localVideoStream);

    call = await incomingcall.AcceptAsync(acceptCallOptions);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>Remote-Teilnehmer- und -Videostreams

Alle Remoteteilnehmer sind über die `RemoteParticipants`-Sammlung für eine Anrufinstanz verfügbar. Sobald der Anruf verbunden ist, können wir auf die Remoteteilnehmer des Anrufs zugreifen und die Remotevideostreams verarbeiten. 

```C#
private async void Agent_OnCallsUpdated(object sender, CallsUpdatedEventArgs args)
{
    foreach (var call in args.AddedCalls)
    {
        foreach (var remoteParticipant in call.RemoteParticipants)
        {
            await AddVideoStreams(remoteParticipant.VideoStreams);
            remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
        }
        call.OnRemoteParticipantsUpdated += Call_OnRemoteParticipantsUpdated;
        call.OnStateChanged += Call_OnStateChanged;
    }
}

private async void Call_OnRemoteParticipantsUpdated(object sender, ParticipantsUpdatedEventArgs args)
{
    foreach (var remoteParticipant in args.AddedParticipants)
    {
        await AddVideoStreams(remoteParticipant.VideoStreams);
        remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
    }
}
```

### <a name="render-remote-videos"></a>Rendern von Remote-Videos

Fügen Sie jeden Remotevideostream an `MediaElement` an. 

```C#
private async Task AddVideoStreams(IReadOnlyList<RemoteVideoStream> streams)
{

    foreach (var remoteVideoStream in streams)
    {
        var remoteUri = await remoteVideoStream.CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            RemoteVideo.Source = remoteUri;
            RemoteVideo.Play();
        });
        remoteVideoStream.Start();
    }
}
```

## <a name="call-state-update"></a>Zustandsaktualisierung aufrufen
Wir müssen die Videorenderer bereinigen, nachdem der Aufruf getrennt wurde. 

```C#
private async void Call_OnStateChanged(object sender, PropertyChangedEventArgs args)
{
    switch (((Call)sender).State)
    {
        case CallState.Disconnected:
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                LocalVideo.Source = null;
                RemoteVideo = null;
            });
            break;
        default:
            Debug.WriteLine(((Call)sender).State);
            break;
    }
}
```

## <a name="end-a-call"></a>Beenden eines Anrufs

Beenden Sie den aktuellen Anruf, nachdem auf die Schaltfläche `Hang Up` geklickt wurde. 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    var hangUpOptions = new HangUpOptions();
    await call.HangUpAsync(hangUpOptions);
}
```

## <a name="run-the-code"></a>Ausführen des Codes

Sie können den Build in Visual Studio erstellen und den Code ausführen. Beachten Sie, dass wir für Lösungsplattformen `ARM64`, `x64` und `x86` unterstützen. 

Sie können einen ausgehenden Videoanruf tätigen, indem Sie in das Textfeld eine Benutzer-ID eingeben und auf die Schaltfläche `Start Call` klicken. 

Hinweis: Durch den Aufruf von `8:echo123` wird der Videostream beendet, da der Echobot das Videostreaming nicht unterstützt. 

Weitere Informationen zu Benutzer IDs (Identität) finden Sie im Leitfaden zu [Benutzerzugriffstoken](../../../access-tokens.md). 
