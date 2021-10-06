---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ca40e760e34be8ef4299e042949f7a36b54f36f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838211"
---
## <a name="set-up-your-system"></a>Einrichten des Systems

### <a name="create-the-xcode-project"></a>Erstellen des Xcode-Projekts

Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View App** aus. In dieser Schnellstartanleitung wird das [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/) verwendet. Legen Sie daher **Language** (Sprache) auf **Swift** und **User Interface** (Benutzeroberfläche) auf **SwiftUI** fest. 

Während dieses Schnellstarts werden keine Komponenten- oder Benutzeroberflächentests erstellt. Die Textfelder **Include Unit Tests** (Komponententests einbeziehen) und **Include UI Tests** (Benutzeroberflächentests einbeziehen) können daher geleert werden.

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-new-ios-project.png" alt-text="Screenshot: Fenster zum Erstellen eines Projekts in Xcode":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installieren des Pakets und der Abhängigkeiten mit CocoaPods

1. Erstellen Sie wie folgt eine Podfile für die Anwendung:

    ```
    platform :ios, '13.0'
    use_frameworks!
    target 'AzureCommunicationCallingSample' do
        pod 'AzureCommunicationCalling', '~> 1.0.0'
    end
    ```
2. Führen Sie `pod install` aus.
3. Öffnen Sie `.xcworkspace` mit Xcode.

### <a name="request-access-to-the-microphone"></a>Anfordern des Zugriffs auf das Mikrofon

Um auf das Mikrofon des Geräts zugreifen zu können, müssen Sie die Liste der Informationseigenschaften Ihrer App mit `NSMicrophoneUsageDescription` aktualisieren. Legen Sie den zugehörigen Wert auf eine Zeichenfolge (`string`) fest. Diese wird in das Dialogfeld eingeschlossen, mit dem das System Zugriff vom Benutzer anfordert.

Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend **Open As** (Öffnen als)  > **Source Code** (Quellcode) aus. Fügen Sie im Abschnitt `<dict>` der obersten Ebene die folgenden Zeilen hinzu, und speichern Sie dann die Datei.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Öffnen Sie die Datei *ContentView.swift* Ihres Projekts, und fügen Sie am Anfang der Datei eine Deklaration vom Typ `import` hinzu, um die Bibliothek `AzureCommunicationCalling` zu importieren. Importieren Sie außerdem `AVFoundation`. Dies ist für Audioberechtigungsanforderungen im Code erforderlich.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="initialize-callagent"></a>Initialisieren von „CallAgent“

Um eine `CallAgent`-Instanz auf der Grundlage von `CallClient` zu erstellen, müssen Sie eine Methode vom Typ `callClient.createCallAgent` verwenden, die asynchron ein Objekt vom Typ `CallAgent` zurückgibt, nachdem es initialisiert wurde.

Für die Erstellung eines Anrufclients muss ein Objekt vom Typ `CommunicationTokenCredential` übergeben werden.

```swift
import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Übergeben Sie das von Ihnen erstellte Objekt vom Typ `CommunicationTokenCredential` an `CallClient`, und legen Sie den Anzeigenamen fest.

```swift
self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})
```