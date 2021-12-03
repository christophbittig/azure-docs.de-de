---
title: Schnellstart für Azure App Configuration mit .NET Framework | Microsoft-Dokumentation
description: In diesem Artikel erstellen Sie mit Azure App Configuration eine .NET Framework-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: f5bd0809de5aa7e7e585662ee0c98feaf9be2e79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245093"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer .NET Framework-App mit Azure App Configuration

Es gibt zwei Möglichkeiten, Azure App Configuration in eine .NET Framework-basierte Anwendung einzubinden.
- Mit dem Konfigurations-Generator für App Configuration können App Configuration-Daten in App- Einstellungen geladen werden. Ihre Anwendung greift wie immer über `ConfigurationManager` auf die Konfiguration zu. Abgesehen von den Aktualisierungen der Dateien *app.config* und *web.config* müssen Sie keine weiteren Codeänderungen vornehmen. In diesem Schnellstart werden Sie durch diese Option geführt.
- Wie im .NET Framework vorgesehen, können die App-Einstellungen nur beim Neustart der Anwendung aktualisiert werden. Der .NET-Anbieter von App Configuration ist eine .NET Standard-Bibliothek. Sie unterstützt das dynamische Zwischenspeichern und Aktualisieren der Konfiguration ohne Anwendungsneustart. Wenn die dynamische Konfiguration für Sie wichtig ist und Sie bereit sind, Codeänderungen vorzunehmen, finden Sie in den Tutorials Anleitungen zur Implementierung der dynamischen Konfigurationsaktualisierung in einer [.NET Framework-Konsolen-App](./enable-dynamic-configuration-dotnet.md) oder einer [ASP.NET-Web-App](./enable-dynamic-configuration-aspnet-netfx.md).

In diesem Schnellstart wird eine .NET Framework-Konsolen-App als Beispiel verwendet, aber das gleiche Verfahren gilt auch für eine ASP.NET Web Forms-/MVC-App.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/dotnet)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 oder höher](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** > **Schlüssel-Wert** aus, um das folgende Schlüssel-Wert-Paar hinzuzufügen:

    | Key                        | Wert                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Daten aus Azure App Configuration* |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** leer.

## <a name="create-a-net-framework-console-app"></a>Erstellen einer .NET Framework-Konsolen-App

1. Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Filtern Sie unter **Neues Projekt erstellen** nach dem Projekttyp **Konsole**, und klicken Sie in der Projektvorlagenliste auf **Konsolen-App (.NET Framework)** mit C#. Klicken Sie auf **Weiter**.

1. Geben Sie unter **Neues Projekt konfigurieren** einen Projektnamen ein. Wählen Sie unter **Framework** die Option **.NET Framework 4.7.2** oder höher aus. Klicken Sie auf **Erstellen**.

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** die folgenden NuGet-Pakete, und fügen Sie sie Ihrem Projekt hinzu.

    - *Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration*, Version 1.0.0 oder höher
    - *Microsoft.Configuration.ConfigurationBuilders.Environment*, Version 2.0.0 oder höher
    - *System.Configuration.ConfigurationManager*, Version 4.6.0 oder höher

1. Aktualisieren Sie die Datei *App.config* Ihres Projekts wie folgt:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Die Verbindungszeichenfolge Ihres App Configuration-Speichers wird aus der Umgebungsvariablen `ConnectionString` gelesen. Fügen Sie den Konfigurations-Generator `Environment` vor `MyConfigStore` in der Eigenschaft `configBuilders` des Abschnitts `appSettings` ein.

1. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `Main`-Methode für die Verwendung von App Configuration, indem Sie `ConfigurationManager` aufrufen.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable namens **ConnectionString** auf die Verbindungszeichenfolge mit schreibgeschütztem Schlüssel fest, die Sie bei der Erstellung Ihres App Configuration-Speichers erhalten haben. 

    Führen Sie bei Verwendung der Windows-Eingabeaufforderung den folgenden Befehl aus:
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Starten Sie Visual Studio, damit die Änderung wirksam wird. 

1. Drücken Sie STRG+F5, um die Konsolen-App zu erstellen und auszuführen. In der Konsole sollte die Meldung der App Configuration-Ausgabe angezeigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen neuen App Configuration-Speicher erstellt und mit einer .NET Framework-Konsolen-App verwendet. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre .NET Framework-App für das dynamische Aktualisieren der Konfigurationseinstellungen konfigurieren.

> [!div class="nextstepaction"]
> [Aktivieren der dynamischen Konfiguration in einer .NET Framework-App](./enable-dynamic-configuration-dotnet.md)

> [!div class="nextstepaction"]
> [Aktivieren der dynamischen Konfiguration in ASP.NET-Web-App](./enable-dynamic-configuration-aspnet-netfx.md)
