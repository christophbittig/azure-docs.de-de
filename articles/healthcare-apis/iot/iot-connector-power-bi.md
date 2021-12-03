---
title: IoT-Connector – Microsoft Power BI – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie den IoT-Connector verwenden und Power BI
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: b099a085041664ed00788720eb3245dd9518c020
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315404"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>IoT-Connector und Microsoft Power BI

In diesem Artikel erfahren Sie, wie Sie den IoT-Connector und Microsoft Power Business Intelligence (BI) verwenden.

## <a name="iot-connector-and-power-bi-reference-architecture"></a>IoT-Connector und Power BI Referenzarchitektur

Die folgende Referenzarchitektur zeigt die grundlegenden Komponenten der Verwendung von Microsoft-Clouddiensten, um Power BI über das Internet der medizinischen Dinge (Internet of Medical Things, IoMT) und Fast Healthcare Interoperability Resources -Daten (FHIR&#174;) zu aktivieren. 

Sie können sogar Power BI Dashboards in den Microsoft Teams-Client einbetten, um die Koordination des Pflegeteams weiter zu verbessern. Weitere Informationen zum Einbetten von Power BI in Teams finden Sie [hier.](/power-bi/collaborate-share/service-embed-report-microsoft-teams)

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="Screenshot: IoT-Connector und Power BI" lightbox="media/iot-concepts/iot-connector-power-bi.png":::

Der IoT-Connector kann IoT-Daten von den meisten IoT-Geräten oder Gateways unabhängig vom Standort, Rechenzentrum oder der Cloud erfassen. 

Es wird die Verwendung von Azure IoT-Diensten zur Unterstützung der Geräte-/Gatewaykonnektivität unterstützt.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="Screenshot: IoT-Connector, IoT Hub und Power BI" lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

Bei einigen Lösungen kann Azure IoT Central anstelle von Azure IoT Hub verwendet werden.

Azure IoT Edge kann in mit IoT Hub verwendet werden, um einen lokalen Endpunkt für Geräte und/oder Gerätekonnektivität zu erstellen.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="Screenshot: IoT-Connector, IoT Hub, IoT Edge und Power BI" lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über den IoT-Connector und Power BI Integration erfahren. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
