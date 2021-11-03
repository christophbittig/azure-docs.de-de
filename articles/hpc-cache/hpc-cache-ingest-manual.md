---
title: 'Azure HPC Cache-Datenerfassung: manuelles Kopieren'
description: Verwenden von cp-Befehlen zum Verschieben von Daten in ein Blobspeicherziel in Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: femila
ms.openlocfilehash: d3d93a767839d552791466d77be5ecbb9c50adf1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078520"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC Cache-Datenerfassung: Methode manuelles Kopieren

Dieser Artikel enthält detaillierte Anweisungen zum manuellen Kopieren von Daten in einen Blobspeichercontainer für die Verwendung mit Azure HPC Cache. Er verwendet parallele Multithreadvorgänge, um die Kopiergeschwindigkeit zu optimieren.

Weitere Informationen zum Verschieben von Daten in einen Blobspeicher für Ihren Azure HPC Cache-Dienst finden Sie unter [Verschieben von Daten in Azure Blob Storage](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Einfaches Kopierbeispiel

Sie können eine Kopie mit mehreren Threads manuell auf einem Client erstellen, indem Sie mehrere Kopierbefehle gleichzeitig im Hintergrund für vordefinierte Gruppen von Dateien oder Pfaden ausführen.

Der Linux/UNIX-Befehl ``cp`` enthält das Argument ``-p``, um Besitz und mtime-Metadaten zu erhalten. Das Hinzufügen dieses Arguments zu den folgenden Befehlen ist optional. (Das Hinzufügen des Arguments erhöht die Anzahl der Dateisystemaufrufe, die vom Client an das Zieldateisystem zur Änderung der Metadaten gesendet werden.)

Dieses einfache Beispiel kopiert zwei Dateien parallel:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Nach der Ausführung dieses Befehls zeigt der Befehl `jobs` an, dass zwei Threads ausgeführt werden.

## <a name="copy-data-with-predictable-file-names"></a>Kopieren von Daten mit vorhersagbaren Dateinamen

Wenn Ihre Dateinamen vorhersagbar sind, können Sie Ausdrücke verwenden, um parallele Kopierthreads zu erstellen.

Wenn Ihr Verzeichnis z. B. 1.000 Dateien enthält, die von `0001` bis `1000` durchnummeriert sind, können Sie die folgenden Ausdrücke verwenden, um 10 parallele Threads zu erstellen, die jeweils 100 Dateien kopieren:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Kopieren von Daten mit unstrukturierten Dateinamen

Wenn Ihre Dateinamenstruktur nicht vorhersagbar ist, können Sie Dateien nach Verzeichnisnamen gruppieren.

In diesem Beispiel werden ganze Verzeichnisse gesammelt, um sie an ``cp``-Befehle zu senden, die als Hintergrundaufgaben ausgeführt werden:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Nachdem die Dateien gesammelt wurden, können Sie parallele Kopierbefehle ausführen, um die Unterverzeichnisse und ihren gesamten Inhalt rekursiv zu kopieren:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Zeitpunkt für das Hinzufügen von Bereitstellungspunkten

Nachdem Sie ausreichend parallele Threads auf einen einzelnen Bereitstellungspunkt des Zieldateisystems ausgerichtet haben, gibt es einen Punkt, an dem das Hinzufügen weiterer Threads nicht zu einem höheren Durchsatz führt. (Der Durchsatz wird in Dateien/Sekunde oder Bytes/Sekunde gemessen, abhängig vom Datentyp.) Im schlimmsten Fall kann eine zu große Anzahl von Threads zu einer Verringerung des Durchsatzes führen.

In diesem Fall können Sie clientseitige Bereitstellungspunkte anderen Einbindungsadressen von Azure HPC Cache hinzufügen und dabei denselben Einbindungspfad des Remotedateisystems verwenden:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Das Hinzufügen von clientseitigen Bereitstellungspunkten ermöglicht es Ihnen, weitere Kopierbefehle für die zusätzlichen `/mnt/destination[1-3]`-Bereitstellungspunkte abzuspalten, um die Parallelität zu erhöhen.

Wenn Sie z. B. sehr große Dateien verwenden, können Sie die Kopierbefehle so definieren, dass sie unterschiedliche Zielpfade verwenden und mehr Befehle parallel vom Client aus senden, der den Kopiervorgang durchführt.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

Im obigen Beispiel sind die Kopiervorgänge der Clientdateien auf alle drei Zielbereitstellungspunkte ausgerichtet.

## <a name="when-to-add-clients"></a>Zeitpunkt zum Hinzufügen von Clients

Wenn Sie die Kapazitäten des Clients erreicht haben, führt das Hinzufügen weiterer Kopierthreads oder zusätzlicher Bereitstellungspunkte nicht zu einer weiteren Erhöhung der „Dateien/Sekunde“ oder „Bytes/Sekunde“. In dieser Situation können Sie einen weiteren Client mit demselben Satz von Bereitstellungspunkten bereitstellen, der auch eigene Sätze von Dateikopiervorgängen ausführen wird.

Beispiel:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Erstellen von Dateimanifesten

Nachdem Sie die oben genannten Ansätze verstanden haben (mehrere Kopierthreads pro Ziel, mehrere Ziele pro Client, mehrere Clients pro über das Netzwerk verfügbarem Quelldateisystem), sollten Sie die folgende Empfehlung beachten: Erstellen Sie Dateimanifeste, und verwenden Sie sie dann mit Kopierbefehlen für mehrere Clients.

Dieses Szenario verwendet den UNIX-Befehl ``find``, um Manifeste von Dateien oder Verzeichnissen zu erstellen:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Leiten Sie dieses Ergebnis in eine Datei um: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Dann können Sie das Manifest mithilfe von BASH-Befehlen durchlaufen, um Dateien zu zählen und die Größe der Unterverzeichnisse zu bestimmen:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Schließlich müssen Sie die eigentlichen Befehle zum Kopieren von Dateien an die Clients anpassen.

Wenn Sie über vier Clients verfügen, verwenden Sie den folgenden Befehl:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Bei fünf Clients verwenden Sie einen Befehl wie den folgenden:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Und für sechs Befehle... Extrapolieren Sie bei Bedarf.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Sie erhalten *N* Ergebnisdateien, eine für jeden Ihrer *N* Clients, der über die Pfadnamen zu den Level-4-Verzeichnissen verfügt, die als Teil der Ausgabe des Befehls `find` erhalten wurden.

Verwenden Sie die einzelnen Dateien, um den Kopierbefehl zu erstellen:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Das obige Beispiel liefert Ihnen *N* Dateien, jede mit einem Kopierbefehl pro Zeile, die als BASH-Skript auf dem Client ausgeführt werden können.

Ziel ist es, mehrere Threads dieser Skripts gleichzeitig pro Client parallel auf mehreren Clients auszuführen.
