---
title: Architektonische Konzepte in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Architektur von Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 46b8cdc7fa33c8ddd382decb49eaa148093c99fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355614"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-Architektur

Dieser Artikel bietet eine Übersicht über die wichtigsten Konzepte in der Azure IoT Central-Architektur.

## <a name="devices"></a>Geräte

Geräte tauschen Daten mit Ihrer Azure IoT Central-Anwendung aus. Ein Gerät kann Folgendes durchführen:

- Senden von Messungen wie Telemetrie
- Synchronisieren von Einstellungen mit Ihrer Anwendung

In Azure IoT Central werden die Daten, die ein Gerät mit Ihrer Anwendung austauschen kann, in einer Gerätevorlage angegeben. Weitere Informationen zu Gerätevorlagen finden Sie unter [Gerätevorlagen](concepts-device-templates.md).

Um mehr darüber zu erfahren, wie Geräte eine Verbindung mit Ihrer Azure IoT Central-Anwendung herstellen können, lesen Sie [Gerätekonnektivität](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-Geräte

Ebenso wie Geräte, die mit den [Azure IoT-SDKs](https://github.com/Azure/azure-iot-sdks) erstellt wurden, können Sie auch [Azure IoT Edge Geräte](../../iot-edge/about-iot-edge.md) mit einer IoT Central-Anwendung verbinden. IoT Edge ermöglicht die Ausführung von Cloud Intelligence und benutzerdefinierter Logik direkt auf IoT-Geräten, die von IoT Central verwaltet werden. Die IoT Edge-Laufzeit ermöglicht Ihnen Folgendes:

- Installieren und Aktualisieren von Workloads auf dem Gerät
- Aufrechterhalten von IoT Edge-Sicherheitsstandards auf dem Gerät
- Sicherstellen, dass die IoT Edge-Module immer ausgeführt werden
- Melden der Modulintegrität an die Cloud für die Remoteüberwachung
- Verwalten der Kommunikation zwischen nachgeschalteten Blattknotengeräten und einem IoT Edge-Gerät, zwischen Modulen auf einem IoT Edge-Gerät sowie zwischen einem IoT Edge-Gerät und der Cloud.

![Azure IoT Central mit Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central bietet die folgenden Funktionen für IoT Edge-Geräte:

- Gerätevorlagen zum Beschreiben der Funktionen eines IoT Edge-Geräts, wie z.B.:
  - Funktion zum Upload eines Bereitstellungsmanifests, wodurch ein Manifest für eine Geräteflotte verwaltet werden kann
  - Module, die auf dem IoT Edge-Gerät ausgeführt werden
  - Die von jedem Modul gesendeten Telemetriedaten
  - Die von jedem Modul gemeldeten Eigenschaften
  - Die Befehle, auf die jedes Modul reagiert
  - Die Beziehungen zwischen einem IoT Edge-Gatewaygerät und dem nachgeschalteten Gerät
  - Cloudeigenschaften, die auf dem IoT Edge-Gerät nicht gespeichert werden
  - Anpassungen, die ändern, wie Gerätefunktionen auf der Benutzeroberfläche angezeigt werden.
  - Geräteansichten und Formulare.

  Weitere Informationen finden Sie im Artikel [Verbinden eines Azure IoT Edge-Geräts mit einer Azure IoT Central-Anwendung](./concepts-iot-edge.md).

- Die Funktion zum Bereitstellen von IoT Edge-Geräten nach Maß mit dem Azure IoT-Gerätebereitstellungsdienst
- Regeln und Aktionen
- Benutzerdefinierte Dashboards und Analysen
- Fortlaufender Datenexport von Telemetriedaten von IoT Edge-Geräten

### <a name="iot-edge-device-types"></a>IoT Edge-Gerätetypen

IoT Central klassifiziert IoT Edge-Gerätetypen folgendermaßen:

- Blattgeräte. Ein IoT Edge-Gerät kann über nachgeschaltete Blattgeräte verfügen, doch diese Geräte werden in IoT Central nicht bereitgestellt.
- Gatewaygeräte mit nachgeschalteten Geräten. Sowohl das Gatewaygerät als auch nachgeschaltete Geräte werden in IoT Central bereitgestellt.

![Übersicht über IoT Central mit IoT Edge](./media/concepts-architecture/gatewayedge.png)

> [!NOTE]
> IoT Central unterstützt zurzeit nicht das Verbinden eines IoT Edge Geräts als nachgeschaltetes Gerät mit einem IoT Edge-Gateway. Der Grund: Alle Geräte, die eine Verbindung mit IoT Central herstellen, werden mit dem Device Provisioning Service (DPS) bereitgestellt, und DPS unterstützt keine geschachtelten IoT Edge-Szenarios.

### <a name="iot-edge-patterns"></a>IoT Edge-Muster

IoT Central unterstützt die folgenden IoT Edge-Gerätemuster:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge als Blattgerät

![IoT Edge als Blattgerät](./media/concepts-architecture/edgeasleafdevice.png)

Das IoT Edge-Gerät wird in IoT Central bereitgestellt; alle nachgeschalteten Geräte und deren Telemetriedaten werden so dargestellt, als stammten sie vom IoT Edge-Gerät. Nachgeschaltete Geräte, die mit dem IoT Edge-Gerät verbunden sind, werden in IoT Central nicht bereitgestellt.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist

![IoT Edge mit nachgeschalteter Geräteidentität](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Runtime-Unterstützung für die Bereitstellung von nachgeschalteten Geräten über das Gateway gibt es derzeit nicht.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist, die vom IoT Edge-Gateway bereitgestellt wird

![IoT Edge mit nachgeschaltetem Gerät ohne Identität](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Runtime-Unterstützung für die Bereitstellung von Identitätsdaten für nachgeschaltete Geräte durch ein Gateway und die Bereitstellung nachgeschalteter Geräte gibt es derzeit nicht. Wenn Sie ein eigenes Modul für die Identitätsübersetzung einsetzen, kann IoT Central dieses Muster unterstützen.

## <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Central verwendet Azure IoT Hub als Cloudgateway, das Gerätekonnektivität ermöglicht. IoT Hub ermöglicht Folgendes:

- Datenerfassung im großen Maßstab in der Cloud
- Geräteverwaltung
- Sichere Gerätekonnektivität

Weitere Informationen zu IoT Hub finden Sie unter [Dokumentation zu IoT Hub](../../iot-hub/index.yml).

Weitere Informationen zur Gerätekonnektivität in Azure IoT Central finden Sie unter [Gerätekonnektivität](concepts-get-connected.md).

## <a name="data-stores"></a>Datenspeicher

Azure IoT Central speichert Anwendungsdaten in der Cloud. Zu den gespeicherten Anwendungsdaten zählen Folgende:

- Gerätevorlagen
- Geräteidentitäten
- Gerätemetadaten
- Benutzer- und Rollendaten

Azure IoT Central verwendet einen Zeitreihenspeicher für die Messdaten, die von Ihren Geräten gesendet werden. Die Zeitreihendaten von Geräten werden vom Analysedienst verwendet.

## <a name="data-export"></a>Datenexport

In einer Azure IoT Central-Anwendung können Sie einen [fortlaufenden Export Ihrer Daten](howto-export-data.md) auf Ihre eigenen Azure Event Hubs und Azure Service Bus-Instanzen durchführen. Außerdem können Sie Ihre Daten in regelmäßigen Abständen in Ihr Azure-Blobspeicherkonto exportieren. Mit IoT Central können Messungen, Geräte und Gerätevorlagen exportiert werden.

## <a name="batch-device-updates"></a>Geräteupdates als Batchvorgang

In einer Azure IoT Central-Anwendung können Sie [Aufträge erstellen und ausführen](howto-manage-devices-in-bulk.md), um verbundene Geräte zu verwalten. Mit diesen Aufträgen können Sie Massenaktualisierungen an Geräteeigenschaften oder -einstellungen vornehmen oder Befehle ausführen. Sie können beispielsweise einen Auftrag erstellen, um die Lüfterdrehzahl für mehrere Verkaufsautomaten mit Kühlung zu erhöhen.

## <a name="role-based-access-control-rbac"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)

Jede IoT Central-Anwendung verfügt über ein eigenes integriertes RBAC-System. Ein Administrator kann mithilfe einer der vordefinierten Rollen oder durch Erstellen einer benutzerdefinierten Rolle [Zugriffsregeln für eine Azure IoT Central-Anwendung definieren](howto-manage-users-roles.md). Über Rollen wird festgelegt, auf welche Bereiche einer Anwendung ein Benutzer zugreifen und welche Aktionen er ausführen darf.

## <a name="security"></a>Sicherheit

Zu den Sicherheitsfeatures in Azure IoT Central gehören Folgende:

- Während der Übertragung und im Ruhezustand verschlüsselte Daten
- Die von Azure Active Directory oder vom Microsoft-Konto bereitgestellte Authentifizierung. Unterstützung für zweistufige Authentifizierung
- Vollständige Mandantenisolation
- Sicherheit auf Geräteebene

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Architektur von Azure IoT Central erfahren haben, besteht der nächste empfohlene Schritt darin, sich mit der [Gerätekonnektivität](concepts-get-connected.md) in Azure IoT Central vertraut zu machen.