---
title: Grundlegendes zum integrierten Azure IoT Hub-Endpunkt | Microsoft-Dokumentation
description: Entwicklerhandbuch – beschreibt, wie der integrierte, Event Hub-kompatible Endpunkt verwendet wird, um Nachrichten zu lesen, die von Geräten an die Cloud gesendet werden.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: eb90f48c78e98284268806aa6f1698a94f57fb4c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355002"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Lesen von Nachrichten, die von Geräten an die Cloud gesendet werden, vom integrierten Endpunkt

Standardmäßig werden Nachrichten an den integrierten dienstseitigen Endpunkt (**messages/events**) gesendet, der mit [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) kompatibel ist. Dieser Endpunkt wird derzeit nur über das [AMQP](https://www.amqp.org/)-Protokoll an Port 5671 und über [AMQP über WebSockets](http://docs.oasis-open.org/amqp-bindmap/amqp-wsb/v1.0/cs01/amqp-wsb-v1.0-cs01.html) an Port 443 verfügbar gemacht. Ein IoT Hub macht die folgenden Eigenschaften verfügbar, damit Sie den integrierten Event Hub-kompatiblen Messagingendpunkt für **messages/events** steuern können.

| Eigenschaft            | BESCHREIBUNG |
| ------------------- | ----------- |
| **Anzahl von Partitionen** | Legen Sie diese Eigenschaft bei der Erstellung fest, um die Anzahl von [Partitionen](../event-hubs/event-hubs-features.md#partitions) für die D2C-Ereigniserfassung zu definieren. |
| **Aufbewahrungsdauer**  | Diese Eigenschaft gibt den Zeitraum in Tagen an, für den Nachrichten von IoT Hub aufbewahrt werden. Als Standardwert ist ein Tag festgelegt, dieser Wert kann jedoch auf sieben Tage erhöht werden. |

IoT Hub ermöglicht die Datenaufbewahrung in den integrierten Event Hubs für bis zu sieben Tage. Sie können die Aufbewahrungsdauer bei der Erstellung Ihres IoT Hubs festlegen. Die Datenaufbewahrungsdauer im IoT-Hub hängt von Ihrer IoT Hub-Ebene und dem Typ der Einheit ab. In Bezug auf die Größe können die integrierten Event Hubs Nachrichten mit der maximalen Nachrichtengröße bis zu einem Kontingent von mindestens 24 Stunden speichern. Ein IoT Hub für die Einheit 1 S1 bietet z. B. ausreichend Speicherplatz, um mindestens 400K Nachrichten von je 4K Größe zu speichern. Wenn Ihre Geräte Nachrichten mit geringerer Größe senden, können diese je nach verbrauchtem Speicherplatz länger (bis zu sieben Tage) aufbewahrt werden. Wir garantieren, dass die Daten mindestens für die angegebene Aufbewahrungsdauer aufbewahrt werden. Nachrichten laufen ab und sind nach Ablauf der Aufbewahrungsdauer nicht mehr zugänglich. 

Mit dem IoT Hub können Sie außerdem Consumergruppen auf dem integrierten D2C-Empfangsendpunkt verwalten. Für jeden IoT Hub können bis zu 20 Consumergruppen bestehen.

Bei Verwendung von [Nachrichtenweiterleitung ](iot-hub-devguide-messages-d2c.md) und aktivierter [Fallbackroute](iot-hub-devguide-messages-d2c.md#fallback-route) gelangen alle Nachrichten, die keiner Abfrage in keiner Route entsprechen in den integrierten Endpunkt. Wenn Sie diese Fallbackroute deaktivieren, werden Nachrichten verworfen, für die sich keine Übereinstimmungen mit Abfragen ergeben.

Sie können die Aufbewahrungsdauer sowohl programmgesteuert über die [IoT Hub-Ressourcenanbieter-REST-APIs](/rest/api/iothub/iothubresource) als auch über das [Azure-Portal](https://portal.azure.com) ändern.

IoT Hub stellt den integrierten Endpunkt **messages/events** für Ihre Back-End-Dienste bereit, damit die D2C-Nachrichten gelesen werden können, die von Ihrem Hub empfangen werden. Der Endpunkt ist Event Hub-kompatibel, sodass Sie zum Lesen von Nachrichten alle Mechanismen verwenden können, die der Event Hubs-Dienst unterstützt.

## <a name="read-from-the-built-in-endpoint"></a>Lesen vom integrierten Endpunkt

Einige Produktintegrationen und Event Hubs SDKs sind über den IoT Hub informiert und ermöglichen es Ihnen, über Ihre IoT Hub-Dienstverbindungszeichenfolge eine Verbindung zum integrierten Endpunkt herzustellen.

Wenn Sie Event Hubs SDKs oder Produktintegrationen verwenden, die nicht IoT Hub-fähig sind, benötigen Sie einen Event Hub-kompatiblen Endpunkt und den Event Hub-kompatiblen Namen: Sie können diese Werte wie folgt aus dem Portal abrufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

2. Klicken Sie auf **Integrierte Endpunkte**.

3. Der Abschnitt **Ereignisse** enthält die folgenden Werte: **Partitionen**, **Event Hub-kompatibler Name**, **Event Hub-kompatibler Endpunkt**, **Aufbewahrungszeit** und **Consumergruppen**.

    ![D2C-Einstellungen](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Im Portal enthält das Feld E“vent Hub-kompatibler Endpunkt“ eine komplette Event Hubs-Verbindungszeichenfolge, die wie folgt aussieht: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Wenn das von Ihnen verwendete SDK andere Werte erfordert, dann wären dies:

| Name | Wert |
| ---- | ----- |
| Endpunkt | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostname | abcd1234namespace.servicebus.windows.net |
| Namespace | abcd1234namespace |

Sie können dann eine beliebige SAS-Richtlinie aus der Dropdownliste auswählen, wie im obigen Screenshot gezeigt. Es werden nur Richtlinien mit den erforderlichen **ServiceConnect**-Berechtigungen für Verbindungen mit dem angegebenen Event Hub angezeigt.

Zu den SDKs, die Sie für die Verbindung mit dem integrierten Event Hub-kompatiblen Endpunkt verwenden können, den der IoT Hub bereitstellt, gehören:

| Sprache | SDK | Beispiel |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Schnellstart](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Schnellstart](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Schnellstart](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) |
| Python | https://pypi.org/project/azure-eventhub/ | [Schnellstart](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) |

Zu den Produktintegrationen, die Sie mit dem integrierten Event Hub-kompatiblen Endpunkt verwenden können, den der IoT Hub bereitstellt, gehören:

* [Azure Functions](../azure-functions/index.yml). Weitere Informationen finden Sie unter [Azure IoT Hub-Bindungen für Azure Functions](../azure-functions/functions-bindings-event-iot.md).
* [Azure Stream Analytics](../stream-analytics/index.yml): Weitere Informationen finden Sie unter [Streamen von Daten als Eingabe in Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml): Weitere Informationen finden Sie unter [Hinzufügen einer IoT Hub-Ereignisquelle zu einer Time Series Insights-Umgebung](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm-Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Sie können sich die [Spoutquelle](https://github.com/apache/storm/tree/master/external/storm-eventhubs) bei GitHub ansehen.
* [Apache Spark-Integration](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/):

## <a name="use-amqp-ws-or-a-proxy-with-event-hubs-sdks"></a>Verwenden von AMQP-WS oder eines Proxys mit Event Hubs SDKs

Sie können die Event Hubs SDKs verwenden, um in Umgebungen, in denen AMQP über WebSockets oder das Lesen über einen Proxy erforderlich ist, Daten aus dem integrierten Endpunkt zu lesen. Weitere Informationen finden Sie in den folgenden Beispielen:

| Sprache | Beispiel |
| -------- | ------ |
| .NET | [ReadD2cMessages (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/ReadD2cMessages) |
| Java | [read-d2c-messages (Java)](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Node.js | [read-d2c-messages (Node.js)](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Python | [read-dec-messages (Python)](https://github.com/Azure-Samples/azure-iot-samples-python/tree/master/iot-hub/Quickstarts/read-d2c-messages) |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu IoT Hub-Endpunkten finden Sie unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md).

* In den [Schnellstarts](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erfahren Sie, wie Sie D2C-Nachrichten über simulierte Geräte senden und die Nachrichten über den integrierten Endpunkt lesen. 

Ausführlichere Informationen finden Sie im Tutorial [Verarbeiten von IoT Hub-D2C-Nachrichten mit Routen](tutorial-routing.md).

* Informationen zum Weiterleiten von Gerät-zu-Cloud-Nachrichten an benutzerdefinierte Endpunkte finden Sie unter [Verwenden von Nachrichtenrouten und benutzerdefinierten Endpunkten für D2C-Nachrichten](iot-hub-devguide-messages-read-custom.md).