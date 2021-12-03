---
title: Probleme des Apache Ambari Metrics Collectors in Azure HDInsight
description: Behandlung von Problemen des Apache Ambari Metrics Collectors in Azure HDInsight
ms.service: hdinsight
ms.author: linjzhu
author: jacky-hdi
ms.topic: troubleshooting
ms.date: 11/03/2021
ms.openlocfilehash: 6fbfc4f47cb14966a7d03d9ef378bb03f519183c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063978"
---
# <a name="apache-ambari-metrics-collector-issues-in-azure-hdinsight"></a>Probleme des Apache Ambari Metrics Collectors in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="scenario-outofmemoryerror-or-unresponsive-apache-ambari-metrics-collector"></a>Szenario: „OutOfMemoryError“ oder „Nicht reagierender Apache Ambari Metrics Collector“

### <a name="issue"></a>Problem

* Sie könnten in der Ambari-Benutzeroberfläche eine kritische Warnung zum **„Metrics Collector Process“** (Metrikensammlerprozess) erhalten unter Anzeige einer ähnlichen Meldung wie folgt.
    `Connection failed: timed out to <headnode fqdn>:6188`
* Ambari Metrics Collector wird möglicherweise häufig im Hauptknoten neu gestartet.
* Einige Apache Ambari-Metriken werden möglicherweise nicht angezeigt. Beispielsweise weist **NAMENODE** den Status **Gestartet** anstatt **Aktiv/Standby** an. 


### <a name="cause"></a>Ursache

Die folgenden Szenarien stellen mögliche Ursachen für diese Probleme dar:

#### <a name="an-out-of-memory-exception-happens-frequently"></a>Es tritt häufig eine Ausnahme wegen unzureichendem Arbeitsspeicher auf.

Überprüfen Sie das Protokoll des Apache Ambari Metrics Collectors: `/var/log/ambari-metrics-collector/ambari-metrics-collector.log*`.

```
19:59:45,457 ERROR [325874797@qtp-2095573052-22] log:87 - handle failed
java.lang.OutOfMemoryError: Java heap space

19:59:45,457 FATAL [MdsLoggerSenderThread] YarnUncaughtExceptionHandler:51 - Thread Thread[MdsLoggerSenderThread,5,main] threw an Error.  Shutting down now...
java.lang.OutOfMemoryError: Java heap space
```

#### <a name="busy-garbage-collection"></a>Ausgelastete Garbage Collection

1. Der Apache Ambari Metrics Collector lauscht nicht an **6188** im „hbase-ams“-Protokoll `/var/log/ambari-metrics-collector/hbase-ams-master-hn*.log`.

   ```
   2021-04-13 05:57:37,546 INFO  [timeline] timeline.HadoopTimelineMetricsSink: No live collector to send metrics to. Metrics to be sent will be discarded. This message will be skipped for the next 20 times.
   ```
   
2. Abrufen der PID des Apache Ambari Metrics Collectors und Überprüfen der GC-Leistung

   ```
   ps -fu ams | grep 'org.apache.ambari.metrics.AMSApplicationServer'
   ```
       
3. Überprüfen Sie den Status der Garbage Collection mittels `jstat -gcutil <pid> 1000 100`. Wenn Sie einen hohen Anstieg von **FGCT** innerhalb weniger Sekunden feststellen, deutet dies darauf hin, dass der Apache Ambari Metrics Collector in „Full GC“ ausgelastet ist und die anderen Anforderungen nicht verarbeiten kann.

### <a name="resolution"></a>Lösung

Um diese Probleme zu vermeiden, sollten Sie eine der folgenden Optionen verwenden:

1. Erhöhen des Heapspeichers des Apache Ambari Metrics Collectors über **Ambari** des  > **Ambari Metric Collector** > **Configuration** > **Metrics Collector Heap Size**

   :::image type="content" source="./media/apache-ambari-troubleshoot-ams-issues/editing-ams-configuration-ambari.png" alt-text="Screenshot des Bearbeitens der Konfigurationseigenschaften des Ambari Metric Service." border="true":::

2. Führen Sie diese Schritte aus, um Ambari Metrics Service-Daten (AMS) zu bereinigen.

   > [!NOTE]
   > Durch das Bereinigen der AMS-Daten werden alle verfügbaren AMS-Verlaufsdaten entfernt. Wenn Sie den Verlauf benötigen, ist dies möglicherweise nicht die beste Option.

   1.  Melden Sie sich beim Ambari-Portal an.  
    1.  Aktivieren Sie den Wartungsmodus für AMS.  
    2.  Beenden Sie AMS über Ambari.  
    3.  Identifizieren Sie auf dem **AMS-Konfigurationsbildschirm** Folgendes  
                1. `hbase.rootdir` (Standardwert ist `file:///mnt/data/ambari-metrics-collector/hbase`)  
                2. `hbase.tmp.dir` (Standardwert ist `/var/lib/ambari-metrics-collector/hbase-tmp`)  
   2. Stellen Sie eine SSH-Verbindung mit dem Hauptknoten her, in dem sich der Apache Ambari Metrics Collector befindet. Als Superuser:
    1. Entfernen Sie die AMS-Zookeeper-Daten, indem Sie den Inhalt von `'hbase.tmp.dir'/zookeeper` **sichern** und entfernen.
    2. Entfernen Sie alle Phoenix-Spooldateien aus dem Ordner `<hbase.tmp.dir>/phoenix-spool`. 
    3. ***(Es empfiehlt sich, diesen Schritt zunächst zu überspringen und zu versuchen, AMS neu zu starten, um festzustellen, ob das Problem dadurch behoben wird. Wenn AMS immer noch nicht gestartet werden kann, probieren Sie diesen Schritt aus.)***  
            AMS-Daten werden wie oben angegeben unter `hbase.rootdir` gespeichert. Verwenden Sie reguläre Betriebssystembefehle, um die Dateien zu sichern und zu entfernen. Beispiel:     
            `tar czf /mnt/backupof-ambari-metrics-collector-hbase-$(date +%Y%m%d-%H%M%S).tar.gz /mnt/data/ambari-metrics-collector/hbase`  
   3.  Neustarten von AMS mittels Ambari.


## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
