---
title: Übersicht zum Datenschutz
titleSuffix: Azure Storage
description: Die für Ihre Daten verfügbaren Datenschutzoptionen für Blob Storage und Azure Data Lake Storage Gen2 ermöglichen es Ihnen, Ihre Daten vor dem Löschen oder Überschreiben zu schützen. Wenn Sie gelöschte oder überschriebene Daten wiederherstellen müssen, kann dieser Leitfaden Ihnen helfen, die für Ihr Szenario beste Wiederherstellungsoption zu wählen.
services: storage
author: tamram
ms.service: storage
ms.date: 07/30/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: d9beb1ce981826eb7555b1a55e00c7c00fb84810
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766614"
---
# <a name="data-protection-overview"></a>Übersicht zum Datenschutz

Azure Storage bietet Datenschutz für Blob Storage und Azure Data Lake Storage Gen2, um Sie auf Szenarien vorzubereiten, in denen Sie Daten wiederherstellen müssen, die gelöscht oder überschrieben wurden. Es ist wichtig, darüber nachzudenken, wie Sie Ihre Daten am besten schützen können, bevor ein Vorfall eintritt, der sie gefährden könnte. Dieser Leitfaden kann Ihnen helfen, im Voraus zu entscheiden, welche Features für den Datenschutz Ihr Szenario erfordert und wie Sie diese implementieren können. Sollten Sie gelöschte oder überschriebene Daten wiederherstellen müssen, finden Sie in dieser Übersicht auch eine Anleitung, wie Sie je nach Szenario vorgehen können.

In der Azure Storage-Dokumentation bezieht sich *Datenschutz* auf Strategien zum Schutz des Speicherkontos und der darin enthaltenen Daten vor Löschung oder Änderung oder zur Wiederherstellung von Daten, nachdem sie gelöscht oder geändert wurden. Azure Storage bietet auch Optionen für die *Notfallwiederherstellung*, einschließlich mehrerer Redundanzebenen, um Ihre Daten vor Dienstausfällen aufgrund von Hardwareproblemen oder Naturkatastrophen zu schützen, sowie ein vom Kunden verwalteter Failover für den Fall, dass das Rechenzentrum in der primären Region nicht mehr verfügbar ist. Weitere Informationen darüber, wie Ihre Daten vor Dienstausfällen geschützt werden, finden Sie unter [Notfallwiederherstellung](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Empfehlungen für den grundlegenden Schutz von Daten

Wenn Sie einen grundlegenden Schutz von Daten für Ihr Speicherkonto und die darin enthaltenen Daten suchen, dann empfiehlt Microsoft, zunächst die folgenden Schritte durchzuführen:

- Konfigurieren Sie eine Azure Resource Manager-Sperre für das Speicherkonto, um das Konto vor Löschung oder Konfigurationsänderungen zu schützen. [Weitere Informationen](../common/lock-account-resource.md)
- Aktivieren Sie das vorläufige Löschen von Containern für das Speicherkonto, um einen gelöschten Container und dessen Inhalt wiederherzustellen. [Weitere Informationen](soft-delete-container-enable.md)
- Speichern Sie den Zustand eines Blobs in regelmäßigen Abständen:
  - Aktivieren Sie für Blob Storage-Workloads die Blobversionsverwaltung, um den Zustand Ihrer Daten jedes Mal automatisch zu speichern, wenn ein Blob überschrieben wird. [Weitere Informationen](versioning-enable.md)
  - Für Azure Data Lake Storage-Workloads können Sie manuelle Momentaufnahmen erstellen, um den Zustand Ihrer Daten zu einem bestimmten Zeitpunkt zu speichern. [Weitere Informationen](snapshots-overview.md)

Diese Optionen sowie weitere Möglichkeiten der Datensicherung für andere Szenarien werden im folgenden Abschnitt näher beschrieben.

Eine Übersicht über die mit diesen Funktionen verbundenen Kosten finden Sie unter [Zusammenfassung der Überlegungen zu Kosten](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Übersicht über Optionen für den Schutz von Daten

In der folgenden Tabelle werden die Optionen zusammengefasst, die in Azure Storage für allgemeine Szenarien zum Schutz von Daten verfügbar sind. Wählen Sie die Szenarien aus, die auf Ihre Situation zutreffen, um mehr über die Ihnen zur Verfügung stehenden Optionen zu erfahren. Beachten Sie, dass derzeit nicht alle Features für Speicherkonten mit aktiviertem hierarchischen Namespace verfügbar sind.

| Szenario | Datenschutzoption | Empfehlungen | Schutzvorteil | Verfügbar für Data Lake Storage |
|--|--|--|--|--|
| Verhindern Sie, dass ein Speicherkonto gelöscht oder geändert wird. | Azure Resource Manager-Sperre<br />[Weitere Informationen](../common/lock-account-resource.md) | Sperren Sie alle Ihre Speicherkonten mit einer Azure Resource Manager-Sperre, um eine Löschung des Speicherkontos zu verhindern. | Schützt das Speicherkonto vor Löschung oder Konfigurationsänderungen.<br /><br />Schützt Container oder Blobs im Konto nicht vor dem Löschen oder Überschreiben. | Ja |
| Verhindern Sie, dass eine Blobversion für einen von Ihnen festgelegten Zeitraum gelöscht wird. | Unveränderlichkeitsrichtlinie für eine Blobversion (Vorschau)<br />[Weitere Informationen](immutable-storage-overview.md) | Legen Sie eine Unveränderlichkeitsrichtlinie für eine einzelne Blobversion fest, um unternehmenskritische Dokumente zu schützen, z. B. um rechtliche oder gesetzliche Complianceanforderungen zu erfüllen. | Schützt eine Blobversion vor dem Löschen und deren Metadaten vor dem Überschreiben. Bei einem Überschreibvorgang wird eine neue Version erstellt.<br /><br />Wenn für mindestens einen Container Unveränderlichkeit auf Versionsebene aktiviert ist, ist auch das Speicherkonto vor dem Löschen geschützt. Das Löschen des Containers schlägt fehl, wenn mindestens ein Blob im Container vorhanden ist. | Nein |
| Verhindern Sie, dass ein Container und seine Blobs für ein von Ihnen festgelegtes Intervall gelöscht oder geändert werden. | Unveränderlichkeitsrichtlinie für einen Container<br />[Weitere Informationen](immutable-storage-overview.md) | Legen Sie eine Unveränderlichkeitsrichtlinie für einen Container fest, um unternehmenskritische Dokumente zu schützen, z. B. um rechtliche oder gesetzliche Complianceanforderungen zu erfüllen. | Schützt einen Container und seine Blobs vor allen Lösch- und Überschreibvorgängen.<br /><br />Wenn eine gesetzliche Aufbewahrungspflicht oder eine gesperrte zeitbasierte Aufbewahrungsrichtlinie in Kraft ist, ist das Speicherkonto ebenfalls vor dem Löschen geschützt. Container, für die keine Unveränderlichkeitsrichtlinie festgelegt wurde, sind nicht vor dem Löschen geschützt. | Ja, in der Vorschauversion |
| Stellen Sie einen gelöschten Container innerhalb eines angegebenen Intervalls wieder her. | Vorläufiges Löschen von Containern<br />[Weitere Informationen](soft-delete-container-overview.md) | Aktivieren Sie das vorläufige Löschen von Containern für alle Speicherkonten, mit einem Mindestaufbewahrungszeitraum von sieben Tagen.<br /><br />Aktivieren Sie die Blobversionsverwaltung und das vorläufige Löschen von Blobs zusammen mit dem vorläufigen Löschen von Containern, um einzelne Blobs in einem Container zu schützen.<br /><br />Speichern Sie Container, die unterschiedliche Aufbewahrungszeiträume benötigen, in separaten Speicherkonten. | Ein gelöschter Container und sein Inhalt können innerhalb des Aufbewahrungszeitraums wiederhergestellt werden.<br /><br />Nur Vorgänge auf Containerebene (z. B. [Container löschen](/rest/api/storageservices/delete-container)) können wiederhergestellt werden. Das vorläufige Löschen von Containern ermöglicht es Ihnen nicht, ein einzelnes Blob im Container wiederherzustellen, wenn dieses gelöscht wurde. | Ja |
| Speichern Sie den Zustand eines Blobs automatisch in einer früheren Version, wenn er überschrieben wird. | Blobversionsverwaltung<br />[Weitere Informationen](versioning-overview.md) | Aktivieren Sie die Blobversionsverwaltung zusammen mit dem vorläufigen Löschen für Container und dem vorläufigen Löschen von Blobs für Speicherkonten, bei denen Sie einen optimalen Schutz für Blobdaten benötigen.<br /><br />Speichern Sie Blobdaten, die keine Versionsverwaltung benötigen, in einem separaten Konto, um die Kosten zu begrenzen. | Bei jedem Blobschreibvorgang wird eine neue Version erstellt. Die aktuelle Version eines Blobs kann aus einer früheren Version wiederhergestellt werden, wenn die aktuelle Version gelöscht oder überschrieben wurde. | Nein |
| Stellen Sie ein gelöschtes Blob oder eine Blobversion innerhalb eines bestimmten Intervalls wieder her. | Vorläufiges Löschen von Blobs<br />[Weitere Informationen](soft-delete-blob-overview.md) | Aktivieren Sie das vorläufige Löschen von Blobs für alle Speicherkonten, mit einem Mindestaufbewahrungszeitraum von sieben Tagen.<br /><br />Aktivieren Sie die Blobversionsverwaltung und das vorläufige Löschen von Containern zusammen mit dem vorläufigen Löschen von Blobdaten für einen optimalen Schutz der Blobdaten.<br /><br />Speichern Sie Blobs, die unterschiedliche Aufbewahrungszeiträume benötigen, in separaten Speicherkonten. | Ein gelöschtes Blob oder eine gelöschte Blobversion kann innerhalb des Aufbewahrungszeitraums wiederhergestellt werden. | Ja, in der Vorschauversion |
| Stellen Sie einen Satz von Blockblobs zu einem früheren Zeitpunkt wieder her. | Wiederherstellung bis zu einem bestimmten Zeitpunkt<br />[Weitere Informationen](point-in-time-restore-overview.md) | Wenn Sie die Zeitpunktwiederherstellung verwenden möchten, um zu einem früheren Zustand zurückzukehren, entwerfen Sie Ihre Anwendung so, dass sie anstelle von Containern einzelne Blockblobs löscht. | Ein Satz von Blockblobs kann in den Zustand zu einem bestimmten Zeitpunkt in der Vergangenheit zurückversetzt werden.<br /><br />Nur an Blockblobs vorgenommene Vorgänge werden rückgängig gemacht. Alle an Containern, Seitenblobs oder Anfügeblobs durchgeführten Vorgänge werden nicht rückgängig gemacht. | Nein |
| Speichern Sie den Zustand eines Blobs zu einem bestimmten Zeitpunkt manuell. | Momentaufnahme eines Blobs<br />[Weitere Informationen](snapshots-overview.md) | Empfohlen als Alternative zur Blobversionsverwaltung, wenn die Versionsverwaltung aus Kosten- oder anderen Gründen für Ihr Szenario nicht geeignet ist, oder wenn das Speicherkonto einen hierarchischen Namespace aktiviert hat. | Ein Blob kann aus einem Momentaufnahme wiederhergestellt werden, wenn das Blob überschrieben wird. Wenn das Blob gelöscht wird, werden auch die Momentaufnahmen gelöscht. | Ja, in der Vorschauversion |
| Ein Blob kann gelöscht oder überschrieben werden, aber die Daten werden regelmäßig in ein zweites Speicherkonto kopiert. | Entwickeln Sie Ihre eigene Lösung zum Kopieren von Daten auf ein zweites Konto unter Verwendung der Azure Storage-Objektreplikation oder eines Tools wie AzCopy oder Azure Data Factory. | Empfohlen zum Schutz vor unerwarteten vorsätzlichen Aktionen oder unvorhersehbaren Szenarien.<br /><br />Erstellen Sie das zweite Speicherkonto in derselben Region wie das primäre Konto, um Gebühren für ausgehenden Datenverkehr zu vermeiden. | Daten können vom zweiten Speicherkonto wiederhergestellt werden, wenn das primäre Konto in irgendeiner Weise gefährdet wird. | AzCopy und Azure Data Factory werden unterstützt.<br /><br />Die Objektreplikation wird nicht unterstützt. |

## <a name="data-protection-by-resource-type"></a>Datenschutz nach Ressourcentyp

Die folgende Tabelle fasst die Azure Storage-Optionen zum Schutz von Daten nach den Ressourcen zusammen, die sie schützen.

| Datenschutzoption | Schützt ein Konto vor dem Löschen | Schützt einen Container vor dem Löschen | Schützt ein Objekt vor dem Löschen | Schützt ein Objekt vor Überschreibung |
|--|--|--|--|--|
| Azure Resource Manager-Sperre | Ja | Nein<sup>1</sup> | Nein | Nein |
| Unveränderlichkeitsrichtlinie für eine Blobversion (Vorschau) | Ja<sup>2</sup> | Ja<sup>3</sup> | Ja | Ja<sup>4</sup> |
| Unveränderlichkeitsrichtlinie für einen Container | Ja<sup>5</sup> | Ja | Ja | Ja |
| Vorläufiges Löschen von Containern | Nein | Ja | Nein | Nein |
| Blobversionsverwaltung<sup>6</sup> | Nein | Nein | Ja | Ja |
| Vorläufiges Löschen von Blobs | Nein | Nein | Ja | Ja |
| Zeitpunktwiederherstellung<sup>6</sup> | Nein | Nein | Ja | Ja |
| Momentaufnahme eines Blobs | Nein | Nein | Nein | Ja |
| Eigene Lösung für das Kopieren von Daten in ein zweites Konto<sup>7</sup> | Nein | Ja | Ja | Ja |

<sup>1</sup> Eine Azure Resource Manager-Sperre schützt einen Container nicht vor dem Löschen.<br />
<sup>2</sup> Speicherkontolöschung schlägt fehl, wenn mindestens ein Container mit aktiviertem unveränderlichen Speicher auf Versionsebene vorhanden ist.<br />
<sup>3</sup> Beim Löschen des Containers tritt ein Fehler auf, wenn mindestens ein Blob im Container vorhanden ist (unabhängig davon, ob die Richtlinie gesperrt ist oder nicht).<br />
<sup>4</sup> Beim Überschreiben des Inhalts der aktuellen Version des Blobs wird eine neue Version erstellt. Eine Unveränderlichkeitsrichtlinie schützt die Metadaten einer Version vor dem Überschreiben.<br />
<sup>5</sup> Solange eine gesetzliche Aufbewahrungspflicht oder eine gesperrte zeitbasierte Aufbewahrungsrichtlinie im Containerbereich in Kraft ist, ist das Speicherkonto ebenfalls vor dem Löschen geschützt.<br />
<sup>6</sup> Wird derzeit nicht für Data Lake Storage-Workloads unterstützt.<br />
<sup>7</sup> AzCopy und Azure Data Factory sind Optionen, die sowohl für Blob Storage- als auch für Data Lake Storage-Workloads unterstützt werden. Die Objektreplikation wird nur für Blob Storage-Workloads unterstützt.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Wiederherstellen gelöschter oder überschriebener Daten

Sollten Sie Daten wiederherstellen müssen, die überschrieben oder gelöscht wurden, hängt die Vorgehensweise davon ab, welche Optionen für den Schutz von Daten Sie aktiviert haben und welche Ressource betroffen war. In der folgenden Tabelle werden die Aktionen zur Wiederherstellung von Daten beschrieben.

| Gelöschte oder überschriebene Ressource | Mögliche Wiederherstellungsaktionen | Anforderungen für die Wiederherstellung |
|--|--|--|
| Speicherkonto | Versuch der Wiederherstellung des gelöschten Speicherkontos<br />[Weitere Informationen](../common/storage-account-recover.md) | Das Speicherkonto wurde ursprünglich mit dem Azure Resource Manager-Bereitstellungsmodell erstellt und wurde innerhalb der letzten 14 Tage gelöscht. Seit dem Löschen des ursprünglichen Kontos wurde kein neues Speicherkonto mit demselben Namen erstellt. |
| Container | Wiederherstellung des vorläufig gelöschten Containers und seiner Inhalte<br />[Weitere Informationen](soft-delete-container-enable.md) | Das vorläufige Löschen von Containern ist aktiviert und der Aufbewahrungszeitraum für das vorläufige Löschen von Containern ist noch nicht abgelaufen. |
| Container und Blobs | Wiederherstellung von Daten aus einem zweiten Speicherkonto | Alle Container- und Blobvorgänge wurden effektiv auf ein zweites Speicherkonto repliziert. |
| Blob (beliebiger Typ) | Wiederherstellung eines Blobs aus einer früheren Version<sup>1</sup><br />[Weitere Informationen](versioning-enable.md) | Die Blobversionsverwaltung ist aktiviert und das Blob besitzt eine oder mehrere vorherige Versionen. |
| Blob (beliebiger Typ) | Wiederherstellung eines vorläufig gelöschten Blobs<br />[Weitere Informationen](soft-delete-blob-enable.md) | Das vorläufige Löschen von Blobs ist aktiviert, und der Aufbewahrungszeitraum für das vorläufige Löschen ist nicht abgelaufen. |
| Blob (beliebiger Typ) | Wiederherstellung eines Blobs aus einer Momentaufnahme<br />[Weitere Informationen](snapshots-manage-dotnet.md) | Das Blob verfügt über mindestens eine Momentaufnahme. |
| Satz von Blockblobs | Wiederherstellung eines Satzes von Blockblobs in den Zustand zu einem früheren Zeitpunkt<sup>1</sup><br />[Weitere Informationen](point-in-time-restore-manage.md) | Die Zeitpunktwiederherstellung ist aktiviert und der Wiederherstellungspunkt liegt innerhalb des Aufbewahrungszeitraums. Das Speicherkonto wurde nicht gefährdet oder beschädigt. |
| Blobversion | Wiederherstellung einer vorläufig gelöschten Version<sup>1</sup><br />[Weitere Informationen](soft-delete-blob-enable.md) | Das vorläufige Löschen von Blobs ist aktiviert, und der Aufbewahrungszeitraum für das vorläufige Löschen ist nicht abgelaufen. |

<sup>1</sup> Wird derzeit nicht für Data Lake Storage-Workloads unterstützt.

## <a name="summary-of-cost-considerations"></a>Zusammenfassung der Überlegungen zu Kosten

In der folgenden Tabelle werden die Kostenüberlegungen für die verschiedenen in diesem Leitfaden beschriebenen Optionen zum Schutz von Daten zusammengefasst.

| Datenschutzoption | Kostenbetrachtung |
|-|-|
| Azure Resource Manager-Sperre für ein Speicherkonto | Keine Gebühren für die Konfiguration einer Sperre für ein Speicherkonto |
| Unveränderlichkeitsrichtlinie für eine Blobversion (Vorschau) | Keine Gebühren für das Aktivieren der Unveränderlichkeit für einen Container auf Versionsebene. Das Erstellen, Ändern oder Löschen einer zeitbasierten Aufbewahrungsrichtlinie oder einer rechtlichen Aufbewahrungspflicht für eine Blobversion führt zu einer Gebühr für Schreibtransaktionen. |
| Unveränderlichkeitsrichtlinie für einen Container | Keine Kosten für die Konfiguration einer Unveränderlichkeitsrichtlinie für einen Container |
| Vorläufiges Löschen von Containern | Keine Kosten für das Aktivieren des vorläufigen Löschens von Containern für ein Speicherkonto Daten in einem vorläufig gelöschten Container werden zum gleichen Tarif wie aktive Daten abgerechnet, bis der vorläufig gelöschte Container endgültig gelöscht wird. |
| Blobversionsverwaltung | Keine Gebühren für die Aktivierung der Blobversionsverwaltung für ein Speicherkonto Nachdem die Blobversionsverwaltung aktiviert wurde, wird bei jedem Schreib- oder Löschvorgang für ein Blob im Konto eine neue Version erstellt, was zu erhöhten Kapazitätskosten führen kann.<br /><br />Eine Blobversion wird auf der Basis von einzelnen Blöcken oder Seiten abgerechnet. Die Kosten steigen also, wenn das Basisblob von einer bestimmten Version abweicht. Das Ändern der Ebene eines Blob oder einer Blobversion kann Auswirkungen auf die Abrechnung haben. Weitere Informationen finden Sie unter [Preise und Abrechnung](versioning-overview.md#pricing-and-billing).<br /><br />Verwenden Sie die Lebenszyklusverwaltung, um ältere Versionen bei Bedarf zu löschen und so die Kosten zu kontrollieren. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](./lifecycle-management-overview.md). |
| Vorläufiges Löschen von Blobs | Keine Kosten für das Aktivieren des vorläufigen Löschens von Blobs für ein Speicherkonto Daten in einem vorläufig gelöschten Blob werden zum gleichen Tarif wie aktive Daten abgerechnet, bis das vorläufig gelöschte Blob endgültig gelöscht wird. |
| Wiederherstellung bis zu einem bestimmten Zeitpunkt | Die Aktivierung der Zeitpunktwiederherstellung für ein Speicherkonto ist kostenlos. Allerdings werden durch die Aktivierung der Zeitpunktwiederherstellung auch die Blobversionsverwaltung, das vorläufige Löschen und der Änderungsfeed aktiviert, was jeweils zu zusätzlichen Kosten führen kann.<br /><br />Die Zeitpunktwiederherstellung wird Ihnen in Rechnung gestellt, wenn Sie einen Wiederherstellungsvorgang durchführen. Die Kosten für einen Wiederherstellungsvorgang hängen von der Menge der wiederherzustellenden Daten ab. Weitere Informationen finden Sie unter [Preise und Abrechnung](point-in-time-restore-overview.md#pricing-and-billing). |
| Blobmomentaufnahmen | Die Daten in einer Momentaufnahme werden auf der Basis eindeutiger Blöcke oder Seiten abgerechnet. Die Kosten steigen also, wenn das Basisblob von der Momentaufnahme abweicht. Das Ändern der Ebene eines Blobs oder einer Momentaufnahme kann sich auf die Abrechnung auswirken. Weitere Informationen finden Sie unter [Preise und Abrechnung](snapshots-overview.md#pricing-and-billing).<br /><br />Verwenden Sie die Lebenszyklusverwaltung, um ältere Momentaufnahmen bei Bedarf zu löschen und so die Kosten zu kontrollieren. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](./lifecycle-management-overview.md). |
| Kopieren von Daten in ein zweites Speicherkonto | Die Verwaltung von Daten in einem zweiten Speicherkonto verursacht Kapazitäts- und Transaktionskosten. Wenn sich das zweite Speicherkonto in einer anderen Region als das Quellkonto befindet, fallen beim Kopieren von Daten auf dieses zweite Konto zusätzlich Gebühren für ausgehenden Datenverkehr an. |

## <a name="disaster-recovery"></a>Notfallwiederherstellung

Azure Storage verwaltet immer mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern über Netzwerk- oder Stromausfälle bis hin zu schweren Naturkatastrophen. Redundanz stellt sicher, dass Ihr Speicherkonto seine Ziele für Verfügbarkeit und Dauerhaftigkeit selbst bei Ausfällen erfüllt. Weitere Informationen zur Konfiguration Ihres Speicherkontos für Hochverfügbarkeit finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).

Wenn Ihr Speicherkonto im Falle eines Ausfalls in einem Rechenzentrum über zwei geografische Regionen hinweg redundant ist (georedundant), haben Sie die Möglichkeit, einen Failover Ihres Kontos von der primären Region auf die sekundäre Region auszuführen. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Failover des Speicherkontos](../common/storage-disaster-recovery-guidance.md).

Ein kundenseitig verwalteter Failover wird derzeit für Speicherkonten nicht unterstützt, bei denen ein hierarchischer Namespace aktiviert ist. Weitere Informationen finden Sie unter [Verfügbare Blob Storage-Features in Azure Data Lake Storage Gen2](./storage-feature-support-in-storage-accounts.md).

## <a name="next-steps"></a>Nächste Schritte

- [Azure-Speicherredundanz](../common/storage-redundancy.md)
- [Notfallwiederherstellung und Speicherkontofailover](../common/storage-disaster-recovery-guidance.md)