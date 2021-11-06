---
title: IoT-Connector und Teams Benachrichtigungen – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie den IoT-Connector verwenden und Benachrichtigungen Teams können.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 5f2cb9c9a4e4ef20e489c36a92285a6f96f74282
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894731"
---
# <a name="iot-connector-and-microsoft-teams-notifications"></a>IoT-Connector und Microsoft Teams Benachrichtigungen

In diesem Artikel untersuchen wir die Verwendung des IoT-Connectors und Microsoft Teams für Benachrichtigungen.

## <a name="iot-connector-and-teams-notifications-reference-architecture"></a>Referenzarchitektur für IoT-Teams-Benachrichtigungen

Bei der Kombination von IoT-Connector, einem Fast Healthcare Interoperability Resources-Dienst (FHIR&#174;) und Teams können Sie mehrere Pflegelösungen aktivieren. 

Im Folgenden finden Sie den IoT-Connector zum Teams von Benachrichtigungen zur Aktivierung von IoT-Connector, FHIR und Teams Patienten-App. 

Sie können sogar dashboards Power BI in den Microsoft Teams einbetten. Weitere Informationen zum Einbetten von Power BI in Microsoft Team finden Sie [hier.](/power-bi/collaborate-share/service-embed-report-microsoft-teams)

:::image type="content" source="media/iot-concepts/iot-connector-teams.png" alt-text="Screenshot: IoT-Connector und Teams." lightbox="media/iot-concepts/iot-connector-teams.png":::

Der IoT-Connector für kann IoT-Daten von den meisten IoT-Geräten oder -Gateways unabhängig von Standort, Rechenzentrum oder Cloud aufnehmen. 

Wir empfehlen die Verwendung von Azure IoT, um die Geräte-/Gatewaykonnektivität zu unterstützen.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-teams.png" alt-text="Screenshot: IoT-Connector und IoT Hub." lightbox="media/iot-concepts/iot-connector-iot-hub-teams.png":::

Bei einigen Lösungen kann Azure IoT Central verwendet werden, statt Azure IoT Hub.

Azure IoT Edge kann in mit IoT Hub verwendet werden, um einen lokalen Endpunkt für Geräte und/oder Gerätekonnektivität zu erstellen.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-teams.png" alt-text="Screenshot: IoT-Connector und IoT Edge." lightbox="media/iot-concepts/iot-connector-iot-edge-teams.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über den IoT-Connector und die Integration Teams Benachrichtigungen erfahren. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
