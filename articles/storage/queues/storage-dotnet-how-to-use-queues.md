---
title: 'Erste Schritte mit Azure Queue Storage mit .NET: Azure Storage'
description: Azure Queue Storage ermöglicht zuverlässiges, asynchrones Messaging zwischen Anwendungskomponenten. Das Cloud-Messaging ermöglicht Ihren Anwendungskomponenten die unabhängige Skalierung.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4dc434d8387a065f60405e1c0937c7bb61ef9833
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562455"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Erste Schritte mit Azure Queue Storage mit .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Übersicht

Azure Queue Storage ermöglicht Cloud-Messaging zwischen Anwendungskomponenten. Bei der Entwicklung skalierbarer Anwendungen werden häufig einzelne Anwendungskomponenten entkoppelt, damit sie unabhängig skaliert werden können. Queue Storage bietet asynchrones Messaging zwischen Anwendungskomponenten unabhängig davon, ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder auf einem mobilen Gerät ausgeführt werden. Queue Storage unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

### <a name="about-this-tutorial"></a>Informationen zu diesem Tutorial

In diesem Tutorial wird gezeigt, wie Sie .NET-Code für einige häufig verwendete Szenarien mit Azure Queue Storage schreiben. Zu den behandelten Szenarien zählen das Erstellen und Löschen von Warteschlangen sowie das Hinzufügen, Lesen und Löschen von Warteschlangennachrichten.

**Geschätzter Zeitaufwand**: 45 Minuten

### <a name="prerequisites"></a>Voraussetzungen

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Ein [Azure Storage-Konto](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Richten Sie als Nächstes Ihre Entwicklungsumgebung in Visual Studio ein, damit Sie die Codebeispiele dieser Anleitung ausprobieren können.

### <a name="create-a-windows-console-application-project"></a>Erstellen eines Windows-Konsolenanwendungsprojekts

Erstellen Sie in Visual Studio eine neue Windows-Konsolenanwendung. In den folgenden Schritten wird veranschaulicht, wie Sie eine Konsolenanwendung in Visual Studio 2019 erstellen. Die Schritte sind in anderen Versionen von Visual Studio ähnlich.

1. Wählen Sie **Datei** > **Neu** > **Projekt**.
2. Wählen Sie **Plattform** > **Windows** aus.
3. Wählen Sie **Konsolen-App (.NET Framework)** .
4. Wählen Sie **Weiter** aus.
5. Geben Sie in das Feld **Projektname** einen Namen für Ihre Anwendung ein.
6. Klicken Sie auf **Erstellen**

Alle Codebeispiele in diesem Tutorial können in der Datei `Program.cs` Ihrer Konsolenanwendung der `Main()`-Methode hinzugefügt werden.

Sie können die Azure Storage-Clientbibliotheken in jeder Art von .NET-Anwendung nutzen, z.B. einem Azure-Clouddienst oder einer Azure-Web-App, einer Desktopanwendung oder einer mobilen Anwendung. In diesem Leitfaden verwenden wir der Einfachheit halber eine Konsolenanwendung.

### <a name="use-nuget-to-install-the-required-packages"></a>Verwenden von NuGet zum Installieren der erforderlichen Pakete

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Sie müssen in Ihrem Projekt auf die folgenden vier Pakete verweisen, um dieses Tutorial abzuschließen:

- [Azure Core-Bibliothek für .NET](https://www.nuget.org/packages/azure.core/): Dieses Paket bietet freigegebene primitive Typen, Abstraktionen und Hilfsprogramme für moderne .NET Azure SDK-Clientbibliotheken.
- [Azure.Storage.Common-Clientbibliothek für .NET](https://www.nuget.org/packages/azure.storage.common/): Dieses Paket stellt die Infrastruktur bereit, die von den anderen Azure Storage-Clientbibliotheken gemeinsam genutzt wird.
- [Azure.Storage.Queues-Clientbibliothek für .NET](https://www.nuget.org/packages/azure.storage.queues/): Dieses Paket ermöglicht das Arbeiten mit Azure Queue Storage zum Speichern von Nachrichten, auf die ein Client möglicherweise zugreift.
- [System.Configuration.ConfigurationManager-Bibliothek für .NET](https://www.nuget.org/packages/system.configuration.configurationmanager/): Dieses Paket bietet Zugriff auf Konfigurationsdateien für Clientanwendungen.

Sie können diese Pakete über NuGet abrufen. Folgen Sie diesen Schritten:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten**.
1. Wählen Sie **Durchsuchen**.
1. Suchen Sie online nach `Azure.Storage.Queues`, und wählen Sie **Installieren** aus, um die Azure Storage-Clientbibliothek und die zugehörigen Abhängigkeiten zu installieren. Dadurch werden auch die Bibliotheken Azure.Storage.Common und Azure.Core installiert, die Abhängigkeiten der Warteschlangenbibliothek sind.
1. Suchen Sie online nach `System.Configuration.ConfigurationManager`, und wählen Sie **Installieren** aus, um den Configuration Manager zu installieren.

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Sie müssen in Ihrem Projekt auf die folgenden drei Pakete verweisen, um dieses Tutorial abzuschließen:

- [Microsoft.Azure.Storage.Common-Clientbibliothek für .NET](https://www.nuget.org/packages/microsoft.azure.storage.common/): Mit diesem Paket erhalten Sie programmgesteuerten Zugriff auf Datenressourcen in Ihrem Speicherkonto.
- [Microsoft Azure Queue Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/microsoft.azure.storage.queue/): Diese Clientbibliothek ermöglicht das Arbeiten mit Azure Queue Storage zum Speichern von Nachrichten, auf die ein Client möglicherweise zugreift.
- [Microsoft Azure Configuration Manager-Bibliothek für .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): Mit diesem Paket wird eine Klasse zum Analysieren einer Verbindungszeichenfolge in einer Konfigurationsdatei bereitgestellt. Dies gilt unabhängig davon, wo die Anwendung ausgeführt wird.

Sie können diese Pakete über NuGet abrufen. Folgen Sie diesen Schritten:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten**.
1. Wählen Sie **Durchsuchen**.
1. Suchen Sie online nach `Microsoft.Azure.Storage.Queue`, und wählen Sie **Installieren** aus, um die Azure Storage-Clientbibliothek und die zugehörigen Abhängigkeiten zu installieren. Dabei wird auch die Bibliothek „Microsoft.Azure.Storage.Common“ installiert, die eine Abhängigkeit der Warteschlangenbibliothek ist.
1. Suchen Sie online nach `Microsoft.Azure.ConfigurationManager`, und wählen Sie **Installieren** aus, um den Azure Configuration Manager zu installieren.

---

### <a name="determine-your-target-environment"></a>Bestimmen der Zielumgebung

Es gibt für die Ausführung der Beispiele in diesem Leitfaden zwei Umgebungsoptionen:

- Sie können den Code über ein Azure Storage-Konto in der Cloud ausführen.
- Sie können den Code über den Azurite-Speicheremulator ausführen. Azurite ist eine lokale Umgebung, in der ein Azure Storage-Konto in der Cloud emuliert wird. Azurite ist eine kostenlose Option zum Testen und Debuggen von Code, während sich Ihre Anwendung in der Entwicklung befindet. Für den Emulator wird ein bekanntes Konto mit Schlüssel verwendet. Weitere Informationen finden Sie unter [Verwenden des Azurite-Emulators für lokale Azure Storage-Entwicklung und -Tests](../common/storage-use-azurite.md).

> [!NOTE]
> Sie können den Speicheremulator als Ziel festlegen, um jegliche Kosten im Zusammenhang mit Azure Storage zu vermeiden. Wenn jedoch ein Azure Storage-Konto in der Cloud das Ziel sein soll, sind die Kosten für das Ausführen dieses Lernprogramms vernachlässigbar.

## <a name="get-your-storage-connection-string"></a>Abrufen der Speicherverbindungszeichenfolge

Die Azure Storage-Clientbibliotheken für .NET unterstützen die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieren Ihrer Anmeldeinformationen aus dem Azure-Portal

Der Beispielcode muss den Zugriff auf Ihr Speicherkonto autorisieren. Zur Autorisierung stellen Sie der Anwendung die Anmeldeinformationen für Ihr Speicherkonto in Form einer Verbindungszeichenfolge bereit. So zeigen Sie die Anmeldeinformationen für Ihr Speicherkonto an:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Suchen Sie nach Ihrem Speicherkonto.
3. Wählen Sie im Abschnitt **Einstellungen** der Speicherkontoübersicht die Option **Zugriffsschlüssel**. Daraufhin werden Ihre Zugriffsschlüssel zusammen mit der jeweiligen vollständigen Verbindungszeichenfolge angezeigt.
4. Suchen Sie unter **key1** nach dem Wert für die **Verbindungszeichenfolge**, und klicken Sie dann auf die Schaltfläche **Kopieren**, um die Verbindungszeichenfolge zu kopieren. Der Wert der Verbindungszeichenfolge wird in einem späteren Schritt einer Umgebungsvariablen hinzugefügt.

    ![Screenshot: Kopieren einer Verbindungszeichenfolge aus dem Azure-Portal](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Verbindungszeichenfolgen für Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Ihr Speicherkontoschlüssel ähnelt dem Stammkennwort für das Speicherkonto. Achten Sie darauf, den Speicherkontoschlüssel immer gut zu schützen. Geben Sie ihn nicht an andere Benutzer weiter, vermeiden Sie das Hartcodieren, und speichern Sie ihn nicht in einer Klartextdatei, auf die andere Benutzer zugreifen können. Generieren Sie Ihren Schlüssel mithilfe des Azure-Portals neu, wenn Sie der Meinung sind, dass er nicht mehr sicher ist.

Am einfachsten lässt sich die Speicherverbindungszeichenfolge in einer Konfigurationsdatei verwalten. Öffnen Sie zum Konfigurieren der Verbindungszeichenfolge die Datei `app.config` im Projektmappen-Explorer in Visual Studio. Fügen Sie den Inhalt des hier gezeigten `<appSettings>`-Elements hinzu. Ersetzen Sie `connection-string` durch den Wert, den Sie aus Ihrem Speicherkonto im Portal kopiert haben:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Ihre Konfigurationseinstellung kann beispielsweise wie folgt lauten:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Wenn Sie den Azurite-Speicheremulator verwenden möchten, können Sie eine Verknüpfung nutzen, mit der eine Zuordnung zum bekannten Kontonamen und dem Schlüssel hergestellt wird. In diesem Fall lautet die Verbindungszeichenfolge wie folgt:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Hinzufügen von using-Direktiven

Fügen Sie am Anfang der Datei `Program.cs` die folgende `using`-Anweisung hinzu:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Erstellen des Queue Storage-Clients

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Mit der [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient)-Klasse können Sie in Queue Storage gespeicherte Warteschlangen abrufen. Hier sehen Sie eine Möglichkeit zum Erstellen des Dienstclients:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Mit der [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true)-Klasse können Sie in Queue Storage gespeicherte Warteschlangen abrufen. Hier sehen Sie eine Möglichkeit zum Erstellen des Dienstclients:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Jetzt können Sie Code schreiben, der Daten aus Queue Storage liest und Daten in Queue Storage schreibt.

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

In diesem Beispiel wird die Erstellung einer Warteschlange gezeigt:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Einfügen einer Nachricht in eine Warteschlange

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, rufen Sie die [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage)-Methode auf. Eine Nachricht kann entweder eine Zeichenfolge (im UTF-8-Format) oder ein Bytearray sein. Mit dem folgenden Code wird eine Warteschlange erstellt (falls diese nicht vorhanden ist), und es wird eine Nachricht eingefügt:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true)-Objekt. Rufen Sie anschließend die [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true)-Methode auf. Eine `CloudQueueMessage` kann aus einer Zeichenfolge (im UTF-8-Format) oder einem Bytearray erstellt werden. Dieser Code erstellt eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht `Hello, World` ein: Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true)-Objekt. Rufen Sie anschließend die [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true)-Methode auf. Eine `CloudQueueMessage` kann aus einer Zeichenfolge (im UTF-8-Format) oder einem Bytearray erstellt werden. Dieser Code erstellt eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht `Hello, World` ein:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Einsehen der nächsten Nachricht

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Sie können einen Blick auf die Nachrichten in der Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages)-Methode aufrufen. Wenn Sie keinen Wert für den Parameter `maxMessages` übergeben, wird standardmäßig eine Nachricht angezeigt.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true)-Methode aufrufen.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn bei einem Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers ein Fehler auftritt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Entfernen der nächsten Nachricht aus der Warteschlange

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Sie können eine Nachricht in zwei Schritten aus einer Warteschlange entfernen. Wenn Sie [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für `ReceiveMessages` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, müssen Sie auch [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In Ihrem Code wird `DeleteMessage` direkt nach der Verarbeitung der Nachricht aufgerufen.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für `GetMessage` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, müssen Sie auch [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In Ihrem Code wird `DeleteMessage` direkt nach der Verarbeitung der Nachricht aufgerufen.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Verwenden des Async-Await-Musters mit allgemeinen Queue Storage-APIs

In diesem Beispiel wird veranschaulicht, wie das Async-Await-Muster mit allgemeinen Queue Storage-APIs verwendet wird. Im Beispiel werden jeweils die asynchronen Versionen der angegebenen Methoden aufgerufen, wie am Suffix `Async` der einzelnen Methoden erkennbar. Wenn eine asynchrone Methode verwendet wird, hält das Async-Await-Muster die lokale Ausführung an, bis der Aufruf abgeschlossen ist. Durch dieses Verhalten kann der aktuelle Thread eine andere Aktion ausführen, wodurch Leistungsengpässe vermieden werden und die allgemeine Reaktionsfähigkeit der Anwendung verbessert wird. Weitere Informationen zur Verwendung des Async-Await-Musters in .NET finden Sie unter [Async und Await (C# und Visual Basic)](/previous-versions/hh191443(v=vs.140)).

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Verwenden zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Im folgenden Codebeispiel wird [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `foreach`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von `ReceiveMessages` alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Im folgenden Codebeispiel wird [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer `foreach`-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von `GetMessages` alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Abrufen der Warteschlangenlänge

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) gibt Warteschlangeneigenschaften einschließlich der Nachrichtenanzahl zurück. Die [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)-Eigenschaft enthält die ungefähre Anzahl der Nachrichten in der Warteschlange. Diese Anzahl ist nicht niedriger ist als die tatsächliche Anzahl der Nachrichten in der Warteschlange, sie kann jedoch höher sein.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) gibt Warteschlangenattribute einschließlich der Nachrichtenanzahl zurück. Mit der [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true)-Eigenschaft wird der letzte Wert zurückgegeben, der mit der `FetchAttributes`-Methode abgerufen wurde, ohne Queue Storage aufzurufen.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete)-Methode für das Warteschlangenobjekt auf.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true)-Methode für das Warteschlangenobjekt auf.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, folgen Sie diesen Links, um mehr über komplexere Speicheraufgaben zu erfahren.

- Vollständige Informationen zu verfügbaren APIs finden Sie in der Queue Storage-Referenzdokumentation:
  - [Azure Storage-Clientbibliothek für .NET-Referenz](/dotnet/api/overview/azure/storage)
  - [Referenz zur REST-API von Azure Storage](/rest/api/storageservices/)
- Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  - [Erste Schritte mit Azure Table Storage mit .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) zum Speichern strukturierter Daten.
  - [Erste Schritte mit Azure Blob Storage mit .NET](../blobs/storage-quickstart-blobs-dotnet.md) zum Speichern unstrukturierter Daten.
  - Informationen zum Speichern relationaler Daten finden Sie unter [Herstellen von Verbindungen mit SQL-Datenbank mithilfe von .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md).
- Erfahren Sie, wie Sie mithilfe des [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)den geschriebenen Code so vereinfachen, dass er mit Azure Storage funktioniert.
