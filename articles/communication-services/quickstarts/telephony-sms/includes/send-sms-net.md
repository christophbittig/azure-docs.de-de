---
title: Datei einfügen
description: include file
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 12e16f8078d90ec1faae990a2e1dc4e6e7a42971
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529481"
---
Steigen Sie in Azure Communication Services ein, indem Sie das C# SMS-SDK von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Die aktuelle Version des [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) für Ihr Betriebssystem.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster den Befehl `dotnet` aus, um sich zu vergewissern, dass das ,NET SDK installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `SmsQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `dotnet add package` das .NET-Paket des SMS SDK von Azure Communication Services.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Fügen Sie am Anfang von **Program.cs** eine Anweisung vom Typ `using` hinzu, um den Namespace `Azure.Communication` einzuschließen.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen dienen zur Behandlung einiger der wichtigsten Features des SMS SDK für C# von Azure Communication Services.

| name                                       | Beschreibung                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Senden von SMS-Nachrichten.                           |
| SmsSendOptions | Diese Klasse bietet Optionen zum Konfigurieren von Zustellberichten. Ist „enable_delivery_report“ auf „True“ festgelegt, wird bei erfolgreicher Zustellung ein Ereignis ausgegeben. |
| SmsSendResult               | Diese Klasse enthält das Ergebnis des SMS-Diensts.                                          |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

 Öffnen Sie **Program.cs** in einem Text-Editor, und ersetzen Sie den Text der Methode `Main` durch Code zum Initialisieren eines SMS-Clients (`SmsClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Senden einer SMS: 1:1

Um eine SMS-Nachricht an einen einzelnen Empfänger zu senden, rufen Sie die Funktion `Send` oder `SendAsync` über SmsClient auf. Fügen Sie in **Program.cs** am Ende der Methode `Main` den folgenden Code hinzu:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number>` durch die Telefonnummer, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

## <a name="send-a-1n-sms-message-with-options"></a>Senden einer SMS mit Optionen: 1:N
Wenn Sie eine SMS an eine Empfängerliste senden möchten, rufen Sie über SmsClient die Funktion `Send` oder `SendAsync` mit einer Liste der Telefonnummern der Empfänger auf. Sie können auch optionale Parameter übergeben, um anzugeben, ob der Zustellbericht aktiviert werden soll, und um benutzerdefinierte Tags festzulegen.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

Ersetzen Sie `<from-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number-1>` und `<to-phone-number-2>` durch die Telefonnummern, an die Sie eine Nachricht senden möchten.

> [!WARNING]
> Beachten Sie, dass Telefonnummern im internationalen Standardformat E.164 angegeben werden sollten (z. B. +14255550123). Die Telefonnummer vom Typ **From** (Anrufertelefonnummer) kann auch ein kurzer Code sein (z. B. 23456).

Der Parameter `enableDeliveryReport` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).

`Tag` wird verwendet, um ein Tag auf den Zustellbericht anzuwenden

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```

## <a name="sample-code"></a>Beispielcode

Sie können die Beispiel-App von [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) herunterladen.
