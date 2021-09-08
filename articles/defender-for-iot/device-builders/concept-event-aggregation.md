---
title: Micro-Agent-Ereigniserfassung (Vorschau)
description: Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6836c12f625645c5b9e0913f9d2f3f36fc06843e
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397500"
---
# <a name="micro-agent-event-collection-preview"></a>Micro-Agent-Ereigniserfassung (Vorschau)

Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud. Der Defender für IoT-Micro-Agent sammelt viele Arten von Geräteereignissen, einschließlich neuer Prozess- und Verbindungsereignisse. Die neuen Prozess- und Verbindungsereignisse können auf einem Gerät innerhalb einer Sekunde vielfach auftreten. Diese Fähigkeit ist für eine umfassende Sicherheit wichtig. Die Anzahl der von den Sicherheits-Agents gesendeten Nachrichten kann jedoch schnell Ihre IoT Hub-Kontingente und Kostenlimits erreichen oder überschreiten. Diese Ereignisse enthalten in jedem Fall äußerst wertvolle Sicherheitsinformationen, die für den Schutz Ihres Geräts von entscheidender Bedeutung sind. 

Um das zusätzliche Kontingent und die Kosten zu reduzieren und Ihre Geräte gleichzeitig geschützt zu halten, aggregieren Defender für IoT-Agents diese Ereignistypen: 

- ProcessCreate (nur Linux) 

- ConnectionCreate für Netzwerke

- Systeminformationen

- Grundwert

Ereignisbasierte Collectors werden basierend auf der entsprechenden Aktivität innerhalb des Geräts ausgelöst. Beispielsweise wurde ein Prozess auf dem Gerät gestartet.  

Auf der Auslösung basierende Collectors werden auf der Basis von den Kundenkonfigurationen zeitgesteuert ausgelöst.

## <a name="how-does-event-aggregation-work"></a>Wie funktioniert die Ereignisaggregation? 

Defender für IoT-Agents aggregieren Ereignisse für den Intervallzeitraum oder das Zeitfenster. Nachdem der Intervallzeitraum abgelaufen ist, sendet der Agent die aggregierten Ereignisse zur weiteren Analyse an die Azure-Cloud. Die aggregierten Ereignisse werden im Arbeitsspeicher gespeichert, bis sie an die Azure-Cloud gesendet werden. 

Der Agent sammelt identische Ereignisse, die bereits im Arbeitsspeicher gespeichert sind. Diese Sammlung veranlasst den Agenten, die Anzahl der Treffer für dieses spezifische Ereignis zu erhöhen, um den Speicherbedarf des Agenten zu verringern. Wenn das Aggregationszeitfenster überschritten wird, sendet der Agent die Trefferanzahl für jeden Ereignistyp, der aufgetreten ist. Die Ereignisaggregation ist einfach die Aggregation der Trefferanzahl für die einzelnen erfassten Ereignistypen. 

## <a name="process-events-event-based"></a>Das Verarbeiten von Ereignissen (ereignisbasierte Ereignisse)

Prozessereignisse werden unter Linux-Betriebssystemen unterstützt. 

Prozessereignisse werden als identisch angesehen, wenn die *Befehlszeile* und  *userid* identisch sind. 

Der Standardpuffer für Prozessereignisse beträgt 256 Prozesse. Wenn dieser Grenzwert erreicht wird, wird der Puffer zyklisch durchlaufen. Außerdem wird das älteste Ereignis verworfen, um Platz für das neueste verarbeitete Ereignis zu schaffen. Es wird eine Warnung zur Erhöhung der Cachegröße protokolliert.

### <a name="data-collection"></a>Datensammlung

Die für jedes Ereignis gesammelten Daten:

- **Zeitstempel** – Das erste Mal, dass der Vorgang beobachtet wurde.

- **Vorgangs_ID** – Verwenden Sie die Linux PID.

- **Übergeordnete_Process_ID** – Verwenden Sie die übergeordnete PID für Linux, sofern vorhanden.

- **Befehlszeile** – Verwenden Sie die Befehlszeile. 

- **Typ** – Es kann sich entweder um `fork` oder `exec` handeln.

- **Anzahl_Treffer** – Ruft die aggregierte Anzahl ab. Die Anzahl der Ausführungen desselben Prozesses in demselben Zeitrahmen, bis die Ereignisse an die Cloud gesendet werden. 

## <a name="network-connection-events-event-based-collector"></a>Netzwerkverbindungsereignisse (ereignisbasierter Collector)

Die Netzwerkverbindungsereignisse werden als identisch angesehen, wenn der lokale Port, der Remote-Port, das Transportprotokoll, die lokale Adresse und die Remote-Adresse identisch sind.

Der Standardpuffer für die Netzwerkverbindungsereignisse beträgt 256. In Situationen, in denen der Cache voll ist: 

- **Azure RTOS-Geräte**: Es werden bis zum nächsten Sammlungszyklus keine neuen Netzwerkereignisse mehr zwischengespeichert.  

- **Linux-Geräte**: Das älteste Ereignis wird durch jedes neue Ereignis ersetzt. Es wird eine Warnung zur Erhöhung der Cachegröße protokolliert.

Für Linux-Geräte wird nur die Version IPv4 unterstützt.

### <a name="data-collection"></a>Datensammlung

Die für jedes Ereignis gesammelten Daten:

- **Lokale Adresse** – Die Quelladresse der Verbindung.

- **Remote Adresse** – Die Zieladresse der Verbindung.

- **Lokaler Port** – Der Quellport der Verbindung.

- **Remote Port** – Der Zielport der Verbindung.

- **Eingehende_Bytes** – Die gesamten aggregierten RX-Bytes der Verbindung.

- **Ausgehende_Bytes** – Die gesamten aggregierten TX-Bytes der Verbindung.

- **Transport_Protokol** – Das kann TCP, UDP, oder ICMP sein.

- **Anwendungsprotokoll** – Das Anwendungsprotokoll, das der Verbindung zugeordnet ist.

- **Erweiterte Eigenschaften** – Zusätzliche Details der Verbindung. Beispiel: `host name`. 

## <a name="baseline-trigger-based"></a>Grundwerte (basierend auf dem Auslöser) 

Der Grundwert-Collector führt in regelmäßigen Abständen CIS-Überprüfungen durch. Nur die fehlerhaften Ergebnisse werden an die Cloud gesendet. Die Cloud aggregiert die Ergebnisse und gibt Empfehlungen. 

### <a name="data-collection"></a>Datensammlung

Die für jedes Ereignis gesammelten Daten:

- **Überprüfungs-ID** – Im CIS Format, CIS-Debian-9-Dateisystem-1.1.2.

- **Überprüfungsergebnis** – Das kann `Error` oder `Fail` sein. Zum Beispiel, `Error` in einer Situation, in der die Prüfung nicht durchgeführt werden kann.

- **Fehler** – Die Informationen und die Beschreibung des Fehlers.

- **Beschreibung** – Die Beschreibung der Überprüfung von dem CIS.

- **Korrektur** – Die Empfehlung für die Korrektur von dem CIS.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Defender für IoT-Sicherheitswarnungen](concept-security-alerts.md).
