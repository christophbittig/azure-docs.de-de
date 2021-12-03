---
title: Öffentliche Endpunkte und Netzwerke
description: Azure Video Analyzer macht eine Reihe von öffentlichen Netzwerkendpunkten verfügbar, die verschiedene Produktszenarien ermöglichen, einschließlich Verwaltung, Erfassung und Wiedergabe. In diesem Artikel wird erläutert, wie Sie auf öffentliche Endpunkte und Netzwerke zugreifen.
ms.topic: how-to
ms.date: 11/04/2021
ms.openlocfilehash: 2285408cbdc6fd9f633e8c34c253c2d5921bc4db
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552903"
---
# <a name="public-endpoints-and-networking"></a>Öffentliche Endpunkte und Netzwerke

Azure Video Analyzer macht eine Reihe von öffentlichen Netzwerkendpunkten verfügbar, die verschiedene Produktszenarien ermöglichen, einschließlich Verwaltung, Erfassung und Wiedergabe. Dieser Artikel beschreibt diese Endpunkte und enthält einige Details zur Verwendung. Das folgende Diagramm zeigt diese Endpunkte sowie einige wichtige Endpunkte, die von zugeordneten Azure-Diensten verfügbar gemacht werden.

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="Das Bild stellt öffentliche Endpunkte von Video Analyzer dar":::

## <a name="video-analyzer-endpoints"></a>Azure Video Analyzer-Endpunkte 

Dieser Abschnitt enthält eine Liste der Video Analyzer-Endpunkte.

### <a name="streaming"></a>Streaming

* **Zweck**: Macht Audio-, Video- und Rückschlussdaten verfügbar, die von [Video Analyzer-Playerwidgets](player-widget.md) oder kompatiblen DASH/HLS-Playern genutzt werden können.
* **Authentifizierung und Autorisierung**: Die Endpunktautorisierung wird durch Token erzwungen, die vom Video Analyzer-Dienst ausgegeben werden. Die Token sind auf ein einzelnes Video beschränkt und werden implizit basierend auf den Autorisierungsregeln ausgegeben, die pro Video auf die Client- und Verwaltungs-APIs angewendet werden. Der Autorisierungsfluss wird automatisch vom Video Analyzer-Playerwidget gehandhabt.
* **Anforderung**: Der Zugriff auf diese Gruppe von Endpunkten ist für die Wiedergabe von Inhalten über die Cloud erforderlich.

### <a name="client-apis"></a>Client-APIs

* **Zweck**: Macht Metadaten (Titel, Beschreibung usw.) für die [Video Analyzer-Videoressource](terminology.md#video) verfügbar. Dies ermöglicht die Anzeige von umfangreichen Videoobjekten in von Kunden entwickelten Clientanwendungen. Diese Metadaten werden vom Video Analyzer Player-Widget genutzt und können auch direkt von Kundenanwendungen genutzt werden.
* **Authentifizierung und Autorisierung**: Die Endpunktautorisierung wird durch eine Kombination aus kundendefinierten [Zugriffsrichtlinien](access-policies.md) und vom Kunden ausgestellten JWT-Token erzwungen. Eine oder mehrere Zugriffsrichtlinien können über die Video Analyzer-Verwaltungs-APIs definiert werden. Diese Richtlinien beschreiben den Umfang des Zugriffs und die erforderlichen Ansprüche, die für die Token überprüft werden müssen. Der Zugriff wird verweigert, wenn im Video Analyzer-Konto keine Zugriffsrichtlinien erstellt wurden.
* **Anforderung**: Der Zugriff auf diesen Endpunkt ist für das Video Analyzer-Player-Widget und ähnliche vom Kunden entwickelte Clientanwendungen erforderlich, um Videometadaten abzurufen und die Wiedergabe zu genehmigen.

### <a name="edge-service-integration"></a>Edge Service-Integration

* **Zweck**: 

    * Macht Richtlinien verfügbar, die in bestimmten Abständen vom Video Analyzer-Edgemodul heruntergeladen werden. Solche Richtlinien steuern grundlegende Verhaltensweisen des Edgemoduls, z. B. Abrechnungs- und Konnektivitätsanforderungen.
    * Orchestrierung der Videoveröffentlichung in der Cloud, einschließlich Abrufen von AZURE Storage-SAS-URLs, mit denen das Video Analyzer-Edgemodul Videodaten im Speicherkonto des Kunden aufzeichnen kann.
* **Authentifizierung und Autorisierung**: Die erste Authentifizierung erfolgt über ein kurzlebiges Bereitstellungstoken, das von den Video Analyzer-Verwaltungs-APIs ausgegeben wird. Sobald der anfängliche Handshake abgeschlossen ist, tauschen das Modul und der Dienst einen Satz automatisch rotierender Autorisierungsschlüssel aus, die ab diesem Zeitpunkt verwendet werden.
* **Anforderung**: Der Zugriff auf diesen Endpunkt ist für die ordnungsgemäße Funktionsweise des Video Analyzer Edge-Moduls erforderlich. Das Edgemodul funktioniert nicht mehr, wenn dieser Endpunkt innerhalb eines Zeitraums von 36 Stunden nicht erreicht werden kann.

## <a name="telemetry"></a>Telemetrie

* **Zweck**: Optionale regelmäßige Übermittlung von Telemetriedaten, die Microsoft ermöglicht, besser zu verstehen, wie das Video Analyzer-Edgemodul verwendet wird, und proaktiv mögliche Verbesserungen in Bezug auf Kompatibilität, Leistung und andere Produktbereiche zu erkennen.
* **Authentifizierung und Autorisierung**: Die Autorisierung basiert auf einem vorab festgelegten Schlüssel.
* **Anforderung**: Der Zugriff auf diesen Endpunkt ist optional und beeinträchtigt nicht die Produktfunktionalität. Die Datensammlung und -übermittlung kann über die Eigenschaften des Modulzwillings deaktiviert werden.

## <a name="associated-azure-endpoints"></a>Zugeordnete Azure-Endpunkte 

> [!NOTE]
> Die in diesem Artikel beschriebene Liste der Endpunkte ist nicht als umfassende Liste der zugeordneten Dienstendpunkte gedacht. Es handelt sich um eine informative Liste der Endpunkte, die für den normalen Betrieb von Video Analyzer erforderlich sind. Eine vollständige Liste der Endpunkte, die von den einzelnen Diensten verfügbar gemacht werden, finden Sie in der Dokumentation zu den einzelnen Azure-Diensten.

## <a name="azure-storage"></a>Azure Storage

* **Zweck**: Aufzeichnen von Audio-, Video- und Rückschlussdaten, wenn [Pipelines](pipeline.md) für die Speicherung von Videodaten in der Cloud über den Knoten der [Videosenke](pipeline.md#video-sink) konfiguriert sind.
* **Authentifizierung und Autorisierung**: Die Autorisierung wird durchgeführt, indem die Standardvorgänge für die Authentifizierung und Autorisierung von Azure Storage-Diensten erzwungen werden. In diesem Fall erfolgt der Zugriff auf den Speicher über containerspezifische SAS-URLs.
* **Anforderung**: Der Zugriff auf diesen Endpunkt ist nur erforderlich, wenn eine Video Analyzer-Edgepipeline für die Archivierung des Videos in der Cloud konfiguriert ist.

## <a name="iot-hub"></a>IoT Hub

* **Zweck**: Steuerungs- und Datenebene für Azure IoT Hub- und Edgegeräte.
* **Authentifizierung und Autorisierung**: Weitere Informationen finden Sie in der Azure IoT Hub-Dokumentation.
* **Anforderung**: Ein richtig konfiguriertes und funktionierendes Edgegerät mit Azure IoT Edge Runtime ist erforderlich, um sicherzustellen, dass das Edgemodul von Azure Video Analyzer korrekt funktioniert.

##  <a name="tls-encryption"></a>TLS-Verschlüsselung 

* **Verschlüsselung und Serverauthentifizierung**: Alle Video Analyzer-Endpunkte werden über TLS 1.2-konforme Endpunkte verfügbar gemacht.

##  <a name="references"></a>Referenzen 

Öffentlich:

* [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Informationen zu Azure IoT Hub-Endpunkten](../../iot-hub/iot-hub-devguide-endpoints.md)
* [Was ist Azure Private Link?](../../private-link/private-link-overview.md)
* [Übersicht über Azure-Diensttags](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Zugriffsrichtlinien](access-policies.md) 
