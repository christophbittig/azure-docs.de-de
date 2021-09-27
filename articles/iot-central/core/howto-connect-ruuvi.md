---
title: Verbinden eines RuuviTag-Geräts in Azure IoT Central | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen RuuviTag-Umgebungssensor mit Ihrer IoT Central-Anwendung verbinden.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 08/20/2021
ms.openlocfilehash: b4d169a46f499e83d0abf5d3145d67a3f6fdf03b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439215"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Verbinden eines RuuviTag-Sensors mit Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie einen RuuviTag-Sensor mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden können.

Was ist RuuviTag?

RuuviTag ist eine moderne und führende Open-Source-Plattform für Sensorbeacons, die die Anforderungen von Geschäftskunden, Entwicklern, Herstellern, Schülern/Studenten und privaten Tüftlern erfüllt. Das Gerät ist so eingerichtet, dass es direkt nach dem Auspacken einsatzbereit ist und am gewünschten Ort bereitgestellt werden kann. Hierbei handelt es sich um einen Bluetooth LE-Beacon mit integriertem Umgebungssensor und Beschleunigungsmesser.

Das RuuviTag-Gerät kommuniziert per BLE (Bluetooth Low Energy) und benötigt für die Kommunikation mit Azure IoT Central ein Gatewaygerät. Sie müssen über ein konfiguriertes Gatewaygerät verfügen, z. B. Rigado Cascade 500, damit für das RuuviTag-Gerät eine Verbindung mit IoT Central hergestellt werden kann.

Befolgen Sie [diese Anweisungen](./howto-connect-rigado-cascade-500.md), um ein Rigado Cascade 500-Gatewaygerät einzurichten.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verbinden von RuuviTag-Sensoren benötigen Sie folgende Ressourcen:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- Einen RuuviTag-Sensor. Weitere Informationen finden Sie unter [RuuviTag](https://ruuvi.com/).

- Ein Rigado Cascade 500-Gerät oder ein anderes BLE-Gateway. Weitere Informationen finden Sie unter [Rigado](https://www.rigado.com/).


## <a name="add-a-ruuvitag-device-template"></a>Hinzufügen einer RuuviTag-Gerätevorlage

Für das Onboarding eines RuuviTag-Sensors in Ihrer Azure IoT Central-Anwendungsinstanz müssen Sie eine entsprechende Gerätevorlage in der Anwendung konfigurieren.

So fügen Sie eine RuuviTag-Gerätevorlage hinzu

1. Navigieren Sie im linken Bereich zur Registerkarte ***Gerätevorlagen** _, und wählen Sie aus _*+ Neu**: ![Neue Gerätevorlage erstellen](./media/howto-connect-ruuvi/device-template-new.png). Auf der dann angezeigten Seite gibt es eine Option zum ***Erstellen einer benutzerdefinierten Vorlage**_ oder zum _ *_Verwenden einer vorkonfigurierten Gerätevorlage_**.
1. Wählen Sie die RuuviTag Multisensor-Gerätevorlage aus der Liste der vorkonfigurierten Gerätevorlagen wie unten dargestellt:  ![RuuviTag Gerätevorlage auswählen](./media/howto-connect-ruuvi/device-template-pre-configured.png)
1. Klicken Sie auf ***Weiter: Anpassen***, um mit dem nächsten Schritt fortzufahren.
1. Wählen Sie auf dem nächsten Bildschirm ***Erstellen*** aus, um das Onboarding der C500-Gerätevorlage in Ihrer IoT Central-Anwendung durchzuführen.

## <a name="connect-a-ruuvitag-sensor"></a>Verbinden eines RuuviTag-Sensors

Wie bereits erwähnt, müssen Sie ein Gatewaygerät einrichten, um das RuuviTag-Gerät mit Ihrer IoT Central-Anwendung zu verbinden. In den unten angegebenen Schritten wird angenommen, dass Sie bereits ein Rigado Cascade 500-Gatewaygerät eingerichtet haben.  

1. Schalten Sie Ihr Rigado Cascade 500-Gerät ein, und verbinden Sie es mit Ihrem Netzwerk (über Ethernet oder drahtlos).
1. Nehmen Sie die Abdeckung des RuuviTag-Geräts ab, und ziehen Sie die Plastiklasche heraus, um die Akkusicherung aufzuheben.
1. Ordnen Sie das RuuviTag-Gerät in der Nähe eines Rigado Cascade 500-Gateways an, das in Ihrer IoT Central-Anwendung bereits konfiguriert ist.
1. Nach wenigen Sekunden sollte Ihr RuuviTag-Gerät in der Liste der Geräte in IoT Central angezeigt werden.  
    ![RuuviTag-Geräteliste](./media/howto-connect-ruuvi/ruuvi-device-list.png)

Sie können dieses RuuviTag-Gerät nun in Ihrer IoT Central-Anwendung verwenden.  

## <a name="create-a-simulated-ruuvitag"></a>Erstellen eines simulierten RuuviTag-Geräts

Wenn Sie nicht über ein physisches RuuviTag-Gerät verfügen, können Sie einen simulierten RuuviTag-Sensor erstellen und für Tests in Ihrer Azure IoT Central-Anwendung verwenden.

So erstellen Sie ein simuliertes RuuviTag-Gerät

1. Wählen Sie **Geräte > RuuviTag** aus.
1. Wählen Sie **+ Neu** aus.
1. Geben Sie eine eindeutige **Geräte-ID** und einen benutzerfreundlichen **Gerätenamen** an.  
1. Aktivieren Sie die Einstellung **Simuliert**.
1. Klicken Sie auf **Erstellen**.  

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes werden folgende Schritte vorgeschlagen:

- Informieren Sie sich zu [Gerätekonnektivität in Azure IoT Central](./concepts-get-connected.md).
- Informieren Sie sich über [Überwachen der Gerätekonnektivität per Azure CLI](./howto-monitor-devices-azure-cli.md).
