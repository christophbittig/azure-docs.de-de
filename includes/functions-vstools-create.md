---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/18/2021
ms.author: glenga
ms.openlocfilehash: b7eabb399e27aa72911f94309227963522076660
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830019"
---
Mit der Azure Functions-Projektvorlage in Visual Studio wird ein Bibliotheksprojekt der Klasse C# erstellt, das Sie in einer Funktions-App in Azure veröffentlichen können. Sie können mit einer Funktions-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen.

1. Wählen Sie im Visual Studio-Menü **Datei** > **Neu** > **Projekt** aus.

1. Geben Sie unter **Neues Projekt erstellen** den Suchbegriff *functions* in das Suchfeld ein, und wählen Sie die Vorlage **Azure Functions** und anschließend **Weiter** aus.

1. Geben Sie unter **Neues Projekt konfigurieren** im Feld **Projektname** einen Namen für Ihr Projekt ein, und wählen Sie anschließend **Erstellen** aus. Der Name der Funktions-App muss als C#-Namespace gültig sein, verwenden Sie daher keine Unterstriche, Bindestriche oder andere nicht alphanumerische Zeichen.

1. Verwenden Sie für die Einstellungen zum **Erstellen einer neuen Azure Functions-Anwendung** die Werte in der folgenden Tabelle:

    # <a name="in-process"></a>[In-Process](#tab/in-process) 

    | Einstellung      | Wert  | Beschreibung                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET-Version** | **.NET Core 3 (LTS)** | Dieser Wert erstellt ein Funktionsprojekt, das prozessintern mit Version 3.x der Azure Functions-Runtime ausgeführt wird. Azure Functions 1.x unterstützt .NET Framework. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](../articles/azure-functions/functions-versions.md).   |
    | **Funktionsvorlage** | **HTTP-Trigger** | Mit diesem Wert wird eine Funktion erstellt, die durch eine HTTP-Anforderung ausgelöst wird. |
    | **Speicherkonto (AzureWebJobsStorage)**  | **Speicheremulator** | Da für eine Funktions-App in Azure ein Speicherkonto erforderlich ist, wird ein Speicherkonto zugewiesen oder erstellt, wenn Sie Ihr Projekt in Azure veröffentlichen. Von HTTP-Triggern wird keine Azure Storage-Kontoverbindungszeichenfolge verwendet. Für alle anderen Triggertypen ist dagegen eine gültige Azure Storage-Kontoverbindungszeichenfolge erforderlich.  |
    | **Autorisierungsstufe** | **Anonym** | Die erstellte Funktion kann von jedem Client ausgelöst werden, ohne dass ein Schlüssel angegeben werden muss. Diese Autorisierungseinstellung erleichtert Ihnen das Testen Ihrer neuen Funktion. Weitere Informationen zu Schlüsseln und zur Autorisierung finden Sie unter [Autorisierungsschlüssel](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) sowie in der [Übersicht über HTTP-Trigger und -Bindungen in Azure Functions](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Functions-Projekteinstellungen](./media/functions-vs-tools-create/functions-project-settings.png)

    # <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

    | Einstellung      | Wert  | Beschreibung                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET-Version** | **.NET 5 (isoliert)** | Dieser Wert erstellt ein Funktionsprojekt, das in einem isolierten Prozess ausgeführt wird und .NET 5.0 unterstützt. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](../articles/azure-functions/functions-versions.md).   |
    | **Funktionsvorlage** | **HTTP-Trigger** | Mit diesem Wert wird eine Funktion erstellt, die durch eine HTTP-Anforderung ausgelöst wird. |
    | **Speicherkonto (AzureWebJobsStorage)**  | **Speicheremulator** | Da für eine Funktions-App in Azure ein Speicherkonto erforderlich ist, wird ein Speicherkonto zugewiesen oder erstellt, wenn Sie Ihr Projekt in Azure veröffentlichen. Von HTTP-Triggern wird keine Azure Storage-Kontoverbindungszeichenfolge verwendet. Für alle anderen Triggertypen ist dagegen eine gültige Azure Storage-Kontoverbindungszeichenfolge erforderlich.  |
    | **Autorisierungsstufe** | **Anonym** | Die erstellte Funktion kann von jedem Client ausgelöst werden, ohne dass ein Schlüssel angegeben werden muss. Diese Autorisierungseinstellung erleichtert Ihnen das Testen Ihrer neuen Funktion. Weitere Informationen zu Schlüsseln und zur Autorisierung finden Sie unter [Autorisierungsschlüssel](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) sowie in der [Übersicht über HTTP-Trigger und -Bindungen in Azure Functions](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Functions-Projekteinstellungen](./media/functions-vs-tools-create/functions-project-settings-isolated.png)

    ---

    Stellen Sie sicher, dass **Autorisierungsstufe** auf **Anonym** festgelegt ist. Bei Verwendung der Standardebene **Funktion** muss in Anforderungen für den Zugriff auf den Funktionsendpunkt der [Funktionsschlüssel](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) angegeben werden.

1. Wählen Sie **Erstellen** aus, um das Funktionsprojekt und die HTTP-Triggerfunktion zu erstellen.
