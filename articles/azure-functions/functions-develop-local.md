---
title: Lokales Entwickeln und Ausführen von Azure Functions
description: Erfahren Sie, wie Sie Azure Functions auf dem lokalen Computer codieren und testen, bevor Sie sie in Azure Functions ausführen.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9a1c760eb2630ddf1c6175625b53b86db9cabc34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355084"
---
# <a name="code-and-test-azure-functions-locally"></a>Lokales Codieren und Testen von Azure Functions

Obwohl Azure Functions im [Azure portal] entwickelt und getestet werden kann, bevorzugen viele Entwickler jedoch dafür eine lokale Entwicklungsumgebung. Mit Functions können Sie einfach Ihren bevorzugten Code-Editor und Ihre bevorzugten Entwicklungstools zum Erstellen und Testen von Funktionen auf dem lokalen Computer verwenden. Die lokalen Funktionen können mit Live-Azure-Diensten verbunden werden, und Sie können sie unter Verwendung der vollständigen Functions-Runtime auf dem lokalen Computer debuggen.

Dieser Artikel enthält Links zu bestimmten Entwicklungsumgebungen für Ihre bevorzugte Sprache. Er bietet auch einige Anleitungen für lokale Entwicklung, z. B. für das Arbeiten mit der Datei [ local.settings.json](#local-settings-file). 

## <a name="local-development-environments"></a>Lokale Entwicklungsumgebungen

Die Art und Weise, wie Sie Funktionen auf dem lokalen Computer entwickeln, hängt von der verwendeten [Sprache](supported-languages.md) und den Tooleinstellungen ab. Die lokale Entwicklung wird in den in der folgenden Tabelle aufgeführten Umgebungen unterstützt:

|Environment                              |Languages         |BESCHREIBUNG|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (Klassenbibliothek)](functions-dotnet-class-library.md)<br/>[Isolierter C#-Prozess (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | Die [Azure Functions-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) erweitert die Functions-Unterstützung um VS Code. Erfordert Core Tools. Unterstützt die Entwicklung unter Linux, macOS und Windows bei Verwendung von Version 2.x der Core Tools. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion mit Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Eingabeaufforderung oder Terminal](functions-run-local.md) | [C# (Klassenbibliothek)](functions-dotnet-class-library.md)<br/>[Isolierter C#-Prozess (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools] umfasst die Core-Runtime und Vorlagen zum Erstellen von Funktionen, die die lokale Entwicklung ermöglichen. In Version 2.x wird die Entwicklung unter Linux, macOS und Windows unterstützt. Für die lokale Functions-Runtime basieren alle Umgebungen auf Core Tools. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (Klassenbibliothek)](functions-dotnet-class-library.md)<br/>[Isolierter C#-Prozess (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs) | Die Azure Functions-Tools sind in der **Azure-Entwicklungsworkload** in [Visual Studio 2019](https://www.visualstudio.com/vs/) und höheren Versionen enthalten. Hiermit können Sie Funktionen in einer Klassenbibliothek kompilieren und die DLL-Datei in Azure veröffentlichen. Enthält die Core Tools für lokale Tests. Weitere Informationen finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (verschiedene) | [Java](functions-reference-java.md) | Der Maven-Archetyp unterstützt die Core Tools, um die Entwicklung von Java-Funktionen zu ermöglichen. In Version 2.x wird die Entwicklung unter Linux, macOS und Windows unterstützt. Weitere Informationen finden Sie unter [Erstellen der ersten Funktion mit Java und Maven](./create-first-function-cli-java.md). Unterstützt auch die Entwicklung mit [Eclipse](functions-create-maven-eclipse.md) und [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

In jeder dieser lokalen Entwicklungsumgebungen können Sie Funktions-App-Projekte erstellen und vordefinierte Functions-Vorlagen zum Erstellen neuer Funktionen verwenden. In jeder wird Core Tools verwendet, sodass Sie Ihre Funktionen in der echten Functions-Runtime auf Ihrem eigenen Computer testen und debuggen können, so wie Sie dies für alle anderen Apps auch tun. Außerdem können Sie Ihr Funktions-App-Projekt in jeder dieser Umgebungen in Azure veröffentlichen.

## <a name="local-settings-file"></a>Datei für lokale Einstellungen

Die Datei „local.settings.json“ speichert App-Einstellungen und Einstellungen, die von lokalen Entwicklungstools verwendet werden. Einstellungen in der Datei „local.settings.json“ werden nur bei der lokalen Ausführung Ihrer Projekte verwendet. 

> [!IMPORTANT]  
> Da die Datei „local.settings.json“ möglicherweise Geheimnisse wie Verbindungszeichenfolgen enthält, sollten Sie sie niemals in einem Remoterepository speichern. Tools, die Functions unterstützen, bieten Möglichkeiten zum Synchronisieren von Einstellungen in der Datei „local.settings.json“ mit den [App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings) in der Funktions-App, in der Ihr Projekt bereitgestellt wird.

Die Datei mit den lokalen Einstellungen hat folgende Struktur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Diese Einstellungen werden bei der lokalen Ausführung von Projekten unterstützt:

| Einstellung      | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Wenn diese Einstellung auf `true` festgelegt wird, werden alle Werte mithilfe eines Schlüssels des lokalen Computers verschlüsselt. Wird mit `func settings`-Befehlen verwendet. Der Standardwert ist `false`. Es empfiehlt sich ggf., die Datei „local.settings.json“ auf dem lokalen Computer zu verschlüsseln, wenn sie Geheimnisse (etwa Dienstverbindungszeichenfolgen) enthält. Der Host entschlüsselt die Einstellungen bei der Ausführung automatisch. Verwenden Sie den Befehl `func settings decrypt`, bevor Sie versuchen, lokal verschlüsselte Einstellungen zu lesen. |
| **`Values`** | Eine Sammlung von Anwendungseinstellungen, die bei der lokalen Ausführung eines Projekts verwendet wird. Diese Schlüssel-Wert-Paare (Zeichenfolge-Zeichenfolge) entsprechen den Anwendungseinstellungen in Ihrer Funktions-App in Azure, etwa [`AzureWebJobsStorage`]. Viele Trigger und Bindungen verfügen über eine Eigenschaft, die auf eine App-Einstellung für die Verbindungszeichenfolge verweist, z. B. `Connection` für den [Blob Storage-Trigger](functions-bindings-storage-blob-trigger.md#configuration). Für diese Eigenschaften muss eine Anwendungseinstellung im Array `Values` definiert sein. Eine Liste der häufig verwendeten Einstellungen finden Sie in der nachfolgenden Tabelle. <br/>Werte müssen Zeichenfolgen und dürfen nicht JSON-Objekte oder Arrays sein. Einstellungsnamen dürfen weder einen Doppelpunkt (`:`) noch einen doppelten Unterstrich (`__`) enthalten. Doppelte Unterstriche sind von der Runtime reserviert, und der Doppelpunkt ist für die Unterstützung der [Abhängigkeitsinjektion](functions-dotnet-dependency-injection.md#working-with-options-and-settings) reserviert. |
| **`Host`** | Die Einstellungen in diesem Abschnitt passen den Hostprozess von Functions bei der lokalen Ausführung von Projekten an. Diese Einstellungen sind getrennt von den host.json-Einstellungen, die auch bei der Ausführung von Projekten in Azure angewendet werden. |
| **`LocalHttpPort`** | Legt den Standardport fest, der bei der Ausführung des lokalen Functions-Host verwendet wird (`func host start` und `func run`). Die Befehlszeilenoption `--port` hat Vorrang vor dieser Einstellung. Beispiel: Bei der Ausführung in der Visual Studio-IDE können Sie die Portnummer ändern, indem Sie zum Fenster „Projekteigenschaften“ > „Debuggen“ navigieren und die Portnummer im Befehl `host start --port <your-port-number>` angeben, der im Feld „Anwendungsargumente“ eingegeben werden kann. |
| **`CORS`** | Definiert die für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) zulässigen Ursprünge. Ursprünge werden als durch Trennzeichen getrennte Liste ohne Leerzeichen bereitgestellt. Den Platzhalterwert (\*) wird unterstützt, wodurch Anforderungen von einem beliebigen Ursprung zulässig sind. |
| **`CORSCredentials`** |  Wird `true` festgelegt, sind Anforderungen vom Typ `withCredentials` zulässig. |
| **`ConnectionStrings`** | Eine Auflistung. Verwenden Sie diese Sammlung nicht für die Verbindungszeichenfolgen, die von Ihren Funktionsbindungen verwendet werden. Diese Sammlung wird nur von Frameworks verwendet, die Verbindungszeichenfolgen üblicherweise aus dem Abschnitt `ConnectionStrings` einer Konfigurationsdatei abrufen, z. B. [Entity Framework](/ef/ef6/). Verbindungszeichenfolgen in diesem Objekt werden der Umgebung mit dem Anbietertyp [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) hinzugefügt. Elemente in dieser Sammlung werden nicht mit anderen App-Einstellungen in Azure veröffentlicht. Sie müssen diese Werte explizit zur Sammlung `Connection strings` in den Einstellungen Ihrer Funktions-App hinzufügen. Bei der Erstellung einer [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)-Klasse in Ihrem Funktionscode sollten Sie den Verbindungszeichenfolgenwert zusammen mit den anderen Verbindungen in den **Anwendungseinstellungen** im Portal speichern. |

Bei lokaler Ausführung können die folgenden Anwendungseinstellungen in das Array **`Values`** aufgenommen werden:

| Einstellung | Werte | BESCHREIBUNG |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Verbindungszeichenfolge für das Speicherkonto oder<br/>`UseDevelopmentStorage=true`| Enthält die Verbindungszeichenfolge für ein Azure-Speicherkonto. Erforderlich, wenn andere Trigger als HTTP verwendet werden. Weitere Informationen finden Sie in der Referenz zu [`AzureWebJobsStorage`].<br/>Wenn der [Azure-Speicheremulator](../storage/common/storage-use-emulator.md) lokal installiert ist und Sie [`AzureWebJobsStorage`] auf `UseDevelopmentStorage=true` festlegen, verwendet Core Tools den Emulator. Der Emulator ist während der Entwicklung hilfreich, doch sollten Sie vor der Bereitstellung einen Test mit einer tatsächlichen Speicherverbindung durchführen.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Wenn Sie bei lokaler Ausführung eine Funktion deaktivieren möchten, fügen Sie der Sammlung `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` hinzu, wobei `<FUNCTION_NAME>` der Name der Funktion ist. Weitere Informationen zum Deaktivieren von Funktionen in Azure Functions finden Sie [hier](disable-function.md#localsettingsjson). |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Gibt die Zielsprache der Functions-Runtime an. Erforderlich für Version 2.x und höher der Functions-Runtime. Diese Einstellung wird von Core Tools für Ihr Projekt generiert. Weitere Informationen finden Sie in der Referenz zu [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime).|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Gibt an, dass bei lokaler Ausführung PowerShell 7 verwendet wird. Wird kein Wert festgelegt, wird PowerShell Core 6 verwendet. Diese Einstellung wird nur bei lokaler Ausführung verwendet. Bei der Ausführung in Azure wird die PowerShell-Runtimeversion durch die Einstellung der `powerShellVersion`-Sitekonfiguration bestimmt, [die im Portal festgelegt werden kann](functions-reference-powershell.md#changing-the-powershell-version). | 

## <a name="next-steps"></a>Nächste Schritte

+ Weitere Informationen zur lokalen Entwicklung von kompilierten C#-Funktionen mithilfe von Visual Studio 2019 finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](functions-develop-vs.md).
+ Weitere Informationen zur lokalen Entwicklung von Funktionen mit VS Code auf einem Mac-, Linux- oder Windows-Computer finden Sie im Artikel Visual Studio Code - Erste Schritte für Ihre bevorzugte Sprache:
    + [C#-Klassenbibliothek](create-first-function-vs-code-csharp.md)
    + [Isolierter C#-Prozess (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ Weitere Informationen zum Entwickeln von Funktionen über die Eingabeaufforderung oder das Terminal finden Sie unter [Arbeiten mit Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
