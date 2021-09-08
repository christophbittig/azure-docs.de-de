---
title: Wiederherstellen einer Datenbank zu einem Zeitpunkt
description: Erfahren Sie, wie Sie eine Zeitpunktwiederherstellung durchführen.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346950"
---
#  <a name="perform-a-point-in-time-restore"></a>Durchführen einer Zeitpunktwiederherstellung


SQL Managed Instance mit Azure Arc-Unterstützung bietet viele PaaS-ähnliche Funktionen. Eine solche Funktion ist die Möglichkeit, eine Datenbank zu einem Zeitpunkt innerhalb des vorkonfigurierten Aufbewahrungszeitraums wiederherzustellen. In diesem Artikel erfahren Sie, wie Sie eine Zeitpunktwiederherstellung einer Datenbank in SQL Managed Instance mit Azure Arc-Unterstützung durchführen.

Die Zeitpunktwiederherstellung ist eine Einstellung auf Instanzebene mit zwei Eigenschaften: Recovery Point Objective (RPO) und Aufbewahrungsdauer (Retention Time, RT). Die Einstellung für Recovery Point Objective legt fest, wie oft Sicherungen der Transaktionsprotokolle erstellt werden. Dies ist auch die Zeitspanne, in der Datenverluste zu erwarten sind. Die Aufbewahrungsdauer (RT) gibt an, wie lange die Sicherungen (vollständig, differenziell und Transaktionsprotokoll) aufbewahrt werden.  

Derzeit kann mit der Zeitpunktwiederherstellung eine Datenbank wie folgt wiederhergestellt werden:

- aus einer vorhandenen Datenbank einer SQL Instanz
- in eine neue Datenbank derselben SQL Instanz

### <a name="limitations"></a>Einschränkungen

Die Zeitpunktwiederherstellung in SQL Managed Instance mit Azure Arc-Unterstützung ist mit folgenden Einschränkungen verbunden:

- Sie können nur eine Wiederherstellung in dieselbe Instanz von SQL Managed Instance mit Azure Arc-Unterstützung durchführen.
- Die Zeitpunktwiederherstellung kann nur über eine YAML-Datei ausgeführt werden. 
- Ältere Sicherungsdateien, die über den vorkonfigurierten Aufbewahrungszeitraum hinausgehen, müssen manuell bereinigt werden.
- Durch das Umbenennen einer Datenbank wird eine neue Sicherungskette in einem neuen Ordner gestartet.
- Das Löschen und Erstellen verschiedener Datenbanken mit demselben Namen wird derzeit nicht ordnungsgemäß verarbeitet.

### <a name="edit-pitr-settings"></a>Bearbeiten von Einstellungen für die Zeitpunktwiederherstellung

##### <a name="enabledisable-automated-backups"></a>Aktivieren/Deaktivieren automatisierter Sicherungen

Der Dienst für die Zeitpunktwiederherstellung (Point-In-Time Restore, PITR) ist standardmäßig mit den folgenden Einstellungen aktiviert:

- Recovery Point Objective (RPO) von 300 Minuten. Neben 0 (null) sind Werte zwischen 300 und 600 (in Sekunden) zulässig.

Dies bedeutet, dass Protokollsicherungen für alle Datenbanken in der Instanz von SQL Managed Instance mit Azure Arc-Unterstützung standardmäßig alle 300 Sekunden (5 Minuten) erstellt werden. Dieser Wert kann in 0 geändert werden, um das Erstellen von Sicherungen zu deaktivieren, oder in einen höheren Wert in Sekunden, abhängig von der RPO-Anforderung für die Datenbanken auf der SQL-Instanz. 

Der Dienst für die Zeitpunktwiederherstellung selbst kann nicht deaktiviert werden, aber die automatisierten Sicherungen für eine bestimmte Instanz von SQL Managed Instance mit Azure Arc-Unterstützung können entweder deaktiviert werden, oder Sie passen die Standardeinstellungen an.

Das RPO kann bearbeitet werden, indem der Wert für die ```recoveryPointObjectiveInSeconds```-Eigenschaft wie folgt geändert wird:

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Dadurch sollte die Spezifikation für benutzerdefinierte Ressourcen für SQL Managed Instance mit Azure Arc-Unterstützung in Ihrem Standard-Editor geöffnet werden. Suchen Sie unter ```spec``` nach ```backup```:

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

Bearbeiten Sie den Wert für ```recoveryPointObjectiveInSeconds``` im Editor, und speichern Sie die Änderungen, damit die neue Einstellung wirksam wird. 

> [!NOTE]
> Wenn Sie die RPO-Einstellung bearbeiten, wird der Pod mit SQL Managed Instance mit Azure Arc-Unterstützung neu gestartet. 

### <a name="restore-a-database-to-a-point-in-time"></a>Wiederherstellen einer Datenbank zu einem Zeitpunkt

Ein Wiederherstellungsvorgang kann für eine Instanz von SQL Managed Instance mit Azure Arc-Unterstützung ausgeführt werden, um eine Wiederherstellung von einer Quelldatenbank zu einem Zeitpunkt innerhalb des Aufbewahrungszeitraums durchzuführen. 
**(1) Erstellen Sie die YAML-Datei wie unten in Ihrem Editor:**

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- name: eindeutige Zeichenfolge für jede benutzerdefinierte Ressource, die für Kubernetes erforderlich ist
- namespace: Namespace, in dem die Instanz von SQL Managed Instance mit Azure Arc-Unterstützung ausgeführt wird
- source > name: Name der Instanz von SQL Managed Instance mit Azure Arc-Unterstützung
- source > database: Name der Quelldatenbank in SQL Managed Instance mit Azure Arc-Unterstützung
- restorePoint: Zeitpunkt für den Wiederherstellungsvorgang in UTC
- destination > name: Name des Ziels für die Wiederherstellung von SQL Managed Instance mit Azure Arc-Unterstützung. Derzeit werden nur Wiederherstellungen auf denselben Instanzen unterstützt.
- destination > database: Name der neuen Datenbank, auf die die Wiederherstellung angewandt wird

**(2) Wenden Sie die YAML-Datei an, um einen Task zum Initiieren des Wiederherstellungsvorgangs zu erstellen:**

Führen Sie den Befehl wie folgt aus, um den Wiederherstellungsvorgang zu initiieren:

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> Der Name des Tasks innerhalb der benutzerdefinierten Ressource und der Dateiname müssen nicht identisch sein.


**Überprüfen des Status der Wiederherstellung**

- Der Status des Wiederherstellungstasks wird etwa alle 10 Sekunden aktualisiert, er ändert sich von „Warten“ in „Wiederherstellung“ in „Abgeschlossen“ (bzw. „Fehlerhaft“). 
- Während der Wiederherstellung einer Datenbank wird der Status „Wiederherstellung“ angezeigt.

Der Status des Tasks kann wie folgt abgerufen werden:

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>Überwachen Ihrer Sicherungen

Die Sicherungen werden im Ordner ```/var/opt/mssql/backups/archived/<dbname>/<datetime>``` gespeichert. Dabei ist ```<dbname>``` der Name der Datenbank und ```<datetime>``` ein Zeitstempel im UTC-Format für den Anfang jeder vollständigen Sicherung. Jedes Mal, wenn eine vollständige Sicherung initiiert wird, wird ein neuer Ordner mit der vollständigen Sicherung und allen nachfolgenden differenziellen und Transaktionsprotokollsicherungen in diesem Ordner erstellt. Die aktuellste vollständige Sicherung und die nachfolgenden differenziellen und Transaktionsprotokollsicherungen werden im Ordner ```/var/opt/mssql/backups/current/<dbname><datetime>``` gespeichert.


### <a name="clean-up"></a>Bereinigen 

Wenn Sie ältere Sicherungen löschen möchten, um Speicherplatz freizugeben oder weil Sie sie nicht mehr benötigen, können Sie alle Ordner im Ordner ```/var/opt/mssql/backups/archived/``` entfernen. Das Entfernen von Ordnern in der Mitte einer Zeitachse kann sich auf die Möglichkeit auswirken, eine Wiederherstellung zu einem Zeitpunkt während dieses Zeitfensters durchzuführen. Es wird empfohlen, zuerst die ältesten Ordner zu löschen, um eine kontinuierliche Zeitachse für die Wiederherstellung zu behalten. 


