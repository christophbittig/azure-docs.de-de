---
title: Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten
titleSuffix: Azure Storage
description: Über zeitbasierte Aufbewahrungsrichtlinien werden Blobdaten im WORM-Zustand (Write-Once, Read-Many) für einen festgelegten Zeitraum gespeichert. Sie können eine zeitbasierte Aufbewahrungsrichtlinie konfigurieren, die sich auf eine Blobversion (Vorschau) oder einen Container bezieht.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1022ebd9166e3e2e92a4c67cd434190c91dd09c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339794"
---
# <a name="time-based-retention-policies-for-immutable-blob-data"></a>Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten

Über eine zeitbasierte Aufbewahrungsrichtlinie werden Blobdaten im WORM-Format (Write-Once, Read-Many) für einen festgelegten Zeitraum gespeichert. Wenn eine zeitbasierte Aufbewahrungsrichtlinie festgelegt ist, können Clients Blobs erstellen und lesen, diese jedoch nicht ändern oder löschen. Nach Ablauf des Aufbewahrungszeitraums können Blobs gelöscht, jedoch nicht überschrieben werden.

Weitere Informationen zu Unveränderlichkeitsrichtlinien für Blob Storage finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md).

## <a name="retention-interval-for-a-time-based-policy"></a>Aufbewahrungszeitraum für eine zeitbasierte Richtlinie

Der kürzeste Aufbewahrungszeitraum für eine zeitbasierte Aufbewahrungsrichtlinie liegt bei einem Tag und der längste bei 146.000 Tagen (400 Jahre).

Wenn Sie eine zeitbasierte Aufbewahrungsrichtlinie konfigurieren, bleiben die betroffenen Objekte so lange im unveränderlichen Zustand, wie der Aufbewahrungszeitraum *gilt*. Die Gültigkeit des Aufbewahrungszeitraums für Objekte entspricht der Differenz zwischen der Erstellungszeit des Blobs und dem vom Benutzer angegebenen Aufbewahrungszeitraum. Da der Aufbewahrungszeitraum einer Richtlinie verlängert werden kann, wird für den unveränderlichen Speicher der letzte Wert des vom Benutzer angegebenen Aufbewahrungszeitraums verwendet, um den effektiven Aufbewahrungszeitraum zu berechnen.

Ein Beispiel: Angenommen, der Benutzer erstellt eine zeitbasierte Aufbewahrungsrichtlinie mit einem Aufbewahrungszeitraum von fünf Jahren. Ein in diesem Container vorhandenes Blob (_testblob1_) wurde vor einem Jahr erstellt. Der effektive Aufbewahrungszeitraum für _testblob1_ beträgt also vier Jahre. Wenn ein neues Blob (_testblob2_) in den Container hochgeladen wird, beträgt der effektive Aufbewahrungszeitraum für _testblob2_ fünf Jahre ab dem Zeitpunkt seiner Erstellung.

## <a name="locked-versus-unlocked-policies"></a>Gesperrte und entsperrte Richtlinien

Wenn Sie erstmals eine zeitbasierte Aufbewahrungsrichtlinie konfigurieren, wird die Richtlinie zu Testzwecken entsperrt. Nach Abschluss der Tests können Sie die Richtlinie sperren, damit sie vollständig mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen konform ist.

Gesperrte und entsperrte Richtlinien schützen vor Lösch- und Überschreibvorgängen. Sie können eine entsperrte Richtlinie jedoch ändern, indem Sie den Aufbewahrungszeitraum verkürzen oder verlängern. Außerdem können Sie eine entsperrte Richtlinie löschen.

Eine gesperrte zeitbasierte Aufbewahrungsrichtlinie können Sie nicht löschen. Sie können den Aufbewahrungszeitraum verlängern, jedoch nicht verkürzen. Maximal fünf Verlängerungen des effektiven Aufbewahrungszeitraums sind während der Lebensdauer einer gesperrten Richtlinie zulässig, die auf Containerebene definiert ist. Für eine Richtlinie, die für eine Blobversion konfiguriert wurde, ist die Anzahl der Verlängerungen des effektiven Zeitraums nicht beschränkt.

> [!IMPORTANT]
> Eine zeitbasierte Aufbewahrungsrichtlinie muss gesperrt sein, damit das Blob zur Erzielung von Konformität mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen in einem konformen unveränderlichen Zustand ist (Schreib- und Löschschutz). Microsoft empfiehlt, die Richtlinie in einem angemessenen Zeitraum zu sperren (in der Regel weniger als 24 Stunden). Der Zustand „Entsperrt“ bietet zwar Unveränderlichkeitsschutz, allerdings wird die Verwendung des Zustands „Entsperrt“ nicht für andere Zwecke als für kurzfristige Tests empfohlen.

## <a name="time-based-retention-policy-scope"></a>Richtlinienbereich von zeitbasierten Aufbewahrungsrichtlinien

Eine zeitbasierte Aufbewahrungsrichtlinie kann in einem der folgenden Bereiche konfiguriert werden:

- Richtlinie auf Versionsebene (Vorschau): Eine zeitbasierte Aufbewahrungsrichtlinie kann so konfiguriert werden, dass sie sich auf eine Blobversion bezieht und so eine differenzierte Verwaltung vertraulicher Daten ermöglicht. Sie können die Richtlinie auf eine einzelne Version anwenden oder eine Standardrichtlinie für einen Container konfigurieren, die standardmäßig für alle Blobs gilt, die in den Container hochgeladen werden.
- Richtlinie auf Containerebene: eine zeitbasierte Aufbewahrungsrichtlinie, die auf Containerebene konfiguriert wird und für alle Objekte im entsprechenden Container gilt. Einzelne Objekte können nicht mit jeweils eigenen Unveränderlichkeitsrichtlinien konfiguriert werden.

Überwachungsprotokolle sind für den Container für zeitbasierte Aufbewahrungsrichtlinien auf Versionsebene sowie auf Containerebene verfügbar. Überwachungsprotokolle sind nicht für eine Richtlinie verfügbar, die für eine Blobversion gilt.

### <a name="version-level-policy-scope-preview"></a>Richtlinienbereich von Richtlinien auf Versionsebene (Vorschau)

Zum Konfigurieren von Aufbewahrungsrichtlinien auf Versionsebene müssen Sie zunächst die Unveränderlichkeit auf Versionsebene für den übergeordneten Container aktivieren. Die Unveränderbarkeit auf Versionsebene kann nicht deaktiviert werden, nachdem sie aktiviert wurde, entsperrte Richtlinien können jedoch gelöscht werden. Weitere Informationen finden Sie unter [Aktivieren der Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Sie können die Unterstützung der Unveränderlichkeit auf Versionsebene zum Zeitpunkt der Erstellung eines Containers aktivieren. Auch für vorhandene Container kann die Unveränderlichkeit auf Versionsebene aktiviert werden, jedoch muss zuvor eine Migration durchgeführt werden. Dieser Vorgang kann einige Zeit in Anspruch nehmen, und er kann nicht rückgängig gemacht werden. Weitere Informationen zum Migrieren eines Containers zur Unterstützung der Unveränderlichkeit auf Versionsebene finden Sie unter [Migrieren eines vorhandenen Containers zur Unterstützung der Unveränderlichkeit auf Versionsebene](immutable-policy-configure-version-scope.md#migrate-an-existing-container-to-support-version-level-immutability).

Für zeitbasierte Aufbewahrungsrichtlinien auf Versionsebene muss die [Blobversionsverwaltung](versioning-overview.md) für das Speicherkonto aktiviert werden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md). Beachten Sie, dass die Aktivierung der Versionsverwaltung sich auf die Abrechnung auswirken kann. Weitere Informationen finden Sie im Abschnitt **Preise und Abrechnung** unter [Blobversionsverwaltung](versioning-overview.md#pricing-and-billing).

Wenn Sie nach der Aktivierung der Versionsverwaltung erstmalig ein Blob hochladen, wird diese Version des Blob zur aktuellen Version. Bei jeder Überschreibung des Blobs wird eine neue Version erstellt, mit der der vorherige Zustand des Blobs gespeichert wird. Wenn Sie die aktuelle Version eines Blobs löschen, wird sie zu einer vorherigen Version und beibehalten, bis sie explizit gelöscht wird. Eine vorherige Blobversion beinhaltet die zeitbasierte Aufbewahrungsrichtlinie, die zu dem Zeitpunkt gültig war, zu dem die aktuelle Version zu einer vorherigen Version wurde.

Wenn eine Standardrichtlinie für den Container gültig ist und dann durch eine Überschreibung eine vorherige Version erstellt wird, erbt die neue aktuelle Version die Standardrichtlinie für den Container.

Für jede Version kann nur eine zeitbasierte Aufbewahrungsrichtlinie konfiguriert werden. Für eine Version kann auch eine gesetzliche Aufbewahrungspflicht konfiguriert werden. Weitere Informationen zu unterstützten Konfigurationen von Unveränderlichkeitsrichtlinien basierend auf dem Bereich finden Sie unter [Bereich von Unveränderlichkeitsrichtlinien](immutable-storage-overview.md#immutability-policy-scope).

Informationen zum Konfigurieren von zeitbasierten Aufbewahrungsrichtlinien auf Versionsebene finden Sie unter [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md).

> [!IMPORTANT]
> Zeitbasierte Aufbewahrungsrichtlinien auf Versionsebene befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
> Nach der Aktivierung der Unveränderlichkeit auf Versionsebene kann es bis zu 30 Sekunden dauern, bevor Sie zeitbasierte Aufbewahrungsrichtlinien auf Versionsebene konfigurieren können.

#### <a name="configure-a-policy-on-the-current-version"></a>Konfigurieren einer Richtlinie für die aktuelle Version

Nachdem Sie die Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container aktiviert haben, haben Sie die Möglichkeit, eine zeitbasierte Standardaufbewahrungsrichtlinie für den Container zu konfigurieren. Wenn Sie eine zeitbasierte Standardaufbewahrungsrichtlinie für den Container konfigurieren und dann ein Blob hochladen, erbt das Blob standardmäßig diese Richtlinie. Außerdem können Sie die Standardrichtlinie für jedes Blob überschreiben, indem Sie eine benutzerdefinierte Richtlinie für das entsprechende Blob konfigurieren.

Wenn die zeitbasierte Standardaufbewahrungsrichtlinie für den Container entsperrt ist, beinhaltet auch die aktuelle Version eines Blobs, das die Standardrichtlinie erbt, eine entsperrte Richtlinie. Nach dem Hochladen eines einzelnen Blobs können Sie den Aufbewahrungszeitraum für die Richtlinie für die aktuelle Version des Blobs verkürzen oder verlängern oder die aktuelle Version löschen. Außerdem können Sie die Richtlinie für die aktuelle Version sperren, selbst wenn die Standardrichtlinie für den Container entsperrt bleibt.

Wenn die zeitbasierte Standardaufbewahrungsrichtlinie für den Container gesperrt ist, beinhaltet auch die aktuelle Version eines Blobs, das die Standardrichtlinie erbt, eine gesperrte Richtlinie. Wenn Sie jedoch die Standardrichtlinie beim Hochladen eines Blobs überschreiben und nur eine Richtlinie für das Blob festlegen, bleibt die Richtlinie des Blobs entsperrt, bis Sie sie explizit sperren. Wenn die Richtlinie für die aktuelle Version gesperrt ist, können Sie den Aufbewahrungszeitraum verlängern, jedoch weder die Richtlinie löschen noch den Aufbewahrungszeitrum verkürzen.

Wenn für einen Container keine Standardrichtlinie konfiguriert ist, können Sie ein Blob entweder mit einer benutzerdefinierten Richtlinie oder ohne Richtlinie hochladen.

Wenn die Standardrichtlinie für einen Container geändert wird, bleiben Richtlinien für Objekte innerhalb des Containers unverändert, auch wenn die Richtlinien von der Standardrichtlinie geerbt wurden.

In der Tabelle unten sind die verschiedenen verfügbaren Optionen zum Festlegen einer zeitbasierten Aufbewahrungsrichtlinie für ein Blob beim Hochladen aufgeführt:

| Standardrichtlinienstatus für Container | Hochladen eines Blobs mit der Standardrichtlinie | Hochladen eines Blobs mit einer benutzerdefinierten Richtlinie | Hochladen eines Blobs ohne Richtlinie |
|--|--|--|--|
| Standardrichtlinie für Container (entsperrt) | Blob wird mit entsperrter Standardrichtlinie hochgeladen | Blob wird mit entsperrter benutzerdefinierter Richtlinie hochgeladen | Blob wird ohne Richtlinie hochgeladen |
| Standardrichtlinie für Container (gesperrt) | Blob wird mit gesperrter Standardrichtlinie hochgeladen | Blob wird mit entsperrter benutzerdefinierter Richtlinie hochgeladen | Blob wird ohne Richtlinie hochgeladen |
| Keine Standardrichtlinie für Container | – | Blob wird mit entsperrter benutzerdefinierter Richtlinie hochgeladen | Blob wird ohne Richtlinie hochgeladen |

#### <a name="configure-a-policy-on-a-previous-version"></a>Konfigurieren einer Richtlinie für eine vorherige Version

Bei aktivierter Versionsverwaltung wird mit einem Schreib- oder Löschvorgang für ein Blob eine neue vorherige Version des Blobs erstellt, mit der auch der Zustand des Blobs vor dem Vorgang gespeichert wird. Standardmäßig beinhaltet eine vorherige Version die zeitbasierte Aufbewahrungsrichtlinie, die gegebenenfalls für die aktuelle Version zu dem Zeitpunkt gültig war, zu dem die aktuelle Version zu einer vorherigen Version wurde. Die neue aktuelle Version erbt die Richtlinie für den Container, sofern vorhanden.

Wenn die von einer vorherigen Version geerbte Richtlinie entsperrt ist, kann der Aufbewahrungszeitraum verkürzt oder verlängert oder die Richtlinie gelöscht werden. Die Richtlinie für eine vorherige Version kann für diese Version auch gesperrt werden, selbst wenn die Richtlinie für die aktuelle Version entsperrt ist.

Wenn die von einer vorherigen Version geerbte Richtlinie gesperrt ist, kann der Aufbewahrungszeitraum verlängert werden. Die Richtlinie kann weder gelöscht noch kann der Aufbewahrungszeitraum verkürzt werden.

Wenn für die aktuelle Version keine Richtlinie konfiguriert ist, erbt die vorherige Version keine Richtlinie. Sie können eine benutzerdefinierte Richtlinie für die Version konfigurieren.  

Wenn die Richtlinie für eine aktuelle Version geändert wird, bleiben die Richtlinien für vorhandene vorherige Versionen unverändert, auch wenn die Richtlinie von einer aktuellen Version geerbt wurde.

### <a name="container-level-policy-scope"></a>Richtlinienbereich von Richtlinien auf Containerebene

Eine zeitbasierte Aufbewahrungsrichtlinie auf Containerebene gilt für alle Objekte in einem Container, d. h. für neue sowie vorhandene Objekte. Bei einem Konto mit einem hierarchischen Namespace gilt eine Richtlinie auf Containerebene auch für alle Verzeichnisse im Container.

Wenn eine zeitbasierte Aufbewahrungsrichtlinie auf einen Container angewandt wird, werden alle vorhandenen Blobs innerhalb von weniger als 30 Sekunden in einen unveränderlichen WORM-Zustand versetzt. Alle neuen Blobs, die in den durch die Richtlinie geschützten Container hochgeladen werden, werden ebenfalls in einen unveränderlichen Zustand versetzt. Wenn sich alle Blobs in einem unveränderlichen Zustand befinden, sind Überschreibungs- oder Löschvorgänge im unveränderlichen Container unzulässig. Bei einem Konto mit einem hierarchischen Namespace können Blobs nicht umbenannt oder in ein anderes Verzeichnis verschoben werden.

Für Aufbewahrungsrichtlinien auf Containerebene gelten folgende Grenzwerte:

- Ein Speicherkonto kann über maximal 10.000 Container mit gesperrten zeitbasierten Unveränderlichkeitsrichtlinien verfügen.
- Bei einem Container sind höchstens fünf Bearbeitungen zur Verlängerung des Aufbewahrungszeitraums für eine gesperrte zeitbasierte Richtlinie möglich.
- Für einen Container werden maximal sieben Überwachungsprotokolle der zeitbasierten Aufbewahrungsrichtlinie für eine gesperrte Richtlinie aufbewahrt.

Informationen zum Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für einen Container finden Sie unter [Konfigurieren von Unveränderlichkeitsrichtlinien für Container](immutable-policy-configure-container-scope.md).

## <a name="allow-protected-append-blobs-writes"></a>Zulassen von Schreibvorgängen in geschützten Anfügeblobs

Anfügeblobs bestehen aus Datenblöcken und sind für Vorgänge zum Anfügen von Daten optimiert, die in Überprüfungs- und Protokollierungsszenarien erforderlich sind. Bei Anfügeblobs können neue Blöcke entwurfsbedingt nur am Ende des Blobs hinzugefügt werden. Unabhängig von der Unveränderlichkeit ist das Ändern oder Löschen vorhandener Blöcke in einem Anfügeblob grundsätzlich nicht zulässig. Weitere Informationen zu Anfügeblobs finden Sie unter [Informationen zu Anfügeblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Nur zeitbasierte Aufbewahrungsrichtlinien enthalten die **AllowProtectedAppendWrites**-Eigenschaft, über die das Schreiben neuer Blöcke in ein Anfügeblob ermöglicht wird und gleichzeitig der Unveränderlichkeitsschutz und die Konformität aufrechterhalten werden. Wenn diese Einstellung aktiviert ist, können Sie ein Anfügeblob direkt in dem durch Richtlinien geschützten Container erstellen und über den Vorgang „Append Block“ weitere neue Datenblöcke am Ende des Anfügeblobs hinzufügen. Es können nur neue Blöcke hinzugefügt werden. Vorhandene Blöcke können nicht geändert oder gelöscht werden. Der Unveränderlichkeitsschutz für die Aufbewahrungszeit gilt weiterhin und verhindert die Löschung des Anfügeblobs bis der geltende Aufbewahrungszeitraum abgelaufen ist. Die Aktivierung dieser Einstellung wirkt sich nicht auf das Unveränderlichkeitsverhalten von Blockblobs oder Seitenblobs aus.

Da diese Einstellung zu einer zeitbasierten Aufbewahrungsrichtlinie gehört, bleiben die Anfügeblobs so lange im unveränderlichen Zustand, wie der Aufbewahrungszeitraum *gilt*. Da neue Daten auch nach der anfänglichen Erstellung des Anfügeblobs angefügt werden können, gibt es bei der Festlegung des Aufbewahrungszeitraums einen geringfügigen Unterschied. Die Gültigkeit des Aufbewahrungszeitraums entspricht der Differenz zwischen dem Zeitpunkt der letzten Änderung des Blobs und dem vom Benutzer angegebenen Aufbewahrungszeitraum. Entsprechend wird beim Verlängern des Aufbewahrungszeitraums für den unveränderlichen Speicher der letzte Wert des vom Benutzer angegebenen Aufbewahrungszeitraums verwendet, um den effektiven Aufbewahrungszeitraum zu berechnen.

Ein Beispiel: Angenommen, ein Benutzer erstellt eine zeitbasierte Aufbewahrungsrichtlinie mit aktivierter **AllowProtectedAppendWrites**-Eigenschaft und einem Aufbewahrungszeitraum von 90 Tagen. Ein Anfügeblob (_logblob1_) wird am aktuellen Datum im Container erstellt. Dem Anfügeblob werden während der nächsten 10 Tage weiterhin neue Protokolle hinzugefügt. Der effektive Aufbewahrungszeitraum für _logblob1_ beträgt also 100 Tage ab dem aktuellen Datum (Zeitpunkt der letzten Anfügung + 90 Tage).

Bei entsperrten zeitbasierten Aufbewahrungsrichtlinien kann die Einstellung der **AllowProtectedAppendWrites**-Eigenschaft zu jedem Zeitpunkt aktiviert und deaktiviert werden. Nachdem die zeitbasierte Richtlinie gesperrt wurde, kann die **AllowProtectedAppendWrites**-Eigenschaft nicht mehr geändert werden.

## <a name="audit-logging"></a>Überwachungsprotokollierung

Jeder Container mit aktivierter zeitbasierter Aufbewahrungsrichtlinie umfasst ein Richtlinienüberwachungsprotokoll. Das Überwachungsprotokoll enthält bis zu sieben zeitbasierte Aufbewahrungsbefehle für gesperrte zeitbasierte Aufbewahrungsrichtlinien. Protokolleinträge enthalten Benutzer-ID, Befehlstyp, Zeitstempel und Aufbewahrungszeitraum. Das Überwachungsprotokoll wird für die Lebensdauer der Richtlinie gemäß den Richtlinien der Verordnung SEC 17a-4(f) aufbewahrt.

Das [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/platform-logs-overview.md) ist ein umfassenderes Protokoll mit allen Verwaltungsdienstaktivitäten. [Azure-Ressourcenprotokolle](../../azure-monitor/essentials/platform-logs-overview.md) enthalten Informationen zu Datenvorgängen. Der Benutzer ist für die dauerhafte Speicherung dieser Protokolle verantwortlich, die aus gesetzlichen oder anderen Gründen ggf. erforderlich ist.

Änderungen an zeitbasierten Aufbewahrungsrichtlinien auf Versionsebene werden nicht überwacht.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht zum Datenschutz](data-protection-overview.md)
- [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md)
- [Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten](immutable-legal-hold-overview.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Container](immutable-policy-configure-container-scope.md)
