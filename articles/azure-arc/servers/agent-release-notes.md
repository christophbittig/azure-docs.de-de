---
title: Neuerungen im Agent für Azure Arc-fähige Server
description: Dieser Artikel enthält Versionshinweise für den Agent für Azure Arc-fähige Server. Für viele der zusammengefassten Issues werden Links zu ausführlicheren Informationen bereitgestellt.
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d4008a41629ac2e71e1abdb91e30f2d6b9350538
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431602"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Neuerungen im Agent für Azure Arc-fähige Server

Der Connected Machine-Agent für Azure Arc-fähige Server wird ständig verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. Wenn Sie nach Elementen suchen, die älter als sechs Monate sind, können Sie sie im [Archiv für Neuerungen im Agent für Server mit Azure Arc-Unterstützung](agent-release-notes-archive.md) finden.

## <a name="august-2021"></a>August 2021

Version 1.10

### <a name="fixed"></a>Fest

- Der Gastkonfigurationsrichtlinien-Agent kann jetzt Systemeinstellungen konfigurieren und korrigieren. Vorhandene Richtlinienzuweisungen können weiterhin nur überwacht werden. Informieren Sie sich ausführlicher über die [Azure Policy-Wartungsoptionen für die Gastkonfiguration](../../governance/policy/concepts/guest-configuration-policy-effects.md).
- Der Gastkonfigurationsrichtlinien-Agent startet jetzt nicht mehr alle 6 Stunden, sondern alle 48 Stunden neu.

## <a name="july-2021"></a>Juli 2021

Version 1.9

## <a name="new-features"></a>Neue Funktionen

Unterstützung für Indonesisch hinzugefügt

### <a name="fixed"></a>Fest

Ein Fehler wurde behoben, der die Erweiterungsverwaltung in der Region „USA, Westen 3“ verhindert hat.

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
 
## <a name="next-steps"></a>Nächste Schritte

- Bevor Sie Server mit Arc-Unterstützung auf mehreren Hybridcomputern auswerten oder aktivieren, lesen Sie [Übersicht über den Connected Machine-Agent](agent-overview.md), um die Anforderungen und technischen Details zum Agent und Bereitstellungsmethoden zu verstehen.

- Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.
