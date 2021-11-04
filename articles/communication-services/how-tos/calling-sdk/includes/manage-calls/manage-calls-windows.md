---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9737c4e756e034c88800264e5cc56a4ca1f81741
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131254082"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

### <a name="request-access-to-the-microphone"></a>Anfordern des Zugriffs auf das Mikrofon

Die App benötigt Zugriff auf das Mikrofon, um ordnungsgemäß ausgeführt zu werden. In UWP-Apps sollte die Mikrofonfunktion in der App-Manifestdatei deklariert werden. Mit den folgenden Schritten wird veranschaulicht, wie dies erreicht werden kann.

1. Doppelklicken Sie im Panel `Solution Explorer` auf die Datei mit der Erweiterung `.appxmanifest`.
2. Klicken Sie auf die Registerkarte `Capabilities`.
3. Aktivieren Sie in der Liste der Funktionen das Kontrollkästchen `Microphone`.

### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>Erstellen von Benutzeroberflächenschaltflächen zum Tätigen und Beenden des Anrufs

Diese einfache Beispiel-App enthält zwei Schaltflächen: eine zum Tätigen und eine weitere zum Beenden des Anrufs.
In den folgenden Schritten wird veranschaulicht, wie Sie diese Schaltflächen zur App hinzufügen.

1. Doppelklicken Sie im Panel `Solution Explorer` auf die Datei mit dem Namen `MainPage.xaml`.
2. Suchen Sie im zentralen Panel in der Vorschau der Benutzeroberfläche nach dem XAML-Code.
3. Ersetzen Sie den XAML-Code von `<Grid>` bis `</Grid>` durch den folgenden Auszug:
```xml
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <Button x:Name="callButton" Click="CallHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Call</Button>
    <Button x:Name="hangupButton" Click="HangupHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Hang up</Button>
</StackPanel>
```

### <a name="setting-up-the-app-with-calling-sdk-apis"></a>Einrichten der App mit Calling SDK-APIs

Die Calling SDK-APIs befinden sich in zwei verschiedenen Namespaces.
Mit den folgenden Schritten wird der C#-Compiler über diese Namespaces informiert, damit IntelliSense in Visual Studio die Codeentwicklung unterstützen kann.

1. Klicken Sie im Panel `Solution Explorer` auf den Pfeil links neben der Datei mit dem Namen `MainPage.xaml`.
2. Doppelklicken Sie auf die nun angezeigte Datei mit dem Namen `MainPage.xaml.cs`.
3. Fügen Sie am Ende der aktuellen `using`-Anweisungen die folgenden Befehle hinzu.

```csharp
using Azure.Communication;
using Azure.Communication.Calling;
```

Lassen Sie `MainPage.xaml.cs` geöffnet. In den nächsten Schritten wird weiterer Code hinzugefügt.

## <a name="allow-app-interactions"></a>Zulassen von App-Interaktionen

Die zuvor hinzugefügten Benutzeroberflächenschaltflächen müssen auf einem platzierten `Call` ausgeführt werden. Dies bedeutet, dass ein `Call`-Datenelement zur `MainPage`-Klasse hinzugefügt werden muss.
Darüber hinaus muss derselben Klasse ein `CallAgent`-Datenelement hinzugefügt werden, damit der asynchrone Vorgang für die Erstellung von `CallAgent` erfolgreich ist.

Fügen Sie der `MainPage`-Klasse die folgenden Datenelemente hinzu:
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>Erstellen von Schaltflächenhandlern

Zuvor wurden dem XAML-Code zwei Benutzeroberflächenschaltflächen hinzugefügt. Der folgende Code fügt die Handler hinzu, die ausgeführt werden sollen, wenn ein Benutzer die Schaltfläche auswählt.
Der folgende Code sollte nach den Datenelementen aus dem vorherigen Abschnitt hinzugefügt werden.

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen befassen sich mit einigen der wichtigsten Features der Azure Communication Services-Clientbibliothek „Calling“ für UWP.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient ist der Haupteinstiegspunkt in die Clientbibliothek „Calling“. |
| CallAgent | „CallAgent“ dient zum Starten von und Teilnehmen an Anrufen. |
| Aufruf | „Call“ wird verwendet, um Anrufe zu verwalten, die getätigt oder an denen teilgenommen wurde. |
| CommunicationTokenCredential | „CommunicationTokenCredential“ dient als tokengestützte Anmeldeinformation zum Instanziieren von „CallAgent“.|
| CallAgentOptions | „CallAgentOptions“ enthält Informationen zum Identifizieren des Anrufers. |
| HangupOptions | „HangupOptions“ informiert, ob ein Anruf für alle Teilnehmer beendet werden soll. |

## <a name="initialize-the-callagent"></a>Initialisieren von „CallAgent“

Zum Erzeugen einer `CallAgent`-Instanz anhand von `CallClient` müssen Sie die `CallClient.CreateCallAgent`-Methode verwenden, die ein `CallAgent`-Objekt asynchron zurückgibt, nachdem es initialisiert wurde.

Zum Erstellen von `CallAgent` müssen Sie ein `CommunicationTokenCredential`-Objekt und ein `CallAgentOptions`-Objekt übergeben. Denken Sie daran, dass `CommunicationTokenCredential` eine Ausnahme auslöst, wenn ein falsch formatiertes Token übergeben wird.

Der folgende Code sollte in `CallHandler` hinzugefügt werden.

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential credentials;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    credentials = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
}
catch (Exception)
{
    throw new Exception("Invalid credential token");
}
```

`<USER ACCESS TOKEN>` muss durch ein gültiges Anmeldeinformationstoken für Ihre Ressource ersetzt werden. Wenn ein Anmeldeinformationstoken eingebunden werden muss, lesen Sie die Dokumentation zu [Benutzerzugriffstoken](../../../../quickstarts/access-tokens.md).

## <a name="create-callagent-and-place-a-call"></a>Erstellen von CallAgent und Tätigen eines Anrufs

Die Objekte, die zum Erstellen eines `CallAgent` erforderlich sind, sind jetzt bereit. Es ist an der Zeit, `CallAgent` asynchron zu erstellen und einen Anruf zu tätigen.

Der folgende Code sollte nach der Behandlung der Ausnahme aus dem vorherigen Schritt hinzugefügt werden.

```csharp
client.CreateCallAgent(creds, callAgentOptions).Completed +=
async (IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    CommunicationUserIdentifier target = new CommunicationUserIdentifier("<CALLEE>");

    StartCallOptions startCallOptions = new StartCallOptions();
    call_ = await agent_.StartCallAsync(new List<ICommunicationIdentifier>() { target }, startCallOptions);
};
```

Ersetzen Sie `<CALLEE>` durch eine beliebige andere Identität Ihres Mandanten. Alternativ können Sie auch `8:echo123` verwenden, um mit dem ACS-Echobot zu sprechen.

## <a name="end-a-call"></a>Beenden eines Anrufs

Sobald ein Anfruf getätigt wurde, sollte die `Hangup`-Methode des `Call`-Objekts verwendet werden, um den Anruf zu beenden.

Eine Instanz von `HangupOptions` sollte auch verwendet werden, um zu informieren, ob der Anruf für alle Teilnehmer beendet werden muss.

Der folgende Code sollte in `HangupHandler` hinzugefügt werden.

```csharp
HangUpOptions hangupOptions = new HangUpOptions();
call_.HangUpAsync(hangupOptions).Completed += (IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{

};
```

## <a name="run-the-code"></a>Ausführen des Codes

Stellen Sie sicher, dass Visual Studio die App für `x64`, `x86` oder `ARM64` erstellt, und klicken Sie dann auf `F5`, um die Ausführung der App zu starten. Klicken Sie anschließend auf die Schaltfläche `Call`, um den festgelegten Gesprächspartner anzurufen.

Beachten Sie, dass das System beim ersten Ausführen der App den Benutzer zur Gewährung des Zugriffs auf das Mikrofon auffordert.