---
title: Micro-Agent-Ereigniserfassung (Vorschau)
description: Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 36cce317c4e53f7578dace7faba5e2fb567f42cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289976"
---
# <a name="micro-agent-event-collection-preview"></a>Micro-Agent-Ereigniserfassung (Vorschau)

Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud. Der Defender für IoT-Micro-Agent sammelt viele Arten von Geräteereignissen, einschließlich neuer Prozess- und Verbindungsereignisse. Die neuen Prozess- und Verbindungsereignisse können auf einem Gerät häufig eintreten. Diese Funktionalität ist wichtig für eine umfassende Sicherheit. Die Anzahl der von den Sicherheits-Agents gesendeten Nachrichten kann jedoch Ihre IoT Hub-Kontingente und -Kostenlimits schnell erreichen oder überschreiten. Diese Nachrichten und Ereignisse enthalten äußerst wertvolle Sicherheitsinformationen, die für den Schutz Ihres Geräts von entscheidender Bedeutung sind.

Defender für IoT-Agents aggregieren die folgenden Ereignistypen, um die Anzahl der Nachrichten und die Kosten bei gleichzeitiger Aufrechterhaltung der Sicherheit Ihres Geräts zu reduzieren:

- ProcessCreate (nur Linux)

- Anmeldeaktivität (nur Linux)

- ConnectionCreate für Netzwerke

- Systeminformationen

- Grundwert

Ereignisbasierte Sammler werden, basierend auf der entsprechenden Aktivität, innerhalb des Geräts ausgelöst. Beispiel: ``a process was started in the device``.  

Auf der Auslösung basierende Collectors werden auf der Basis von den Kundenkonfigurationen zeitgesteuert ausgelöst.

## <a name="how-does-event-aggregation-work"></a>Wie funktioniert die Ereignisaggregation?

Defender für IoT-Agents aggregieren Ereignisse für den Intervallzeitraum oder das Zeitfenster. Nachdem der Intervallzeitraum abgelaufen ist, sendet der Agent die aggregierten Ereignisse zur weiteren Analyse an die Azure-Cloud. Die aggregierten Ereignisse werden im Arbeitsspeicher gespeichert, bis sie an die Azure-Cloud gesendet werden.

Der Agent sammelt identische Ereignisse, die bereits im Arbeitsspeicher gespeichert sind. Diese Sammlung führt dazu, dass der Agent die Anzahl der Treffer für dieses spezifische Ereignis erhöht, um seinen Speicherbedarf zu reduzieren. Wenn das Aggregationszeitfenster überschritten wird, sendet der Agent die Trefferanzahl für jeden Ereignistyp, der aufgetreten ist. Die Ereignisaggregation ist einfach die Aggregation der Trefferanzahl für die einzelnen erfassten Ereignistypen.

## <a name="process-events-event-based"></a>Das Verarbeiten von Ereignissen (ereignisbasierte Ereignisse)

Prozessereignisse werden unter Linux-Betriebssystemen unterstützt.

Prozessereignisse werden als identisch angesehen, wenn die *Befehlszeile* und  *userid* identisch sind.

Der Standardpuffer für Prozessereignisse beträgt 256 Prozesse. Wenn dieser Grenzwert erreicht wird, wird der Puffer zyklisch durchlaufen. Außerdem wird das älteste Prozessereignis verworfen, um Platz für das neueste verarbeitete Ereignis zu schaffen. Es wird eine Warnung zur Erhöhung der Cachegröße protokolliert.

Die für jedes Ereignis gesammelten Daten lauten:

| Parameter | Beschreibung|
|--|--|
| **Timestamp** | Das erste Mal, dass der Vorgang beobachtet wurde. |
| **Prozess-ID** | Die Linux-PID. |
| **ID des übergeordneten Prozesses** | Die übergeordnete Linux-PID, sofern vorhanden. |
| **Befehlszeile** | Die Befehlszeile. |
| **Typ** | Kann entweder `fork` oder `exec` sein. |
| **Trefferanzahl** | Die aggregierte Anzahl. Die Anzahl der Ausführungen desselben Prozesses in demselben Zeitrahmen, bis die Ereignisse an die Cloud gesendet werden. |

## <a name="network-connection-events-event-based-collector"></a>Netzwerkverbindungsereignisse (ereignisbasierter Sammler)

Die Netzwerkverbindungsereignisse werden als identisch angesehen, wenn der lokale Port, der Remote-Port, das Transportprotokoll, die lokale Adresse und die Remote-Adresse identisch sind.

Der Standardpuffer für ein Netzwerkverbindungsereignis ist „256“. Für Situationen, in denen der Cache voll ist:

- **Azure RTOS-Geräte**: Bis zum Beginn des nächsten Sammlungszyklus werden keine neuen Netzwerkereignisse mehr zwischengespeichert.  

- **Linux-Geräte**: Das älteste Ereignis wird durch jedes neue Ereignis ersetzt. Es wird eine Warnung zur Erhöhung der Cachegröße protokolliert.

Bei Linux-Geräten wird nur „IPv4“ unterstützt.

Die für jedes Ereignis gesammelten Daten lauten:

| Parameter | BESCHREIBUNG|
|--|--|
| **Lokale Adresse** | Die Quelladresse der Verbindung. |
| **Remoteadresse** | Die Zieladresse der Verbindung. |
| **Lokaler Port** | Der Quellport der Verbindung. |
| **Remoteport** | Der Zielport der Verbindung. |
| **Eingehende Bytes** | Die gesamten aggregierten RX-Bytes der Verbindung. |
| **Ausgehende Bytes** | Die gesamten aggregierten TX-Bytes der Verbindung. |
| **Transportprotokoll** | Kann „TCP“, „UDP“ oder „ICMP“ sein. |
| **Anwendungsprotokoll** | Das der Verbindung zugeordnete Anwendungsprotokoll. |
| **Erweiterte Eigenschaften** | Die zusätzlichen Details der Verbindung. Beispiel: `host name`. |

## <a name="login-collector-event-based-collector"></a>Anmeldungssammler (ereignisbasierter Sammler)

Der Anmeldungssammler sammelt Benutzeranmeldungen, Abmeldungen und fehlgeschlagene Anmeldeversuche.

Zurzeit werden SSH und Telnet vollständig unterstützt.  

Die folgenden Daten werden gesammelt:

| Parameter | Beschreibung|
|--|--|
| **operation** | Die Werte „Login“, „Logout“ oder „LoginFailed“. |
| **Prozess-ID** | Die Linux-PID. |
| **user_name** | Der Linux-Benutzer. |
| **executable** | Das Terminalgerät. Beispiel: „tty1..6“ oder „pts/n“. |
| **remote_address** | Die Quelle der Verbindung – entweder Remote-IP im IPv6- oder IPv4-Format oder aber „127.0.0.1/0.0.0.0“ zur Angabe der lokalen Verbindung. |

> [!Note]
> Ein Anmeldeereignis wird erfasst, wenn ein Terminal auf einem Gerät geöffnet wird und bevor sich der Benutzer tatsächlich anmeldet. Bei diesem Ereignis gibt es einen TTY-Prozess, einen Anmeldeereignistyp und einen Benutzernamen. Beispiel: `LOGIN`.

## <a name="system-information-trigger-based-collector"></a>Systeminformationen (triggerbasierter Sammler)

Die für jedes Ereignis gesammelten Daten lauten:

| Parameter | BESCHREIBUNG|
|--|--|
| **hardware_vendor** | Der Name des Geräteherstellers. |
| **hardware_model** | Die Modellnummer des Geräts. |
| **os_dist** | Die Verteilung des Betriebssystems. Beispiel: `Linux`. |
| **os_version** | Die Version des Betriebssystems. Beispiel: `Windows 10` oder `Ubuntu 20.04.1`. |
| **os_platform** | Das Betriebssystem des Geräts |
| **os_arch** | Die Architektur des Betriebssystems. Beispiel: `x86_64`. |
| **nics** | Der Netzwerkschnittstellencontroller. Die vollständige Liste der Eigenschaften ist nachstehend aufgeführt. |

Die **nics**-Eigenschaften bestehen aus Folgendem:

| Parameter | BESCHREIBUNG|
|--|--|
|**type** | Einer der folgenden Werte: `UNKNOWN`, `ETH`, `WIFI`, `MOBILE` oder `SATELLITE`. |
| **vlans** | Das der Netzwerkschnittstelle zugeordnete virtuelle LAN. |
| **vendor** | Der Anbieter des Netzwerkcontrollers. |
| **info** | IPS und MACs, die dem Netzwerkcontroller zugeordnet sind. Dazu gehören die folgenden Felder: <br> - **ipv4_address**: Die IPv4-Adresse. <br> - **ipv6_address**: Die IPv6-Adresse. <br> - **mac**: Die MAC-Adresse.|

## <a name="baseline-trigger-based"></a>Grundwerte (basierend auf dem Auslöser)

Der Grundwert-Collector führt in regelmäßigen Abständen CIS-Überprüfungen durch. Nur die fehlerhaften Ergebnisse werden an die Cloud gesendet. Die Cloud aggregiert die Ergebnisse und gibt Empfehlungen.

### <a name="data-collection"></a>Datensammlung

Die für jedes Ereignis gesammelten Daten lauten:

| Parameter | BESCHREIBUNG|
|--|--|
| **Prüf-ID** | Im CIS-Format. Beispiel: `CIS-debian-9-Filesystem-1.1.2`. |
| **Ergebnis überprüfen** | Kann `Error` oder `Fail` sein. Zum Beispiel, `Error` in einer Situation, in der die Prüfung nicht durchgeführt werden kann. |
| **Fehler** | Fehler – Die Informationen zum Fehler und die entsprechende Beschreibung. |
| **Beschreibung** | Die Beschreibung der Überprüfung vom CIS. |
| **Wartung** | Die Empfehlung zur Wartung vom CIS. |
| **Schweregrad** | Der Schweregrad. |

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Defender für IoT-Sicherheitswarnungen](concept-security-alerts.md).
