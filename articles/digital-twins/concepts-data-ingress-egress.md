---
title: Dateneingang und -ausgang
titleSuffix: Azure Digital Twins
description: Grundlegendes zu den Anforderungen an Ein- und Ausgänge für die Integration von Azure Digital Twins in andere Dienste.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 55aa3f69976a413e1c23e9d935ad99fde88065ec
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350971"
---
# <a name="data-ingress-and-egress-for-azure-digital-twins"></a>Datenein- und -ausgänge für Azure Digital Twins

Azure Digital Twins wird in der Regel zusammen mit anderen Diensten verwendet, um flexible, verbundene Lösungen zu erstellen, die Ihre Daten auf verschiedene Weise verwenden.

Unter Verwendung von [Ereignisrouten](concepts-route-events.md) kann Azure Digital Twins Daten von Upstreamdiensten wie [IoT Hub](../iot-hub/about-iot-hub.md) oder [Logic Apps](../logic-apps/logic-apps-overview.md) empfangen, die zur Bereitstellung von Telemetrie und Benachrichtigungen verwendet werden. 

Azure Digital Twins kann Daten auch an Downstreamdienste wie [Azure Maps](../azure-maps/about-azure-maps.md) und [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) zur Speicherung, Workflowintegration, Analyse und für andere Zwecke weiterleiten. 

## <a name="data-ingress"></a>Dateneingang

Azure Digital Twins kann mit Daten und Ereignissen aus jedem Dienst gespeist werden – [IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), Ihrem eigenen benutzerdefinierten Dienst usw. Dies ermöglicht Ihnen, Telemetrie von physischen Geräten in Ihrer Umgebung zu erfassen und diese Daten mithilfe des Azure Digital Twins-Graphen in der Cloud zu verarbeiten.

Anstatt einen integrierten IoT Hub im Hintergrund zu verwenden, können Sie mit Azure Digital Twins Ihren eigenen IoT Hub für den Dienst verwenden. Sie können einen vorhandenen IoT Hub verwenden, den Sie derzeit in der Produktionsumgebung einsetzen, oder einen neuen IoT Hub zu diesem Zweck bereitstellen. Dadurch erhalten Sie vollen Zugriff auf alle Geräteverwaltungsfunktionen des IoT Hubs.

Um Daten von einer beliebigen Quelle in Azure Digital Twins zu erfassen, verwenden Sie eine [Azure-Funktion](../azure-functions/functions-overview.md). Weitere Informationen zu diesem Muster finden Sie unter[Erfassen von Telemetriedaten aus IoT Hub](how-to-ingest-iot-hub-data.md), oder testen Sie selbst im Azure Digital Twins-Tutorial: [Verbinden einer End-to-End-Lösung](tutorial-end-to-end.md). 

Sie können sich auch informieren, wie Sie Azure Digital Twins mit einem Logic Apps-Trigger verbinden. Weitere Informationen finden Sie unter [Integration in Logic Apps](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Datenausgangsdienste

Azure Digital Twins kann Daten an verbundene **Endpunkte** senden. Folgende Endpunkte können unterstützt werden:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Endpunkte werden über Verwaltungs-APIs oder das Azure-Portal an Azure Digital Twins angefügt. Weitere Informationen zum Anfügen eines Endpunkts an Azure Digital Twins finden Sie in [Verwalten von Endpunkten und Routen](how-to-manage-routes.md).

Es gibt viele andere Dienste, an die Sie Ihre Daten letztendlich weiterleiten können, z. B. [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md) [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) oder [Time Series Insights](../time-series-insights/overview-what-is-tsi.md). Fügen Sie den Zieldienst an einen Endpunkt an, um Ihre Daten an Dienste wie diese zu senden.

Wenn Sie z. B. auch Azure Maps verwenden und den Standort mit Ihrem Azure Digital Twins-[Zwillingsgraphen](concepts-twins-graph.md) korrelieren möchten, können Sie Azure Functions mit Event Grid verwenden, um die Kommunikation zwischen allen Diensten in Ihrer Bereitstellung herzustellen. Weitere Informationen dazu finden Sie unter [Verwenden von Azure Digital Twins zum Aktualisieren eines Azure Maps-Gebäudeplans](how-to-integrate-maps.md).

Sie können sich auch informieren, wie Sie Daten auf ähnliche Weise an Time Series Insights weiterleiten können. Weitere Informationen finden Sie unter [Integration in Time Series Insights](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Endpunkten und zur Weiterleitung von Ereignissen an externe Dienste:
* [Weiterleiten von Azure Digital Twins-Ereignissen](concepts-route-events.md)

Erfahren Sie, wie Sie Azure Digital Twins zum Erfassen von Daten aus IoT Hub einrichten:
* [Erfassen von Telemetriedaten aus IoT Hub](how-to-ingest-iot-hub-data.md)