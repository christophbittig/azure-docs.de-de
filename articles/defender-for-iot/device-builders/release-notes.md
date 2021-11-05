---
title: Neuerungen bei Azure Defender für IoT für Gerätehersteller
description: Erfahren Sie mehr über die neuesten Releases und Funktionen von Defender für IoT für Gerätehersteller.
ms.topic: overview
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c420fddad0332b2a83907dd564bf977c31ea6d4d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086282"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>Neuerungen bei Azure Defender für IoT für Gerätehersteller  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

In diesem Artikel sind neue und verbesserte Features von Defender für IoT aufgeführt.

Die genannten Features befinden sich in der Vorschauphase. Die [ergänzenden Bestimmungen für Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Versionsverwaltung und Unterstützung für Azure Defender für IoT

Nachfolgend sind die Unterstützung, die Breaking Change-Richtlinien für Defender für IoT sowie die derzeit verfügbaren Versionen von Azure Defender für IoT aufgeführt.

### <a name="servicing-information-and-timelines"></a>Wartungsinformationen und Zeitachsen

Microsoft plant, mindestens vierteljährlich Updates für Azure Defender für IoT zu veröffentlichen. Jede allgemein verfügbare Version des Sensors mit Azure Defender für IoT und der lokalen Verwaltungskonsole wird nach der Veröffentlichung bis zu neun Monate lang unterstützt. Korrekturen und neue Funktionen werden auf die aktuelle allgemein verfügbare Version angewendet, die derzeit unterstützt wird. Sie werden nicht auf ältere allgemein verfügbare Versionen angewendet.

### <a name="versions-and-support-dates"></a>Versionen und Supportdaten

| Version | Veröffentlichungsdatum | Ablauf des Supports |
|--|--|--|
| 10.0 | 01/2021 | 10/2021 |
| 10.3 | 04/2021 | 02/2022 |
| 10.5.2 | 11/2021 | 07/2022 |

## <a name="april-2021"></a>April 2021

### <a name="work-with-automatic-threat-intelligence-updates-public-preview"></a>Verwenden automatischer Threat Intelligence-Updates (Public Preview)

Neue Threat Intelligence-Pakete können jetzt automatisch an mit der Cloud verbundene Sensoren gepusht werden, sobald sie von Microsoft Defender für IoT veröffentlicht werden. Diese Option steht zusätzlich zum Herunterladen von Threat Intelligence-Paketen und zum anschließenden Hochladen auf Sensoren zur Verfügung.

Durch die Verwendung automatischer Updates lässt sich der Betriebsaufwand reduzieren und eine höhere Sicherheit gewährleisten.

Aktivieren Sie die automatische Aktualisierung, indem Sie Ihren mit der Cloud verbundenen Sensor im Portal von Defender für IoT integrieren. Aktivieren Sie dazu die Umschaltfläche **Automatic Threat Intelligence Updates** (Automatische Threat Intelligence-Updates).

Wenn Sie einen konservativeren Ansatz zum Aktualisieren Ihrer Threat Intelligence-Daten verfolgen möchten, können Sie Pakete nur dann manuell aus dem Portal von Azure Defender für IoT an mit der Cloud verbundene Sensoren pushen, wenn Sie es für erforderlich halten.
Dadurch können Sie steuern, wann ein Paket installiert wird, ohne es herunterladen und dann auf Ihre Sensoren hochladen zu müssen. Updates werden in Defender für IoT über die Seite **Standorte und Sensoren** manuell an Sensoren gepusht.

Sie können auch die folgenden Informationen zu Threat Intelligence-Paketen überprüfen:

- Installierte Paketversion
- Modus des Threat Intelligence-Updates 
- Status des Threat Intelligence-Updates

### <a name="view-cloud-connected-sensor-information-public-preview"></a>Anzeigen von Informationen zu mit der Cloud verbundenen Sensoren (Public Preview)

Wichtige Betriebsinformationen zu mit der Cloud verbundenen Sensoren finden Sie auf der Seite **Standorte und Sensoren**.

- Installierte Sensorversion
- Der Status der Sensorverbindung mit der Cloud.
- Der letzte Zeitpunkt, zu dem die Verbindungsherstellung des Sensors mit der Cloud erkannt wurde

### <a name="alert-api-enhancements"></a>Verbesserungen der Warnungs-API

Für Benutzer, die Warnungs-APIs verwenden, sind neue Felder verfügbar.

**Lokale Verwaltungskonsole**

- Quell- und Zieladresse
- Schritte zur Bereinigung
- Der vom Benutzer definierte Name des Sensors
- Der Name der Zone, die dem Sensor zugeordnet ist
- Der Name des Standorts, der dem Sensor zugeordnet ist

**Sensor**

- Quell- und Zieladresse
- Schritte zur Bereinigung

Bei Verwendung der neuen Felder ist API-Version 2 erforderlich.

### <a name="features-delivered-as-generally-available-ga"></a>Als allgemein verfügbar bereitgestellte Features

Die folgenden Features waren zuvor für die Public Preview verfügbar und sind jetzt allgemein verfügbar:

- Sensor: erweiterte benutzerdefinierte Warnungsregeln
- Lokale Verwaltungskonsole: Exportieren von Warnungen
- Hinzufügen einer zweiten Netzwerkschnittstelle zur lokalen Verwaltungskonsole
- Gerätehersteller: neuer Mikro-Agent

## <a name="march-2021"></a>März 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensor: erweiterte benutzerdefinierte Warnungsregeln (Public Preview)

Sie können jetzt benutzerdefinierte Regeln für Warnungen erstellen, die auf dem Tag, einer Gruppe von Tagen und dem Zeitraum basieren, in dem Netzwerkaktivität erkannt wurde.  Das Arbeiten mit Regelbedingungen für Tag und Uhrzeit ist nützlich, z. B. in Fällen, in denen der Schweregrad einer Warnung von der Uhrzeit des Ereignisses abgeleitet wird. Erstellen Sie z. B. eine benutzerdefinierte Regel, die eine Warnung mit hohem Schweregrad auslöst, wenn am Wochenende oder am Abend Netzwerkaktivität festgestellt wird.

Dieses Feature ist mit der Veröffentlichung der Version 10.2 im Sensor verfügbar.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Lokale Verwaltungskonsole: Exportieren von Warnungen (Public Preview)

Informationen zu Warnungen können jetzt aus der lokalen Verwaltungskonsole in eine CSV-Datei exportiert werden. Sie können Informationen zu allen erkannten Warnungen oder Informationen basierend auf der gefilterten Ansicht exportieren.

Dieses Feature ist in der lokalen Verwaltungskonsole mit Veröffentlichung von Version 10.2 verfügbar.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Hinzufügen einer zweiten Netzwerkschnittstelle zur lokalen Verwaltungskonsole (Public Preview)

Sie können die Sicherheit Ihrer Bereitstellung nun verbessern, indem Sie Ihrer lokalen Verwaltungskonsole eine zweite Netzwerkschnittstelle hinzufügen. Mit diesem Feature können Sie die verbundenen Sensoren Ihrer lokalen Verwaltung in einem sicheren Netzwerk verwenden und gleichzeitig Ihren Benutzern den Zugriff auf die lokale Verwaltungskonsole über eine zweite separate Netzwerkschnittstelle ermöglichen.

Dieses Feature ist in der lokalen Verwaltungskonsole mit Veröffentlichung von Version 10.2 verfügbar.

### <a name="device-builder---new-micro-agent-public-preview"></a>Gerätehersteller: neuer Mikro-Agent (öffentliche Vorschau)

Ein neues Geräteherstellermodul ist verfügbar. Das Modul, das als Micro-Agent bezeichnet wird, ermöglicht Folgendes:

- **Integration in Azure IoT Hub und Azure Defender für IoT** : Integrieren Sie mehr Endpunktsicherheit direkt in Ihre IoT-Geräte, indem Sie sie mit der Überwachungsoption von Azure IoT Hub und Azure Defender für IoT verbinden.
- **Flexible Bereitstellungsoptionen mit Unterstützung für IoT-Standardbetriebssysteme** können entweder als binäres Paket oder als modifizierbarer Quellcode bereitgestellt werden, mit Unterstützung für IoT-Standardbetriebssysteme wie Linux und Azure RTOS.
- **Minimale Ressourcenanforderungen ohne Kernelabhängigkeiten vom Betriebssystem**: geringer Platzbedarf, geringe CPU-Auslastung und keine Kernelabhängigkeiten vom Betriebssystem.
- **Verwaltung des Sicherheitsstatus**: Sie können den Sicherheitsstatus Ihrer IoT-Geräte aktiv verwalten.
- **Kontinuierliche Erkennung von IoT/OT-Bedrohungen in Echtzeit**: Erkennung von Bedrohungen wie Botnets, Brute-Force-Angriffen, Krypto-Minern und verdächtigen Netzwerkaktivitäten

Die veraltete Dokumentation zum Defender für IoT-Micro-Agent wird in den Ordner *Agent-basierte Lösung für Gerätehersteller > Classic* verschoben.

Diese Featuregruppe ist in der aktuellen öffentlichen Vorschauversion verfügbar.

## <a name="january-2021"></a>Januar 2021

- [Security](#security)
- [Onboarding](#onboarding)
- [Benutzerfreundlichkeit](#usability)
- [Andere Updates](#other-updates)

### <a name="security"></a>Sicherheit

Für dieses Release wurden Verbesserungen bei der Wiederherstellung von Zertifikaten und Kennwörtern vorgenommen.

#### <a name="certificates"></a>Zertifikate
  
Diese Version ermöglicht Folgendes:

- Direktes Hochladen von SSL-Zertifikaten auf die Sensoren und lokalen Verwaltungskonsolen
- Durchführen der Überprüfung zwischen der lokalen Verwaltungskonsole und den verbundenen Sensoren sowie zwischen einer Verwaltungskonsole und einer Verwaltungskonsole für Hochverfügbarkeit. Die Überprüfung basiert auf Ablaufdaten, der Echtzeit der Stammzertifizierungsstelle und auf Zertifikatssperrlisten.  Wenn bei der Überprüfung ein Fehler auftritt, wird die Sitzung nicht fortgesetzt.

Für Upgrades:

- Änderungen in Bezug auf die Funktionen für SSL-Zertifikate oder die Überprüfung während des Upgrades wurden nicht vorgenommen.
- Nach dem Upgrade können Administratorbenutzer der Sensoren und der lokalen Verwaltungskonsole SSL-Zertifikate ersetzen oder die Überprüfung von SSL-Zertifikaten über das Fenster „Systemeinstellungen“ > „SSL-Zertifikat“ aktivieren.  

Für Neuinstallationen:

- Bei der erstmaligen Anmeldung müssen Benutzer entweder ein SSL-Zertifikat (empfohlen) oder ein lokal generiertes selbstsigniertes Zertifikat (nicht empfohlen) verwenden.
- Für Neuinstallationen ist die Zertifikatüberprüfung standardmäßig aktiviert.

#### <a name="password-recovery"></a>Kennwortwiederherstellung
  
Administratorbenutzer der Sensoren und der lokalen Verwaltungskonsole können jetzt im Azure Defender für IoT-Portal Kennwörter wiederherstellen. Bisher musste für die Kennwortwiederherstellung das Supportteam hinzugezogen werden.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Lokale Verwaltungskonsole: Committete Geräte

Nach der erstmaligen Anmeldung bei der lokalen Verwaltungskonsole müssen Benutzer jetzt eine Aktivierungsdatei hochladen. Die Datei enthält die aggregierte Anzahl von Geräten, die im Netzwerk der Organisation überwacht werden sollen. Diese Zahl wird als Anzahl von committeten Geräten bezeichnet.
Committete Geräte werden beim Onboardingprozess im Azure Defender für IoT-Portal definiert, bei dem die Aktivierungsdatei generiert wird.
Erstmalige Benutzer und Benutzer, die ein Upgrade durchführen, müssen die Aktivierungsdatei hochladen.
Nach der ersten Aktivierung kann es vorkommen, dass die Anzahl von im Netzwerk erkannten Geräten die Anzahl von committeten Geräten übersteigt. Dies kann beispielsweise der Fall sein, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl erkannter Geräte und der Anzahl committeter Geräte wird in der Verwaltungskonsole eine Warnung angezeigt. In diesem Fall sollten Sie eine neue Aktivierungsdatei hochladen.

#### <a name="pricing-page-options"></a>Optionen auf der Preisseite

Auf der Preisseite können Sie für neue Abonnements das Onboarding für Azure Defender für IoT durchführen und committete Geräte in Ihrem Netzwerk definieren.  
Darüber hinaus können Sie auf der Preisseite jetzt vorhandene Abonnements verwalten, die einem Sensor zugeordnet sind, und die committeten Geräte aktualisieren.

#### <a name="view-and-manage-onboarded-sensors"></a>Anzeigen und Verwalten von integrierten Sensoren

Auf einer neuen Portalseite für Standorte und Sensoren können Sie folgende Aktionen durchführen:

- Hinzufügen von beschreibenden Informationen zum Sensor. Dies können beispielsweise Zonen sein, die dem Sensor zugeordnet sind, oder Freitext-Tags.
- Anzeigen und Filtern von Sensorinformationen. Sie können beispielsweise Details zu Sensoren, die mit der Cloud verbunden sind oder lokal verwaltet werden, oder Informationen zu Sensoren in einer bestimmten Zone anzeigen.  

### <a name="usability"></a>Benutzerfreundlichkeit

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel-Seite „Neuer Connector“

Die Azure Defender für IoT-Seite für den Datenconnector in Azure Sentinel wurde überarbeitet. Der Datenconnector basiert jetzt nicht mehr auf IoT-Hubs, sondern auf Abonnements, um Kunden eine bessere Verwaltung ihrer Konfigurationsverbindung in Azure Sentinel zu ermöglichen.

#### <a name="azure-portal-permission-updates"></a>Updates für Berechtigungen im Azure-Portal  

Die Unterstützung für „Sicherheitsleseberechtigte“ und „Sicherheitsadministrator“ wurde hinzugefügt.

### <a name="other-updates"></a>Weitere Updates

#### <a name="access-group---zone-permissions"></a>Zugriffsgruppe: Zonenberechtigungen
  
Die Zugriffsgruppenregeln für die lokale Verwaltungskonsole enthalten nicht die Option zum Gewähren des Zugriffs auf eine bestimmte Zone. Es wurde keine Änderung für das Definieren von Regeln vorgenommen, in denen Standorte, Regionen und Geschäftseinheiten verwendet werden.   Nach dem Upgrade werden Zugriffsgruppen, für die über Regeln der Zugriff auf bestimmte Zonen zulässig war, so geändert, dass der Zugriff auf deren übergeordneten Standort, einschließlich aller Zonen, möglich ist.

#### <a name="terminology-changes"></a>Terminologieänderungen

Anstelle des Ausdrucks „Ressource“ wird für den Sensor und die lokale Verwaltungskonsole, in den Berichten und in anderen Lösungsbenutzeroberflächen jetzt „Gerät“ verwendet.
Anstelle von „Ereignis verwalten“ wird in Warnungen für den Sensor und in der lokalen Verwaltungskonsole jetzt „Problembehandlungsschritte“ verwendet.

## <a name="next-steps"></a>Nächste Schritte

[Info zu Agent-basierten Lösungen für Gerätehersteller](architecture-agent-based.md)
