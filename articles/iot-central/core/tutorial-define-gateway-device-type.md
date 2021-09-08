---
title: 'Tutorial: Definieren eines neuen Gatewaygerätetyps in Azure IoT Central | Microsoft-Dokumentation'
description: In diesem Tutorial für Ersteller erfahren Sie, wie Sie in Ihrer Azure IoT Central-Anwendung einen neuen IoT-Gatewaygerätetyp definieren.
author: rangv
ms.author: rangv
ms.date: 08/18/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 44a659b5b10e1f97293e2cd2636d5774baaebd40
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426206"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definieren eines neuen IoT-Gatewaygerätetyps in Ihrer Azure IoT Central-Anwendung

In diesem Tutorial erfahren Sie, wie Sie in Ihrer IoT Central-Anwendung mithilfe einer Gatewaygerätevorlage ein Gatewaygerät definieren. Anschließend konfigurieren Sie mehrere nachgeschaltete Geräte, die über das Gatewaygerät eine Verbindung mit Ihrer IoT Central-Anwendung herstellen. 

In diesem Tutorial erstellen Sie eine Vorlage für ein Gatewaygerät vom Typ **Smart Building**. Ein Gatewaygerät vom Typ **Smart Building** verfügt über Beziehungen mit anderen nachgeschalteten Geräten.

![Diagramm der Beziehung zwischen Gatewaygerät und nachgeschalteten Geräten](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Neben der Kommunikation von nachgeschalteten Geräten mit Ihrer IoT Central-Anwendung ermöglicht ein Gatewaygerät auch Folgendes:

* Senden eigener Telemetriedaten (beispielsweise zur Temperatur)
* Reagieren auf die Aktualisierung einer schreibbaren Eigenschaft durch einen Bediener. Ein Bediener kann beispielsweise das Sendeintervall für Telemetriedaten ändern.
* Reagieren auf Befehle (beispielsweise Neustarten des Geräts)

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Erstellen von Vorlagen für nachgeschaltete Geräte
> * Erstellen einer Gatewaygerätevorlage
> * Veröffentlichen der Gerätevorlage
> * Erstellen der simulierten Geräte

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen der Schritte in diesem Tutorial benötigen Sie Folgendes:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="create-downstream-device-templates"></a>Erstellen von Vorlagen für nachgeschaltete Geräte

In diesem Tutorial werden Gerätevorlagen für ein Gerät vom Typ **S1 Sensor** und für ein Gerät vom Typ **RS40 Occupancy Sensor** verwendet, um simulierte nachgeschaltete Geräte zu generieren.

So erstellen sie eine Gerätevorlage für ein Gerät vom Typ **S1 Sensor**:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. Wählen Sie anschließend **+ Neu** aus, um mit dem Hinzufügen der Vorlage zu beginnen.

1. Scrollen Sie nach unten zur Kachel für das Gerät **Minew S1**. Wählen Sie die Kachel und anschließend **Weiter: Anpassen**.

1. Wählen Sie auf der Seite **Überprüfen** die Option **Erstellen** aus, um die Gerätevorlage Ihrer Anwendung hinzuzufügen. 

So erstellen sie eine Gerätevorlage für ein Gerät vom Typ **RS40 Occupancy Sensor**:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. Wählen Sie anschließend **+ Neu** aus, um mit dem Hinzufügen der Vorlage zu beginnen.

1. Scrollen Sie nach unten zur Kachel für das Gerät **RS40 Occupancy Sensor**. Wählen Sie die Kachel und anschließend **Weiter: Anpassen**.

1. Wählen Sie auf der Seite **Überprüfen** die Option **Erstellen** aus, um die Gerätevorlage Ihrer Anwendung hinzuzufügen. 

Sie verfügen nun über Gerätevorlagen für die beiden nachgeschalteten Gerätetypen:

![Gerätevorlagen für nachgeschaltete Geräte](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Erstellen einer Gatewaygerätevorlage

In diesem Tutorial erstellen Sie von Grund auf eine Gerätevorlage für ein Gatewaygerät. Diese Vorlage wird später verwendet, um ein simuliertes Gatewaygerät in Ihrer Anwendung zu erstellen.

So fügen Sie Ihrer Anwendung eine neue Gatewaygerätevorlage hinzu:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. Wählen Sie anschließend **+ Neu** aus, um mit dem Hinzufügen der Vorlage zu beginnen.

1. Wählen Sie auf der Seite **Vorlagentyp auswählen** die Kachel **IoT-Gerät** und anschließend **Weiter: Anpassen**.

1. Aktivieren Sie auf der Seite **Gerät anpassen** das Kontrollkästchen **Dies ist ein Gatewaygerät.** .

1. Geben Sie als Vorlagenname **Smart Building gateway device** ein, und wählen Sie dann **Weiter: Überprüfen** aus.

1. Wählen Sie auf der Seite **Überprüfen** die Option **Erstellen** aus. 



1. Wählen Sie auf der Seite **Modell erstellen** die Kachel **Benutzerdefiniertes Modell** aus.

1. Wählen Sie **Funktion hinzufügen** aus, um eine Funktion hinzuzufügen.

1. Geben Sie als Anzeigename **Zu sendende Daten** ein, und wählen Sie als Funktionstyp **Eigenschaft** aus.

1. Wählen Sie **+ Funktion hinzufügen** aus, um eine weitere Funktion hinzuzufügen. Geben Sie als Anzeigename **Boolean Telemetry** (Boolesche Telemetriedaten) ein, und wählen Sie als Funktionstyp **Telemetriedaten** und als Schema **Boolesch** aus.

1. Wählen Sie **Speichern** aus.

### <a name="add-relationships"></a>Hinzufügen von Beziehungen

Als Nächstes fügen Sie den Vorlagen für die nachgeschalteten Geräte Beziehungen hinzu:

1. Wählen Sie in der Vorlage **Smart Building gateway device** die Option **Beziehungen** aus.

1. Wählen Sie **+ Beziehung hinzufügen** aus. Geben Sie **Environmental Sensor** als Anzeigename ein, und wählen Sie **S1 Sensor** als Ziel aus.

1. Wählen Sie erneut **+ Beziehung hinzufügen** aus. Geben Sie **Occupancy Sensor** als Anzeigename ein, und wählen Sie **RS40 Occupancy Sensor** als Ziel aus.

1. Wählen Sie **Speichern** aus.

![Vorlage „Smart Building gateway device“ mit Beziehungen](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Hinzufügen von Cloudeigenschaften

Eine Gatewaygerätevorlage kann Cloudeigenschaften enthalten. Cloudeigenschaften sind nur in der IoT Central-Anwendung vorhanden und werden niemals an ein Gerät gesendet oder von einem Gerät empfangen.

So fügen Sie der Vorlage **Smart Building gateway device** Cloudeigenschaften hinzu:

1. Wählen Sie in der Vorlage **Smart Building gateway device** die Option **Cloudeigenschaften** aus.

1. Verwenden Sie die Informationen in der folgenden Tabelle, um Ihrer Gatewaygerätevorlage zwei Cloudeigenschaften hinzuzufügen:

    | `Display name`      | Semantischer Typ | Schema |
    | ----------------- | ------------- | ------ |
    | Datum der letzten Wartung | Keine          | Date   |
    | Customer Name     | Keine          | String |

1. Wählen Sie **Speichern** aus.

### <a name="create-views"></a>Erstellen von Ansichten

Als Ersteller können Sie die Anwendung so anpassen, dass relevante Informationen zum Umgebungssensorgerät einem Bediener angezeigt werden. Ihre Anpassungen ermöglichen dem Bediener die Verwaltung des mit der Anwendung verbundenen Umgebungssensors. Sie können zwei Arten von Ansichten erstellen, die von einem Bediener zum Interagieren mit Geräten verwendet werden:

* Formulare zum Anzeigen und Bearbeiten von Geräte- und Cloudeigenschaften
* Ansichten zum Visualisieren von Geräten.

So generieren Sie die Standardansichten für die Vorlage **Smart Building gateway device**:

1. Wählen Sie in der Vorlage **Smart Building gateway device** die Option **Ansichten** aus.

1. Wählen Sie die Kachel **Standardansichten generieren** aus, und stellen Sie sicher, dass alle Optionen ausgewählt sind.

1. Wählen Sie die Option zum **Generieren der Standarddashboardansicht(en)** aus.

## <a name="publish-the-device-template"></a>Veröffentlichen der Gerätevorlage

Um ein simuliertes Gatewaygerät erstellen oder eine Verbindung für ein echtes Gatewaygerät herstellen zu können, müssen Sie zunächst Ihre Gerätevorlage veröffentlichen.

So veröffentlichen Sie die Gatewaygerätevorlage:

1. Wählen Sie auf der Seite **Gerätevorlagen** die Vorlage **Smart Building gateway device** aus.

2. Wählen Sie **Veröffentlichen**.

3. Wählen Sie im Dialogfeld **Publish a Device Template** (Gerätevorlage veröffentlichen) die Option **Veröffentlichen** aus.

Nachdem eine Gerätevorlage veröffentlicht wurde, wird sie auf der Seite **Geräte** und für den Bediener angezeigt. Der Bediener kann die Vorlage verwenden, um Geräteinstanzen zu erstellen oder Regeln und Überwachungen einzurichten. Das Bearbeiten einer veröffentlichten Vorlage kann sich auf das Verhalten in der gesamten Anwendung auswirken.

Weitere Informationen zum Ändern einer Gerätevorlage nach der Veröffentlichung finden Sie unter [Bearbeiten einer vorhandenen Gerätevorlage](howto-edit-device-template.md).

## <a name="create-the-simulated-devices"></a>Erstellen der simulierten Geräte

In diesem Tutorial werden simulierte nachgeschaltete Geräte und ein simuliertes Gatewaygerät verwendet.

So erstellen Sie ein simuliertes Gatewaygerät:

1. Wählen Sie auf der Seite **Geräte** in der Liste mit den Gerätevorlagen die Vorlage **Smart Building gateway device** aus.

1. Wählen Sie **+ Neu** aus, um mit dem Hinzufügen eines neuen Geräts zu beginnen.

1. Behalten Sie die generierten Werte für **Geräte-ID** und **Gerätename** bei. Stellen Sie sicher, dass die Option **Simuliert** auf **Ein** festgelegt ist. Klicken Sie auf **Erstellen**.

So erstellen Sie die simulierten nachgeschalteten Geräte:

1. Wählen Sie auf der Seite **Geräte** in der Liste mit den Gerätevorlagen die Vorlage **RS40 Occupancy Sensor** aus.

1. Wählen Sie **+ Neu** aus, um mit dem Hinzufügen eines neuen Geräts zu beginnen.

1. Behalten Sie die generierten Werte für **Geräte-ID** und **Gerätename** bei. Stellen Sie sicher, dass die Option **Simuliert** auf **Ein** festgelegt ist. Klicken Sie auf **Erstellen**.

1. Wählen Sie auf der Seite **Geräte** in der Liste mit den Gerätevorlagen die Vorlage **S1 Sensor** aus.

1. Wählen Sie **+ Neu** aus, um mit dem Hinzufügen eines neuen Geräts zu beginnen.

1. Behalten Sie die generierten Werte für **Geräte-ID** und **Gerätename** bei. Stellen Sie sicher, dass die Option **Simuliert** auf **Ein** festgelegt ist. Klicken Sie auf **Erstellen**.

![Simulierte Geräte in Ihrer Anwendung](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Hinzufügen von Beziehungen mit nachgeschalteten Geräten zu einem Gatewaygerät

Nachdem die simulierten Geräte nun in Ihrer Anwendung vorhanden sind, können Sie als Nächstes die Beziehungen zwischen den nachgeschalteten Geräten und dem Gatewaygerät erstellen:

1. Wählen Sie auf der Seite **Geräte** in der Liste mit den Gerätevorlagen die Vorlage **S1 Sensor** und anschließend Ihr simuliertes Gerät **S1 Sensor** aus.

1. Wählen Sie **An Gateway anfügen** aus.

1. Wählen Sie im Dialogfeld **An Gateway anfügen** die Vorlage **Smart Building gateway device** und anschließend die simulierte Instanz aus, die Sie zuvor erstellt haben.

1. Wählen Sie **Anfügen** aus.

1. Wählen Sie auf der Seite **Geräte** in der Liste mit den Gerätevorlagen die Vorlage **RS40 Occupancy Sensor** und anschließend Ihr simuliertes Gerät **RS40 Occupancy Sensor** aus.

1. Wählen Sie **Connect to gateway** (Mit Gateway verbinden) aus.

1. Wählen Sie im Dialogfeld **Connect to a gateway** (Verbindung mit einem Gateway herstellen) die Vorlage **Smart Building gateway device** und anschließend die simulierte Instanz aus, die Sie zuvor erstellt haben.

1. Wählen Sie **Anfügen** aus.

Beide simulierten nachgeschalteten Geräte sind nun mit Ihrem simulierten Gatewaygerät verbunden. In der Ansicht **Nachgeschaltete Geräte** für Ihr Gatewaygerät werden die zugehörigen nachgeschalteten Geräte angezeigt:

![Ansicht „Nachgeschaltete Geräte“](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

## <a name="connect-real-downstream-devices"></a>Verbinden von realen nachgeschalteten Geräten

Anhand des Beispielcodes im Tutorial [Erstellen und Verbinden einer Clientanwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md) sehen Sie, wie Sie die Modell-ID aus der Gerätevorlage in die vom Gerät gesendeten Bereitstellungsnutzdaten einfügen. Über die Modell-ID kann IoT Central dem Gerät die richtige Gerätevorlage zuordnen. Beispiel:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
  provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
    provisioning_host=provisioning_host,
    registration_id=registration_id,
    id_scope=id_scope,
    symmetric_key=symmetric_key,
  )

  provisioning_device_client.provisioning_payload = {"modelId": model_id}
  return await provisioning_device_client.register()
```

Wenn Sie eine Verbindung mit einem nachgeschalteten Gerät herstellen, können Sie die Bereitstellungsnutzdaten so ändern, dass sie die ID des Gatewaygeräts enthalten. Über die Modell-ID kann IoT Central dem Gerät die richtige Vorlage des nachgeschalteten Geräts zuordnen. Mit der Gateway-ID kann IoT Central die Beziehung zwischen dem nachgeschalteten Gerät und dem Gateway einrichten. In diesem Fall sehen die vom Gerät gesendeten Bereitstellungsnutzdaten wie der folgende JSON-Code aus:

```json
{
  "modelId": "dtmi:rigado:S1Sensor;2",
  "iotcGateway":{
    "iotcGatewayId": "gateway-device-001"
  }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Erstellen eines neuen IoT-Gateways als Gerätevorlage
* Erstellen von Cloudeigenschaften
* Erstellen von Anpassungen
* Definieren einer Visualisierung für die Gerätetelemetriedaten
* Hinzufügen von Beziehungen
* Veröffentlichen Ihrer Gerätevorlage

Als Nächstes können Sie das folgende Tutorial durcharbeiten:

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen eines Azure IoT Edge-Geräts zu Ihrer Azure IoT Central-Anwendung](tutorial-add-edge-as-leaf-device.md)
