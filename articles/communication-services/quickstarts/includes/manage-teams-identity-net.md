---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 717eefe0795809a9dd974ce6557d39486fbf9dd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461150"
---
## <a name="set-up-prerequisites"></a>Einrichten erforderlicher Komponenten

- Die aktuelle Version des [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet) für Ihr Betriebssystem.

## <a name="set-up"></a>Einrichtung

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `CommunicationAccessTokensQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o CommunicationAccessTokensQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd CommunicationAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `dotnet add package` die Azure Communication Services-Identitätsbibliothek für das .NET-Paket.

```console
dotnet add package Azure.Communication.Identity --prerelease
dotnet add package Microsoft.Identity.Client --version 4.36.2
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen Sie die Datei **Program.cs** in einem Text-Editor.
1. Fügen Sie eine Anweisung vom Typ `using` hinzu, um den Namespace `Azure.Communication.Identity` einzuschließen.
1. Aktualisieren der `Main`-Methodendeklaration zur Unterstützung von asynchronem Code

Verwenden Sie zum Einstieg den folgenden Code:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace CommunicationAccessTokensQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Teams Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Schritt 1: Empfangen des Azure AD-Benutzertokens über die MSAL-Bibliothek

Der erste Schritt im Flow für den Tokenaustausch besteht darin, mithilfe von [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md) ein Token für Ihre Teams-Benutzer abzurufen.

```csharp
string appId = "<contoso_application_id>";
string tenantId = "<contoso_tenant_id>";
string authority = $"https://login.microsoftonline.com/{tenantId}";
string redirectUri = "http://localhost";

var aadClient = PublicClientApplicationBuilder
                .Create(appId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

string scope = "https://auth.msft.communication.azure.com/VoIP";

var teamsUserAadToken = await aadClient
                        .AcquireTokenInteractive(new List<string> { scope })
                        .ExecuteAsync();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Schritt 2: Initialisieren von CommunicationIdentityClient

Initialisieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string).

Fügen Sie der `Main`-Methode folgenden Code hinzu:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Schritt 3: Austauschen des Azure AD-Benutzertokens gegen das Teams-Zugriffstoken

Verwenden Sie die Methode `GetTokenForTeamsUser`, um ein Zugriffstoken für den Teams-Benutzer auszustellen, das mit den Azure Communication Services SDKs verwendet werden kann.

```csharp
var accessToken = await client.GetTokenForTeamsUser(teamsUserAadToken.AccessToken);
Console.WriteLine($"Token: {accessToken.Value.Token}");
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```
