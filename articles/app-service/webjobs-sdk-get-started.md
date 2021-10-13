---
title: Tutorial für die ereignisgesteuerte Hintergrundverarbeitung mit dem WebJobs SDK
description: Erfahren Sie, wie Sie Ihre Web-Apps zum Ausführen von Hintergrundaufgaben aktivieren. Verwenden Sie dieses Tutorial zum Einstieg in das WebJobs SDK.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.date: 06/25/2021
ms.author: glenga
ms.topic: tutorial
ms.openlocfilehash: 2454957e6cff781199ad9217ed02ab5241d104c2
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661253"
---
# <a name="tutorial-get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Tutorial: Erste Schnitte mit dem Azure WebJobs SDK für die ereignisgesteuerte Hintergrundverarbeitung

Erste Schritte mit dem Azure WebJobs SDK für Azure App Service ermöglichen Ihren Web-Apps das Ausführen von Hintergrundaufgaben, geplanten Aufgaben und das Reagieren auf Ereignisse. 

Erstellen Sie mit Visual Studio 2019 eine .NET Core-Konsolen-App, die mithilfe des WebJobs SDK auf Azure Storage-Warteschlangennachrichten reagiert, das Projekt lokal ausführt und schließlich in Azure bereitstellt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Konsolenanwendung
> * Hinzufügen einer Funktion
> * Lokales Testen
> * In Azure bereitstellen
> * Aktivieren der Application Insights-Protokollierung
> * Hinzufügen von Eingabe-/Ausgabebindungen

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio 2019 mit der Workload **Azure-Entwicklung**. [Visual Studio 2019 installieren](/visualstudio/install/)

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).

## <a name="create-a-console-app"></a>Erstellen einer Konsolenanwendung
In diesem Abschnitt erstellen Sie zunächst ein Projekt in Visual Studio 2019. Als Nächstes fügen Sie Tools für die Azure-Entwicklung, Codeveröffentlichung und Funktionen hinzu, die auf Trigger und Aufruffunktionen lauschen. Schließlich richten Sie die Konsolenprotokollierung ein, die ein älteres Überwachungstool deaktiviert und einen Konsolenanbieter mit Standardfilterung aktiviert. 

>[!NOTE]  
>Die Verfahren in diesem Artikel werden überprüft, um eine .NET Core-Konsolen-App zu erstellen, die unter .NET Core 3.1 ausgeführt wird.

### <a name="create-a-project"></a>Erstellen eines Projekts

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.

1. Wählen Sie in **Neues Projekt erstellen** die Option **Konsolenanwendung (C#)** und dann **Weiter** aus.

1. Benennen Sie in **Neues Projekt konfigurieren** das Projekt mit *WebJobsSDKSample*, und wählen Sie **Weiter** aus.

1. Wählen Sie Ihr **Zielframework** und dann **Erstellen** aus. Dieses Tutorial wurde mit .NET Core 3.1 verifiziert.

### <a name="install-webjobs-nuget-packages"></a>Installieren von WebJobs-NuGet-Paketen

Installieren Sie das aktuelle WebJobs-NuGet-Paket. Dieses Paket enthält Microsoft.Azure.WebJobs (WebJobs SDK), mit dem Sie Ihren Funktionscode in WebJobs in Azure App Service veröffentlichen können.

1. Rufen Sie die aktuelle stabile 3.x-Version des [NuGet-Pakets Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/) ab.

2. Gehen Sie in Visual Studio zu **Tools** > **NuGet-Paket-Manager**.

3. Wählen Sie **Paket-Manager-Konsole** aus. Es werden eine Liste von NuGet-Cmdlets, ein Link zur Dokumentation und ein `PM>`-Einstiegspunkt angezeigt.

4. Ersetzen Sie im folgenden Befehl `<3_X_VERSION>` durch die aktuelle Versionsnummer, die Sie in Schritt 1 gefunden haben. 

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```
5. Führen Sie in der **Paket-Manager-Konsole** den folgenden Befehl aus. Die Erweiterungsliste wird angezeigt und automatisch installiert. 
  
### <a name="create-the-host"></a>Erstellen des Hosts

Der Host ist der Laufzeitcontainer für Funktionen, der auf Trigger lauscht und Funktionen aufruft. In den folgenden Schritten wird ein Host erstellt, der [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) implementiert. Dies ist der generische Host in ASP.NET Core.

1. Wählen Sie die Registerkarte **Program.cs** aus, und fügen Sie diese `using`-Anweisungen hinzu:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Ersetzen Sie auch in **Program.cs** die `Main`-Methode durch den folgenden Code:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

In ASP.NET Core werden Hostkonfigurationen durch Aufrufen von Methoden in der [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)-Instanz festgelegt. Weitere Informationen finden Sie unter [Generischer .NET-Host](/aspnet/core/fundamentals/host/generic-host). Die `ConfigureWebJobs`-Erweiterungsmethode initialisiert den WebJobs-Host. Initialisieren Sie in `ConfigureWebJobs` bestimmte Bindungserweiterungen, z. B. die Storage-Bindungserweiterung, und legen Sie die Eigenschaften dieser Erweiterungen fest.  

### <a name="enable-console-logging"></a>Aktivieren der Konsolenprotokollierung

Richten Sie die Konsolenprotokollierung ein, die das [ASP.NET Core-Protokollierungsframework](/aspnet/core/fundamentals/logging) verwendet. Dieses Framework, Microsoft.Extensions.Logging, enthält eine API, die mit einer Vielzahl von Protokollierungsanbietern (integriert und von Drittanbietern) zusammenarbeiten kann.

1. Rufen Sie die aktuelle stabile Version des [`Microsoft.Extensions.Logging.Console`-NuGet-Pakets](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) (enthält `Microsoft.Extensions.Logging`) auf.

2. Ersetzen Sie im folgenden Befehl `<3_X_VERSION>` durch die aktuelle Versionsnummer, die Sie in Schritt 1 gefunden haben. Jeder Typ des NuGet-Pakets verfügt über eine eindeutige Versionsnummer.

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
3. Geben Sie in der **Paket-Manager-Konsole** die aktuelle Versionsnummer ein, und führen Sie den Befehl aus. Die Erweiterungsliste wird angezeigt und automatisch installiert. 

4. Fügen Sie auf der Registerkarte **Program.cs** diese `using`-Anweisung hinzu:

   ```cs
   using Microsoft.Extensions.Logging;
   ```
5. Fügen Sie unter **Program.cs** fortfahrend der [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)-Methode vor dem `Build`-Befehl [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) hinzu. Die [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole)-Methode fügt der Konfiguration Konsolenprotokollierung hinzu.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Die `Main`-Methode sieht jetzt wie folgt aus:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Diese Ergänzung bewirkt diese Änderungen:

    * Deaktiviert die [Dashboardprotokollierung](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Das Dashboard ist ein älteres Überwachungstool, und die Dashboardprotokollierung wird in Produktionsszenarien mit hohem Durchsatz nicht empfohlen.
    * Fügt den Konsolenanbieter mit der Standard[filterung](webjobs-sdk-how-to.md#log-filtering) hinzu.

Jetzt können Sie eine Funktion hinzufügen, die durch eingehende Nachrichten in einer Azure Storage-Warteschlange ausgelöst wird.

## <a name="add-a-function"></a>Hinzufügen einer Funktion

Eine Funktion ist eine Codeeinheit, die nach einem Zeitplan ausgeführt, auf Ereignissen basierend ausgelöst oder bei Bedarf ausgeführt wird. Ein Trigger lauscht auf ein Dienstereignis. Im Kontext des WebJobs SDK bezieht sich „ausgelöst“ nicht auf den Bereitstellungsmodus. Ereignisgesteuerte oder geplante WebJobs, die mit dem SDK erstellt wurden, sollten immer als fortlaufende WebJobs mit aktiviertem „Always On“ bereitgestellt werden. 

In diesem Abschnitt erstellen Sie eine Funktion, die durch Nachrichten in einer Azure Storage-Warteschlange ausgelöst wird. Zunächst müssen Sie eine Bindungserweiterung hinzufügen, um eine Verbindung mit Azure Storage herzustellen.

### <a name="install-the-storage-binding-extension"></a>Installieren der Storage-Bindungserweiterung

Ab Version 3 des WebJobs SDK müssen Sie zum Herstellen einer Verbindung mit Azure Storage-Diensten ein separates Storage-Bindungserweiterungspaket installieren. 

1. Rufen Sie die aktuelle stabile Version des NuGet-Pakets [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), Version 3.x auf.

1. Ersetzen Sie `<3_X_VERSION>` im folgenden Befehl durch die aktuelle Versionsnummer aus Schritt1. Jeder Typ des NuGet-Pakets verfügt über eine eindeutige Versionsnummer. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
1. Führen Sie in der **Paket-Manager-Konsole** den Befehl mit der aktuellen Versionsnummer am `PM>`-Einstiegspunkt aus.

1. Fügen Sie in **Program.cs** in der `ConfigureWebJobs`-Erweiterungsmethode die `AddAzureStorage`-Methode der [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)-Instanz (vor dem `Build`-Befehl) hinzu, um die Storage-Erweiterung zu initialisieren. An diesem Punkt sieht die `ConfigureWebJobs`-Methode wie folgt aus:

    ```cs
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    ```
1. Fügen Sie den folgenden Code nach dem Instanziieren von `builder` in der `Main`-Methode hinzu:

    ```csharp
    builder.UseEnvironment(EnvironmentName.Development);
     ```

    Die Ausführung im [Entwicklungsmodus](webjobs-sdk-how-to.md#host-development-settings) reduziert das [exponentielle Backoff der Warteschlangenabrufe](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm), was die Zeitspanne erheblich verlängern kann, die die Runtime benötigt, um die Nachricht zu finden und die Funktion aufzurufen. Sie sollten diese Codezeile entfernen oder zu `Production` wechseln, wenn Sie mit dem Entwickeln und Testen fertig sind. 

    Die Methode `Main` sollte nun wie im folgenden Beispiel aussehen:

    ```csharp
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureLogging((context, b) =>
        {
            b.AddConsole();
        });
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
            b.AddAzureStorage();
        });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

### <a name="create-a-queue-triggered-function"></a>Erstellen einer Funktion mit Auslösung per Warteschlange

Das `QueueTrigger`-Attribut weist die Runtime an, diese Funktion aufzurufen, wenn eine neue Nachricht in die Azure Storage-Warteschlange namens `queue` geschrieben wird. Der Inhalt der Warteschlangennachricht wird für den Methodencode im `message`-Parameter bereitgestellt. Die Triggerdaten werden im Text der Methode verarbeitet. In diesem Beispiel protokolliert der Code nur die Nachricht.

1. Wählen Sie im Projektmappen-Explorer mit der rechten Maustaste das Projekt aus, und wählen Sie dann **Hinzufügen** > **Neues Element** und **Klasse** aus. 

2. Nennen Sie die neue C#-Klassendatei *Functions.cs*, und wählen Sie **Hinzufügen** aus.

3. Ersetzen Sie die generierte Vorlage in *Functions.cs* durch den folgenden Code:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

    Sie sollten die *Functions*-Klasse als `public static` markieren, damit die Runtime auf die Methode zugreifen und sie ausführen kann. In dem obigen Codebeispiel wird, wenn einer Warteschlange mit dem Namen `queue` eine Nachricht hinzugefügt wird, die Funktion ausgeführt, und die `message`-Zeichenfolge wird in die Protokolle geschrieben. Die überwachte Warteschlange befindet sich im Azure Storage-Standardkonto, das Sie als Nächstes erstellen.
   
Der `message`-Parameter muss keine Zeichenfolge sein. Sie können auch eine Bindung an ein JSON-Objekt, Bytearray oder [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)-Objekt erstellen. Weitere Informationen finden Sie unter [Verwendung des Warteschlangentriggers](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Jeder Bindungstyp (z. B. Warteschlangen, Blobs oder Tabellen) weist einen anderen Satz von Parametertypen auf, an die Sie binden können.

### <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Der lokal ausgeführte Azure Storage-Emulator verfügt nicht über alle Funktionen, die das WebJobs SDK benötigt. Sie erstellen ein Speicherkonto in Azure und konfigurieren das Projekt für dessen Verwendung. 

Informationen zum Erstellen eines universellen v2-Speicherkontos finden Sie unter [Speicherkonto erstellen](../storage/common/storage-account-create.md?tabs=azure-portal).

### <a name="locate-and-copy-your-connection-string"></a>Finden und Kopieren der Verbindungszeichenfolge
Zum Konfigurieren des Speichers ist eine Verbindungszeichenfolge erforderlich. Halten Sie diese Verbindungszeichenfolge für die nächsten Schritte bereit.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, und wählen Sie **Einstellungen** aus.
1. Wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus.
1. Wählen Sie für die **Verbindungszeichenfolge** unter **key1** das Symbol **In Zwischenablage kopieren** aus.

     ![key](./media/webjobs-sdk-get-started/connection-key.png)

### <a name="configure-storage-to-run-locally"></a>Konfigurieren der lokalen Ausführung des Speichers

Das WebJobs SDK sucht in den Anwendungseinstellungen in Azure nach der Verbindungszeichenfolge für den Speicher. Bei lokaler Ausführung wird in der lokalen Konfigurationsdatei oder in den Umgebungsvariablen nach diesem Wert gesucht.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element...** und **JavaScript-JSON-Konfigurationsdatei** aus. Geben Sie der neuen Datei den Namen *appsettings.json*, und wählen Sie **Hinzufügen** aus. 

1. Fügen Sie in der neuen Datei wie im folgenden Beispiel ein `AzureWebJobsStorage`-Feld hinzu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Ersetzen Sie *{Speicherverbindungszeichenfolge}* durch die Verbindungszeichenfolge, die Sie zuvor kopiert haben.

1. Wählen Sie im Projektmappen-Explorer die Datei *appsettings.json* aus, und legen Sie im Fenster **Eigenschaften** die Option **In Ausgabeverzeichnis kopieren** auf **Kopieren, wenn neuer** fest.

Da diese Datei ein Verbindungszeichenfolgen-Geheimnis enthält, sollten Sie die Datei nicht in einem Remotecoderepository speichern. Nachdem Sie Ihr Projekt in Azure veröffentlicht haben, können Sie die gleiche App-Einstellung für Verbindungszeichenfolgen in Azure App Service Ihrer App hinzufügen.

## <a name="test-locally"></a>Lokales Testen

Erstellen Sie das Projekt lokal, führen Sie es aus, und erstellen Sie eine Nachrichtenwarteschlange, um die Funktion auszulösen.

1. Erweitern Sie in Visual Studio im **Cloud-Explorer** den Knoten für Ihr neues Speicherkonto, und klicken Sie dann mit der rechten Maustaste auf **Warteschlangen**.

1. Wählen Sie **Warteschlange erstellen** aus.

1. Geben Sie *queue* als Namen für die Warteschlange ein, und wählen Sie dann **OK** aus.

   ![Screenshot, der zeigt, wo Sie die Warteschlange erstellen und sie „Warteschlange“ nennen. ](./media/webjobs-sdk-get-started/create-queue.png)

1. Klicken Sie mit der rechten Maustaste auf den Knoten für die neue Warteschlange, und wählen Sie dann **Öffnen** aus.

1. Wählen Sie das Symbol **Nachricht hinzufügen** aus.

   ![Screenshot, der das Symbol „Nachricht hinzufügen“ hervorhebt.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Geben Sie im Dialogfeld **Nachricht hinzufügen** die Zeichenfolge *Hello World!* als **Nachrichtentext** ein, und wählen Sie dann **OK** aus. Es gibt jetzt eine Nachricht in der Warteschlange.

   ![Erstellen einer Warteschlange](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Drücken Sie **STRG+F5**, um das Projekt auszuführen.

   Die Konsole zeigt, dass die Runtime Ihre Funktion gefunden hat. Weil Sie in der `ProcessQueueMessage`-Funktion das `QueueTrigger`-Attribut verwendet haben, lauscht die WebJobs-Runtime in der Warteschlange namens `queue` auf Nachrichten. Wenn in dieser Warteschlange eine neue Nachricht gefunden wird, ruft die Runtime die Funktion auf und übergibt den Wert der Nachrichtenzeichenfolge.

1. Wechseln Sie zurück zum Fenster **Warteschlange**, und aktualisieren Sie es. Die Nachricht ist nicht mehr vorhanden, da sie durch die lokal ausgeführte Funktion verarbeitet wurde.

1. Schließen Sie das Konsolenfenster. 

Jetzt ist es an der Zeit, Ihr WebJobs SDK-Projekt in Azure zu veröffentlichen.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Bereitstellen in Azure

Während der Bereitstellung erstellen Sie eine App Service-Instanz, in der Ihre Funktionen ausgeführt werden. Wenn Sie in Azure eine .NET Core-Konsolen-App in App Service veröffentlichen, wird sie automatisch als WebJob ausgeführt. Weitere Informationen zum Veröffentlichen finden Sie unter [Entwickeln und Bereitstellen von WebJobs mit Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

### <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

### <a name="enable-always-on"></a>Aktivieren von AlwaysOn

Für einen fortlaufenden WebJob sollten Sie die Einstellung „Always On“ auf der Website aktivieren, damit Ihre WebJobs ordnungsgemäß ausgeführt werden. Wenn Sie Always On nicht aktivieren, wird die Runtime nach einigen Minuten Inaktivität in den Leerlauf versetzt. 

1. Wählen Sie auf der Seite **Veröffentlichen** die drei Punkte oberhalb von **Hosting** aus, um **Aktionen des Abschnitts „Hostingprofil“** anzuzeigen, und wählen Sie **In Azure-Portal öffnen** aus.  

1. Wählen Sie unter **Einstellungen** die Option **Konfiguration** > **Allgemeine Einstellungen** aus, legen Sie **Always On** auf **Ein** fest, und wählen Sie dann **Speichern** und **Fortfahren** aus, um die Site neu zu starten.

### <a name="publish-the-project"></a>Veröffentlichen des Projekts

Da die Web-App in Azure erstellt wurde, ist es an der Zeit, das WebJobs-Projekt zu veröffentlichen. 

1. Wählen Sie auf der Seite **Veröffentlichen** unter **Hosting** die Schaltfläche „Bearbeiten“ aus, ändern Sie den **WebJob-Typ** in `Continuous`, und wählen Sie **Speichern** aus. Dadurch wird sichergestellt, dass der WebJob ausgeführt wird, wenn der Warteschlange Nachrichten hinzugefügt werden. Ausgelöste WebJobs werden in der Regel nur für manuelle Webhooks verwendet. 

1. Wählen Sie die Schaltfläche **Veröffentlichen** oben rechts auf der Seite **Veröffentlichen** aus. Nach Abschluss des Vorgangs wird Ihr WebJob in Azure ausgeführt.

### <a name="create-a-storage-connection-app-setting"></a>Erstellen einer Speicherverbindungs-App-Einstellung

Sie müssen die gleiche Einstellung für die Speicherverbindungszeichenfolge in Azure erstellen, die Sie lokal in Ihrer appsettings.json-Konfigurationsdatei verwendet haben. Dadurch können Sie die Verbindungszeichenfolge sicherer speichern und   

1. Wählen Sie auf der Profilseite **Veröffentlichen** die drei Punkte oberhalb von **Hosting** aus, um **Aktionen des Abschnitts „Hostingprofil“** anzuzeigen, und wählen Sie **Azure App Service-Einstellungen verwalten** aus.

1. Wählen Sie unter **Anwendungseinstellungen** die Option **+ Einstellung hinzufügen** aus.

1. Geben Sie in **Name der neuen App-Einstellung** `AzureWebJobsStorage` ein, und wählen Sie **OK** aus. 
 
1. Fügen Sie in **Remote** die Verbindungszeichenfolge aus Ihrer lokalen Einstellung ein, und wählen Sie **OK** aus. 

Die Verbindungszeichenfolge ist jetzt in Ihrer App in Azure festgelegt.

### <a name="trigger-the-function-in-azure"></a>Auslösen der Funktion in Azure

1. Achten Sie darauf, keine lokale Ausführung durchzuführen. Schließen Sie das Konsolenfenster, wenn es noch geöffnet ist. Andernfalls verarbeitet möglicherweise die lokale Instanz als erste die von Ihnen erstellten Warteschlangennachrichten.

1. Fügen Sie in Visual Studio auf der Seite **Warteschlange** wie zuvor eine Nachricht zur Warteschlange hinzu.

1. Aktualisieren Sie die Seite **Warteschlange**. Daraufhin verschwindet die neue Nachricht, da sie von der in Azure ausgeführten Funktion verarbeitet wurde.

## <a name="enable-application-insights-logging"></a>Aktivieren der Application Insights-Protokollierung

Wenn WebJob in Azure ausgeführt wird, können Sie die Funktionsausführung nicht durch Anzeigen der Konsolenausgabe überwachen. Um Ihren WebJob überwachen zu können, sollten Sie eine zugeordnete [Application Insights-Instanz](../azure-monitor/app/app-insights-overview.md) erstellen, wenn Sie Ihr Projekt veröffentlichen.

### <a name="create-an-application-insights-instance"></a>Erstellen einer Application Insights-Instanz

1. Wählen Sie auf der Profilseite **Veröffentlichen** die drei Punkte oberhalb von **Hosting** aus, um **Aktionen des Abschnitts „Hostingprofil“** anzuzeigen, und wählen Sie **In Azure-Portal öffnen** aus.

1. Wählen Sie in der Web-App unter **Einstellungen** die Option **Application Insights** und dann **Application Insights aktivieren** aus.

1. Überprüfen Sie den generierten **Ressourcennamen** für die Instanz und den **Speicherort**, und wählen Sie **Anwenden** aus. 

1. Wählen Sie **Einstellungen** unter **Konfiguration** aus, und überprüfen Sie, ob ein neuer `APPINSIGHTS_INSTRUMENTATIONKEY` erstellt wurde. Mit diesem Schlüssel wird eine Verbindung Ihrer WebJob-Instanz mit Application Insights hergestellt.   

Zur Nutzung der [Application Insights](../azure-monitor/app/app-insights-overview.md)-Protokollierung müssen Sie auch Ihren Protokollierungscode aktualisieren.

### <a name="install-the-application-insights-extension"></a>Installieren der Application Insights-Erweiterung

1. Rufen Sie die aktuelle stabile Version des NuGet-Pakets [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights), Version 3.x auf.

2. Ersetzen Sie `<3_X_VERSION>` im folgenden Befehl durch die aktuelle Versionsnummer aus Schritt1. Jeder Typ des NuGet-Pakets verfügt über eine eindeutige Versionsnummer. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
    ```
3. Führen Sie in der **Paket-Manager-Konsole** den Befehl mit der aktuellen Versionsnummer am `PM>`-Einstiegspunkt aus.

### <a name="initialize-the-application-insights-logging-provider"></a>Initialisieren des Application Insights-Protokollierungsanbieters

Öffnen Sie *Program.cs*, und fügen Sie nach dem Aufruf von `AddConsole` den folgenden Initialisierer in `ConfigureLogging` hinzu:

```csharp
// If the key exists in settings, use it to enable Application Insights.
string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
if (!string.IsNullOrEmpty(instrumentationKey))
{
    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
}
```

Die `Main`-Methode sollte nun wie im folgenden Beispiel aussehen: 

```csharp
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment(EnvironmentName.Development);
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    builder.ConfigureLogging((context, b) =>
            {
                b.AddConsole();

                // If the key exists in settings, use it to enable Application Insights.
                string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                if (!string.IsNullOrEmpty(instrumentationKey))
                {
                    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                }
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Hiermit wird der Application Insights-Protokollierungsanbieter mit standardmäßiger [Filterung](webjobs-sdk-how-to.md#log-filtering) initialisiert. Bei lokaler Ausführung werden alle Protokolle der Stufe „Information“ und höher sowohl in die Konsole als auch in Application Insights geschrieben.

### <a name="republish-the-project-and-trigger-the-function-again"></a>Erneutes Veröffentlichen des Projekts und erneutes Auslösen der Funktion

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Verwenden Sie wie zuvor den **Cloud-Explorer** in Visual Studio, um eine Warteschlangennachricht wie [zuvor](#test-locally) zu erstellen, mit der Ausnahme, dass Sie hier *Hello App Insights!* eingeben. als Nachrichtentext eingeben.

1. Wählen Sie auf der Profilseite **Veröffentlichen** die drei Punkte oberhalb von **Hosting** aus, um **Aktionen des Abschnitts „Hostingprofil“** anzuzeigen, und wählen Sie **In Azure-Portal öffnen** aus.

1. Wählen Sie in der Web-App unter **Einstellungen** die Option **Application Insights** und dann **Application Insights-Daten anzeigen** aus.

1. Wählen Sie **Suchen** und dann **Alle Daten in den letzten 24 Stunden anzeigen** aus.

   ![„Suchen“ auswählen](./media/webjobs-sdk-get-started/select-search.png)

1. Wenn die Nachricht *Hello App Insights!* nicht angezeigt wird, wählen Sie **Aktualisieren** in regelmäßigen Abständen mehrere Minuten lang aus. Die Protokolle werden nicht sofort angezeigt, weil es eine Weile dauert, bis der Application Insights-Client die verarbeiteten Protokolle leert.

   ![Protokolle in Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

## <a name="add-inputoutput-bindings&quot;></a>Hinzufügen von Eingabe-/Ausgabebindungen

Bindungen vereinfachen Code, der Daten liest und schreibt. Eingabebindungen vereinfachen Code, der Daten liest. Ausgabebindungen vereinfachen Code, der Daten schreibt.  

### <a name=&quot;add-input-binding&quot;></a>Hinzufügen der Eingabebindung

Eingabebindungen vereinfachen Code, der Daten liest. In diesem Beispiel ist die Warteschlangennachricht der Name eines Blobs, mit dem Sie ein Blob in Azure Storage suchen und lesen.

1. Ersetzen Sie in *Functions.cs* die `ProcessQueueMessage`-Methode durch den folgenden Code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger(&quot;queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   In diesem Code ist `queueTrigger` ein [Bindungsausdruck](../azure-functions/functions-bindings-expressions-patterns.md), was bedeutet, dass er zur Laufzeit in einen anderen Wert aufgelöst wird.  Zur Laufzeit weist er den Inhalt der Warteschlangennachricht auf.

1. Fügen Sie ein `using`-Element hinzu:

   ```cs
   using System.IO;
   ```

1. Erstellen Sie einen Blobcontainer in Ihrem Speicherkonto.

   a. Erweitern Sie in Visual Studio in **Cloud-Explorer** den Knoten für Ihr Speicherkonto, klicken Sie mit der rechten Maustaste auf **Blobs**, und wählen Sie dann **Blobcontainer erstellen** aus.

   b. Geben Sie im Dialogfeld **Blobcontainer erstellen** den Text *container* als Containernamen ein, und wählen Sie dann **OK** aus.

1. Laden Sie die Datei *Program.cs* in den Blobcontainer hoch. (Diese Datei wird hier als Beispiel verwendet. Sie können eine beliebige Textdatei hochladen und eine Warteschlangennachricht mit dem Namen der Datei erstellen.)

   a. Doppelklicken Sie im **Cloud-Explorer** auf den Knoten für den Container, den Sie erstellt haben.

   b. Wählen Sie im Fenster **Container** die Schaltfläche **Hochladen** aus.

   ![Schaltfläche „Blob hochladen“](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Suchen Sie die Datei *Program.cs*, wählen Sie die Datei aus, und wählen Sie dann **OK** aus.

1. Erstellen Sie in der zuvor von Ihnen erstellten Warteschlange eine Warteschlangennachricht, und verwenden Sie *Program.cs* als Text der Nachricht.

   ![Warteschlangennachricht „Program.cs“](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Führen Sie das Projekt lokal aus.

   Die Warteschlangennachricht löst die Funktion aus, die dann das Blob liest und dessen Länge protokolliert. Die Konsolenausgabe sieht wie folgt aus:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```
### <a name="add-an-output-binding"></a>Hinzufügen einer Ausgabebindung

Ausgabebindungen vereinfachen Code, der Daten schreibt. In diesem Beispiel wird der vorherige Code geändert, indem eine Kopie des Blobs geschrieben wird, statt dessen Größe zu protokollieren. Blobspeicherbindungen sind im Azure Storage-Erweiterungspaket enthalten, das wir zuvor installiert haben.

1. Ersetzen Sie die `ProcessQueueMessage`-Methode durch den folgenden Code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Erstellen Sie eine weitere Warteschlangennachricht mit dem Nachrichtentext *Program.cs*.

1. Führen Sie das Projekt lokal aus.

   Die Warteschlangennachricht löst die Funktion aus, die dann das Blob liest, dessen Länge protokolliert und ein neues Blob erstellt. Die Konsolenausgabe ist identisch, aber wenn Sie zum Fenster des Blobcontainers wechseln und **Aktualisieren** auswählen, wird ein neues Blob mit dem Namen *copy-Program.cs* angezeigt.

### <a name="republish-the-project"></a>Erneutes Veröffentlichen des Projekts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Stellen Sie sicher, dass im Dialogfeld **Veröffentlichen** das aktuelle Profil ausgewählt ist, und wählen Sie dann **Veröffentlichen** aus. Die Ergebnisse der Veröffentlichung finden Sie im Fenster **Ausgabe**.
 
1. Überprüfen Sie die Funktion in Azure, indem Sie erneut eine Datei in den Blobcontainer hochladen und der Warteschlange eine Nachricht in Form des Namens der hochgeladenen Datei hinzufügen. Daraufhin wird die Nachricht aus der Warteschlange entfernt und eine Kopie der Datei im Blobcontainer erstellt. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde gezeigt, wie Sie ein WebJobs SDK 3.x-Projekt erstellen, ausführen und bereitstellen.

> [!div class="nextstepaction"]
> [Weitere Informationen zum WebJobs SDK](webjobs-sdk-how-to.md)
