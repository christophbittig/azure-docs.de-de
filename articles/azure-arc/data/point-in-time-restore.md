---
title: Wiederherstellen einer Datenbank in einer Azure Arc-fähigen SQL Managed Instance zu einem früheren Zeitpunkt
description: Erklärt, wie man eine Datenbank zu einem bestimmten Zeitpunkt auf einer Azure Arc-fähigen SQL Managed Instance wiederherstellt.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401094"
---
#  <a name="perform-a-point-in-time-restore"></a>Eine zeitpunktbezogene Wiederherstellung durchführen

Verwenden Sie die Point-in-Time Restore (PITR), um eine Datenbank als Kopie einer anderen Datenbank zu einem früheren Zeitpunkt zu erstellen. Dieser Artikel beschreibt, wie Sie eine Point-in-Time-Wiederherstellung einer Datenbank in einer Azure Arc-fähigen SQL Managed Instance durchführen.

Mit der Point-in-Time-Wiederherstellung kann eine Datenbank wiederhergestellt werden:

- Von einer bestehenden Datenbank
- in eine neue Datenbank auf derselben Azure Arc-fähigen SQL-Manager-Instanz

Sie können eine Datenbank zu einem bestimmten Zeitpunkt innerhalb einer vorkonfigurierten Aufbewahrungseinstellung wiederherstellen.

Die Point-in-Time-Wiederherstellung ist eine Einstellung auf Instanzebene mit zwei Eigenschaften - Wiederherstellungspunktziel (RPO) und Aufbewahrungszeitraum. RPO bestimmt, wie oft die Transaktionsprotokollsicherungen durchgeführt werden. RPO ist die Zeitspanne, in der ein Datenverlust zu erwarten ist. RPO wird in Minuten angegeben. Die Aufbewahrungsfrist bestimmt, wie lange (in Tagen) die Datenbank-Backups von Azure Arc-fähigen Datendiensten gespeichert werden sollen. Die Aufbewahrungsfrist gilt für Voll-, Differenz- und Transaktionsprotokoll-Backups.  

Derzeit kann eine Datenbank mit Point-in-Time Restore wiederhergestellt werden:

- Von einer bestehenden Datenbank auf einer Instanz
- In eine neue Datenbank auf der gleichen Instanz

## <a name="limitations"></a>Einschränkungen

Die Point-in-Time-Wiederherstellung auf eine SQL Managed Instance mit Azure Arc-Unterstützung unterliegt den folgenden Einschränkungen:

- Eine punktuelle Wiederherstellung einer gesamten SQL Managed Instance mit Azure Arc-Unterstützung ist nicht möglich. 
- Eine Azure Arc-aktivierte SQL Managed Instance, die mit Hochverfügbarkeit (Preview) bereitgestellt wird, unterstützt derzeit keine Point-in-Time-Wiederherstellung.
- Sie können nur auf dieselbe Azure Arc-fähige SQL Managed Instance wiederherstellen.
- Point-in-Time Restore kann nur über eine yaml-Datei durchgeführt werden.
- Ältere Sicherungsdateien, die den vorkonfigurierten Aufbewahrungszeitraum überschritten haben, müssen manuell bereinigt werden.
- Das Umbenennen einer Datenbank startet eine neue Sicherungskette in einem neuen Ordner.
- Das Löschen und Erstellen verschiedener Datenbanken mit gleichem Namen wird derzeit nicht korrekt gehandhabt.

## <a name="description"></a>BESCHREIBUNG

Point-in-Time Restore stellt eine Datenbank zu einem bestimmten Zeitpunkt wieder her. Um eine Datenbank zu einem bestimmten Zeitpunkt wiederherzustellen, wenden Azure Arc-fähige Datendienste die Backup-Dateien in einer bestimmten Reihenfolge an. Beispiel:

1. Vollständige Sicherung
2. Differenzielle Sicherung 
3. Eine oder mehrere Transaktionsprotokollsicherungen

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Point-in-Time-Wiederherstellung":::

SQL Managed Instance mit Azure Arc-Unterstützung verfügt über eine integrierte Funktion zur Wiederherstellung zu einem bestimmten Zeitpunkt. Jedes Mal, wenn eine neue Datenbank auf einer SQL Managed Instance mit Azure Arc-Unterstützung erstellt oder wiederhergestellt wird, werden automatisch Backups erstellt. 

Es gibt zwei Parameter, die sich auf die Möglichkeit der zeitpunktbezogenen Wiederherstellung auswirken:

- RPO (Recovery Point Objective) 
- Beibehaltungsdauer

## <a name="create-a-database-from-a-point-in-time"></a>Erstellen einer Datenbank ab einem bestimmten Zeitpunkt

Nachfolgend sind die Schritte zur Wiederherstellung einer Datenbank auf derselben Azure Ar-aktivierten SQL Managed Instance unter Verwendung der `kubectl`:

1. Erstellen Sie eine Aufgabe für den Wiederherstellungsvorgang. Erstellen Sie eine .yaml-Datei mit den Wiederherstellungsparametern.

   Beispiel:

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   Bearbeiten Sie die obige yaml-Datei:

   - `metadata` > `name` - Name für die benutzerdefinierte Ressource (CR) der Aufgabe
   - `metadata` > `namespace` - Namespace der SQL Managed Instance mit Azure Arc-Unterstützung
   - `source` > `name` - Name der SQL Managed Instance mit Azure Arc-Unterstützung
   - `source` > `database` - Name der **source** auf der SQL Managed Instance mit Azure Arc-Unterstützung, von der wiederhergestellt werden soll
   - `restorePoint` - Zeitpunkt der Wiederherstellung, in "UTC" Datumszeit.
   - `destination` > `name` - Name der SQL Managed Instance mit Azure Arc-Unterstützung
   - `destination` > `database` - Name der **destination** auf der SQL Managed Instance mit Azure Arc-Unterstützung


   > [!NOTE] 
   > Der Name der Azure Arc-fähigen SQL Managed Instance sollte mit dem Namen des Ziels übereinstimmen.

2. Erstellen Sie eine Aufgabe, um die Point-in-Time-Restore-Operation zu initiieren

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. Überprüfen Sie den Status der Wiederherstellung

   Führen Sie den folgenden Befehl aus, um den Status des Wiederherstellungsvorgangs zu überprüfen.

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   Ersetzen Sie `<namespace>` durch den Namespace, in dem sich die SQL-Instanz befindet.

Sobald der Status der Wiederherstellungsaufgabe **Erledigt** anzeigt, sollte die neue Datenbank verfügbar sein. 

## <a name="troubleshoot-failed-restore-operations"></a>Fehlerbehebung bei fehlgeschlagenen Wiederherstellungsvorgängen

Wenn der Status der Wiederherstellungsaufgabe **Fehlgeschlagen** anzeigt, führen Sie den folgenden Befehl aus, um in den Ereignissen nach der Ursache zu suchen.

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

Beispiel:
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>Aktivieren/Deaktivieren automatisierter Sicherungen

Der Dienst Point-in-Time-Restore (PITR) ist standardmäßig mit den folgenden Einstellungen aktiviert:

- Recovery Point Objective (RPO) von 300 Minuten. Neben 0 (null) sind Werte zwischen 300 und 600 (in Sekunden) zulässig.

Mit dem RPO wird der Dienst so eingestellt, dass er standardmäßig alle 300 Sekunden oder 5 Minuten Log-Backups für alle Datenbanken auf der Azure Arc-fähigen verwalteten SQL-Instanz erstellt. Dieser Wert kann in 0 geändert werden, um das Erstellen von Sicherungen zu deaktivieren, oder in einen höheren Wert in Sekunden, abhängig von der RPO-Anforderung für die Datenbanken auf der SQL-Instanz. 

Sie können die automatischen Backups für eine bestimmte Instanz einer Azure Arc-fähigen SQL-Managerinstanz deaktivieren oder die Standardeinstellungen ändern. Der PITR-Dienst selbst kann nicht deaktiviert werden.

Sie können das RPO bearbeiten, indem Sie den Wert für die Eigenschaft `recoveryPointObjectiveInSeconds` wie folgt ändern:

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Dieser Befehl öffnet die benutzerdefinierte Ressourcenspezifikation für die Azure Arc-fähige SQL Managed Instance in Ihrem Standard-Editor. Suchen Sie die Einstellung `backup` unter `spec`:

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

Bearbeiten Sie den Wert für `recoveryPointObjectiveInSeconds` im Editor, und speichern Sie die Änderungen, damit die neue Einstellung wirksam wird. 

> [!NOTE]
> Wenn Sie die RPO-Einstellung bearbeiten, wird der Pod mit SQL Managed Instance mit Azure Arc-Unterstützung neu gestartet. 

## <a name="monitor-backups"></a>Backups überwachen

Die Sicherungen werden im Ordner `/var/opt/mssql/backups/archived/<dbname>/<datetime>` gespeichert. Dabei ist `<dbname>` der Name der Datenbank und `<datetime>` ein Zeitstempel im UTC-Format für den Anfang jeder vollständigen Sicherung. Jedes Mal, wenn eine vollständige Sicherung initiiert wird, wird ein neuer Ordner mit der vollständigen Sicherung und allen nachfolgenden differenziellen und Transaktionsprotokollsicherungen in diesem Ordner erstellt. Die aktuellste vollständige Sicherung und die nachfolgenden differenziellen und Transaktionsprotokollsicherungen werden im Ordner `/var/opt/mssql/backups/current/<dbname><datetime>` gespeichert.

### <a name="clean-up"></a>Bereinigen 

Wenn Sie ältere Sicherungen löschen möchten, um Speicherplatz freizugeben oder weil Sie sie nicht mehr benötigen, können Sie alle Ordner im Ordner `/var/opt/mssql/backups/archived/` entfernen. Das Entfernen von Ordnern in der Mitte einer Zeitachse kann sich auf die Möglichkeit auswirken, eine Wiederherstellung zu einem Zeitpunkt während dieses Zeitfensters durchzuführen. Löschen Sie zuerst die ältesten Ordner, um eine kontinuierliche Wiederherstellbarkeit zu gewährleisten. 

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über Features und Funktionen von Azure Arc-fähigen SQL Managed Instance-Instanzen](managed-instance-features.md).

[Beginnen Sie mit der Erstellung eines Datencontrollers](create-data-controller.md)

[Wurde bereits ein Datencontroller erstellt? Erstellen von Azure Arc-fähigen SQL Managed Instance-Instanzen](create-sql-managed-instance.md)
