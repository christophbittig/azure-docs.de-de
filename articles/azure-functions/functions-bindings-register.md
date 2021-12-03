---
title: Registrieren von Bindungserweiterungen von Azure Functions
description: Lernen Sie, wie Sie eine Azure Functions-Bindungserweiterung abhängig von Ihrer Umgebung registrieren.
author: craigshoemaker
ms.topic: reference
ms.date: 09/14/2020
ms.author: cshoe
ms.openlocfilehash: b847243543eb8667a38d253e7eaad215af29e7b2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852333"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrieren von Bindungserweiterungen von Azure Functions

Ab Version 2.x von Azure Functions umfasst die Funktionsruntime standardmäßig nur HTTP- und Timertrigger. Andere [Trigger und Bindungen](./functions-triggers-bindings.md) sind als separate Pakete verfügbar.

Funktions-Apps mit .NET-Klassenbibliotheken verwenden Bindungen, die als NuGet-Pakete im Projekt installiert sind. Nicht auf .NET basierende Funktions-Apps können mithilfe von Erweiterungsbundles dieselben Bindungen nutzen, ohne mit der .NET-Infrastruktur interagieren zu müssen.

In der folgenden Tabelle ist angegeben, wann und wie Sie Bindungen registrieren.

| Entwicklungsumgebung |Registrierung<br/> in Functions 1.x  |Registrierung<br/> in Funktionen 2.x oder höher  |
|-------------------------|------------------------------------|------------------------------------|
|Azure-Portal|Automatic|Automatic<sup>*</sup>|
|Nicht-.NET-Sprachen|Automatic|Verwendung von [Erweiterungsbundles](#extension-bundles) (empfohlen) oder [explizite Installation von Erweiterungen](#explicitly-install-extensions)|
|C#-Klassenbibliothek mit Visual Studio 2019|[Verwendung von NuGet-Tools](#vs)|[Verwendung von NuGet-Tools](#vs)|
|C#-Klassenbibliothek mit Visual Studio Code|–|[Verwendung der .NET Core-CLI](#vs-code)|

<sup>*</sup> Das Portal verwendet Erweiterungsbündel.

## <a name="access-extensions-in-non-net-languages"></a>Zugreifen auf Erweiterungen in Nicht-.NET-Sprachen

Für Java, JavaScript, PowerShell, Python und benutzerdefinierte Handlerfunktions-Apps wird die Verwendung von Erweiterungsbundles empfohlen, um auf Bindungen zuzugreifen. Falls die Verwendung von Erweiterungsbundles nicht möglich ist, können Sie Bindungserweiterungen explizit installieren.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Erweiterungsbündel

Mit Erweiterungsbundles können Sie Ihrer Funktions-App kompatible Bindungserweiterungen hinzufügen. Erweiterungsbundles werden in der Datei *host.json* der App aktiviert.

Sie können Erweiterungsbündel mit Version 2.x und höheren Versionen der Functions-Runtime verwenden.

Erweiterungsbundles sind mit Versionsangaben versehen. Jede Version enthält einen bestimmten Satz an Bindungserweiterungen, deren gemeinsame Funktionalität überprüft wurde. Wählen Sie eine Bundleversion entsprechend den Erweiterungen aus, die Sie in Ihrer App benötigen.

Um Ihrer Funktions-App ein Erweiterungsbundle hinzuzufügen, fügen Sie den Abschnitt `extensionBundle` in die Datei *host.json* ein. In vielen Fällen erfolgt die Hinzufügung automatisch durch Visual Studio Code und Azure Functions Core Tools.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

In der folgenden Tabelle werden die derzeit verfügbaren Versionen des Standardbundles *Microsoft.Azure.Functions.ExtensionBundle* sowie Links zu den darin enthaltenen Erweiterungen aufgeführt.

| Bundleversion | Version in „host.json“ | Enthaltene Erweiterungen |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Siehe Datei [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json), mit der das Bundle generiert wurde |
| 2.x | `[2.*, 3.0.0)` | Siehe Datei [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json), mit der das Bundle generiert wurde |
| 3.x | `[3.3.0, 4.0.0)` | Siehe Datei [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v3.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json), mit der das Bundle generiert wurde |

> [!NOTE]
> Sie können zwar in der Datei „host.json“ einen benutzerdefinierten Versionsbereich angeben, es wird aber empfohlen, einen Versionswert aus dieser Tabelle zu verwenden.

### <a name="explicitly-install-extensions"></a>Explizites Installieren von Erweiterungen

Wenn Sie keine Erweiterungspakete verwenden können, können Sie Azure Functions Core Tools lokal nutzen, um die für Ihr Projekt erforderlichen Erweiterungspakete zu installieren.

> [!IMPORTANT]
> Erweiterungen können nicht explizit in einer Funktions-App installiert werden, die Erweiterungspakete verwendet. Entfernen Sie den Abschnitt `extensionBundle` in *host.json*, bevor Sie Erweiterungen explizit installieren.

Die folgenden Elemente beschreiben einige Gründe, aus denen Erweiterungen möglicherweise manuell installiert werden müssen:

* Sie müssen auf eine bestimmte Version einer Erweiterung zugreifen, die nicht in einem Paket verfügbar ist.
* Sie müssen auf eine benutzerdefinierte Erweiterung zugreifen, die nicht in einem Paket verfügbar ist.
* Sie müssen auf eine bestimmte Kombination von Erweiterungen zugreifen, die nicht in einem einzelnen Paket verfügbar ist.

> [!NOTE]
> Um Erweiterungen mithilfe von Core Tools manuell zu installieren, muss das [.NET Core 3.1. SDK](https://dotnet.microsoft.com/download) installiert sein. Das .NET Core SDK wird von Azure Functions Core Tools zum Installieren von Erweiterungen über NuGet verwendet. Für die Verwendung von Azure Functions-Erweiterungen müssen Sie nicht mit .NET vertraut sein.

Bei der expliziten Installation von Erweiterungen wird die .NET-Projektdatei „extensions.csproj“ zum Stamm Ihres Projekts hinzugefügt. Diese Datei definiert die von Ihren Funktionen benötigten NuGet-Pakete. Sie können zwar mit den [NuGet-Paketverweisen](/nuget/consume-packages/package-references-in-project-files) in dieser Datei arbeiten, mit Core Tools können Erweiterungen jedoch installiert werden, ohne die Datei manuell bearbeiten zu müssen.

Es gibt verschiedene Möglichkeiten, um Core Tools zum Installieren der erforderlichen Erweiterungen in Ihrem lokalen Projekt zu verwenden. 

#### <a name="install-all-extensions"></a>Installieren aller Erweiterungen 

Verwenden Sie den folgenden Befehl, um automatisch alle Erweiterungspakete hinzuzufügen, die von den Bindungen in Ihrem lokalen Projekt verwendet werden:

```command
func extensions install
```

Der Befehl liest die Datei *function.json*, um zu ermitteln, welche Pakete Sie benötigen, installiert diese und erzeugt das Erweiterungenprojekt (extensions.csproj) neu. Er fügt alle neuen Bindungen an die aktuelle Version hinzu, aktualisiert aber keine vorhandenen Bindungen. Verwenden Sie die Option `--force`, um vorhandene Bindungen beim Installieren neuer auf die neueste Version zu aktualisieren. Weitere Informationen finden Sie unter dem [`func extensions install`-Befehl](functions-core-tools-reference.md#func-extensions-install).

Verwendet Ihre Funktions-App Bindungen, die von Core Tools nicht erkannt werden, müssen Sie die jeweilige Erweiterung manuell installieren.

#### <a name="install-a-specific-extension"></a>Installieren einer bestimmten Erweiterung

Verwenden Sie den folgenden Befehl, um ein bestimmtes Erweiterungspaket mit einer bestimmten Version zu installieren (in diesem Fall die Speichererweiterung):

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 5.0.0
```

Weitere Informationen finden Sie unter dem [`func extensions install`-Befehl](functions-core-tools-reference.md#func-extensions-install).

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Installieren von Erweiterungen aus NuGet in .NET-Sprachen

Bei einem auf C#-Klassenbibliotheken basierenden Functions-Projekt sollten Sie Erweiterungen direkt installieren. Erweiterungsbündel wurden eigens für Projekte konzipiert, die nicht auf C#-Klassenbibliotheken basieren.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>C\#-Klassenbibliothek mit Visual Studio

In **Visual Studio** können Sie Pakete mithilfe des Befehls [Install-Package](/nuget/tools/ps-ref-install-package) aus der Paket-Manager-Konsole installieren, wie im folgenden Beispiel gezeigt wird:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Der Name des Pakets, das für eine bestimmte Bindung verwendet wird, ist im Referenzartikel für diese Bindung angegeben. Ein Beispiel finden Sie im [Referenzartikel zur Service Bus-Bindung im Abschnitt „Pakete“](functions-bindings-service-bus.md#functions-1x).

Ersetzen Sie `<TARGET_VERSION>` im Beispiel durch eine bestimmte Version des Pakets, z.B. `3.0.0-beta5`. Gültige Versionen sind auf den Seiten der einzelnen Pakete auf [NuGet.org](https://nuget.org) aufgeführt. Die Hauptversionen, die Functions Runtime 1.x oder 2.x entsprechen, sind im Referenzartikel für die Bindung angegeben.

Wenn Sie `Install-Package` verwenden, um auf eine Bindung zu verweisen, müssen Sie keine [Erweiterungsbündel](#extension-bundles) verwenden. Diese Vorgehensweise ist spezifisch für Klassenbibliotheken, die in Visual Studio erstellt werden.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C#-Klassenbibliothek mit Visual Studio Code

Installieren Sie in **Visual Studio Code** Pakete für eine C#-Klassenbibliothek über die Eingabeaufforderung. Verwenden Sie hierfür den Befehl [dotnet add package](/dotnet/core/tools/dotnet-add-package) in der .NET Core-CLI. Im folgenden Beispiel wird veranschaulicht, wie Sie eine Bindung hinzufügen:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Die .NET Core-CLI kann nur für die Entwicklung in Azure Functions 2.x verwendet werden.

Ersetzen Sie `<BINDING_TYPE_NAME>` durch den Namen des Pakets, das die benötigte Bindung enthält. Den gewünschten Bindungsreferenzartikel finden Sie in der [Liste der unterstützten Bindungen](./functions-triggers-bindings.md#supported-bindings).

Ersetzen Sie `<TARGET_VERSION>` im Beispiel durch eine bestimmte Version des Pakets, z.B. `3.0.0-beta5`. Gültige Versionen sind auf den Seiten der einzelnen Pakete auf [NuGet.org](https://nuget.org) aufgeführt. Die Hauptversionen, die Functions Runtime 1.x oder 2.x entsprechen, sind im Referenzartikel für die Bindung angegeben.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Trigger- und Bindungsbeispiel für Azure Functions](./functions-bindings-example.md)
