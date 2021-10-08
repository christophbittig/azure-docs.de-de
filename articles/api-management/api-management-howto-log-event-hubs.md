---
title: Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure API Management Ereignisse in Azure Event Hubs protokollieren. Event Hubs ist ein hoch skalierbarer Datenerfassungsdienst.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 80a518b7db0f28e92a421189d111bde2e82875ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591879"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management
Azure Event Hubs ist ein hochgradig skalierbarer Dateneingangsdienst, der Millionen von Ereignissen pro Sekunde erfassen kann. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Event Hubs fungiert als „Eingangstür“ für eine Ereignispipeline. Nach der Erfassung in Event Hubs können Sie Daten mit einem beliebigen Echtzeit-Analyseanbieter oder mit Batchverarbeitungs-/Speicheradaptern umwandeln und speichern. Event Hubs entkoppelt die Erzeugung eines Datenstroms von Ereignissen von der Nutzung dieser Ereignisse, sodass  Ereignisconsumer nach einem eigenen Zeitplan auf Ereignisse zugreifen können.

In diesem Artikel wird beschrieben, wie Sie API Management-Ereignisse mithilfe von Azure Event Hubs protokollieren.

## <a name="create-an-azure-event-hub"></a>Erstellen eines Azure Event Hubs

Ausführliche Schritte zum Erstellen eines Event Hubs und zum Abrufen von Verbindungszeichenfolgen, die Sie zum Senden und Empfangen von Ereignis aus dem bzw. im Event Hub benötigen, finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Erstellen eines API Management-Loggers
Der Event Hub ist nun vorhanden. Der nächste Schritt besteht darin, einen [Logger](/rest/api/apimanagement/2020-12-01/logger) im API Management-Dienst zu konfigurieren, sodass Ereignisse im Event Hub protokolliert werden können.

API Management-Logger werden mit der [API Management-REST-API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST)konfiguriert. Ausführliche Anforderungsbeispiele finden Sie unter [Erstellen von Protokollierungen](/rest/api/apimanagement/2020-12-01/logger/create-or-update).

## <a name="configure-log-to-eventhub-policies"></a>Konfigurieren von log-to-eventhub-Richtlinien

Nachdem Sie die Protokollierung in API Management konfiguriert haben, können Sie die Richtlinien zum Protokollieren im Event Hub („log-to-eventhub-Richtlinie“) für die gewünschten Ereignisse konfigurieren. Die log-to-eventhub-Richtlinie kann im Abschnitt mit Richtlinien für eingehenden Datenverkehr oder im Abschnitt mit Richtlinien für ausgehenden Datenverkehr verwendet werden.

1. Navigieren Sie zu Ihrer APIM-Instanz.
2. Wählen Sie die Registerkarte „API“ aus.
3. Wählen Sie die API aus, der Sie die Richtlinie hinzufügen möchten. In diesem Beispiel fügen wir die Richtlinie zur **Echo-API** im Produkt **Unlimited** hinzu.
4. Wählen Sie **Alle Vorgänge** aus.
5. Klicken Sie im oberen Seitenbereich auf die Registerkarte „Entwurf“.
6. Klicken Sie im Fenster „Eingehende Verarbeitung“ bzw. „Ausgehende Verarbeitung“ auf das Dreieck (neben dem Stift).
7. Wählen Sie den Code-Editor aus. Weitere Informationen finden Sie unter [Einrichten oder Bearbeiten von Richtlinien](set-edit-policies.md).
8. Positionieren Sie den Cursor im Richtlinienabschnitt `inbound` oder `outbound`.
9. Wählen Sie im Fenster rechts die Optionen **Erweiterte Richtlinien** > **Bei EventHub anmelden** aus. Damit wird die Richtlinienanweisungsvorlage `log-to-eventhub` eingefügt.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Ersetzen Sie `logger-id` durch den Wert, den Sie im vorherigen Schritt in der Anforderungs-URL zum Erstellen der Protokollierung für `{loggerId}` verwendet haben.

Sie können jeden Ausdruck verwenden, der eine Zeichenfolge als Wert für das Element `log-to-eventhub` zurückgibt. In diesem Beispiel wird eine Zeichenfolge im JSON-Format protokolliert, die das Datum, die Uhrzeit, den Dienstnamen, die Anforderungs-ID, die IP-Adresse der Anforderung und den Vorgangsnamen enthält.

Klicken Sie auf **Speichern** , um die aktualisierte Richtlinienkonfiguration zu speichern. Die Richtlinie ist sofort nach dem Speichern aktiv, und im vorgesehenen Event Hub werden Ereignisse protokolliert.

> [!NOTE]
> Die maximal unterstützte Nachrichtengröße, die von dieser API Management-Richtlinie an einen Event Hub gesendet werden kann, beträgt 200 Kilobyte (KB). Ist eine an einen Event Hub gesendete Nachricht größer als 200 KB, wird sie automatisch gekürzt, und die gekürzte Nachricht wird an Event Hubs übertragen.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Anzeigen einer Vorschau des Protokolls in Event Hubs mithilfe von Azure Stream Analytics

Sie können eine Vorschau des Protokolls in Event Hubs anzeigen, indem Sie [Azure Stream Analytics-Abfragen](../event-hubs/process-data-azure-stream-analytics.md) verwenden. 

1. Navigieren Sie im Azure-Portal zu dem Event Hub, an den die Protokollierung Ereignisse sendet. 
2. Wählen Sie unter **Features** die Registerkarte **Daten verarbeiten** aus.
3. Wählen Sie auf der Karte **Echtzeiterkenntnisse von Ereignissen aktivieren** die Option **Erkunden** aus.
4. Sie sollten in der Lage sein, die Vorschau des Protokolls auf der Registerkarte **Eingabevorschau** anzuzeigen. Wenn die angezeigten Daten nicht aktuell sind, wählen Sie **Aktualisieren** aus, um die neuesten Ereignisse anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Event Hubs
  * [Erste Schritte mit Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Empfangen von Nachrichten mit EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Programmierleitfaden für Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Erfahren Sie mehr über die Integration der API-Verwaltung und Event Hubs
  * [Verweis zu Protokollierungstool](/rest/api/apimanagement/2020-12-01/logger)
  * [log-to-eventhub policy reference](./api-management-advanced-policies.md#log-to-eventhub)
  * [Überwachen von APIs mit Azure API Management, Event Hubs und Moesif](api-management-log-to-eventhub-sample.md)  
* Erfahren Sie mehr über die [Integration in Azure Application Insights](api-management-howto-app-insights.md).

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
