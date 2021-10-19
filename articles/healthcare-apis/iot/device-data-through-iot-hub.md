---
title: Empfangen von Gerätedaten über Azure IoT Hub – Azure Healthcare-APIs
description: In diesem Tutorial erfahren Sie, wie Sie das Gerätedatenrouting von IoT Hub zum FHIR-Dienst über den IoT-Connector aktivieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: fe478235897b855d079241192108f5d5ebd70fe0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992977"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutorial: Empfangen von Gerätedaten über Azure IoT Hub

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector bietet Ihnen die Möglichkeit, Daten von IoMT-Geräten (Internet of Medical Things) im dienst Fast Healthcare Interoperability Resources (FHIR&#174;) zu erfassen. IoT Connector kann auch mit Geräten verwendet werden, die über Azure IoT Hub bereitgestellt und verwaltet werden. In diesem Tutorial erfahren Sie, wie Sie eine Verbindung herstellen und Gerätedaten von Azure IoT Hub an IoT Connector weiterleiten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. ([Kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- FHIR-Dienstressource mit mindestens einem IoT-Connector: [Bereitstellen des IoT-Connectors mit Azure-Portal](deploy-iot-connector-in-azure.md)
- Azure IoT Hub-Ressource, die mit mindestens einem echten oder simulierten Gerät verbunden ist. Weitere Informationen finden Sie unter [Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub und Lesen der Telemetriedaten mit einer Back-End-Anwendung (.NET)](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp).

> [!TIP]
> Bei Verwendung einer simulierten Azure IoT Hub-Geräteanwendung können Sie aus verschiedenen unterstützten Sprachen und Systemen die gewünschte Anwendung auswählen.

## <a name="get-connection-string-for-iot-connector"></a>Abrufen der Verbindungszeichenfolge für den IoT-Connector

Azure IoT Hub erfordert eine Verbindungszeichenfolge, um eine sichere Verbindung mit Ihrem IoT-Connector herzustellen. Erstellen Sie eine neue Verbindungszeichenfolge für Ihren IoT-Connector, wie unter [Generieren einer Verbindungszeichenfolge](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string)beschrieben. Tresor diese Verbindungszeichenfolge, die im nächsten Schritt verwendet werden soll.

Von IoT Connector wird im Hintergrund eine Azure Event Hub-Instanz verwendet, um Gerätenachrichten zu empfangen. Die oben erstellte Verbindungszeichenfolge ist im Grunde die Verbindungszeichenfolge für diesen zugrunde liegenden Event Hub.

## <a name="connect-azure-iot-hub-with-iot-connector"></a>Verbinden Azure IoT Hub mit IoT-Connector

Azure IoT Hub unterstützt das so genannte [Nachrichtenrouting](../../iot-hub/iot-hub-devguide-messages-d2c.md). Mit diesem Feature können Gerätedaten an verschiedene Azure-Dienste wie Event Hub, Speicherkonto und Service Bus gesendet werden. Der IoT-Connector verwendet dieses Feature, um eine Verbindung herzustellen und Gerätedaten von Azure IoT Hub an seinen Event Hub-Endpunkt zu senden.

> [!NOTE] 
> Derzeit können Sie nur PowerShell oder den CLI-Befehl zum Erstellen des [Nachrichtenroutings](../../iot-hub/tutorial-routing.md) verwenden, da der Event Hub des IoT-Connectors nicht im Kundenabonnement gehostet wird und daher nicht über die Azure-Portal für Sie sichtbar ist. Nachdem die Nachrichtenroutenobjekte mithilfe von PowerShell oder über die Befehlszeilenschnittstelle hinzugefügt wurden, sind Sie jedoch im Azure-Portal verfügbar und können dort verwaltet werden.

Die Einrichtung eines Nachrichtenroutings umfasst zwei Schritte:

### <a name="add-an-endpoint"></a>Hinzufügen eines Endpunkts

In diesem Schritt wird ein Endpunkt definiert, an den die Daten durch die IoT Hub-Instanz weitergeleitet werden. Dieser Endpunkt kann nach Belieben mithilfe des PowerShell-Befehls [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) oder mithilfe des CLI-Befehls [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) erstellt werden.

Im Anschluss sind die Parameter für den Endpunkterstellungsbefehl aufgeführt:

|PowerShell-Parameter|CLI-Parameter|BESCHREIBUNG|
|---|---|---|
|ResourceGroupName|resource-group|Der Ressourcengruppenname Ihrer IoT Hub-Instanz.|
|Name|hub-name|Der Name Ihrer IoT Hub-Ressource.|
|EndpointName|endpoint-name|Verwenden Sie einen Namen, den Sie dem Endpunkt zuweisen möchten, den Sie gerade erstellen.|
|EndpointType|endpoint-type|Die Art des Endpunkts, mit dem von IoT Hub eine Verbindung hergestellt werden muss. Verwenden Sie den Literalwert „EventHub“ (PowerShell) oder „eventhub“ (Befehlszeilenschnittstelle).|
|EndpointResourceGroup|endpoint-resource-group|Ressourcengruppenname für die FHIR-Dienstressource Ihres IoT-Connectors. Sie können diesen Wert auf der Seite Übersicht des FHIR-Diensts abrufen.|
|EndpointSubscriptionID|endpoint-subscription-id|Abonnement-ID für die FHIR-Dienstressource Ihres IoT-Connectors. Sie können diesen Wert auf der Seite Übersicht des FHIR-Diensts abrufen.|
|ConnectionString|connection-string|Verbindungszeichenfolge für Ihren IoT-Connector. Verwenden Sie den Wert aus dem vorherigen Schritt.|

### <a name="add-a-message-route"></a>Hinzufügen einer Nachrichtenroute
In diesem Schritt wird eine Nachrichtenroute mit dem oben erstellten Endpunkt definiert. Für die Routenerstellung können Sie entweder den PowerShell-Befehl [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) oder den CLI-Befehl [az iot hub route create](/cli/azure/iot/hub/route) verwenden.

Im Anschluss sind die Parameter für den Befehl zum Hinzufügen einer Nachrichtenroute aufgeführt:

|PowerShell-Parameter|CLI-Parameter|BESCHREIBUNG|
|---|---|---|
|ResourceGroupName|g|Der Ressourcengruppenname Ihrer IoT Hub-Instanz.|
|Name|hub-name|Der Name Ihrer IoT Hub-Ressource.|
|EndpointName|endpoint-name|Der Name des Endpunkts, den Sie weiter oben erstellt haben.|
|RouteName|route-name|Ein Name, den Sie der Nachrichtenroute zuweisen möchten, die Sie gerade erstellen.|
|`Source`|source-type|Die Art von Daten, die an den Endpunkt gesendet werden sollen. Verwenden Sie den Literalwert „DeviceMessages“ (PowerShell) oder „devicemessages“ (Befehlszeilenschnittstelle).|

## <a name="send-device-message-to-azure-iot-hub"></a>Senden einer Gerätenachricht an Azure IoT Hub

Verwenden Sie Ihr (echtes oder simuliertes) Gerät, um die unten gezeigte exemplarische Herzfrequenznachricht an Azure IoT Hub zu senden. Diese Nachricht wird an den IoT-Connector weitergeleitet, wo die Nachricht in eine FHIR-Beobachtungsressource transformiert und im FHIR-Dienst gespeichert wird.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Stellen Sie sicher, dass Sie die Gerätenachricht senden, die den mit Ihrem IoT-Connector konfigurierten [Zuordnungsvorlagen](how-to-use-fhir-mapping-iot.md) entspricht.

## <a name="view-device-data-in-fhir-service"></a>Anzeigen von Gerätedaten im FHIR-Dienst

Sie können die vom IoT-Connector erstellten FHIR-Beobachtungsressourcen im FHIR-Dienst mithilfe von Postman anzeigen. Weitere Informationen finden Sie unter [Zugreifen auf den FHIR-Dienst mithilfe von Postman](./../use-postman.md)und Senden einer `GET` Anforderung an , um `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` Beobachtungs-FHIR-Ressourcen mit dem In der obigen Beispielnachricht übermittelten Heartratewert anzuzeigen.

> [!TIP]
> Stellen Sie sicher, dass Ihr Benutzer über den entsprechenden Zugriff auf die FHIR-Dienstdatenebene verfügt. Verwenden Sie die [rollenbasierte Zugriffssteuerung in Azure](../azure-api-for-fhir/configure-azure-rbac.md) (Azure Role-Based Access Control, Azure RBAC), um die erforderlichen Datenebenenrollen zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial richten Sie Azure IoT Hub ein, um Gerätedaten an den IoT-Connector weiterzuleiten. Wählen Sie die folgenden nächsten Schritte aus, um mehr über den IoT-Connector zu erfahren:

Grundlegendes zu den verschiedenen Phasen des Datenflusses innerhalb des IoT-Connectors

>[!div class="nextstepaction"]
>[Datenfluss des IoT-Connectors](iot-data-flow.md)

(FHIR&#174;) ist eine registrierte Marke von HL7 und wird mit der Berechtigung von HL7 verwendet.