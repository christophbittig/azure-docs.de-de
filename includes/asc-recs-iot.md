---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 09/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 1e6040007e345be6e1b80e7e797daf4b1f3d862a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536842"
---
Diese Kategorie enthält **zwölf** Empfehlungen.

|Empfehlung |BESCHREIBUNG |severity |
|---|---|---|
|[Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |Die IP-Filterkonfiguration sollte Regeln für zulässigen Datenverkehr definieren und den gesamten übrigen Datenverkehr ablehnen<br />(Keine zugehörige Richtlinie) |Medium |
|[In IoT Hub müssen Diagnoseprotokolle aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist.<br />(Zugehörige Richtlinie: [In IoT Hubs sollten Diagnoseprotokolle aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Niedrig |
|[ Identische Anmeldeinformationen für Authentifizierung](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |Identische Authentifizierungsinformationen für den IoT Hub wurden von mehreren Geräten verwendet. Dies kann auf ein unzulässiges Gerät hinweisen, das die Identität eines legitimen Geräts vorgibt. Außerdem entsteht das Risiko, dass ein Angreifer die Identität eines Geräts vorgibt<br />(Keine zugehörige Richtlinie) |Hoch |
|[ IoT-Geräte: Agent sendet Nachrichten zu Unterauslastung](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |Die Kapazität für die Nachrichtengröße des IoT-Agent wird zurzeit nicht ausgeschöpft. Dadurch erhöht sich die Anzahl gesendeter Nachrichten. Passen Sie die Nachrichtenintervalle an, um eine bessere Nutzung zu erzielen<br />(Keine zugehörige Richtlinie) |Niedrig |
|[ IoT-Geräte: Überwachter Prozess hat das Senden von Ereignissen beendet](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |Aus dem Auditd-Prozess stammende sicherheitsrelevante Ereignisse werden von diesem Gerät nicht mehr empfangen<br />(Keine zugehörige Richtlinie) |Hoch |
|[ IoT-Geräte: offene Ports auf Gerät](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |Auf dem Gerät wurde ein lauschender Endpunkt gefunden<br />(Keine zugehörige Richtlinie) |Medium |
|[ IoT-Geräte: Fehler bei Validierung von Baseline von Betriebssystem](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |In der Systemkonfiguration wurden sicherheitsbezogene Probleme erkannt<br />(Keine zugehörige Richtlinie) |Medium |
|[ IoT-Geräte: In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |In den Hauptfirewallketten (Eingabe/Ausgabe) wurde eine zugelassene Firewallrichtlinie gefunden. Die Richtlinie muss standardmäßig den gesamten Datenverkehr verweigern und Regeln definieren, die die notwendige Kommunikation zum/vom Gerät ermöglichen<br />(Keine zugehörige Richtlinie) |Medium |
|[ IoT-Geräte: In der Eingabekette wurde eine zu wenig einschränkende Firewallregel gefunden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält.<br />(Keine zugehörige Richtlinie) |Medium |
|[ IoT-Geräte: In der Ausgabekette wurde eine zu wenig einschränkende Firewallregel gefunden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält<br />(Keine zugehörige Richtlinie) |Medium |
|[ IoT-Geräte:Upgrade der TLS-Verschlüsselungssammlung erforderlich](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |Unsichere TLS-Konfigurationen wurden erkannt. Sofortiges Upgrade der TLS-Verschlüsselungssammlung empfohlen<br />(Keine zugehörige Richtlinie) |Medium |
|[ IP-Filterregel umfasst großen IP-Adressbereich](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |Der Quell-IP-Adressbereich einer Filterregel für zulässige IP-Adressen ist zu groß. Durch zu wenig einschränkende Regeln ist Ihr IoT Hub möglicherweise Personen mit böser Absicht ausgesetzt<br />(Keine zugehörige Richtlinie) |Medium |
|||
