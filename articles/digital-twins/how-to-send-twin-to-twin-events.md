---
title: Einrichten der Verarbeitung von Ereignissen zwischen Zwillingen
titleSuffix: Azure Digital Twins
description: Informationen zur Erstellung einer Funktion in Azure, um Ereignisse über den Zwillingsgraph zu verteilen.
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350948"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>Einrichten der Verarbeitung von Ereignissen zwischen Zwillingen mit Azure Functions

Ein vollständig verbundener Azure Digital Twins-Graph wird durch die Ereignisweitergabe gesteuert. Daten gelangen aus externen Quellen wie IoT Hub zu Azure Digital Twins und werden dann über den Azure Digital Twins-Graph verteilt, wobei relevante Zwillinge bei Bedarf aktualisiert werden.

Beispiel: Ein Graph repräsentiert Etagen und Räume in einem Gebäude, in dem jede Etage in mehrere Räume aufgeteilt ist. Sie können den Datenfluss zwischen den Zwillingen so einrichten, dass jedes Mal, wenn die Temperatureigenschaft eines Raumzwillings aktualisiert wird, eine neue Durchschnittstemperatur für alle Räume in derselben Etage berechnet wird, und dass die Temperatureigenschaft des Etagenzwillings aktualisiert wird, um die neue Durchschnittstemperatur aller darin enthaltenen Räume widerzuspiegeln (einschließlich des aktualisierten Raums). 

In diesem Artikel erhalten Sie Informationen darüber, wie Sie Ereignisse von einem Zwilling an einen anderen Zwilling senden, sodass Sie Zwillinge als Reaktion auf die Änderung von Eigenschaften oder Daten eines beliebigen Zwillings im Graph aktualisieren können. Zurzeit werden Updates zwischen Zwillingen verarbeitet, indem eine [Azure-Funktion](../azure-functions/functions-overview.md) eingerichtet wird. Diese Funktion überwacht Lebenszyklusereignisse der Zwillinge, die sich auf andere Bereiche des Graphs auswirken können, und nimmt an den anderen Zwillingen entsprechende Änderungen vor.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird **Visual Studio** verwendet. Sie können die neueste Version unter [Visual Studio-Downloads](https://visualstudio.microsoft.com/downloads/) herunterladen.

Um die Verarbeitung von Ereignissen zwischen Zwillingen einzurichten, benötigen Sie eine **Azure Digital Twins-Instanz**. Informationen über das Erstellen einer Azure Digital Twins-Instanz erhalten Sie unter [Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung](./how-to-set-up-instance-portal.md). Die Instanz sollte mindestens **zwei Zwillinge** enthalten, zwischen denen Sie Daten senden wollen.

Optional können Sie außerdem die [automatische Telemetriedatenerfassung über IoT Hub](how-to-ingest-iot-hub-data.md) für Ihre Zwillinge einrichten. Dies ist zwar nicht erforderlich, um Daten von einem Zwilling an einen anderen zu senden, aber es ist ein wichtiger Bestandteil einer vollständigen Lösung, bei der der Zwillingsgraph durch Livetelemetriedaten gesteuert wird.

## <a name="set-up-endpoint-and-route"></a>Einrichten von Endpunkt und Route

Um die Ereignisverarbeitung zwischen Zwillingen einzurichten, müssen Sie zunächst einen **Endpunkt** in Azure Digital Twins sowie eine **Route** zu diesem Endpunkt erstellen. Zwillinge, die aktualisiert werden, nutzen die Route, um Informationen über ihre Updateereignisse an den Endpunkt zu senden (von dort kann Event Grid sie später abholen und zur Verarbeitung an eine Azure-Funktion übergeben).

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>Erstellen der Azure-Funktion

Erstellen Sie als Nächstes eine Azure-Funktion, die am Endpunkt lauscht und die Ereignisse der Zwillinge empfängt, die über die Route dorthin gesendet werden. 

1. Erstellen Sie auf Ihrem Computer zunächst ein Azure Functions-Projekt in Visual Studio. Anweisungen dazu finden Sie unter [Entwickeln von Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Fügen Sie Ihrem Projekt die folgenden Pakete hinzu (Sie können dazu den Visual Studio NuGet-Paketmanager oder `dotnet`-Befehle in einem Befehlszeilentool verwenden).

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. Füllen Sie die Logik Ihrer Funktion aus. Um den Einstieg zu erleichtern, können Sie sich Funktionscodebeispiele für mehrere Szenarien im Repository [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) ansehen.

5. Veröffentlichen der Funktions-App in Azure Anweisungen zum Veröffentlichen einer Funktions-App finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>Konfigurieren der Funktions-App

Bevor Ihre Funktion auf Azure Digital Twins zugreifen kann, benötigt sie einige Informationen über die Instanz und die notwendige Zugriffsberechtigung. In diesem Abschnitt **weisen Sie eine Zugriffsrolle** für die Funktion zu, und **konfigurieren die Anwendungseinstellungen**, damit sie die Instanz finden und darauf zugreifen kann.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>Verbinden der Funktion mit Event Grid

Abonnieren Sie als nächstes Ihre Azure-Funktion für das Event Grid-Thema, das Sie zuvor erstellt haben. Dadurch wird sichergestellt, dass Daten von einem aktualisierten Zwilling über das Event Grid-Thema an die Funktion fließen können.

Dazu erstellen Sie ein **Event Grid-Abonnement**, das Daten aus dem zuvor erstellten Event Grid-Thema an Ihre Azure-Funktion sendet.

Verwenden Sie den folgenden CLI-Befehl und geben Sie Platzhalter für Ihre Abonnement-ID, die Ressourcengruppe, die Funktions-App und den Funktionsnamen ein.

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

Jetzt kann Ihre Funktion Ereignisse über Ihr Event Grid-Thema empfangen. Die Einrichtung des Datenflusses ist damit abgeschlossen.

## <a name="test-and-verify-results"></a>Testen und Überprüfen der Ergebnisse

Der letzte Schritt besteht darin, zu überprüfen, ob der Flow funktioniert. Dazu müssen Sie einen Zwilling aktualisieren und überprüfen, ob die zugehörigen Zwillinge gemäß der Logik in Ihrer Azure-Funktion ebenfalls aktualisiert werden.

Um den Prozess zu starten, aktualisieren Sie zunächst den Zwilling, der die Quelle des Ereignisflusses ist. Für das Update können Sie die [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update), das [Azure Digital Twins-SDK](how-to-manage-twin.md#update-a-digital-twin) oder die [Azure Digital Twins-REST APIs](how-to-use-postman.md?tabs=data-plane) verwenden.

Senden Sie danach eine Abfrage zu dem zugehörigen Zwilling an Ihre Azure Digital Twins-Instanz. Dafür können Sie die [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) oder die [Azure Digital Twins-REST APIs und das SDK](how-to-query-graph.md#run-queries-with-the-api) verwenden. Überprüfen Sie, ob der Zwilling die Daten empfangen hat und wie erwartet aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel richten Sie die Ereignisverarbeitung zwischen Zwillingen in Azure Digital Twins ein. Richten Sie als Nächstes eine Azure-Funktion ein, um diesen Flow automatisch basierend auf den eingehenden Telemetriedaten von IoT Hub-Geräten auszulösen: [Erfassen von Telemetriedaten aus IoT Hub](how-to-ingest-iot-hub-data.md).
