---
title: Entdecken von Kameras mit ONVIF-Unterstützung im lokalen Subnetz
description: In dieser Lerneinheit erfahren Sie, wie Sie das Video Analyzer-Edgemodul verwenden können, um ONVIF-fähige Kameras in Ihrem lokalen Subnetz zu entdecken.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e0629294ba5d19315da096072fa804824649acc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564022"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>Entdecken von Kameras mit ONVIF-Unterstützung im lokalen Subnetz

[!INCLUDE [header](includes/edge-env.md)]

Dieser Leitfaden führt Sie durch die Verwendung des Edgemoduls von Azure Video Analyzer, um ONVIF-kompatible Kameras im gleichen Subnetz wie das IoT-Edgegerät zu entdecken. Open Network Video Interface Forum (ONVIF) ist ein offener Standard, durch den diskrete IP-basierte physische Geräte wie Überwachungskameras mit weiteren Netzwerkgeräten und Software kommunizieren können. Weitere Informationen zu ONVIF finden Sie auf der [ONVIF](https://www.onvif.org/about/mission/)-Website.

## <a name="prerequisites"></a>Voraussetzungen

Die Durchführung der in diesem Artikel aufgeführten Schritte setzt Folgendes voraus:

- Ein aktives Azure-Abonnement
- Eine installierte [Azure Befehlszeilenschnittstellen-Erweiterung für IoT](https://github.com/Azure/azure-iot-cli-extension#installation)
- Führen Sie einen der folgenden Schritte aus:
  - [Schnellstart: Erste Schritte mit Azure Video Analyzer](get-started-detect-motion-emit-events.md)
  - [Schnellstart: Erste Schritte mit Azure Video Analyzer im Azure-Portal](get-started-detect-motion-emit-events-portal.md)
- Ein bereitgestelltes Video Analyzer-Edgemodul, Version 1.1 (oder neuer), auf Ihrem IoT-Edgegerät
- Wenn Sie einen virtuellen [Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11))-Computer oder [EFLOW](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true) verwenden, ist ein [externer Switch](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) erforderlich, um den direkten Methodenaufruf onvifDeviceDiscover auszuführen. Diese Anforderung ist auf einen [Multicastaufruf](https://en.wikipedia.org/wiki/Multicast) zurückzuführen, der für die ONVIF-Geräteermittlung verwendet wird.

Das ONVIF-Feature des Video Analyzer-Edgemoduls erfordert bestimmte Optionen zum Erstellen von Containern, wie unter [Aktivieren der ONVIF-Ermittlungsfunktion](#enable-onvif-discovery-feature) beschrieben. Für diese ONVIF-Ermittlungsfunktion muss Port 3702 verfügbar sein.
  
> [!NOTE]
> Wenn Sie über eine neue Bereitstellung von Version 1.1 des Video Analyzer-Edgemoduls verfügen, können Sie mit dem Abschnitt zum [Verwenden direkter Methodenaufrufe](#use-direct-method-calls) fortfahren. Führen Sie andernfalls die Abschnitte unten aus, um Ihr vorhandenes Video Analyzer-Edgemodul zu aktualisieren, um die ONVIF-Ermittlungsfunktion zu aktivieren.  

## <a name="check-the-version"></a>Version überprüfen

Führen Sie an einer Eingabeaufforderung die folgenden Befehl aus:

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

Wenn das Ergebnis des Befehls oben **Azure Video Analyzer:1.0.1** ist, führen Sie die folgenden Schritte aus, um das Video Analyzer-Edgemodul auf Version 1.1 zu aktualisieren.

1. Navigieren Sie im Azure-Portal zu dem IoT Hub (`<IOT_HUB_NAME>` oben), der zusammen mit ihrer Bereitstellung des Video Analyzer-Edgemoduls verwendet wird.
1. Klicken Sie unter „Automatische Geräteverwaltung“ auf **IoT Edge**, und wählen Sie das IoT-Edgegerät (`<IOT_EDGE_DEVICE_NAME>` oben) aus, auf dem das Video Analyzer-Edgemodul bereitgestellt wurde.
1. Klicken Sie auf **Module festlegen** und dann auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.
1. Nach einigen Minuten wird das Video Analyzer-Edgemodul aktualisiert, und Sie können den Befehl oben zur Überprüfung erneut ausführen.

### <a name="enable-onvif-discovery-feature"></a>Aktivieren der ONVIF-Ermittlungsfunktion

Fügen Sie als Nächstes den Optionen zur Containererstellung für das Video Analyzer-Edgemodul weitere Optionen hinzu. Dies ermöglicht ihm die Kommunikation über das Hostnetzwerk, um ONVIF-fähige Kameras im gleichen Subnetz zu entdecken.

1. Navigieren Sie im Azure-Portal zum IoT Hub.
1. Klicken Sie unter „Automatische Geräteverwaltung“ auf **IoT Edge**, und wählen Sie das IoT-Edgegerät aus, auf dem das Video Analyzer-Edgemodul bereitgestellt wurde.
1. Klicken Sie auf **Module festlegen**, und wählen Sie das Video Analyzer-Edgemodul aus.
1. Wechseln Sie zur Registerkarte **Containererstellungsoptionen**, und fügen Sie den entsprechenden Abschnitten die folgenden JSON-Einträge hinzu:

    - Geben Sie im Feld JSON-Eintrag nach der ersten `{` Folgendes ein:
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - Im `HostConfig`-JSON-Objekt:

        ```JSON
        "NetworkMode": "host",
        ```
1. Klicken Sie unten auf **Aktualisieren**.
1. Klicken Sie auf **Überprüfen und erstellen**.
1. Klicken Sie auf **Erstellen**

## <a name="use-direct-method-calls"></a>Verwenden von Aufrufen direkter Methoden

Das Video Analyzer-Edgemodul stellt direkte Methodenaufrufe für die ONVIF-Ermittlung von Kameras mit Netzwerkverbindung im gleichen Subnetz zur Verfügung. Die folgenden Schritte gelten sowohl für den Abschnitt `onvifDeviceDiscover` als auch für `onvifDeviceGet` unten:

1. Navigieren Sie im Azure-Portal zum IoT Hub.
1. Klicken Sie unter „Automatische Geräteverwaltung“ auf **IoT Edge**, und wählen Sie das IoT-Edgegerät aus, auf dem das Video Analyzer-Edgemodul bereitgestellt wurde.
1. Klicken Sie auf das Video Analyzer-Edgemodul, und klicken Sie in der Menüleiste oben auf **Direkte Methode**.

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

Diese direkte Methode listet alle ermittelbaren ONVIF-Geräte im gleichen Subnetz wie das Video Analyzer-Edgemodul auf.


1. Geben Sie im Methodennamen ein:

    ```JSON
    onvifDeviceDiscover
    ```
1. Geben Sie in der Nutzlast ein:

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   Die Ermittlungsdauer ist die Dauer, die das Video Analyzer-Edgemodul auf den Empfang von Antworten von ermittelbaren ONVIF-Geräten wartet. In großen Umgebungen muss dieser Wert möglicherweise angepasst werden.

1. Innerhalb einiger Sekunden sollten Sie ein `result` wie etwa das Folgende sehen:

    ```JSON
    {
        "status": 200,
        "payload": {
            "value": [
                {
                    "serviceIdentifier": "{urn:uuid}",
                    "remoteIPAddress": "{IP_ADDRESS}",
                    "scopes": [
                        "onvif://www.onvif.org/type/Network_Video_Transmitter",
                        "onvif://www.onvif.org/name/{CAMERA_MANUFACTURE}",
                        "onvif://www.onvif.org/location/",
                        "onvif://www.onvif.org/hardware/{CAMERA_MODEL}",
                        "onvif://www.onvif.org/Profile/Streaming",
                        "onvif://www.onvif.org/Profile/G",
                        "onvif://www.onvif.org/Profile/T"
                    ],
                    "endpoints": [
                        "http://<IP_ADDRESS>/onvif/device_service",
                        "https://<IP_ADDRESS>/onvif/device_service"
                    ],
                    
                }
            ]
        }
    }
    ```
    Der Rückgabestatus 200 gibt an, dass der Aufruf der direkten Methode erfolgreich verarbeitet wurde.

### <a name="onvifdeviceget"></a>onvifDeviceGet

Der onvifDeviceGet-Aufruf unterstützt sowohl ungesicherte als auch TLS-fähige Endpunkte. Dieser direkte Methodenaufruf ruft ausführliche Informationen zu einem bestimmten ONVIF-Gerät ab.

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> Wenn der onvifDeviceGet-Aufruf an einen ungesicherten Endpunkt auf einer ONVIF-fähigen Kamera erfolgt, müssen Sie die Einstellung `"allowUnsecuredEndpoints"` für den Identitätszwilling des Video Edge-Moduls auf `true` festlegen. Weitere Informationen finden Sie unter [Eigenschaften von Modulzwillingen](./module-twin-configuration-schema.md).

1. Geben Sie im Methodennamen ein:

    ```JSON
    onvifDeviceGet
    ```
1. Geben Sie in der Nutzlast ein:

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "http://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }
    ```

# <a name="tlsendpoint"></a>[TlsEndpoint](#tab/tlsendpoint)

1. Geben Sie im Methodennamen ein:

    ```JSON
    onvifDeviceGet
    ```
1. Geben Sie in der Nutzlast ein:

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.TlsEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "https://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }

---

In the above payload:

- `url` is the IP address of the ONVIF device from which you wish to get additional details
- `username` is the user name that is used to authenticate with the device, which has permissions to the ONVIF features. Some devices require specific accounts to be created for this purpose.
- `password` is the password for that user name

    Within a few seconds you should see a result such as:

    ```JSON
    {
        "status": 200,
        "payload": {
            "hostname": {
                "fromDhcp": true,
                "hostname": "{NAME_OF_THE_ONVIF_DEVICE}"
            },
            "systemDateTime": {
                "type": "ntp",
                "time": "2021-09-28T03:05:05.000Z",
                "timeZone": "GMT"
            },
            "dns": {
                "fromDhcp": true,
                "ipv4Address": [
                    "{IP_ADDRESS}"
                ],
                "ipv6Address": []
            },
            "mediaProfiles": [
                {
                    "name": "{Profile1}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 3840,
                            "height": 2160
                        },
                        "rateControl": {
                            "bitRateLimit": 15600,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                },
                {
                    "name": "{Profile2}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 1280,
                            "height": 720
                        },
                        "rateControl": {
                            "bitRateLimit": 1900,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                }
            ]
        }
    }
    ```

### <a name="return-status-of-onvifdeviceget"></a>Rückgabestatus von onvifDeviceGet

| Status | Code      | Bedeutung/Behebung                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Erfolg   | Der Aufruf der direkten Methode wurde erfolgreich abgeschlossen.                                                                                                                                                                                |
| 400    | Ungültige Anforderung  | Die Anforderung war falsch formatiert.   |
| 403    | Verboten | Der Aufruf der direkten Methode konnte die angeforderten Informationen aufgrund eines Authentifizierungsfehlers nicht erfolgreich beim ONVIF-Gerät abrufen. Überprüfen Sie, ob der Benutzername und/oder das Kennwort im Nachrichtentext korrekt waren. |
| 500 | Fehler | Unbekannter Fehler aufgetreten.  |
| 502    | Ungültiges Gateway | Es wurde eine ungültige Antwort von einem Upstreamdienst empfangen.  |
| 504    | Timeout   | Der Aufruf der direkten Methode ist abgelaufen, bevor die Antwort des ONVIF-Geräts empfangen wurde.  |

## <a name="troubleshooting"></a>Problembehandlung

In diesem Abschnitt werden einige Schritte zur Problembehandlung behandelt:

- Im Azure-Portal auf dem Blatt für direkte Methoden des IoT-Edgemoduls, wenn Sie den Fehler empfangen „Der Vorgang wurde aufgrund eines Fehlers nicht ordnungsgemäß abgeschlossen. Fehler bei der Anforderung mit Statuscode 504“. (Siehe Abbildung unten):

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="Screenshot des 504-Fehlers.":::

    Vergewissern Sie sich, dass die Einstellung für `"discoveryDuration":"PT8S"` im direkten Methodenaufruf oben kürzer als die Werte für `Connection Timeout` oder `Method Timeout` ist.

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="Screenshot mit Behebung des 504-Fehlers.":::

- Wenn im Feld `Results` der direkten Methode „{“status“:200,“payload“:{“value“:[]}} angezeigt wird, benötigen Sie möglicherweise längere Zeitspannen für die Ermittlung.

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="Die zurückgegebene Nachricht wird im Feld ‚Results‘ der direkten Methode angezeigt":::

    Passen Sie den Zeitwert (x) in `"discoveryDuration":"PTxS"` auf eine größere Zahl an. Passen Sie außerdem den Wert von `Connection Timeout` und/oder `Method Timeout` entsprechend an.
- Der direkte Methodenaufruf `onvifDeviceGet` zeigt keine Medienprofile für H.265-codierte Medienstreams an.
- Der Rückgabestatus 403 wird möglicherweise zurückgegeben, wenn das Benutzerkonto, das zum Herstellen einer Verbindung mit dem ONVIF-Gerät verwendet wird, nicht über Berechtigungen für die ONVIF-Kamerafunktionen verfügt. Für einige ONVIF-kompatible Kameras ist es erforderlich, dass den ONVIF-Sicherheitseinstellungen ein Benutzer hinzugefügt wird, um die ONVIF-Geräteinformationen abzurufen.
- Aktuell gibt das Video Analyzer-Edgemodul bis zu 200 ONVIF-fähige Kameras zurück, die über Multicast im gleichen Subnetz erreichbar sind. Dies erfordert auch, dass Port 3702 verfügbar ist.
- Wenn onvifDeviceGet mit einem TLS-Endpunkt aufgerufen wird, müssen der ONVIF-Gerätename und die IP-Adresse der Hostdatei (/etc/hosts) auf dem Edgegerät hinzugefügt werden.  Wenn ein selbstsigniertes Zertifikat für die TLS-Verschlüsselung verwendet wird, muss das Zertifikat außerdem dem Zertifikatspeicher des IoT-Edgegeräts hinzugefügt werden. 
- Für den Aufruf der direkten Methode onvifDeviceDiscover von einem virtuellen EFLOW-Computer aus müssen die folgenden Schritte ausgeführt werden:
  - Herstellen einer Verbindung mit dem virtuellen EFLOW-Computer und Ausführen von `sudo iptables -I INPUT -p udp -j ACCEPT`

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie die [Schnellstartanleitung zum Analysieren von Livevideos](analyze-live-video-use-your-model-http.md) mit dem ermittelten ONVIF-Gerät aus.
