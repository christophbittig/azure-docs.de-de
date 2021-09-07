---
title: Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten
titleSuffix: Azure Storage
description: Eine Aufbewahrung für juristische Zwecke speichert Blobdaten in einem WORM-Format (Write-Once, Read-Many), bis sie explizit gelöscht werden. Verwenden Sie eine Aufbewahrung für juristische Zwecke, wenn der Zeitraum, in dem die Daten in einem WORM-Zustand aufbewahrt werden müssen, unbekannt ist.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1434f689f8f629fd04ec0c0b059a1298b32d5ac9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349568"
---
# <a name="legal-holds-for-immutable-blob-data"></a>Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten

Eine Aufbewahrung für juristische Zwecke ist eine vorübergehende Unveränderlichkeitsrichtlinie, die für juristische Untersuchungszwecke oder allgemeine Schutzrichtlinien angewendet werden kann. Eine Aufbewahrung für juristische Zwecke speichert Blobdaten in einem WORM-Format (Write-Once, Read-Many), bis sie explizit gelöscht werden. Wenn ein Zeitraum für die Aufbewahrung für juristische Zwecke festgelegt wird, können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Verwenden Sie eine Aufbewahrung für juristische Zwecke, wenn der Zeitraum, in dem die Daten in einem WORM-Zustand aufbewahrt werden müssen, unbekannt ist.

Weitere Informationen zu Unveränderlichkeitsrichtlinien für Blob Storage finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md).

## <a name="legal-hold-scope"></a>Geltungsbereich von Richtlinien zur Aufbewahrung für juristische Zwecke

Eine Aufbewahrung für juristische Zwecke kann in einem der folgenden Bereiche konfiguriert werden:

- Richtlinie auf Versionsebene (Vorschau): Eine Aufbewahrungsrichtlinie für juristische Zwecke kann auf der Ebene einer einzelnen Blobversion konfiguriert werden, um sensible Daten präzise zu verwalten.
- Richtlinie auf Containerebene: Eine Aufbewahrungsrichtlinie für juristische Zwecke, die auf Containerebene konfiguriert ist, gilt für alle Blobs in diesem Container. Einzelne Blobs können nicht mit jeweils eigenen Unveränderlichkeitsrichtlinien konfiguriert werden.

### <a name="version-level-policy-scope-preview"></a>Geltungsbereich von Richtlinien auf Versionsebene (Vorschau)

Um eine Aufbewahrung für juristische Zwecke für eine Blobversion zu konfigurieren, müssen Sie zunächst die Unveränderlichkeit auf Versionsebene für den übergeordneten Container aktivieren. Die Unveränderlichkeit auf Versionsebene kann nicht deaktiviert werden, nachdem sie aktiviert wurde. Weitere Informationen finden Sie unter [Aktivieren der Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Nachdem die Unveränderlichkeit auf Versionsebene für einen Container aktiviert wurde, kann eine Aufbewahrung für juristische Zwecke nicht mehr auf Containerebene festgelegt werden. Aufbewahrungsrichtlinien für juristische Zwecke müssen auf einzelne Blobversionen angewendet werden. Eine Aufbewahrung für juristische Zwecke kann für die aktuelle Version oder eine frühere Version eines Blobs konfiguriert werden.

Für Aufbewahrungsrichtlinien für juristische Zwecke auf Versionsebene muss die Blobversionsverwaltung für das Speicherkonto aktiviert werden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md). Beachten Sie, dass die Aktivierung der Versionsverwaltung sich auf die Abrechnung auswirken kann. Weitere Informationen finden Sie im Abschnitt **Preise und Abrechnung** unter [Blobversionsverwaltung](versioning-overview.md#pricing-and-billing).

Weitere Informationen zum Aktivieren einer Aufbewahrung für juristische Zwecke auf Versionsebene finden Sie unter [Konfigurieren oder Löschen einer Aufbewahrungsrichtlinie für juristische Zwecke](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold).

### <a name="container-level-scope"></a>Geltungsbereich auf Containerebene

Wenn Sie eine Aufbewahrungsrichtlinie für juristische Zwecke für einen Container konfigurieren, gilt diese Aufbewahrungsrichtlinie für alle Objekte im Container. Wenn die Aufbewahrungsrichtlinie für juristische Zwecke gelöscht wird, können Clients wieder Objekte im Container erstellen und löschen, es sei denn, dass auch eine zeitbasierte Aufbewahrungsrichtlinie für den Container wirksam ist.

Wenn eine Aufbewahrungsrichtlinie für juristische Zwecke auf einen Container angewendet wird, werden alle vorhandenen Blobs innerhalb von weniger als 30 Sekunden in einen unveränderlichen WORM-Zustand versetzt. Alle neuen Blobs, die in den durch die Richtlinie geschützten Container hochgeladen werden, werden ebenfalls in einen unveränderlichen Zustand versetzt. Wenn sich alle Blobs in einem unveränderlichen Zustand befinden, sind Überschreibungs- oder Löschvorgänge im unveränderlichen Container unzulässig. Bei einem Konto mit einem hierarchischen Namespace können Blobs nicht umbenannt oder in ein anderes Verzeichnis verschoben werden.

Informationen zum Konfigurieren einer Aufbewahrungsrichtlinie für juristische Zwecke mit einem Geltungsbereich auf Containerebene finden Sie unter [Konfigurieren oder Löschen einer Aufbewahrungsrichtlinie für juristische Zwecke](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold).

#### <a name="legal-hold-tags"></a>Tags für Aufbewahrungsrichtlinien für juristische Zwecke

Eine Aufbewahrungsrichtlinie für juristische Zwecke auf Containerebene muss mindestens einem benutzerdefinierten alphanumerischen Tag zugeordnet werden, das als Bezeichnerzeichenfolge dient. Ein Tag kann beispielsweise eine Fall-ID oder einen Ereignisnamen enthalten.

#### <a name="audit-logging"></a>Überwachungsprotokollierung

Jeder Container mit einer Aufbewahrungsrichtlinie für juristische Zwecke stellt ein Richtlinienüberwachungsprotokoll bereit.  Das Protokoll enthält Benutzer-ID, Befehlstyp, Zeitstempel und die Tags für die Aufbewahrungsrichtlinie für juristische Zwecke. Das Überwachungsprotokoll wird für die Lebensdauer der Richtlinie gemäß den SEC 17a-4(f)-Bestimmungsrichtlinien aufbewahrt.

Das [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/platform-logs-overview.md) ist ein umfassenderes Protokoll mit allen Verwaltungsdienstaktivitäten. [Azure-Ressourcenprotokolle](../../azure-monitor/essentials/platform-logs-overview.md) enthalten Informationen zu Datenvorgängen. Der Benutzer ist für die dauerhafte Speicherung dieser Protokolle verantwortlich, die aus gesetzlichen oder anderen Gründen ggf. erforderlich ist.

#### <a name="limits"></a>Grenzwerte

Die folgenden Grenzwerte gelten für Aufbewahrungsrichtlinien für juristische Zwecke auf Containerebene:

- Ein Speicherkonto kann über maximal 10.000 Container mit einer Einstellung zur Aufbewahrung für juristische Zwecke verfügen.
- Ein Container kann über maximal zehn Tags für die Aufbewahrungsrichtlinie für juristische Zwecke verfügen.
- Ein Tag für die Aufbewahrung für juristische Zwecke muss mindestens drei alphanumerische Zeichen lang sein. Die Höchstlänge beträgt 23 alphanumerische Zeichen.
- Für einen Container werden höchstens zehn Richtlinienüberwachungsprotokolle für die Aufbewahrungsrichtlinie für juristische Zwecke für die Geltungsdauer der Richtlinie aufbewahrt.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht zum Datenschutz](data-protection-overview.md)
- [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md)
- [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Container](immutable-policy-configure-container-scope.md)
