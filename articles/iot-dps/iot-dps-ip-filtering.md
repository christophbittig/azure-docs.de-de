---
title: IP-Verbindungsfilter in Microsoft Azure IoT DPS
description: Informieren Sie sich, wie Sie die IP-Filterung zum Blockieren von Verbindungen von bestimmten IP-Adressen zu Ihrer Azure IoT DPS-Instanz verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.service: iot-dps
services: iot-dps
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 9354b1e3bfc57951cff919b0c14e3bc950939d8f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485322"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Verwenden von IP-Verbindungsfiltern in Azure IoT DPS

Sicherheit ist ein wichtiger Aspekt jeder IoT-Lösung. Manchmal müssen Sie im Rahmen der Sicherheitskonfiguration die IP-Adressen explizit angeben, über die Geräte eine Verbindung herstellen können. Mit dem Feature *IP-Filter* für einen Azure IoT Hub Device Provisioning-Dienst (Device Provisioning Service, DPS) können Sie Regeln zur Ablehnung oder Akzeptierung des Datenverkehrs von bestimmten IPv4-Adressen konfigurieren.

## <a name="when-to-use"></a>Verwendung

Es gibt zwei spezielle Anwendungsfälle, in denen es nützlich ist, Verbindungen von bestimmten IP-Adressen zu einem DPS-Endpunkt zu blockieren:

* Ihr DPS sollte Datenverkehr nur aus einem bestimmten IP-Adressbereich empfangen und jeglichen anderen Datenverkehr ablehnen. Beispielsweise verwenden Sie Ihren DPS bei [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), um private Verbindungen zwischen einem DPS und Ihren Geräten zu erstellen.

* Sie müssen Datenverkehr von IP-Adressen ablehnen, die vom DPS-Administrator als verdächtig eingestuft wurden.

>[!Note]
>Wenn die IP-Filterung aktiviert ist, können Sie im Azure-Portal keine Dienstvorgänge mehr ausführen (d. h. Registrierungen verwalten). Um Dienstvorgänge über das Portal auszuführen, müssen Sie die IP-Filterung vorübergehend deaktivieren, Ihre Arbeit abschließen und dann die IP-Filterfunktion erneut aktivieren. Wenn Sie Ihre eigenen Clients verwenden und die Deaktivierung der IP-Filterung vermeiden möchten, können Sie die IP-Adresse Ihres Computers in `ipFilterRules` hinzufügen und die Registrierungen im DPS über die Befehlszeilenschnittstelle verwalten.

## <a name="how-filter-rules-are-applied"></a>Anwenden von Filterregeln

Die IP-Filterregeln werden auf DPS-Instanzebene angewendet. Daher gelten die IP-Filterregeln für alle Verbindungen von Geräten und Back-End-Apps mit allen unterstützten Protokollen.

Jeder Verbindungsversuch über eine IP-Adresse, der mit einer IP-Ablehnungsregel in Ihrer DPS-Instanz übereinstimmt, wird mit dem Statuscode „Unauthorized 401“ (401 – Nicht autorisiert) und einer Beschreibung versehen. In der Antwortnachricht wird die IP-Regel nicht erwähnt.

> [!IMPORTANT]
> Durch die Ablehnung von IP-Adressen lässt sich verhindern, dass andere Azure-Dienste mit der DPS-Instanz interagieren.

## <a name="default-setting"></a>Standardeinstellung

Standardmäßig ist die IP-Filterung deaktiviert, und **Öffentlicher Netzwerkzugriff** ist auf *Alle Netzwerke* festgelegt. Diese Standardeinstellung bedeutet, dass Ihr DPS Verbindungen von beliebigen IP-Adressen akzeptiert oder dass eine Regel angewandt wird, die den IP-Adressbereich 0.0.0.0/0 akzeptiert.

:::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-default.png" alt-text="Standardeinstellungen der IP-Filterung von IoT DPS":::

## <a name="add-an-ip-filter-rule"></a>Hinzufügen einer IP-Filterregel

So fügen Sie eine IP-Filterregel hinzu

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

4. Wählen Sie im Menü **Einstellungen** auf der linken Seite *Netzwerk* aus.

5. Wählen Sie unter **Öffentlicher Netzwerkzugriff** die Option *Ausgewählte IP-Adressbereiche* aus.

6. Wählen Sie **+ IP-Filterregel hinzufügen** aus.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-add-rule.png" alt-text="Hinzufügen einer IP-Filterregel in IoT DPS":::

7. Füllen Sie die folgenden Felder aus:

    | Feld | Beschreibung|
    |-------|------------|
    | **Name** |Eine eindeutige alphanumerische Zeichenfolge ohne Beachtung von Groß-/Kleinschreibung mit maximal 128 Zeichen. Nur alphanumerische ASCII 7-Bit-Zeichen und die Zeichen `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sind zulässig.|
    | **Adressbereich** |Eine einzelne IPv4-Adresse oder einen Block von IP-Adressen in CIDR-Notation. In CIDR-Notation steht 192.168.100.0/22 beispielsweise für die 1.024 IPv4-Adressen von 192.168.100.0 bis 192.168.103.255.|
    | **Aktion** |Wählen Sie **Zulassen** oder **Verweigern** aus.|

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Nach dem Auswählen von „IP-Filterregel hinzufügen“":::

8. Wählen Sie **Speichern** aus. Daraufhin sollte eine Warnung mit dem Hinweis angezeigt werden, dass der Updatevorgang läuft.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png" alt-text="Benachrichtigung über das Speichern einer IP-Filterregel":::

    >[!Note]
    > Die Option **+ IP-Filterregel hinzufügen** ist deaktiviert, wenn Sie das Maximum von 100 IP-Filterregeln erreichen.

## <a name="edit-an-ip-filter-rule"></a>Bearbeiten einer IP-Filterregel

So bearbeiten Sie eine vorhandene Regel

1. Wählen Sie die IP-Filterregeldaten aus, die Sie ändern möchten.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-rule-edit.png" alt-text="Bearbeiten einer IP-Filterregel":::

2. Nehmen Sie die Änderungen vor.

3. Wählen Sie **Speichern** aus.

## <a name="delete-an-ip-filter-rule"></a>Löschen einer IP-Filterregel

So löschen Sie eine IP-Filterregel

1. Wählen Sie das Löschsymbol in der Zeile der IP-Regel aus, die Sie löschen möchten.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-delete-rule.png" alt-text="Löschen einer IP-Filterregel in IoT DPS":::

2. Wählen Sie **Speichern** aus.

## <a name="ip-filter-rule-evaluation"></a>Auswertung von IP-Filterregeln

IP-Filterregeln werden in einer bestimmten Reihenfolge angewandt. Die erste Regel, die der IP-Adresse entspricht, bestimmt die Aktion zum Annehmen oder Verweigern.

Wenn Sie beispielsweise Adressen im Bereich 192.168.100.0/22 zulassen und alle anderen Adressen ablehnen möchten, sollte die erste Regel im Raster lauten, dass der Adressbereich 192.168.100.0/22 zulässig ist. Mit der nächsten Regel sollten alle Adressen abgelehnt werden, indem der Bereich 0.0.0.0/0 verwendet wird.

So ändern Sie die Reihenfolge Ihrer IP-Filterregeln

1. Wählen Sie die Regel aus, die Sie verschieben möchten.

2. Ziehen Sie die Regel per Drag & Drop an die gewünschte Position.

3. Wählen Sie **Speichern** aus.

## <a name="update-ip-filter-rules-using-azure-resource-manager-templates"></a>Aktualisieren von IP-Filterregeln mithilfe von Azure Resource Manager-Vorlagen

Es gibt zwei Möglichkeiten, Ihren DPS-IP-Filter zu aktualisieren:

1. Rufen Sie die Methode der IoT Hub-Ressourcenanbieter-REST-API auf. Informationen zum Aktualisieren Ihrer IP-Filterregeln mithilfe von REST finden Sie unter `IpFilterRule` im Abschnitt [Definitionen](/api/iothub/iot-hub-resource/update#definitions) der [IoT Hub-Ressource: Updatemethode](/api/iothub/iot-hub-resource/update).

2. Verwenden Sie die Azure Resource Manager-Vorlagen. Unter [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) finden Sie Leitfäden zur Verwendung von Resource Manager-Vorlagen. In den folgenden Beispielen wird gezeigt, wie Sie DPS-IP-Filterregeln mit Azure Resource Manager-Vorlagen erstellen, bearbeiten und löschen.

    >[!NOTE]
    >Die Aktualisierung von DPS-IP-Filterregeln mithilfe der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell wird derzeit nicht unterstützt.

### <a name="add-an-ip-filter-rule"></a>Hinzufügen einer IP-Filterregel

Im folgenden Vorlagenbeispiel wird die neue IP-Filterregel „AllowAll“ (AlleZulassen) erstellt, die den gesamten Datenverkehr akzeptiert.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Aktualisieren Sie die Attribute für die IP-Filterregel der Vorlage aufgrund Ihrer Anforderungen.

| attribute                | BESCHREIBUNG |
| ------------------------ | ----------- |
| **FilterName**           | Geben Sie einen Namen für die IP-Filterregel an. Er muss eine eindeutige alphanumerische Zeichenfolge ohne Beachtung von Groß-/Kleinschreibung sein, die bis zu 128 Zeichen umfassen kann. Nur die alphanumerischen ASCII-7-Bit-Zeichen sowie `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` werden akzeptiert. |
| **Aktion**               | Akzeptierte Werte sind **Accept**  (Akzeptieren) oder  **Reject**  (Ablehnen) als Aktion für die IP-Filterregel. |
| **ipMask**               | Geben Sie eine einzelne IPv4-Adresse oder einen Block von IP-Adressen in CIDR-Notation ein. In CIDR-Notation steht 192.168.100.0/22 beispielsweise für die 1.024 IPv4-Adressen von 192.168.100.0 bis 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualisieren einer IP-Filterregel

Im folgenden Vorlagenbeispiel wird die oben gezeigte IP-Filterregel „AllowAll“ aktualisiert, um den gesamten Datenverkehr abzulehnen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Löschen einer IP-Filterregel

Im folgenden Vorlagenbeispiel werden alle IP-Filterregeln für die DPS-Instanz gelöscht.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von DPS finden Sie unter:

* [Understanding IoT DPS IP addresses](iot-dps-understand-ip-address.md) (Informationen zu IP-Adressen für IoT DPS)
* [Einrichten von DPS über die Azure CLI](quick-setup-auto-provision-cli.md)
* [Steuern des Zugriffs auf DPS](how-to-control-access.md)
