---
title: Warnungstypen und -beschreibungen
description: Lesen Sie die Beschreibungen der Warnungen zu Defender für IoT.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: ee5e3b32d0a716b9ce05ec2b7bb1ed7c39ddada6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283921"
---
# <a name="alert-types-and-descriptions"></a>Warnungstypen und -beschreibungen

Dieser Artikel enthält Informationen zu den Benachrichtigungstypen, Beschreibungen und dem Schweregrad, die von den Defender for IoT-Engines generiert werden können. Diese Informationen können verwendet werden, um Warnungen Playbooks zu zuordnen, Weiterleitungsregeln, Ausschlussregeln und benutzerdefinierte Warnungen zu definieren und die entsprechenden Regeln innerhalb des SIEM zu definieren. Warnungen werden im Fenster „Warnungen“ angezeigt. Dort können Sie das Warnungsereignis verwalten. 

## <a name="policy-engine-alerts"></a>Warnungen der Richtlinien-Engine

Die Warnungen der Richtlinien-Engine beschreiben erkannte Abweichungen vom gelernten normalen Verhalten.

>[!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

| Titel  | BESCHREIBUNG | severity |
|--|--|--|
| Ungewöhnliche Verwendung von MAC-Adressen | Ein neues Quellgerät wurde im Netzwerk erkannt, aber es wurde nicht autorisiert. | Nebenversion |
| Beckhoff-Software geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Datenbankanmeldung fehlgeschlagen | Es wurde ein fehlerhafter Anmeldeversuch über ein Quellgerät bei einem Zielserver erkannt. Dies ist möglicherweise auf einen menschlichen Fehler zurückzuführen, kann jedoch auch auf einen böswilligen Versuch hinweisen, den Server oder die darauf befindlichen Daten zu kompromittieren. | Hauptversion |
| Emerson ROC-Firmwareversion geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Kommunikation einer externen Adresse im Netzwerk mit dem Internet | Ein Quellgerät, das als Teil Ihres Netzwerks definiert ist, kommuniziert mit Internetadressen. Die Quelle ist nicht für die Kommunikation mit Internetadressen autorisiert. | Kritisch |
| Feldgerät unerwartet ermittelt | Ein neues Quellgerät wurde im Netzwerk erkannt, aber es wurde nicht autorisiert. | Hauptversion |
| Firmwareänderung erkannt | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Firmwareversion geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Nicht autorisierter Foxboro-E/A-Vorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Fehler bei FTP-Anmeldung | Es wurde ein fehlerhafter Anmeldeversuch über ein Quellgerät bei einem Zielserver erkannt. Dies ist möglicherweise auf einen menschlichen Fehler zurückzuführen, kann jedoch auch auf einen böswilligen Versuch hinweisen, den Server oder die darauf befindlichen Daten zu kompromittieren. | Hauptversion |
| Funktionscode hat nicht autorisierte Ausnahme ausgelöst | Ein Quellgerät (untergeordnet) hat eine Ausnahme an ein Zielgerät (übergeordnet) zurückgegeben. | Hauptversion |
| Einstellungen für GOOSE-Nachrichtentyp | Die Einstellungen einer Nachricht (identifiziert durch Protokoll-ID) wurden auf einem Quellgerät geändert. | Warnung |
| Honeywell-Firmwareversion geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Unzulässige HTTP-Kommunikation | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Internetzugriff erkannt | Ein Quellgerät, das als Teil Ihres Netzwerks definiert ist, kommuniziert mit Internetadressen. Die Quelle ist nicht für die Kommunikation mit Internetadressen autorisiert. | Hauptversion |
| Mitsubishi-Firmwareversion geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Verstoß gegen Modbus-Adressbereich | Ein Mastergerät hat Zugriff auf eine neue Speicheradresse auf dem untergeordneten Gerät angefordert. | Hauptversion |
| Modbus-Firmwareversion geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Neue Aktivität erkannt: CIP-Klasse | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: CIP-Klassendienst | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: CIP-PCCC-Befehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: CIP-Symbol | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Ethernet/IP-E/A-Verbindung | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Ethernet/IP-Protokollbefehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: GSM-Nachrichtencode | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: LonTalk-Befehlscodes | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neuer Port erkannt | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Warnung |
| Neue Aktivität erkannt: LonTalk-Netzwerkvariable | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Ovation-Datenanforderung | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Befehl zum Lesen/Schreiben (AMS-Indexgruppe) | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Befehl zum Lesen/Schreiben (AMS-Indexoffset) | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Nicht autorisierter DeltaV-Nachrichtentyp | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Nicht autorisierter DeltaV-ROC-Vorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Nicht autorisierter RPC-Nachrichtentyp | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Nicht autorisierter RPC | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Verwendung des AMS-Protokollbefehls | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Verwendung des Siemens SICAM-Befehls | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Verwendung des Suitelink-Protokollbefehls | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Verwendung von Suitelink-Protokollsitzungen | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Aktivität erkannt: Verwendung des Yokogawa-VNetIP-Befehls | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Neue Ressource erkannt | Ein neues Quellgerät wurde im Netzwerk erkannt, aber es wurde nicht autorisiert. | Hauptversion |
| Neue LLDP-Gerätekonfiguration | Ein neues Quellgerät wurde im Netzwerk erkannt, aber es wurde nicht autorisiert. | Hauptversion |
| Nicht autorisierter Omron-FINS-Befehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| S7 Plus SPS-Firmware geändert | Die Firmware wurde auf einem Quellgerät aktualisiert. Dies kann eine autorisierte Aktivität sein, z. B. eine geplante Wartungsprozedur. | Hauptversion |
| Einstellungen für Nachrichtentyp mit Stichprobenwerten | Die Einstellungen einer Nachricht (identifiziert durch Protokoll-ID) wurden auf einem Quellgerät geändert. | Warnung |
| Verdacht auf ungültige Integritätsüberprüfung | Es wurde eine Überprüfung auf einem DNP3-Quellgerät (Outstation) erkannt. Diese Überprüfung war nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter Befehl für Verknüpfung von Toshiba-Computer | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Nebenversion |
| Nicht autorisierter ABB Totalflow-Dateivorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter ABB Totalflow-Registrierungsvorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Zugriff auf Siemens S7-Datenblock | Ein Quellgerät hat versucht, auf eine Ressource auf einem anderen Gerät zuzugreifen. Ein Zugriffsversuch auf diese Ressource zwischen diesen beiden Geräten wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Warnung |
| Nicht autorisierter Zugriff auf Siemens S7 Plus-Objekt | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Zugriff auf ein Wonderware-Tag | Ein Quellgerät hat versucht, auf eine Ressource auf einem anderen Gerät zuzugreifen. Ein Zugriffsversuch auf diese Ressource zwischen diesen beiden Geräten wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter BACNet-Objektzugriff | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte BACNet-Route | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte Datenbankanmeldung | Es wurde ein Anmeldeversuch zwischen einem Quellclient und einem Zielserver erkannt. Die Kommunikation zwischen diesen Geräten wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter Datenbankvorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Emerson ROC-Vorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter GE SRTP-Dateizugriff | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter GE SRTP-Protokollbefehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter GE SRTP-Systemspeichervorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte HTTP-Aktivität | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter HTTP-Server | Auf einem Quellgerät wurde eine nicht autorisierte Anwendung erkannt. Die Anwendung wurde nicht als gelernte Anwendung in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierte HTTP-SOAP-Aktion | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter HTTP-Benutzer-Agent | Auf einem Quellgerät wurde eine nicht autorisierte Anwendung erkannt. Die Anwendung wurde nicht als gelernte Anwendung in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierte Internetverbindung erkannt | Ein Quellgerät, das als Teil Ihres Netzwerks definiert ist, kommuniziert mit Internetadressen. Die Quelle ist nicht für die Kommunikation mit Internetadressen autorisiert. | Kritisch |
| Nicht autorisierter Mitsubishi MELSEC-Befehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Zugriff auf MMS-Programm | Ein Quellgerät hat versucht, auf eine Ressource auf einem anderen Gerät zuzugreifen. Ein Zugriffsversuch auf diese Ressource zwischen diesen beiden Geräten wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter MMS-Dienst | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte Multicast-/Übertragungsverbindung | Zwischen einem Quellgerät und anderen Geräten wurde eine Multicast-/Übertragungsverbindung erkannt. Die Multicast-/Übertragungskommunikation ist nicht autorisiert. | Kritisch |
| Nicht autorisierte Namensabfrage | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte OPC UA-Aktivität | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte OPC UA-Anforderung/-Antwort | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Vorgang durch benutzerdefinierte Regel erkannt | Datenverkehr zwischen zwei Geräten wurde erkannt. Diese Aktivität ist aufgrund einer benutzerdefinierten Warnungsregel nicht autorisiert. | Hauptversion |
| Nicht autorisierter Lesevorgang für SPS-Konfiguration | Das Quellgerät ist nicht als Programmiergerät definiert, sondern hat einen Lese-/Schreibvorgang auf einem Zielcontroller ausgeführt. Programmieränderungen sollten nur von Programmiergeräten durchgeführt werden. Möglicherweise wurde eine Programmieranwendung auf diesem Gerät installiert. | Warnung |
| Nicht autorisierter Schreibvorgang für SPS-Konfiguration | Das Quellgerät hat einen Befehl zum Lesen/Schreiben des Programms eines Zielcontrollers gesendet. Diese Aktivität wurde zuvor nicht beobachtet. | Hauptversion |
| Nicht autorisierter Upload von SPS-Programm | Das Quellgerät hat einen Befehl zum Lesen/Schreiben des Programms eines Zielcontrollers gesendet. Diese Aktivität wurde zuvor nicht beobachtet. | Hauptversion |
| Nicht autorisierte SPS-Programmierung | Das Quellgerät ist nicht als Programmiergerät definiert, sondern hat einen Lese-/Schreibvorgang auf einem Zielcontroller ausgeführt. Programmieränderungen sollten nur von Programmiergeräten durchgeführt werden. Möglicherweise wurde eine Programmieranwendung auf diesem Gerät installiert. | Kritisch |
| Nicht autorisierter Profinet-Frametyp | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter SAIA S-Bus-Befehl | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte Ausführung der Steuerungsfunktion von Siemens S7 | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte Siemens S7-Ausführung von benutzerdefinierter Funktion | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Siemens S7 Plus-Blockzugriff | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Siemens S7 Plus-Vorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierte SMB-Anmeldung | Es wurde ein Anmeldeversuch zwischen einem Quellclient und einem Zielserver erkannt. Die Kommunikation zwischen diesen Geräten wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter SNMP-Vorgang | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter SSH-Zugriff | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht autorisierter Windows-Prozess | Auf einem Quellgerät wurde eine nicht autorisierte Anwendung erkannt. Die Anwendung wurde nicht als gelernte Anwendung in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter Windows-Dienst | Auf einem Quellgerät wurde eine nicht autorisierte Anwendung erkannt. Die Anwendung wurde nicht als gelernte Anwendung in Ihrem Netzwerk autorisiert. | Hauptversion |
| Nicht autorisierter Vorgang durch benutzerdefinierte Regel erkannt | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination verstößt gegen eine benutzerdefinierte Regel. | Hauptversion |
| Nicht zugelassene Modbus Schneider Electric-Erweiterung | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Nicht zugelassene Verwendung von ASDU-Typen | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Unzulässige Verwendung von DNP3-Funktionscode | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |
| Unzulässige Verwendung von internem Hinweis | Ein DNP3-Quellgerät (Outstation) hat einen internen Hinweis gemeldet, der nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert wurde. | Hauptversion |
| Unzulässige Verwendung von Modbus-Funktionscode | Es wurden neue Datenverkehrsparameter erkannt. Diese Parameterkombination wurde nicht als erlernter Datenverkehr in Ihrem Netzwerk autorisiert. Die folgende Kombination ist nicht autorisiert. | Hauptversion |

## <a name="anomaly-engine-alerts"></a>Engine-Warnungen zu Anomalien

Warnungen der Anomalie-Engine beschreiben erkannte Anomalien in der Netzwerkaktivität.

| Titel | BESCHREIBUNG | severity |
|--|--|--|
| Ungewöhnliches Ausnahmemuster auf untergeordnetem Gerät | Auf einem Quellgerät wurde eine übermäßige Anzahl von Fehlern erkannt. Dies kann durch ein Betriebsproblem verursacht worden sein. | Nebenversion |
| Ungewöhnliche Länge des HTTP-Headers | Das Quellgerät hat eine ungewöhnliche Nachricht gesendet. Dies kann auf einen Angriffsversuch auf das Zielgerät hindeuten. | Kritisch |
| Ungewöhnliche Anzahl von Parametern im HTTP-Header | Das Quellgerät hat eine ungewöhnliche Nachricht gesendet. Dies kann auf einen Angriffsversuch auf das Zielgerät hindeuten. | Kritisch |
| Ungewöhnliches regelmäßiges Verhalten im Kommunikationskanal | Eine Änderung der Kommunikationshäufigkeit zwischen dem Quell- und dem Zielgerät wurde erkannt. | Nebenversion |
| Ungewöhnliche Beendung von Anwendungen | Auf einem Quellgerät wurde eine übermäßige Anzahl von Stoppbefehlen erkannt. Dies kann auf ein Betriebsproblem oder den Versuch zurückzuführen sein, das Gerät zu manipulieren. | Hauptversion |
| Ungewöhnliche Datenverkehrsbandbreite | Auf einem Kanal wurde eine ungewöhnliche Bandbreite erkannt. Die Bandbreite scheint erheblich niedriger oder höher als zuvor erkannt zu sein. Weitere Informationen erhalten Sie, wenn Sie mit dem Widget für die Gesamtbandbreite arbeiten. | Warnung |
| Ungewöhnliche Datenverkehrsbandbreite zwischen Geräten | Auf einem Kanal wurde eine ungewöhnliche Bandbreite erkannt. Die Bandbreite scheint erheblich niedriger oder höher als zuvor erkannt zu sein. Weitere Informationen erhalten Sie, wenn Sie mit dem Widget für die Gesamtbandbreite arbeiten. | Warnung |
| Adressüberprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte überprüft. Dieses Gerät wurde nicht für die Netzwerküberprüfung autorisiert. | Kritisch |
| ARP-Adressenüberprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte mithilfe des Address Resolution-Protokolls (ARP) überprüft. Diese Geräteadresse wurde nicht als gültige Adresse für die ARP-Überprüfung autorisiert. | Kritisch |
| ARP-Adressenüberprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte mithilfe des Address Resolution-Protokolls (ARP) überprüft. Diese Geräteadresse wurde nicht als gültige Adresse für die ARP-Überprüfung autorisiert. | Kritisch |
| ARP-Spoofing | Im Netzwerk wurde eine ungewöhnliche Menge von Paketen erkannt. Dies kann auf einen Angriff hindeuten, z. B. auf ARP-Spoofing oder einen ICMP-Floodingangriff. | Warnung |
| Übermäßige Anzahl von Anmeldeversuchen | Ein Quellgerät hat eine übermäßige Anzahl von Anmeldeversuchen auf einem Zielserver erkannt. Es kann sich hierbei um einen Brute-Force-Angriff handeln. Der Server wurde möglicherweise von einem böswilligen Akteur kompromittiert. | Kritisch |
| Übermäßige Anzahl von Sitzungen | Ein Quellgerät hat eine übermäßige Anzahl von Anmeldeversuchen auf einem Zielserver erkannt. Es kann sich hierbei um einen Brute-Force-Angriff handeln. Der Server wurde möglicherweise von einem böswilligen Akteur kompromittiert. | Kritisch |
| Übermäßige Neustartrate einer Outstation | Auf einem Quellgerät wurde eine übermäßige Anzahl von Neustartbefehlen erkannt. Dies kann auf ein Betriebsproblem oder den Versuch zurückzuführen sein, das Gerät zu manipulieren. | Hauptversion |
| Übermäßige Anzahl von SMB-Anmeldeversuchen | Ein Quellgerät hat eine übermäßige Anzahl von Anmeldeversuchen auf einem Zielserver erkannt. Es kann sich hierbei um einen Brute-Force-Angriff handeln. Der Server wurde möglicherweise von einem böswilligen Akteur kompromittiert. | Kritisch |
| ICMP-Flooding | Im Netzwerk wurde eine ungewöhnliche Menge von Paketen erkannt. Dies kann auf einen Angriff hindeuten, z. B. auf ARP-Spoofing oder einen ICMP-Floodingangriff. | Warnung |
| Unzulässiger Inhalt von HTTP-Header | Das Quellgerät hat eine ungültige Anforderung initiiert. | Kritisch |
| Inaktiver Kommunikationskanal | Ein Kommunikationskanal zwischen zwei Geräten war während eines Zeitraums inaktiv, in dem die Aktivität normalerweise beobachtet wird. Dies deutet möglicherweise darauf hin, dass das Programm, das diesen Datenverkehr erzeugt, geändert wurde oder nicht verfügbar ist. Es wird empfohlen, die ordnungsgemäße Konfiguration des installierten Programms zu überprüfen. | Warnung |
| Zeitintensive Adressüberprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte überprüft. Dieses Gerät wurde nicht für die Netzwerküberprüfung autorisiert. | Kritisch |
| Versuch zum Erraten des Kennworts erkannt | Ein Quellgerät hat eine übermäßige Anzahl von Anmeldeversuchen auf einem Zielserver erkannt. Es kann sich hierbei um einen Brute-Force-Angriff handeln. Der Server wurde möglicherweise von einem böswilligen Akteur kompromittiert. | Kritisch |
| SPS-Überprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte überprüft. Dieses Gerät wurde nicht für die Netzwerküberprüfung autorisiert. | Kritisch |
| Portüberprüfung erkannt | Es wurde erkannt, dass ein Quellgerät Netzwerkgeräte überprüft. Dieses Gerät wurde nicht für die Netzwerküberprüfung autorisiert. | Kritisch |
| Unerwartete Nachrichtenlänge | Das Quellgerät hat eine ungewöhnliche Nachricht gesendet. Dies kann auf einen Angriffsversuch auf das Zielgerät hindeuten. | Kritisch |
| Unerwarteter Datenverkehr für Standardport | Auf einem Gerät wurde Datenverkehr über einen Port erkannt, der für ein anderes Protokoll reserviert ist. | Hauptversion |

## <a name="protocol-violation-engine-alerts"></a>Warnungen der Protokollverstoß-Engine

Warnungen der Protokoll-Engine beschreiben erkannte Abweichungen in der Paketstruktur oder in Feldwerten im Vergleich zu Protokollspezifikationen.

| Titel | BESCHREIBUNG | severity |
|--|--|--|
| Übermäßige Anzahl falsch formatierter Pakete in einer einzigen Sitzung | Eine ungewöhnliche Anzahl falsch formatierter Pakete, die vom Quellgerät an das Zielgerät gesendet werden. Dies kann auf eine fehlerhafte Kommunikation oder einen Versuch hinweisen, das Zielgerät zu manipulieren. | Hauptversion |
| Firmwareupdate | Ein Quellgerät hat einen Befehl zum Aktualisieren der Firmware auf einem Zielgerät gesendet. Überprüfen Sie, ob die aktuellen, auf dem Zielgerät durchgeführten Programmierungs-, Konfigurations- und Firmwareupgrades gültig sind. | Warnung |
| Funktionscode von Outstation nicht unterstützt | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässige BACNet-Nachricht | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Unzulässiger Verbindungsversuch an Port 0 | Ein Quellgerät hat versucht, über die Portnummer 0 eine Verbindung mit dem Zielgerät herzustellen. Für das TCP ist Port 0 reserviert und kann nicht verwendet werden. Für UDP ist der Port optional, und der Wert 0 bedeutet, dass es keinen Port gibt. In der Regel gibt es auf Systemen keinen Dienst, der Port 0 überwacht. Dieses Ereignis weist möglicherweise auf einen Angriff auf das Zielgerät oder darauf hin, dass eine Anwendung falsch programmiert wurde. | Nebenversion |
| Unzulässiger DNP3-Vorgang | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Unzulässiger Modbus-Vorgang (Ausnahme durch Master ausgelöst) | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Unzulässiger Modbus-Vorgang (Funktionscode null) | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Unzulässige Protokollversion | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Falscher Parameter an Outstation gesendet | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Initiierung eines veralteten Funktionscodes (Daten initialisieren) | Das Quellgerät hat eine ungültige Anforderung initiiert. | Nebenversion |
| Initiierung eines veralteten Funktionscodes (Konfiguration speichern) | Das Quellgerät hat eine ungültige Anforderung initiiert. | Nebenversion |
| Das übergeordnete Gerät hat die Bestätigung einer Anwendungsebene angefordert. | Das Quellgerät hat eine ungültige Anforderung initiiert. | Warnung |
| Modbus-Ausnahme | Ein Quellgerät (untergeordnet) hat eine Ausnahme an ein Zielgerät (übergeordnet) zurückgegeben. | Hauptversion |
| Unzulässiger ASDU-Typ von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässige Befehlsursache für die Übertragung von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässige allgemeine Adresse von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässiger Datenadressparameter von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässiger Datenwertparameter von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässiger Funktionscode von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unzulässige Informationsobjektadresse von untergeordnetem Gerät empfangen | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Unbekanntes Objekt an Outstation gesendet | Das Zielgerät hat eine ungültige Anforderung empfangen. | Hauptversion |
| Verwendung eines reservierten Funktionscodes | Das Quellgerät hat eine ungültige Anforderung initiiert. | Hauptversion |
| Verwendung einer unsachgemäßen Formatierung durch Outstation | Das Quellgerät hat eine ungültige Anforderung initiiert. | Warnung |
| Verwendung reservierter Statusflags (IIN) | Ein DNP3-Quellgerät (Outstation) hat den reservierten internen Indikator 2.6 verwendet. Es wird empfohlen, die Konfiguration des Geräts zu überprüfen. | Warnung |

## <a name="malware-engine-alerts"></a>Warnungen der Malware-Engine

Warnungen der Schadsoftware-Engine beschreiben erkannte schädliche Netzwerkaktivitäten.

| Titel | BESCHREIBUNG| severity |
|--|--|--|
| Verbindungsversuch mit bekannter böswilliger IP-Adresse | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Ungültige SMB-Nachricht (Hintertürinstallation von DoublePulsar) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Schädliche Domänennamenanforderung | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Malwaretestdatei erkannt: EICAR AV erfolgreich | Eine EICAR-AV-Testdatei wurde im Datenverkehr zwischen zwei Geräten erkannt. Es handelt sich bei der Datei nicht um Malware. Sie wird verwendet, um sicherzustellen, dass die Antivirussoftware ordnungsgemäß installiert wurde, um zu veranschaulichen, was geschieht, wenn ein Virus gefunden wird, und um interne Prozeduren für und Reaktionen auf einen Virusfund zu überprüfen. Antivirussoftware sollte EICAR wie einen echten Virus erkennen. | Hauptversion |
| Verdacht auf Conficker-Malware | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdacht auf Denial-of-Service-Angriff | Ein Quellgerät hat versucht, eine übermäßige Anzahl neuer Verbindungen mit einem Zielgerät zu initiieren. Hierbei kann es sich um einen Denial-of-Service-Angriff (DOS) auf das Zielgerät handeln, der die Funktionalität des Geräts, die Leistung und die Dienstverfügbarkeit beeinträchtigen oder nicht behebbare Fehler verursachen kann. | Kritisch |
| Verdacht auf schädliche Aktivität | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdacht auf schädliche Aktivität (BlackEnergy) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (DarkComet) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Duqu) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Flame) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Havex) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädlich Aktivität (Karagany) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (LightsOut) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Namensabfragen) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdacht auf schädliche Aktivität (Poison Ivy) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Regin) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (Stuxnet) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf schädliche Aktivität (WannaCry) | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdacht auf NotPetya-Malware: Unzulässige SMB-Parameter erkannt | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf NotPetya-Malware: Unzulässige SMB-Transaktion erkannt | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |
| Verdacht auf Remotecodeausführung mit PsExec | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdacht auf Remoteverwaltung von Windows-Diensten | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdächtige ausführbare Datei am Endpunkt erkannt | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Hauptversion |
| Verdächtiger Datenverkehr erkannt | Es wurde eine verdächtige Netzwerkaktivität erkannt. Diese Aktivität kann einem Angriff zugeordnet werden, bei dem eine Methode verwendet wird, für die eine bestimmte Malware bekannt ist. | Kritisch |

## <a name="operational-engine-alerts"></a>Warnungen der Betriebs-Engine

Warnungen der Betriebs-Engine beschreiben erkannte Betriebsvorfälle oder Entitäten mit Funktionsfehlern.

| Titel | BESCHREIBUNG | severity |
|--|--|--|
| S7-Befehl zum Beenden der SPS gesendet | Das Quellgerät hat einen Stoppbefehl an einen Zielcontroller gesendet. Der Controller wird erst beendet, wenn ein Startbefehl gesendet wird. | Warnung |
| BACNet-Vorgang fehlgeschlagen | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Fehlerhafter MMS-Gerätestatus | Von einem MMS-VMD (Virtual Manufacturing Device) wurde eine Statusmeldung gesendet. Die Meldung weist darauf hin, dass der Server möglicherweise nicht ordnungsgemäß konfiguriert, teilweise betriebsbereit oder nicht betriebsbereit ist. | Hauptversion |
| Gerätekonfigurationsänderung | Auf einem Quellgerät wurde eine Konfigurationsänderung erkannt. | Nebenversion |
| Ständiger Ereignispufferüberlauf in Outstation | Ein Pufferüberlaufereignis wurde auf einem Quellgerät erkannt. Das Ereignis kann zu Datenbeschädigung, Programmabstürzen oder der Ausführung von schädlichem Code führen. | Hauptversion |
| Controllerzurücksetzung | Ein Quellgerät hat einen Befehl zum Zurücksetzen an einen Zielcontroller gesendet. Der Controller wurde beendet und automatisch neu gestartet. | Warnung |
| Controllerbeendung | Das Quellgerät hat einen Stoppbefehl an einen Zielcontroller gesendet. Der Controller wird erst beendet, wenn ein Startbefehl gesendet wird. | Warnung |
| Gerät konnte keine dynamische IP-Adresse empfangen | Das Quellgerät ist so konfiguriert, dass es eine dynamische IP-Adresse von einem DHCP-Server empfängt, aber keine Adresse empfangen hat. Dies weist auf einen Konfigurationsfehler auf dem Gerät oder auf einen Betriebsfehler auf dem DHCP-Server hin. Es wird empfohlen, den Netzwerkadministrator über den Incident zu benachrichtigen. | Hauptversion |
| Verbindung des Geräts möglicherweise getrennt (keine Reaktion) | Ein Quellgerät hat nicht auf einen an dieses gesendeten Befehl geantwortet. Möglicherweise wurde die Verbindung getrennt, als der Befehl gesendet wurde. | Hauptversion |
| Fehler bei Ethernet/IP-CIP-Dienstanforderung | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Fehler bei Befehl von Ethernet/IP-Kapselungsprotokoll | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Ereignispufferüberlauf in Outstation | Ein Pufferüberlaufereignis wurde auf einem Quellgerät erkannt. Das Ereignis kann zu Datenbeschädigung, Programmabstürzen oder der Ausführung von schädlichem Code führen. | Hauptversion |
| Erwarteter Sicherungsvorgang fand nicht statt | Die erwartete Sicherungs-/Dateiübertragungsaktivität zwischen zwei Geräten hat nicht stattgefunden. Dies weist auf mögliche Fehler im Sicherungs-/Dateiübertragungsprozess hin. | Hauptversion |
| Fehler bei GE SRTP-Befehl | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| GE SRTP-Befehl zum Beenden der SPS gesendet | Das Quellgerät hat einen Stoppbefehl an einen Zielcontroller gesendet. Der Controller wird erst beendet, wenn ein Startbefehl gesendet wird. | Warnung |
| Weitere Konfiguration für GOOSE-Kontrollblock erforderlich | Ein Quellgerät hat eine GOOSE-Meldung gesendet, in der darauf hingewiesen wird, dass das Gerät in Betrieb genommen werden muss. Das bedeutet, dass der GOOSE-Kontrollblock weitere Konfigurationen erfordert und die GOOSE-Meldung teilweise oder überhaupt nicht funktionstüchtig ist. | Hauptversion |
| GOOSE-Datasetkonfiguration geändert | Ein Nachrichtendataset (identifiziert durch die Protokoll-ID) wurde auf einem Quellgerät geändert. Das bedeutet, dass das Gerät ein anderes Dataset für diese Nachricht meldet. | Warnung |
| Unerwarteter Status von Honeywell-Controller | Ein Honeywell-Controller hat eine unerwartete Diagnosemeldung gesendet, die auf eine Statusänderung hinweist. | Warnung |
| HTTP-Clientfehler | Das Quellgerät hat eine ungültige Anforderung initiiert. | Warnung |
| Unzulässige IP-Adresse | Vom System wurde Datenverkehr zwischen einem Quellgerät und einer IP-Adresse erkannt, die eine ungültige Adresse darstellt. Dies weist möglicherweise auf eine falsche Konfiguration oder den Versuch hin, unzulässigen Datenverkehr zu generieren | Nebenversion |
| Authentifizierungsfehler zwischen übergeordnetem und untergeordnetem Gerät | Fehler beim Authentifizierungsprozess zwischen einem DNP3-Quellgerät (übergeordnet) und einem Zielgerät (Outstation). | Nebenversion |
| Fehler bei MMS-Dienstanforderung | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Kein Datenverkehr an Sensorschnittstelle erkannt | Ein Sensor hat die Erkennung von Netzwerkdatenverkehr an einer Netzwerkschnittstelle beendet. | Kritisch |
| OPC UA-Server hat ein Ereignis ausgelöst, das einen Benutzereingriff erfordert | Ein OPC UA-Server hat eine Ereignisbenachrichtigung an einen Client gesendet. Dieser Ereignistyp erfordert einen Benutzereingriff. | Hauptversion |
| Fehler bei OPC UA-Dienstanforderung | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Outstation neu gestartet | Auf einem Quellgerät wurde ein kalter Neustart erkannt. Das bedeutet, dass das Gerät physisch aus- und wieder eingeschaltet wurde. | Warnung |
| Häufige Neustarts der Outstation | Auf einem Quellgerät wurde eine übermäßige Anzahl von kalten Neustarts erkannt. Das bedeutet, dass das Gerät übermäßig häufig physisch aus- und wieder eingeschaltet wurde. | Nebenversion |
| Konfiguration der Outstation geändert | Auf einem Quellgerät wurde eine Konfigurationsänderung erkannt. | Hauptversion |
| Beschädigte Konfiguration der Outstation erkannt | Dieses DNP3-Quellgerät (Outstation) hat eine beschädigte Konfiguration gemeldet. | Hauptversion |
| Fehler bei Profinet-DCP-Befehl | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Zurücksetzung von Profinet-Gerät auf Werkseinstellungen | Ein Quellgerät hat einen Befehl zum Zurücksetzen auf die Werkseinstellungen an ein Profinet-Zielgerät gesendet. Mit dem Befehl zum Zurücksetzen werden Gerätekonfigurationen von Profinet gelöscht, und der Vorgang wird beendet. | Warnung |
| Fehler bei RPC-Vorgang | Ein Server hat einen Fehlercode zurückgegeben. Dies weist auf einen Serverfehler oder eine ungültige Anforderung von einem Client hin. | Hauptversion |
| Konfiguration für Nachrichtendataset mit Stichprobenwerten geändert | Ein Nachrichtendataset (identifiziert durch die Protokoll-ID) wurde auf einem Quellgerät geändert. Das bedeutet, dass das Gerät ein anderes Dataset für diese Nachricht meldet. | Warnung |
| Nicht behebbarer Fehler auf untergeordnetem Gerät | Ein nicht behebbarer Bedingungsfehler wurde auf einem Quellgerät erkannt. Diese Art von Fehler deutet in der Regel auf einen Hardwarefehler oder einen Fehler bei der Ausführung eines bestimmten Befehls hin. | Hauptversion |
| Verdacht auf Hardwareprobleme an der Outstation | Ein nicht behebbarer Bedingungsfehler wurde auf einem Quellgerät erkannt. Diese Art von Fehler deutet in der Regel auf einen Hardwarefehler oder einen Fehler bei der Ausführung eines bestimmten Befehls hin. | Hauptversion |
| Verdacht auf nicht reagierendes Modbus-Gerät | Ein Quellgerät hat nicht auf einen an dieses gesendeten Befehl geantwortet. Möglicherweise wurde die Verbindung getrennt, als der Befehl gesendet wurde. | Nebenversion |
| Datenverkehr an Sensorschnittstelle erkannt | Ein Sensor hat die Erkennung von Netzwerkdatenverkehr an einer Netzwerkschnittstelle fortgesetzt. | Warnung |

## <a name="next-steps"></a>Nächste Schritte

Sie können [Warnungsereignisse verwalten](how-to-manage-the-alert-event.md).
Erfahren Sie, wie Sie [Warnungsinformationen weiterleiten](how-to-forward-alert-information-to-partners.md).
