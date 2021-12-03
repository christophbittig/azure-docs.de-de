---
title: 'Tutorial: Erstellen einer End-to-End-Lösung'
titleSuffix: Azure Digital Twins
description: In diesem Tutorial erfahren Sie, wie Sie eine Azure Digital Twins-End-to-End-Lösung erstellen, die auf Gerätedaten basiert.
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 9233f33818e9961cc5daf8cf35b14457172ecae3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504833"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Tutorial: Erstellen einer End-to-End-Lösung

In diesem Azure Digital Twins-Tutorial wird beschrieben, wie Sie eine End-to-End-Lösung erstellen, die die Funktionalität des Diensts veranschaulicht. Für die Einrichtung einer vollständigen End-to-End-Lösung, die auf Livedaten aus Ihrer Umgebung basiert, können Sie für Ihre Azure Digital Twins-Instanz eine Verbindung mit anderen Azure-Diensten herstellen, um die Verwaltung von Geräten und Daten durchzuführen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten einer Azure Digital Twins-Instanz
> * Kennenlernen des Beispielszenarios für die Erstellung und Instanziieren der vorab geschriebenen Komponenten
> * Verwenden einer [Azure Functions](../azure-functions/functions-overview.md)-App zum Weiterleiten von simulierten Telemetriedaten von einem [IoT Hub](../iot-hub/about-iot-hub.md)-Gerät an Eigenschaften digitaler Zwillinge
> * Verteilen von Änderungen über den **Zwillingsgraphen** durch die Verarbeitung von Benachrichtigungen für digitale Zwillinge mit Azure Functions, Endpunkten und Routen

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Einrichten einer Cloud Shell-Sitzung
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Einstieg in die Erstellung

Im Beispielprojekt dieses Tutorials geht es um ein Szenario mit einem realen **Gebäude**, das über eine Etage, einen Raum und ein Thermostat verfügt. Diese Komponenten werden in einer Azure Digital Twins-Instanz digital dargestellt, die dann mit [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md) und zwei [Azure-Funktionen](../azure-functions/functions-overview.md) verbunden wird, um die Verschiebung von Daten zu ermöglichen.

Unten ist ein Diagramm mit dem vollständigen Szenario angegeben. 

Sie erstellen zuerst die Azure Digital Twins-Instanz (im Diagramm **Abschnitt A**) und richten dann den Fluss der Telemetriedaten zu den digitalen Zwillingen ein (**Pfeil B**). Anschließend richten Sie die Verteilung der Daten über den Zwillingsgraphen ein (**Pfeil C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Diagramm des vollständigen Gebäudeszenarios, das die Daten zeigt, die von einem Gerät über verschiedene Azure-Dienste in und aus Azure Digital Twins fließen":::

Für dieses Szenario interagieren Sie mit den Komponenten der vorab geschriebenen Beispiel-App, die Sie weiter oben heruntergeladen haben.

Hier sind die Komponenten aufgeführt, die mit der Beispiel-App *AdtSampleApp* für das Gebäudeszenario implementiert werden:
* Geräte-Authentifizierung 
* Beispiele für die Nutzung des [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (in *CommandLoop.cs*)
* Konsolenschnittstelle zum Aufrufen der Azure Digital Twins-API
* *SampleClientApp*: Azure Digital Twins-Beispiellösung
* *SampleFunctionsApp*: Eine Azure Functions-App, mit der Ihr Azure Digital Twins-Graph basierend auf den Telemetriedaten von IoT Hub- und Azure Digital Twins-Ereignissen aktualisiert wird

### <a name="instantiate-the-pre-created-twin-graph"></a>Instanziieren des vorab erstellten Zwillingsgraphen

Zunächst verwenden Sie die Lösung *AdtSampleApp* aus dem Beispielprojekt, um die Azure Digital Twins-Komponente des End-to-End-Szenarios zu erstellen (**Abschnitt A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Diagramm eines Auszugs aus dem Diagramm mit dem vollständigen Gebäudeszenario mit Hervorhebung des Abschnitts der Azure Digital Twins-Instanz":::

Führen Sie in Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, das Projekt mit dieser Symbolleistenschaltfläche aus:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Screenshot: Visual Studio-Startschaltfläche mit geöffnetem Projekt „SampleClientApp“":::

Ein Konsolenfenster wird geöffnet, die Authentifizierung wird durchgeführt, und dann wird auf einen Befehl gewartet. Führen Sie in dieser Konsole den nächsten Befehl aus, um die Azure Digital Twins-Beispiellösung zu instanziieren.

> [!IMPORTANT]
> Wenn Sie auf Ihrer Azure Digital Twins-Instanz bereits über digitale Zwillinge und Beziehungen verfügen, werden diese mit dem Ausführen dieses Befehls gelöscht und durch die Zwillinge und Beziehungen für das Beispielszenario ersetzt.

```cmd/sh
SetupBuildingScenario
```

Die Ausgabe dieses Befehls umfasst eine Reihe von Bestätigungsmeldungen, während in Ihrer Azure Digital Twins-Instanz drei [digitale Zwillinge](concepts-twins-graph.md) erstellt und verbunden werden: die Etage „floor1“, der Raum „room21“ und der Temperatursensor „thermostat67“. Diese digitalen Zwillinge stehen für die Entitäten, die auch in einer realen Umgebung vorhanden wären.

Sie werden über Beziehungen mit dem folgenden [Zwillingsgraphen](concepts-twins-graph.md) verbunden. Der Zwillingsgraph steht für die gesamte Umgebung, z. B. für die Interaktion der Entitäten und ihre Beziehung untereinander.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Diagramm: „floor1“ enthält „room21“, und „room21“ enthält „thermostat67“" border="false":::

Sie können die erstellten Zwillinge überprüfen, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden für die verbundene Azure Digital Twins-Instanz alle digitalen Zwillinge abgefragt, die sie enthält:

```cmd/sh
Query
```

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

Nun können Sie die Ausführung des Projekts beenden. Lassen Sie die Projektmappe mit der Lösung aber in Visual Studio geöffnet, weil Sie sie für das Tutorial noch benötigen.

## <a name="set-up-the-sample-function-app"></a>Einrichten der Beispiel-Funktions-App

Der nächste Schritt umfasst die Einrichtung einer [Azure Functions-App](../azure-functions/functions-overview.md), die im gesamten Tutorial zum Verarbeiten von Daten verwendet wird. Die Funktions-App *SampleFunctionsApp* enthält zwei Funktionen:
* *ProcessHubToDTEvents*: Verarbeitet eingehende IoT Hub-Daten und aktualisiert Azure Digital Twins entsprechend.
* *ProcessDTRoutedData*: Verarbeitet Daten digitaler Zwillinge und aktualisiert die übergeordneten Zwillinge in Azure Digital Twins entsprechend.

In diesem Abschnitt veröffentlichen Sie die vorab geschriebene Funktions-App und stellen sicher, dass diese auf Azure Digital Twins zugreifen kann, indem Sie ihr eine Azure AD-Identität (Azure Active Directory) zuweisen. Wenn Sie diese Schritte ausführen, können im restlichen Teil des Tutorials die Funktionen in der Funktions-App verwendet werden. 

In Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, ist die Funktions-App in der Projektdatei _**SampleFunctionsApp**_ enthalten. Sie können sie im Bereich *Projektmappen-Explorer* anzeigen.

### <a name="update-dependencies"></a>Aktualisieren von Abhängigkeiten

Vor dem Veröffentlichen der App sollten Sie sicherstellen, dass Ihre Abhängigkeiten auf dem aktuellen Stand sind und dass Sie jeweils über die aktuelle Version aller enthaltenen Pakete verfügen.

Erweitern Sie im Bereich *Projektmappen-Explorer* die Optionen _**SampleFunctionsApp** > Abhängigkeiten_. Klicken Sie mit der rechten Maustaste auf *Pakete*, und wählen Sie *NuGet-Pakete verwalten...* aus.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Screenshot: Visual Studio mit der Menüschaltfläche „NuGet-Pakete verwalten“ für das SampleFunctionsApp-Projekt" border="false":::

Dadurch wird der NuGet-Paket-Manager geöffnet. Wählen Sie die Registerkarte *Updates* aus. Falls zu aktualisierende Pakete vorhanden sind, aktivieren Sie das Kontrollkästchen *Alle Pakete auswählen*. Wählen Sie dann *Aktualisieren* aus.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Screenshot von Visual Studio mit der Vorgehensweise beim Auswählen der Aktualisierung aller Pakete im NuGet-Manager":::

### <a name="publish-the-app"></a>Veröffentlichen der App

Um die Funktions-App in Azure zu veröffentlichen, müssen Sie zunächst ein Speicherkonto erstellen, dann die Funktions-App in Azure erstellen und schließlich die Funktionen in der Azure-Funktions-App veröffentlichen. In diesem Abschnitt werden diese Aktionen mithilfe von Azure CLI abgeschlossen.

1. Erstellen Sie mit folgendem Befehl ein **Azure-Speicherkonto**:

    ```azurecli-interactive
    az storage account create --name <name-for-new-storage-account> --location <location> --resource-group <resource-group> --sku Standard_LRS
    ```

1. Erstellen Sie mit folgendem Befehl eine **Azure-Funktions-App**:

    ```azurecli-interactive
    az functionapp create --name <name-for-new-function-app> --storage-account <name-of-storage-account-from-previous-step> --consumption-plan-location <location> --runtime dotnet --resource-group <resource-group>
    ```

1. Als Nächstes komprimieren Sie die Funktionen in eine **ZIP**-Datei und **veröffentlichen** sie in Ihrer neuen Azure-Funktions-App.

    1. Öffnen Sie ein Terminal wie PowerShell auf Ihrem lokalen Computer, und navigieren Sie zum [Repository mit Digital Twins-Beispielen](https://github.com/azure-samples/digital-twins-samples/tree/master/), das Sie zuvor in diesem Tutorial heruntergeladen haben. Navigieren Sie im heruntergeladenen Repositoryordner zu *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp*.
    
    1. Führen Sie in Ihrem Terminal den folgenden Befehl aus, um das Projekt zu veröffentlichen:

        ```powershell
        dotnet publish -c Release
        ```

        Dieser Befehl veröffentlicht das Projekt im Verzeichnis *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish*.

    1. Komprimieren Sie die veröffentlichten Dateien, die sich im Verzeichnis *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish* befinden, in eine ZIP-Datei. 
        
        Wenn Sie PowerShell verwenden, können Sie zum Erstellen der ZIP-Datei den vollständigen Pfad des *\publish*-Verzeichnisses kopieren und in den folgenden Befehl einfügen:
    
        ```powershell
        Compress-Archive -Path <full-path-to-publish-directory>\* -DestinationPath .\publish.zip
        ```

        Das Cmdlet erstellt eine **publish.zip**-Datei am Verzeichnisspeicherort Ihres Terminals, die eine *host.json*-Datei sowie die Verzeichnisse *bin*, *ProcessDTRoutedData* und *ProcessHubToDTEvents* enthält.

        Wenn Sie PowerShell nicht verwenden und keinen Zugriff auf das `Compress-Archive`-Cmdlet haben, müssen Sie die Dateien mithilfe des Datei-Explorers oder einer anderen Methode in eine ZIP-Datei komprimieren.

1. Führen Sie den folgenden Befehl in Azure CLI aus, um die veröffentlichten und in eine ZIP-Datei komprimierten Funktionen in Ihrer Azure-Funktions-App bereitzustellen:

    ```azurecli-interactive
    az functionapp deployment source config-zip --resource-group <resource-group> --name <name-of-your-function-app> --src "<full-path-to-publish.zip>"
    ```

    > [!NOTE]
    > Wenn Sie Azure CLI lokal verwenden, können Sie direkt über den dazugehörigen Pfad auf die ZIP-Datei auf Ihrem Computer zugreifen.
    > 
    >Wenn Sie Azure Cloud Shell verwenden, laden Sie die ZIP-Datei über diese Schaltfläche in Cloud Shell hoch, bevor Sie den Befehl ausführen:
    >
    > :::image type="content" source="media/tutorial-end-to-end/azure-cloud-shell-upload.png" alt-text="Screenshot von Azure Cloud Shell, in dem dargestellt wird, wie Dateien hochgeladen werden.":::
    >
    > In diesem Fall wird die Datei in das Stammverzeichnis Ihres Cloud Shell-Speichers hochgeladen, sodass Sie direkt über ihren Namen für den `--src`-Parameter des Befehls auf die Datei verweisen können (wie in `--src publish.zip`).

    Eine erfolgreiche Bereitstellung gibt den Statuscode 202 sowie ein JSON-Objekt mit Details Ihrer neuen Funktion zurück. Sie können bestätigen, dass die Bereitstellung erfolgreich war, indem Sie im Ergebnis nach diesem Feld suchen:

    ```json
    {
      ...
      "provisioningState": "Succeeded",
      ...
    }
    ```

Sie haben die Funktionen nun in einer Funktions-App in Azure veröffentlicht.

Damit Ihre Funktions-App als Nächstes auf Azure Digital Twins zugreifen kann, muss sie über die entsprechenden Berechtigungen verfügen. Dieser Zugriff wird im nächsten Abschnitt konfiguriert.

### <a name="configure-permissions-for-the-function-app"></a>Konfigurieren von Berechtigungen für die Funktions-App

Es müssen zwei Einstellungen festgelegt werden, damit die Funktions-App auf Ihre Azure Digital Twins-Instanz zugreifen kann. Beide können mithilfe der Azure CLI festgelegt werden. 

#### <a name="assign-access-role"></a>Zuweisen der Zugriffsrolle

Die erste Einstellung weist der Funktions-App die Rolle **Azure Digital Twins-Datenbesitzer** in der Azure Digital Twins-Instanz zu. Diese Rolle ist für alle Benutzer oder Funktionen erforderlich, von denen auf der Instanz viele Datenebenenaktivitäten durchgeführt werden sollen. Weitere Informationen zu Sicherheit und Rollenzuweisungen finden Sie unter [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md). 

1. Verwenden Sie den folgenden Befehl, um die Details der systemseitig verwalteten Identität für die Funktion anzuzeigen. Beachten Sie in der Ausgabe das Feld **principalId**.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > Falls die Ergebnisanzeige leer ist und die Details einer Identität nicht angezeigt werden, sollten Sie mit diesem Befehl eine neue systemseitig verwaltete Identität erstellen:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > In der Ausgabe werden dann Details zur Identität angezeigt, z. B. der für den nächsten Schritt benötigte Wert für **principalId**. 

1. Verwenden Sie den Wert **principalId** im folgenden Befehl, um die Identität der Funktions-App der Rolle **Azure Digital Twins Data Owner** (Azure Digital Twins-Datenbesitzer) für Ihre Azure Digital Twins-Instanz zuzuweisen.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

Die Ausgabe dieses Befehls enthält Informationen zur von Ihnen erstellten Rollenzuweisung. Die Funktions-App verfügt jetzt über Berechtigungen für den Zugriff auf Daten in Ihrer Azure Digital Twins-Instanz.

#### <a name="configure-application-settings"></a>Konfigurieren von Anwendungseinstellungen

Die zweite Einstellung erstellt eine **Umgebungsvariable** für die Funktion mit der URL Ihrer Azure Digital Twins-Instanz. Der Funktionscode verweist mit dem Wert dieser Variablen auf Ihre Instanz. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Verwalten Ihrer Funktions-App](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Führen Sie den folgenden Befehl aus, und ersetzen Sie die Platzhalter durch die Details Ihrer Ressourcen.

```azurecli-interactive
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

Bei der Ausgabe handelt es sich um die Liste der Einstellungen für die Azure-Funktion. Diese sollte nun einen Eintrag namens **ADT_SERVICE_URL** enthalten.


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Verarbeiten von simulierten Telemetriedaten von einem IoT Hub-Gerät

Ein Azure Digital Twins-Graph sollte auf Telemetriedaten von realen Geräten basieren. 

In diesem Schritt verbinden Sie ein simuliertes Thermostat, das unter [IoT Hub](../iot-hub/about-iot-hub.md) registriert ist, mit dem digitalen Zwilling, der das Gerät in Azure Digital Twins repräsentiert. Wenn das simulierte Gerät Telemetriedaten ausgibt, werden die Daten über die Azure-Funktion *ProcessHubToDTEvents* geleitet, die ein entsprechendes Update auf dem digitalen Zwilling auslöst. Auf diese Weise bleibt der digitale Zwilling über die Daten des realen Geräts auf dem Laufenden. In Azure Digital Twins wird das Leiten von Ereignisdaten von einem Ort an einen anderen als das [Weiterleiten von Ereignissen](concepts-route-events.md) bezeichnet.

Die Verarbeitung der simulierten Telemetriedaten erfolgt in diesem Teil des End-to-End-Szenarios (**Pfeil B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Diagramm eines Auszugs aus dem Diagramm mit dem vollständigen Gebäudeszenario mit Hervorhebung des Abschnitts, der Elemente vor Azure Digital Twins zeigt":::

Hier sind die Aktionen aufgeführt, die Sie zum Einrichten dieser Geräteverbindung durchführen:
1. Erstellen eines IoT-Hubs zum Verwalten des simulierten Geräts
2. Verbinden des IoT-Hubs mit der entsprechenden Azure-Funktion, indem eine Ereignisabonnement eingerichtet wird
3. Registrieren des simulierten Geräts auf dem IoT-Hub
4. Ausführen des simulierten Geräts und Generieren von Telemetriedaten
5. Abfragen von Azure Digital Twins zum Anzeigen der Liveergebnisse

### <a name="create-an-iot-hub-instance"></a>Erstellen einer IoT Hub-Instanz

Azure Digital Twins ist für die Zusammenarbeit mit [IoT Hub](../iot-hub/about-iot-hub.md) (Azure-Dienst zum Verwalten von Geräten und den darauf enthaltenen Daten) ausgelegt. In diesem Schritt richten Sie einen IoT-Hub ein, mit dem das Beispielgerät in diesem Tutorial verwaltet wird.

Verwenden Sie in Azure Cloud Shell diesen Befehl, um einen neuen IoT-Hub zu erstellen:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> --resource-group <your-resource-group> --sku S1
```

Die Ausgabe dieses Befehls enthält Informationen zum erstellten IoT-Hub.

Speichern Sie den **Namen**, den Sie Ihrem IoT-Hub gegeben haben. Es wird später noch benötigt.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Verbinden des IoT-Hubs mit der Azure-Funktion

Verbinden Sie als Nächstes Ihren IoT-Hub mit der Azure-Funktion *ProcessHubToDTEvents* in der Funktions-App, die Sie weiter oben veröffentlicht haben. Dies ermöglicht den Datenfluss vom Gerät im IoT-Hub über die Funktion, mit der Azure Digital Twins aktualisiert wird.

Zu diesem Zweck erstellen Sie ein **Ereignisabonnement** in Ihrer IoT Hub-Instanz mit der Azure-Funktion als Endpunkt. Hierdurch wird die Funktion für Ereignisse, die auf dem IoT-Hub auftreten, „abonniert“.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem neu erstellten IoT-Hub, indem Sie in der oberen Suchleiste nach dessen Namen suchen. Wählen Sie im Menü des Hubs die Option *Ereignisse* und dann *+ Ereignisabonnement* aus.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Screenshot des Azure-Portals mit dem IoT Hub-Ereignisabonnement":::

Dadurch wird die Seite *Ereignisabonnement erstellen* angezeigt.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Screenshot des Azure-Portals mit der Vorgehensweise beim Erstellen eines Ereignisabonnements":::

Füllen Sie die Felder wie folgt aus (standardmäßig ausgefüllte Felder werden nicht angegeben):
* *DETAILS ZUM EREIGNISABONNEMENT* > **Name**: Geben Sie Ihrem Ereignisabonnement einen Namen.
* *DETAILS ZUM THEMA* > **Name des Systemthemas**: Geben Sie einen Namen für das Systemthema an. 
* *EREIGNISTYPEN* > **Nach Ereignistypen filtern**: Wählen Sie im Menü die Option *Gerätetelemetrie* aus.
* *ENDPUNKTDETAILS* > **Endpunkttyp**: Wählen Sie im Menü die Option *Azure-Funktion* aus.
* *ENDPUNKTDETAILS* > **Endpunkt**: Wählen Sie den Link *Endpunkt auswählen* aus. Daraufhin wird das Fenster *Azure-Funktion auswählen* geöffnet:   :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Screenshot: Ereignisabonnement im Azure-Portal mit dem Fenster zum Auswählen einer Azure-Funktion" border="false":::
    - Füllen Sie die Felder **Abonnement**, **Ressourcengruppe**, **Funktions-App** und **Funktion** (*ProcessHubToDTEvents*) aus. Einige Werte werden unter Umständen automatisch ausgefüllt, nachdem Sie das Abonnement ausgewählt haben.
    - Klicken Sie auf **Auswahl bestätigen**.

Wählen Sie auf der Seite *Ereignisabonnement erstellen* die Option **Erstellen** aus.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registrieren des simulierten Geräts für IoT Hub 

In diesem Abschnitt wird eine Darstellung des Geräts in IoT Hub mit der ID thermostat67 erstellt. Das simulierte Gerät stellt eine Verbindung mit dieser Darstellung her. Auf diese Weise gelangen Telemetrieereignisse vom Gerät zu IoT Hub. Im IoT-Hub lauscht die abonnierte Azure-Funktion aus dem vorherigen Schritt, um die Ereignisse auszuwählen und mit der Verarbeitung fortzufahren.

Erstellen Sie per Azure Cloud Shell ein Gerät in IoT Hub, indem Sie den folgenden Befehl verwenden:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> --resource-group <your-resource-group>
```

Die Ausgabe enthält Informationen zum erstellten Gerät.

### <a name="configure-and-run-the-simulation"></a>Konfigurieren und Ausführen der Simulation

Konfigurieren Sie als Nächstes den Gerätesimulator für das Senden der Daten an Ihre IoT Hub-Instanz.

Rufen Sie zunächst mit dem folgenden Befehl die *IoT Hub-Verbindungszeichenfolge* ab:

```azurecli-interactive
az iot hub connection-string show --hub-name <your-IoT-hub-name>
```

Rufen Sie anschließend mit diesem Befehl die *Geräte-Verbindungszeichenfolge* ab:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Sie fügen diese Werte in den Gerätesimulatorcode Ihres lokalen Projekts ein, um den Simulator mit diesem IoT-Hub und dem IoT-Hub-Gerät zu verbinden.

Öffnen Sie in einem neuen Visual Studio-Fenster (im Ordner mit der heruntergeladenen Lösung) die Datei unter _Gerätesimulator > **DeviceSimulator.sln**_.

>[!NOTE]
> Sie sollten nun über zwei Visual Studio-Fenster verfügen: eins mit _**DeviceSimulator.sln**_ und ein bereits vorher geöffnetes mit _**AdtE2ESample.sln**_.

Wählen Sie im *Projektmappen-Explorer* in diesem neuen Visual Studio-Fenster _DeviceSimulator/**AzureIoTHub.cs**_ aus, um die Datei im Bearbeitungsfenster zu öffnen. Ändern Sie die folgenden Verbindungszeichenfolgen-Werte in die oben ermittelten Werte:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Speichern Sie die Datei .

Führen Sie nun das Projekt **DeviceSimulator** mit der folgenden Symbolleistenschaltfläche aus, um die Ergebnisse der von Ihnen eingerichteten Datensimulation anzuzeigen:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Screenshot: Visual Studio-Startschaltfläche mit geöffnetem Projekt „DeviceSimulator“":::

Im geöffneten Konsolenfenster werden Meldungen mit simulierten Telemetriedaten zur Temperatur angezeigt. Diese Meldungen werden an IoT Hub gesendet, wo sie dann ausgewählt und von der Azure-Funktion verarbeitet werden.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Screenshot: Konsolenausgabe des Gerätesimulators mit gesendeten Telemetriedaten zur Temperatur":::

Sie müssen in dieser Konsole keine weiteren Schritte ausführen, aber Sie sollten sie während der nächsten Schritte geöffnet lassen.

### <a name="see-the-results-in-azure-digital-twins"></a>Anzeigen der Ergebnisse in Azure Digital Twins

Die von Ihnen weiter oben veröffentlichte Funktion *ProcessHubToDTEvents* lauscht auf die IoT Hub-Daten und ruft eine Azure Digital Twins-API auf, um die *Temperature*-Eigenschaft des Zwillings thermostat67 zu aktualisieren.

Navigieren Sie zum Anzeigen der Daten aufseiten von Azure Digital Twins zu Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, und führen Sie das Projekt aus.

Führen Sie im geöffneten Konsolenfenster des Projekts den folgenden Befehl aus, um die Temperaturen abzurufen, die vom digitalen Zwilling thermostat67 gemeldet werden:

```cmd
ObserveProperties thermostat67 Temperature
```

Sie sollten verfolgen können, wie die Liveaktualisierung der Temperaturen *von Ihrer Azure Digital Twins-Instanz* alle zwei Sekunden in der Konsole protokolliert wird.

>[!NOTE]
> Es kann einige Sekunden dauern, bis die Daten vom Gerät an den Zwilling weitergegeben werden. Die ersten Temperaturmesswerte werden unter Umständen als 0 angezeigt, bevor die Daten eintreffen.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Screenshot: Konsolenausgabe mit Protokollierung der Temperaturmeldungen des digitalen Zwillings „thermostat67“":::

Nachdem Sie überprüft haben, ob die Liveprotokollierung der Temperatur richtig funktioniert, können Sie die Ausführung beider Projekte beenden. Lassen Sie die Visual Studio-Fenster geöffnet, da Sie sie im weiteren Verlauf des Tutorials noch benötigen.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Verteilen von Azure Digital Twins-Ereignissen im Graphen

Bisher haben Sie in diesem Tutorial erfahren, wie Azure Digital Twins mit externen Gerätedaten aktualisiert werden kann. Als Nächstes wird beschrieben, wie Änderungen eines digitalen Zwillings über den Azure Digital Twins-Graphen verteilt werden können. Anders ausgedrückt: Wie Zwillinge anhand von internen Daten des Diensts aktualisiert werden.

Verwenden Sie hierfür die Azure-Funktion *ProcessDTRoutedData*, um einen Zwilling vom Typ „Room“ zu aktualisieren, wenn der verbundene Thermostatzwilling aktualisiert wird. Die Updatefunktion wird in diesem Teil des End-to-End-Szenarios ausgeführt (**Pfeil C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Diagramm eines Auszugs aus dem Diagramm mit dem vollständigen Gebäudeszenario mit Hervorhebung des Abschnitts, der die Elemente nach Azure Digital Twins zeigt":::

Hier sind die Aktionen angegeben, die Sie zum Einrichten dieses Datenflusses durchführen:
1. [Erstellen eines Event Grid-Themas](#create-the-event-grid-topic) zum Aktivieren der Verschiebung von Daten zwischen Azure-Diensten
1. [Erstellen eines Endpunkts](#create-the-endpoint) in Azure Digital Twins zum Verbinden der Instanz mit dem Event Grid-Thema
1. [Einrichten einer Route](#create-the-route) in Azure Digital Twins zum Senden von Ereignissen zur Änderung von Zwillingseigenschaften an den Endpunkt
1. [Einrichten einer Azure-Funktion](#connect-the-azure-function), die am Event Grid-Thema am Endpunkt lauscht, die an den Endpunkt gesendeten Ereignisse zur Änderung von Zwillingseigenschaften empfängt und andere Zwillinge im Diagramm entsprechend aktualisiert.

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

### <a name="connect-the-azure-function"></a>Herstellen einer Verbindung mit der Azure-Funktion

Abonnieren Sie als Nächstes die Azure-Funktion *ProcessDTRoutedData* für das von Ihnen erstellte Event Grid-Thema. Dies ermöglicht den Fluss der Telemetriedaten vom Zwilling thermostat67 über das Event Grid-Thema an die Funktion, mit der in Azure Digital Twins der Zwilling room21 dann entsprechend aktualisiert wird.

Dazu erstellen Sie ein **Event Grid-Abonnement**, das Daten aus dem zuvor erstellten **Event Grid-Thema** an Ihre Azure-Funktion *ProcessDTRoutedData* sendet.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Event Grid-Thema, indem Sie in der oberen Suchleiste nach dessen Namen suchen. Klicken Sie auf *+ Ereignisabonnement*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Screenshot des Azure-Portals mit der Vorgehensweise beim Erstellen eines Event Grid-Abonnements":::

Die Schritte zum Erstellen dieses Ereignisabonnements ähneln den Schritten beim Abonnieren der ersten Azure-Funktion für IoT Hub weiter oben in diesem Tutorial. Dieses Mal müssen Sie nicht *Gerätetelemetrie* als Ereignistyp angeben, auf den gelauscht wird, und Sie verwenden für die Verbindungsherstellung eine andere Azure-Funktion.

Füllen Sie auf der Seite *Ereignisabonnement erstellen* die Felder wie folgt aus (standardmäßig ausgefüllte Felder werden nicht angegeben):
* *DETAILS ZUM EREIGNISABONNEMENT* > **Name**: Geben Sie Ihrem Ereignisabonnement einen Namen.
* *ENDPUNKTDETAILS* > **Endpunkttyp**: Wählen Sie im Menü die Option *Azure-Funktion* aus.
* *ENDPUNKTDETAILS* > **Endpunkt**: Wählen Sie den Link *Endpunkt auswählen* aus. Dadurch wird das Fenster *Azure-Funktion auswählen* geöffnet:
    - Füllen Sie die Felder **Abonnement**, **Ressourcengruppe**, **Funktions-App** und **Funktion** (*ProcessDTRoutedData*) aus. Einige Werte werden unter Umständen automatisch ausgefüllt, nachdem Sie das Abonnement ausgewählt haben.
    - Klicken Sie auf **Auswahl bestätigen**.

Wählen Sie auf der Seite *Ereignisabonnement erstellen* die Option **Erstellen** aus.

## <a name="run-the-simulation-and-see-the-results"></a>Ausführen der Simulation und Anzeigen der Ergebnisse

Nun sollten Ereignisse vom simulierten Gerät an Azure Digital Twins und über den Azure Digital Twins-Graphen übertragen werden können, um Zwillinge nach Bedarf zu aktualisieren. In diesem Abschnitt führen Sie den Gerätesimulator erneut aus, um den von Ihnen eingerichteten vollständigen Ereignisfluss zu starten, und fragen Azure Digital Twins ab, um die Liveergebnisse anzuzeigen.

Navigieren Sie zu Ihrem Visual Studio-Fenster, in dem das Projekt _**DeviceSimulator**_ geöffnet ist, und führen Sie das Projekt aus.

Wie auch bei der Ausführung des Gerätesimulators wird ein Konsolenfenster geöffnet, in dem Meldungen mit simulierten Telemetriedaten zur Temperatur angezeigt werden. Diese Ereignisse durchlaufen den Datenfluss, den Sie weiter oben für die Aktualisierung des Zwillings thermostat67 eingerichtet haben, und dann den Datenfluss, den Sie anschließend für die Aktualisierung des passenden Zwillings room21 eingerichtet haben.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Screenshot: Konsolenausgabe des Gerätesimulators mit gesendeten Telemetriedaten zur Temperatur":::

Sie müssen in dieser Konsole keine weiteren Schritte ausführen, aber Sie sollten sie während der nächsten Schritte geöffnet lassen.

Navigieren Sie zum Anzeigen der Daten aufseiten von Azure Digital Twins zu Ihrem Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, und führen Sie das Projekt aus.

Führen Sie im geöffneten Konsolenfenster des Projekts den folgenden Befehl aus, um die Temperaturen abzurufen, die vom digitalen Zwilling „thermostat67“ **und** vom digitalen Zwilling „room21“ gemeldet werden.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Sie sollten verfolgen können, wie die Liveaktualisierung der Temperaturen *von Ihrer Azure Digital Twins-Instanz* alle zwei Sekunden in der Konsole protokolliert wird. Beachten Sie, dass die Temperatur für room21 aktualisiert wird, damit sie mit den Aktualisierungen für thermostat67 übereinstimmt.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Screenshot: Konsolenausgabe mit einem Protokoll der Temperaturmeldungen für ein Thermostat und einen Raum":::

Nachdem Sie überprüft haben, ob die Liveprotokollierung der Temperatur von Ihrer Instanz richtig funktioniert, können Sie die Ausführung beider Projekte beenden. Da das Tutorial nun abgeschlossen ist, können Sie auch die Visual Studio-Fenster schließen.

## <a name="review"></a>Überprüfung

Hier wird das in diesem Tutorial erstellte Szenario noch einmal zusammengefasst.

1. Eine Azure Digital Twins-Instanz verfügt über eine digitale Darstellung einer Etage, eines Raums und eines Thermostats (unten im Diagramm **Abschnitt A**).
2. Simulierte Gerätetelemetriedaten werden an die IoT Hub-Instanz gesendet, auf der die Azure-Funktion *ProcessHubToDTEvents* auf Telemetrieereignisse lauscht. Die Azure-Funktion *ProcessHubToDTEvents* nutzt die Informationen in diesen Ereignissen zum Festlegen der *Temperature*-Eigenschaft für thermostat67 (**Pfeil B** im Diagramm).
3. Ereignisse zu Änderungen von Eigenschaften in Azure Digital Twins werden an ein Event Grid-Thema geleitet, unter dem die Azure-Funktion *ProcessDTRoutedData* auf Ereignisse lauscht. Die Azure-Funktion *ProcessDTRoutedData* nutzt die Informationen in diesen Ereignissen, um die *Temperature*-Eigenschaft für room21 festzulegen (**Pfeil C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Diagramm des vollständigen Gebäudeszenarios, das die Daten zeigt, die von einem Gerät über verschiedene Azure-Dienste in und aus Azure Digital Twins fließen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials können Sie die zu entfernenden Ressourcen auswählen, abhängig davon, wie Sie weiter vorgehen möchten.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Wenn Sie die in diesem Artikel eingerichtete Azure Digital Twins-Instanz weiterhin verwenden, aber einige oder alle ihrer Modelle, Zwillinge und Beziehungen entfernen möchten**, können Sie die CLI-Befehle vom Typ [az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) in einem [Azure Cloud Shell](https://shell.azure.com)-Fenster verwenden, um die gewünschten Elemente zu löschen.

    Mit dieser Option werden keine der anderen in diesem Tutorial erstellten Azure-Ressourcen (IoT Hub, Azure Functions-App usw.) entfernt. Sie können diese einzeln mithilfe der entsprechenden [dt-Befehle](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true) für jeden Ressourcentyp löschen.

Löschen Sie ggf. auch den Projektordner von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein End-to-End-Szenario erstellt, mit dem veranschaulicht wird, wie eine Azure Digital Twins-Instanz auf Livedaten von Geräten basiert.

Sehen Sie sich als Nächstes die Konzeptdokumentation an, um mehr über die Elemente zu erfahren, mit denen Sie in diesem Tutorial gearbeitet haben:

> [!div class="nextstepaction"]
> [Benutzerdefinierte Modelle](concepts-models.md)