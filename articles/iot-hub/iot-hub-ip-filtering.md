---
title: Azure IoT Hub – IP-Filter | Microsoft Docs
description: Informieren Sie sich, wie Sie die IP-Filterung zum Zulassen von Verbindungen von bestimmten IP-Adressen für Ihren Azure IoT-Hub verwenden.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jlian
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b984b7ee718a051456f8f51e5c9a49c247b884b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040554"
---
# <a name="use-ip-filters"></a>Verwenden von IP-Filtern

Die Sicherheit ist bei jeder IoT-Lösung, die auf Azure IoT Hub basiert, ein wichtiger Aspekt. Manchmal müssen Sie im Rahmen der Sicherheitskonfiguration die IP-Adressen explizit angeben, über die Geräte eine Verbindung herstellen können. Mit dem Feature *IP-Filter* können Sie Regeln konfigurieren, mit denen Datenverkehr von bestimmten IPv4-Adressen abgelehnt oder zugelassen wird.

## <a name="when-to-use"></a>Verwendung

Mithilfe von „IP-Filter“ können Sie Datenverkehr nur aus einem bestimmten IP-Adressbereich empfangen und jeglichen anderen Datenverkehr ablehnen. Ein Beispiel hierfür: Sie verwenden Ihren IoT-Hub mit [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), um private Verbindungen zwischen einem IoT-Hub und Ihrer lokalen Infrastruktur zu erstellen.

## <a name="default-setting"></a>Standardeinstellung

Wenn Sie die Seite mit den IP-Filtereinstellungen aufrufen möchten, wählen Sie **Netzwerk**, **Öffentlicher Zugriff** und dann **Ausgewählte IP-Bereiche** aus:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Screenshot: Festlegen der Standardeinstellungen für IP-Filter":::

Das Raster **IP-Filter** im Portal ist für ein IoT Hub standardmäßig leer. Diese Standardeinstellung bedeutet, dass Ihr Hub Verbindungen von allen IP-Adressen blockiert. Die Standardeinstellung entspricht einer Regel, mit der der IP-Adressbereich `0.0.0.0/0` blockiert wird.

## <a name="add-or-edit-an-ip-filter-rule"></a>Hinzufügen oder Bearbeiten einer IP-Filterregel

Zum Hinzufügen einer IP-Filterregel wählen Sie **+ IP-Filterregel hinzufügen** aus. Um die IP-Adresse Ihres Computers schnell hinzuzufügen, klicken Sie auf **Client-IP-Adresse hinzufügen**. 

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Screenshot: Hinzufügen einer IP-Filterregel zu einem IoT Hub":::

Nachdem Sie **IP-Filterregel hinzufügen** ausgewählt haben, füllen Sie die Felder aus. Diese Felder werden vorab für Sie ausgefüllt, wenn Sie die Option zum Hinzufügen Ihrer Client-IP-Adresse ausgewählt haben.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Screenshot, der zeigt, was nach dem Hinzufügen einer IP-Filterregel zu tun ist":::

* Geben Sie einen **Namen** für die IP-Filterregel an. Dieser Name muss eine eindeutige alphanumerische Zeichenfolge ohne Beachtung von Groß-/Kleinschreibung sein, die bis zu 128 Zeichen umfassen kann. Nur alphanumerische ASCII 7-Bit-Zeichen und die Zeichen `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sind zulässig.

* Geben Sie eine einzelne IPv4-Adresse oder einen Block von IP-Adressen in CIDR-Notation ein. In CIDR-Notation steht 192.168.100.0/22 beispielsweise für die 1.024 IPv4-Adressen von 192.168.100.0 bis 192.168.103.255.

Nachdem Sie die Felder ausgefüllt haben, wählen Sie **Speichern** aus, um die Regel zu speichern. Daraufhin wird eine Warnung mit dem Hinweis angezeigt, dass der Updatevorgang läuft.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Screenshot mit Benachrichtigung zum Speichern einer IP-Filterregel":::

Die Option **Hinzufügen** ist deaktiviert, wenn Sie das Maximum von 10 IP-Filterregeln erreichen.

Wenn Sie eine vorhandene Regel bearbeiten möchten, wählen Sie die zu ändernden Daten aus, nehmen Sie die Änderung vor, und wählen Sie **Speichern** aus, um Ihre Bearbeitung zu speichern.

## <a name="delete-an-ip-filter-rule"></a>Löschen einer IP-Filterregel

Wählen Sie zum Löschen einer IP-Filterregel das Papierkorbsymbol in dieser Zeile und dann **Speichern** aus. Die Regel wird entfernt und die Änderung gespeichert.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Screenshot: Löschen einer IoT Hub IP-Filterregel":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Anwenden von IP-Filterregeln auf den integrierten Event Hub-kompatiblen Endpunkt

Wenn Sie die IP-Filterregeln auf den integrierten Event Hub-kompatiblen Endpunkt anwenden möchten, aktivieren Sie das Kontrollkästchen neben **Apply IP filters to the built-in endpoint?** (IP-Filterregeln auf den integrierten Event Hub-kompatiblen Endpunkt anwenden?), und wählen Sie **Speichern** aus.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Screenshot: Umschaltfläche für den integrierten Endpunkt":::

> [!NOTE]
> Diese Option ist für kostenlose IoT-Hubs (F1) nicht verfügbar. Wenn Sie IP-Filterregeln auf den integrierten Endpunkt anwenden möchten, verwenden Sie einen kostenpflichtigen IoT-Hub.

Durch Aktivierung dieser Option werden Ihre IP-Filterregeln auf den integrierten Endpunkt repliziert, sodass nur vertrauenswürdige IP-Adressbereiche darauf zugreifen können.

Wenn Sie diese Option deaktivieren, ist der Zugriff auf den integrierten Endpunkt für alle IP-Adressen möglich. Dieses Verhalten kann nützlich sein, wenn Sie aus dem Endpunkt mit Diensten mit sich ändernden IP-Adressen (z. B. Azure Stream Analytics) lesen möchten. 

## <a name="how-filter-rules-are-applied"></a>Anwenden von Filterregeln

Die IP-Filterregeln werden auf IoT Hub-Dienstebene angewendet. Daher gelten die IP-Filterregeln für alle Verbindungen von Geräten und Back-End-Apps mit allen unterstützten Protokollen. Außerdem können Sie auswählen, ob der [integrierte Event Hub-kompatible Endpunkt](iot-hub-devguide-messages-read-builtin.md) (nicht über die IoT Hub-Verbindungszeichenfolge) an diese Regeln gebunden werden soll. 

Bei jedem Verbindungsversuch von einer IP-Adresse, die nicht explizit zulässig ist, wird ein nicht autorisierter Statuscode 401 und eine Beschreibung empfangen. In der Antwortnachricht wird die IP-Regel nicht erwähnt. Das Ablehnen von IP-Adressen kann andere Azure-Dienste wie z. B. Azure Stream Analytics, Azure Virtual Machines oder den Geräte-Explorer im Azure-Portal an der Interaktion mit dem IoT-Hub hindern.

> [!NOTE]
> Wenn Sie Azure Stream Analytics (ASA) verwenden müssen, um Nachrichten aus einem IoT-Hub mit aktiviertem IP-Filter zu lesen, **deaktivieren** Sie die Option **Apply IP filters to the built-in endpoint** (IP-Filter auf den integrierten Endpunkt anwenden). Verwenden Sie dann den Event Hub-kompatiblen Namen und Endpunkt Ihres IoT-Hubs zum manuellen Hinzufügen einer [Event Hubs-Streameingabe](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) in ASA.

### <a name="ordering"></a>Sortieren

IP-Filterregeln sind Regeln für *Zulassen* und werden unsortiert angewendet. Nur von Ihnen hinzugefügte IP-Adressen dürfen eine Verbindung mit IoT Hub herstellen. 

Wenn Sie beispielsweise Adressen im Bereich `192.168.100.0/22` zulassen und alle anderen Adressen ablehnen möchten, müssen Sie nur eine einzige Regel mit dem Adressbereich `192.168.100.0/22` im Raster hinzufügen.

### <a name="azure-portal"></a>Azure-Portal 

IP-Filterregeln werden auch bei der Verwendung von IoT Hub über das Azure-Portal angewendet. Das liegt daran, dass API-Aufrufe des IoT Hub-Dienst direkt über Ihren Browser mit Ihren Anmeldeinformationen ausgeführt werden, was anderen Azure-Diensten entspricht. Fügen Sie die IP-Adresse Ihres Computers der Positivliste hinzu, um bei aktiviertem IP-Adressfilter über das Azure-Portal auf IoT Hub zuzugreifen. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Abrufen und Aktualisieren von IP-Filtern über die Azure-Befehlszeilenschnittstelle

Die IP-Filter für Ihren IoT Hub können über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) abgerufen und aktualisiert werden.

Wenn Sie die aktuellen IP-Filter für Ihren IoT Hub abrufen möchten, führen Sie Folgendes aus:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Damit wird ein JSON-Objekt zurückgegeben, in dem Ihre vorhandenen IP-Filter unter dem Schlüssel `properties.networkRuleSets` aufgelistet sind:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Wenn Sie einen neuen IP-Filter für Ihren IoT Hub hinzufügen möchten, führen Sie aus:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Wenn Sie einen vorhandenen IP-Filter in Ihrem IoT Hub entfernen möchten, führen Sie aus:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Hier muss `<ipFilterIndexToRemove>` der Reihenfolge von IP-Filtern in `properties.networkRuleSets.ipRules` Ihres IoT Hubs entsprechen.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Abrufen und Aktualisieren von IP-Filtern über Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Die IP-Filter für Ihren IoT Hub können über [Azure PowerShell](/powershell/azure/) abgerufen und festgelegt werden.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualisieren von IP-Filterregeln mit REST


Möglicherweise müssen Sie den IP-Filter für Ihren IoT Hub auch über den REST-Endpunkt des Azure-Ressourcenanbieters abrufen und ändern. Lesen Sie dazu `properties.networkRuleSets` unter [createorupdate-Methode](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Außerbetriebnahme des IP-Filters (klassisch)

Der klassische IP-Filter wurde außer Betrieb genommen. Weitere Informationen finden Sie unter [IoT Hub classic IP filter and how to upgrade](iot-hub-ip-filter-classic.md) (IoT Hub – Klassischer IP-Filter und Aktualisieren).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [IoT Hub-Metriken](./monitor-iot-hub.md)
* [IoT Hub-Unterstützung für virtuelle Netzwerke mit Private Link und verwalteter Identität](virtual-network-support.md)
* [Verwalten des Zugriffs über öffentliche Netzwerke für Ihren IoT-Hub](iot-hub-public-network-access.md)
* [Überwachen von IoT Hub](monitor-iot-hub.md)
