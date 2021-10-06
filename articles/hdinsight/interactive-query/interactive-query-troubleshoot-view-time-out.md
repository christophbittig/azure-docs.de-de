---
title: Timeout der Apache Hive-Ansicht bei Abfrageergebnis – Azure HDInsight
description: Timeout der Apache Hive-Ansicht beim Abrufen eines Abfrageergebnisses in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 5b1ec7fec182d5b0b6f2d68467d6e3e84fdb5f3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601404"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Szenario: Timeout der Apache Hive-Ansicht beim Abrufen eines Abfrageergebnisses in Azure HDInsight

Dieser Artikel beschreibt Schritte zur Problembehandlung sowie mögliche Lösungen für Probleme bei der Verwendung von Interactive Query-Komponenten in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

Beim Ausführen bestimmter Abfragen über die Apache Hive-Ansicht tritt unter Umständen der folgende Fehler auf:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Ursache

Der Standardtimeoutwert für die Hive-Ansicht eignet sich möglicherweise nicht für die von Ihnen ausgeführte Abfrage. Der angegebene Zeitraum ist zu kurz, um das Abfrageergebnis über die Hive-Ansicht abzurufen.

## <a name="resolution"></a>Lösung

1. Erhöhen Sie die Timeouts für die Apache Ambari Hive-Ansicht, indem Sie die folgenden Eigenschaften in `/etc/ambari-server/conf/ambari.properties` für **beide Hauptknoten** festlegen:
  ```
  views.ambari.request.read.timeout.millis=300000
  views.request.read.timeout.millis=300000
  views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
  ```
  Der Wert von `HIVE_VIEW_INSTANCE_NAME` ist am Ende der URL für die Hive-Ansicht verfügbar.

2. Starten Sie den aktiven Ambari-Server neu, indem Sie Folgendes ausführen. Wenn Sie eine Fehlermeldung erhalten, die besagt, dass es sich nicht um den aktiven Ambari-Server handelt, stellen Sie einfach eine SSH-Datei mit dem nächsten Hauptknoten her, und wiederholen Sie diesen Schritt.
  ```
  sudo ambari-server restart
  ```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
