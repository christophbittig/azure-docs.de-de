---
title: Integrieren mit Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Erfahren Sie, Azure Digital Twins-Telemetriedaten mit Azure SignalR an Clients gestreamt werden.
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8690f69bace02e73f144b563a5541073cb976f22
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770608"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrieren von Azure Digital Twins in Azure SignalR Service

In diesem Artikel erfahren Sie, wie Sie Azure Digital Twins in [Azure SignalR Service](../azure-signalr/signalr-overview.md) integrieren.

Mithilfe der in diesem Artikel beschriebenen Lösung können Sie Telemetriedaten des digitalen Zwillings an verbundene Clients pushen, z. B. an eine einzelne Webseite oder eine mobile Anwendung. Das Ergebnis ist, dass Clients mit Echtzeitmetriken und Statusinformationen von IoT-Geräten aktualisiert werden, ohne dass der Server abgefragt werden muss oder neue HTTP-Anforderungen für Updates gesendet werden müssen.

## <a name="prerequisites"></a>Voraussetzungen

Folgende Voraussetzungen müssen erfüllt sein, bevor Sie fortfahren können:

* Bevor Sie Ihre Lösung in Azure SignalR Service integrieren, sollten Sie das Azure Digital Twins-Tutorial [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md) durcharbeiten, da die Vorgehensweise in diesem Artikel darauf aufbaut. Das Tutorial führt Sie durch das Einrichten einer Azure Digital Twins-Instanz, die mit einem virtuellen IoT-Gerät funktioniert, um Updates für digitale Zwillinge auszulösen. In diesem Artikel zur Vorgehensweise werden diese Updates per Azure SignalR Service mit einer Beispiel-Web-App verbunden.

* Sie benötigen die folgenden Werte aus dem Tutorial:
  - Event Grid-Thema
  - Resource group
  - App-Dienst-/Funktions-App-Name
    
* Auf Ihrem Computer muss [Node.js](https://nodejs.org/) installiert sein.

Stellen Sie sicher, dass Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) anmelden, da Sie es in diesem Leitfaden verwenden müssen.

## <a name="solution-architecture"></a>Lösungsarchitektur

Sie fügen Azure SignalR Service über den unten angegebenen Pfad an Azure Digital Twins an. Die Abschnitte A, B und C in der Abbildung stammen aus dem Architekturdiagramm, das unter [Tutorial: Erstellen einer End-to-End-Lösung](tutorial-end-to-end.md) im Abschnitt „Voraussetzungen“ enthalten ist. In diesem Anleitungsartikel erstellen Sie Abschnitt D für die vorhandene Architektur, der zwei neue Azure-Funktionen enthält, die mit SignalR und Client-Apps kommunizieren.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Diagramm der Azure-Dienste in einem End-to-End-Szenario, das den Ein- und Ausgang von Daten bei Azure Digital Twins darstellt." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Herunterladen der Beispielanwendungen

Laden Sie zunächst die erforderlichen Beispiel-Apps herunter. Sie benötigen die beiden folgenden Beispiele:
* [End-to-End-Beispiele für Azure Digital Twins:](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) Dieses Beispiel enthält eine *AdtSampleApp* mit zwei Azure-Funktionen zum Verschieben von Daten in eine Azure Digital Twins-Instanz (weitere Informationen zu diesem Szenario finden Sie unter [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md)). Es enthält auch eine *DeviceSimulator*-Beispielanwendung, die ein IOT-Gerät simuliert und jede Sekunde einen neuen Temperaturwert generiert.
    - Wenn Sie das Beispiel nicht bereits als Teil des Tutorials unter [Voraussetzungen](#prerequisites) heruntergeladen haben, [navigieren Sie zum Beispiel](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), und wählen die Schaltfläche *Code durchsuchen* unter dem Titel aus. Dadurch gelangen Sie zum GitHub-Repository für die Beispiele, die Sie als ZIP-Datei herunterladen können. Wählen Sie hierzu die Schaltfläche *Code* und anschließend *ZIP herunterladen* aus.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Screenshot des Repositorys „digital-twins-samples“ auf GitHub und der Schritte zum Herunterladen des Repositorys als ZIP-Datei." lightbox="media/includes/download-repo-zip.png":::

    Diese Schaltfläche lädt eine Kopie des Beispielrepositorys als **digital-twins-samples-master.zip** auf Ihren Computer herunter. Entpacken Sie den Ordner.
* [Web-App-Beispiel für SignalR-Integration](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): Diese Beispiel-Web-App (React) nutzt Azure Digital Twins-Telemetriedaten über eine Azure SignalR Service-Instanz.
    -  Navigieren Sie zum Link des Beispiels, und führen Sie den gleichen Downloadprozess durch, um eine Kopie des Beispiels auf Ihren Computer herunterzuladen (_**digitaltwins-signalr-webapp-sample-main.zip**_). Entpacken Sie den Ordner.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lassen Sie das Browserfenster mit dem Azure-Portal geöffnet, da Sie es im nächsten Abschnitt erneut verwenden.

## <a name="publish-and-configure-the-azure-functions-app"></a>Veröffentlichen und Konfigurieren der Azure-Funktions-App

In diesem Abschnitt richten Sie zwei Azure-Funktionen ein:
* **negotiate**: eine HTTP-Triggerfunktion. Sie verwendet die *SignalRConnectionInfo*-Eingabebindung, um gültige Verbindungsinformationen zu generieren und zurückzugeben.
* **broadcast**: eine [Event Grid](../event-grid/overview.md)-Triggerfunktion. Sie empfängt Azure Digital Twins-Telemetriedaten über das Event Grid und verwendet die Ausgabebindung der im vorherigen Schritt erstellten *SignalR*-Instanz, um die Nachricht an alle verbundenen Clientanwendungen zu übertragen.

Starten Sie Visual Studio (oder einen anderen Code-Editor Ihrer Wahl), und öffnen Sie die Codeprojektmappe im Ordner *digital-twins-samples-master > ADTSampleApp*. Führen Sie dann die folgenden Schritte aus, um die Funktionen zu erstellen:

1. Erstellen Sie im Projekt *SampleFunctionsApp* eine neue C#-Klasse namens **SignalRFunctions.cs**. Anweisungen zum Erstellen einer neuen Klasse finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

1. Ersetzen Sie den Inhalt der Klassendatei durch den folgenden Code:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Navigieren Sie im Fenster *Paket-Manager-Konsole* von Visual Studio oder in einem beliebigen Befehlsfenster auf Ihrem Computer zum Ordner *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp*, und führen Sie den folgenden Befehl aus, um das NuGet-Paket `SignalRService` für das Projekt zu installieren:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Die Ausführung dieses Befehls sollte alle Abhängigkeitsprobleme in der Klasse beheben.

1. Veröffentlichen Sie Ihre Funktion in Azure. Sie können sie in der gleichen App Service-/Funktions-App veröffentlichen, die Sie im End-to-End-[Voraussetzungstutorial](#prerequisites) verwendet haben, oder Sie erstellen eine neue App. Wahrscheinlich möchten Sie jedoch dieselbe App verwenden, um Duplizierungen zu minimieren. Anweisungen zum Veröffentlichen einer Funktion mithilfe von Visual Studio finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

### <a name="configure-the-function"></a>Konfigurieren der Funktion

Konfigurieren Sie als Nächstes die Funktion für die Kommunikation mit Ihrer Azure SignalR-Instanz. Zunächst ermitteln Sie die **Verbindungszeichenfolge** der SignalR-Instanz und fügen diese dann den Einstellungen der Funktions-App hinzu.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und suchen Sie oben im Portal in der Suchleiste nach dem Namen Ihrer SignalR-Instanz. Wählen Sie die Instanz aus, um sie zu öffnen.
1. Wählen Sie im Menü der Instanz die Option **Schlüssel** aus, um die Verbindungszeichenfolgen für die SignalR Service-Instanz anzuzeigen.
1. Wählen Sie das Symbol *Kopieren* aus, um die primäre Verbindungszeichenfolge zu kopieren.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Screenshot des Azure-Portals mit der Seite „Schlüssel“ für die SignalR-Instanz. Die Verbindungszeichenfolge wird kopiert." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Fügen Sie abschließend Ihre Azure SignalR-**Verbindungszeichenfolge** den App-Einstellungen der Funktion hinzu, indem Sie den folgenden Azure CLI-Befehl verwenden. Ersetzen Sie darüber hinaus die Platzhalter durch die Namen Ihrer Ressourcengruppe und des App-Diensts bzw. der Funktions-App aus den [Voraussetzungen für das Tutorial](how-to-integrate-azure-signalr.md#prerequisites). Der Befehl kann in [Azure Cloud Shell](https://shell.azure.com) oder lokal ausgeführt werden, wenn die [Azure CLI auf Ihrem Computer installiert](/cli/azure/install-azure-cli) ist:
 
    ```azurecli-interactive
    az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Die Ausgabe dieses Befehls gibt alle App-Einstellungen aus, die für Ihre Azure-Funktion eingerichtet sind. Suchen Sie unten in der Liste nach `AzureSignalRConnectionString`, um sicherzustellen, dass der Wert hinzugefügt wurde.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Screenshot der Ausgabe in einem Befehlsfenster mit einem Listenelement mit dem Namen „AzureSignalRConnectionString“.":::

## <a name="connect-the-function-to-event-grid"></a>Verbinden der Funktion mit Event Grid

Abonnieren Sie als Nächstes die Azure-Funktion *broadcast* für das **Event Grid-Thema**, das Sie beim Erfüllen der [Voraussetzungen für das Tutorial](how-to-integrate-azure-signalr.md#prerequisites) erstellt haben. Diese Aktion ermöglicht es, dass Telemetriedaten vom Zwilling „thermostat67“ über das Event Grid-Thema an die Funktion gesendet werden. Von hier aus kann die Funktion die Daten an alle Clients übertragen.

Sie erstellen zum Übertragen der Daten ein **Ereignisabonnement** aus Ihrem Event Grid-Thema mit der Azure-Funktion *broadcast* als Endpunkt.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Event Grid-Thema, indem Sie in der oberen Suchleiste nach dessen Namen suchen. Klicken Sie auf *+ Ereignisabonnement*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Screenshot des Erstellens eines Ereignisabonnements im Azure-Portal.":::

Füllen Sie auf der Seite *Ereignisabonnement erstellen* die Felder wie folgt aus (standardmäßig ausgefüllte Felder werden nicht angegeben):
* *DETAILS ZUM EREIGNISABONNEMENT* > **Name**: Geben Sie Ihrem Ereignisabonnement einen Namen.
* *ENDPUNKTDETAILS* > **Endpunkttyp**: Wählen Sie im Menü die Option *Azure-Funktion* aus.
* *ENDPUNKTDETAILS* > **Endpunkt**: Wählen Sie den Link *Endpunkt auswählen* aus. Dadurch wird das Fenster *Azure-Funktion auswählen* geöffnet:
    - Füllen Sie die Felder **Abonnement**, **Ressourcengruppe**, **Funktions-App** und **Funktion** (*broadcast*) aus. Einige dieser Felder werden unter Umständen automatisch ausgefüllt, nachdem Sie das Abonnement ausgewählt haben.
    - Klicken Sie auf **Auswahl bestätigen**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Screenshot des Formulars zum Erstellen eines Ereignisabonnements im Azure-Portal.":::

Wählen Sie auf der Seite *Ereignisabonnement erstellen* die Option **Erstellen** aus.

An diesem Punkt sollten auf der Seite *Event Grid-Thema* zwei Ereignisabonnements angezeigt werden.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Screenshot des Azure-Portals mit zwei Ereignisabonnements auf der Seite „Event Grid-Thema“." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Konfigurieren und Ausführen der Web-App

In diesem Abschnitt wird das Ergebnis in Aktion gezeigt. Zunächst konfigurieren Sie die **Beispielclient-Web-App**, um eine Verbindung mit dem Azure SignalR-Flow herzustellen, den Sie eingerichtet haben. Dann starten Sie die **Beispiel-App für simulierte Geräte**, die Telemetriedaten über Ihre Azure Digital Twins-Instanz sendet. Danach zeigen Sie die Beispiel-Web-App an, um zu sehen, wie die simulierten Gerätedaten die Beispiel-Web-App in Echtzeit aktualisieren.

### <a name="configure-the-sample-client-web-app"></a>Konfigurieren der Beispielclient-Web-App

Konfigurieren Sie als Nächstes die Beispielclient-Web-App. Ermitteln Sie zunächst die **HTTP-Endpunkt-URL** über die Funktion *negotiate*, und nutzen Sie diese dann, um den App-Code auf Ihrem Computer zu konfigurieren.

1. Navigieren Sie im Azure-Portal zur Seite [Funktions-Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp), und wählen Sie in der Liste Ihre Funktions-App aus. Wählen Sie im App-Menü *Funktionen* aus, und wählen Sie dann die *negotiate*-Funktion aus.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Screenshot der Functions-Apps im Azure-Portal, wobei „Functions“ im Menü und „negotiate“ in der Liste der Funktionen hervorgehoben ist.":::

1. Wählen Sie *Funktions-URL abrufen* aus, und kopieren Sie den Wert **bis zu _/api_ (schließen Sie die letzte Angabe _/negotiate?_ nicht ein)** . Dieser Wert wird im nächsten Schritt verwendet.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Screenshot des Azure-Portals mit der Funktion „negotiate“ und hervorgehobener Schaltfläche „Funktions-URL abrufen“ sowie hervorgehobener Funktions-URL.":::

1. Öffnen Sie mithilfe von Visual Studio oder einem beliebigen Code-Editor Ihrer Wahl den entpackten Ordner _**digitaltwins-signalr-webapp-sample-main**_, den Sie im Abschnitt [Herunterladen der Beispielanwendungen](#download-the-sample-applications) heruntergeladen haben.

1. Öffnen Sie die Datei *src/App.js*, und ersetzen Sie die Funktions-URL in `HubConnectionBuilder` durch die HTTP-Endpunkt-URL der **negotiate**-Funktion, die Sie im vorherigen Schritt gespeichert haben:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function-URL>')
            .build();
    ```
1. Navigieren Sie in der *Developer-Eingabeaufforderung* von Visual Studio oder in einem beliebigen Befehlsfenster auf Ihrem Computer zum Ordner *digitaltwins-signalr-webapp-sample-main\src*. Führen Sie den folgenden Befehl aus, um die abhängigen Knotenpakete zu installieren:

    ```cmd
    npm install
    ```

Legen Sie nun Berechtigungen in Ihrer Funktions-App im Azure-Portal fest:
1. Wählen Sie auf der Seite [Funktions-Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) des Azure-Portals ihre Funktions-App-Instanz aus.

1. Scrollen Sie im Instanzmenü nach unten, und wählen Sie *CORS* aus. Fügen Sie auf der CORS-Seite `http://localhost:3000` als zulässigen Ursprung hinzu, indem Sie diese Angabe in das leere Feld eingeben. Aktivieren Sie das Kontrollkästchen *Access-Control-Allow-Credentials aktivieren*, und wählen Sie *Speichern* aus.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Screenshot des Azure-Portals mit der CORS-Einstellung in Azure Functions.":::

### <a name="run-the-device-simulator"></a>Ausführen des Gerätesimulators

Währen des End-to-End-Voraussetzungstutorials haben Sie den [Gerätesimulator konfiguriert](tutorial-end-to-end.md#configure-and-run-the-simulation), um Daten über einen IoT-Hub und an Ihre Azure Digital Twins-Instanz zu senden.

Nun müssen Sie nur noch das Simulatorprojekt starten, das sich in *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln* befindet. Wenn Sie Visual Studio verwenden, können Sie das Projekt öffnen und dann mit dieser Schaltfläche auf der Symbolleiste ausführen:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Screenshot der Visual Studio-Startschaltfläche mit geöffnetem Projekt „DeviceSimulator“.":::

Im geöffneten Konsolenfenster werden Meldungen mit simulierten Telemetriedaten zur Temperatur angezeigt. Diese Nachrichten werden über Ihre Azure Digital Twins-Instanz gesendet. Dort werden sie dann von Azure Functions und SignalR übernommen.

Sie müssen in dieser Konsole keine weiteren Schritte ausführen, aber Sie sollten sie während des nächsten Schritts geöffnet lassen.

### <a name="see-the-results"></a>Anzeigen der Ergebnisse

Um die Ergebnisse in Aktion zu sehen, starten Sie das **SignalR-Integrations-Web-App-Beispiel**. Sie können das Beispiel in jedem Konsolenfenster im Ordner *digitaltwins-signalr-webapp-sample-main\src* starten, indem Sie diesen Befehl ausführen:

```cmd
npm start
```

Durch die Ausführung dieses Befehls wird ein Browserfenster geöffnet, in dem die Beispiel-App ausgeführt wird, die ein visuelles Temperaturmessgerät angezeigt. Sobald die App ausgeführt wird, sollten Sie die Temperaturtelemetriewerte vom Gerätesimulator sehen, die über Azure Digital Twins weitergegeben und in Echtzeit von der Web-App angezeigt werden.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Screenshot der Beispielclient-Web-App, der ein visuelles Temperaturmessgerät anzeigt. Die gezeigt Temperatur ist 67,52.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen nicht mehr benötigen, folgen Sie den Schritten unten, um sie zu löschen. 

Bei Verwendung von Azure Cloud Shell oder der lokalen Azure CLI können Sie alle Azure-Ressourcen in einer Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) löschen. Wenn Sie die Ressourcengruppe entfernen, wird auch Folgendes entfernt...
* Die Azure Digital Twins-Instanz (aus dem End-to-End-Tutorial)
* Der IoT-Hub-und die Hub-Geräteregistrierung (aus dem End-to-End-Tutorial)
* Das Event Grid-Thema und zugehörige Abonnements
* Die Azure Functions-App, einschließlich aller drei Funktionen und zugeordneten Ressourcen wie Speicher
* Die Azure SignalR-Instanz

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Löschen Sie abschließend die Projektbeispielordner, die Sie auf Ihren lokalen Computer heruntergeladen haben (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* und die entsprechenden entpackten Daten).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel richten Sie Azure-Funktionen mit SignalR ein, um Azure Digital Twins-Telemetrieereignisse an eine Clientbeispielanwendung zu senden.

Erfahren Sie dann über Azure SignalR Service:
* [Was ist Azure SignalR Service?](../azure-signalr/signalr-overview.md)

Erfahren Sie mehr über Azure SignalR Service-Authentifizierung mit Azure Functions:
* [Azure SignalR Service-Authentifizierung](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)