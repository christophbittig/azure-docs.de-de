---
title: Neuerungen im Agent für Azure Arc-fähige Server
description: Dieser Artikel enthält Versionshinweise für den Agent für Azure Arc-fähige Server. Für viele der zusammengefassten Issues werden Links zu ausführlicheren Informationen bereitgestellt.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: d53ebd32c870ce8ec26bca7bcb811fbdd45c58b2
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392372"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Neuerungen im Agent für Azure Arc-fähige Server

Der Connected Machine-Agent für Azure Arc-fähige Server wird ständig verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

## <a name="july-2021"></a>Juli 2021

Version 1.8

### <a name="new-features"></a>Neue Funktionen

- Verbesserte Zuverlässigkeit bei der Installation der Azure Monitor-Agent-Erweiterung auf Red Hat- und CentOS-Systemen
- Agent-seitige Erzwingung der maximalen Ressourcennamenlänge (54 Zeichen) hinzugefügt
- Verbesserungen an der Gastkonfigurationsrichtlinie:
  - Unterstützung für PowerShell-basierte Gastkonfigurationsrichtlinien unter Linux-Betriebssystemen hinzugefügt
  - Unterstützung für mehrere Zuweisungen derselben Gastkonfigurationsrichtlinie auf demselben Server hinzugefügt
  - Upgrade von PowerShell Core auf Version 7.1 unter Windows-Betriebssystemen

### <a name="fixed"></a>Fest

- Der Agent wird weiterhin ausgeführt, wenn er keine Start-/Stop-Dienstereignisse in das Windows Anwendungsereignisprotokoll schreiben kann.

## <a name="june-2021"></a>Juni 2021

Version 1.7

### <a name="new-features"></a>Neue Funktionen

- Verbesserte Zuverlässigkeit beim Onboarding:
  - Verbesserte Wiederholungslogik, wenn HIMDS nicht verfügbar ist
  - Das Onboarding wird jetzt fortgesetzt, anstatt abgebrochen zu werden, wenn keine Betriebssysteminformationen abgerufen werden können.
- Verbesserte Zuverlässigkeit bei der Installation der OMS-Agent-Erweiterung auf Red Hat- und CentOS-Systemen

## <a name="may-2021"></a>Mai 2021

Version 1.6

### <a name="new-features"></a>Neue Funktionen

- Die Unterstützung für SUSE Enterprise Linux 12 wurde hinzugefügt
- Der Gastkonfigurationsagent wurde auf die Version 1.26.12.0 aktualisiert, um Folgendes einzuschließen:

   - Die Richtlinien werden in einem separaten Prozess ausgeführt.
   - Die Unterstützung der V2-Signatur für die Erweiterungsüberprüfung wurde hinzugefügt.
   - Eine kleinere Aktualisierung für die Datenprotokollierung.

## <a name="april-2021"></a>April 2021

Version 1.5

### <a name="new-features"></a>Neue Funktionen

- Es wurde die Unterstützung für Red Hat Enterprise Linux 8 und CentOS Linux 8 hinzugefügt.
- Neuer `-useStderr` Parameter, um Fehler und ausführliche Ausgabe an stderr weiterzuleiten.
- Neuer Parameter `-json` zur direkten Ausgabe der Ergebnisse im JSON-Format (bei Verwendung mit -useStderr).
- Sammeln weiterer Instanzmetadaten: Hersteller, Modell und Clusterressourcen-ID (für Azure Stack HCI Knoten).
 
## <a name="march-2021"></a>März 2021

Version 1.4

### <a name="new-features"></a>Neue Funktionen

- Unterstützung für private Endpunkte, die sich derzeit in der eingeschränkten Vorschau befinden, wurde hinzugefügt.
- Die Liste der Exitcodes für azcmagent wurde erweitert.
- Agent-Konfigurationsparameter können nun aus einer Datei mit dem `--config` Parameter gelesen werden.
- Sammeln neuer Instanzmetadaten, um zu bestimmen, ob Microsoft SQL Server auf dem Server installiert ist.

### <a name="fixed"></a>Fest

Netzwerkendpunktüberprüfungen sind nun schneller.

## <a name="december-2020"></a>Dezember 2020

Version: 1.3

### <a name="new-features"></a>Neue Funktionen

Die Unterstützung für den Windows Server 2008 R2 SP1 wurde hinzugefügt.

### <a name="fixed"></a>Fest

Ein Problem wurde behoben, das verhindert, dass die benutzerdefinierte Skripterweiterung für Linux erfolgreich installiert wird.

## <a name="november-2020"></a>November 2020

Version: 1.2

### <a name="fixed"></a>Fest

Ein Problem wurde behoben, bei dem die Proxykonfiguration nach dem Upgrade auf RPM-basierte Verteilungen verloren gehen konnte.

## <a name="october-2020"></a>Oktober 2020

Version: 1.1

### <a name="fixed"></a>Fest

- Es wurde ein Proxyskript korrigiert, sodass der Speicherort einer alternativen GC-Daemon-Moduldatei verarbeitet werden kann.
- Änderungen an der Zuverlässigkeit des GuestConfig-Agent.
- Unterstützung des GuestConfig-Agent für die Region „US Gov Virginia“.
- Die Berichtsmeldungen der GuestConfig-Agent-Erweiterung, die bei Fehlern angezeigt werden, sind nun ausführlicher.

## <a name="september-2020"></a>September 2020

Version: 1.0 (Allgemeine Verfügbarkeit)

### <a name="plan-for-change"></a>Plan für Änderung

- Unterstützung für Vorschau-Agents (alle Versionen, die älter als 1.0 sind) wird in einem zukünftigen Dienstupdate entfernt.
- Unterstützung für den Fallbackendpunkt `.azure-automation.net` wurde entfernt. Wenn Sie einen Proxy verwenden, müssen Sie den Endpunkt `*.his.arc.azure.com` zulassen.
- Wenn der Connected Machine-Agent auf einem virtuellen Computer installiert ist, der in Azure gehostet wird, können VM-Erweiterungen nicht von der Ressource „Arc-fähige Server“ installiert oder geändert werden. Dadurch soll vermieden werden, dass widersprüchliche Erweiterungsvorgänge von der Ressource **Microsoft.Compute** und **Microsoft.HybridCompute** des virtuellen Computers durchgeführt werden. Verwenden Sie die **Microsoft.Compute**-Ressource für den Computer für alle Erweiterungsvorgänge.
- Der Name des Gastkonfigurationsprozesses wurde aus *gcd* in *gcad* unter Linux und aus *gcservice* in *gcarcservice* unter Windows geändert.

### <a name="new-features"></a>Neue Funktionen

- `azcmagent logs`-Option zum Erfassen von Informationen für den Support wurde hinzugefügt.
- `azcmagent license`-Option zum Anzeigen der EULA wurde hinzugefügt.
- `azcmagent show --json`-Option zum Ausgeben des Agent-Zustands in einem leicht zu analysierenden Format wurde hinzugefügt.
- Flag in `azcmagent show`-Ausgabe wurde hinzugefügt, um anzugeben, ob sich der Server auf einem in Azure gehosteten virtuellen Computer befindet.
- `azcmagent disconnect --force-local-only`-Option wurde hinzugefügt, um das Zurücksetzen des lokalen Agent-Zustands zuzulassen, wenn der Azure-Dienst nicht erreichbar ist.
- Die Option `azcmagent connect --cloud` wurde zur Unterstützung weiterer Clouds hinzugefügt. In diesem Release wird nur Azure vom Dienst zum Zeitpunkt des Agent-Releases unterstützt.
- Der Agent wurde in von Azure unterstützte Sprachen lokalisiert.

### <a name="fixed"></a>Fest

- Verbesserungen der Verbindungsüberwachung.
- Problem wurde behoben, durch das die Proxyservereinstellungen beim Upgrade des Agents unter Linux verloren gingen.
- Probleme beim Versuch behoben, den Agent auf einem Server zu installieren, auf dem Windows Server 2012 R2 ausgeführt wird.
- Verbesserungen bei der Zuverlässigkeit der Erweiterung.

## <a name="august-2020"></a>August 2020

Version: 0.11

- Für dieses Release wurde zuvor Unterstützung für Ubuntu 20.04 angekündigt. Da einige Azure-VM-Erweiterungen Ubuntu 20.04 nicht unterstützen, wird Unterstützung für diese Version von Ubuntu entfernt.

- Verbesserungen bei der Zuverlässigkeit von Erweiterungsbereitstellungen.

### <a name="known-issues"></a>Bekannte Probleme

Wenn Sie eine ältere Version des Linux-Agents verwenden und dieser für die Verwendung eines Proxyservers konfiguriert ist, müssen Sie die Proxyservereinstellung nach dem Upgrade neu konfigurieren. Führen Sie hierzu `sudo azcmagent_proxy add http://proxyserver.local:83` aus.

## <a name="next-steps"></a>Nächste Schritte

- Bevor Sie Server mit Arc-Unterstützung auf mehreren Hybridcomputern auswerten oder aktivieren, lesen Sie [Übersicht über den Connected Machine-Agent](agent-overview.md), um die Anforderungen und technischen Details zum Agent und Bereitstellungsmethoden zu verstehen.

- Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.
