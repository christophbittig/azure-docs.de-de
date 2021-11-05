---
title: Azure IoT Zentrale Skalierbarkeit und Hochverfügbarkeit | Microsoft-Dokumente
description: In diesem Abschnitt wird beschrieben, wie IoT Central automatisch skaliert wird, um mehr Geräte und dessen Hochverfügbarkeit zu bedienen.
author: dominicbetts
ms.author: dobett
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bea4e5d7dcb4349f17a4a967d8e0ef15e6f737b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095608"
---
# <a name="scalability-and-high-availability"></a>Skalierbarkeit und Hochverfügbarkeit

IoT Central-Anwendungen nutzen intern mehrere Azure-Dienste, wie zum Beispiel den IoT Hub den Gerätebereitstellungsdienst (Device Provisioning Service DPS). Viele dieser zugrunde liegenden Dienste sind mehrstufige Dienste. Um jedoch die vollständige Isolierung von Kundendaten sicherzustellen, verwendet IoT Central IoT Hubs mit nur einem Mandanten. IoT Central verwaltet automatisch mehrere Instanzen der zugrunde liegenden Dienste, um Ihre IoT Central-Anwendungen zu skalieren und sie hoch verfügbar zu machen.

IoT Central skaliert automatisch dessen IoT Hubs, basierend auf den Lastprofilen in Ihrer Anwendung. IoT Central kann einzelne IoT Hubs hochskalieren und die Anzahl der IoT Hubs aufskalieren. Für eine hoch verfügbare Gerätekonnektivität verfügt IoT Central immer über mindestens zwei IoT Hubs. Obwohl IoT Central IoT Hubs für Sie verwaltet, wirkt sich die Verwendung mehrerer IoT Hubs auf die Implementierung Ihrer Gerätefirmware aus.

Die IoT Hubs in einer IoT Central-Anwendung befinden sich alle in derselben Azure-Region. Aus diesem Grund bietet die Architektur mit mehreren IoT Hubs eine hoch verfügbare Gerätekonnektivität, wenn es zu einem isolierten Ausfall kommt. Wenn eine gesamte Azure-Region nicht mehr verfügbar ist, führen Notfallwiederherstellungsverfahren ein Failover für IoT Central-Anwendungen in einer anderen Region aus.

## <a name="device-provisioning"></a>Gerätebereitstellung

Bevor ein Gerät eine Verbindung mit IoT Central herstellt, muss es bei den zugrunde liegenden Diensten registriert und bereitgestellt werden. Wenn Sie ein Gerät zu einer IoT Central-Anwendung hinzufügen, fügt IoT Central einen Eintrag zu einer DPS-Registrierungsgruppe hinzu. Informationen aus der Registrierungsgruppe wie ID-Bereich, Geräte-ID und Schlüssel werden auf der IoT Central-Benutzeroberfläche angezeigt.

Wenn ein Gerät zum ersten Mal eine Verbindung mit Ihrer IoT Central-Anwendung herstellt, stellt DPS das Gerät in einer der mit den IoT Hubs verbundenen Registrierungsgruppen zur Verfügung. DPS verwendet eine Zuordnungsrichtlinie, um einen Lastenausgleich für die Bereitstellung über die IoT Hubs in der Anwendung herzustellen. Dadurch wird sichergestellt, dass jeder IoT Hub über eine ähnliche Anzahl von bereitgestellten Geräten verfügt.

Weitere Informationen zur Registrierung und Bereitstellung in IoT Central finden Sie unter [Verbinden mit Azure IoT Central.](concepts-get-connected.md)

## <a name="device-connections"></a>Geräteverbindungen

Nachdem DPS ein Gerät für einen IoT Hub zur Verfügung stellt, versucht das Gerät immer, eine Verbindung zu diesem Hub herzustellen. Wenn ein Gerät den bereitgestellten IoT-Hub nicht erreichen kann, kann es keine Verbindung mit Ihrer IoT Central herstellen. Um dieses Szenario in den Griff zu bekommen, sollte Ihre Gerätefirmware eine Wiederholungsstrategie enthalten.

Weitere Informationen dazu, wie die Gerätefirmware Verbindungsfehler behandeln und eine Verbindung mit einem anderen Hub herstellen soll, finden Sie unter [Bewährte Methoden für die Geräteentwicklung](concepts-best-practices.md).

Weitere Informationen zum Überprüfen, wie Ihre Gerätefirmware Verbindungsfehler behandeln kann, finden Sie unter [Testen von Failover-Funktionen](concepts-best-practices.md#test-failover-capabilities).

## <a name="data-export"></a>Datenexport

IoT Central-Anwendungen verwenden häufig andere, vom Benutzer konfigurierte Dienste. Beispielsweise können Sie Ihre IoT Central-Anwendung so konfigurieren, dass Daten kontinuierlich in Dienste wie Azure Event Hubs und Azure Blob Storage exportiert werden.

Wenn ein konfigurierter Datenexport sich nicht in sein Ziel einschreiben kann, versucht IoT Central bis zu 15 Minuten lang, die Daten erneut zu übertragen. Anschließend markiert IoT Central das Ziel als fehlgeschlagen. Fehlerhafte Ziele werden regelmäßig überprüft, um zu überprüfen, ob sie schreibbar sind.

Sie können erzwingen, dass IoT Central die fehlerhaften Exporte erneut startet, indem Sie den Datenexport deaktivieren und erneut aktivieren.

Hier finden Sie bewährte Methoden für Hochverfügbarkeit und Skalierbarkeit für den von Ihnen verwendeten Datenexportzieldienst an:

- Azure Blob-Speicher: [Azure Speicherredundanz](../../storage/common/storage-redundancy.md) und [eine Checkliste für Leistung und Skalierbarkeit für Blob-Speicher](../../storage/blobs/storage-performance-checklist.md)
- Azure Event Hubs: [Verfügbarkeit und Konsistenz in Event Hubs](../../event-hubs/event-hubs-availability-and-consistency.md) und [Skalierung mit Event Hubs](../../event-hubs/event-hubs-scalability.md)
- Azure Service Bus: [Bewährte Methoden zum Schutz von Anwendungen vor Service-Bus-Ausfällen und -Notfällen](../../service-bus-messaging/service-bus-outages-disasters.md) und [ automatisches Aktualisieren von Messaging-Einheiten eines Azure Service-Bus-Namensraumes](../../service-bus-messaging/automate-update-messaging-units.md)

## <a name="limitations"></a>Einschränkungen

Derzeit gibt es einige ältere IoT Central-Anwendungen, die vor April 2021 erstellt wurden und die noch nicht auf die IoT Mehrfach-Hub-Architektur aufgerüstet wurden. Verwenden Sie den Befehl `az iot central device manual-failover`, um zu überprüfen, ob Ihre Anwendung immer noch einen einzelnen IoT Hub verwendet.

Derzeit können IoT Edge-Geräte nicht zwischen IoT Hubs wechseln.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie etwas über die Skalierbarkeit und die Hochverfügbarkeit von Azure IoT Central erfahren haben, wird als nächster Schritt empfohlen, dass Sie sich über die [Gerätekonnektivität](concepts-get-connected.md) in Azure IoT Central informieren.
