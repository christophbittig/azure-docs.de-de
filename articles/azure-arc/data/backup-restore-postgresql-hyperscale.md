---
title: Sichern und Wiederherstellen von Azure Database for PostgreSQL Hyperscale-Servergruppen
description: Sichern und Wiederherstellen von Azure Database for PostgreSQL Hyperscale-Servergruppen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: de908a8f29d85aea1954415ba5c9793349837c5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339399"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Sichern und Wiederherstellen von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung

> [!IMPORTANT]
> Das Sichern und Wiederherstellen von Servern mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung wird in der aktuellen Vorschauversion nicht unterstützt.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Wenn Sie Ihre Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung sichern oder wiederherstellen, werden alle Datenbanken auf allen PostgreSQL-Knoten Ihrer Servergruppe gesichert und/oder wiederhergestellt.

## <a name="take-a-manual-full-backup"></a>Manuelles Erstellen einer vollständigen Sicherung

Führen Sie den folgenden Befehl aus, um eine vollständige Sicherung der gesamten Daten und Protokollordner Ihrer Servergruppe zu erstellen:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Hierbei gilt:
- __name__ gibt den Namen einer Sicherung an.
- __server-name__ gibt eine Servergruppe an.
- __no-wait__ gibt an, dass die Befehlszeile nicht auf den Abschluss der Sicherung wartet, sodass Sie mit der Verwendung des Befehlszeilenfensters fortfahren können.

Dieser Befehl koordiniert eine verteilte vollständige Sicherung für alle Knoten, aus denen Ihre Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung besteht. Das bedeutet, alle Daten in Ihren Coordinator- und Workerknoten werden gesichert.

Zum Beispiel:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Bei Abschluss der Sicherung werden ID, Name, Größe, Zustand und Zeitstempel der Sicherung zurückgegeben. Beispiel:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` gibt die Zeitzone für den Zeitpunkt der Sicherungserstellung an. „+ 00:00“ steht in diesem Beispiel für koordinierte Weltzeit (UTC + 00 Stunden und 00 Minuten).

> [!NOTE]
> Folgendes ist noch nicht möglich:
> - Planen von automatischen Backups
> - Anzeigen des Fortschritts der Sicherungserstellung

## <a name="list-backups"></a>Auflisten der Sicherungen

Führen Sie den folgenden Befehl aus, um die Sicherungen aufzulisten, die zur Wiederherstellung verfügbar sind:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Zum Beispiel:

```console
azdata arc postgres backup list --server-name postgres01
```

Beispiel für die Ausgabe:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

In der Zeitstempelspalte wird der Zeitpunkt (in UTC) angegeben, zu dem die Sicherung erstellt wurde.

## <a name="restore-a-backup"></a>Wiederherstellen einer Sicherung
In diesem Abschnitt erfahren Sie, wie Sie eine vollständige Wiederherstellung oder eine Point-in-Time-Wiederherstellung durchführen. Wenn Sie eine vollständige Sicherung wiederherstellen, wird der gesamte Inhalt der Sicherung wiederhergestellt. Bei einer Point-in-Time-Wiederherstellung werden die Daten bis zu dem von Ihnen angegebenen Zeitpunkt wiederhergestellt. Transaktionen, die nach diesem Zeitpunkt stattgefunden haben, werden nicht wiederhergestellt.

> [!CAUTION]
> Sie können die Wiederherstellung nur für eine Servergruppe durchführen, die über dieselbe Anzahl von Workerknoten verfügt wie zum Zeitpunkt der Erstellung der Sicherung. Wenn Sie die Anzahl der Workerknoten seit dem Erstellen der Sicherung erhöht oder verringert haben, müssen Sie vor der Wiederherstellung die Anzahl der Workerknoten erhöhen/verringern oder eine neue Servergruppe erstellen, damit es eine Übereinstimmung mit dem Inhalt der Sicherung gibt. Die Wiederherstellung schlägt fehl, wenn die Anzahl der Workerknoten nicht übereinstimmt.

### <a name="restore-a-full-backup"></a>Wiederherstellen einer vollständigen Sicherung
Wenn Sie den gesamten Inhalt einer Sicherung wiederherstellen möchten, führen Sie den folgenden Befehl aus:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Hierbei gilt:
- __backup-id__ entspricht der ID der Sicherung, die mit dem oben gezeigten Befehl zum Auflisten der Sicherungen angezeigt wird.
Hiermit wird eine verteilte vollständige Wiederherstellung für alle Knoten koordiniert, aus denen Ihre Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung besteht. Das bedeutet, alle Daten in Ihren Coordinator- und Workerknoten werden wiederhergestellt.

#### <a name="examples"></a>Beispiele:

__Wiederherstellen der Servergruppe „postgres01“ in sich selbst:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Dieser Vorgang wird erst ab der PostgreSQL-Version 12 unterstützt.

__Wiederherstellen der Servergruppe „postgres01“ als eine andere Servergruppe (postgres02):__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Dieser Vorgang wird ab der PostgreSQL-Version 11 unterstützt. Die Zielservergruppe muss vor dem Wiederherstellungsvorgang erstellt werden. Außerdem muss sie die gleiche Konfiguration besitzen und den gleichen Sicherungs-PVC verwenden wie die Quellservergruppe.

Wenn der Wiederherstellungsvorgang abgeschlossen ist, wird eine Ausgabe ähnlich der folgenden in der Befehlszeile zurückgegeben:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Folgendes ist noch nicht möglich:
> - Wiederherstellen einer Sicherung durch Angabe ihres Namens
> - Anzeigen des Fortschritts eines Wiederherstellungsvorgangs


### <a name="do-a-point-in-time-restore"></a>Durchführen einer Point-in-Time-Wiederherstellung

Wenn Sie eine Servergruppe bis zu einem bestimmten Zeitpunkt wiederherstellen möchten, führen Sie den folgenden Befehl aus:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Führen Sie Folgendes aus, um sich das allgemeine Format des Wiederherstellungsbefehls anzusehen: `azdata arc postgres backup restore --help`.

`time` ist der Zeitpunkt für die Wiederherstellung. Geben Sie entweder einen Zeitstempel oder eine Zahl und ein Suffix ( `m` für Minuten, `h` für Stunden, `d` für Tage oder `w` für Wochen) an. `1.5h` geht beispielsweise 90 Minuten zurück.

#### <a name="examples"></a>Beispiele:
__Durchführen einer Point-in-Time-Wiederherstellung der Servergruppe „postgres01“ in sich selbst:__

Es ist noch nicht möglich, eine Point-in-Time-Wiederherstellung einer Servergruppe in sich selbst durchzuführen.

__Durchführen einer Point-in-Time-Wiederherstellung der Servergruppe „postgres01“ als eine andere Servergruppe (postgres02) mit einem bestimmten Zeitstempel:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

In diesem Beispiel wird der Zustand, in dem sich die Servergruppe „postgres01“ am 8. Dezember 2020 um 04:23:48,75 Uhr (UTC) befand, in der Servergruppe „postgres02“ wiederhergestellt. Beachten Sie, dass „+00“ die Zeitzone des gewünschten Zeitpunkts angibt. Wenn Sie keine Zeitzone angeben, wird die Zeitzone des Clients verwendet, von dem aus Sie den Wiederherstellungsvorgang ausführen.

Beispiel:
- `2020-12-08 04:23:48.751326+00` wird als `2020-12-08 04:23:48.751326` (UTC) interpretiert.
- Wenn Sie sich in der PST-Zeitzone (UTC+08) befinden, wird `2020-12-08 04:23:48.751326` als `2020-12-08 12:23:48.751326` interpretiert. Dieser Vorgang wird ab der PostgreSQL-Version 11 unterstützt. Die Zielservergruppe muss vor dem Wiederherstellungsvorgang erstellt werden und den gleichen Sicherungs-PVC verwenden wie die Quellservergruppe.


__Durchführen einer Point-in-Time-Wiederherstellung der Servergruppe „postgres01“ als eine andere Servergruppe (postgres02) mit einem Zustand, der eine bestimmte Zeit zurückliegt:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

In diesem Beispiel wird der Zustand, in dem sich die Servergruppe „postgres01“ vor 22 Minuten befand, in der Servergruppe „postgres02“ wiederhergestellt.
Dieser Vorgang wird ab der PostgreSQL-Version 11 unterstützt. Die Zielservergruppe muss vor dem Wiederherstellungsvorgang erstellt werden und den gleichen Sicherungs-PVC verwenden wie die Quellservergruppe.

> [!NOTE]
> Folgendes ist noch nicht möglich:
> - Anzeigen des Fortschritts eines Wiederherstellungsvorgangs

## <a name="delete-backups"></a>Löschen von Sicherungen

Die Aufbewahrungsdauer für Sicherungen kann in der Vorschau nicht festgelegt werden. Sie können jedoch nicht benötigte Sicherungen manuell löschen.
Der allgemeine Befehl zum Löschen von Sicherungen lautet:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

Dabei gilt Folgendes:
- `--server-name` ist der Name der Servergruppe, aus der der Benutzer eine Sicherung löschen möchte.
- `--name` ist der Name der Sicherung, die gelöscht werden soll.
- `-id` ist die ID der Sicherung, die gelöscht werden soll.

> [!NOTE]
> `--name` und `-id` schließen sich gegenseitig aus.

Beispiel:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Sie können den Namen und die ID der Sicherungen abrufen, indem Sie den Befehl „list backup“ ausführen, wie im vorherigen Absatz erläutert.

Um weitere Informationen zum Löschbefehl zu erhalten, führen Sie Folgendes aus:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Aufskalieren](scale-out-in-postgresql-hyperscale-server-group.md) der Servergruppe (Hinzufügen von Workerknoten).
- Erfahren Sie mehr über das [zentrale Hoch- oder Herunterskalieren](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) der Servergruppe (Vergrößern/Verkleinern von Arbeitsspeicher/virtuellen Kernen).
