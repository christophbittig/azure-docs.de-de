---
title: include file
description: Datei einfügen
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: d41f9c56cfa9b2a41cb77355af8a35a0499adb7a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893218"
---
### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster den Befehl `dotnet` aus, um sich zu vergewissern, dass das ,NET SDK installiert ist.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

1. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `RelayTokenQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o RelayTokenQuickstart
```

2. Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd RelayTokenQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `dotnet add package` die Azure Communication Services Identity- und NetworkTraversal-Bibliothek für das .NET-Paket.

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.NetworkTraversal
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen Sie die Datei **Program.cs** in einem Text-Editor.
2. Fügen Sie eine `using`-Anweisung hinzu, um `Azure.Communication.Identity`, `System.Threading.Tasks` und `System.Net.Http` einzuschließen
3. Aktualisieren der `Main`-Methodendeklaration zur Unterstützung von asynchronem Code

Der Code lautet wie folgt:

```csharp
using System;
using Azure.Communication.Identity;
using Azure;
using System.Collections.Generic;
using Azure.Communication.NetworkTraversal;

namespace RelayTokenQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Relay Token Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="authenticate-the-client"></a>Authentifizieren des Clients

Initialisieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen abgerufen. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellt haben, müssen der Editor, die IDE oder die Shell, in dem bzw. der sie ausgeführt wird, geschlossen und erneut geöffnet werden, damit der Zugriff auf die Variable möglich ist.

Fügen Sie der `Main`-Methode folgenden Code hinzu:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `createUser`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```csharp
var identityResponse = await client.CreateUserAsync().Result;
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>Austauschen des Benutzerzugriffstokens gegen ein Relaytoken

Rufen Sie den Azure Communication-Tokendienst auf, um das Benutzerzugriffstoken gegen ein TURN-Diensttoken zu tauschen

```csharp
var relayClient = new CommunicationRelayClient("COMMUNICATION_SERVICES_CONNECTION_STRING");

Response<CommunicationRelayConfiguration> turnTokenResponse = await relayClient.GetRelayConfigurationAsync(identity).Result;
DateTimeOffset turnTokenExpiresOn = turnTokenResponse.Value.ExpiresOn;
IReadOnlyList<CommunicationIceServer> iceServers = turnTokenResponse.Value.IceServers;
Console.WriteLine($"Expires On: {turnTokenExpiresOn}");
foreach (CommunicationIceServer iceServer in iceServers)
{
    foreach (string url in iceServer.Urls)
    {
        Console.WriteLine($"TURN Url: {url}");
    }
    Console.WriteLine($"TURN Username: {iceServer.Username}");
    Console.WriteLine($"TURN Credential: {iceServer.Credential}");
}
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```
