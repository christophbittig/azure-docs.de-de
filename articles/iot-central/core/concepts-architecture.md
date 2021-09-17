---
title: Architektonische Konzepte in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Architektur von Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273211"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-Architektur

Dieser Artikel bietet eine Übersicht über die wichtigsten Konzepte in der Azure IoT Central-Architektur.

## <a name="devices"></a>Geräte

Geräte tauschen Daten mit Ihrer Azure IoT Central-Anwendung aus. Ein Gerät kann Folgendes durchführen:

- Senden von Messungen wie Telemetrie
- Synchronisieren von Einstellungen mit Ihrer Anwendung

In Azure IoT Central werden die Daten, die ein Gerät mit Ihrer Anwendung austauschen kann, in einer Gerätevorlage angegeben. Weitere Informationen zu Gerätevorlagen finden Sie unter [Gerätevorlagen](concepts-device-templates.md).

Um mehr darüber zu erfahren, wie Geräte eine Verbindung mit Ihrer Azure IoT Central-Anwendung herstellen können, lesen Sie [Gerätekonnektivität](concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-Geräte

Ebenso wie Geräte, die mit den [Azure IoT-SDKs](https://github.com/Azure/azure-iot-sdks) erstellt wurden, können Sie auch [Azure IoT Edge Geräte](../../iot-edge/about-iot-edge.md) mit einer IoT Central-Anwendung verbinden. IoT Edge ermöglicht die Ausführung von Cloud Intelligence und benutzerdefinierter Logik direkt auf IoT-Geräten, die von IoT Central verwaltet werden. Sie können auch IoT Edge als Gateway verwenden, um anderen nachgeschalteten Geräten das Herstellen einer Verbindung mit IoT Central zu ermöglichen.

Weitere Informationen finden Sie unter [Verbinden eines Azure IoT Edge-Geräts mit einer Azure IoT Central-Anwendung](concepts-iot-edge.md).

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