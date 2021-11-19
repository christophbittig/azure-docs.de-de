---
title: Häufig gestellte Fragen zu Microsoft Defender für IoT für Geräteentwickler
description: Hier finden Sie Antworten auf die am häufigsten gestellten Fragen zum Microsoft Defender für IoT-Agent.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9075424368a6d3e8c2e17306a573f768cb4a0e45
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293476"
---
# <a name="microsoft-defender-for-iot-for-device-builders-frequently-asked-questions"></a>Häufig gestellte Fragen zu Microsoft Defender für IoT für Geräteentwickler

Dieser Artikel enthält eine Liste häufig gestellter Fragen (FAQ) zum Defender für IoT-Agent und die zugehörigen Antworten.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Muss ich einen eingebetteten Sicherheits-Agent installieren?

Die Installation des Agent auf Ihren IoT-Geräten ist nicht zwingend erforderlich, um Defender für IoT zu aktivieren. Sie können zwischen den beiden folgenden Optionen wählen. Es gibt vier verschiedene Stufen der Sicherheitsüberwachung sowie Verwaltungsfunktionen, die unterschiedlichen Schutz bieten:

- Installieren des eingebetteten Defender für IoT-Sicherheits-Agents mit oder ohne Änderungen. Diese Option bietet ein Höchstmaß an verbesserten Sicherheitsinformationen über das Geräteverhalten und den Zugriff.

- Keine Installation eines Sicherheits-Agent auf Ihren IoT-Geräten. Diese Option ermöglicht die Kommunikationsüberwachung der IoT Hub-Instanz mit reduzierter Sicherheitsüberwachung und Verwaltungsfunktionen.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Was leistet der Defender für IoT-Agent?

Der Defender für IoT-Agent bietet Bedrohungsschutz auf Geräteebene für Gerätekonfiguration, -verhalten und -zugriff (durch Scannen der Konfiguration), Prozess und Konnektivität. Der Defender für IoT-Sicherheits-Agent scannt keine geschäftsrelevanten Daten oder Aktivitäten.

Der Defender für IoT-Sicherheits-Agent ist Open Source und auf GitHub in 32-Bit- und 64-Bit-Versionen für Windows und Linux verfügbar: https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Wie lauten die Abhängigkeiten und Voraussetzungen für den Agent?

Defender für IoT unterstützt eine Vielzahl von Plattformen. Informationen zur Unterstützung für Ihre spezifischen Geräte finden Sie unter [Unterstützte Geräteplattformen](how-to-deploy-agent.md).

## <a name="which-data-is-collected-by-the-agent"></a>Welche Daten sammelt der Agent?

Der Agent sammelt Daten zu Konnektivität, Zugriff, Firewallkonfiguration, Prozessliste und BS-Baseline.

## <a name="how-much-data-will-the-agent-generate"></a>Wie viele Daten generiert der Agent?

Die Generierung von Daten durch den Agent wird durch Gerät, Anwendung, Konnektivitätsart und Konfiguration des Kunde-Agent gesteuert. Aufgrund der hohen Variabilität zwischen Geräten und IoT-Lösungen empfehlen wir, den Agent zunächst in einem Labor oder einer Testumgebung einzusetzen, um die spezifische Konfiguration zu beobachten, zu lernen und einzustellen, die Ihren Bedürfnissen entspricht, und gleichzeitig die Menge der generierten Daten zu messen. Nach dem Start des Dienstes gibt der Defender für IoT-Agent operative Empfehlungen zur Optimierung des Agent-Durchsatzes, um Sie bei der Konfiguration und Anpassung zu unterstützen.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Wird für Nachrichten des Agent Kontingent von IoT Hub verwendet?

Ja. Die vom Agent übertragenen Daten werden in Ihrem IoT Hub-Kontingent gezählt.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Nächste Schritte Ich habe einen Agent installiert, es werden aber keine Aktivitäten oder Protokolle angezeigt...

1. Überprüfen Sie, ob der [Typ des Agent mit der angegebenen Betriebssystemplattform Ihres Geräts übereinstimmt](how-to-deploy-agent.md).

1. Vergewissern Sie sich, dass der [Agent auf dem Gerät ausgeführt wird](how-to-agent-configuration.md).

1. Überprüfen Sie, ob der [Dienst erfolgreich](quickstart-onboard-iot-hub.md) für **Security** in Ihrem IoT Hub aktiviert wurde.

1. Überprüfen Sie, ob das Gerät im [IoT Hub mit dem Defender für IoT-Modul konfiguriert](quickstart-create-security-twin.md) ist.

Wenn die Aktivitäten oder Protokolle immer noch nicht verfügbar sind, wenden Sie sich an Ihren Defender für IoT-Partner, um zusätzliche Hilfe zu erhalten.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Was geschieht, wenn die Internetverbindung nicht mehr funktioniert?

Solange das Gerät betrieben wird, werden die Sensoren und Agents weiterhin ausgeführt und speichern Daten. Die Daten werden entsprechend der Größenkonfiguration im Cache für Sicherheitsmeldungen gespeichert. Wenn das Gerät wieder eine Verbindung herstellt, werden die Sicherheitsmeldungen wieder gesendet.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kann der Agent die Leistung des Geräts oder einer anderen installierten Software beeinträchtigen?

Der Agent verbraucht wie jede andere Anwendung/jeder andere Prozess Computerressourcen und sollte die normale Geräteaktivität nicht stören. Der Ressourcenverbrauch auf dem Gerät, auf dem der Agent ausgeführt wird, ist mit dessen Einrichtung und Konfiguration gekoppelt. Wir empfehlen, Ihre Agentkonfiguration in einer geschlossenen Umgebung zusammen mit der Interoperabilität mit Ihren anderen IoT-Anwendungen und -Funktionen zu testen, bevor Sie versuchen, sie in einer Produktionsumgebung einzusetzen.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Ich führe einige Wartungsmaßnahmen auf dem Gerät aus. Kann ich den Agent deaktivieren?

Der Agent kann nicht deaktiviert werden.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Gibt es eine Möglichkeit zum Testen, ob der Agent ordnungsgemäß funktioniert?

Wenn der Agent die Kommunikation einstellt oder keine Sicherheitsmeldungen sendet, wird eine Warnung **Gerät ist still** generiert.

## <a name="can-i-create-my-own-alerts"></a>Kann ich meine eigenen Warnungen erstellen?

Ja, Sie können benutzerdefinierte Warnungen erstellen, die auf mehreren Parametern, z. B. IP-/MAC-Adresse, Protokolltyp, Klasse, Dienst, Funktion, Befehl usw., sowie auf in den Nutzdaten enthaltenen Werten benutzerdefinierter Tags basieren. Weitere Informationen zu benutzerdefinierten Warnungen und deren Erstellung finden Sie unter [Erstellen benutzerdefinierter Warnungen](quickstart-create-custom-alerts.md).

## <a name="next-steps"></a>Nächste Schritte

Um mehr darüber zu erfahren, wie Sie mit Defender für IoT beginnen können, lesen Sie die folgenden Artikel:

- Lesen der [Übersicht](overview.md) über Defender für IoT
- Grundlegendes zu [Defender für IoT-Sicherheitswarnungen](concept-security-alerts.md)
