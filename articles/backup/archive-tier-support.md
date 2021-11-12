---
title: Azure Backup – Übersicht über die Archivebene
description: Informationen zur Unterstützung des Archiv-Tarifs für Azure Backup.
ms.topic: overview
ms.date: 10/23/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9712b198e8be9a7a90699cc6ce544a627bfdea63
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510357"
---
# <a name="overview-of-archive-tier-in-azure-backup"></a>Übersicht über die Archivebene in Azure Backup

Kunden setzen zum Speichern von Sicherungsdaten, darunter auch Sicherungsdaten für die langfristige Aufbewahrung, auf Azure Backup. Die entsprechenden Anforderungen an die Aufbewahrungsdauer werden in den Konformitätsregeln des jeweiligen Unternehmens definiert. In den meisten Fällen erfolgt nur selten ein Zugriff auf die älteren Sicherungsdaten, die ausschließlich zu Konformitätszwecken gespeichert werden.

Azure Backup unterstützt neben Momentaufnahmen und dem Standard-Tarif auch die Sicherung von Wiederherstellungspunkten für die langfristige Aufbewahrung im Archiv-Tarif.

## <a name="supported-workloads"></a>Unterstützte Workloads

Der Archiv-Tarif unterstützt die folgenden Workloads:

| Arbeitsauslastungen | Operations |
| --- | --- |
| Virtuelle Azure-Computer | <ul><li>Nur monatliche und jährliche Wiederherstellungspunkte. Tägliche und wöchentliche Wiederherstellungspunkte werden nicht unterstützt.  </li><li>Alter: mindestens drei (3) Monate auf der Tresorstandardebene </li><li>Restliche Aufbewahrungsdauer: mindestens sechs (6) Monate </li><li>Keine aktiven täglichen und wöchentlichen Abhängigkeiten. </li></ul> |
| SQL Server in Azure Virtual Machines/SAP HANA in Azure Virtual Machines | <ul><li>Nur vollständige Wiederherstellungspunkte. Protokolle und differenzielle Sicherungen werden nicht unterstützt. </li><li>Alter: mindestens 45 Tage im Vault-Standard-Tarif </li><li>Restliche Aufbewahrungsdauer: mindestens sechs (6) Monate </li><li>Keine Abhängigkeiten </li></ul> |

>[!Note]
>- Die Unterstützung des Archiv-Tarifs für SQL Server in Azure Virtual Machines und SAP HANA in Azure Virtual Machines ist ab sofort in vielen Regionen allgemein verfügbar. Eine ausführliche Liste der unterstützten Regionen finden Sie in der [Supportmatrix](#support-matrix).
>- Die Unterstützung der Zugriffsebene „Archiv“ für Azure Virtual Machines befindet sich ebenfalls in der eingeschränkten öffentlichen Vorschau. Füllen Sie [dieses Formular aus](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u), um sich für die eingeschränkte öffentliche Vorschauversion zu registrieren.

## <a name="supported-clients"></a>Unterstützte Clients

Der Archiv-Tarif unterstützt die folgenden Clients:

- [PowerShell](/azure/backup/use-archive-tier-support?pivots=client-powershelltier)
- [BEFEHLSZEILENSCHNITTSTELLE (CLI)](/azure/backup/use-archive-tier-support?pivots=client-clitier)
- [Azure-Portal](/azure/backup/use-archive-tier-support?pivots=client-portaltier)

## <a name="how-azure-backup-moves-recovery-points-to-the-vault-archive-tier"></a>Wie verschiebt Azure Backup Wiederherstellungspunkte in den Vault-Archiv-Tarif?

> [!VIDEO https://www.youtube.com/embed/nQnH5mpiz60?start=416]

## <a name="archive-recommendations-only-for-azure-virtual-machines"></a>Archivempfehlungen (nur für Azure Virtual Machines)

Die Wiederherstellungspunkte für Azure Virtual Machines sind inkrementell. Wenn Sie Wiederherstellungspunkte in den Archiv-Tarif verschieben, werden sie in vollständige Wiederherstellungspunkte konvertiert (um sicherzustellen, dass alle Wiederherstellungspunkte im Archiv-Tarif voneinander unabhängig und isoliert sind). Daher kann der gesamte Sicherungsspeicher (Vault-Standard + Vault-Archiv) größer werden.

Die Zunahme der Speichergröße hängt vom Änderungsmuster der virtuellen Computer ab.

- Je höher die Änderungsrate der virtuellen Computer ist, desto kleiner ist der gesamte Sicherungsspeicher, wenn ein Wiederherstellungspunkt in den Archiv-Tarif verschoben wird.
- Wenn die Änderungsrate der virtuellen Computer gering ist, kann die Verschiebung in den Archiv-Tarif zu einer Vergrößerung des Sicherungsspeichers führen. Dies kann den Preisunterschied zwischen dem Vault-Standard- und dem Vault-Archiv-Tarif aufrechnen. Dadurch können die Gesamtkosten steigen.

Um dies zu vermeiden, stellt Azure Backup einen Empfehlungssatz bereit. Der Empfehlungssatz gibt eine Liste von Wiederherstellungspunkten zurück, die Kosteneinsparungen sicherstellen, wenn sie zusammen in den Archiv-Tarif verschoben werden.

>[!Note]
>Die Kosteneinsparungen hängen von verschiedenen Faktoren ab und können sich für die einzelnen Instanzen unterschiedlich sein.

## <a name="modify-protection"></a>Ändern des Schutzes

Azure Backup bietet zwei Möglichkeiten, den Schutz für eine Datenquelle zu ändern:

- Bearbeiten einer vorhandenen Richtlinie
- Schützen der Datenquelle durch eine neue Richtlinie

In beiden Szenarien wird die neue Richtlinie auf alle älteren Wiederherstellungspunkte, die sich im Standard- und Archiv-Tarifbefinden, angewendet. Möglicherweise werden ältere Wiederherstellungspunkte gelöscht, wenn die Richtlinie geändert wird.

Für Wiederherstellungspunkte, die Sie ins Archiv verschieben, fällt für einen Zeitraum von 180 Tagen eine Gebühr für vorzeitiges Löschen an. Die Gebühren werden anteilmäßig abgerechnet. Beim Löschen eines Wiederherstellungspunkts, der sich keine 180 Tage im Archiv befunden hat, werden Kosten für 180 Tage abzüglich der Anzahl der Tage im Standard-Tarif in Rechnung gestellt.

Beim Löschen von Wiederherstellungspunkten, die sich nicht mindestens 180 Tage im Archiv befunden haben, wird eine Gebühr für vorzeitiges Löschen fällig.

## <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

Durch das Beenden des Schutzes und das Löschen der Daten werden alle Wiederherstellungspunkte gelöscht. Bei Wiederherstellungspunkten im Archiv, die sich keine 180 Tage im Archiv-Tarif befunden haben, verursacht ein Löschvorgang Kosten für vorzeitiges Löschen.

## <a name="archive-tier-pricing"></a>Archiv-Tarif – Preise

Die Preise für den Archiv-Tarif finden Sie auf unserer [Preisseite](azure-backup-pricing.md).

## <a name="support-matrix"></a>Unterstützungsmatrix

| Arbeitsauslastungen | Vorschau | Allgemein verfügbar |
| --- | --- | --- |
| SQL Server in Azure Virtual Machines/SAP HANA in Azure Virtual Machines | Keine | „Australien, Osten“, „Indien, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Asien, Osten“, „Australien, Südosten“, „Kanada, Mitte“, „Brasilien, Süden“, „Kanada, Osten“, „Frankreich, Mitte“, „Frankreich, Süden“, „Japan, Osten“, „Japan, Westen“, „Korea, Mitte“, „Korea, Süden“, „Indien, Süden“, „Vereinigtes Königreich, Westen“, „Vereinigtes Königreich, Süden“, „USA, Mitte“, „USA, Osten 2“, „USA, Westen“, „USA, Westen 2“, „USA, Westen-Mitte“, „USA, Osten“, „USA, Süden-Mitte“, „USA, Norden-Mitte“, „Europa, Westen“, „US Gov TX“, „US GOV AZ“. |
| Azure-VMs | „USA, Osten“, „USA, Osten 2“, „USA, Mitte“, „USA, Süden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Westen-Mitte“, „USA, Norden-Mitte“, „Brasilien, Süden“, „Kanada, Osten“, „Kanada, Mitte“, „Europa, Westen“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Asien, Osten“, „Japan, Osten“, „Indien, Süden“, „Asien, Südosten“, „Australien, Osten“, „Indien, Mitte“, „Europa, Mitte“, „Australien, Südosten“, „Frankreich, Mitte“, „Frankreich, Süden“, „Japan, Westen“, „Südkorea, Mitte“, „Südkorea, Süden“. | Keine |


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Was passiert mit den Wiederherstellungspunkten im Archiv, wenn ich den Schutz beende und die Daten behalte?

Der Wiederherstellungspunkt bleibt dauerhaft im Archiv gespeichert. Weitere Informationen finden Sie unter [Auswirkungen auf Wiederherstellungspunkte beim Beenden des Schutzes](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>Wird die regionsübergreifende Wiederherstellung von der Zugriffsebene „Archiv“ unterstützt?

Wenn Sie Ihre Daten in GRS-Tresoren von der Zugriffsebene „Standard“ in die Zugriffsebene „Archiv“ verschieben, werden die Daten in das GRS-Archiv verschoben. Dies gilt auch, wenn die regionsübergreifende Wiederherstellung aktiviert ist. Sobald Sicherungsdaten in den Archiv-Tarif verschoben wurden, können Sie die Daten nicht mehr in der gekoppelten Region wiederherstellen. Fällt eine Region aus, stehen die Sicherungsdaten in der sekundären Region jedoch für die Wiederherstellung zur Verfügung. 

Beim Wiederherstellen eines Wiederherstellungspunkts in der Zugriffsebene „Archiv“ in der primären Region wird der Wiederherstellungspunkt in die Zugriffsebene „Standard“ kopiert und sowohl in der primären als auch in der sekundären Region gemäß der Dauer der Aktivierung aufbewahrt. Sie können die regionsübergreifende Wiederherstellung aus diesen aktivierten Wiederherstellungspunkten ausführen.

### <a name="i-can-see-eligible-recovery-points-for-my-virtual-machine-but-i-cant-seeing-any-recommendation-what-can-be-the-reason"></a>Ich kann geeignete Wiederherstellungspunkte für meinen virtuellen Computer sehen, aber keine Empfehlung. Was kann der Grund dafür sein?

Die Wiederherstellungspunkte für Virtual Machines erfüllen die Berechtigungskriterien. Es gibt also archivierbare Wiederherstellungspunkte. Die Änderungsrate auf dem virtuellen Computer kann jedoch gering sein, sodass es keine Empfehlungen gibt. In diesem Szenario können Sie die archivierbaren Wiederherstellungspunkte in den Archiv-Tarif verschieben. Dadurch können aber die Gesamtkosten für den Sicherungsspeicher steigen.

### <a name="i-have-stopped-protection-and-retained-data-for-my-workload-can-i-move-the-recovery-points-to-archive-tier"></a>Ich habe den Schutz beendet und Daten für meine Workload aufbewahrt. Kann ich die Wiederherstellungspunkte in den Archiv-Tarif verschieben?

Nein. Sobald der Schutz für eine bestimmte Workload beendet wurde, können die entsprechenden Wiederherstellungspunkte nicht in den Archiv-Tarif verschoben werden. Um Wiederherstellungspunkte in den Archiv-Tarif zu verschieben, müssen Sie den Schutz für die Datenquelle fortsetzen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden des Archiv-Tarifs](use-archive-tier-support.md)
- [Preise für Azure Backup](azure-backup-pricing.md)
