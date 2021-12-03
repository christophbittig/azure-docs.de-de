---
title: Informationen zu Warnmeldungen
description: Wählen Sie im Fenster „Warnungen“ eine Warnung aus, um Details dazu anzuzeigen.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d76b10943932825388075b7aef44f0f5b94c1d53
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278567"
---
# <a name="about-alert-messages"></a>Informationen zu Warnmeldungen

Wählen Sie im Fenster **Warnungen** eine Warnung aus, um Details zu dieser Warnung anzuzeigen. Folgende Informationen sind enthalten:

- Metadaten zur Warnung

- Informationen zu Datenverkehr, Geräten und Ereignis

- Links zu verbundenen Geräten in der Gerätezuordnung

- Von Sicherheitsanalysten und Administratoren definierte Kommentare

- Empfehlungen für die Untersuchung des Ereignisses

## <a name="alert-metadata"></a>Metadaten zur Warnung

Die Warnungsdetails enthalten die folgenden Metadaten:

  - Warnungs-ID

  - Richtlinien-Engine, die die Warnung ausgelöst hat

  - Datum und Uhrzeit der Warnungsauslösung

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Nicht autorisierte Internetverbindung erkannt":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informationen zu Datenverkehr, Geräten und Ereignis

Die Warnung stellt folgende Informationen bereit:

  - Die erkannten Geräte

  - Der zwischen den Geräten erkannte Datenverkehr, z. B. Protokolle und Funktionscodes

  - Erkenntnisse zu den Auswirkungen des Ereignisses

Sie können anhand dieser Informationen entscheiden, wie mit dem Warnungsereignis verfahren werden soll.

## <a name="links-to-connected-devices-in-the-device-map"></a>Links zu verbundenen Geräten in der Gerätezuordnung

Um mehr über Geräte zu erfahren, die mit den erkannten Geräten verbunden sind, können Sie eine Gerätedarstellung in der Warnung auswählen und dann die verbundenen Geräte in der Zuordnung anzeigen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Fehler beim RCP-Vorgang":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Screenshot der Geräte":::

Die Karte wird nach dem ausgewählten Gerät und den anderen mit diesem verbundenen Geräten gefiltert. Außerdem wird in der Zuordnung auch das Dialogfeld **Quick Properties** (Schnelle Eigenschaften) für die Geräte angezeigt, die in den Warnungen erkannt wurden.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Von Sicherheitsanalysten und Administratoren definierte Kommentare 

Warnungen können eine Liste vordefinierter Kommentare enthalten. Diese Kommentare können z. B. Anweisungen für die anzuwendenden Entschärfungsmaßnahmen oder die Namen von Personen sein, die bei diesem Ereignis informiert werden müssen.

Wenn Sie ein Warnungsereignis verwalten, können Sie die Kommentare auswählen, die den Ereignisstatus oder die unternommenen Schritte zum Untersuchen der Warnung am besten widerspiegeln.

Die ausgewählten Kommentare werden in der Warnmeldung gespeichert. Durch die Verwendung von Kommentaren wird die Kommunikation zwischen Einzelpersonen und Teams bei der Untersuchung eines Warnungsereignisses verbessert. Daher können Kommentare die Reaktionszeit bei einem Vorfall verkürzen.

Die Kommentare werden von einem Administrator oder Sicherheitsanalysten definiert. Ausgewählte Kommentare werden nicht an Partnersysteme weitergeleitet, die in den Weiterleitungsregeln definiert sind.

Nachdem Sie die Informationen in einer Warnung überprüft haben, können Sie verschiedene forensische Maßnahmen durchführen, um das Warnungsereignis zu behandeln. Beispiel:

- Analysieren Sie die aktuelle Geräteaktivität (Data Mining-Bericht). 

- Analysieren Sie weitere Ereignisse, die zur gleichen Zeit aufgetreten sind (Ereigniszeitachse). 

- Analysieren Sie den umfassenden Datenverkehr beim Ereignis (PCAP-Datei).

## <a name="pcap-files"></a>PCAP-Dateien

In einigen Fällen können Sie über die Warnmeldung auf eine PCAP-Datei zugreifen. Dies kann hilfreich sein, wenn Sie ausführlichere Informationen zum zugehörigen Netzwerkdatenverkehr benötigen.

Wählen Sie zum Herunterladen der PCAP-Datei das Symbol :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Herunterladen"::: rechts oben im Dialogfeld **Warnungsdetails** aus.

## <a name="recommendations-for-investigating-an-event"></a>Empfehlungen für die Untersuchung eines Ereignisses 

Der Bereich **Empfehlung** einer Warnung enthält Informationen, die Ihnen helfen können, ein Ereignis besser zu verstehen. Sehen Sie sich diese Informationen an, bevor Sie das Warnungsereignis behandeln oder auf dem Gerät oder im Netzwerk Aktionen ausführen.

## <a name="see-also"></a>Weitere Informationen

[Beschleunigen des Warnungsworkflows](how-to-accelerate-alert-incident-response.md)

[Verwalten des Warnungsereignisses](how-to-manage-the-alert-event.md)
