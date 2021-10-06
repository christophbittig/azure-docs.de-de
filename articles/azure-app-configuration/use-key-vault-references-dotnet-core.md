---
title: 'Tutorial: Verwenden der Key Vault-Verweise von Azure App Configuration in einer ASP.NET Core-App | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Key Vault-Verweise von Azure App Configuration in einer ASP.NET Core-App verwenden.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 9f095bbadf8f395b809d46c8beea5f6665932d12
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357915"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Verwenden von Key Vault-Verweisen in einer ASP.NET Core-App

In diesem Tutorial erfahren Sie, wie Sie den Azure App Configuration-Dienst zusammen mit Azure Key Vault verwenden. Die Dienste App Configuration und Key Vault ergänzen sich gegenseitig und werden bei den meisten Anwendungsbereitstellungen gemeinsam eingesetzt.

App Configuration unterstützt Sie beim parallelen Verwenden der Dienste, indem Schlüssel erstellt werden, mit denen auf in Key Vault gespeicherte Werte verwiesen wird. Wenn mit App Configuration Schlüssel dieser Art erstellt werden, werden nicht die eigentlichen Werte, sondern die URIs der Key Vault-Werte gespeichert.

Ihre Anwendung nutzt den App Configuration-Clientanbieter, um Key Vault-Verweise abzurufen, wie dies auch für alle anderen Schlüssel der Fall ist, die in App Configuration gespeichert sind. In diesem Fall sind die in App Configuration gespeicherten Werte URIs, mit denen auf die Werte in Key Vault verwiesen wird. Es sind keine Key Vault-Werte oder -Anmeldeinformationen. Da der Clientanbieter die Schlüssel als Key Vault-Verweise erkennt, wird Key Vault zum Abrufen der zugehörigen Werte verwendet.

Ihre Anwendung ist für die ordnungsgemäße Authentifizierung sowohl bei App Configuration als auch bei Key Vault zuständig. Die beiden Dienste kommunizieren nicht direkt.

In diesem Tutorial wird veranschaulicht, wie Sie Key Vault-Verweise in Ihrem Code implementieren. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist beispielsweise ein plattformübergreifender Code-Editor, der für die Betriebssysteme Windows, macOS und Linux verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines App Configuration-Schlüssels, der auf einen in Key Vault gespeicherten Wert verweist
> * Zugreifen auf den Wert dieses Schlüssels über eine ASP.NET Core-Webanwendung

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Erstellen eines Tresors

1. Wählen Sie oben links im Azure-Portal die Option **Ressource erstellen** aus:

    ![Screenshot: Option „Ressource erstellen“ im Azure-Portal](./media/quickstarts/search-services.png)
1. Geben Sie im Suchfeld **Key Vault** ein, und wählen Sie in der Dropdown-Option **Key Vault** aus.
1. Wählen Sie in der Ergebnisliste links **Key Vault** aus.
1. Wählen Sie unter **Schlüsseltresore** die Option **Hinzufügen** aus.
1. Geben Sie auf der rechten Seite unter **Schlüsseltresor erstellen** die folgenden Informationen ein:
    - Wählen Sie die Option **Abonnement** aus, um ein Abonnement auszuwählen.
    - Geben Sie unter **Ressourcengruppe** eine bereits vorhandene Ressourcengruppe ein, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie einen Ressourcengruppennamen ein.
    - Unter **Schlüsseltresorname** müssen Sie einen eindeutigen Namen eingeben.
    - Wählen Sie in der Dropdownliste **Region** einen Ort aus.
1. Übernehmen Sie für die anderen Optionen unter **Schlüsseltresor erstellen** die Standardwerte.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Das System überprüft die von Ihnen eingegebenen Daten und zeigt sie an. Klicken Sie auf **Erstellen**.

An diesem Punkt ist nur Ihr Azure-Konto für den Zugriff auf diesen neuen Tresor autorisiert.


## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Zum Hinzufügen eines Geheimnisses zum Tresor müssen Sie lediglich einige zusätzliche Schritte ausführen. In diesem Fall fügen Sie eine Nachricht hinzu, die Sie verwenden können, um den Key Vault-Abruf zu testen. Die Nachricht hat den Namen **Nachricht**, und Sie speichern darin den Wert „Hallo von Key Vault“.

1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Geheimnisse** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.
1. Geben Sie im Bereich **Geheimnis erstellen** die folgenden Werte ein:
    - **Uploadoptionen**: Geben Sie **Manuell** ein.
    - **Name**: Geben Sie **Nachricht** ein.
    - **Value**: Geben Sie **Hallo von Key Vault** ein.
1. Übernehmen Sie für die anderen Eigenschaften unter **Geheimnis erstellen** die Standardwerte.
1. Klicken Sie auf **Erstellen**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hinzufügen eines Key Vault-Verweises zu App Configuration

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie die Option **Alle Ressourcen** und dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie **Konfigurations-Explorer** aus.

1. Wählen Sie **+ Erstellen** > **Schlüsseltresorverweis** aus, und geben Sie dann die folgenden Werte an:
    - **Key**: Wählen Sie **TestApp:Settings:KeyVaultMessage** aus.
    - **Bezeichnung:** Lassen Sie diesen Wert leer.
    - **Abonnement**, **Ressourcengruppe** und **Schlüsseltresor**: Geben Sie die Werte ein, die den Werten des im vorherigen Abschnitt erstellten Schlüsseltresors entsprechen.
    - **Geheimnis**: Wählen Sie das Geheimnis mit dem Namen **Nachricht** aus, das Sie im vorherigen Abschnitt erstellt haben.

![Ein Screenshot zum Erstellen eines neuen Key Vault-Referenzformulars](./media/create-key-vault-reference.png)

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualisieren des Codes für die Verwendung eines Key Vault-Verweises

1. Führen Sie den folgenden Befehl aus, um einen Verweis auf die erforderlichen NuGet-Pakete hinzuzufügen:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Öffnen Sie die Datei *Program.cs*, und fügen Sie Verweise auf die folgenden erforderlichen Pakete hinzu:

    ```csharp
    using Azure.Identity;
    ```

1. Aktualisieren Sie die `CreateWebHostBuilder`-Methode für die Verwendung von App Configuration, indem Sie die Methode `config.AddAzureAppConfiguration` aufrufen. Fügen Sie die Option `ConfigureKeyVault` ein, und übergeben Sie die richtigen Anmeldeinformationen an Key Vault.

     #### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

1. Wenn Sie die Verbindung mit App Configuration initialisiert haben, richten Sie die Verbindung mit Key Vault ein, indem Sie die Methode `ConfigureKeyVault` aufrufen. Nach der Initialisierung können Sie auf die Werte der Key Vault-Verweise genauso zugreifen, wie Sie bei den Werten regulärer App Configuration-Schlüssel vorgehen.

    Um diesen Prozess in Aktion zu sehen, öffnen Sie die Datei *Index.cshtml* im Ordner **Ansichten** > **Home**. Ersetzen Sie ihren Inhalt durch den folgenden Code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Sie greifen auf den Wert des Key Vault-Verweises **TestApp:Settings:KeyVaultMessage** genauso wie auf den Konfigurationswert von **TestApp:Settings:Message** zu.


## <a name="grant-your-app-access-to-key-vault"></a>Gewähren des Zugriffs auf Key Vault für Ihre App

Die Azure App Configuration greift nicht auf Ihren Schlüsseltresor zu. Ihre App liest direkt aus dem Key Vault, sodass Sie Ihrer App Lesezugriff auf die Geheimnisse in Ihrem Schlüsseltresor gewähren müssen. Auf diese Weise bleibt das Geheimnis immer in Ihrer App. Der Zugriff kann entweder mithilfe einer [Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md) oder der [rollenbasierten Zugriffssteuerung](../key-vault/general/rbac-guide.md)von Azure gewährt werden.

Sie verwenden `DefaultAzureCredential` in Ihrem obigen Code. Es handelt sich um eine aggregierte Tokenanmeldeinformation, die automatisch eine Reihe von Anmeldeinformationstypen wie`EnvironmentCredential`, `ManagedIdentityCredential`, `SharedTokenCacheCredential`, and `VisualStudioCredential` ausprobiert. Weitere Informationen hierzu finden Sie in der Dokumentation unter [DefaultAzureCredential-Klasse.](/dotnet/api/azure.identity.defaultazurecredential) Sie können `DefaultAzureCredential` explizit durch einen beliebigen Anmeldeinformationstyp ersetzen. Mit `DefaultAzureCredential` können Sie jedoch den gleichen Code verwenden, der sowohl in lokalen als auch in Azure-Umgebungen ausgeführt wird. Beispielsweise gewähren Sie Ihren eigenen Anmeldeinformationen Zugriff auf Ihren Schlüsseltresor. `DefaultAzureCredential` greift automatisch auf `SharedTokenCacheCredential` oder `VisualStudioCredential` zurück, wenn Sie Visual Studio für die lokale Entwicklung verwenden.

Alternativ können Sie die Umgebungsvariablen AZURE_TENANT_ID, AZURE_CLIENT_ID und AZURE_CLIENT_SECRET festlegen und `DefaultAzureCredential` wird den geheimen Clientschlüssel verwenden, den Sie über `EnvironmentCredential` verwenden, um sich bei Ihrem Schlüsseltresor zu authentifizieren. Nachdem Ihre App für einen Azure-Dienst mit aktivierter verwalteter Identität bereitgestellt wurde, wie z. B. dem Azure App Service, Azure Kubernetes Service oder der Azure Containerinstanz, gewähren Sie der verwalteten Identität des Azure-Diensts die Berechtigung für den Zugriff auf Ihren Schlüsseltresor. `DefaultAzureCredential` verwendet wird automatisch `ManagedIdentityCredential`, wenn Ihre App in Azure ausgeführt wird. Sie können dieselbe verwaltete Identität verwenden, um sich sowohl mit App Configuration als auch mit dem Key Vault authentifizieren. Weitere Informationen finden Sie unter [ das Verwenden verwalteter Identitäten für den Zugriff auf App Configuration](howto-integrate-azure-managed-service-identity.md).

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

    ```dotnetcli
    dotnet build
    ```

1. Verwenden Sie nach Abschluss des Buildvorgangs den folgenden Befehl, um die Web-App lokal auszuführen:

    ```dotnetcli
    dotnet run
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstart: Starten der lokalen App](./media/key-vault-reference-launch-local.png)



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Schlüssel in der App Configuration erstellt, mit dem auf einen im Key Vault gespeichertes Geheimnis verwiesen wird.
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Geheimnisse und Zertifikate automatisch aus Key Vault erneut laden:

> [!div class="nextstepaction"]
> [Geheimnisse und Zertifikate automatisch aus Key Vault erneut laden](./reload-key-vault-secrets-dotnet.md)

Informationen zur Verwendung der verwalteten Identität zum Optimieren des Zugriffs auf App Configuration und Key Vault finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
