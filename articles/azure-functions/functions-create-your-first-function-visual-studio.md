---
title: 'Schnellstart: Erstellen Ihrer ersten C#-Funktion in Azure mithilfe von Visual Studio'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Visual Studio verwenden, um eine durch C#-HTTP ausgelöste Funktion für Azure Functions zu erstellen und zu veröffentlichen, die unter .NET Core 3.1 ausgeführt wird.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 11/03/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 60e40b675ac8bc32328ea68786329b372f91d056
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028209"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>Schnellstart: Erstellen Ihrer ersten C#-Funktion in Azure mithilfe von Visual Studio

Mit Azure Functions können Sie Ihren C#-Code in einer serverlosen Umgebung in Azure ausführen. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Verwenden Sie Visual Studio, um ein C#-Klassenbibliotheksprojekt unter .NET 6.0 zu erstellen.
> * Erstellen Sie eine Funktion, die auf HTTP-Anforderungen reagiert. 
> * Führen Sie Ihren Code lokal aus, um das Funktionsverhalten zu überprüfen.
> * Stellen Sie Ihr Codeprojekt für Azure Functions bereit.
 
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-csharp.md) dieses Artikels.

## <a name="prerequisites"></a>Voraussetzungen

+ [Visual Studio 2022](https://azure.microsoft.com/downloads/) mit Unterstützung für .NET 6.0 Wählen Sie bei der Installation unbedingt die Workload **Azure-Entwicklung** aus. 

+ [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Wenn Sie noch nicht über ein Konto verfügen, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/dotnet/), bevor Sie beginnen.

## <a name="create-a-function-app-project"></a>Erstellen eines Funktionen-App-Projekts

Mit der Azure Functions-Projektvorlage in Visual Studio wird ein Bibliotheksprojekt der Klasse C# erstellt, das Sie in einer Funktions-App in Azure veröffentlichen können. Sie können mit einer Funktions-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen.

1. Wählen Sie im Visual Studio-Menü **Datei** > **Neu** > **Projekt** aus.

1. Geben Sie unter **Neues Projekt erstellen** den Suchbegriff *functions* in das Suchfeld ein, und wählen Sie die Vorlage **Azure Functions** und anschließend **Weiter** aus.

1. Geben Sie unter **Neues Projekt konfigurieren** im Feld **Projektname** einen Namen für Ihr Projekt ein, und wählen Sie anschließend **Erstellen** aus. Der Name der Funktions-App muss als C#-Namespace gültig sein, verwenden Sie daher keine Unterstriche, Bindestriche oder andere nicht alphanumerische Zeichen.

1. Verwenden Sie für die Einstellungen zum **Erstellen einer neuen Azure Functions-Anwendung** die Werte in der folgenden Tabelle:

    | Einstellung      | Wert  | Beschreibung                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET-Version** | **.NET 6** | Dieser Wert erstellt ein Funktionsprojekt, das prozessintern mit Version 4.x der Azure Functions-Runtime ausgeführt wird. Sie können auch **.NET 6 (isoliert)** auswählen, um ein Projekt zu erstellen, das in einem separaten Workerprozess ausgeführt wird. Azure Functions 1.x unterstützt .NET Framework. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](./functions-versions.md).   |
    | **Funktionsvorlage** | **HTTP-Trigger** | Mit diesem Wert wird eine Funktion erstellt, die durch eine HTTP-Anforderung ausgelöst wird. |
    | **Speicherkonto (AzureWebJobsStorage)**  | **Speicheremulator** | Da für eine Funktions-App in Azure ein Speicherkonto erforderlich ist, wird ein Speicherkonto zugewiesen oder erstellt, wenn Sie Ihr Projekt in Azure veröffentlichen. Von HTTP-Triggern wird keine Azure Storage-Kontoverbindungszeichenfolge verwendet. Für alle anderen Triggertypen ist dagegen eine gültige Azure Storage-Kontoverbindungszeichenfolge erforderlich. Wenn Sie diese Option auswählen, wird der Azurite-Emulator verwendet. |
    | **Autorisierungsstufe** | **Anonym** | Die erstellte Funktion kann von jedem Client ausgelöst werden, ohne dass ein Schlüssel angegeben werden muss. Diese Autorisierungseinstellung erleichtert Ihnen das Testen Ihrer neuen Funktion. Weitere Informationen zu Schlüsseln und zur Autorisierung finden Sie unter [Autorisierungsschlüssel](./functions-bindings-http-webhook-trigger.md#authorization-keys) sowie in der [Übersicht über HTTP-Trigger und -Bindungen in Azure Functions](./functions-bindings-http-webhook.md). |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Azure Functions-Projekteinstellungen":::

    Stellen Sie sicher, dass **Autorisierungsstufe** auf **Anonym** festgelegt ist. Bei Verwendung der Standardebene **Funktion** muss in Anforderungen für den Zugriff auf den Funktionsendpunkt der [Funktionsschlüssel](./functions-bindings-http-webhook-trigger.md#authorization-keys) angegeben werden.

1. Wählen Sie **Erstellen** aus, um das Funktionsprojekt und die HTTP-Triggerfunktion zu erstellen.

Visual Studio erstellt ein Projekt und eine Klasse mit Codebausteinen für den Funktionstyp „HTTP-Trigger“. Mit Code in den Codebausteinen wird eine HTTP-Antwort gesendet, die einen Wert aus dem Anforderungstext oder der Abfragezeichenfolge enthält. Mit dem Attribut `HttpTrigger` wird angegeben, dass die Funktion von einer HTTP-Anforderung ausgelöst wird.

## <a name="rename-the-function"></a>Umbenennen der Funktion

Das Methodenattribut `FunctionName` dient zum Festlegen des Namens der Funktion, der standardmäßig als `Function1` generiert wird. Da die Tools Sie den Standardfunktionsnamen nicht überschreiben lassen, wenn Sie Ihr Projekt erstellen, nehmen Sie sich eine Minute Zeit, um einen besseren Namen für die Funktionsklasse, Datei und die Metadaten zu erstellen.

1. Klicken Sie im **Datei-Explorer** mit der rechten Maustaste auf die Datei „Function1.cs“, und benennen Sie sie in `HttpExample.cs` um.

1. Benennen Sie die Klasse „Function1“ im Code in `HttpExample` um.

1. Benennen Sie in der `HttpTrigger`-Methode namens `Run` das Methodenattribut `FunctionName` in `HttpExample` um. 

Die Definition der Funktion sollte nun wie folgt aussehen:

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

Nachdem Sie die Funktion nun umbenannt haben, können Sie sie auf Ihrem lokalen Computer testen.

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Visual Studio wird in Azure Functions Core Tools integriert, sodass Sie Ihre Funktionen mithilfe der vollständigen Azure Functions-Runtime lokal testen können.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nachdem Sie sichergestellt haben, dass die Funktion auf Ihrem lokalen Computer richtig ausgeführt wird, können Sie das Projekt in Azure veröffentlichen.

## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

Sie müssen in Ihrem Azure-Abonnement über eine Funktions-App verfügen, um Ihr Projekt veröffentlichen zu können. Bei der Veröffentlichung in Visual Studio wird eine Funktions-App für Sie erstellt, wenn Sie das Projekt zum ersten Mal veröffentlichen.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Überprüfen Ihrer Funktion in Azure

1. In Cloud-Explorer sollte Ihre neue Funktions-App ausgewählt sein. Wenn nicht, erweitern Sie Ihr Abonnement > **App Services**, und wählen Sie Ihre neue Funktions-App aus.

1. Klicken Sie mit der rechten Maustaste auf die Funktions-App, und wählen Sie **In Browser öffnen** aus. Dadurch wird der Stamm ihrer Funktions-App in Ihrem Standardwebbrowser geöffnet und die Seite angezeigt, die anzeigt, dass ihre Funktions-App ausgeführt wird. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="Ausgeführte Funktions-App":::

1. Fügen Sie in der Adressleiste des Browsers die Zeichenfolge `/api/HttpExample?name=Functions` an die Basis-URL an, und führen Sie die Anforderung aus.

    Die URL, über die Ihre Funktion mit HTTP-Trigger aufgerufen wird, hat das folgende Format:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. Wechseln Sie zu dieser URL, und im Browser wird eine von der App auf die GET-Remoteanforderung zurückgegebene Antwort angezeigt, die wie im folgenden Beispiel aussieht:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Funktionsantwort im Browser":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstarts in dieser Sammlung bauen auf diesem Schnellstart auf. Überspringen Sie die Bereinigung der Ressourcen, falls Sie mit nachfolgenden Schnellstartanleitungen, Tutorials oder einem der Dienste, die Sie im Rahmen dieser Schnellstartanleitung erstellt haben, weiterarbeiten möchten.

*Ressourcen* bezieht sich bei Azure auf Funktions-Apps, Funktionen, Speicherkonten und Ähnliches. Sie werden in *Ressourcengruppen* zusammengefasst, und sämtliche Inhalte einer Gruppe können durch das Löschen der Gruppe gelöscht werden. 

Im Rahmen dieser Schnellstartanleitungen haben Sie Ressourcen erstellt. Für diese Ressourcen fallen je nach [Kontostatus](https://azure.microsoft.com/account/) und [Dienstpreisen](https://azure.microsoft.com/pricing/) unter Umständen Kosten an. 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe von Visual Studio eine C#-Funktions-App in Azure mit einer einfachen HTTP-Triggerfunktion erstellt und veröffentlicht. 

Im nächsten Artikel erfahren Sie, wie Sie Ihrer Funktion eine Azure Storage-Warteschlangenbindung zu hinzufügen:
> [!div class="nextstepaction"]
> [Hinzufügen einer Azure Storage-Warteschlangenbindung zu Ihrer Funktion](functions-add-output-binding-storage-queue-vs.md)

