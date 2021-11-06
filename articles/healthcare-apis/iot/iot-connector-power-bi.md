---
title: IoT-Connector microsoft Power BI – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie IoT-Connector und -Power BI
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 014bf6fe316252536a14c15bad8255285226ce10
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894721"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>IoT-Connector und Microsoft Power BI

In diesem Artikel wird die Verwendung des IoT-Connectors und von Microsoft Power Business Intelligence (BI) beschrieben.

## <a name="iot-connector-and-power-bi-reference-architecture"></a>IoT-Connector und Power BI Referenzarchitektur

Die folgende Referenzarchitektur zeigt die grundlegenden Komponenten der Verwendung von Microsoft-Clouddiensten, um Power BI über IoMT-Daten (Internet of Medical Things) und Fast Healthcare Interoperability Resources -Daten (FHIR&#174;) zu aktivieren. 

Sie können sogar dashboards Power BI in den Microsoft Teams-Client einbetten, um die Koordination des Pflegeteams weiter zu verbessern. Weitere Informationen zum Einbetten von Power BI in Teams finden Sie [hier.](/power-bi/collaborate-share/service-embed-report-microsoft-teams)

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="Screenshot: IoT-Connector und Power BI." lightbox="media/iot-concepts/iot-connector-power-bi.png":::

Der IoT-Connector kann IoT-Daten von den meisten IoT-Geräten oder -Gateways unabhängig vom Standort, Rechenzentrum oder der Cloud aufnehmen. 

Wir empfehlen die Verwendung von Azure IoT, um die Geräte-/Gatewaykonnektivität zu unterstützen.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="Screenshot: IoT-Connector, IoT Hub und Power BI." lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

Für einige Lösungen kann Azure IoT Central verwendet werden, statt Azure IoT Hub.

Azure IoT Edge kann in mit IoT Hub verwendet werden, um einen lokalen Endpunkt für Geräte und/oder Gerätekonnektivität zu erstellen.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="Screenshot: IoT-Connector, IoT Hub, IoT Edge und Power BI." lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über den IoT-Connector und die Power BI erfahren. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
