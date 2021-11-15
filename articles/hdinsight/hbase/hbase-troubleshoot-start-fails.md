---
title: Fehler beim Starten von Apache HBase Master in Azure HDInsight
description: Fehler beim Starten von Apache HBase Master (HMaster) in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/07/2021
ms.openlocfilehash: 7870aa48a75de6a5443298d909f0fe7d59311a78
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061623"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Fehler beim Starten von Apache HBase Master (HMaster) in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="scenario-master-startup-cannot-progress-in-holding-pattern-until-region-comes-online"></a>Szenario: Master-Start kann nicht fortfahren; befindet sich im Holding-Muster, bis die Region online ist.

### <a name="issue"></a>Problem

HMaster kann aufgrund der folgenden Warnung nicht gestartet werden:
```output
hbase:namespace,,<timestamp_region_create>.<encoded_region_name>.is NOT online; state={<encoded_region_name> state=OPEN, ts=<some_timestamp>, server=<server_name>}; ServerCrashProcedures=true. Master startup cannot progress, in holding-pattern until region onlined. 
```

Beispielsweise können die Parameterwerte in der tatsächlichen Meldung variieren:
```output
hbase:namespace,,1546588612000.0000010bc582e331e3080d5913a97000. is NOT online; state={0000010bc582e331e3080d5913a97000 state=OPEN, ts=1633935993000, server=<wn fqdn>,16000,1622012792000}; ServerCrashProcedures=false. Master startup cannot progress, in holding-pattern until region onlined.
```

### <a name="cause"></a>Ursache

HMaster überprüft das Vorhandensein des WAL-Verzeichnisses auf den Regionsservern, bevor die **OFFENEN** (OPEN) Regionen wieder online gebracht werden. Wenn dieses Verzeichnis in diesem Fall nicht vorhanden war, wurde er nicht gestartet.

### <a name="resolution"></a>Lösung

1. Erstellen Sie dieses Dummyverzeichnis mit dem Befehl: `sudo -u hbase hdfs dfs -mkdir /hbase-wals/WALs/<wn fqdn>,16000,1622012792000`.

2. Starten Sie den HMaster-Dienst über die Ambari-Benutzeroberfläche neu.

## <a name="scenario-atomic-renaming-failure"></a>Szenario: Fehler bei der atomischen Umbenennung

### <a name="issue"></a>Problem

Beim Start wurden unerwartete Dateien erkannt.

### <a name="cause"></a>Ursache

Im Rahmen des Startvorgangs führt HMaster verschiedene Initialisierungsschritte aus. Dabei werden unter anderem Daten aus dem Ablageordner (.tmp) in den Datenordner verschoben. HMaster untersucht zudem den Ordner mit write-ahead-Protokollen (WALs), um festzustellen, ob nicht reagierende Regionsserver vorhanden sind.

HMaster führt einen einfachen list-Befehl für die WAL-Ordner aus. Findet HMaster zu einem beliebigen Zeitpunkt eine unerwartete Datei in einem dieser Ordner, wird eine Ausnahme ausgelöst und kein Start ausgeführt.

### <a name="resolution"></a>Lösung

Überprüfen Sie die Aufrufliste und versuchen Sie festzustellen, welcher Ordner das Problem verursachen könnte (dies können beispielsweise der WAL-Ordner oder der TMP-Ordner sein). Versuchen Sie anschließend im Cloud-Explorer oder mithilfe von HDFS-Befehlen die problematische Datei zu lokalisieren. Im Allgemeinen handelt es sich dabei um eine `*-renamePending.json`-Datei. (Die `*-renamePending.json`-Datei ist eine Journaldatei, mit welcher der Vorgang der atomischen Umbenennung im WASB-Treiber implementiert wird. Aufgrund von Fehlern in dieser Implementierung können diese Dateien nach Prozessabstürzen, usw., übrig bleiben). Erzwingen Sie das Löschen dieser Datei entweder im Cloud Explorer oder mittels HDFS-Befehlen.

Gelegentlich kann an diesem Speicherort auch eine temporäre Datei mit einem Namen wie `$$$.$$$` vorhanden sein. Sie müssen den HDFS-Befehl `ls` verwenden, um diese Datei anzuzeigen; im Cloud-Explorer wird die Datei nicht angezeigt. Führen Sie zum Löschen dieser Datei den HDFS-Befehl `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` aus.

Nach dem Ausführen dieser Befehle sollte HMaster sofort gestartet werden.

---

## <a name="scenario-no-server-address-listed"></a>Szenario: Keine Serveradresse aufgelistet

### <a name="issue"></a>Problem

Möglicherweise wird eine Meldung angezeigt, die besagt, dass die `hbase: meta`-Tabelle nicht online ist. Bei Ausführung von `hbck` wird u. U. Folgendes gemeldet: `hbase: meta table replicaId 0 is not found on any region.` In den HMaster-Protokollen könnte diese Meldung angezeigt werden: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Ursache

HMaster konnte nach einem HBase-Neustart nicht initialisiert werden.

### <a name="resolution"></a>Lösung

1. Geben Sie in der HBase-Shell die folgenden Befehle ein (ändern Sie ggf. die Istwerte):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Löschen Sie den Eintrag `hbase: namespace`. Bei diesem Eintrag kann es sich um den gleichen Fehler handeln, der auch beim Überprüfen der Tabelle `hbase: namespace` gemeldet wurde.

1. Starten Sie den aktiven HMaster von der Ambari-Benutzeroberfläche aus neu, um HBase hochzufahren.

1. Führen Sie in der HBase-Shell den folgenden Befehl aus, um alle Tabellen hochzufahren, die offline sind:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Szenario: „java.io.IOException: Timedout“

### <a name="issue"></a>Problem

Für HMaster tritt ein Timeout mit einer schwerwiegenden Ausnahme wie `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` auf.

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn viele Tabellen und Regionen vorhanden sind, die beim Neustart der HMaster-Dienste nicht geleert wurden. Das Timeout ist ein bekannter HMaster-Fehler. Allgemeine Cluster-Startaufgaben können sehr lange dauern. HMaster wird heruntergefahren, wenn die Namespacetabelle noch nicht zugewiesen ist. Die langwierigen Starttasks treten auf, wenn eine große Menge an nicht geleerten Daten vorhanden und ein Zeitlimit von fünf Minuten nicht ausreichend ist.

### <a name="resolution"></a>Lösung

1. Wechseln Sie auf der Apache Ambari-Benutzeroberfläche zu **HBase** > **Configs**. Fügen Sie in der benutzerdefinierten Datei `hbase-site.xml` die folgende Einstellung hinzu:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Starten Sie die erforderlichen Dienste neu (HMaster und möglicherweise andere HBase-Dienste).

---

## <a name="scenario-frequent-region-server-restarts"></a>Szenario: Häufige Neustarts des Regionsservers

### <a name="issue"></a>Problem

Knoten werden regelmäßig neu gestartet. Die Regionsserverprotokolle enthalten Einträge wie:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Ursache

Lange Unterbrechung der JVM-GC von `regionserver`. Die Pause führt dazu, dass `regionserver` nicht mehr reagiert und innerhalb des ZooKeeper-Sitzungstimeouts von 40 Sekunden keinen Heartbeat an HMaster senden kann. HMaster stuft `regionserver` als inaktiv ein, bricht `regionserver` ab, und führt einen Neustart aus.

### <a name="resolution"></a>Lösung

Ändern Sie das ZooKeeper-Sitzungstimeout. Hierzu muss nicht nur die `hbase-site`-Einstellung `zookeeper.session.timeout`, sondern auch die `zoo.cfg`-Einstellung `maxSessionTimeout` von ZooKeeper geändert werden.

1. Navigieren Sie auf der Ambari-Benutzeroberfläche zu **HBase > Configs > Settings** („HBase“ > „Konfigurationen“ > „Einstellungen“), und ändern Sie im Timeoutabschnitt den Wert des ZooKeeper-Sitzungstimeouts.

1. Navigieren Sie auf der Ambari-Benutzeroberfläche zu **Zookeeper -> Configs -> Custom** `zoo.cfg` (ZooKepper -> Konfigurationen -> Benutzerdefiniert), und fügen Sie die folgende Einstellung hinzu, bzw. ändern Sie sie. Achten Sie darauf, dass der Wert mit dem HBase-Wert für `zookeeper.session.timeout` übereinstimmt.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Starten Sie die erforderlichen Dienste neu.

---

## <a name="scenario-log-splitting-failure"></a>Szenario: Fehler bei der Protokollaufteilung

### <a name="issue"></a>Problem

Fehler beim Starten von HMaster in einem HBase-Cluster.

### <a name="cause"></a>Ursache

Falsch konfigurierte HDFS- und HBase-Einstellungen für ein sekundäres Speicherkonto.

### <a name="resolution"></a>Lösung

`set hbase.rootdir: wasb://@.blob.core.windows.net/hbase`, und starten Sie Dienste in Ambari neu.

---

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [notes](../includes/hdinsight-troubleshooting-next-steps.md)]
