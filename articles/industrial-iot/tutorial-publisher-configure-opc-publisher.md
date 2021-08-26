---
title: Konfigurieren von Microsoft OPC Publisher
description: In diesem Tutorial erfahren Sie, wie Sie OPC Publisher im eigenständigen Modus konfigurieren.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 3667a201e293a844fbcd60b4f00c43aee03ca550
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015648"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Tutorial: Konfigurieren von OPC Publisher

Dieses Tutorial enthält Informationen zur Konfiguration von OPC Publisher. Für die Konfiguration können verschiedene Schnittstellen verwendet werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren von OPC Publisher per Konfigurationsdatei
> * Konfigurieren von OPC Publisher über Befehlszeilenargumente
> * Konfigurieren von OPC Publisher über direkte IoT Hub-Methoden
> * Konfigurieren von OPC Publisher über einen cloudbasierten begleitenden REST-Microservice

## <a name="configuring-security"></a>Konfigurieren der Sicherheit

IoT Edge stellt OPC Publisher die zugehörige Sicherheitskonfiguration für den automatischen Zugriff auf IoT Hub bereit. OPC Publisher kann auch als eigenständiger Docker-Container ausgeführt werden. In diesem Fall wird über den Befehlszeilenparameter `dc` eine Geräteverbindungszeichenfolge für den Zugriff auf IoT Hub angegeben. Ein Gerät für IoT Hub kann über das Azure-Portal erstellt und die zugehörige Verbindungszeichenfolge dort abgerufen werden.

Für den Zugriff auf Ressourcen mit OPC UA-Unterstützung werden von OPC UA X.509-Zertifikate und die zugehörigen privaten Schlüssel verwendet. Dies wird als OPC UA-Anwendungsauthentifizierung bezeichnet und zusätzlich zur OPC UA-Benutzerauthentifizierung verwendet. Von OPC Publisher wird ein dateisystembasierter Zertifikatspeicher verwendet, um alle Anwendungszertifikate zu verwalten. Beim Start wird von OPC Publisher geprüft, ob in diesem Zertifikatspeicher ein verwendbares Zertifikat vorhanden ist. Falls nicht, werden ein neues selbstsigniertes Zertifikat und ein neuer zugeordneter privater Schlüssel erstellt. Selbstsignierte Zertifikate bieten zwar nur eine schwache Authentifizierung, da sie nicht von einer vertrauenswürdigen Zertifizierungsstelle signiert wurden, ermöglichen aber immerhin die Verschlüsselung der Kommunikation mit der Ressource mit OPC UA-Unterstützung.

Die Sicherheit wird in der Konfigurationsdatei über das Flag `"UseSecurity": true,` aktiviert. Es wird automatisch der sicherste verfügbare Endpunkt auf den OPC UA-Servern ausgewählt, mit denen OPC Publisher eine Verbindung herstellen soll.
Von OPC Publisher wird standardmäßig die anonyme Benutzerauthentifizierung verwendet (zusätzlich zur oben beschriebenen Anwendungsauthentifizierung). OPC Publisher unterstützt jedoch auch die Benutzerauthentifizierung mit Benutzername und Kennwort. Dies kann wie weiter unten beschrieben über die REST-API-Konfigurationsschnittstelle oder wie folgt über die Konfigurationsdatei angegeben werden:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Bis zur OPC Publisher-Version 2.5 werden Benutzername und Kennwort außerdem in der Konfigurationsdatei verschlüsselt. Ab Version 2.6 werden Benutzername und Kennwort nur in Klartext unterstützt. Dies wird in der nächsten Version von OPC Publisher verbessert.

Damit die Sicherheitskonfiguration von OPC Publisher nach Neustarts erhalten bleibt, müssen das Zertifikat und der private Schlüssel im Verzeichnis des Zertifikatspeichers dem Dateisystem des IoT Edge-Hostbetriebssystems zugeordnet werden. Weitere Informationen finden Sie weiter oben unter „Angeben von Containererstellungsoptionen im Azure-Portal“.

## <a name="configuration-via-configuration-file"></a>Konfiguration per Konfigurationsdatei

OPC Publisher lässt sich am einfachsten über eine Konfigurationsdatei konfigurieren. Eine Beispielkonfigurationsdatei sowie eine Dokumentation ihres Formats werden in der Datei [`publishednodes.json`](https://raw.githubusercontent.com/Azure/Industrial-IoT/main/components/opc-ua/src/Microsoft.Azure.IIoT.OpcUa.Edge.Publisher/tests/Engine/publishednodes.json) in diesem Repository bereitgestellt.
Die Syntax der Konfigurationsdatei hat sich im Laufe der Zeit geändert. Von OPC Publisher können zwar weiterhin alte Formate gelesen werden, sie werden jedoch bei der (regelmäßig automatisch stattfindenden) Speicherung der Konfiguration in das aktuelle Format konvertiert.

Eine grundlegende Konfigurationsdatei sieht wie folgt aus:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Zur Optimierung der Netzwerkbandbreite werden von OPC UA-Ressourcen nur Datenänderungen an OPC Publisher gesendet, wenn sich die Daten geändert haben. Für den Fall, dass Datenänderungen häufiger oder regelmäßig veröffentlicht werden müssen, unterstützt OPC Publisher einen Heartbeat für jedes konfigurierte Datenelement, der durch zusätzliche Angabe des Schlüssels „HeartbeatInterval“ in der Konfiguration des Datenelements aktiviert werden kann. Das Intervall wird in Sekunden angegeben:
```
 "HeartbeatInterval": 3600,
```

Von einer OPC UA-Ressource wird immer der aktuelle Wert eines Datenelements gesendet, wenn OPC Publisher zum ersten Mal eine Verbindung mit ihm herstellt. Wenn Sie die Veröffentlichung dieser Daten in IoT Hub verhindern möchten, können Sie in der Konfiguration des Datenelements zusätzlich den Schlüssel „SkipFirst“ angegeben:
```
 "SkipFirst": true,
```

Beide Einstellungen können auch global über Befehlszeilenoptionen aktiviert werden.

## <a name="configuration-via-command-line-arguments"></a>Konfiguration über Befehlszeilenargumente

Es gibt mehrere Befehlszeilenargumente, die verwendet werden können, um globale Einstellungen für OPC Publisher festzulegen. Sie sind [hier](reference-command-line-arguments.md) beschrieben.


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Konfiguration über die integrierte OPC UA-Serverschnittstelle

>[!NOTE] 
> Dieses Feature steht nur bis zur Version 2.5 von OPC Publisher zur Verfügung.

OPC Publisher verfügt über einen integrierten OPC UA-Server am Port 62222. Durch diesen werden drei OPC UA-Methoden implementiert:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Auf diese Schnittstelle kann über eine OPC UA-Clientanwendung wie [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html) zugegriffen werden.

## <a name="configuration-via-iot-hub-direct-methods"></a>Konfiguration über direkte IoT Hub-Methoden

>[!NOTE] 
> Dieses Feature steht nur bis zur Version 2.5 von OPC Publisher zur Verfügung.

Von OPC Publisher werden die folgenden [direkten IoT Hub-Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) implementiert. Sie können weltweit über eine Anwendung unter Verwendung des [IoT Hub Device SDK](../iot-hub/iot-hub-devguide-sdks.md) aufgerufen werden:

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Wir haben eine [Beispielkonfigurationsanwendung](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) sowie eine [Anwendung zum Lesen von Diagnoseinformationen](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) aus OPC Publisher (Open-Source) unter Verwendung dieser Schnittstelle bereitgestellt.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Konfiguration über cloudbasierten begleitenden REST-Microservice

>[!NOTE] 
> Dieses Feature steht erst ab Version 2.6 von OPC Publisher zur Verfügung.

Ein cloudbasierter begleitender Microservice mit einer REST-Schnittstelle ist [hier](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md) beschrieben und verfügbar. Mit ihm kann OPC Publisher über eine OpenAPI-kompatible Schnittstelle konfiguriert werden (beispielsweise über Swagger).

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Konfiguration des einfachen JSON-Telemetrieformats über eine separate Konfigurationsdatei

>[!NOTE] 
> Dieses Feature steht nur bis zur Version 2.5 von OPC Publisher zur Verfügung.

OPC Publisher ermöglicht das Filtern der Teile des nicht standardisierten einfachen Telemetrieformats über eine separate Konfigurationsdatei, die über die Befehlszeilenoption „tc“ angegeben werden kann. Ohne Angabe einer Konfigurationsdatei wird das vollständige JSON-Telemetrieformat an IoT Hub gesendet. Das Format der separaten Telemetriekonfigurationsdatei wird [hier](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format) beschrieben.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie OPC Publisher konfiguriert haben, können Sie sich als Nächstes mit der Optimierung der Leistung und des Arbeitsspeichers des Edge-Moduls beschäftigen:

> [!div class="nextstepaction"]
> [Tutorial: Optimieren der Leistung und des Arbeitsspeichers von OPC Publisher](tutorial-publisher-performance-memory-tuning-opc-publisher.md)
