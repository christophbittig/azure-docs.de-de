---
title: Konsistente Datenbank-Snapshots mit erweitertem Pre-Post-Skript-Framework
description: Erfahren Sie, wie Sie mit Azure Backup konsistente Datenbank-Snapshots erstellen können, indem Sie Azure VM-Backup nutzen und vorgefertigte Skripte verwenden.
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa9f4ba3dc344e07a3383c6f8081c9304e3ebbeb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092203"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>Verbesserte Pre-Post-Skripte für einen konsistenten Datenbank-Snapshot

Der Azure-Backup-Dienst bietet bereits ein [_Pre-Post-_ Skript-Framework](./backup-azure-linux-app-consistent.md), um die Anwendungskonsistenz in Linux-VMs mit Azure Backup zu erreichen. Dies beinhaltet den Aufruf eines Pre-Skripts (um die Anwendungen in den Ruhezustand zu versetzen), bevor ein Snapshot der Festplatten erstellt wird, und den Aufruf eines Post-Skripts (Befehle zum Aufheben des Ruhezustands der Anwendungen), nachdem der Snapshot abgeschlossen ist, um die Anwendungen in den normalen Modus zurückzuversetzen.

Das Erstellen, Debuggen und Pflegen von elektronischen Pre-/Post-Skripten kann eine Herausforderung sein. Um diese Komplexität zu beseitigen, bietet Azure Backup eine vereinfachte Pre-/Post-Skript-Erfahrung für Marquee-Datenbanken, um einen konsistenten Anwendungs-Snapshot mit minimalem Overhead zu erhalten.

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/linux-application-consistent-snapshot.png" alt-text="Diagramm, das den Linux-anwendungskonsistenten Snapshot von Azure Backup zeigt.":::

Der neue, _verbesserte_ Pre-Post-Skript-Rahmen hat die folgenden Hauptvorteile:

- Diese Pre-Post-Skripte werden zusammen mit der Backup-Erweiterung direkt in Azure-VMs installiert. Dadurch entfällt das Erstellen und Herunterladen von einem externen Speicherort.
- Sie können die Definition und den Inhalt von Pre-Post-Skripten auf [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) einsehen und sogar Vorschläge und Änderungen einreichen. Sie können sogar Vorschläge und Änderungen über GitHub einreichen, die dann geprüft und zum Nutzen der gesamten Community hinzugefügt werden.
- Sie können sogar neue Pre-Post-Skripte für andere Datenbanken über [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) hinzufügen, _die dann geprüft und bearbeitet werden, um der breiteren Gemeinschaft zu dienen_.
- Der robuste Rahmen ist in der Lage, Szenarien wie Fehler bei der Ausführung von Skripten oder Abstürze zu bewältigen. In jedem Fall wird das Post-Skriptum automatisch ausgeführt, um alle im Pre-Skriptum vorgenommenen Änderungen rückgängig zu machen.
- Der Rahmen bietet auch einen _Nachrichtenkanal_ für externe Tools, um Aktualisierungen abzurufen und ihren eigenen Aktionsplan zu jeder Nachricht/jedem Ereignis zu erstellen.

## <a name="solution-flow-preview"></a>Lösungsfluss (Vorschau)

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/solution-flow.png" alt-text="Schematische Darstellung des Lösungsablaufs.":::

## <a name="support-matrix"></a>Unterstützungsmatrix

Die folgende Liste von Datenbanken fällt unter den erweiterten Rechtsrahmen:

- [Oracle (allgemein verfügbar)](../virtual-machines/workloads/oracle/oracle-database-backup-azure-backup.md) - [Link zur Support-Matrix](backup-support-matrix-iaas.md#support-matrix-for-managed-pre-post-scripts-for-linux-databases)
- MySQL (Vorschauversion)

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen nur eine Konfigurationsdatei, _workload.conf_ in `/etc/azure`, ändern, um die Verbindungsdetails anzugeben. Dadurch kann Azure Backup eine Verbindung zur jeweiligen Anwendung herstellen und Pre- und Post-Skripte ausführen. Die Konfigurationsdatei enthält die folgenden Parameter.

```json
[workload]
# valid values are mysql, oracle
workload_name =
command_path = 
linux_user =
credString = 
ipc_folder = 
timeout =
```

In der folgenden Tabelle sind die Parameter beschrieben:

|Parameter  |Obligatorisch.  |Erklärung  |
|---------|---------|---------|
|Workload_name     |   Ja      |     Dieser enthält den Namen der Datenbank, für die Sie eine konsistente Sicherung der Anwendung benötigen. Die derzeit unterstützten Werte sind `oracle` oder `mysql`.    |
|command_path/configuration_path     |         |   Dieser enthält den Pfad zur Workload-Binärdatei. Dies ist kein Pflichtfeld, wenn die Workload-Binärdatei als Pfadvariable festgelegt ist.      |
|linux_user     |    Ja     |   Dieser enthält den Benutzernamen des Linux-Benutzers mit Zugriff auf das Datenbank-Benutzer-Login. Wenn dieser Wert nicht gesetzt ist, wird root als Standardbenutzer betrachtet.      |
|credString     |         |     Dies steht für die Zeichenfolge der Anmeldeinformationen zur Verbindung mit der Datenbank. Dieser enthält die gesamte Anmeldezeichenfolge.    |
|ipc_folder     |         |   Die Workload kann nur in bestimmte Dateisystempfade schreiben. Sie müssen hier diesen Ordnerpfad angeben, damit das Pre-Skript die Zustände in diesen Ordnerpfad schreiben kann.      |
|timeout     |    Ja     |     Dies ist die maximale Zeitspanne, für die die Datenbank in den Ruhezustand versetzt wird. Der Standardwert ist 90 Sekunden. Es wird nicht empfohlen, einen Wert von weniger als 60 Sekunden einzustellen.    |

> [!NOTE]
> Die JSON-Definition ist eine Vorlage, die der Azure Backup-Dienst an eine bestimmte Datenbank anpassen kann. Um die Konfigurationsdatei für jede Datenbank zu verstehen, lesen Sie bitte das [Handbuch der jeweiligen Datenbank](#support-matrix).

Die allgemeine Erfahrung bei der Verwendung des erweiterten Pre-Post-Skript-Rahmens ist wie folgt:

- Vorbereiten der Datenbankumgebung
- Bearbeiten der Konfigurationsdatei
- Auslösen der VM-Sicherung
- Wiederherstellung von VMs oder Festplatten/Dateien vom konsistenten Wiederherstellungspunkt der Anwendung nach Bedarf.

## <a name="build-a-database-backup-strategy"></a>Erstellen einer Datenbank-Backup-Strategie

### <a name="using-snapshots-instead-of-streaming"></a>Schnappschüsse statt Streaming verwenden

In der Regel verwenden Datenbankadministratoren in ihrer Sicherungsstrategie Streaming-Backups (z. B. vollständige, differenzielle oder inkrementelle Backups) und Protokolle. Nachfolgend sind einige der wichtigsten Eckpunkte des Entwurfs aufgeführt.

- **Leistung und Kosten**: Ein tägliches Full + Logs wäre bei der Wiederherstellung am schnellsten, verursacht aber erhebliche Kosten. Die Einbeziehung der differenziellen/inkrementellen Streaming-Backup-Variante reduziert die Kosten, kann aber die Wiederherstellungsleistung beeinträchtigen. Snapshots bieten jedoch die beste Kombination aus Leistung und Kosten.  Da Snapshots von Natur aus inkrementell sind, haben sie die geringsten Auswirkungen auf die Leistung während der Sicherung, werden schnell wiederhergestellt und sparen zudem Kosten.
- **Auswirkungen auf die Datenbank/Infrastruktur**: Die Leistung eines Streaming-Backups hängt von den zugrunde liegenden Speicher-IOPS und der verfügbaren Netzwerkbandbreite ab, wenn der Stream auf einen entfernten Standort ausgerichtet ist. Bei Snapshots besteht diese Abhängigkeit nicht, und der Bedarf an IOPS und Netzwerkbandbreite ist deutlich geringer.
- **Wiederverwendbarkeit**: Die Befehle zum Auslösen der verschiedenen Streaming-Backup-Typen sind für jede Datenbank unterschiedlich. Skripte können also nicht einfach wiederverwendet werden. Wenn Sie verschiedene Sicherungsarten verwenden, sollten Sie auch die Abhängigkeitskette bewerten, um den Lebenszyklus aufrechtzuerhalten. Für Snapshots ist es einfach, ein Skript zu schreiben, da es keine Abhängigkeitskette gibt.
- **Langfristige Aufbewahrung**: Vollständige Backups sind für die langfristige Aufbewahrung immer von Vorteil, da sie unabhängig voneinander verschoben und wiederhergestellt werden können. Für operative Backups mit kurzfristiger Aufbewahrung sind Snapshots jedoch vorteilhaft.

Daher ist ein täglicher Snapshot + Protokolle mit gelegentlicher Vollsicherung zur langfristigen Aufbewahrung die beste Sicherungsstrategie für Datenbanken.

### <a name="log-backup-strategy"></a>Sicherungsstrategie

Das erweiterte Pre-Post-Skript-Framework basiert auf Azure VM Backup, das die Sicherung einmal pro Tag plant. Daher ist das Datenverlustfenster mit einem RPO von 24 Stunden für Produktionsdatenbanken nicht geeignet. Ergänzt wird diese Lösung durch eine Log-Backup-Strategie, bei der die Log-Backups explizit gestreamt werden.

[NFS auf Blob](../storage/blobs/network-file-system-protocol-support.md) und [NFS auf AFS (Preview)](../storage/files/files-nfs-protocol.md) helfen bei der einfachen Montage von Volumes direkt auf Datenbank-VMs und nutzen Datenbank-Clients zur Übertragung von Log-Backups. Das Zeitfenster für den Datenverlust, d. h. das RPO, hängt von der Häufigkeit der Log-Backups ab. Außerdem müssen NFS-Ziele nicht sehr leistungsfähig sein, da Sie möglicherweise kein regelmäßiges Streaming (vollständig und inkrementell) für operative Sicherungen auslösen müssen, nachdem Sie eine Datenbank mit konsistenten Snapshots haben.

>[!NOTE]
>Das erweiterte Pre-Skript sorgt in der Regel dafür, dass alle Log-Transaktionen auf dem Weg zum Ziel der Log-Sicherung geleert werden, bevor die Datenbank für die Erstellung eines Snapshots stillgelegt wird. Daher sind die Snapshots bei der Wiederherstellung datenbankkonsistent und zuverlässig.

### <a name="recovery-strategy"></a>Wiederherstellungsstrategie

Sobald die konsistenten Datenbank-Snapshots erstellt und die Log-Backups auf ein NFS-Volume gestreamt sind, könnte die Wiederherstellungsstrategie der Datenbank die Wiederherstellungsfunktionalität von Azure VM-Backups nutzen. Die Fähigkeit der Log-Backups wird zusätzlich über den Datenbank-Client angewendet. Nachfolgend sind einige Optionen für die Wiederherstellungsstrategie aufgeführt:

- Erstellen Sie neue VMs vom konsistenten Wiederherstellungspunkt der Datenbank aus. Die VM sollte bereits mit dem Log-Mount-Punkt verbunden sein. Verwenden Sie Datenbank-Clients, um Wiederherstellungsbefehle für die Point-in-Time-Wiederherstellung auszuführen.
- Erstellen Sie Datenträger vom konsistenten Wiederherstellungspunkt der Datenbank und hängen Sie sie an eine andere Ziel-VM an. Hängen Sie dann das Protokollziel ein und verwenden Sie Datenbank-Clients, um Wiederherstellungsbefehle für die Point-in-Time-Wiederherstellung auszuführen
- Verwenden Sie die Option Dateiwiederherstellung und erstellen Sie ein Skript. Führen Sie das Skript auf der Ziel-VM aus und hängen Sie den Wiederherstellungspunkt als iSCSI-Datenträger an. Verwenden Sie dann Datenbank-Clients, um die datenbankspezifischen Validierungsfunktionen auf den angeschlossenen Festplatten auszuführen und die Sicherungsdaten zu validieren. Verwenden Sie außerdem Datenbank-Clients zum Exportieren/Wiederherstellen einiger Tabellen/Dateien, anstatt die gesamte Datenbank wiederherzustellen.
- Verwenden Sie die regionsübergreifende Wiederherstellungsfunktion, um die oben genannten Aktionen von der sekundären gepaarten Region aus durchzuführen, wenn eine regionale Katastrophe eintritt.

## <a name="summary"></a>Zusammenfassung

Durch die Verwendung von konsistenten Datenbank-Snapshots und Protokollen, die mit einer benutzerdefinierten Lösung gesichert werden, können Sie eine leistungsstarke und kosteneffiziente Datenbank-Backup-Lösung aufbauen, die die Vorteile von Azure VM-Backup nutzt und auch die Funktionen von Datenbank-Clients wiederverwendet.
