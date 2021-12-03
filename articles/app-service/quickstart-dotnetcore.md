---
title: 'Schnellstart: Bereitstellen einer ASP.NET-Web-App'
description: Hier erfahren Sie, wie Sie Web-Apps in Azure App Service ausführen, indem Sie Ihre erste ASP.NET-App bereitstellen.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1, mode-portal
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 2d30b39d641c69580e11589616212fbc35ff5f5d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522724"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET 6.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET 6.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Schnellstart: Bereitstellen einer ASP.NET-Web-App

In dieser Schnellstartanleitung wird beschrieben, wie Sie Ihre erste ASP.NET-Web-App erstellen und für [Azure App Service](overview.md) bereitstellen. App Service unterstützt verschiedene Versionen von .NET-Apps und bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching. ASP.net Web-Apps sind plattformübergreifend und können unter Linux oder Windows gehostet werden. Am Ende verfügen Sie über eine Azure-Ressourcengruppe, die einen App Service-Hostingplan und eine App Service-Instanz mit einer bereitgestellten Webanwendung umfasst.

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Zum Erstellen von Apps auf der Windows-Hostingplattform wird Azure PowerShell empfohlen. Verwenden Sie zum Erstellen von Apps unter Linux ein anderes Tool, etwa die [Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli).

:::zone-end

## <a name="prerequisites"></a>Voraussetzungen

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2022</a> mit der Workload **ASP.NET und Webentwicklung**

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2022</a> mit der Workload **ASP.NET und Webentwicklung** (Stellen Sie sicher, dass das optionale Kontrollkästchen **.NET Framework project and item templates** (Projekt- und Elementvorlagen für .NET Framework) aktiviert ist.)

-----

Sie haben Visual Studio 2022 bereits installiert:

1. Installieren Sie in Visual Studio die neuesten Updates, indem Sie **Hilfe** > **Nach Updates suchen** auswählen.
1. Fügen Sie die Workload hinzu. Wählen Sie dazu **Tools** > **Tools und Features abrufen** aus.
    
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>
- Die Erweiterung für <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure-Tools</a>.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> Installieren Sie das aktuelle .NET 6.0 SDK.</a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank">Installieren Sie das .NET Framework 4.8 Developer Pack.</a>

> [!NOTE]
> Visual Studio Code ist plattformübergreifend, .NET Framework jedoch nicht. Wenn Sie .NET Framework-Apps mit Visual Studio Code entwickeln, empfiehlt es sich, einen Windows-Computer zu verwenden, um die Buildabhängigkeiten zu erfüllen.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- Die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>
- Das .NET SDK (einschließlich Runtime und CLI).

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> Installieren Sie das aktuelle .NET 6.0 SDK.</a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> Installieren Sie das neueste .NET 6.0 SDK </a> und <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> das .NET Framework 4.8 Developer Pack.</a>

> [!NOTE]
> Die [.NET-CLI](/dotnet/core/tools) und .NET 6.0 sind plattformübergreifend, .NET Framework jedoch nicht. Wenn Sie .NET Framework-Apps mit .NET CLI entwickeln, empfiehlt es sich, einen Windows-Computer zu verwenden, um die Buildabhängigkeiten zu erfüllen. .NET 6.0 ist plattformübergreifend.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet).
- <a href="/powershell/azure/install-az-ps" target="_blank">Azure PowerShell</a>
- Das .NET SDK (einschließlich Runtime und CLI).

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> Installieren Sie das aktuelle .NET 6.0 SDK.</a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> Installieren Sie das neueste .NET 6.0 SDK </a> und <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> das .NET Framework 4.8 Developer Pack.</a>

> [!NOTE]
> [Azure PowerShell](/powershell/azure/) und .NET 6.0 sind plattformübergreifend, .NET Framework jedoch nicht. Wenn Sie .NET Framework-Apps mit .NET CLI entwickeln, empfiehlt es sich, einen Windows-Computer zu verwenden, um die Buildabhängigkeiten zu erfüllen.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Erstellen einer ASP.NET-Web-App

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Suchen Sie unter **Neues Projekt erstellen** die Option **ASP.NET Core-Web-App**, und wählen Sie sie aus. Wählen Sie anschließend **Weiter** aus.
1. Geben Sie der Anwendung unter **Neues Projekt konfigurieren** den Namen _MyFirstAzureWebApp_, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="./media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio: Konfigurieren Sie die ASP.NET 6.0 Web-App." lightbox="media/quickstart-dotnet/configure-webapp-net.png" border="true":::

1. Wählen Sie **.NET Core 6.0 (langfristige Unterstützung)** aus.
1. Stellen Sie sicher, dass der **Authentifizierungstyp**  auf **Keine** festgelegt ist. Wählen Sie **Erstellen** aus.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net60.png" alt-text="Visual Studio: zusätzliche Informationen bei der Auswahl von .NET Core 6.0" lightbox="media/quickstart-dotnet/vs-additional-info-net60.png" border="true":::

1. Wählen Sie im Visual Studio-Menü **Debuggen** > **Starten ohne Debugging** aus, um die Web-App lokal auszuführen.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio: ASP.NET Core 6.0 wird lokal ausgeführt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Suchen Sie unter **Neues Projekt erstellen** die Option **ASP.NET-Webanwendung (.NET Framework)** , und wählen Sie sie aus. Wählen Sie anschließend **Weiter** aus.
1. Geben Sie der Anwendung unter **Neues Projekt konfigurieren** den Namen _MyFirstAzureWebApp_, und wählen Sie dann **Erstellen** aus.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio: Konfigurieren Sie die ASP.NET 4.8 Web-App." lightbox="media/quickstart-dotnet/configure-webapp-netframework48.png" border="true":::

1. Wählen Sie die Vorlage **MVC** aus.
1. Stellen Sie sicher, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Wählen Sie **Erstellen** aus.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio: Wählen Sie die MVC-Vorlage aus." lightbox="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" border="true":::

1. Wählen Sie im Visual Studio-Menü **Debuggen** > **Starten ohne Debugging** aus, um die Web-App lokal auszuführen.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio ASP.NET Framework 4.8 wird lokal ausgeführt." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Erstellen Sie im Terminalfenster einen neuen Ordner mit dem Namen _MyFirstAzureWebApp_, und öffnen Sie ihn in Visual Studio Code. 

    ```terminal
    mkdir MyFirstAzureWebApp
    code MyFirstAzureWebApp
    ```

1. Öffnen Sie in Visual Studio Code das <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminalfenster</a>, indem Sie `Ctrl` + `` ` `` eingeben.

1. Erstellen Sie im Terminal in Visual Studio Code mit dem Befehl [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) eine neue .NET Web-App.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    ```dotnetcli
    dotnet new webapp -f net6.0
    ```
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ```dotnetcli
    dotnet new webapp --target-framework-override net48
    ```
    
    > [!IMPORTANT]
    > Das `--target-framework-override`-Flag ist ein Freitextersatz für den Zielframeworkmoniker (TFM) für das Projekt und stellt *keine Garantie* dafür dar, dass die unterstützende Vorlage vorhanden ist oder kompiliert wird. Sie können .NET Framework Apps nur unter Windows erstellen und ausführen.
    
    ---

1. Führen Sie die Anwendung über das **Terminal** in Visual Studio Code mit dem Befehl [`dotnet run`](/dotnet/core/tools/dotnet-run) lokal aus.

    ```dotnetcli
    dotnet run --urls=https://localhost:5001/
    ```

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu der App auf `https://localhost:5001`.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    Die Vorlage für die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code: Lokales Ausführen von .NET 6.0 im Browser" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    Die Vorlage für die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code: Führen Sie .NET 4.8 lokal im Browser aus." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::
    
    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli,development-environment-ps"
<!-- markdownlint-enable MD044 -->

1. Öffnen Sie auf Ihrem Computer in einem Arbeitsverzeichnis ein Terminalfenster. Erstellen Sie mit dem Befehl [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) eine neue .NET-Web-App, und ändern Sie dann die Verzeichnisse in der neu erstellten App.

    <!-- Please keep the following commands in two lines instead of one && separated line. The latter doesn't work in PowerShell -->
    
    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    ```dotnetcli
    dotnet new webapp -n MyFirstAzureWebApp --framework net6.0
    cd MyFirstAzureWebApp
    ```
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ```dotnetcli
    dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48
    cd MyFirstAzureWebApp
    ```
    
    > [!IMPORTANT]
    > Das `--target-framework-override`-Flag ist ein Freitextersatz für den Zielframeworkmoniker (TFM) für das Projekt und stellt *keine Garantie* dafür dar, dass die unterstützende Vorlage vorhanden ist oder kompiliert wird. Sie können .NET Framework Apps nur unter Windows erstellen.
    
    ---

1. Führen Sie die Anwendung aus derselben Terminalsitzung mithilfe des Befehls [`dotnet run`](/dotnet/core/tools/dotnet-run) lokal aus.

    ```dotnetcli
    dotnet run --urls=https://localhost:5001/
    ```

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu der App auf `https://localhost:5001`.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    Die Vorlage für die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code: ASP.NET Core 6.0 im lokalen Browser" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    Die Vorlage für die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code: ASP.NET Framework 4.8 im lokalen Browser." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::
    
    ---

:::zone-end

## <a name="publish-your-web-app"></a>Veröffentlichen Ihrer Web-App

Zum Veröffentlichen Ihrer Web-App müssen Sie zuerst eine neue App Service-Instanz erstellen und konfigurieren, auf der Sie Ihre App veröffentlichen können.

Erstellen Sie bei der Einrichtung der App Service-Instanz Folgendes:

- Eine neue [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology), die alle Azure-Ressourcen für den Dienst enthalten soll.
- Einen neuen [Hostingplan](overview-hosting-plans.md), mit dem der Standort, die Größe und die Funktionen der Webserverfarm zum Hosten Ihrer App angegeben werden.

Führen Sie die folgenden Schritte aus, um Ihre App Service-Ressourcen zu erstellen und Ihr Projekt zu veröffentlichen:

:::zone target="docs" pivot="development-environment-vs"

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **MyFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus.
1. Wählen Sie unter **Veröffentlichen** die Option **Azure** aus, und klicken Sie dann auf **Weiter**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio: Veröffentlichen Sie die Web-App und zielen Sie dabei auf Azure." lightbox="media/quickstart-dotnet/vs-publish-target-Azure.png" border="true":::

1. Wählen Sie als **Spezifisches Ziel** entweder **Azure App Service (Linux)** oder **Azure App Service (Windows)** aus. Klicken Sie auf **Weiter**.

    > [!IMPORTANT]
    > Wenn Sie ASP.NET Framework 4.8 als Ziel auswählen, verwenden Sie **Azure App Service (Windows)** .

1. Ihre Optionen hängen davon ab, ob Sie bereits bei Azure angemeldet sind und ob Sie über ein Visual Studio-Konto verfügen, das mit einem Azure-Konto verknüpft ist. Wählen Sie entweder **Konto hinzufügen** oder **Anmelden** aus, um sich bei Ihrem Azure-Abonnement anzumelden. Wenn Sie bereits angemeldet sind, wählen Sie das gewünschte Konto aus.

    :::image type="content" source="media/quickstart-dotnet/sign-in-azure.png" border="true" alt-text="Visual Studio: Wählen Sie das Dialogfeld „Anmelden bei Azure“ aus." lightbox="media/quickstart-dotnet/sign-in-azure.png" :::

1. Klicken Sie rechts von **App Service-Instanzen** auf **+** .

    :::image type="content" source="media/quickstart-dotnet/publish-new-app-service.png" border="true" alt-text="Visual Studio: Dialogfeld „Neue App Service-App“." lightbox="media/quickstart-dotnet/publish-new-app-service.png" :::

1. Akzeptieren Sie unter **Abonnement** das angegebene Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus. Geben Sie unter **Name der neuen Ressourcengruppe** den Namen *myResourceGroup* ein, und wählen Sie **OK** aus.
1. Wählen Sie unter **Hostingplan** die Option **Neu** aus.
1. Geben Sie im Dialogfeld **Hostingplan: Neu erstellen** die Werte ein, die in der folgenden Tabelle angegeben sind:

    | Einstellung          | Vorgeschlagener Wert          | BESCHREIBUNG                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Hostingplan** | *MyFirstAzureWebAppPlan* | Name des App Service-Plans.                                         |
    | **Location**     | *Europa, Westen*            | Das Rechenzentrum, in dem die Web-App gehostet wird.                           |
    | **Größe**         | *Free*                   | Der [Tarif][app-service-pricing-tier] bestimmt die Hostingfeatures. |

    :::image type="content" source="media/quickstart-dotnet/create-new-hosting-plan.png" border="true" alt-text="Erstellen eines neuen Hostingplans" lightbox="media/quickstart-dotnet/create-new-hosting-plan.png" :::

1. Geben Sie unter **Name** einen eindeutigen App-Namen ein, der nur aus den folgenden zulässigen Zeichen besteht: `a-z`, `A-Z`, `0-9` und `-`. Sie können den automatisch generierten eindeutigen Namen übernehmen. Die URL der Web-App lautet `http://<app-name>.azurewebsites.net`, wobei `<app-name>` der Name Ihrer App ist.
1. Wählen Sie **Erstellen** aus, um die Azure-Ressourcen zu erstellen.

    :::image type="content" source="media/quickstart-dotnet/web-app-name.png" border="true" alt-text="Visual Studio: Dialogfeld zum Erstellen von App-Ressourcen." lightbox="media/quickstart-dotnet/web-app-name.png" :::

   Nach dem Abschluss des Assistenten werden die Azure-Ressourcen für Sie erstellt, und Sie können Ihr ASP.NET Core-Projekt veröffentlichen.

1. Vergewissern Sie sich im Dialogfeld **Veröffentlichen**, dass Ihre App Service-App unter **App Service-Instanz** ausgewählt ist, und wählen Sie dann **Fertig stellen** aus. Visual Studio erstellt ein Veröffentlichungsprofil für die ausgewählte App Service-App.
1. Wählen Sie auf der Seite **Veröffentlichen** die Option **Veröffentlichen** aus. Wenn eine Warnmeldung angezeigt wird, klicken Sie auf **Weiter**.

    Visual Studio erstellt, packt und veröffentlicht die App in Azure und startet sie anschließend im Standardbrowser.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    Die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio: ASP.NET Core 6.0-Web-App in Azure" :::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio: ASP.NET Framework 4.8-Web-App in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

<!-- :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - Sign in to Azure." border="true"::: -->

1. Öffnen Sie in Visual Studio Code die [**Befehlspalette**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) <kbd>STRG</kbd>++<kbd>UMSCHALT</kbd>++<kbd>P</kbd>.
1. Suchen Sie „Azure App Service: „In Web-App bereitstellen“ aus.
1. Gehen Sie bei den Aufforderungen wie folgt vor:

    1. Wählen Sie *MyFirstAzureWebApp* als Ordner für die Bereitstellung aus.
    1. Wählen Sie nach Aufforderung die Option **Konfiguration hinzufügen** aus.
    1. Melden Sie sich nach Aufforderung bei Ihrem Azure-Konto an.
    1. Wählen Sie Ihr **Abonnement** aus.
    1. Wählen Sie **Neue Web-App erstellen... Erweitert** aus.
    1. Verwenden Sie bei **Geben Sie einen global eindeutigen Namen ein** einen Namen, der in ganz Azure eindeutig ist (*zulässig sind die Zeichen `a-z`, `0-9` sowie `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
    1. Wählen Sie **Neue Ressourcengruppe erstellen** aus, und geben Sie einen Namen ein, z. B. `myResourceGroup`.
    1. Wenn Sie zur **Auswahl eines Laufzeitstapels** aufgefordert werden:
      - Wählen Sie für *.NET 6.0* die Option **.NET 6** aus
      - Wählen Sie für *.NET Framework 4.8* die Option **ASP.NET V4.8** aus
    1. Wählen Sie ein Betriebssystem (Windows oder Linux) aus.
        - Bei *.NET Framework 4.8* wird Windows implizit ausgewählt.
    1. Wählen Sie einen Ort in Ihrer Nähe aus.
    1. Klicken Sie auf **Einen neuen App Services-Plan erstellen**, geben Sie einen Namen an, und wählen Sie dann den [Tarif][app-service-pricing-tier] **F1 Free** aus.
    1. Wählen Sie für die Application Insights-Ressource die Option **Vorerst überspringen** aus.

1. Wählen Sie in dem Popup **Arbeitsbereich „MyFirstAzureWebApp“ immer in \<app-name> bereitstellen** die Option **Ja** aus. Auf diese Weise stellt Visual Studio Code, solange Sie sich im selben Arbeitsbereich befinden, jedes Mal in derselben App Service-App bereit.
1. Wenn die Veröffentlichung abgeschlossen ist, wählen Sie in der Benachrichtigung **Website durchsuchen** aus, und wählen Sie bei entsprechender Aufforderung **Öffnen**.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    Die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code: ASP.NET Core 6.0-Web-App in Azure":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code: ASP.NET Framework 4.8-Web-App in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

1. Melden Sie sich mithilfe des Befehls [`az login`](/cli/reference-index#az_login) bei Ihrem Azure-Konto an, und folgen Sie der Eingabeaufforderung:

    ```azurecli
    az login
    ```
    
1. Stellen Sie den Code mit dem Befehl [`az webapp up`](/cli/azure/webapp#az_webapp_up) in Ihrem lokalen Verzeichnis *MyFirstAzureWebApp* bereit:

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type <os>
    ```

    - Wenn der Befehl `az` nicht erkannt wird, stellen Sie sicher, dass Sie die Azure CLI gemäß der Beschreibung im Abschnitt [Voraussetzungen](#prerequisites) installiert haben.
    - Ersetzen Sie `<app-name>` durch einen Namen, der innerhalb von Azure eindeutig ist (*gültige Zeichen: `a-z`, `0-9` und `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
    - Mit dem Argument `--sku F1` wird die Web-App im [Tarif][app-service-pricing-tier] **Free** erstellt. Lassen Sie dieses Argument weg, um einen schnelleren Premium-Tarif zu verwenden. Dieser verursacht jedoch stündlich Kosten.
    - Ersetzen Sie `<os>` entweder durch `linux` oder durch `windows`. Sie müssen `windows` verwenden, wenn Sie *ASP.NET Framework 4.8* als Ziel verwenden.
    - Optional können Sie das Argument `--location <location-name>` einfügen, wobei `<location-name>` eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) ausführen.
    
    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Bei der Ausführung werden Meldungen zum Erstellen der Ressourcengruppe, des App Service-Plans und der Hosting-App und zur Konfiguration der Protokollierung angezeigt, und anschließend erfolgt die ZIP-Bereitstellung. Danach wird eine Meldung mit der URL der APP ausgegeben:
    
    ```azurecli
    You can launch the app at http://<app-name>.azurewebsites.net
    ```

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu der URL.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    Die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI: ASP.NET Core 6.0-Web-App in Azure":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    Die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI: ASP.NET Framework 4.8-Web-App in Azure.":::

    -----

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Zum Erstellen von Apps auf der Windows-Hostingplattform wird Azure PowerShell empfohlen. Verwenden Sie zum Erstellen von Apps unter Linux ein anderes Tool, etwa die [Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli).

1. Melden Sie sich mithilfe des Befehls [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto an, und folgen Sie der Eingabeaufforderung:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

<!-- ### [Deploy to Windows](#tab/windows) -->

2. Erstellen Sie mithilfe des Befehls [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) eine neue App.

    ```azurepowershell-interactive
    New-AzWebApp -Name <app-name> -Location westeurope
    ```

    - Ersetzen Sie `<app-name>` durch einen Namen, der innerhalb von Azure eindeutig ist (*gültige Zeichen: `a-z`, `0-9` und `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
    - Optional können Sie den Parameter `-Location <location-name>` einfügen, wobei `<location-name>` eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation) ausführen.

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Während der Ausführung werden eine Ressourcengruppe, ein App Service-Plan und die App Service-Ressource erstellt.

    <!-- ### [Deploy to Linux](#tab/linux)
    
    2. Create the Azure resources you need:
    
        ```azurepowershell-interactive
        New-AzResourceGroup -Name myResourceGroup -Location westeurope
        New-AzAppServicePlan -ResourceGroupName myResourceGroup -Name myAppServicePlan -Location westeurope -Linux
        New-AzWebApp -ResourceGroupName myResourceGroup -AppServicePlan myAppServicePlan -Name <app-name>
        Set-AzWebApp -
        ```
    
        - Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
        - You can optionally specify a different location in the `-Location` parameter. You can retrieve a list of allowable regions for your Azure account by running the [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation) command.
        - [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) creates a resource group to contain the resources.
        - [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) uses `-Linux` to create a Linux App Service plan, which hosts your app. The default pricing tier is `Free`, but you can change it with the `-Tier` parameter.
        - [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) creates the app itself.
    
    --- -->
    
1. Bereiten Sie im Stammordner der Anwendung die lokale Anwendung *MyFirstAzureWebApp* mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) auf die Bereitstellung vor:

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. Wechseln Sie zum Releaseverzeichnis, und erstellen Sie aus dem Inhalt eine ZIP-Datei:

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    ```powershell-interactive
    cd bin\Release\net6.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. Veröffentlichen Sie die ZIP-Datei mit dem Befehl [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) in der Azure-App:

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath` benötigt den vollständigen Pfad der ZIP-Datei.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu der URL.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    Die ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI: ASP.NET Core 6.0-Web-App in Azure":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    Die ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI: ASP.NET Framework 4.8-Web-App in Azure.":::

    -----

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Aktualisieren der App und erneutes Bereitstellen

Führen Sie die folgenden Schritte aus, um Ihre Web-App zu aktualisieren und erneut bereitzustellen:

:::zone target="docs" pivot="development-environment-vs"

1. Öffnen Sie im **Projektmappen-Explorer** unter Ihrem Projekt die Datei *Index.cshtml*.
1. Ersetzen Sie das erste `<div>`-Element durch den folgenden Code:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Speichern Sie die Änderungen.

1. Klicken Sie zur erneuten Bereitstellung in Azure im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **MyFirstAzureWebApp**, und wählen Sie **Veröffentlichen** aus.
1. Wählen Sie auf der Zusammenfassungsseite **Veröffentlichen** die Option **Veröffentlichen** aus.

    Nach Abschluss der Veröffentlichung wird in Visual Studio ein Browser mit der URL der Web-App gestartet.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    Die aktualisierte ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio: Aktualisierte ASP.NET Core 6.0-Web-App in Azure":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Die aktualisierte ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio: Aktualisierte ASP.NET Framework 4.8-Web-App in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Öffnen Sie *Index.cshtml*.
1. Ersetzen Sie das erste `<div>`-Element durch den folgenden Code:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Speichern Sie die Änderungen.

1. Öffnen Sie in Visual Studio Code die [**Befehlspalette**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) <kbd>STRG</kbd>++<kbd>UMSCHALT</kbd>++<kbd>P</kbd>.
1. Suchen Sie „Azure App Service: „In Web-App bereitstellen“ aus. Denken Sie daran, dass Sie Visual Studio Code in einem vorherigen Schritt angewiesen haben, die App zu speichern, in der Sie Ihren Arbeitsbereich bereitstellen möchten.
1. Wählen Sie **Bereitstellen**, wenn Sie dazu aufgefordert werden.
1. Wenn die Veröffentlichung abgeschlossen ist, wählen Sie in der Benachrichtigung **Website durchsuchen** aus, und wählen Sie bei entsprechender Aufforderung **Öffnen**.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    Die aktualisierte ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code: Aktualisierte ASP.NET Core 6.0-Web-App in Azure":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Die aktualisierte ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code: Aktualisierte ASP.NET Framework 4.8-Web-App in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Öffnen Sie im lokalen Verzeichnis die Datei *Index.cshtml*. Ersetzen Sie das erste `<div>`-Element:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Speichern Sie Ihre Änderungen, und stellen Sie die App dann mit dem Befehl `az webapp up` erneut bereit:

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

ASP.NET Core 6.0 ist plattformübergreifend. Ersetzen Sie basierend auf Ihrer vorherigen Bereitstellung `<os>` durch `linux` oder `windows`.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 weist Framework-Abhängigkeiten auf und muss unter Windows gehostet werden.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Wenn Sie Ihre .NET-Apps unter Linux hosten möchten, sollten Sie eine Migration von [ASP.NET Framework zu ASP.NET Core](/aspnet/core/migration/proper-to-2x)in Erwägung ziehen.

---

In diesem Befehl werden lokal zwischengespeicherte Werte aus der Datei *.azure/config* verwendet (einschließlich App-Name, Ressourcengruppe und App Service-Plan).

Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und wählen Sie die Option „Aktualisieren“ aus.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

Die aktualisierte ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI: Aktualisierte ASP.NET Core 6.0-Web-App in Azure":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Die aktualisierte ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI: Aktualisierte ASP.NET Framework 4.8-Web-App in Azure.":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

1. Öffnen Sie im lokalen Verzeichnis die Datei *Index.cshtml*. Ersetzen Sie das erste `<div>`-Element:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

1. Bereiten Sie im Stammordner der Anwendung die lokale Anwendung *MyFirstAzureWebApp* mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) auf die Bereitstellung vor:

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. Wechseln Sie zum Releaseverzeichnis, und erstellen Sie aus dem Inhalt eine ZIP-Datei:

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    ```powershell-interactive
    cd bin\Release\net6.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. Veröffentlichen Sie die ZIP-Datei mit dem Befehl [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) in der Azure-App:

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath` benötigt den vollständigen Pfad der ZIP-Datei.

1. Wechseln Sie nach Abschluss der Bereitstellung wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde, und wählen Sie die Option „Aktualisieren“ aus.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    Die aktualisierte ASP.NET Core 6.0-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI: Aktualisierte ASP.NET Core 6.0-Web-App in Azure":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    Die aktualisierte ASP.NET Framework 4.8-Web-App wird auf der Seite angezeigt.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI: Aktualisierte ASP.NET Framework 4.8-Web-App in Azure.":::
    
    ---
    
:::zone-end

## <a name="manage-the-azure-app"></a>Verwalten der Azure-App

Wechseln Sie zum Verwalten Ihrer Web-App zum [Azure-Portal](https://portal.azure.com), und suchen Sie nach **App Services**. Wählen Sie diese Option anschließend aus.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure-Portal: Wählen Sie die Option „Azure App Services“ aus.":::

Wählen Sie auf der Seite **App Services** den Namen Ihrer Web-App aus.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure-Portal: Seite „App Services“ mit einer ausgewählten Beispiel-Web-App.":::

Die Seite **Übersicht** für Ihre Web-App enthält Optionen für die grundlegende Verwaltung, z. B. Durchsuchen, Beenden, Starten, Neustarten und Löschen. Im linken Menü können Sie auf weitere Seiten für die Konfiguration Ihrer App zugreifen.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure-Portal: Übersichtsseite „Azure App Service“.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up PowerShell resources](../../includes/powershell-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine ASP.NET-Web-App auf Azure App Service bereitgestellt.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

Fahren Sie mit dem nächsten Artikel fort, um sich darüber zu informieren, wie Sie eine .NET Core-App erstellen und dafür eine Verbindung mit einer SQL-Datenbank herstellen:

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core-App mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurieren der ASP.NET Core-App](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Fahren Sie mit dem nächsten Artikel fort, um sich darüber zu informieren, wie Sie eine .NET Framework-App erstellen und dafür eine Verbindung mit einer SQL-Datenbank herstellen:

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET-App mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurieren einer ASP.NET Framework-App](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
