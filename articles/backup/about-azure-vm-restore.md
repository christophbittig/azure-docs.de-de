---
title: Informationen zur Wiederherstellung virtueller Azure-Computer
description: Erfahren Sie, wie virtuelle Azure-Computer mit dem Dienst „Azure Backup“ wiederhergestellt werden.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: c81a2c227214643678f0a82bac29b7d573b9994e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262145"
---
# <a name="about-azure-vm-restore"></a>Informationen zur Wiederherstellung von Azure-VMs

In diesem Artikel wird beschrieben, wie virtuelle Azure-Computer (VMs) mit dem Dienst [Azure Backup](./backup-overview.md) wiederhergestellt werden. Es gibt eine Reihe von Wiederherstellungsoptionen. Wir besprechen die verschiedenen Szenarien, in denen Sie unterstützt werden.

## <a name="concepts"></a>Konzepte

- **Wiederherstellungspunkt**: Bei einem Wiederherstellungspunkt handelt es sich um eine Kopie der ursprünglichen Daten, die gesichert wurden.

- **Ebene (Momentaufnahme oder Tresor)** :  Die Azure-VM-Sicherung erfolgt in zwei Phasen:

  - In Phase 1 wird die erstellte Momentaufnahme zusammen mit dem Datenträger gespeichert. Dies wird als **Momentaufnahmenebene** bezeichnet. Wiederherstellungen auf Momentaufnahmenebene erfolgen schneller (als die Wiederherstellung aus dem Tresor), da die Wartezeit für das Kopieren von Momentaufnahmen in den Tresor entfällt, bevor die Wiederherstellung ausgelöst wird. Daher wird die Wiederherstellung über die Momentaufnahmenebene auch als [sofortige Wiederherstellung](./backup-instant-restore-capability.md) bezeichnet.
  - In Phase 2 wird die Momentaufnahme übertragen und in dem vom Dienst Azure Backup verwalteten Tresor gespeichert. Dies wird als **Tresorebene** bezeichnet.

- **Wiederherstellung am ursprünglichen Speicherort**: Eine Wiederherstellung, die vom Wiederherstellungspunkt zur Azure-Quell-VM, deren Sicherungen erstellt wurden, durchgeführt und durch den im Wiederherstellungspunkt gespeicherten Zustand ersetzt wird. Dabei werden der Betriebssystem- und die anderen Datenträger der Quell-VM ersetzt.

- **Wiederherstellung an einem alternativen Speicherort**: Eine Wiederherstellung, die vom Wiederherstellungspunkt zu einem anderen Server als dem ursprünglichen Server erfolgt, auf dem die Sicherungen erstellt wurden.

- **Wiederherstellung auf Elementebene**: Hierbei werden einzelne Dateien oder Ordner innerhalb der VM mithilfe des Wiederherstellungspunkts wiederhergestellt.

- **Verfügbarkeit (Replikationsarten)** : Azure Backup bietet zwei Replikationsarten, um die Hochverfügbarkeit Ihres Speichers bzw. Ihrer Daten sicherzustellen:
  - [Lokal redundanter Speicher (Locally Redundant Storage, LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) repliziert Ihre Daten dreimal in einer Speicherskalierungseinheit in einem Datencenter. (Es werden also drei Kopien Ihrer Daten erstellt.) Alle Kopien der Daten befinden sich in derselben Region. LRS ist eine kostengünstige Möglichkeit, um Daten vor lokalen Hardwarefehlern zu schützen.
  - [Geografisch redundanter Speicher (Geo-Redundant Storage, GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) ist die standardmäßige und empfohlene Replikationsoption. GRS repliziert Ihre Daten in einer sekundären Region, die mehrere hundert Kilometer vom primären Speicherort der Quelldaten entfernt ist. GRS führt zu höheren Kosten als LRS, bietet aber eine höhere Dauerhaftigkeit Ihrer Daten (auch im Falle eines regionalen Ausfalls).
  - [Zonenredundanter Speicher (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) repliziert Ihre Daten in [Verfügbarkeitszonen](../availability-zones/az-overview.md#availability-zones) und gewährleistet dadurch Data Residency und Resilienz in derselben Region. Bei ZRS gibt es keine Downtime. Daher können Ihre kritischen Workloads, die [Data Residency](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) erfordern und bei denen keine Downtime auftreten darf, in ZRS gesichert werden.

- **Regionsübergreifende Wiederherstellung**: Als eine der [Wiederherstellungsoptionen](./backup-azure-arm-restore-vms.md#restore-options) ermöglicht die regionsübergreifende Wiederherstellung das Wiederherstellen von Azure-VMs in einer sekundären Region, bei der es sich um eine [gekoppelte Azure-Region](../best-practices-availability-paired-regions.md#what-are-paired-regions) handelt. Sie können die Daten jederzeit – bei partiellen oder vollständigen Ausfällen oder zu jedem anderen beliebigen Zeitpunkt – in der sekundären Region wiederherstellen. 

## <a name="restore-scenarios"></a>Wiederherstellungsszenarien

![Wiederherstellungsszenarien ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Szenario**                                                 | **Aufgaben**                                             | **Einsatzgebiete**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Wiederherstellen zum Erstellen eines neuen virtuellen Computers](./backup-azure-arm-restore-vms.md) | Stellt die gesamte VM am ursprünglichen Speicherort (wenn die Quell-VM noch vorhanden ist) oder am alternativen Speicherort wieder her | <li> Wenn die Quell-VM verloren gegangen oder beschädigt ist, können Sie die gesamte VM wiederherstellen.  <li> Sie können eine Kopie der VM erstellen.  <li> Sie können für Überwachungs- oder Compliancezwecke eine Wiederherstellungsübung ausführen.  <li> Diese Option funktioniert nicht für Azure-VMs, die anhand von Marketplace-Images erstellt wurden (d. h. wenn diese nicht verfügbar sind, weil die Lizenz abgelaufen ist). |
| [Wiederherstellen von Datenträgern der VM](./backup-azure-arm-restore-vms.md#restore-disks) | An die VM angefügte Datenträger wiederherstellen                             |  Alle Datenträger: Mit dieser Option wird die Vorlage erstellt und der Datenträger wiederhergestellt. Sie können diese Vorlage mit speziellen Konfigurationen (z. B. Verfügbarkeitsgruppen) Ihren Anforderungen entsprechend bearbeiten und dann sowohl die Vorlage als auch den Datenträger verwenden, um die VM neu zu erstellen. |
| [Wiederherstellen bestimmter Dateien in der VM](./backup-azure-restore-files-from-vm.md) | Wiederherstellungspunkt wählen, System durchsuchen, Dateien auswählen und unter dem gleichen (oder einem kompatiblen) Betriebssystem wie dem der gesicherten VM wiederherstellen |  Wenn Sie wissen, welche spezifischen Dateien wiederhergestellt werden müssen, sollten Sie diese Option wählen, anstatt die gesamte VM wiederherzustellen. |
| [Wiederherstellen einer verschlüsselten VM](./backup-azure-vms-encryption.md) | Im Portal die Datenträger wiederherstellen und dann mit PowerShell die VM erstellen | <li> [Verschlüsselte VM mit Azure Active Directory](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Verschlüsselte VM ohne Azure AD](../virtual-machines/windows/disk-encryption-windows.md) <li> [Verschlüsselte VM *mit Azure AD* nach der Migration zur Variante *ohne Azure AD*](../virtual-machines/windows/disk-encryption-faq.yml#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app-) |
| [Regionsübergreifende Wiederherstellung](./backup-azure-arm-restore-vms.md#cross-region-restore) | Eine neue VM erstellen oder Datenträger in einer sekundären Region wiederherstellen (mit Azure gekoppelte Region) | <li> **Vollständiger Ausfall**:  Beim Feature der regionsübergreifenden Wiederherstellung gibt es keine Wartezeit für die Wiederherstellung von Daten in der sekundären Region. Sie können Wiederherstellungen in der sekundären Region einleiten, noch bevor Azure einen Ausfall bekannt gibt. <li> **Teilausfall**: Ausfallzeiten können in bestimmten Speicherclustern auftreten, in denen Azure Backup Ihre gesicherten Daten speichert oder sogar im Netzwerk, wobei Azure Backup und Speichercluster, die Ihren gesicherten Daten zugeordnet sind, miteinander verbunden werden. Bei der regionsübergreifenden Wiederherstellung können Sie eine Wiederherstellung in der sekundären Region durchführen, indem Sie ein Replikat der gesicherten Daten in der sekundären Region verwenden. <li> **Kein Ausfall**: Sie können mit den Daten in der sekundären Region zu Überprüfungs- oder Compliancezwecken Übungen für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity Disaster Recovery, BCDR) durchführen. Dadurch können Sie für Übungen zur Sicherstellung von Geschäftskontinuität und Notfallwiederherstellung eine Wiederherstellung der gesicherten Daten in der sekundären Region durchführen, selbst wenn in der primären Region kein vollständiger oder Teilausfall vorliegt.  |

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen zur VM-Wiederherstellung](./backup-azure-vm-backup-faq.yml)
- [Unterstützte Wiederherstellungsmethoden](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Problembehandlung bei der Wiederherstellung](./backup-azure-vms-troubleshoot.md#restore)
