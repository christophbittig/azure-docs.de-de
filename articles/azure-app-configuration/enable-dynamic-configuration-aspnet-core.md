---
title: 'Tutorial: Verwenden der dynamischen Konfiguration von App Configuration in ASP.NET Core'
titleSuffix: Azure App Configuration
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für ASP.NET Core-Apps dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4e7ce4f07e49b7efc0f62ff668e72542fc3def77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302005"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer ASP.NET Core-App

ASP.NET Core verfügt über ein austauschbares Konfigurationssystem, das Konfigurationsdaten aus vielen verschiedenen Quellen lesen kann. Änderungen können dynamisch verarbeitet werden, ohne dass eine Anwendung neu gestartet werden muss. ASP.NET Core unterstützt die Bindung von Konfigurationseinstellungen an stark typisierte .NET-Klassen. Sie werden mithilfe von `IOptionsSnapshot<T>` in Ihren Code eingefügt. Dadurch wird die Konfiguration der Anwendung automatisch neu geladen, wenn sich die zugrunde liegenden Daten ändern.

In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code implementieren können. Dies baut auf der Web-App auf, die in den Schnellstartanleitungen vorgestellt wurde. Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md), bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Anwendung für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in einem App Configuration-Speicher
> * Einfügen der aktuellen Konfiguration in die Controller Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie für dieses Tutorial das [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Durchlaufen Sie zuerst die Schnellstartanleitung zum [Erstellen einer ASP.NET Core-App mit Azure App Configuration](./quickstart-aspnet-core-app.md), bevor Sie fortfahren.

## <a name="add-a-sentinel-key"></a>Hinzufügen eines Sentinel-Schlüssels

Ein *Sentinel-Schlüssel* ist ein spezieller Schlüssel, den Sie aktualisieren, nachdem Sie die Änderung aller anderen Schlüssel abgeschlossen haben. Der Sentinel-Schlüssel wird von Ihrer App überwacht. Wird eine Änderung erkannt, werden alle Konfigurationswerte von Ihrer Anwendung aktualisiert. Dieser Ansatz trägt dazu bei, die Konsistenz der Konfiguration in Ihrer Anwendung sicherzustellen, und verringert die Gesamtanzahl von Anforderungen, die an App Configuration gesendet werden (verglichen mit der Überwachung aller Schlüssel auf Änderungen).

1. Wählen Sie im Azure-Portal die Option **Konfigurations-Explorer > Erstellen > Schlüssel-Wert** aus.
1. Geben Sie unter **Schlüssel** die Zeichenfolge *TestApp:Settings:Sentinel* ein. Geben Sie unter **Wert** den Wert „1“ ein. Lassen Sie **Bezeichnung** und **Inhaltstyp** leer.
1. Wählen Sie **Übernehmen**.

> [!NOTE]
> Wenn Sie keinen Sentinel-Schlüssel verwenden, müssen Sie jeden zu überwachenden Schlüssel manuell registrieren.

## <a name="reload-data-from-app-configuration"></a>Erneutes Laden von Daten aus App Configuration

1. Fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.Azure.AppConfiguration.AspNetCore` hinzu, indem Sie den folgenden Befehl ausführen:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `CreateWebHostBuilder`-Methode, um die `config.AddAzureAppConfiguration()`-Methode hinzuzufügen.

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

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
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
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
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
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
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    In der Methode `ConfigureRefresh` werden Schlüssel innerhalb Ihres App Configuration-Speichers registriert, die Sie auf Änderungen überwachen möchten. Mit dem Parameter `refreshAll` der Methode `Register` wird angegeben, dass alle Konfigurationswerte aktualisiert werden sollen, wenn sich der registrierte Schlüssel ändert. Durch die Methode `SetCacheExpiration` wird angegeben, wie viel Zeit mindestens verstreichen muss, bevor eine neue Anforderung an App Configuration gesendet wird, um nach Konfigurationsänderungen zu suchen. In diesem Beispiel wird die Standardablaufzeit von 30 Sekunden in fünf Minuten geändert. Dadurch wird die Anzahl potenzieller Anforderungen verringert, die an Ihren App Configuration-Speicher gesendet werden.

    > [!NOTE]
    > Zu Testzwecken können Sie die Ablaufzeit für die Cacheaktualisierung verringern.

    Eine tatsächliche Konfigurationsaktualisierung kann mithilfe der App Configuration-Middleware ausgelöst werden. Dies wird in einem späteren Schritt beschrieben.

1. Fügen Sie im Verzeichnis „Controllers“ die Datei *Settings.cs* hinzu, die eine neue `Settings`-Klasse definiert und implementiert. Ersetzen Sie den Namespace durch den Namen Ihres Projekts. 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

1. Öffnen Sie *Startup.cs*, und aktualisieren Sie die Methode `ConfigureServices`. Rufen Sie `Configure<Settings>` auf, um Konfigurationsdaten an die Klasse `Settings` zu binden. Rufen Sie `AddAzureAppConfiguration` auf, um App Configuration-Komponenten der Dienstsammlung Ihrer Anwendung hinzuzufügen.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---

1. Aktualisieren Sie die Methode `Configure`, und fügen Sie einen Aufruf von `UseAzureAppConfiguration` hinzu. Dadurch kann Ihre Anwendung die Konfigurationsupdates automatisch mithilfe der App Configuration-Middleware verarbeiten.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    > [!NOTE]
    > Die App Configuration-Middleware überwacht den Sentinel-Schlüssel oder alle anderen Schlüssel, die Sie im vorherigen Schritt im Aufruf von `ConfigureRefresh` für die Aktualisierung registriert haben. Die Middleware wird bei jeder eingehenden Anforderung Ihrer Anwendung ausgelöst. Von der Middleware werden jedoch erst dann Anforderungen zum Überprüfen des Werts in App Configuration gesendet, wenn die von Ihnen festgelegte Cacheablaufzeit verstrichen ist. Wird eine Änderung erkannt, wird entweder die gesamte Konfiguration aktualisiert (bei Verwendung des Sentinel-Schlüssels), oder es werden nur die Werte der registrierten Schlüssel aktualisiert.
    > - Tritt bei einer an App Configuration gesendeten Änderungserkennungsanforderung ein Fehler auf, wird von Ihrer Anwendung weiterhin die zwischengespeicherte Konfiguration verwendet. Eine weitere Überprüfung erfolgt, wenn die konfigurierte Cacheablaufzeit erneut verstrichen ist und neue eingehende Anforderungen für Ihre Anwendung vorliegen.
    > - Die Konfigurationsaktualisierung wird asynchron zur Verarbeitung der eingehenden Anforderungen Ihrer Anwendung durchgeführt. Die eingehende Anforderung, durch die die Aktualisierung ausgelöst wurde, wird nicht blockiert oder verlangsamt. Die Anforderung, durch die die Aktualisierung ausgelöst wurde, erhält möglicherweise nicht die aktualisierten Konfigurationswerte; nachfolgende Anforderungen dagegen schon.
    > - Um sicherzustellen, dass die Middleware ausgelöst wird, empfiehlt es sich, `app.UseAzureAppConfiguration()` frühestmöglich in Ihrer Anforderungspipeline aufzurufen, damit sie nicht durch eine andere Middleware in Ihrer Anwendung beeinträchtigt wird.

## <a name="use-the-latest-configuration-data"></a>Verwenden der aktuellsten Konfigurationsdaten

1. Öffnen Sie *HomeController.cs* im Verzeichnis „Controllers“, und fügen Sie einen Verweis auf das Paket `Microsoft.Extensions.Options` hinzu.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualisieren Sie die `HomeController`-Klasse, um `Settings` per Abhängigkeitsinjektion zu erhalten, und verwenden Sie die darin enthaltenen Werte.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```
    ---
    > [!Tip]
    > Weitere Informationen zu den Optionsmustern beim Lesen von Konfigurationswerten finden Sie unter [Optionsmuster in ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

3. Öffnen Sie im Verzeichnis „Views > Home“ die Datei *Index.cshtml*, und ersetzen Sie den Inhalt durch das folgende Skript:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

    ```console
        dotnet build
    ```

1. Führen Sie nach erfolgreicher Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

    ```console
        dotnet run
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu der URL, die in der `dotnet run`-Ausgabe angezeigt wird.

    ![Lokales Starten der Schnellstart-App](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Klicken Sie auf **Alle Ressourcen**, und wählen Sie dann die Instanz des App Configuration-Speichers aus, die Sie in der Schnellstartanleitung erstellt haben.

1. Wählen Sie **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel. Denken Sie daran, den Sentinel-Schlüssel zuletzt zu aktualisieren.

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Daten aus Azure App Configuration – jetzt mit Liveupdates! |
    | TestApp:Settings:Sentinel | 2 |

1. Aktualisieren Sie die Browserseite, um die neuen Konfigurationseinstellungen anzuzeigen. Möglicherweise müssen Sie sie mehrmals aktualisieren, damit die Änderungen widergespiegelt werden. Alternativ können Sie die Rate für die Cacheablaufzeit auf weniger als fünf Minuten festlegen. 

    ![Lokales Starten der aktualisierten Schnellstart-App](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre ASP.NET Core-Web-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
