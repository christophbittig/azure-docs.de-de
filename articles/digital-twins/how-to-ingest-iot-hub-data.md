---
title: Erfassen von Telemetriedaten aus IoT Hub
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Gerätetelemetrienachrichten aus Azure IoT Hub in digitalen Zwillingen in einer Instanz von Azure Digital Twins erfassen.
author: baanders
ms.author: baanders
ms.date: 10/28/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b10a87312fd7b27c22bbfa56a928a0357d4698e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500925"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Erfassen von IoT Hub Telemetriedaten in Azure Digital Twins

Dieser Leitfaden führt Sie durch das Schreiben einer Funktion, die Telemetriedaten aus IoT Hub erfassen und an eine Instanz von Azure Digital Twins senden kann.

Azure Digital Twins wird mit Daten von IoT-Geräten und anderen Quellen gesteuert. Eine gängige Quelle für Gerätedaten zur Verwendung in Azure Digital Twins ist [IoT Hub](../iot-hub/about-iot-hub.md).

Der Prozess der Erfassung von Daten in Azure Digital Twins besteht darin, eine externe Computeressource einzurichten, z. B. eine Funktion, die mithilfe von [Azure Functions](../azure-functions/functions-overview.md) erstellt wird. Die Funktion empfängt die Daten und verwendet [APIs von Azure Digital Twins](/rest/api/digital-twins/dataplane/twins), um entsprechend für [digitale Zwillinge](concepts-twins-graph.md) Eigenschaften festzulegen oder Telemetrieereignisse auszulösen. 

In dieser Schrittanleitung wird erläutert, wie Sie eine Funktion schreiben, die Telemetriedaten von IoT Hub erfassen kann.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Beispiel fortfahren können, müssen Sie die folgenden Ressourcen als Voraussetzungen einrichten:
* **Einen IoT-Hub**. Anweisungen finden Sie im Abschnitt *Erstellen eines IoT-Hubs* in diesem [IoT Hub-Schnellstart](../iot-hub/quickstart-send-telemetry-cli.md).
* **Eine Azure Digital Twins-Instanz** zum Empfangen Ihrer Gerätetelemetrie. Anweisungen dazu finden Sie unter [Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung](./how-to-set-up-instance-portal.md).

In diesem Artikel wird auch **Visual Studio** verwendet. Sie können die neueste Version unter [Visual Studio-Downloads](https://visualstudio.microsoft.com/downloads/) herunterladen.

## <a name="example-telemetry-scenario"></a>Beispieltelemetrieszenario

In dieser Schrittanleitung wird erläutert, wie Sie Nachrichten von IoT Hub mithilfe einer Funktion in Azure an Azure Digital Twins senden. Es gibt viele mögliche Konfigurationen und Abgleichstrategien, die Sie zum Senden von Nachrichten nutzen können, aber das Beispiel für diesen Artikel enthält die folgenden Teile:
* Ein Thermostatgerät in IoT Hub mit einer bekannten Geräte-ID
* Ein digitaler Zwilling als Darstellung des Geräts mit einer übereinstimmenden ID

> [!NOTE]
> In diesem Beispiel wird eine einfache ID-Übereinstimmung zwischen der Geräte-ID und der ID eines zugehörigen digitalen Zwillings verwendet. Es können jedoch auch anspruchsvollere Zuordnungen vom Gerät zu dessen Zwilling bereitgestellt werden, z. B. eine Zuordnungstabelle.

Immer wenn ein Temperaturtelemetrieereignis vom Thermostat gesendet wird, verarbeitet eine Funktion die Telemetriedaten, und die Eigenschaft *temperature* des digitalen Zwillings sollte aktualisiert werden. Dieses Szenario ist nachstehend in einem Diagramm dargestellt:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramm eines IoT Hub-Geräts, das Temperaturtelemetriedaten an eine Funktion in Azure sendet, welche eine Temperatureigenschaft in einem Zwilling in Azure Digital Twins aktualisiert." border="false":::

## <a name="add-a-model-and-twin"></a>Hinzufügen eines Modells und eines Zwillings

In diesem Abschnitt richten Sie einen [digitalen Zwilling](concepts-twins-graph.md) in Azure Digital Twins ein, der das Thermostat darstellt und mit Informationen aus IoT Hub aktualisiert wird.

Wenn Sie den Zwilling des Thermostattyps erstellen möchten, müssen Sie zunächst das [Thermostatmodell](concepts-models.md) in Ihre Instanz hochladen, das die Eigenschaften eines Thermostats beschreibt und später zum Erstellen des Zwillings verwendet wird.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Anschließend müssen Sie **einen einzigen Zwilling mit diesem Modell erstellen**. Verwenden Sie den folgenden Befehl zum Erstellen eines Thermostatzwillings mit dem Namen thermostat67 und zum Festlegen von „0,0“ als anfänglichen Temperaturwert.

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

Wenn der Zwilling erfolgreich erstellt wurde, sollte die CLI-Ausgabe des Befehls in etwa wie folgt aussehen:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Erstellen einer Funktion

In diesem Abschnitt erstellen Sie eine Azure-Funktion für den Zugriff auf Azure Digital Twins und aktualisieren Zwillinge auf der Grundlage von IoT-Telemetrieereignissen, die sie empfängt. Führen Sie die folgenden Schritte aus, um die Funktion zu erstellen und zu veröffentlichen.

1. Erstellen Sie zunächst in Visual Studio ein neues Funktions-App-Projekt. Anweisungen dazu finden Sie unter [Entwickeln von Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu:
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. Benennen Sie die Beispielfunktion *Function1.cs*, die von Visual Studio generiert wurde, in *IoTHubtoTwins.cs* um. Ersetzen Sie den Code in der Datei durch den folgenden Code:

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    Speichern Sie Ihren Funktionscode.

4. Veröffentlichen Sie das Projekt mit der Funktion *IoTHubtoTwins.cs* in einer Funktions-App in Azure. Anweisungen dazu finden Sie unter [Entwickeln von Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

Damit Ihre Funktions-App auf Azure Digital Twins zugreifen kann, muss sie über eine systemseitig verwaltete Identität mit Zugriffsberechtigungen für Ihre Azure Digital Twins-Instanz verfügen. Dies wird im nächsten Schritt eingerichtet.

### <a name="configure-the-function-app"></a>Konfigurieren der Funktions-App

**Weisen Sie dann eine Zugriffsrolle** für die Funktion zu, und **Konfigurieren Sie die Anwendungseinstellungen**, damit sie auf Ihre Azure Digital Twins-Instanz zugreifen kann.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>Verbinden Ihrer Funktion mit IoT Hub

In diesem Abschnitt richten Sie Ihre Funktion als Ereignisziel für die IoT Hub-Gerätedaten ein. Wenn Sie Ihre Funktion so einrichten, wird sichergestellt, dass die Daten aus dem Thermostatgerät in IoT Hub zur Verarbeitung an die Azure-Funktion gesendet werden.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer IoT Hub-Instanz, die Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben. Erstellen Sie unter **Ereignisse** ein Abonnement für Ihre Funktion.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot des Azure-Portals mit „Hinzufügen eines Ereignisabonnements“":::

Füllen Sie auf der Seite **Ereignisabonnement erstellen** die Felder wie folgt aus:
  1. Wählen Sie für **Name** einen beliebigen Namen für das Ereignisabonnement aus.
  2. Wählen Sie als **Ereignisschema** die Option _Event Grid-Schema_ aus.
  3. Wählen Sie unter **Name des Systemthemas** den gewünschten Namen aus.
  1. Aktivieren Sie unter **Nach Ereignistypen filtern** das Kontrollkästchen _Gerätetelemetrie_, und deaktivieren Sie andere Ereignistypen.
  1. Wählen Sie für **Endpunkttyp** die Option _Azure-Funktion_ aus.
  1. Verwenden Sie für **Endpunkt** den Link _Endpunkt auswählen_, um auszuwählen, welche Azure-Funktion für den Endpunkt verwendet werden soll.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Screenshot des Azure-Portals zum Erstellen der Details zum Ereignisabonnement.":::

Überprüfen Sie auf der daraufhin geöffneten Seite _Azure-Funktion auswählen_ die folgenden Details oder ergänzen Sie sie:
 1. **Abonnement**: Ihr Azure-Abonnement.
 2. **Ressourcengruppe**: Ihre Ressourcengruppe
 3. **Funktions-App**: Name Ihrer Funktions-App
 4. **Slot**: _Produktion_
 5. **Funktion**: Wählen Sie die zuvor verwendete Funktion *IoTHubtoTwins* aus dem Dropdownmenü aus.

Wählen Sie die Schaltfläche _Auswahl bestätigen_ aus, um Ihre Details zu speichern.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Screenshot des Azure-Portals zum Auswählen einer Funktion":::

Wählen Sie die Schaltfläche _Erstellen_ aus, um ein Ereignisabonnement zu erstellen.

## <a name="send-simulated-iot-data"></a>Senden von simulierten IoT-Daten

Verwenden Sie zum Testen Ihrer neuen Eingangsfunktion den Gerätesimulator aus [Verbinden einer End-to-End-Lösung](./tutorial-end-to-end.md). Grundlage dieses Tutorials ist ein [in C# geschriebenes End-to-End-Beispielprojekt für Azure Digital Twins](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Sie verwenden in diesem Repository das Projekt **DeviceSimulator**.

Führen Sie im End-to-End-Tutorial die folgenden Schritte aus:
1. [Registrieren des simulierten Geräts für IoT Hub](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [Konfigurieren und Ausführen der Simulation](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Überprüfen Ihrer Ergebnisse

Während der Ausführung des vorstehenden Gerätesimulators ändert sich der Temperaturwert Ihres digitalen Zwillings. Führen Sie in der Azure CLI den folgenden Befehl zum Anzeigen des Temperaturwerts aus.

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

Die Ausgabe sollte einen Temperaturwert wie diesen enthalten:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

Führen Sie zum Anzeigen der Wertänderung den vorstehenden Abfragebefehl wiederholt aus.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über eingehende und ausgehende Daten mit Azure Digital Twins:
* [Dateneingang und -ausgang](concepts-data-ingress-egress.md)
