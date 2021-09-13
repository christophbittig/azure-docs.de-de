---
title: Konfigurieren von Regeln und Aktionen in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Gewusst-wie-Artikel wird gezeigt, wie Sie als Ersteller telemetriebasierte Regeln und Aktionen in Ihrer Azure IoT Central-Anwendung konfigurieren.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355771"
---
# <a name="configure-rules"></a>Konfigurieren von Regeln

Regeln in IoT Central dienen als anpassbares Antworttool, das bei aktiv überwachten Ereignisse von verbundenen Geräten ausgelöst wird. In den folgenden Abschnitten wird beschrieben, wie Regeln ausgewertet werden. Sie können eine oder mehrere Aktionen definieren, die ausgeführt werden sollen, wenn eine Regel ausgelöst wird. In diesem Artikel werden E-Mails, Webhooks und Azure Monitor-Aktionsgruppen beschrieben. Informationen zu den anderen Aktionstypen finden Sie unter [Integrieren Ihrer Azure IoT Central-Anwendung in andere Clouddienste mithilfe von Workflows](howto-configure-rules-advanced.md).

## <a name="select-target-devices"></a>Auswählen von Zielgeräten

Wählen Sie über den Abschnitt „Zielgeräte“ aus, auf welche Art von Geräten die jeweilige Regel angewendet werden soll. Mithilfe von Filtern können Sie weiter verfeinern, welche Geräte einbezogen werden sollen. Die Filter verwenden Eigenschaften in der Gerätevorlage zum Filtern der Gruppe von Geräten. Filter selbst lösen keine Aktion aus. Im nachstehenden Screenshot sind die Zielgeräte vom Gerätevorlagentyp **Refrigerator** (Kühlschrank). Der Filter gibt an, dass die Regel nur **Refrigerators** (Kühlschränke) enthalten soll, deren Eigenschaft **Manufactured State** (Staat der Fertigung) gleich **Washington** ist.

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="Screenshot: Bedingungsdefinition":::

## <a name="use-multiple-conditions"></a>Verwenden von mehreren Bedingungen

Bedingungen sind die Grundlage für das Auslösen von Regeln. Sie können einer Regel mehrere Bedingungen hinzufügen und angeben, ob die Regel ausgelöst werden soll, wenn alle Bedingungen wahr sind oder wenn mindestens eine beliebige Bedingung wahr ist.  

Im nachstehenden Screenshot wird mit den Bedingungen überprüft, wann die Temperatur höher als 70&deg;F und die Luftfeuchtigkeit niedriger als 10 ist. Wenn eine oder mehrere dieser Anweisungen wahr sind, wird die Regel als TRUE ausgewertet und löst eine Aktion aus.

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="Der Screenshot zeigt einen Kühlschrankmonitor mit Bedingungen, die für Temperatur und Luftfeuchtigkeit angegeben wurden.":::

> [!NOTE]
> Derzeit werden nur Telemetriebedingungen unterstützt.  

### <a name="use-a-cloud-property-in-a-value-field"></a>Verwenden einer Cloudeigenschaft in einem Wertefeld

Sie können im Feld **Wert** für eine Bedingung von der Gerätevorlage auf eine Cloudeigenschaft verweisen. Die Cloudeigenschaft und der Telemetriewert müssen vom gleichen Typ sein. Wenn z. B. **Temperatur** ein Double-Wert ist, werden nur die Cloudeigenschaften vom Typ Double als Optionen in der Dropdownliste **Wert** angezeigt.

Wenn Sie einen Telemetriewert von einem Ereignistyp auswählen, enthält die Dropdownliste **Wert** die Option **Beliebig**. Die Option **Beliebig** bedeutet, dass die Regel ausgelöst wird, wenn die Anwendung ein Ereignis dieses Typs empfängt, unabhängig von der Nutzlast.

## <a name="use-aggregate-windowing"></a>Verwenden von aggregiertem Windowing

Sie können eine zeitliche Aggregation angeben, damit Ihre Regel basierend auf einem Zeitfenster ausgelöst wird. Regelbedingungen werten aggregierte Zeitfenster für Telemetriedaten als rollierende Fenster aus. Wenn die Regel Eigenschaftenfilter enthält, werden diese am Ende des Zeitfensters angewendet. Im nachstehenden Screenshot beträgt das Zeitfenster fünf Minuten. Die Regel wertet alle fünf Minuten die Telemetriedaten der letzten fünf Minuten aus. Die Daten werden nur einmal in dem für sie zutreffenden Fenster ausgewertet.

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="Diagramm: Definition rollierender Fenster":::

## <a name="create-an-email-action"></a>Erstellen einer E-Mail-Aktion

Wenn Sie eine E-Mail-Aktion erstellen, muss es sich bei der E-Mail-Adresse um eine **Benutzer-ID** in der Anwendung handeln, und der Benutzer muss sich mindestens einmal bei der Anwendung angemeldet haben. Sie können auch einen Hinweis angeben, der in der E-Mail angezeigt werden soll. IoT Central zeigt ein Beispiel, wie diese E-Mail aussieht, wenn die Regel ausgelöst wird:

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="Screenshot: E-Mail-Aktion für eine Regel":::

## <a name="create-a-webhook-action"></a>Erstellen einer Webhookaktion

Mit Webhooks können Sie Ihre IoT Central-App zur Remoteüberwachung und Benachrichtigung mit anderen Anwendungen und Diensten verbinden. Durch Webhooks werden automatisch andere Anwendungen und Dienste benachrichtigt, die Sie verbinden, wenn eine Regel in Ihrer IoT Central-App ausgelöst wird. Ihre IoT Central-App sendet eine POST-Anforderung an den HTTP-Endpunkt der anderen Anwendung, sobald eine Regel ausgelöst wird. Die Nutzlast enthält Details zu Gerät und Regeltriggern.

In diesem Beispiel stellen Sie eine Verbindung mit *RequestBin* her, damit Sie benachrichtigt werden, wenn eine Regel ausgelöst wird:

1. Öffnen Sie [RequestBin](https://requestbin.net/).

1. Erstellen Sie ein neues RequestBin-Element, und kopieren Sie die **Bin-URL**.

1. Fügen Sie der Regel eine Aktion hinzu:

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="Screenshot: Bildschirm für die Erstellung eines Webhooks":::

1. Wählen Sie die Webhookaktion aus, geben Sie einen Anzeigenamen an, und fügen Sie die RequestBin-URL als **Rückruf-URL** ein.

1. Speichern Sie die Regel.

Wenn die Regel jetzt ausgelöst wird, wird eine neue Anforderung in RequestBin angezeigt.

### <a name="payload"></a>Nutzlast

Wenn eine Regel ausgelöst wird, sendet sie eine HTTP-POST-Anforderung an die Rückruf-URL. Die Anforderung enthält eine JSON-Payload mit Informationen zu Telemetrie, Gerät, Regel und Anwendung. Die Payload sieht ungefähr wie der folgende JSON-Codeausschnitt aus:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

Wenn die Regel aggregierte Telemetriedaten über einen bestimmten Zeitraum überwacht, enthält die Payload einen Telemetrieabschnitt, der ungefähr so aussieht:

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>Änderungshinweis zum Datenformat

Wenn Sie vor dem **3. April 2020** Webhooks erstellt und gespeichert haben, löschen Sie diese, und erstellen Sie neue. Ältere Webhooks verwenden ein Payloadformat, das als veraltet markiert ist:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>Erstellen einer Azure Monitor-Gruppenaktion

In diesem Abschnitt wird die Verwendung von [Azure Monitor](../../azure-monitor/overview.md)-*Aktionsgruppen* zum Anfügen mehrerer Aktionen an eine IoT Central-Regel erläutert. Sie können eine Aktionsgruppe an mehrere Regeln anfügen. Eine [Aktionsgruppe](../../azure-monitor/alerts/action-groups.md) ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden.

Sie können [Aktionsgruppen im Azure-Portal erstellen und verwalten](../../azure-monitor/alerts/action-groups.md) oder eine [Azure Resource Manager-Vorlage](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md) verwenden.

Eine Aktionsgruppe hat folgende Funktionen:

- Senden von Benachrichtigungen (z.B. per E-Mail oder SMS) oder Tätigen von Sprachanrufen
- Ausführen von Aktionen, z.B. das Aufrufen eines Webhooks

Auf dem folgenden Screenshot ist eine Aktionsgruppe dargestellt, die Benachrichtigungen per E-Mail und SMS versendet und einen Webhook aufruft:

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="Screenshot: Aktionsgruppe im Azure-Portal":::

Wenn Sie eine Aktionsgruppe in einer IoT Central-Regel verwenden möchten, muss diese sich im selben Azure-Abonnement wie die IoT Central-Anwendung befinden.

Wenn Sie der Regel in IoT Central eine Aktion hinzufügen, wählen Sie **Azure Monitor-Aktionsgruppen** aus.

Wählen Sie eine Aktionsgruppe aus Ihrem Azure-Abonnement aus:

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="Screenshot: Aktionsgruppe in einer IoT Central-Regel":::

Wählen Sie **Speichern** aus. Die Aktionsgruppe wird nun in der Liste der Aktionen angezeigt, die ausgeführt werden sollen, wenn die Regel ausgelöst wird.

In der folgenden Tabelle werden die Informationen zusammengefasst, die an die unterstützten Aktionstypen gesendet werden:

| Aktionstyp | Ausgabeformat |
| ----------- | -------------- |
| Email       | IoT Central-Standardvorlage für E-Mails |
| SMS         | Azure IoT Central-Warnung: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} (${applicationName} - "${ruleName}" wurde für "${deviceName}" um ${triggerDate} ${triggerTime} ausgelöst.) |
| Sprache       | Azure I.O.T Central-Warnung: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} (Regel "${ruleName}" wurde auf dem Gerät "${deviceName}" um ${triggerDate} ${triggerTime}, in der Anwendung ${applicationName} ausgelöst.) |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

Der folgende Text ist ein Beispiel für eine SMS einer Aktionsgruppe:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>Verwenden von Regeln bei IoT Edge-Modulen

Für Regeln, die auf IoT Edge-Module angewendet werden, gilt eine Einschränkung. Regeln für Telemetriedaten aus verschiedenen Modulen werden nicht als gültige Regeln ausgewertet. Sehen Sie sich das folgende Beispiel an: Die erste Bedingung der Regel bezieht sich auf Temperaturtelemetriedaten aus Modul A. Die zweite Bedingung der Regel bezieht sich auf Luftfeuchtigkeitstelemetriedaten aus Modul B. Da die beiden Bedingungen aus unterschiedlichen Modulen stammen, handelt es sich hier um eine ungültige Gruppe von Bedingungen. Die Regel ist nicht gültig und gibt einen Fehler aus, wenn Sie versuchen, sie zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie eine Regel in der Azure IoT Central-Anwendung konfigurieren, können Sie erfahren, wie Sie mit Power Automate oder Azure Logic Apps [erweiterte Regeln konfigurieren](howto-configure-rules-advanced.md).
