---
title: 'Erstellen einer C#-Funktion mit Visual Studio Code: Azure Functions'
description: Erfahren Sie, wie Sie eine C#-Funktion erstellen und dann das lokale Projekt für serverloses Hosting in Azure Functions unter Verwendung der Azure Functions-Erweiterung in Visual Studio Code veröffentlichen.
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
ms.openlocfilehash: 61ff0df6a69835862491f05c7e568cd1c6da141d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027905"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Schnellstart: Erstellen einer C#-Funktion in Azure mit Visual Studio Code

In diesem Artikel wird mithilfe von Visual Studio Code eine C#-Funktion erstellt, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]    

In diesem Artikel wird eine durch HTTP ausgelöste Funktion erstellt, die unter .NET 6.0 ausgeführt wird. Es gibt auch eine [CLI-basierte Version](create-first-function-cli-csharp.md) dieses Artikels.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

Vergewissern Sie sich zunähst, dass Folgendes vorhanden ist:

+ [.NET 6.0 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), Version 4.x.

+ [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code.  

+ [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code

Sie benötigen außerdem ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Erstellen Ihres lokalen Projekts

In diesem Abschnitt wird mithilfe von Visual Studio Code ein lokales Azure Functions-Projekt in C# erstellt. Weiter unten in diesem Artikel wird der Funktionscode in Azure veröffentlicht.

1. Wählen Sie auf der Aktivitätsleiste das Azure-Symbol und anschließend im Bereich **Azure: Funktionen** das Symbol **Neues Projekt erstellen** aus.

    ![Auswählen von „Neues Projekt erstellen“](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wählen Sie einen Verzeichnisspeicherort für Ihren Projektarbeitsbereich und anschließend **Auswählen** aus.

    > [!NOTE]
    > Diese Schritte sollten außerhalb eines Arbeitsbereichs ausgeführt werden. Wählen Sie in diesem Fall keinen Projektordner aus, der Teil eines Arbeitsbereichs ist.

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    # <a name="in-process"></a>[In-Process](#tab/in-process) 

    |Prompt|Auswahl|
    |--|--|
    |**Auswählen einer Sprache für Ihr Funktionsprojekt**|Klicken Sie auf die Option `C#`.|
    | **Auswählen einer .NET-Laufzeit** | Klicken Sie auf die Option `.NET 6`.|
    |**Auswählen einer Vorlage für die erste Funktion Ihres Projekts**|Klicken Sie auf die Option `HTTP trigger`.|
    |**Angeben eines Funktionsnamens**|Geben Sie `HttpExample`ein.|
    |**Angeben eines Namespaces** | Geben Sie `My.Functions`ein. |
    |**Autorisierungsstufe**|Wählen Sie `Anonymous` aus, damit Ihr Funktionsendpunkt von jedem Benutzer aufgerufen werden kann. Weitere Informationen zur Autorisierungsstufe finden Sie unter [Autorisierungsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Auswählen, wie Sie Ihr Projekt öffnen möchten**|Klicken Sie auf die Option `Add to workspace`.|

    # <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

    |Prompt|Auswahl|
    |--|--|
    |**Auswählen einer Sprache für Ihr Funktionsprojekt**|Klicken Sie auf die Option `C#`.|
    | **Auswählen einer .NET-Laufzeit** | Klicken Sie auf die Option `.NET 6 Isolated`.|
    |**Auswählen einer Vorlage für die erste Funktion Ihres Projekts**|Klicken Sie auf die Option `HTTP trigger`.|
    |**Angeben eines Funktionsnamens**|Geben Sie `HttpExample`ein.|
    |**Angeben eines Namespaces** | Geben Sie `My.Functions`ein. |
    |**Autorisierungsstufe**|Wählen Sie `Anonymous` aus, damit Ihr Funktionsendpunkt von jedem Benutzer aufgerufen werden kann. Weitere Informationen zur Autorisierungsstufe finden Sie unter [Autorisierungsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Auswählen, wie Sie Ihr Projekt öffnen möchten**|Klicken Sie auf die Option `Add to workspace`.|

    ---
    
    > [!NOTE]
    > Wird .NET 6 nicht als Laufzeitoption angezeigt, überprüfen Sie Folgendes:
    > 
    > + Stellen Sie sicher, dass Sie das .NET 6.0 SDK installiert haben.
    > + Drücken Sie F1, geben Sie `Preferences: Open user settings` ein, suchen Sie nach `Azure Functions: Project Runtime`, und ändern Sie die Standardruntimeversion in `~4`.  
    
1. Auf der Grundlage dieser Informationen generiert Visual Studio Code ein Azure Functions-Projekt mit einem HTTP-Trigger. Die lokalen Projektdateien können im Explorer angezeigt werden. Weitere Informationen zu den erstellten Dateien finden Sie unter [Generierte Projektdateien](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code-csharp](../../includes/functions-run-function-test-local-vs-code-csharp.md)]

Nachdem Sie sich vergewissert haben, dass die Funktion auf Ihrem lokalen Computer korrekt ausgeführt wird, können Sie das Projekt mithilfe von Visual Studio Code direkt in Azure veröffentlichen.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) genutzt, um eine Funktions-App mit einer einfachen Funktion zu erstellen, die über HTTP ausgelöst wird. Im nächsten Artikel erweitern Sie diese Funktion, indem Sie eine Verbindung mit Azure Cosmos DB oder Azure Queue Storage herstellen. Weitere Informationen zum Herstellen einer Verbindung mit anderen Azure-Diensten finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="in-process"></a>[In-Process](#tab/in-process) 

> [!div class="nextstepaction"]
> [Mit Azure Cosmos DB verbinden](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [Mit Azure Queue Storage verbinden](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Mit Azure Cosmos DB verbinden](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)
> [Mit Azure Queue Storage verbinden](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)

---

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
