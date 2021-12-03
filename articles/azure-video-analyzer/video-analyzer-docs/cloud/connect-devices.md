---
title: Verbinden von Geräten mit Azure Video Analyzer
description: In diesem Artikel wird beschrieben, wie Sie Geräte mit Azure Video Analyzer verbinden.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70ad7876b56b12c6a9d6fd1654e12dc0a764aa00
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339970"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Verbinden von Geräten mit Azure Video Analyzer

[!INCLUDE [header](includes/cloud-env.md)]

Um Videos von einem Gerät zu erfassen und aufzuzeichnen, muss der Azure Video Analyzer-Dienst eine [RTSP](../terminology.md#rtsp)-Verbindung mit diesem herstellen. Wenn sich das Gerät hinter einer Firewall befindet, werden solche Verbindungen blockiert, und es ist unter Umständen nicht immer möglich, Regeln zum Zulassen eingehender Verbindungen von Azure zu erstellen. Zum Unterstützen solcher Geräte können Sie eine [Azure IoT Plug and Play](../../../iot-develop/overview-iot-plug-and-play.md)-Geräteimplementierung erstellen und installieren, die nach Befehlen lauscht, die von Video Analyzer über IoT Hub gesendet werden, und dann einen sicheren Websockettunnel zum Dienst öffnet. Nachdem ein solcher Tunnel eingerichtet wurde, kann Video Analyzer eine Verbindung mit dem RTSP-Server herstellen.

## <a name="overview"></a>Übersicht 

Dieser Artikel enthält grundlegende Konzepte zum Erstellen einer Azure IoT PnP-Geräteimplementierung, mit der Video Analyzer Videos von einem Gerät erfassen und aufzeichnen kann. 

Die Anwendung muss dazu: 

1. als IoT-Gerät ausgeführt werden 
1. die [IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md)-Schnittstelle mit einem bestimmten Befehl (`tunnelOpen`) implementieren 
1. beim Empfang eines solchen Befehls: 
   * die empfangenen Argumente überprüfen 
   * eine sichere Websocketverbindung mit der URL öffnen, die mithilfe des bereitgestellten Tokens angegeben wird
   * die Websocketbytes an die TCP-Verbindung des RTSP-Servers der Kamera weiterleiten

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="Herstellen von Verbindungen von Geräten mit der Cloud":::

## <a name="run-as-an-iot-device"></a>Ausführen als IoT-Gerät 

Die Video Analyzer-Anwendung wird als Video Analyzer-PnP-Plug-In bereitgestellt. Dazu ist die Verwendung eines der [Azure IoT-Geräte-SDKs](../../../iot-develop/libraries-sdks.md#device-sdks) erforderlich, um Ihre IoT-PnP-Geräteimplementierung zu erstellen. Registrieren Sie das IoT-Gerät bei Ihrem IoT Hub, um die IoT Hub-Geräte-ID und die Geräteverbindungszeichenfolge zu erhalten.

### <a name="iot-device-clientconfiguration"></a>Clientkonfiguration für das IoT-Gerät

* Legen Sie  OPTION_MODEL_ID auf `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”` fest, um PnP-Abfragen zu unterstützen  
* Vergewissern Sie sich, dass Ihr Gerät entweder das MQTT- oder das MQTT-über-WebSockets-Protokoll verwendet, um eine Verbindung mit Azure IoT Hub herzustellen. 
    * Verbinden Sie IoT Hub über einen HTTPS-Proxy, wenn einer auf dem IoT-Gerät konfiguriert ist.  
* Registrieren Sie den Rückruf für die direkte Methode  `tunnelOpen` . 

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>Implementieren Sie die IoT-PnP-Schnittstelle für Video Analyzer

Das folgende [DTDL-Modell (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) beschreibt ein Gerät, das eine Verbindung mit Video Analyzer herstellen kann.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:azure:videoanalyzer:WebSocketTunneling;1",
  "@type": "Interface",
  "displayName": "Azure Video Analyzer Web Socket Tunneling",
  "description": "This interface enables media publishing to Azure Video Analyzer service from a RTSP compatible device which is located behind a firewall or NAT device.",
  "contents": [
    {
      "@type": "Command",
      "displayName": "Tunnel Open",
      "name": "tunnelOpen",
      "request": {
        "@type": "CommandPayload",
        "displayName": "Parameters",
        "name": "parameters",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "displayName": "Remote Endpoint",
              "description": "The remote endpoint for the web socket tunnel.",
              "name": "remoteEndpoint",
              "schema": "string"
            },
            {
              "displayName": "Remote Authorization Token",
              "description": "The bearer token for the web socket authentication.",
              "name": "remoteAuthorizationToken",
              "schema": "string"
            },
            {
              "displayName": "Local Port",
              "description": "The local port where web socket data should be tunneled to.",
              "name": "localPort",
              "schema": "integer"
            }
          ]
        }
      }
    }
  ]
}
```

Das IoT-Gerät registriert eine direkte Methode  `tunnelOpen`, wobei der Text der Anforderung die Parameter `remoteEndpoint`, `remoteAuthorizationToken` und `localPort` aufweist, wie oben abgebildet.

## <a name="implement-the-direct-method-tunnelopen"></a>Implementieren der direkten Methode `tunnelOpen`
Wenn die direkte Methode `tunnelOpen` vom Video Analyzer-Dienst aufgerufen wird, muss die Anwendung folgende Schritte ausführen:

1. die verfügbaren RTSP-Ports des Geräts abrufen
1. den im Aufruf der direkten Methode angegebenen `localPort`-Wert mit den verfügbaren Ports vergleichen
   * **BadRequest** zurückgeben, wenn keine Übereinstimmung gefunden wird (mehr dazu finden Sie unten im Abschnitt „Fehlerantworten“)
1. eine TCP-Verbindung mit „(Kamera-IP oder Hostname): `localPort`" öffnen
   * **BadRequest** bei einem Verbindungsfehler zurückgeben
   * HINWEIS: Der Hostname ist in der Regel **localhost**.
1. eine Websocketverbindung mit dem `remoteEndpoint` öffnen (über einen Proxy, wenn er auf dem Gerät konfiguriert ist)
   * den HTTP-Header „Authorization“ auf „Bearer (remoteAuthorizationToken)“ festlegen
   * den Header „TunnelConnectionSource“ auf den Wert „PnpDevice“ festlegen
   * User-Agent auf einen geeigneten Wert festlegen, mit dem Sie Ihre Implementierung identifizieren können. 
      * Beispielsweise können Sie hier die Architektur der CPU, das Betriebssystem, das Modell/den Hersteller des Geräts angeben.
   * „200 OK“ zurückgeben, wenn die Websocketverbindung erfolgreich war, andernfalls den entsprechenden Fehlercode zurückgeben
1. die Antwort zurückgeben (nicht blockieren)
1. Die IoT PnP-Geräteimplementierung beginnt mit dem bidirektionalen Senden von TCP-Daten zwischen dem Websocket und der TCP-Verbindung des RTSP-Servers.

Der Video Analyzer-Dienst versucht, `tunnelOpen` bei einem Fehler zu wiederholen, sodass keine Wiederholungen in der Anwendung erforderlich sind.

### <a name="error-responses"></a>Fehlercodes
Bei einem Fehler der `tunnelOpen`-Anforderung sollte der Antworttext wie folgt aussehen

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
Hier sehen Sie Beispiele für solche Fehlerantworten:

* Der lokale Port ist nicht als RTSP- oder RTSPS-Port verfügbar { „code“: „400“, „target“: „(Kamera-IP oder Hostname):{localPort}“, „message“: „Lokaler Port ist nicht verfügbar“}
* Timeout/Verbindung mit RTSP-Endpunkt nicht möglich { „code“: „400“, „target“: „(Kamera-IP oder Hostname):{localPort}“, „message“: „Verbindung mit RTPS-Endpunkt nicht möglich“}
*   Timeout/Fehlerantwort von Websocket-Verbindungsversuch { „code“: „{WebSocket-Antwortcode}“, „target“: „{remoteEndpoint}“, „message“: „{Fehlermeldung der Websocketantwort}“}


## <a name="ingestion-to-video-analyzer"></a>Erfassung in Video Analyzer
Zum Erfassen und Aufzeichnen von Videos in Video Analyzer muss eine Pipelinetopologie mit aktivierter Tunnelung erstellt werden. In dieser Topologie muss eine ausgehende Livepipeline erstellt und aktiviert werden. [Anweisungen für diesen Prozess sind hier beschrieben.](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>Beispielimplementierung
Wenden Sie sich an videoanalyzerhelp@microsoft.com, wenn Sie eine Anwendung auf Ihrem Gerät implementieren möchten, um sie mit Video Analyzer zu verbinden.

## <a name="see-also"></a>Weitere Informationen 

[Was ist IoT Plug &amp; Play?](../../../iot-develop/overview-iot-plug-and-play.md)
