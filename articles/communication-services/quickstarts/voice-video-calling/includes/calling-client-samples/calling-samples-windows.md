---
title: Schnellstart – Hinzufügen von Telefonie zu einer Windows-App mithilfe von Azure Communication Services
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie das Calling SDK von Azure Communication Services für Windows verwenden.
author: tophpalmer
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5959cb809e9537c5de9ed2dce771a234f742dc08
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078559"
---
In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Anruf mithilfe des Calling SDK von Azure Communication Services für Windows einleiten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../../create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../../access-tokens.md)
- Eine Edition von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), bei der die `Universal Windows Platform development`-Workload zusammen mit Windows SDK `10.0.17134` oder höher installiert ist. Darüber hinaus sollten auch die Komponenten `NuGet package manager` und `NuGet targets and build tasks` installiert werden.
- Optional: Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Anruffunktionen zu einer Anwendung](../../getting-started-with-calling.md) durch.

## <a name="setting-up"></a>Einrichten

### <a name="creating-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

Erstellen Sie in Visual Studio 2019 ein neues `Blank App (Universal Windows)`-Projekt. Nach der Eingabe des Projektnamens können Sie ein beliebiges Windows SDK höher als `10.0.17134` auswählen. 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>Installieren des Pakets und der Abhängigkeiten mit NuGet-Paket-Manager

Die Calling SDK-APIs und -Bibliotheken sind über ein NuGet-Paket öffentlich verfügbar.
In den folgenden Schritten wird veranschaulicht, wie Sie das Calling SDK-NuGet-Paket suchen, herunterladen und installieren.

1. Öffnen des NuGet-Paket-Managers (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. Klicken Sie auf `Browse`, und geben Sie dann `Azure.Communication.Calling` in das Suchfeld ein.
3. Stellen Sie sicher, dass das Kontrollkästchen `Include prerelease` aktiviert ist.
4. Klicken Sie auf das `Azure.Communication.Calling`-Paket.
5. Aktivieren Sie auf der rechten Registerkarte das Kontrollkästchen, das dem CS-Projekt entspricht.
6. Klicken Sie auf die Schaltfläche `Install`.


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

## <a name="allow-app-interactions"></a>Zulassen von App-Interaktionen

Die zuvor hinzugefügten Benutzeroberflächenschaltflächen müssen auf einem platzierten `Call` ausgeführt werden. Dies bedeutet, dass ein `Call`-Datenelement zur `MainPage`-Klasse hinzugefügt werden muss.
Darüber hinaus muss derselben Klasse ein `CallAgent`-Datenelement hinzugefügt werden, damit der asynchrone Vorgang für die Erstellung von `CallAgent` erfolgreich ist.

Fügen Sie der `MainPage`-Klasse die folgenden Datenelemente hinzu:
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>Erstellen von Schaltflächenhandlern

Zuvor wurden dem XAML-Code zwei Benutzeroberflächenschaltflächen hinzugefügt. Der folgende Code fügt die Handler hinzu, die ausgeführt werden sollen, wenn ein Benutzer auf die Schaltfläche klickt.
Der folgende Code sollte nach den Datenelementen aus dem vorherigen Abschnitt hinzugefügt werden.

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="initialize-the-callagent"></a>Initialisieren von „CallAgent“

Zum Erzeugen einer `CallAgent`-Instanz anhand von `CallClient` müssen Sie die `CallClient.CreateCallAgent`-Methode verwenden, die ein `CallAgent`-Objekt asynchron zurückgibt, nachdem es initialisiert wurde.

Zum Erstellen von `CallAgent` müssen Sie ein `CommunicationTokenCredential`-Objekt und ein `CallAgentOptions`-Objekt übergeben. Denken Sie daran, dass `CommunicationTokenCredential` eine Ausnahme auslöst, wenn ein falsch formatiertes Token übergeben wird.

Der folgende Code sollte in `CallHandler` hinzugefügt werden.

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential creds;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    creds = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
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
call_.HangUpAsync(hangupOptions).Completed +=
(IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{
};
```

## <a name="run-the-code"></a>Ausführen des Codes

Stellen Sie sicher, dass Visual Studio die App für `x64`, `x86` oder `ARM64` erstellt, und klicken Sie dann auf `F5`, um die Ausführung der App zu starten. Klicken Sie anschließend auf die Schaltfläche `Call`, um den festgelegten Gesprächspartner anzurufen.

Beachten Sie, dass das System beim ersten Ausführen der App den Benutzer zur Gewährung des Zugriffs auf das Mikrofon auffordert.
