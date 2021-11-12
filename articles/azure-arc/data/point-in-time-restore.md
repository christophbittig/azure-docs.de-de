---
title: Wiederherstellen einer Datenbank in einer Azure Arc-fähigen SQL Managed Instance zu einem früheren Zeitpunkt
description: Erklärt, wie man eine Datenbank zu einem bestimmten Zeitpunkt auf einer Azure Arc-fähigen SQL Managed Instance wiederherstellt.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 51235a67d6f00410ee2b3db753e892cf05992baa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563262"
---
#  <a name="perform-a-point-in-time-restore"></a>Eine zeitpunktbezogene Wiederherstellung durchführen

Verwenden Sie die Zeitpunktwiederherstellung (Point-in-Time Restore, PITR), um eine Datenbank als Kopie einer anderen Datenbank zu einem Zeitpunkt in der Vergangenheit zu erstellen, der innerhalb des Aufbewahrungszeitraums liegt. In diesem Artikel erfahren Sie, wie Sie eine Zeitpunktwiederherstellung einer Datenbank in SQL Managed Instance mit Azure Arc-Unterstützung durchführen.

Mit der Point-in-Time-Wiederherstellung kann eine Datenbank wiederhergestellt werden:

- Von einer bestehenden Datenbank
- In eine neue Datenbank auf derselben SQL Managed Instance mit Azure Arc-Unterstützung

Sie können eine Datenbank zu einem bestimmten Zeitpunkt innerhalb einer vorkonfigurierten Aufbewahrungseinstellung wiederherstellen.
Sie können die Aufbewahrungseinstellung für eine SQL Managed Instance mit Azure Arc-Unterstützung wie folgt überprüfen:

Für den Konnektivitätsmodus **Direkt**:
```
az sql mi-arc show --name <SQL instance name> --resource-group <resource-group>
#Example
az sql mi-arc show --name sqlmi --resource-group myresourcegroup
```
Für den Konnektivitätsmodus **Indirekt**:
```
az sql mi-arc show --name <SQL instance name> --k8s-namespace <SQL MI namespace> --use-k8s
#Example
az sql mi-arc show --name sqlmi --k8s-namespace arc --use-k8s
```

Derzeit kann eine Datenbank mit Point-in-Time Restore wiederhergestellt werden:

- Von einer bestehenden Datenbank auf einer Instanz
- In eine neue Datenbank auf der gleichen Instanz

## <a name="automatic-backups"></a>Automatische Sicherungen

Bei SQL Managed Instance mit Azure Arc-Unterstützung ist das integrierte Feature für automatische Sicherungen aktiviert. Bei jedem Erstellen einer neuen Datenbank oder dem Wiederherstellen einer Datenbank initiiert SQL Managed Instance mit Azure Arc-Unterstützung sofort eine vollständige Sicherung und plant differenzielle Sicherungen sowie Transaktionsprotokollsicherungen automatisch. SQL Managed Instance speichert diese Sicherungen in der während der Bereitstellung angegebenen Speicherklasse. 

Mit der Zeitpunktwiederherstellung (PITR) kann eine Datenbank auf einen bestimmten Zeitpunkt innerhalb des Aufbewahrungszeitraums wiederhergestellt werden. Um eine Datenbank zu einem bestimmten Zeitpunkt wiederherzustellen, wenden Azure Arc-fähige Datendienste die Backup-Dateien in einer bestimmten Reihenfolge an. Beispiel:

1. Vollständige Sicherung
2. Differenzielle Sicherung 
3. Eine oder mehrere Transaktionsprotokollsicherungen

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Point-in-Time-Wiederherstellung":::

Derzeit werden vollständige Sicherungen einmal pro Woche, differenzielle Sicherungen alle 12 Stunden und Transaktionsprotokollsicherungen alle 5 Minuten erstellt.

## <a name="retention-period"></a>Bindungsdauer

Die Standardaufbewahrungsdauer für eine neue SQL Managed Instance mit Azure Arc-Unterstützung beträgt sieben Tage und kann mit Werten von 0 oder 1 bis 35 Tagen angepasst werden. Der Aufbewahrungszeitraum kann während der Bereitstellung der SQL Managed Instance festgelegt werden, indem die Eigenschaft `--retention-days` angegeben wird. Sicherungsdateien, die älter als der konfigurierte Aufbewahrungszeitraum sind, werden automatisch gelöscht.


## <a name="create-a-database-from-a-point-in-time-using-az"></a>Erstellen einer Datenbank ab einem bestimmten Zeitpunkt mittels az

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s
```

Sie können auch die Option `--dry-run` verwenden, um den Wiederherstellungsvorgang zu überprüfen, ohne die Datenbank tatsächlich wiederherzustellen. 

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s --dry-run
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s --dry-run
```


## <a name="create-a-database-from-a-point-in-time-using-azure-data-studio"></a>Erstellen einer Datenbank ab einem bestimmten Zeitpunkt mittels Azure Data Studio

Sie können eine Datenbank auch aus Azure Data Studio wie folgt auf einen Zeitpunkt wiederherstellen:
1. Starten von Azure Data Studio
2. Stellen Sie sicher, dass Sie über die erforderlichen Arc-Erweiterungen verfügen, wie unter [Tools](install-client-tools.md) beschrieben.
3. Herstellen einer Verbindung mit dem Azure Arc-Datencontroller
4. Erweitern Sie den Datencontrollerknoten, klicken Sie mit der rechten Maustaste auf die SQL Managed Instance mit Azure Arc-Unterstützung, und wählen Sie „Verwalten“ aus. Azure Data Studio startet das Dashboard von SQL Managed Instance.
5. Klicken Sie im Dashboard auf die Registerkarte **Sicherungen**.
6. Es sollte eine Liste der Datenbanken auf der SQL Managed Instance, die Zeitfenster für die „Früheste“ und „Neueste“ Wiederherstellung sowie ein Symbol zum Initiieren der **Wiederherstellung** angezeigt werden.
7. Klicken Sie auf das Symbol für die Datenbank, aus der sie wiederherstellen möchten. Azure Data Studio startet ein Blatt auf der rechten Seite
8. Geben Sie die erforderliche Eingabe auf dem Blatt an, und klicken Sie auf **Wiederherstellen**.

### <a name="monitor-progress"></a>Fortschritt überwachen

Wenn eine Wiederherstellung initiiert wird, wird im Kubernetes-Cluster eine Aufgabe erstellt, die die tatsächlichen Wiederherstellungsvorgänge vollständiger, differenzieller und Protokollsicherungen ausführt. Der Status dieser Aktivität kann von Ihrem Kubernetes-Cluster aus wie folgt überwacht werden:

```console
kubectl get sqlmirestoretask -n <namespace>
#Example
kubectl get sqlmirestoretask -n arc
```

Sie können weitere Details zur Aufgabe abrufen, indem Sie `kubectl describe` für die Aufgabe ausführen. Zum Beispiel:

```console
kubectl describe sqlmirestoretask <nameoftask> -n <namespace>
```

## <a name="configure-retention-period"></a>Konfigurieren des Aufbewahrungszeitraums

Der Aufbewahrungszeitraum für eine SQL Managed Instance mit Azure Arc-Unterstützung kann wie folgt abweichend von der ursprünglichen Einstellung neu konfiguriert werden:

> [!WARNING] 
> Wenn Sie die aktuelle Beibehaltungsdauer verringern, verlieren Sie die Möglichkeit, Zeitpunkte wiederherzustellen, die älter als die neue Beibehaltungsdauer sind. Sicherungen, die für die Bereitstellung von Point-in-Time-Wiederherstellungen innerhalb der neuen Beibehaltungsdauer nicht mehr benötigt werden, werden gelöscht. Wenn Sie die aktuelle Beibehaltungsdauer verringern, wird die Möglichkeit, Zeitpunkte innerhalb der neuen Beibehaltungsdauer wiederherzustellen, nicht sofort hergestellt. Sie erhalten diese Möglichkeit im Lauf der Zeit, während das System beginnt, Sicherungen länger aufzubewahren.

### <a name="change-retention-period-for-direct-connected-sql-managed-instance"></a>Ändern des Aufbewahrungszeitraums für eine **direkt** verbundene SQL Managed Instance

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
```

### <a name="change-retention-period-for-indirect-connected-sql-managed-instance"></a>Ändern des Aufbewahrungszeitraums für eine **indirekt** verbundene SQL Managed Instance

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days <retentiondays>
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 10
```

## <a name="disable-automatic-backups"></a>Deaktivieren automatischer Sicherungen

Sie können die automatischen Sicherungen für eine bestimmte Instanz einer SQL Managed Instance mit Azure Arc-Unterstützung deaktivieren, indem Sie die Einstellung `--retention-days` wie folgt auf 0 festlegen.

> [!WARNING]
> Wenn Sie automatische Sicherungen für eine SQL Managed Instance mit Azure Arc-Unterstützung deaktivieren, werden alle konfigurierten automatischen Sicherungen gelöscht, und Sie verlieren die Fähigkeit zum Durchführen einer Zeitpunktwiederherstellung. Sie können die Eigenschaft `retention-days` so ändern, dass automatische Sicherungen bei Bedarf erneut initiiert werden.

### <a name="disable-automatic-backups-for-direct-connected-sql-managed-instance"></a>Deaktivieren automatischer Sicherungen für eine **direkt** verbundene SQL Managed Instance

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
```

### <a name="disable-automatic-backups-for-indirect-connected-sql-managed-instance"></a>Deaktivieren automatischer Sicherungen für eine **indirekt** verbundene SQL Managed Instance

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 0
```

## <a name="monitor-backups"></a>Backups überwachen

Die Sicherungen werden im Ordner `/var/opt/mssql/backups/archived/<dbname>/<datetime>` gespeichert. Dabei ist `<dbname>` der Name der Datenbank und `<datetime>` ein Zeitstempel im UTC-Format für den Anfang jeder vollständigen Sicherung. Jedes Mal, wenn eine vollständige Sicherung initiiert wird, wird ein neuer Ordner mit der vollständigen Sicherung und allen nachfolgenden differenziellen und Transaktionsprotokollsicherungen in diesem Ordner erstellt. Die aktuellste vollständige Sicherung und die nachfolgenden differenziellen und Transaktionsprotokollsicherungen werden im Ordner `/var/opt/mssql/backups/current/<dbname><datetime>` gespeichert.

## <a name="limitations"></a>Einschränkungen

Die Zeitpunktwiederherstellung in SQL Managed Instance mit Azure Arc-Unterstützung ist mit folgenden Einschränkungen verbunden:

- Eine Zeitpunktwiederherstellung einer ganzen SQL Managed Instance mit Azure Arc-Unterstützung ist nicht möglich. 
- Eine Azure Arc-aktivierte SQL Managed Instance, die mit Hochverfügbarkeit (Preview) bereitgestellt wird, unterstützt derzeit keine Point-in-Time-Wiederherstellung.
- Sie können nur auf dieselbe Azure Arc-fähige SQL Managed Instance wiederherstellen.
- Das Löschen und Erstellen verschiedener Datenbanken mit gleichem Namen wird derzeit nicht korrekt gehandhabt.
- Die Angabe eines zukünftigen Datums beim Ausführen des Wiederherstellungsvorgangs mit ```--dry-run``` führt zu einem Fehler.




## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über Features und Funktionen von Azure Arc-aktivierten SQL Managed Instance-Instanzen](managed-instance-features.md)

[Beginnen Sie mit der Erstellung eines Datencontrollers](create-data-controller.md)

[Wurde bereits ein Datencontroller erstellt? Erstellen von Azure Arc-fähigen SQL Managed Instance-Instanzen](create-sql-managed-instance.md)
