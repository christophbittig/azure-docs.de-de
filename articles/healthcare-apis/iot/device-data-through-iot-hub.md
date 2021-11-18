---
title: Empfangen von Gerätedaten über Azure IoT Hub – Azure Healthcare-APIs
description: In diesem Tutorial erfahren Sie, wie Sie das Routing von Gerätedaten IoT Hub in den FHIR-Dienst über den IoT-Connector aktivieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 9c4dd42d81374f75beb66f0564a2fb2b0fc38c01
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709213"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutorial: Empfangen von Gerätedaten über Azure IoT Hub

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector bietet die Möglichkeit, Integritätsdaten von verschiedenen IoT Health-bezogenen oder medizinischen Geräten im Fast Healthcare Interoperability Resources-Dienst (FHIR®) zu sammeln und zu erfassen. Der IoT-Connector ist interoperabel und reaktionsfähig mit Geräten, die über Azure IoT Hub erstellt und verwaltet werden, um verbesserte Workflows und benutzerfreundlichkeit zu ermöglichen. In diesem Tutorial erfahren Sie, wie Sie eine Verbindung herstellen und Gerätedaten von Azure IoT Hub an IoT Connector weiterleiten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. ([Kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- FHIR-Dienstressource mit mindestens einem IoT-Connector– [Bereitstellen des IoT-Connectors mit Azure-Portal](deploy-iot-connector-in-azure.md)
- Azure IoT Hub-Ressource, die mit mindestens einem echten oder simulierten Gerät verbunden ist. Weitere Informationen finden Sie unter [Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub und Lesen der Telemetriedaten mit einer Back-End-Anwendung (.NET)](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp).

> [!TIP]
> Bei Verwendung einer simulierten Azure IoT Hub-Geräteanwendung können Sie aus verschiedenen unterstützten Sprachen und Systemen die gewünschte Anwendung auswählen.

## <a name="get-connection-string-for-iot-connector"></a>Verbindungszeichenfolge für IoT-Connector

Azure IoT Hub erfordert eine Verbindungszeichenfolge, um eine sichere Verbindung mit Ihrem IoT-Connector herzustellen. Erstellen Sie eine neue Verbindungszeichenfolge für Ihren IoT-Connector, wie unter [Generieren einer Verbindungszeichenfolge beschrieben.](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string) Tresor verbindungszeichenfolge, die im nächsten Schritt verwendet werden soll.

Von IoT Connector wird im Hintergrund eine Azure Event Hub-Instanz verwendet, um Gerätenachrichten zu empfangen. Die oben erstellte Verbindungszeichenfolge ist im Grunde die Verbindungszeichenfolge für diesen zugrunde liegenden Event Hub.

## <a name="connect-azure-iot-hub-with-iot-connector"></a>Verbinden Azure IoT Hub mit IoT-Connector

Azure IoT Hub unterstützt das so genannte [Nachrichtenrouting](../../iot-hub/iot-hub-devguide-messages-d2c.md). Mit diesem Feature können Gerätedaten an verschiedene Azure-Dienste wie Event Hub, Speicherkonto und Service Bus gesendet werden. Der IoT-Connector verwendet dieses Feature, um eine Verbindung herzustellen und Gerätedaten von Azure IoT Hub an den Event Hub-Endpunkt zu senden.

> [!NOTE] 
> Zu diesem Zeitpunkt können Sie nur PowerShell oder den CLI-Befehl zum Erstellen des Nachrichtenroutings verwenden, da der Event Hub des IoT-Connectors nicht im Kundenabonnement gehostet wird und daher nicht über das Abonnement Azure-Portal. [](../../iot-hub/tutorial-routing.md) Nachdem die Nachrichtenroutenobjekte mithilfe von PowerShell oder über die Befehlszeilenschnittstelle hinzugefügt wurden, sind Sie jedoch im Azure-Portal verfügbar und können dort verwaltet werden.

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
|EndpointResourceGroup|endpoint-resource-group|Ressourcengruppenname für die FHIR-Dienstressource Ihres IoT-Connectors. Sie können diesen Wert auf der Seite Übersicht des FHIR-Diensts erhalten.|
|EndpointSubscriptionID|endpoint-subscription-id|Abonnement-ID für die FHIR-Dienstressource Ihres IoT-Connectors. Sie können diesen Wert auf der Seite Übersicht des FHIR-Diensts erhalten.|
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

Verwenden Sie Ihr (echtes oder simuliertes) Gerät, um die unten gezeigte exemplarische Herzfrequenznachricht an Azure IoT Hub zu senden. Diese Nachricht wird an den IoT-Connector geroutet, wo die Nachricht in eine FHIR-Beobachtungsressource transformiert und im FHIR-Dienst gespeichert wird.

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
> Stellen Sie sicher, dass Sie die Gerätenachricht senden, die den mit [Ihrem](how-to-use-fhir-mappings.md) IoT-Connector konfigurierten Zuordnungsvorlagen entspricht.

## <a name="view-device-data-in-fhir-service"></a>Anzeigen von Gerätedaten im FHIR-Dienst

Sie können die vom IoT-Connector erstellten FHIR-Beobachtungsressourcen im FHIR-Dienst mithilfe von Postman anzeigen. Weitere Informationen finden Sie unter Zugreifen auf den [FHIR-Dienst mithilfe von Postman,](./../use-postman.md)und senden Sie eine Anforderung an , um FHIR-Beobachtungsressourcen mit dem In der obigen Beispielnachricht übermittelten Heart rate-Wert `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` anzeigen zu lassen.

> [!TIP]
> Stellen Sie sicher, dass Ihr Benutzer über den entsprechenden Zugriff auf die Datenebene des FHIR-Diensts verfügt. Verwenden Sie die [rollenbasierte Zugriffssteuerung in Azure](../azure-api-for-fhir/configure-azure-rbac.md) (Azure Role-Based Access Control, Azure RBAC), um die erforderlichen Datenebenenrollen zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial richten Sie Azure IoT Hub ein, um Gerätedaten an den IoT-Connector weiter zu routen. Wählen Sie im Folgenden die nächsten Schritte aus, um weitere Informationen zum IoT-Connector zu erhalten:

Verstehen der verschiedenen Phasen des Datenflusses innerhalb des IoT-Connectors.

>[!div class="nextstepaction"]
>[Datenfluss des IoT-Konnektors](iot-data-flow.md)

(FHIR&#174;) ist eine registrierte Marke von HL7 und wird mit der Berechtigung von HL7 verwendet.
