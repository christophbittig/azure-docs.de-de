---
title: Sichern von SQL Server Always-On-Verfügbarkeitsgruppen
description: In diesem Artikel erfahren Sie, wie Sie SQL Server Always-On-Verfügbarkeitsgruppen sichern.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 84826d7d6d8f0611c73dcfda25ca3492d9370c97
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115175"
---
# <a name="backup-sql-server-always-on-availability-groups"></a>Sichern von SQL Server Always-On-Verfügbarkeitsgruppen

Azure Backup bietet eine End-to-End-Unterstützung für die Sicherung von SQL Server Always On-Verfügbarkeitsgruppen (VG), wenn sich alle Knoten in derselben Region und im gleichen Abonnement wie der Recovery Services-Tresor befinden. Wenn die Knoten der VG jedoch auf Regionen/Abonnements/lokal und Azure verteilt sind, sollten Sie einige Überlegungen berücksichtigen.

>[!Note]
>Die Sicherung von Datenbanken der Verfügbarkeitsgruppe „Basic“ wird von Azure Backup nicht unterstützt.

Die von Azure Backup SQL VG verwendete Sicherungseinstellung unterstützt vollständige und differenzielle Sicherungen nur vom primären Replikat. Daher werden diese Sicherungsaufträge unabhängig von der Sicherungseinstellung immer auf dem primären Knoten ausgeführt. Bei vollständigen Kopiesicherungen und Transaktionsprotokollsicherungen wird bei der Entscheidung über den Knoten, auf dem die Sicherung ausgeführt wird, die Einstellung für die Sicherung der Gruppe berücksichtigt.

| VG-Sicherungseinstellung | Vollständige und differenzielle Sicherungen werden ausgeführt unter | Kopiesicherungen und Protokollsicherungen werden erstellt von |
| -------------------- | ---------------------------- | ---------------------------------------- |
| Primär | Primäres Replikat | Primäres Replikat |
| Nur sekundär | Primäres Replikat | Eines der sekundären Replikate |
| Sekundär bevorzugen | Primäres Replikat | Sekundäre Replikate werden bevorzugt, Sicherungen können jedoch auch auf dem primären Replikat ausgeführt werden. |
| Keine | Primäres Replikat | Beliebiges Replikat |

Die Workloadsicherungserweiterung wird auf dem Knoten installiert, wenn sie beim Azure Backup-Dienst registriert ist. Wenn eine VG-Datenbank für die Sicherung konfiguriert ist, werden die Sicherungszeitpläne per Push an alle registrierten Knoten der VG übertragen. Die Zeitpläne werden auf allen VG-Knoten und den Workloadsicherungserweiterungen auf diesen Knoten ausgelöst, um festzulegen, welcher Knoten die Sicherung durchführt. Die Knotenauswahl hängt, wie in Abschnitt 1 erläutert, vom Sicherungstyp und der Sicherungseinstellung ab. 

Der ausgewählte Knoten fährt mit dem Sicherungsauftrag fort, während der Auftrag, der auf den anderen Knoten ausgelöst wird, nicht mehr ausgeführt wird, d. h., er überspringt den Auftrag.

>[!Note]
>Azure Backup berücksichtigt bei der Auswahl zwischen den sekundären Replikaten keine Sicherungsprioritäten oder Replikate.

## <a name="register-ag-nodes-to-the-recovery-services-vault"></a>Registrieren von VG-Knoten im Recovery Services-Tresor

Ein Recovery Services-Tresor unterstützt die Sicherung von Datenbanken nur für VMs in derselben Region und demselben Abonnement wie der Tresor.

- Sie müssen den primären Knoten beim Tresor registrieren (andernfalls können keine vollständigen Sicherungen durchgeführt werden).
- Wenn die Sicherungseinstellung _nur sekundär_ ist, müssen Sie mindestens einen sekundären Knoten beim Tresor registrieren (andernfalls können keine vollständigen Protokoll-/Kopiesicherungen durchgeführt werden).

Wenn die oben genannten Bedingungen nicht erfüllt sind, tritt bei der Konfiguration von Sicherungen für VG-Datenbanken ein Fehler mit dem Fehlercode _FabricSvcBackupPreferenceCheckFailedUserError_ auf.

Betrachten wir die folgende VG-Bereitstellung als Referenz.

:::image type="content" source="./media/backup-sql-server-on-availability-groups/ag-deployment.png" alt-text="Abbildung für die VG-Bereitstellung als Referenz":::

Bei der obigen VG-Beispielbereitstellung sind verschiedene Überlegungen zu berücksichtigen:

- Da sich der primäre Knoten in Region 1 und Abonnement 1 befindet, muss sich der Recovery Services-Tresor (Tresor 1) in Region 1 und Abonnement 1 befinden, um diese VG zu schützen.
- VM3 kann nicht bei Tresor 1 registriert werden, da er sich in einem anderen Abonnement befindet.
- VM4 kann nicht bei Tresor 1 registriert werden, da er sich in einer anderen Region befindet.
- Wenn die Sicherungseinstellung _nur sekundär_ ist, müssen VM1 (primär) und VM2 (sekundär) bei Tresor 1 registriert werden (da vollständige Sicherungen den primären Knoten und Protokolle einen sekundären Knoten erfordern). Für andere Sicherungseinstellungen muss VM1 (primär) bei Tresor 1 registriert sein, VM2 ist optional (da alle Sicherungen auf dem primären Knoten ausgeführt werden können).
- VM3 könnte zwar in Abonnement 2 bei Tresor 2 registriert werden, und die VG-Datenbanken würden dann für den Schutz in Tresor 2 angezeigt, aber aufgrund des Fehlens des primären Knotens in Tresor 2 würde die Konfiguration von Sicherungen fehlschlagen.
- Auch wenn VM4 für Tresor 4 in Region 2 registriert werden kann, schlägt das Konfigurieren von Sicherungen fehl, da der primäre Knoten nicht in Tresor 4 registriert ist.

## <a name="handle-failover"></a>Failover-Behandlung

Nachdem für die VG ein Failover auf einen der sekundären Knoten erfolgt ist:

- Die vollständigen und differenziellen Sicherungen werden vom neuen primären Knoten aus fortgesetzt, wenn sie beim Tresor registriert sind.
- Die vollständigen Protokoll- und Kopiesicherungen werden basierend auf der Sicherungseinstellung vom primären/sekundären Knoten fortgesetzt.

>[!Note]
>Protokollkettenunterbrechungen werden beim Failover nicht ausgeführt, wenn das Failover nicht mit einer Sicherung übereinstimmt.

Im Folgenden werden die verschiedenen Failovermöglichkeiten für die obige Beispielbereitstellung der VG-Gruppe aufgeführt:

- Failover auf VM2
  - Vollständige und differenzielle Sicherungen werden von VM2 durchgeführt.
  - Vollständige Protokoll- oder Kopiesicherungen werden basierend auf der Sicherungseinstellung von VM1 oder VM2 durchgeführt.
- Failover auf VM3 (anderes Abonnement)
  - Da Sicherungen in Tresor 2 nicht konfiguriert sind, werden keine Sicherungen durchgeführt.
  - Wenn die Sicherungseinstellung nicht auf nur sekundär festgelegt ist, können Sicherungen jetzt in Tresor 2 konfiguriert werden, da der primäre Knoten in diesem Tresor registriert ist. Dies kann jedoch zu Konflikten/Sicherungsfehlern führen. Weitere Informationen hierzu unter [Konfigurieren von Sicherungen für eine VG mit mehreren Regionen](#configure-backups-for-a-multi-region-ag).
- Failover auf VM4 (andere Region)
  - Da Sicherungen in Tresor 4 nicht konfiguriert sind, werden keine Sicherungen durchgeführt.
  - Wenn die Sicherungseinstellung nicht auf nur sekundär festgelegt ist, können Sicherungen jetzt in Tresor 4 konfiguriert werden, da der primäre Knoten in diesem Tresor registriert ist. Dies kann jedoch zu Konflikten/Sicherungsfehlern führen. Weitere Informationen hierzu unter [Konfigurieren von Sicherungen für eine VG mit mehreren Regionen](#configure-backups-for-a-multi-region-ag).

## <a name="configure-backups-for-a-multi-region-ag"></a>Konfigurieren von Sicherungen für eine VG mit mehreren Regionen

Der Recovery Services-Tresor unterstützt keine abonnement- oder regionsübergreifenden Sicherungen. In diesem Abschnitt wird zusammengefasst, wie Sie Sicherungen für VGs aktivieren, die verschiedene Abonnements oder Azure-Regionen umfassen, und die damit verbundenen Überlegungen.

- Werten Sie aus, ob Sie wirklich Sicherungen von allen Knoten aktivieren müssen. Wenn eine Region/ein Abonnement die meisten Knoten der Gruppe aufweist und ein Failover auf andere Knoten sehr selten erfolgt, reicht das Einrichten der Sicherung in dieser ersten Region möglicherweise aus. Wenn die Failover in eine andere Region/ein anderes Abonnement häufig und über einen längeren Zeitraum erfolgen, sollten Sie Sicherungen auch in der anderen Region proaktiv einrichten.

- Jeder Tresor, in dem die Sicherung aktiviert wird, verfügt über eigene Wiederherstellungspunktketten. Wiederherstellungen von diesen Wiederherstellungspunkten können nur auf VMs durchgeführt werden, die in diesem Tresor registriert sind.

- Vollständige/differenzielle Sicherungen werden nur im Tresor erfolgreich ausgeführt, der über den primären Knoten verfügt. Bei Sicherungen in anderen Tresoren treten weiterhin Fehler auf.

- Protokollsicherungen funktionieren weiterhin im vorherigen Tresor, bis eine Protokollsicherung im neuen Tresor ausgeführt wird (d. h. in dem Tresor, in dem der neue primäre Knoten vorhanden ist) und die Protokollkette für den alten Tresor _unterbricht_.
  >[!Note]
  >Es gibt eine feste Grenze von 15 Tagen, nach der Protokollsicherungen fehlschlagen.

- Vollständige Kopiesicherungen funktionieren in allen Tresoren.

- Der Schutz wird in jedem Tresor als unterschiedliche Datenquelle behandelt und separat abgerechnet.

Es wird empfohlen, die Sicherungseinstellung auf Primär festzulegen, um Protokollsicherungskonflikte zwischen den beiden Tresoren zu vermeiden. Je nachdem, welcher Tresor über den primären Knoten verfügt, werden dann in diesem auch die Protokollsicherungen erstellt.

Im Folgenden finden Sie die Schritte zum Aktivieren der Sicherung von allen Knoten anhand der obigen Beispiel-VG-Bereitstellung. Es wird davon ausgegangen, dass die Sicherungseinstellung in allen Schritten erfüllt wird.

### <a name="step-1-enable-backups-in-region-1-subscription-1-vault-1"></a>Schritt 1: Aktivieren von Sicherungen in Region 1, Abonnement 1 (Tresor 1)

Da sich der primäre Knoten in der Region und im Abonnement befindet, funktionieren die üblichen Schritte zum Aktivieren von Sicherungen.

### <a name="step-2-enable-backups-in-region-1-subscription-2-vault-2"></a>Schritt 2: Aktivieren von Sicherungen in Region 1, Abonnement 2 (Tresor 2)

1. Failover der VG auf VM3, sodass der primäre Knoten in Tresor 2 vorhanden ist
1. Konfigurieren Sie Sicherungen für die VG-Datenbanken in Tresor 2.
1. Zu diesem Zeitpunkt gilt Folgendes:
   1. Die vollständigen/differenziellen Sicherungen schlagen in Tresor 1 fehl, da keiner der registrierten Knoten diese Sicherung erstellen kann.
   1. Die Protokollsicherungen werden in Tresor 1 erfolgreich ausgeführt, bis eine Protokollsicherung in Tresor 2 ausgeführt wird und die Protokollkette für Vault 1 _unterbrochen_ wird.
1. Failback der VG auf VM1

### <a name="step-3-enable-backups-in-region-2-subscription-1-vault-4"></a>Schritt 3: Aktivieren von Sicherungen in Region 2, Abonnement 1 (Tresor 4)

Identisch mit Schritt 2

## <a name="backup-an-ag-that-spans-azure-and-on-premises"></a>Sichern einer VG, die Azure- und lokale Bereiche umfasst

Azure Backup für SQL Server kann nicht lokal ausgeführt werden. Wenn sich der primäre Knoten in Azure befindet und die Sicherungseinstellung von den Knoten in Azure erfüllt wird, können Sie die obige Anleitung für die VG mit mehreren Regionen befolgen, um Sicherungen für die Replikate in Azure zu aktivieren. Wenn ein Failover auf einen lokalen Knoten erfolgt, treten bei den vollständigen und differenziellen Sicherungen in Azure Fehler auf. Protokollsicherungen können fortgesetzt werden, bis die Protokollkette unterbrochen wird oder 15 Tage vergangen sind.

## <a name="throttling-for-backup-jobs-in-an-ag-database"></a>Drosselung für Sicherungsaufträge in einer VG-Datenbank

Derzeit gelten die Grenzwerte für die Sicherungsdrosselung auf ebene einzelner Computer. Der Standardgrenzwert ist 20. Wenn mehr als 20 Sicherungen gleichzeitig ausgelöst werden, werden die ersten 20 ausgeführt, und die anderen werden in die Warteschlange gestellt. Wenn die ausgeführten Aufträge abgeschlossen sind, wird die Ausführung der Aufträge in der Warteschlange gestartet.

Sie können diesen Wert in einen kleineren Wert ändern, wenn die gleichzeitigen Sicherungen eine Speicher-, E/A- oder CPU-Auslastung auf dem Knoten verursachen.
**Da die Drosselung auf Knotenebene ausgeführt wird, kann dies zu Problemen bei der Sicherungssynchronisierung führen**. Stellen Sie sich dazu eine Verfügbarkeitsgruppe mit 2 Knoten vor.

Beispielsweise verfügt der erste Knoten über 50 eigenständige Datenbanken, die geschützt sind, und beide Knoten verfügen über 5 geschützte VG-Datenbanken. Auf Knoten 1 sind 55 Datenbanksicherungsaufträge geplant, auf Knoten 2 dagegen nur 5. Außerdem sind alle diese Sicherungen so konfiguriert, dass sie stündlich gleichzeitig ausgeführt werden. An einem Punkt werden alle 55 Sicherungen auf Knoten 1 ausgelöst, und 35 dieser Sicherungen werden in die Warteschlange gestellt. Einige davon sind die VG-Datenbanksicherungen. Auf Knoten 2 würden die VG-Datenbanksicherungen jedoch ohne Warteschlangen ausgeführt.

Da die VG-Datenbankaufträge auf einem Knoten in die Warteschlange gestellt und auf einem anderen ausgeführt werden, funktioniert die Sicherungssynchronisierung (wie in Abschnitt 6 erwähnt) nicht ordnungsgemäß. Knoten 2 geht möglicherweise davon aus, dass Knoten 1 ausgefallen ist und Aufträge von dort nicht mehr zur Synchronisierung verwendet werden. Dies kann zu Protokollkettenunterbrechungen oder zusätzlichen Sicherungen führen, da beide Knoten Sicherungen unabhängig voneinander erstellen können.

Ein ähnliches Problem kann vorkommen, wenn die Anzahl der geschützten VG-Datenbanken über dem Drosselungslimit liegt. In diesem Fall kann die Sicherung für z. B. DB1 auf Knoten 1 in die Warteschlange gestellt werden, während sie auf Knoten 2 ausgeführt wird. 

Es wird empfohlen, die folgenden Sicherungseinstellungen zu verwenden, um diese Synchronisierungsprobleme zu vermeiden:

- Legen Sie für eine VG mit 2 Knoten die Sicherungseinstellung auf Primär oder Nur sekundär fest. Dann kann nur ein Knoten die Sicherungen durchführen, der andere wird immer aussetzen. 
- Legen Sie für eine VG mit mehr als 2 Knoten die Sicherungseinstellung auf Primär fest. Dann kann nur der primäre Knoten die Sicherungen durchführen, andere Knoten setzen aus.

## <a name="billing-for-ag-backups"></a>Abrechnung für VG-Sicherungen

Wie bei einer eigenständigen SQL-Instanz wird eine gesicherte VG-Instanz als eine geschützte Instanz betrachtet. Die Gesamtgröße des Front-Ends aller geschützten Datenbanken in einer Instanz wird in Rechnung gestellt. Betrachten Sie die folgende Bereitstellung:

:::image type="content" source="./media/backup-sql-server-on-availability-groups/protected-instances-calculation.png" alt-text="Abbildung zeigt die Berechnung geschützter Instanzen von Datenbanken":::

Die geschützten Instanzen werden wie folgt berechnet:

| Geschützte Instanz/Abrechnungsinstanz | Datenbanken, die für die Berechnung der Front-End-Größe berücksichtigt werden |
| ------------------------------------ | -------------------------------------------------- |
| VG1 | DB1, DB2 |
| VG2 | DB4 |
| VM2 | DB3 |
| VM3 | DB6 |
| VM4 | DB5 |

## <a name="moving-a-protected-database-in-or-out-of-an-ag"></a>Verschieben einer geschützten Datenbank in eine oder aus einer VG

Azure Backup betrachtet **SQL instance oder AGName\DatabaseName** als eindeutigen Datenbanknamen. Als die eigenständige Datenbank geschützt wurde, war ihr eindeutiger Name _StandAloneInstanceName\DBName_. Wenn sie unter einer VG bewegt wird, ändert sich der eindeutige Name in _AGName\DBName_. Bei den Sicherungen für die eigenständige Datenbank tritt ein Fehler mit dem folgenden Fehlercode auf: _UserErrorBackupFailedStandaloneDatabaseMovedInToAG_.

Die Datenbank muss für den Schutz unter der VG konfiguriert sein. Diese wird als neue Datenquelle mit einer separaten Wiederherstellungspunktkette behandelt. Der ältere Schutz einer eigenständigen Datenbank kann mit Beibehaltung von Daten beendet werden, um zu verhindern, dass zukünftige Sicherungen ausgelöst werden und fehlschlagen. Wenn eine geschützte Ag-Datenbank aus der Ag-Datenbank wechselt und zu einer eigenständigen Datenbank wird, tritt bei den Sicherungen ein Fehler mit dem Fehlercode auf: _UserErrorBackupFailedDatabaseMovedOutOfAG_.

Die Datenbank muss für den Schutz unter der eigenständigen Instanz konfiguriert sein. Diese wird als neue Datenquelle mit einer separaten Wiederherstellungspunktkette behandelt. Der ältere Schutz der VG-Datenbank kann mit Beibehaltung von Daten beendet werden, um zu verhindern, dass zukünftige Sicherungen ausgelöst werden und fehlschlagen.

## <a name="additionremoval-of-a-node-to-an-ag"></a>Hinzufügen/Entfernen eines Knotens zu einer VG

Wenn einer für Sicherungen konfigurierten VG ein neuer Knoten hinzugefügt wird, erkennen die Workloadsicherungserweiterungen, die auf den bereits registrierten Knoten ausgeführt werden, die Änderung der VG-Topologie und informieren den Azure Backup-Dienst während des nächsten geplanten Datenbankermittlungsauftrags. Wenn dieser neue Knoten für Sicherungen im gleichen Recovery Services-Tresor wie die anderen vorhandenen Knoten registriert wird, löst der Azure Backup-Dienst einen Workflow aus, der diesen neuen Knoten mit den erforderlichen Metadaten für die Durchführung von VG-Sicherungen konfiguriert.

Danach synchronisiert der neue Knoten die Informationen zum VG-Sicherungszeitplan des Azure Backup-Diensts und beginnt mit der Teilnahme am synchronisierten Sicherungsprozess. Wenn der neue Knoten nicht in der Lage ist, die Sicherungszeitpläne zu synchronisieren und an Sicherungen teilzunehmen, erzwingt das Auslösen einer erneuten Registrierung auf dem Knoten auch eine Neukonfiguration des Knotens für VG-Sicherungen. Auch in diesem Fall erkennen die Workloaderweiterungen beim Hinzufügen von Knoten die Änderung der VG-Topologie und informieren den Azure Backup Dienst. Der Dienst startet einen Workflow zum _Entfernen der Knotenkonfiguration_ im entfernten Knoten, um die Sicherungszeitpläne für VG-Datenbanken zu löschen und die VG-bezogenen Metadaten zu löschen.

## <a name="un-register-an-ag-node-from-azure-backup"></a>Aufheben einer VG-Knotenregistrierung aus Azure Backup

Wenn ein Knoten Teil einer VG ist, für die mindestens eine Datenbank für die Sicherung konfiguriert ist, lässt Azure Backup die Aufhebung der Registrierung dieses Knotens nicht zu. Dies soll zukünftige Sicherungsfehler verhindern, falls die Sicherungseinstellung ohne diesen Knoten nicht erfüllt werden kann. Sie müssen den Knoten zunächst aus der VG entfernen, um dessen Registrierung aufheben zu können. Wenn der Workflow _zum Aufheben der Konfiguration_ des Knotens abgeschlossen ist, können Sie die Registrierung des Knotens aufheben.

Wiederherstellen einer Datenbank aus Azure Backup in einer Verfügbarkeitsgruppe, SQL-Verfügbarkeitsgruppen unterstützen kein direktes Wiederherstellen einer Datenbank in die Verfügbarkeitsgruppe. Die Datenbank muss in einer eigenständigen SQL-Instanz wiederhergestellt und dann mit einer VG verbunden werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:

* [Wiederherstellen von gesicherten SQL Server-Datenbanken](restore-sql-database-azure-vm.md)
* [Verwalten von gesicherten SQL Server-Datenbanken](manage-monitor-sql-database-backup.md)
