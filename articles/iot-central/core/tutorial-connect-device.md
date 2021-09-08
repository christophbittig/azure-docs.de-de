---
title: 'Tutorial: Verbinden einer generischen Client-App mit Azure IoT Central | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie ein Gerät, auf dem eine C-, C#-, Java-, JavaScript- oder Python-Client-App ausgeführt wird, mit Ihrer Azure IoT Central-Anwendung verbinden. Sie ändern die automatisch generierte Gerätevorlage durch Hinzufügen von Ansichten, über die ein Operator mit einem verbundenen Gerät interagieren kann.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 7be12549d6e4afaa981a3d3dc83ad817ff5d9c9d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471316"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutorial: Erstellen einer Clientanwendung und Verbinden dieser Anwendung mit Ihrer Azure IoT Central-Anwendung

In diesem Tutorial erfahren Sie, wie Sie eine Clientanwendung mit Ihrer Azure IoT Central-Anwendung verbinden. Die Anwendung simuliert das Verhalten eines Thermostatsgerät. Wenn die Anwendung eine Verbindung mit IoT Central herstellt, sendet sie die Modell-ID des Thermostatgerätemodells. IoT Central verwendet die Modell-ID, um das Gerätemodell abzurufen und eine Gerätevorlage für Sie zu erstellen. Sie fügen der Gerätevorlage Anpassungen und Ansichten hinzu, um einem Operator die Interaktion mit einem Gerät zu ermöglichen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Ausführen des Gerätecodes und Verfolgen der Verbindungsherstellung mit Ihrer IoT Central-Anwendung
> * Zeigen Sie die simulierten Telemetriedaten an, die vom Gerät gesendet werden.
> * Hinzufügen von benutzerdefinierten Ansichten zu einer Gerätevorlage
> * Veröffentlichen der Gerätevorlage
> * Verwalten von Geräteeigenschaften mithilfe einer Ansicht
> * Aufrufen eines Befehls zum Steuern des Geräts

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Anzeigen von Rohdaten

Sie können die Ansicht **Rohdaten** verwenden, um die Rohdaten zu untersuchen, die von Ihrem Gerät an IoT Central gesendet werden:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Ansicht „Rohdaten“":::

In dieser Ansicht können Sie die anzuzeigenden Spalten auswählen und einen Zeitbereich zum Anzeigen festlegen. In der Spalte für **Nicht modellierte Daten** werden die Daten des Geräts angezeigt, die keiner der Definitionen für Eigenschaften oder Telemetriedaten in der Gerätevorlage entsprechen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit den Tutorials zu IoT Central fortfahren und mehr über das Erstellen einer IoT Central-Lösung erfahren möchten, lesen Sie den folgenden Artikel:

> [!div class="nextstepaction"]
> [Erstellen einer Gatewaygerätevorlage](./tutorial-define-gateway-device-type.md)
