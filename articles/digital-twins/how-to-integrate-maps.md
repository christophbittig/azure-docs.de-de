---
title: Integration in Azure Maps
titleSuffix: Azure Digital Twins
description: In diesem Artikel erfahren Sie, wie Sie mit Azure Functions eine Funktion erstellen, die den Zwillingsgraphen und die Azure Digital Twins-Benachrichtigungen verwenden kann, um einen Azure Maps-Gebäudeplan zu aktualisieren.
author: baanders
ms.author: baanders
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 494e4b048fc33a0cd7fcfc6c354a7f757298ebef
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223477"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Verwenden von Azure Digital Twins zum Aktualisieren eines Azure Maps-Gebäudeplans

In diesem Artikel werden die Schritte erläutert, die erforderlich sind, um mithilfe von Azure Digital Twins-Daten Informationen zu aktualisieren, die in einem *Gebäudeplan* unter Verwendung von [Azure Maps](../azure-maps/about-azure-maps.md) angezeigt werden. Azure Digital Twins speichert einen Graphen der Beziehungen Ihres IoT-Geräts und leitet die Telemetrie an verschiedene Endpunkte weiter, was es zum perfekten Dienst für die Aktualisierung von Informationsüberlagerungen auf Karten macht.

Dieser Leitfaden enthält Informationen zu Folgendem:

1. Konfigurieren Ihrer Azure Digital Twins-Instanz, um Aktualisierungsereignisse des Zwillings an eine Funktion in [Azure Functions](../azure-functions/functions-overview.md) zu senden.
2. Erstellen einer Funktion zur Aktualisierung eines Featurezustandssets für Azure Maps-Gebäudepläne.
3. Speichern Ihrer Karten-ID und Featurezustandsset-ID im Azure Digital Twins-Graphen.

### <a name="prerequisites"></a>Voraussetzungen

* Befolgen Sie die Azure Digital Twins-Anleitungen unter [Verbinden einer End-to-End-Lösung](./tutorial-end-to-end.md).
    * Sie werden diesen Zwilling um einen zusätzlichen Endpunkt und eine zusätzliche Route erweitern. Sie fügen Ihrer Funktions-App auch eine weitere Funktion aus diesem Tutorial hinzu. 
* Befolgen Sie die Azure Maps-Anleitungen unter [Erstellen von Gebäudeplänen mit Azure Maps Creator](../azure-maps/tutorial-creator-indoor-maps.md), um einen Azure Maps-Gebäudeplan mit einem *Featurezustandsset* zu erstellen.
    * [Featurezustandssets](../azure-maps/creator-indoor-maps.md#feature-statesets) sind Sammlungen dynamischer Eigenschaften (Zustände), die Datasetfeatures wie z. B. Räumen oder Ausstattung zugewiesen sind. Im obigen Azure Maps-Tutorial speichert das Featurezustandsset den Raumstatus, den Sie auf einer Karte anzeigen werden.
    * Sie benötigen Ihre *Featurezustandsset-ID* und den Azure Maps-*Abonnementschlüssel*.

### <a name="topology"></a>Topologie

Die Abbildung unten zeigt, wo die Integrationselemente der Gebäudepläne in diesem Tutorial in ein größeres Azure Digital Twins-End-to-End-Szenario passen.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Diagramm der Azure-Dienste in einem End-to-End-Szenario, wobei die Komponente der Gebäudeplanintegration hervorgehoben wird." lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Erstellen einer Funktion zum Aktualisieren einer Karte beim Aktualisieren von Zwillingen

Zunächst erstellen Sie eine Route in Azure Digital Twins, um alle Ereignisse zur Aktualisierung der Zwillinge an ein Event Grid-Thema weiterzuleiten. Dann verwenden Sie eine Funktion, um diese Aktualisierungsnachrichten zu lesen und ein Featurezustandsset in Azure Maps zu aktualisieren. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Erstellen einer Route und eines Filters für Benachrichtigungen beim Aktualisieren von Zwillingen

Azure Digital Twins-Instanzen können Ereignisse zur Aktualisierung von Zwillingen ausgeben, wenn der Zustand eines Zwillings aktualisiert wird. Die Azure Digital Twins-Anleitungen unter [Verbinden einer End-to-End-Lösung](./tutorial-end-to-end.md), zu denen Sie oben einen Link finden, führen Sie durch ein Szenario, in dem ein Temperaturattribut, das an den Zwilling eines Raums angefügt ist, mit einem Thermometer aktualisiert wird. Sie werden diese Lösung erweitern, indem Sie Aktualisierungsbenachrichtigungen für Zwillinge abonnieren und diese Informationen zum Aktualisieren Ihrer Karten verwenden.

Dieses Muster liest direkt aus dem Raumzwilling und nicht vom IoT-Gerät, was Ihnen die Flexibilität bietet, die zugrunde liegende Datenquelle für die Temperatur zu ändern, ohne dass Sie Ihre Zuordnungslogik aktualisieren müssen. Sie können z. B. mehrere Thermometer hinzufügen oder diesen Raum so einrichten, dass ein Thermometer mit einem anderen Raum gemeinsam genutzt wird, ohne dass Sie Ihre Kartenlogik aktualisieren müssen.

1. Erstellen Sie ein Event Grid-Thema, das Ereignisse von Ihrer Azure Digital Twins-Instanz empfängt.
    ```azurecli-interactive
    az eventgrid topic create --resource-group <your-resource-group-name> --name <your-topic-name> --location <region>
    ```

2. Erstellen Sie einen Endpunkt, um Ihr Event Grid-Thema mit Azure Digital Twins zu verknüpfen.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
    ```

3. Erstellen Sie eine Route in Azure Digital Twins, um Ereignisse zur Aktualisierung von Zwillingen an Ihren Endpunkt zu senden.

    >[!NOTE]
    >Zurzeit besteht ein **bekanntes Problem** in Cloud Shell, das sich auf diese Befehlsgruppen auswirkt: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Problemlösung: Führen Sie vor Ausführung des Befehls das Cmdlet `az login` in Cloud Shell aus, oder verwenden Sie die [lokale Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) anstelle von Cloud Shell. Weitere Informationen hierzu finden Sie unter [Problembehandlung: Bekannte Probleme in Azure Digital Twins](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Erstellen einer Funktion zum Aktualisieren von Plänen

Sie erstellen eine **durch Event Grid ausgelöste Funktion** innerhalb Ihrer Funktions-App aus dem End-to-End-Tutorial ([Verbinden einer End-to-End-Lösung](./tutorial-end-to-end.md)). Diese Funktion entpackt diese Benachrichtigungen und sendet Aktualisierungen an ein Azure Maps-Featurezustandsset, um die Temperatur in einem Raum zu aktualisieren.

Referenzinformationen finden Sie im folgenden Dokument: [Azure Event Grid-Trigger für Azure Functions](../azure-functions/functions-bindings-event-grid-trigger.md).

Ersetzen Sie den Funktionscode durch folgenden Code. Er wird nur Aktualisierungen für Raumzwillinge herausfiltern, die aktualisierte Temperatur lesen und diese Informationen an Azure Maps senden.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

Sie müssen zwei Umgebungsvariablen in Ihrer Funktions-App festlegen. Eine Variable ist Ihr [primärer Azure Maps-Abonnementschlüssel](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account) und die andere ist Ihre [Azure Maps-Zustandsset-ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-function-app-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-function-app-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Anzeigen von Liveupdates auf Ihrer Karte

Führen Sie die folgenden Schritte aus, um die Liveupdate-Temperatur anzuzeigen:

1. Beginnen Sie mit dem Senden simulierter IoT-Daten, indem Sie das **DeviceSimulator**-Projekt aus Azure Digital Twins-Tutorial [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md) ausführen. Anweisungen zu diesem Prozess finden Sie im Abschnitt [Konfigurieren und Ausführen der Simulation](././tutorial-end-to-end.md#configure-and-run-the-simulation).
2. Verwenden Sie [das Modul Azure Maps Indoor](../azure-maps/how-to-use-indoor-module.md), um Ihre mit Azure Maps Creator erstellten Karten für Gebäudepläne zu rendern.
    1. Kopieren Sie den HTML-Code aus dem Abschnitt [Beispiel: Verwenden des Indoor Maps-Moduls](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) der Gebäudepläne unter [Verwenden des Indoor Maps-Moduls von Azure Maps](../azure-maps/how-to-use-indoor-module.md) in eine lokale Datei.
    1. Ersetzen Sie *subscription key*, *tilesetId* und *statesetID* in der lokalen HTML-Datei mit Ihren Werten.
    1. Öffnen Sie diese Datei in Ihrem Browser.

Beide Beispiele senden die Temperatur in einem kompatiblen Bereich, sodass die Farbe von Raum 121 etwa alle 30 Sekunden auf der Karte aktualisiert werden sollte.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Screenshot eines Büroplans, der Raum 121 in Orange zeigt.":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Speichern Ihrer Karteninformationen in Azure Digital Twins

Nachdem Sie jetzt über eine hartcodierte Lösung zur Aktualisierung Ihrer Karteninformationen verfügen, können Sie den Azure Digital Twins-Graphen verwenden, um alle Informationen zu speichern, die für die Aktualisierung Ihres Gebäudeplans erforderlich sind. Diese Informationen würden die Zustandsset-ID, die Kartenabonnement-ID und die Feature-ID jeder Karte bzw. jedes Ortes umfassen. 

Eine Lösung für dieses spezielle Beispiel würde die Aktualisierung jedes Raums der obersten Ebene mit einer Zustandsset-ID und einem Kartenabonnement-ID-Attribut sowie die Aktualisierung jedes Raums mit einer Feature-ID umfassen. Sie müssten diese Werte einmal bei der Initialisierung des Zwillingsgraphen festlegen und diese Werte dann für jedes Aktualisierungsereignis des Zwillings abfragen.

Abhängig von der Konfiguration Ihrer Topologie können diese drei Attribute auf verschiedenen Ebenen gespeichert werden, die der Granularität Ihrer Karte entsprechen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten, Aktualisieren und Abrufen von Informationen aus dem Zwillingsgraphen finden Sie in den folgenden Referenzen:

* [Verwalten digitaler Zwillinge](./how-to-manage-twin.md)
* [Abfragen des Zwillingsgraphen](./how-to-query-graph.md)
