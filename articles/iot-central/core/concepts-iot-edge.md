---
title: Azure IoT Edge und Azure IoT Central | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure IoT Edge mit einer IoT Central-Anwendung verwenden.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: a8d57f2d9f17b0a20f029ed4849e244af308bb0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246735"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Verbinden eines Azure IoT Edge-Geräts mit einer Azure IoT Central-Anwendung

Azure IoT Edge verschiebt Cloudanalysen und benutzerdefinierte Geschäftslogik auf Geräte, damit Ihre Organisation sich auf geschäftliche Erkenntnisse konzentrieren kann und sich nicht mit der Datenverwaltung befassen muss. Skalieren Sie Ihre IoT-Lösung auf, indem Sie Ihre Geschäftslogik in Standardcontainer packen. Stellen Sie diese Container dann für Ihre Geräte bereit, und überwachen Sie sie über die Cloud.

Dieser Artikel beschreibt Folgendes:

* IoT Edge Gatewaymuster mit IoT Central.
* Wie IoT Edge-Geräte sich mit einer IoT Central-Anwendung verbinden
* Wie Sie mit IoT Central Ihre IoT Edge-Geräte verwalten

Weitere Informationen zu IoT Edge finden Sie unter [Was ist Azure IoT Edge?](../../iot-edge/about-iot-edge.md).

## <a name="iot-edge"></a>IoT Edge

![Azure IoT Central mit Azure IoT Edge](./media/concepts-iot-edge/iotedge.png)

IoT Edge besteht aus drei Komponenten:

* *IoT Edge-Module* sind Container, die Azure-Dienste, Partnerdienste oder Ihren Code ausführen. Module werden auf IoT Edge-Geräten bereitgestellt und lokal auf diesen Geräten ausgeführt. Weitere Informationen hierzu finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](../../iot-edge/iot-edge-modules.md).
* Die *IoT Edge-Runtime* wird auf jedem IoT Edge-Gerät ausgeführt und dient zum Verwalten der Module, die auf einem Gerät jeweils bereitgestellt wurden. Die Runtime besteht aus zwei IoT Edge-Modulen: dem *IoT Edge-Agent* und dem *IoT Edge-Hub*. Weitere Informationen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](../../iot-edge/iot-edge-runtime.md).
* Mit einer *cloudbasierten Schnittstelle* können Sie für IoT Edge-Geräte die Remoteüberwachung und -verwaltung durchführen. Ein Beispiel für eine Cloudschnittstelle ist IoT Central.

IoT Central bietet die folgenden Funktionen für IoT Edge-Geräte:

* Gerätevorlagen zum Beschreiben der Funktionen eines IoT Edge-Geräts, wie z.B.:
  * Funktion zum Upload eines Bereitstellungsmanifests, wodurch ein Manifest für eine Geräteflotte verwaltet werden kann
  * Module, die auf dem IoT Edge-Gerät ausgeführt werden
  * Die von jedem Modul gesendeten Telemetriedaten
  * Die von jedem Modul gemeldeten Eigenschaften
  * Die Befehle, auf die jedes Modul reagiert
  * Die Beziehungen zwischen einem IoT Edge-Gatewaygerät und dem nachgeschalteten Gerät
  * Cloudeigenschaften, die auf dem IoT Edge-Gerät nicht gespeichert werden
  * Anpassungen, die ändern, wie Gerätefunktionen auf der Benutzeroberfläche angezeigt werden.
  * Geräteansichten und Formulare.
* Die Funktion zum Bereitstellen von IoT Edge-Geräten nach Maß mit dem Azure IoT-Gerätebereitstellungsdienst.
* Regeln und Aktionen
* Benutzerdefinierte Dashboards und Analysen
* Fortlaufender Datenexport von Telemetriedaten von IoT Edge-Geräten

Ein IoT Edge-Gerät kann eines der folgenden Geräte sein:

* Ein eigenständiges Gerät, das aus Modulen besteht
* Ein *Gatewaygerät*, mit dem untergeordnete Geräte eine Verbindung herstellen

![Übersicht über IoT Central mit IoT Edge](./media/concepts-iot-edge/gatewayedge.png)

Ein Gatewaygerät kann sein:

* Ein *transparentes Gateway*, bei dem sich das IoT Edge-Hub-Modul wie IoT Central verhält und für Verbindungen mit Geräten zuständig ist, die bei IoT Central registriert sind. Nachrichten werden von untergeordneten Geräten an IoT Central übergeben, als wäre dazwischen kein Gateway.

    > [!NOTE]
    > IoT Central unterstützt zurzeit nicht das Verbinden eines IoT Edge Geräts als nachgeschaltetes Gerät mit einem transparenten IoT Edge-Gateway. Der Grund: Alle Geräte, die eine Verbindung mit IoT Central herstellen, werden mit dem Device Provisioning Service (DPS) bereitgestellt, und DPS unterstützt keine geschachtelten IoT Edge-Szenarios.

* Ein *Übersetzungsgateway*, bei dem sich Geräte, die selbst keine Verbindung mit IoT Central herstellen können, stattdessen mit einem benutzerdefinierten IoT Edge-Modul verbinden. Das Modul im IoT Edge-Gerät verarbeitet eingehende Nachrichten von untergeordneten Geräten, und leitet sie dann an IoT Central weiter.

Ein einzelnes IoT Edge-Gerät kann sowohl als transparentes Gateway als auch als Übersetzungsgateway fungieren.

Weitere Informationen zu den Gatewaymustern in IoT Edge finden Sie unter [Verwendung eines IoT Edge-Geräts als Gateway](../../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-patterns"></a>IoT Edge-Muster

IoT Central unterstützt die folgenden IoT Edge-Gerätemuster:

### <a name="iot-edge-as-leaf-device"></a>IoT Edge als Blattgerät

![IoT Edge als Blattgerät](./media/concepts-iot-edge/edgeasleafdevice.png)

Das IoT Edge-Gerät wird in IoT Central bereitgestellt; alle nachgeschalteten Geräte und deren Telemetriedaten werden so dargestellt, als stammten sie vom IoT Edge-Gerät. Nachgeschaltete Geräte, die mit dem IoT Edge-Gerät verbunden sind, werden in IoT Central nicht bereitgestellt.

### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist

![IoT Edge mit nachgeschalteter Geräteidentität](./media/concepts-iot-edge/edgewithdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Runtime-Unterstützung für die Bereitstellung von nachgeschalteten Geräten über das Gateway gibt es derzeit nicht.

### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist, die vom IoT Edge-Gateway bereitgestellt wird

![IoT Edge mit nachgeschaltetem Gerät ohne Identität](./media/concepts-iot-edge/edgewithoutdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Derzeit bietet IoT Central keine Runtimeunterstützung für ein Gateway, um eine Identität und nachgeschaltete Geräte bereitstellen zu können. Wenn Sie ein eigenes Modul für die Identitätsübersetzung einsetzen, kann IoT Central dieses Muster unterstützen.

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Beziehungen nachgeschalteter Geräte mit einem Gateway und Modulen

Untergeordnete Geräte können über das *IoT Edge-Hub-Modul* eine Verbindung mit einem IoT Edge-Gatewaygerät herstellen. In diesem Szenario ist das IoT Edge-Gerät ein transparentes Gateway:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagramm des transparenten Gateways" border="false":::

Nachgeschaltete Geräte können auch über ein benutzerdefiniertes Modul eine Verbindung mit einem IoT Edge-Gatewaygerät herstellen. Im folgenden Szenario stellen untergeordnete Geräte die Verbindung über ein benutzerdefiniertes *Modbus*-Modul her. In diesem Szenario ist das IoT Edge-Gerät ein Übersetzungsgateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagramm der Verbindung über das benutzerdefinierte Modul" border="false":::

Das folgende Diagramm zeigt die Verbindungen mit einem IoT Edge-Gatewaygerät über beide Arten von Modulen. In diesem Szenario ist das IoT Edge-Gerät sowohl ein transparentes Gateway als auch ein Übersetzungsgateway:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagramm mit Verbindungen mit beiden Verbindungsmodulen" border="false":::

Untergeordnete Geräte können über mehrere benutzerdefinierte Module eine Verbindung mit einem IoT Edge-Gatewaygerät herstellen. Das folgende Diagramm zeigt untergeordnete Geräte, die über ein benutzerdefiniertes Modbus-Modul, ein benutzerdefiniertes BLE-Modul und das *IoT Edge-Hub*-Modul eine Verbindung herstellen:

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagramm mit Verbindungen über mehrere benutzerdefinierte Module" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge-Geräte und IoT Central

IoT Edge-Geräte können *SAS-Token (Shared Access Signature)* oder X.509-Zertifikate für die Authentifizierung bei IoT Central verwenden. Sie können Ihre IoT Edge-Geräte manuell bei IoT Central registrieren, bevor diese zum ersten Mal eine Verbindung herstellen, oder Device Provisioning Service für die Registrierung verwenden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](concepts-get-connected.md).

In IoT Central wird mit [Gerätevorlagen](concepts-device-templates.md) definiert, wie die Lösung mit einem Gerät interagiert. In einer Gerätevorlage wird z. B. Folgendes angegeben:

* Die Arten von Telemetriedaten und Eigenschaften, die ein Gerät sendet, damit IoT Central diese interpretieren und Visualisierungen erstellen kann
* Die Befehle, auf die ein Gerät antwortet, damit IoT Central eine Benutzeroberfläche für einen Operator zum Aufrufen der Befehle anzeigen kann

IoT Edge-Geräte können wie Standardgeräte Telemetriedaten senden, Eigenschaftswerte synchronisieren und auf Befehle reagieren. Daher ist für IoT Edge-Geräte eine Gerätevorlage in IoT Central erforderlich.

### <a name="iot-edge-device-templates"></a>IoT Edge-Gerätevorlagen

In IoT Central-Gerätevorlagen werden Modelle zum Beschreiben der Funktionen von Geräten verwendet. Das folgende Diagramm zeigt die Struktur des Modells für ein IoT Edge-Gerät:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Struktur des Modells für ein IoT Edge-Gerät, das mit IoT Central verbunden ist" border="false":::

IoT Central modelliert ein IoT Edge-Gerät wie folgt:

* Jede IoT Edge-Gerätevorlage enthält ein Gerätefunktionsmodell.
* Für jedes benutzerdefinierte Modul im Bereitstellungsmanifest wird ein Modulfunktionsmodell generiert.
* Zwischen den einzelnen Modulfunktionsmodellen und einem Gerätemodell wird eine Beziehung eingerichtet.
* Ein Modulfunktionsmodell implementiert eine oder mehrere Modulschnittstelle(n).
* Jede Modulschnittstelle enthält Telemetrie, Eigenschaften und Befehle.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge-Bereitstellungsmanifeste und IoT Central-Gerätevorlagen

In IoT Edge wird Geschäftslogik in Form von Modulen bereitgestellt und verwaltet. IoT Edge-Module sind die kleinste von IoT Edge verwaltete Berechnungsergebniseinheit und können Azure-Dienste (z. B. Azure Stream Analytics) oder Ihren eigenen lösungsspezifischen Code enthalten.

IoT Edge-*Bereitstellungsmanifeste* enthalten die IoT Edge-Module, die auf dem Gerät bereitgestellt werden sollen, sowie Informationen zu deren Konfiguration. Weitere Informationen finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](../../iot-edge/module-composition.md).

Importieren Sie ein Bereitstellungsmanifest in Azure IoT Central, um eine Gerätevorlage für das IoT Edge-Gerät zu erstellen.

Der folgende Codeausschnitt zeigt ein Beispiel für ein IoT Edge-Bereitstellungsmanifest:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

Im obigen Ausschnitt sehen Sie Folgendes:

* Es gibt drei Module: die Systemmodule *IoT Edge-Agent* und *IoT Edge-Hub*, die in jedem Bereitstellungsmanifest enthalten sind, und das benutzerdefinierte Modul **SimulatedTemperatureSensor**.
* Die öffentlichen Modulimages werden aus einem Azure Container Registry-Repository gepullt, bei dem zum Herstellen einer Verbindung keine Anmeldeinformationen erforderlich sind. Legen Sie für private Modulimages die in der Einstellung `registryCredentials` für das *IoT Edge-Agent*-Modul zu verwendenden Container Registry-Anmeldeinformationen fest.
* Das benutzerdefinierte Modul **SimulatedTemperatureSensor** verfügt über zwei Eigenschaften: `"SendData": true` und `"SendInterval": 10`.

Wenn Sie dieses Bereitstellungsmanifest in eine IoT Central-Anwendung importieren, wird die folgende Gerätevorlage generiert:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Screenshot: Basierend auf dem Bereitstellungsmanifest erstellte Gerätevorlage":::

Im obigen Screenshot sehen Sie Folgendes:

* Ein Modul namens **SimulatedTemperatureSensor**. Die Systemmodule *IoT Edge-Agent* und *IoT Edge-Hub* sind in der Vorlage nicht enthalten.
* Eine Schnittstelle namens **management** (Verwaltung), die zwei schreibbare Eigenschaften namens **SendData** und **SendInterval** enthält.

Das Bereitstellungsmanifest enthält keine Informationen über die Telemetriedaten, die das Modul **SimulatedTemperatureSensor** sendet, oder die Befehle, auf die es reagiert. Fügen Sie diese Definitionen der Gerätevorlage manuell hinzu, bevor Sie sie veröffentlichen.

Weitere Informationen finden Sie unter [Tutorial: Hinzufügen eines Azure IoT Edge-Geräts zu Ihrer Azure IoT Central-Anwendung](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Aktualisieren eines Bereitstellungsmanifests

Wenn Sie das Bereitstellungsmanifest ersetzen, laden alle verbundenen IoT Edge-Geräte das neue Manifest herunter und aktualisieren ihre Module. Bei Änderungen an der Modulkonfiguration aktualisiert IoT Central allerdings nicht die Schnittstellen in der Gerätevorlage. Wenn Sie z. B. das im vorherigen Codeausschnitt gezeigte Manifest durch das folgende ersetzen, wird die Eigenschaft **SendUnits** nicht automatisch in der Schnittstelle **management** (Verwaltung) in der Gerätevorlage angezeigt. Fügen Sie der Schnittstelle **management** die neue Eigenschaft manuell hinzu, damit IoT Central sie erkennt:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Bereitstellen der IoT Edge-Runtime

Informationen darüber, wo Sie die IoT Edge-Runtime ausführen können, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](../../iot-edge/support.md).

Sie können die IoT Edge-Runtime auch in den folgenden Umgebungen installieren:

* [Installieren oder Deinstallieren von Azure IoT Edge für Linux](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)
* [Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät (Vorschau)](../../iot-edge/how-to-provision-single-device-linux-on-windows-symmetric.md)
* [Ausführen von Azure IoT Edge auf virtuellen Ubuntu-Computern in Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge-Gatewaygeräte

Wenn Sie ausgewählt haben, dass das IoT Edge-Gerät ein Gatewaygerät ist, können Sie für Geräte, die eine Verbindung mit dem Gatewaygerät herstellen sollen, Downstreambeziehungen mit Gerätemodellen hinzufügen.

Weitere Informationen finden Sie unter [Verbinden von Geräten über ein transparentes IoT Edge-Gateway](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="monitor-your-iot-edge-devices"></a>Überwachen Ihrer IoT Edge-Geräte

Wenn Sie erfahren möchten, wie Sie Ihre IoT Edge-Flotte mithilfe von Azure Monitor und integrierten Metriken überwachen können, lesen Sie [Sammeln und Transportieren von Metriken](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes sollten Sie sich darüber informieren, wie Sie [Ihre eigenen IoT Edge-Module entwickeln](../../iot-edge/module-development.md).
