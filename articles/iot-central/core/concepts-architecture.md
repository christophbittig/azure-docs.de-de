---
title: Architektonische Konzepte in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Architektur von Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 58c9f69b898a41a1d07ae41755f67d926b2ac74f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082453"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-Architektur

Dieser Artikel bietet eine Übersicht über die wichtigsten Elemente in einer IoT Central-Lösungsarchitektur.

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="Allgemeine Architektur einer IoT Central-Lösung" border="false":::

Eine IoT Central-Anwendung:

- Ermöglicht Ihnen die Verwaltung der IoT-Geräte in Ihrer Lösung
- Ermöglicht Ihnen das Anzeigen und Analysieren der Daten von Ihren Geräten
- Kann in andere Dienste, die Teil der Lösung sind, exportiert und mit diesen integriert werden

## <a name="iot-central"></a>IoT Central

IoT Central ist eine vorgefertigte Umgebung für die IoT-Lösungsentwicklung. Es handelt sich um eine PaaS-IoT-Lösung (Platform as a Service), und die primäre Schnittstelle ist eine Web-UI. Es gibt auch eine [REST-API](#rest-api), mit der Sie programmgesteuert mit Ihrer Anwendung interagieren können.

In diesem Abschnitt werden die wichtigsten Funktionen einer IoT Central-Anwendung beschrieben.

### <a name="manage-devices"></a>Verwalten von Geräten

Mit IoT Central können Sie den Bestand an [IoT-Geräten](#devices) verwalten, die Daten an Ihre Lösung senden. Beispielsweise können Sie folgende Aktionen ausführen:

- Steuern Sie, welche Geräte eine [Verbindung](concepts-get-connected.md) mit Ihrer Anwendung herstellen können und wie sie sich authentifizieren.
- Verwenden Sie [Gerätevorlagen](concepts-device-templates.md), um die Gerätetypen zu definieren, die eine Verbindung mit Ihrer Anwendung herstellen können.
- Verwalten Sie Geräte, indem Sie Eigenschaften festlegen oder Befehle auf verbundenen Geräten aufrufen. Legen Sie beispielsweise eine Zieltemperatureigenschaft für ein Thermostat fest, oder rufen Sie einen Befehl auf, um für ein Gerät ein Update seiner Firmware auszulösen. Sie können folgendermaßen Eigenschaften festlegen und Befehle aufrufen:
  - Für einzelne Geräte über eine [anpassbare](concepts-device-templates.md#views) Webbenutzeroberfläche
  - Für mehrere Geräte mit geplanten oder bedarfsorientierten [Aufträgen](howto-manage-devices-in-bulk.md)
- Verwalten Sie [Gerätemetadaten](concepts-device-templates.md#cloud-properties) wie Kundenadresse oder Datum des letzten Diensts.

### <a name="view-and-analyze-data"></a>Anzeigen und Analysieren von Daten

In einer IoT Central-Anwendung können Sie Daten für einzelne Geräte oder aggregierte Daten von mehreren Geräten anzeigen und analysieren:

- Verwenden Sie Gerätevorlagen, um [benutzerdefinierte Ansichten](howto-set-up-template.md#views) für einzelne Geräte bestimmter Typen zu definieren. Sie können z. B. die Temperatur im Zeitverlauf für ein einzelnes Thermostat zeichnen oder den Livestandort eines Lieferlastwagens anzeigen.
- Verwenden Sie die integrierte [Analyse](tutorial-use-device-groups.md), um aggregierte Daten für mehrere Geräte anzuzeigen. Sie können z. B. die Gesamtauslastung in mehreren Einzelhandelsläden anzeigen oder die Filialen mit den höchsten oder niedrigsten Auslastungsraten identifizieren.
- Erstellen Sie benutzerdefinierte [Dashboards](howto-manage-dashboards.md), um Ihre Geräte zu verwalten. Sie können z. B. Karten, Kacheln und Diagramme hinzufügen, um Gerätetelemetriedaten anzuzeigen.  

### <a name="secure-your-solution"></a>Sichern Ihrer Lösung

In einer IoT Central-Anwendung können Sie die folgenden Sicherheitsaspekte Ihrer Lösung verwalten:

- [Gerätekonnektivität](concepts-get-connected.md): Erstellen, Widerrufen und Aktualisieren der Sicherheitsschlüssel, mit denen Ihre Geräte eine Verbindung mit Ihrer Anwendung herstellen.
- [App-Integrationen](howto-authorize-rest-api.md#get-an-api-token): Erstellen, Widerrufen und Aktualisieren der Sicherheitsschlüssel, die andere Anwendungen verwenden, um sichere Verbindungen mit Ihrer Anwendung herzustellen.
- [Datenexport:](howto-export-data.md#connection-options) Verwenden Sie verwaltete Identitäten, um die Verbindung mit Ihren Datenexportzielen zu sichern.
- [Benutzerverwaltung](howto-manage-users-roles.md): Verwalten der Benutzer, die sich bei der Anwendung anmelden können, und der Rollen, die bestimmen, über welche Berechtigungen diese Benutzer verfügen.
- [Organisationen](howto-create-organizations.md): Definieren einer Hierarchie, um zu verwalten, welche Benutzer sehen können, welche Geräte in Ihrer IoT Central-Anwendung angezeigt werden.

### <a name="rest-api"></a>REST-API

Erstellen Sie Integrationen, mit denen andere Anwendungen und Dienste Ihre Anwendung verwalten können. Beispielsweise können Sie die [Geräte](howto-control-devices-with-rest-api.md) in Ihrer Anwendung programmgesteuert verwalten oder [Benutzerinformationen](howto-manage-users-roles-with-rest-api.md) mit einem externen System synchronisieren.

## <a name="devices"></a>Geräte

Geräte sammeln Daten von Sensoren, die als Telemetriedatenstrom an eine IoT Central Anwendung gesendet werden. Beispielsweise sendet eine Kühleinheit einen Datenstrom mit Temperaturwerten, oder ein Lieferlastwagen streamt seinen Standort.

Ein Gerät kann Eigenschaften verwenden, um seinen Zustand zu melden, z. B. ob ein Ventil geöffnet oder geschlossen ist. Eine IoT Central-Anwendung kann auch Eigenschaften verwenden, um den Gerätezustand festzulegen, z. B. eine Zieltemperatur für ein Thermostat einstellen.

IoT Central kann Geräte auch steuern, indem Befehle auf dem Gerät aufgerufen werden. Ein Beispiel ist die Anweisung an ein Gerät, ein Firmwareupdate herunterzuladen und zu installieren.

Die [Telemetrie, Eigenschaften und Befehle](concepts-telemetry-properties-commands.md), die ein Gerät implementiert, werden zusammen als Gerätefunktionen bezeichnet. Sie definieren diese Funktionen in einem Modell, das vom Gerät und der IoT Central-Anwendung gemeinsam genutzt wird. In IoT Central ist dieses Modell Teil der Gerätevorlage, die einen bestimmten Gerätetyp definiert.

Die [Geräteimplementierung](tutorial-connect-device.md) sollte die [IoT Plug & Play-Konventionen](../../iot-develop/concepts-convention.md) befolgen, um sicherzustellen, dass eine Kommunikation mit IoT Central möglich ist. Weitere Informationen finden Sie in den verschiedenen Sprach-[SDKs und Beispielen](../../iot-develop/libraries-sdks.md).

Geräte stellen über eines der unterstützten Protokolle eine Verbindung mit IoT Central her: [MQTT, AMQP oder HTTP](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="gateways"></a>Gateways

Lokale Gerätegateways sind in verschiedenen Szenarios nützlich, z. B.:

- Geräte können möglicherweise keine direkte Verbindung mit IoT Central herstellen, da sie keine Verbindung mit dem Internet herstellen können. Sie können z. B. über eine Sammlung von Bluetooth-aktivierten Auslastungssensoren verfügen, die über ein Gateway verbunden werden müssen.
- Die Menge der von Ihren Geräten generierten Daten kann hoch sein. Zur Kostensenkung können Sie die Daten in einem lokalen Gateway kombinieren oder aggregieren, bevor sie an Ihre IoT Central-Anwendung gesendet werden.
- Ihre Lösung erfordert möglicherweise schnelle Antworten auf Anomalien in den Daten. Sie können Regeln auf einem Gateway ausführen, die Anomalien identifizieren, und lokal Maßnahmen ergreifen, ohne Daten an Ihre IoT Central-Anwendung senden zu müssen.

Weitere Informationen finden Sie unter [Verbinden eines Azure IoT Edge-Geräts mit einer Azure IoT Central-Anwendung](concepts-iot-edge.md).

## <a name="data-export"></a>Datenexport

IoT Central verfügt zwar über integrierte Analysefeatures, Sie können Daten aber in andere Dienste und Anwendungen exportieren. Gründe für den Export von Daten:

### <a name="storage-and-analysis"></a>Speicher und Analyse

Für die langfristige Speicherung und Kontrolle über Archivierungs- und Aufbewahrungsrichtlinien können Sie Ihre [Daten kontinuierlich in andere Speicherziele exportieren](howto-export-data.md). Mithilfe von separatem Speicher können Sie auch andere Analysetools verwenden, um Erkenntnisse zu gewinnen und die Daten in Ihrer Lösung anzuzeigen.

### <a name="business-automation"></a>Geschäftsautomatisierung

Mit [Regeln](howto-configure-rules-advanced.md) in IoT Central können Sie externe Aktionen auslösen, z. B. das Senden einer E-Mail oder das Auslösen eines Ereignisses als Reaktion auf Bedingungen innerhalb von IoT Central. Beispielsweise können Sie einen Techniker benachrichtigen, wenn die Umgebungstemperatur für ein Gerät einen Schwellenwert erreicht.

### <a name="additional-computation"></a>Zusätzliche Berechnung

Möglicherweise müssen Sie [Berechnungen für Ihre Daten transformieren oder durchführen](howto-transform-data.md), bevor sie in IoT Central oder einem anderen Dienst verwendet werden können. Beispielsweise können Sie den Standortdaten, die von einem Lieferlastwagen gemeldet werden, lokale Wetterinformationen hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie etwas über die Architektur von Azure IoT Central erfahren haben, wird als nächster Schritt empfohlen, dass Sie sich über [Skalierbarkeit und Hochverfügbarkeit](concepts-scalability-availability.md) in Azure IoT Central informieren.
