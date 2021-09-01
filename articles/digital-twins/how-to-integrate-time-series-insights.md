---
title: Integration in Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Ereignisrouten von Azure Digital Twins in Azure Time Series Insights einrichten.
author: baanders
ms.author: baanders
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3dc79c2307365a81c6994a1e692f86ec1faa0511
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122353660"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrieren von Azure Digital Twins in Azure Time Series Insights

In diesem Artikel erfahren Sie, wie Sie Azure Digital Twins in [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) integrieren.

Mithilfe der in diesem Artikel beschriebenen Lösung können Sie Verlaufsdaten zu ihrer IoT-Lösung erfassen und analysieren. Azure Digital Twins eignet sich hervorragend dazu, Daten in Time Series Insights einzuspeisen, da Sie mehrere Datenströme zu korrelieren und Ihre Informationen standardisieren können, bevor sie an Time Series Insights gesendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine Beziehung mit Time Series Insights einrichten können, müssen Sie die folgenden Ressourcen einrichten:
* Eine **Azure Digital Twins-Instanz**. Anweisungen dazu finden Sie unter [Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung](./how-to-set-up-instance-portal.md).
* Ein **Modell und einen Zwilling in der Azure Digital Twins-Instanz**. Sie müssen die Informationen des Zwillings einige Male aktualisieren, damit die Daten in Time Series Insights nachverfolgt werden können. Anleitungen dazu finden Sie im Abschnitt [Hinzufügen eines Modells und eines Zwillings](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) im Artikel *Erfassen der Telemetriedaten von IoT Hub*.

> [!TIP]
> In diesem Artikel werden die geänderten Werte für digitale Zwillinge, die in Time Series Insights angezeigt werden, der Einfachheit halber manuell aktualisiert. Wenn Sie diesen Artikel jedoch mit simulierten Livedaten durcharbeiten möchten, können Sie eine Azure-Funktion einrichten, die digitale Zwillinge basierend auf IoT-Telemetrieereignissen von einem simulierten Gerät aktualisiert. Anweisungen hierzu finden Sie unter [Erfassen von IoT Hub-Telemetriedaten in Azure Digital Twins](how-to-ingest-iot-hub-data.md). In diesem Artikel werden u. a. die letzten Schritte zum Ausführen des Gerätesimulators und zum Überprüfen der ordnungsgemäßen Funktion des Datenflusses erläutert.
>
> Suchen Sie später nach einem weiteren TIPP, um zu sehen, wo Sie mit der Ausführung des Gerätesimulators beginnen, und lassen Sie ihre Azure-Funktionen die Zwillinge automatisch aktualisieren, anstatt manuelle Befehle zum Aktualisieren digitaler Zwillinge zu senden.


## <a name="solution-architecture"></a>Lösungsarchitektur

Sie fügen Time Series Insights über den folgenden Pfad an Azure Digital Twins an.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagramm: Azure-Dienste in einem End-to-End-Szenario, wobei Time Series Insights hervorgehoben ist" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Erstellen eines Event Hub-Namespace

Bevor Sie die Event Hubs erstellen, erstellen Sie zuerst einen Event Hub-Namespace, der Ereignisse von Ihrer Azure Digital Twins-Instanz empfängt. Sie können die nachstehenden Anweisungen für die Azure CLI oder das Azure-Portal verwenden, indem Sie die Schritte unter [Erstellen eines Event Hub mit dem Azure-Portal](../event-hubs/event-hubs-create.md) ausführen. Informationen zu Regionen, die Event Hubs unterstützen, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-Event-Hubs-namespace> --resource-group <your-resource-group> --location <region>
```

> [!TIP]
> Wenn Sie eine Fehlermeldung mit dem Hinweis `BadRequest: The specified service namespace is invalid.` erhalten, stellen Sie sicher, dass der für Ihren Namespace ausgewählte Name die in diesem Referenzdokument beschriebenen Benennungsanforderungen erfüllt: [Erstellen eines Namespace](/rest/api/servicebus/create-namespace).

Sie verwenden diesen Event Hubs-Namespace, um die beiden für diesen Artikel erforderlichen Event Hubs zu speichern:

  1. **Zwillingshub:** Event Hub zum Empfangen von Änderungsereignissen für Zwillinge
  2. **Zeitreihenhub:** Event Hub zum Streamen von Ereignissen an Time Series Insights

In den nächsten Abschnitten erfahren Sie Schritt für Schritt, wie Sie diese Hubs in Ihrem Event Hub-Namespace erstellen und konfigurieren.

## <a name="create-twins-hub"></a>Erstellen eines Zwillingshubs

Der erste Event Hub, den Sie in diesem Artikel erstellen, ist der **Zwillingshub**. Dieser Event Hub empfängt Zwillingsänderungsereignisse von Azure Digital Twins.
Führen Sie zum Einrichten des Zwillingshubs die folgenden Schritte in diesem Abschnitt aus:

1. Erstellen des Zwillingshubs
2. Erstellen einer Autorisierungsregel zum Steuern der Berechtigungen für den Hub
3. Erstellen eines Endpunkts in Azure Digital Twins, der die Autorisierungsregel für den Zugriff auf den Hub verwendet
4. Erstellen einer Route in Azure Digital Twins, die das Zwillingsaktualisierungsereignis an den Endpunkt und den Hub für verbundene Zwillinge sendet
5. Abrufen der Verbindungszeichenfolge für den Zwillingshub

Erstellen Sie den **Zwillingshub** mit dem folgenden CLI-Befehl. Geben Sie einen Namen für Ihren Zwillingshub an.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Erstellen einer Autorisierungsregel für den Zwillingshub

Erstellen Sie eine [Autorisierungsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create) mit Sende- und Empfangsberechtigungen. Geben Sie einen Namen für die Regel an.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Erstellen eines Endpunkts für den Zwillingshub

Erstellen Sie einen Azure Digital Twins-[Endpunkt](concepts-route-events.md#create-an-endpoint), der Ihren Event Hub mit Ihrer Azure Digital Twins-Instanz verknüpft. Geben Sie einen Namen für den Endpunkt Ihres Zwillingshubs an.

```azurecli-interactive
az dt endpoint create eventhub --dt-name <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-Event-Hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Erstellen einer Ereignisroute für den Zwillingshub

Azure Digital Twins-Instanzen können [Ereignisse zur Aktualisierung von Zwillingen](./concepts-event-notifications.md) ausgeben, wenn der Zustand eines Zwillings aktualisiert wird. In diesem Abschnitt erstellen Sie eine Azure Digital Twins-**Ereignisroute**, die diese Aktualisierungsereignisse zur weiteren Verarbeitung an den Zwillingshub weiterleitet.

Erstellen Sie eine [Route](concepts-route-events.md#create-an-event-route) in Azure Digital Twins, um Ereignisse zur Aktualisierung von Zwillingen an Ihren obigen Endpunkt zu senden. Der Filter in dieser Route erlaubt nur das Weitergeben von Nachrichten über Zwillingsaktualisierungen an Ihren Endpunkt. Geben Sie einen Namen für die Zwillingshub-Ereignisroute an.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Abrufen der Verbindungszeichenfolge für den Zwillingshub

Rufen Sie die [Event Hubs-Verbindungszeichenfolge der Zwillinge](../event-hubs/event-hubs-get-connection-string.md) mithilfe der Autorisierungsregeln ab, die Sie zuvor für den Zwillingshub erstellt haben.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Notieren Sie sich den Wert **primaryConnectionString** aus dem Ergebnis, um später in diesem Artikel die App-Einstellung für den Zwillingshub zu konfigurieren.

## <a name="create-time-series-hub"></a>Erstellen eines Zeitreihenhubs

Der zweite Event Hub, den Sie in diesem Artikel erstellen, ist der **Zeitreihenhub**. Dies ist ein Event Hub, der die Azure Digital Twins-Ereignisse an Time Series Insights streamt.
Führen Sie zum Einrichten des Zeitreihenhubs die folgenden Schritte aus:

1. Erstellen des Zeitreihenhubs
2. Erstellen einer Autorisierungsregel zum Steuern der Berechtigungen für den Hub
3. Abrufen der Verbindungszeichenfolge für den Zeitreihenhub

Wenn Sie später die Time Series Insights-Instanz erstellen, verbinden Sie diesen Zeitreihenhub als Ereignisquelle für die Time Series Insights-Instanz.

Erstellen Sie den **Zeitreihenhub** mit dem folgenden Befehl. Geben Sie einen Namen für den Zeitreihenhub an.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Erstellen einer Autorisierungsregel für den Zeitreihenhub

Erstellen Sie eine [Autorisierungsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create) mit Sende- und Empfangsberechtigungen. Geben Sie einen Namen für die Autorisierungsregel für den Zeitreihenhub an.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Abrufen der Verbindungszeichenfolge für den Zeitreihenhub

Rufen Sie die [Verbindungszeichenfolge für den Zeitreihenhub](../event-hubs/event-hubs-get-connection-string.md) mithilfe der Autorisierungsregeln ab, die Sie zuvor für den Zeitreihenhub erstellt haben:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Notieren Sie sich den Wert **primaryConnectionString** aus dem Ergebnis, um später in diesem Artikel die App-Einstellung für den Zeitreihenhub zu konfigurieren.

Notieren Sie sich außerdem die folgenden Werte, um sie später zum Erstellen einer Time Series Insights-Instanz zu verwenden:
* Event Hub-Namespace
* Name des Zeitreihenhubs
* Zeitreihenhub-Authentifizierungsregel

## <a name="create-a-function"></a>Erstellen einer Funktion

In diesem Abschnitt erstellen Sie eine Azure-Funktion, die Ereignisse zur Aktualisierung von Zwillingen von ihrer ursprünglichen Form als JSON-Patchdokumente in JSON-Objekte konvertiert, die nur aktualisierte und hinzugefügte Werte von Ihren Zwillingen enthalten.

1. Erstellen Sie zunächst in Visual Studio ein neues Funktions-App-Projekt. Anweisungen dazu finden Sie unter [Entwickeln von Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Erstellen Sie eine neue Azure-Funktion namens *ProcessDTUpdatetoTSI.cs*, um Gerätetelemetrieereignisse auf die Time Series Insights-Instanz zu aktualisieren. Als Funktionstyp wird **Event Hub-Trigger** verwendet.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Screenshot: Visual Studio zum Erstellen einer neuen Azure-Funktion vom Typ „Event Hub-Trigger“":::

3. Fügen Sie Ihrem Projekt dann die folgenden Pakete hinzu:
    * [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

4. Ersetzen Sie den Code in der Datei *ProcessDTUpdatetoTSI.cs* durch den folgenden Code:

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

    Speichern Sie Ihren Funktionscode.

5. Veröffentlichen Sie das Projekt mit der Funktion *ProcessDTUpdatetoTSI.cs* in einer Funktions-App in Azure. Anweisungen dazu finden Sie unter [Entwickeln Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

Speichern Sie den Namen der Funktions-App, um sie später zum Konfigurieren der App-Einstellungen für die beiden Event Hubs zu verwenden.

### <a name="configure-the-function-app"></a>Konfigurieren der Funktions-App

**Weisen Sie dann eine Zugriffsrolle** für die Funktion zu, und **konfigurieren Sie die Anwendungseinstellungen**, damit sie auf Ihre Ressourcen zugreifen kann.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

Fügen Sie dann Umgebungsvariablen in den Einstellungen der Funktions-App hinzu, die ihr den Zugriff auf den **Zwillingshub** und den **Zeitreihenhub** ermöglichen.

Verwenden Sie den zuvor gespeicherten Zwillingshubwert für **primaryConnectionString**, um in Ihrer Funktions-App eine App-Einstellung zu erstellen, die die Zwillingshub-Verbindungszeichenfolge enthält:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

Verwenden Sie den zuvor gespeicherten Zeitreihenhubwert für **primaryConnectionString**, um in Ihrer Funktions-App eine App-Einstellung zu erstellen, die die Zeitreihenhub-Verbindungszeichenfolge enthält:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Erstellen und Verbinden einer Time Series Insights-Instanz

In diesem Abschnitt richten Sie eine Time Series Insights-Instanz zum Empfangen von Daten von Ihrem Zeitreihenhub ein. Weitere Informationen zu diesem Prozess finden Sie unter [Einrichten einer Azure Time Series Insights Gen2-Umgebung](../time-series-insights/tutorial-set-up-environment.md). Führen Sie die folgenden Schritte aus, um eine Time Series Insights-Umgebung zu erstellen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach *Time Series Insights-Umgebungen*, und wählen Sie die Schaltfläche **Erstellen** aus. Wählen Sie die folgenden Optionen aus, um die Zeitreihenumgebung zu erstellen:

    * **Abonnement**: Wählen Sie Ihr Abonnement aus.
        - **Ressourcengruppe**: Wählen Sie Ihre Ressourcengruppe aus.
    * **Umgebungsname**: Geben Sie einen Namen für Ihre Zeitreihenumgebung an.
    * **Standort**: Wählen Sie einen Standort aus.
    * **Tarif**: Wählen Sie den Tarif **Gen2(L1)** aus.
    * **Eigenschaftenname**: Geben Sie **$dtId** ein. (Weitere Informationen zur Auswahl eines ID-Werts finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](../time-series-insights/how-to-select-tsid.md).)
    * **Speicherkontoname**: Geben Sie einen Speicherkontonamen an.
    * **Warm Storage aktivieren**: Lassen Sie dieses Feld auf *Ja* festgelegt.

    Sie können für andere Eigenschaften auf dieser Seite die Standardwerte übernehmen. Klicken Sie auf die Schaltfläche **Weiter: Ereignisquelle >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie die Time Series Insights-Umgebung erstellt wird (Teil 1 von 3)." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie die Time Series Insights-Umgebung erstellt wird (Teil 2 von 3)." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Wählen Sie auf der Registerkarte *Ereignisquelle* die folgenden Felder aus:

   * **Ereignisquelle erstellen?** : Wählen Sie *Ja* aus.
   * **Quelltyp**: Wählen Sie *Event Hub* aus.
   * **Name**: Geben Sie einen Namen für Ihre Ereignisquelle an.
   * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus.
   * **Event Hub-Namespace**: Wählen Sie den Namespace aus, den Sie weiter oben in diesem Artikel erstellt haben.
   * **Event Hub-Name**: Wählen Sie den Namen des **Zeitreihenhubs** aus, den Sie weiter oben in diesem Artikel erstellt haben.
   * **Name der Event Hub-Zugriffsrichtlinie**: Wählen Sie die **Zeitreihenhub-Authentifizierungsregel** aus, die Sie weiter oben in diesem Artikel erstellt haben.
   * **Event Hub-Consumergruppe**: Wählen Sie *Neu* aus, und geben Sie einen Namen für Ihre Event Hub-Consumergruppe an. Klicken Sie anschließend auf *Hinzufügen*.
   * **Eigenschaftenname**: Lassen Sie dieses Feld leer.
    
    Wählen Sie die Schaltfläche **Überprüfen + erstellen** aus, um alle Details zu überprüfen. Wählen Sie dann erneut die Schaltfläche **Überprüfen + erstellen** aus, um die Zeitreihenumgebung zu erstellen.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie die Time Series Insights-Umgebung erstellt wird (Teil 3 von 3)." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Senden von IoT-Daten an Azure Digital Twins

Um mit dem Senden von Daten an Time Series Insights zu beginnen, müssen Sie zunächst die Eigenschaften des digitalen Zwillings in Azure Digital Twins mit sich ändernden Datenwerten aktualisieren.

Verwenden Sie den CLI-Befehl [az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update), um eine Eigenschaft des Zwillings zu aktualisieren, den Sie im Abschnitt [Voraussetzungen](#prerequisites) hinzugefügt haben. Wenn Sie die Anweisungen zum Erstellen von Zwillingen aus [Erfassen von Telemetriedaten aus IoT Hub](how-to-ingest-iot-hub-data.md) verwendet haben, können Sie den folgenden Befehl in der lokalen CLI oder im **bash**-Terminal von Cloud Shell verwenden, um die Temperatureigenschaft auf dem Zwilling „thermostat67“ zu aktualisieren.

```azurecli-interactive
az dt twin update --dt-name <your-Azure-Digital-Twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Wiederholen Sie den Befehl mindestens vier weitere Male mit unterschiedlichen Eigenschaftswerten**, um mehrere Datenpunkte zu erstellen, die später in der Time Series Insights-Umgebung beobachtet werden können.

> [!TIP]
> Wenn Sie diesen Artikel mit simulierten Livedaten abschließen möchten, anstatt die Werte des digitalen Zwillings manuell zu aktualisieren, stellen Sie zunächst sicher, dass Sie den Tipp aus dem Abschnitt [Voraussetzungen](#prerequisites) zum Einrichten einer Azure-Funktion abgeschlossen haben, die Zwillinge von einem simulierten Gerät aktualisiert.
Danach können Sie das Gerät nun ausführen, um mit dem Senden simulierter Daten und dem Aktualisieren Ihres digitalen Zwillings über diesen Datenfluss zu beginnen.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisieren Ihrer Daten in Time Series Insights

Nun sollten Daten in Ihre Time Series Insights-Instanz übertragen werden, die zur Analyse bereit sind. Führen Sie die folgenden Schritte aus, um die eingehenden Daten zu untersuchen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Namen Ihrer Zeitreihenumgebung, den Sie zuvor erstellt haben. Wählen Sie in den Menüoptionen auf der linken Seite *Übersicht* aus, um die *Time Series Insights-Explorer-URL* anzuzeigen. Wählen Sie die URL aus, um die Temperaturänderungen anzuzeigen, die sich in der Time Series Insights-Umgebung widerspiegeln.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Screenshot: Time Series Insights-Explorer-URL im Azure-Portal auf der Registerkarte „Übersicht“ der Time Series Insights-Umgebung." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Im Explorer werden die Zwillinge in der Azure Digital Twins-Instanz auf der linken Seite angezeigt. Wählen Sie den Zwilling aus, für den Sie die Eigenschaften bearbeitet haben, wählen Sie die geänderte Eigenschaft aus, und wählen Sie dann **Hinzufügen** aus.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Screenshot des Time Series Insights-Explorers mit den Schritten zur Auswahl von „thermostat67“, Auswahl der Eigenschaft „temperature“ und Hervorhebung der Auswahl von „add“." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Nun sollten nun die von Ihnen vorgenommenen Eigenschaftenänderungen im Diagramm angezeigt werden, wie unten dargestellt. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Screenshot des Time Series Insights-Explorers mit den anfänglichen Temperaturdaten, die eine Linie mit zufälligen Werten zwischen 68 und 85 zeigen." lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Nach einer viel längeren Ausführung der Simulation sieht die Visualisierung etwa wie folgt aus:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Screenshot: Time Series Insights-Explorer, in dem die Temperaturdaten für jeden Zwilling in drei parallelen Linien unterschiedlicher Farben dargestellt werden." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Nächste Schritte

Die digitalen Zwillinge werden standardmäßig als flache Hierarchie in Time Series Insights gespeichert, können jedoch für die Organisation mit Modellinformationen und einer Hierarchie mit mehreren Ebenen erweitert werden. Weitere Informationen zu diesem Vorgang finden Sie im 

* [Definieren und Anwenden eines Modells](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Sie können eine benutzerdefinierte Logik schreiben, um diese Informationen mithilfe der bereits in Azure Digital Twins gespeicherten Modell- und Diagrammdaten automatisch bereitzustellen. Weitere Informationen zum Verwalten, Aktualisieren und Abrufen von Informationen aus dem Zwillingsgraphen finden Sie in den folgenden Referenzen:

* [Verwalten eines digitalen Zwillings](./how-to-manage-twin.md)
* [Abfragen des Zwillingsgraphen](./how-to-query-graph.md)