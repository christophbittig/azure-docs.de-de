---
title: Erstellen Ihrer ersten dauerhaften Funktion in Azure Functions mithilfe von PowerShell
description: Hier erfahren Sie, wie Sie eine dauerhafte Azure-Funktion in PowerShell mithilfe von Visual Studio Code erstellen und veröffentlichen.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: acd1c61a145067d66f7c1921da108a794bd1faee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752294"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Erstellen Ihrer ersten dauerhaften Funktion in PowerShell

*Durable Functions* ist eine Erweiterung von [Azure Functions](../functions-overview.md), mit der Sie zustandsbehaftete Funktionen in einer serverlosen Umgebung schreiben können. Die Erweiterung verwaltet Status, Prüfpunkte und Neustarts für Sie.

In diesem Artikel erfahren Sie, wie Sie die Azure Functions-Erweiterung von Visual Studio Code verwenden, um lokal eine dauerhafte Funktion namens „hello world“ zu erstellen und zu testen.  Mit dieser Funktion werden Aufrufe anderer Funktionen orchestriert und miteinander verkettet. Anschließend veröffentlichen Sie den Funktionscode in Azure.

![Ausführen einer dauerhaften Funktion in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Installieren Sie [Visual Studio Code](https://code.visualstudio.com/download).

* Installieren Sie die VS Code-Erweiterung [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

* Stellen Sie sicher, dass Sie über die neueste Version der [Azure Functions Core Tools](../functions-run-local.md) verfügen.

* Für Durable Functions ist ein Azure-Speicherkonto erforderlich. Sie benötigen ein Azure-Abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Erstellen Ihres lokalen Projekts 

In diesem Abschnitt wird mithilfe von Visual Studio Code ein lokales Azure Functions-Projekt erstellt. 

1. Drücken Sie in Visual Studio Code F1 (oder STRG+UMSCHALT+P bzw. BEFEHL+UMSCHALT-P), um die Befehlspalette zu öffnen. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Create New Project...`, und wählen Sie ihn aus.

    ![Erstellen einer Funktion](media/quickstart-js-vscode/functions-create-project.png)

1. Wählen Sie einen leeren Ordner für Ihr Projekt und anschließend **Auswählen** aus.

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen ein:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | Auswählen einer Sprache für Ihr Funktions-App-Projekt | PowerShell | Erstellen Sie ein lokales PowerShell-Functions-Projekt. |
    | „Select a version“ (Wählen Sie eine Version aus.) | Azure Functions v3 | Diese Option wird nur angezeigt, wenn die Core Tools noch nicht installiert sind. In diesem Fall werden die Core Tools beim erstmaligen Ausführen der App installiert. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | Vorerst überspringen | |
    | Auswählen, wie Sie Ihr Projekt öffnen möchten | Im aktuellen Fenster | Öffnet VS Code wieder im ausgewählten Ordner. |

Von Visual Studio Code werden bei Bedarf die Azure Functions Core Tools installiert. Außerdem wird ein Funktions-App-Projekt in einem Ordner erstellt. Dieses Projekt enthält die Konfigurationsdateien [host.json](../functions-host-json.md) und [local.settings.json](../functions-develop-local.md#local-settings-file).

Und es wird die Datei „package.json“ im Stammordner erstellt.

### <a name="configure-function-app-to-use-powershell-7"></a>Konfigurieren der Funktions-App für die Verwendung von PowerShell 7

Öffnen Sie die Datei *local.settings.json*, und überprüfen Sie, ob die Einstellung `FUNCTIONS_WORKER_RUNTIME_VERSION` auf `~7` festgelegt ist. Wenn sie fehlt oder auf einen anderen Wert festgelegt ist, aktualisieren Sie den Inhalt der Datei.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Erstellen Ihrer Funktionen

Die einfachste Durable Functions-App enthält drei Funktionen:

* *Orchestratorfunktion:* Beschreibt einen Workflow, der andere Funktionen orchestriert.
* *Aktivitätsfunktion:* Wird von der Orchestratorfunktion aufgerufen, führt die Aufgabe aus und gibt optional einen Wert zurück.
* *Clientfunktion:* Eine reguläre Azure-Funktion, die eine Orchestratorfunktion startet. In diesem Beispiel wird eine per HTTP ausgelöste Funktion verwendet.

### <a name="orchestrator-function"></a>Orchestratorfunktion

Sie verwenden eine Vorlage, um den Code Ihrer dauerhaften Funktion in Ihrem Projekt zu erstellen.

1. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Create Function...`, und wählen Sie ihn aus.

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen ein:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | „Select a template for your function“ (Wählen Sie eine Vorlage für Ihre Funktion aus.) | Orchestrator für Durable Functions | Erstellen Sie eine Orchestrierung für Durable Functions. |
    | Angeben eines Funktionsnamens | HelloOrchestrator | Der Name Ihrer dauerhaften Funktion. |

Sie haben einen Orchestrator für die Koordinierung von Aktivitätsfunktionen hinzugefügt. Öffnen Sie *HelloOrchestrator/run.ps1*, um die Orchestratorfunktion anzuzeigen. Mit jedem Aufruf des Cmdlets `Invoke-ActivityFunction` wird eine Aktivitätsfunktion namens `Hello` aufgerufen.

Fügen Sie als Nächstes die referenzierte Aktivitätsfunktion `Hello` hinzu.

### <a name="activity-function"></a>Aktivitätsfunktion

1. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Create Function...`, und wählen Sie ihn aus.

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen ein:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | „Select a template for your function“ (Wählen Sie eine Vorlage für Ihre Funktion aus.) | Aktivität für Durable Functions | Erstellen Sie eine Aktivitätsfunktion. |
    | Angeben eines Funktionsnamens | Hallo | Der Name Ihrer Aktivitätsfunktion. |

Sie haben die Aktivitätsfunktion `Hello` hinzugefügt, die vom Orchestrator aufgerufen wird. Öffnen Sie *Hello/run.ps1*, um zu überprüfen, ob sie einen Namen als Eingabe akzeptiert und eine Begrüßung zurückgibt. Eine Aktivitätsfunktion dient zum Ausführen von Aktionen wie Datenbankaufrufen oder Berechnungen.

Abschließend fügen Sie noch eine per HTTP ausgelöste Funktion hinzu, durch die die Orchestrierung gestartet wird.

### <a name="client-function-http-starter"></a>Clientfunktion (HTTP-Starter)

1. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Create Function...`, und wählen Sie ihn aus.

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen ein:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | „Select a template for your function“ (Wählen Sie eine Vorlage für Ihre Funktion aus.) | HTTP-Starter für Durable Functions | Erstellen Sie eine HTTP-Startfunktion. |
    | Angeben eines Funktionsnamens | HttpStart | Der Name Ihrer Aktivitätsfunktion. |
    | Autorisierungsstufe | Anonym | Lassen Sie zu Demonstrationszwecken das Aufrufen der Funktion ohne Authentifizierung zu. |

Sie haben eine per HTTP ausgelöste Funktion hinzugefügt, durch die eine Orchestrierung gestartet wird. Öffnen Sie *HttpStart/run.ps1*. Sie sehen, dass das Cmdlet `Start-NewOrchestration` zum Starten einer neuen Orchestrierung verwendet wird. Anschließend wird mit dem Cmdlet `New-OrchestrationCheckStatusResponse` eine HTTP-Antwort mit URLs zurückgegeben, die zum Überwachen und Verwalten der neuen Orchestrierung verwendet werden können.

Sie verfügen nun über eine Durable Functions-App, die lokal ausgeführt und in Azure bereitgestellt werden kann.

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

Mit Azure Functions Core-Tools können Sie ein Azure Functions-Projekt auf dem lokalen Entwicklungscomputer ausführen. Sie werden beim ersten Starten einer Funktions-App in Visual Studio Code zum Installieren dieser Tools aufgefordert.

1. Legen Sie zum Testen der Funktion einen Breakpoint im Code der Aktivitätsfunktion `Hello` (*Hello/run.ps1*) fest. Drücken Sie F5, oder wählen Sie über die Befehlspalette den Befehl `Debug: Start Debugging` aus, um das Funktions-App-Projekt zu starten. Die Ausgabe der Core Tools wird im Bereich **Terminal** angezeigt.

    > [!NOTE]
    > Weitere Informationen zum Debuggen finden Sie unter [Diagnose in Durable Functions in Azure](durable-functions-diagnostics.md#debugging).

1. Zum Ausführen von Durable Functions ist ein Azure-Speicherkonto erforderlich. Wenn Sie von VS Code zum Auswählen eines Speicherkontos aufgefordert werden, wählen Sie **Speicherkonto auswählen** aus.

    ![Speicherkonto erstellen](media/quickstart-js-vscode/functions-select-storage.png)

1. Geben Sie bei den Eingabeaufforderungen die folgenden Informationen an, um ein neues Speicherkonto in Azure zu erstellen:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | Auswählen des Abonnements | *Name Ihres Abonnements* | Auswählen des Azure-Abonnements |
    | Auswählen eines Speicherkontos | Erstellen eines neuen Speicherkontos |  |
    | „Enter the name of the new storage account“ (Geben Sie den Namen des neuen Speicherkontos ein.) | *Eindeutiger Name* | Der Name des zu erstellenden Speicherkontos. |
    | Auswählen einer Ressourcengruppe | *Eindeutiger Name* | Der Name der zu erstellenden Ressourcengruppe. |
    | Standort auswählen | *Region* | Wählen Sie eine Region in Ihrer Nähe aus. |

1. Kopieren Sie im Bereich **Terminal** den URL-Endpunkt Ihrer über HTTP ausgelösten Funktion.

    ![Lokale Azure-Ausgabe](media/quickstart-js-vscode/functions-f5.png)

1. Senden Sie mit Ihrem Browser oder einem Tool wie [Postman](https://www.getpostman.com/) oder [cURL](https://curl.haxx.se/) eine HTTP-POST-Anforderung an den URL-Endpunkt. Ersetzen Sie das letzte Segment durch den Namen der Orchestratorfunktion (`HelloOrchestrator`). Die URL sollte in etwa wie folgt lauten: `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   Die Antwort ist das erste Ergebnis der HTTP-Funktion, um mitzuteilen, dass die dauerhafte Orchestrierung erfolgreich gestartet wurde. Es ist noch nicht das Endergebnis der Orchestrierung. Die Antwort enthält einige nützliche URLs. Zunächst fragen wir den Status der Orchestrierung ab.

1. Kopieren Sie den URL-Wert für `statusQueryGetUri`, fügen Sie ihn in die Adressleiste des Browsers ein, und führen Sie anschließend die Anforderung aus. Alternativ können Sie auch weiter Postman verwenden, um die GET-Anforderung auszuführen.

   Mit der Anforderung wird für die Orchestrierungsinstanz der Status abgefragt. Sie sollten schließlich eine Antwort erhalten, die zeigt, dass die Instanz abgeschlossen wurde, und die die Ausgaben oder Ergebnisse der dauerhaften Funktion enthält. Er sieht wie folgt aus: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Drücken Sie in VS Code **UMSCHALT+F5**, um das Debuggen zu beenden.

Nachdem Sie sichergestellt haben, dass die Funktion auf Ihrem lokalen Computer richtig ausgeführt wird, können Sie das Projekt in Azure veröffentlichen.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

In diesem Abschnitt erstellen Sie eine Funktions-App sowie zugehörige Ressourcen in Ihrem Azure-Abonnement und stellen anschließend Ihren Code bereit. 

> [!IMPORTANT]
> Beim Veröffentlichen in einer vorhandenen Funktions-App wird der Inhalt dieser App in Azure überschrieben. 


1. Wählen Sie auf der Aktivitätsleiste das Azure-Symbol und anschließend im Bereich **Azure: Funktionen** die Schaltfläche **Deploy to function app...** (In Funktions-App bereitstellen...) aus.

    ![Veröffentlichen Ihres Projekts in Azure](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    + **Wählen Sie einen Ordner aus:** Wählen Sie einen Ordner in Ihrem Arbeitsbereich aus, oder navigieren Sie zu einem Ordner, der Ihre Funktions-App enthält. Diese Option wird nicht angezeigt, wenn Sie bereits eine gültige Funktions-App geöffnet haben.

    + **Wählen Sie das Abonnement aus:** Wählen Sie das zu verwendende Abonnement aus. Wenn Sie nur über ein Abonnement verfügen, wird diese Option nicht angezeigt.

    + **Select Function App in Azure:** (Wählen Sie die Funktions-App in Azure aus:) Wählen Sie die Option `+ Create new Function App`. (Wählen Sie nicht die Option `Advanced` aus, diese wird im vorliegenden Artikel nicht behandelt.)
      
    + **Enter a globally unique name for the function app:** (Geben Sie einen global eindeutigen Namen für die Funktions-App ein:) Geben Sie einen Namen ein, der in einem URL-Pfad gültig ist. Der eingegebene Name wird überprüft, um sicherzustellen, dass er in Azure Functions eindeutig ist. 

    + **Select a runtime:** (Wählen Sie eine Runtime aus:) Wählen Sie die lokal ausgeführte PowerShell-Version aus. Sie können den Befehl `pwsh -version` ausführen, um Ihre Version zu überprüfen.

        > [!NOTE]
        > Die VS Code-Erweiterung von Azure Functions unterstützt PowerShell 7 unter Umständen noch nicht. Ist PowerShell 7 nicht als Option verfügbar, wählen Sie vorerst PowerShell 6.x aus, und [aktualisieren Sie die Version manuell](#update-function-app-ps7) nach der Erstellung der Funktions-App.

    + **Wählen Sie einen Speicherort für neue Ressourcen aus:**  Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe aus, um eine bessere Leistung zu erzielen. 
    
1.  Nach Abschluss des Vorgangs werden in Ihrem Abonnement die folgenden Azure-Ressourcen erstellt, deren Namen auf dem Namen Ihrer Funktions-App basieren:
    
    + Eine Ressourcengruppe als logischen Container für verwandte Ressourcen.
    + Ein Azure Storage-Standardkonto, in dem Status- und andere Informationen zu Ihren Projekten verwaltet werden.
    + Ein Verbrauchsplan, der den zugrunde liegenden Host für Ihre serverlose Funktions-App definiert. 
    + Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Mit einer Funktions-App können Sie Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen im selben Hostingplan.
    + Eine mit der Funktions-App verbundene Application Insights-Instanz, die die Nutzung Ihrer serverlosen Funktion nachverfolgt.

    Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet.

1. <a name="update-function-app-ps7"></a>Wenn Sie zuvor beim Erstellen der Funktions-App nicht *PowerShell 7* auswählen konnten, drücken Sie F1 (oder STRG/CMD+UMSCHALT+P), um die Befehlspalette zu öffnen. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Upload Local Settings...`, und wählen Sie ihn aus. Befolgen Sie die Anweisungen zum Auswählen der zuvor erstellten Funktions-App. Wählen Sie *Nein, keine* aus, wenn Sie zum Überschreiben vorhandener Einstellungen aufgefordert werden.
    
1. Wählen Sie in dieser Benachrichtigungen **Ausgabe anzeigen** aus, um die Erstellungs- und Bereitstellungsergebnisse (auch für die von Ihnen erstellten Azure-Ressourcen) anzuzeigen. Wenn Sie die Benachrichtigung übersehen haben, wählen Sie das Glockensymbol in der unteren rechten Ecke aus, um sie erneut anzuzeigen.

    ![Benachrichtigung nach Abschluss der Erstellung](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Testen der Funktion in Azure

1. Kopieren Sie die URL des HTTP-Triggers im Bereich **Ausgabe**. Die URL, über die Ihre per HTTP ausgelöste Funktion aufgerufen wird, muss das folgende Format haben: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Fügen Sie diese neue URL für die HTTP-Anforderung in die Adresszeile des Browsers ein. Sie sollten die gleiche Statusantwort wie zuvor erhalten, als Sie die veröffentlichte App verwendet haben.

## <a name="next-steps"></a>Nächste Schritte

Sie haben Visual Studio Code verwendet, um eine dauerhafte PowerShell-Funktions-App zu erstellen und zu veröffentlichen.

> [!div class="nextstepaction"]
> [Informationen zu gängigen Mustern für dauerhafte Funktionen](durable-functions-overview.md#application-patterns)
