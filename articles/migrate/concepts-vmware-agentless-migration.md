---
title: Replikation ohne Agents von virtuellen VMware-Computerkonzepten
description: Beschreibt Konzepte im Zusammenhang mit der Migration ohne Agents von VMware-VMs in Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 05/31/2021
ms.openlocfilehash: c19bff913f38503dbd99f86757ad6cda0ee5cb5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658220"
---
# <a name="azure-migrate-agentless-migration-of-vmware-virtual-machines"></a>Azure Migrate Migration ohne Agents von virtuellen VMware-Computern

Dieser Artikel beschreibt die Replikationskonzepte bei der Migration von VMware-VMs mit Azure Migrate beschrieben: Migrationsmethode der Servermigration ohne Agent.

## <a name="replication-process"></a>Replikationsprozess

Die Replikationsoption ohne Agent funktioniert mithilfe von VMware-Momentaufnahmen und der VMware CBT-Technologie (VMware Changed Block Tracking), um Daten von Datenträgern virtueller Computer zu replizieren. Das folgende Blockdiagramm zeigt verschiedene Schritte, die bei der Migration Ihrer VM mittels Azure Migrate: Server Migration Tool erforderlich sind.

 ![Schritte bei der Migration](./media/concepts-vmware-agentless-migration/migration-phases.png)

Wenn Replikation für einen virtuellen Computer konfiguriert ist, wird zuerst eine anfängliche Replikationsphase durchlaufen. Während der ersten Replikation wird eine VM-Momentaufnahme erstellt, und eine vollständige Kopie der Daten von den Momentaufnahmedatenträgern wird auf verwaltete Datenträger in Ihrem Zielabonnement repliziert.

Nach Abschluss der ersten Replikation für die VM geht der Replikationsprozess in eine inkrementelle Replikationsphase (Deltareplikation) über. In der inkrementellen Replikationsphase werden Datenänderungen, die seit Beginn des letzten abgeschlossenen Replikationszyklus eingetreten sind, repliziert und auf die verwalteten Datenträger des Replikats geschrieben, wodurch die Replikation mit den Änderungen auf der VM synchron gehalten wird.

Änderungen zwischen Replikationszyklen werden mithilfe der VMware CBT-Technologie für geänderte Blöcke nachverfolgt. Zu Beginn des Replikationszyklus wird eine VM-Momentaufnahme erstellt, und die Nachverfolgung geänderter Blöcke wird verwendet, um die Änderungen zwischen der aktuellen Momentaufnahme und der zuletzt erfolgreich replizierten Momentaufnahme abzurufen. Es werden nur Daten repliziert, die seit dem vorherigen abgeschlossenen Replikationszyklen geändert wurden, um die Replikation für die VM synchron zu halten. Am Ende jedes Replikationszyklus wird die Momentaufnahme freigegeben und eine Momentaufnahmekonsolidierung für den virtuellen Computer ausgeführt.

Wenn Sie den Migrationsvorgang auf einem replizierenden virtuellen Computer ausführen, gibt es einen bedarfsorientierten Deltareplikationszyklus, der die verbleibenden Änderungen seit dem letzten Replikationszyklus repliziert. Nach Ende des bedarfsorientierten Zyklus werden die dem virtuellen Computer entsprechenden verwalteten Datenträger des Replikats verwendet, um den virtuellen Computer in Azure zu erstellen. Unmittelbar vor dem Auslösen der Migration/Failover müssen Sie den lokalen virtuellen Computer herunterfahren. Durch das Herunterfahren des virtuellen Computers wird sichergestellt, dass während der Migration kein Datenverlust entsteht.


Nachdem die Migration erfolgreich war und der virtuelle Computer in Azure gestartet wird, stellen Sie sicher, dass Sie die Replikation des virtuellen Computers beenden. Wenn Sie die Replikation beenden, werden die zwischengeschalteten Datenträger (Seed-Datenträger) gelöscht, die während der Datenreplikation erstellt wurden. Außerdem vermeiden Sie zusätzliche Gebühren für Speichertransaktionen auf diesen Datenträgern.

## <a name="replication-cycles"></a>Replikationszyklen

Replikationszyklen beziehen sich auf den regelmäßigen Prozess der Übertragung von Daten aus der lokalen Umgebung auf verwaltete Azure-Datenträger. Ein vollständiger Replikationszyklus besteht aus den folgenden Schritten:

1. Erstellen einer VMware-Momentaufnahme für jeden Datenträger, der dem virtuellen Computer zugeordnet ist
2. Hochladen von Daten in das Protokollspeicherkonto in Azure
3. Releasemomentaufnahme
4. Konsolidieren von VMware-Datenträgern

Ein Zyklus wird als abgeschlossen bezeichnet, nachdem die Datenträger konsolidiert wurden.

## <a name="components-involved-in-replication"></a>Komponenten, die an der Replikation beteiligt sind

**Lokale Komponenten:** Die Azure Migrate Appliance verfügt über die folgenden Komponenten, die für die Replikation verantwortlich sind

- DRA-Agent
- Gateway-Agent

**Azure-Komponenten:** In der folgenden Tabelle sind die verschiedenen Azure Artifacts zusammengefasst, die mithilfe der VMware-VM-Migrationsmethode ohne Agent erstellt werden.

| Komponente | Region | Subscription | BESCHREIBUNG |
| --- | --- | --- | --- |
| Recovery Services-Tresor | Azure Migrate-Projektregion | Azure Migrate-Projektabonnement | Wird zum Orchestrieren der Datenreplikation verwendet |
| Service Bus | Zielregion | Azure Migrate-Projektabonnement | Wird für die Kommunikation zwischen Clouddienst und Azure Migrate Appliance verwendet |
| Protokollspeicherkonto | Zielregion | Azure Migrate-Projektabonnement | Wird zum Speichern von Replikationsdaten verwendet, die vom Dienst gelesen und auf den verwalteten Datenträger des Kunden angewendet werden |
| Gatewayspeicherkonto | Zielregion | Azure Migrate-Projektabonnement | Wird zum Speichern von Computerzuständen während der Replikation verwendet |
| Schlüsseltresor | Zielregion | Azure Migrate-Projektabonnement | Verwaltet Verbindungszeichenfolgen für Service Bus und Zugriffsschlüssel für das Protokollspeicherkonto |
| Virtueller Azure-Computer | Zielregion | Zielabonnement | VM, die bei der Migration in Azure erstellt wurde |
| Azure Managed Disks | Zielregion | Zielabonnement | Verwaltete Datenträger, die an Azure-VMs angefügt sind |
| Netzwerkschnittstellenkarten | Zielregion | Zielabonnement | Die NICs, die an die in Azure erstellten VMs angefügt sind |

## <a name="permissions-required"></a>Erforderliche Berechtigungen

Wenn Sie die Replikation zum ersten Mal starten, müssen dem angemeldeten Benutzer die folgenden Rollen zugewiesen werden:

- Besitzer oder Mitwirkender und Benutzerzugriffsadministrator für die Ressourcengruppe und Zielressourcengruppe des Azure Migrate Projekts

Für die nachfolgenden Replikationen müssen dem angemeldeten Benutzer die folgenden Rollen zugewiesen werden:

- Besitzer oder Mitwirkender für die Ressourcengruppe und Zielressourcengruppe des Azure Migrate Projekts

Zusätzlich zu den oben beschriebenen Rollen benötigt der angemeldete Benutzer die folgende Berechtigung auf Abonnementebene: Microsoft.Resources/subscriptions/resourceGroups/read


## <a name="data-integrity"></a>Datenintegrität

Es gibt zwei Phasen in jedem Replikationszyklus, die die Datenintegrität zwischen dem lokalen Datenträger (Quelldatenträger) und dem Replikatdatenträger in Azure (Zieldatenträger) sicherstellen.

1. Zunächst überprüfen wir, ob jeder Sektor, der sich auf dem Quelldatenträger geändert hat, auf den Zieldatenträger repliziert wird. Die Validierung wird mithilfe von Bitmaps ausgeführt.
Der Quelldatenträger ist in Sektoren mit 512 Bytes unterteilt. Jeder Sektor auf dem Quelldatenträger wird einem Bit in der Bitmap zugeordnet. Wenn die Datenreplikation startet, wird die Bitmap für alle geänderten Blöcke (im Deltazyklus) auf dem Quelldatenträger erstellt, die repliziert werden müssen. Entsprechend wird beim Übertragen der Daten auf den Azure-Zieldatenträger eine Bitmap erstellt. Nachdem die Datenübertragung erfolgreich abgeschlossen wurde, vergleicht der Clouddienst die beiden Bitmaps, um sicherzustellen, dass kein geänderter Block ausgelassen wurde. Falls zwischen den Bitmaps ein Konflikt besteht, wird der Zyklus als fehlgeschlagen betrachtet. Da jeder Zyklus neu synchronisiert wird, wird der Konflikt im nächsten Zyklus behoben.

1. Als Nächstes stellen wir sicher, dass die Daten, die auf die Azure-Datenträger übertragen werden, mit den Daten übereinstimmen, die von den Quelldatenträgern repliziert wurden. Jeder geänderte Block, der hochgeladen wird, wird komprimiert und verschlüsselt, bevor er als Blob in das Protokollspeicherkonto geschrieben wird. Wir berechnen die Prüfsumme dieses Blocks vor der Komprimierung. Diese Prüfsumme wird zusammen mit den komprimierten Daten als Metadaten gespeichert. Bei der Dekomprimierung wird die Prüfsumme für die Daten berechnet und mit der Prüfsumme verglichen, die in der Quellumgebung berechnet wird. Wenn ein Konflikt vorliegt, werden die Daten nicht auf die Azure-Datenträger geschrieben, und der Zyklus wird als fehlgeschlagen betrachtet. Da jeder Zyklus neu synchronisiert wird, wird der Konflikt im nächsten Zyklus behoben.

## <a name="security"></a>Sicherheit

Die Azure Migrate-Appliance komprimiert Daten und verschlüsselt sie vor dem Hochladen. Daten werden über einen sicheren Kommunikationskanal über HTTPS übertragen und verwenden TLS 1.2 oder höher. Zudem werden Ihre Daten von Azure Storage beim Speichern in der Cloud automatisch verschlüsselt (Verschlüsselung ruhender Daten).



## <a name="migration-phase"></a>Phase: Migration

Wenn eine VM repliziert wird, gibt es einige Zustände, die möglich sind:

- **Erste Replikation (in Warteschlange):** Der virtuelle Computer wird für die Replikation (oder Migration) in die Warteschlange gestellt, wenn andere VMs die lokalen Ressourcen während der Replikation (oder Migration) nutzen. Sobald die Ressourcen frei sind, wird dieser virtuelle Computer verarbeitet.
- **Erste Replikation:** Für den virtuellen Computer wird eine erste Replikation ausgeführt. Wenn die erste Replikation für den virtuellen Computer ausgeführt wird, können Sie nicht mit der Testmigration bzw. der Migration fortfahren. Sie können die Replikation nur in dieser Phase beenden.
- **Testmigration ausstehend:** Die VM befindet sich in der Deltareplikationsphase, und Sie können jetzt eine Testmigration (oder Migration) durchführen.
- **Migration in Bearbeitung (in Warteschlange):** Der virtuelle Computer wird für die Migration in die Warteschlange gestellt, wenn andere VMs die lokalen Ressourcen während der Replikation (oder Migration) nutzen. Sobald die Ressourcen frei sind, wird der virtuelle Computer für die Migration verarbeitet.
- **Migration in Bearbeitung:** Die VM wird migriert. Sie können den Link auswählen, um den laufenden Migrationsauftrag zu überprüfen. Dieser Auftrag besteht aus fünf Phasen: Voraussetzungsprüfung für die Migration, Herunterfahren des virtuellen Computers (optionaler Schritt), Vorbereiten der Migration, Erstellen eines virtuellen Azure-Computers, Starten der Azure-VM.
- **Nicht zutreffend:** Wenn die VM erfolgreich migriert wurde und/oder wenn Sie die Replikation beendet haben, ändert sich der Status in nicht zutreffend. Sobald Sie die Replikation beenden und der Vorgang erfolgreich abgeschlossen wurde, wird der virtuelle Computer aus der Liste der replizierbaren Computer entfernt. Sie finden den virtuellen Computer auf der Registerkarte virtuelle Computer im Replikations-Assistenten.

> [!Note]
> Einige VMs befinden sich in der Warteschlange, um minimale Auswirkungen auf die Quellumgebung aufgrund des IOPS-Speicherverbrauchs sicherzustellen. Diese VMs werden basierend auf der Planungslogik verarbeitet (siehe nächster Abschnitt).

## <a name="scheduling-logic"></a>Planungslogik

Die erste Replikation wird geplant, wenn die Replikation für einen virtuellen Computer konfiguriert wird. Darauf folgen inkrementelle Replikationen (Deltareplikation).

Deltareplikationszyklen werden wie folgt geplant:

- Der erste Deltareplikationszyklus wird unmittelbar nach Abschluss des ersten Replikationszyklus geplant.
- Die nächsten Deltareplikationszyklen werden gemäß der folgenden Logik geplant: max. [(Zeit vorheriger Deltareplikationszyklus/2), 1 Stunde]

Das heißt, die nächste Deltareplikation wird nicht vor einer Stunde geplant. Beispiel: Wenn ein Deltareplikationszyklus für eine VM vier Stunden dauert, wird der nächste Deltareplikationszyklus in zwei Stunden geplant, nicht in der nächsten Stunde.

> [!Note]
> Die Planungslogik ist nach Abschluss der ersten Replikation anders. Der erste Deltazyklus wird unmittelbar nach Abschluss der ersten Replikation geplant, und die nachfolgenden Zyklen folgen der oben beschriebenen Planungslogik.

- Wenn Sie die Migration auslösen, wird für den virtuellen Computer vor der Migration ein bedarfsbasierter Deltareplikationszyklus (Deltareplikationszyklus vor dem Failover) ausgeführt.

**Priorisieren von VMs für verschiedene Phasen der Replikation**

- Laufende VM-Replikationen werden gegenüber geplanten Replikationen (neue Replikationen) priorisiert.
- Der Zyklus vor dem Failover (bedarfsbasierte Deltareplikation) hat die höchste Priorität, gefolgt vom ersten Replikationszyklus. Der Deltareplikationszyklus hat die geringste Priorität.

Das heißt, wenn ein Migrationsvorgang ausgelöst wird, wird der bedarfsbasierte Replikationszyklus für den virtuellen Computer geplant und andere laufende Replikationen stehen wieder hintenan, wenn sie um Ressourcen konkurrieren.

**Einschränkungen:**

Wir verwenden die folgenden Einschränkungen, um sicherzustellen, dass wir die IOPS-Grenzwerte für die SANs nicht überschreiten.

- Jede Azure Migrate Appliance unterstützt die parallele Replikation von 52 Datenträgern.
- Jeder ESXi-Host unterstützt acht Datenträger. Jeder ESXi-Host verfügt über einen NFC-Puffer von 32 MB. Daher können wir acht Datenträger auf dem Host planen (jeder Datenträger benötigt 4 MB Puffer für IR, DR).
- Jeder Datenspeicher kann maximal 15 Datenträgermomentaufnahmen enthalten. Die einzige Ausnahme ist, wenn einem virtuellen Computer mehr als 15 Datenträger angefügt sind.

## <a name="scale-out-replication"></a>Replikation für horizontale Skalierung

Azure Migrate unterstützt die gleichzeitige Replikation von 500 virtuellen Computern. Wenn Sie planen, mehr als 300 virtuelle Computer zu replizieren, müssen Sie eine Appliance für die horizontale Skalierung bereitstellen. Die Appliance für die horizontale Skalierung ähnelt einer primären Azure Migrate Appliance, besteht aber nur aus einem Gateway-Agent, um die Datenübertragung zu Azure zu vereinfachen. Das folgende Diagramm zeigt die empfohlene Methode zur Verwendung der Appliance für die horizontale Skalierung.

![Konfiguration für die horizontale Skalierung](./media/concepts-vmware-agentless-migration/scale-out-configuration.png)


Nachdem Sie die primäre Appliance konfiguriert haben, können Sie die Appliance für die horizontale Skalierung jederzeit bereitstellen, aber dies ist nicht erforderlich, bis 300 VMs gleichzeitig repliziert werden. Wenn 300 VMs gleichzeitig repliziert werden, müssen Sie die Appliance für die horizontale Skalierung bereitstellen, um fortzufahren.

## <a name="stop-replication"></a>Beenden der Replikation

Wenn Sie die Replikation beenden, werden die verwalteten Zwischendatenträger (Seed-Datenträger), die während der Replikation erstellt wurden, gelöscht. Der virtuelle Computer, für den die Replikation beendet wird, kann mit den üblichen Schritten repliziert und erneut migriert werden.

Sie können die Replikation an zwei Punkten beenden:

-  Wenn die Replikation noch läuft
-  Wenn die Migration für einen virtuellen Computer abgeschlossen ist

Als bewährte Methode sollten Sie die Replikation immer beenden, nachdem die VM erfolgreich zu Azure migriert wurde, um sicherzustellen, dass keine zusätzlichen Gebühren für Speichertransaktionen auf den verwalteten Zwischendatenträgern (Seed-Datenträger) entstehen.

In einigen Fällen werden Sie feststellen, dass das Beenden der Replikation einige Zeit dauert. Dies liegt daran, dass der fortlaufende Replikationszyklus bei jedem Beenden der Replikation abgeschlossen wird (nur wenn sich der virtuelle Computer in der Deltasynchronisierung befindet), bevor die Artefakte gelöscht werden.

## <a name="impact-of-churn"></a>Auswirkung des Churn

Wir versuchen, die Menge der Datenübertragungen in jedem Replikationszyklus zu minimieren, indem wir zulassen, dass sich die Daten so weit wie möglich falten, bevor wir den nächsten Zyklus planen. Da bei der Replikation ohne Agent Daten zusammengelegt werden, ist das _Änderungsmuster_ wichtiger als die _Änderungsrate_. Wenn eine Datei immer wieder geschrieben wird, hat die Rate keine große Auswirkung. Dagegen verursacht ein Muster, bei dem in jeden zweiten Sektor geschrieben wird, im nächsten Zyklus viele Änderungen.

## <a name="management-of-replication"></a>Verwaltung der Replikation

### <a name="throttling"></a>Drosselung

Sie können die Replikationsbandbreite mithilfe von _NetQosPolicy_ erhöhen oder verringern. Das in der  _NetQosPolicy_ zu verwendende _AppNamePrefix_ ist „GatewayWindowsService.exe“. 

Sie können eine Richtlinie auf der Azure Migrate-Appliance erstellen, um den Replikationsdatenverkehr von der Appliance zu drosseln, indem Sie eine Richtlinie wie die folgende erstellen:

`New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB`

> [!NOTE]
> Dies gilt für alle replizierenden VMs von der Azure Migrate Appliance gleichzeitig.

Sie können die Replikationsbandbreite auch nach einem Zeitplan erhöhen und verringern, indem Sie das [Beispielskript](./common-questions-server-migration.md) verwenden.

### <a name="blackout-window"></a>Blackout-Fenster

Azure Migrate bietet einen konfigurationsbasierten Mechanismus, über den Kunden das Zeitintervall angeben können, in dem keine Replikationen fortgesetzt werden sollen. Dieses Zeitintervall wird als Blackout-Fenster bezeichnet. Ein Blackout-Fenster kann in mehreren Szenarien notwendig sein, z. B. wenn die Quellumgebung ressourcenbeschränkt ist oder wenn Kunden möchten, dass die Replikation nur außerhalb der Geschäftszeiten erfolgt usw.

> [!NOTE]
> Die vorhandenen Replikationszyklen am Anfang des Blackout-Fensters werden abgeschlossen, bevor die Replikation angehalten wird.

Ein Blackout-Fenster kann für die Appliance angegeben werden, indem die Datei GatewayDataWorker.json in C:\ProgramData\Microsoft Azure\Config  erstellt/aktualisiert wird. Eine typische Datei hat die folgende Form:

```
{
    
    "BlackoutWindows": "List of blackout windows"
    
}
```

    
Die Liste der Blackout-Fenster ist eine durch eine "|" getrennte Zeichenfolge im Format „Wochentag;Startzeit;Dauer“. Die Dauer kann in Tagen, Stunden und Minuten angegeben werden. Das Blackout-Fenster kann beispielsweise wie folgt angegeben werden:

```
{
    
    "BlackoutWindows": "Monday;7:00;7h | Tuesday;8:00;1d7h | Wednesday;16:00;1d | Thursday;18:00;5h | Friday;13:00;8m"
    
}
```
    
Der erste Wert im obigen Beispiel zeigt ein Blackout-Fenster, das jeden Montag um 7:00 Uhr Ortszeit (Zeit auf der Appliance) beginnt und sieben Stunden dauert.

Sobald die Datei GatewayDataWorker.json mit diesen Inhalten erstellt/aktualisiert wurde, muss der Gatewaydienst auf der Appliance neu gestartet werden, damit diese Änderungen wirksam werden.

Im Szenario mit horizontaler Skalierung werden die Blackout-Fenster von der primären Appliance und der Appliance für die horizontale Skalierung unabhängig voneinander unterstützt. Als bewährte Methode empfiehlt es sich, die Fenster applianceübergreifend konsistent zu halten.

## <a name="next-steps"></a>Nächste Schritte
[Migrieren von virtuellen VMware-Computern](tutorial-migrate-vmware.md) ohne Agents
