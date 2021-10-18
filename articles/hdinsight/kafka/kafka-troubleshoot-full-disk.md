---
title: Broker kann aufgrund einer vollen Festplatte in Azure HDInsight nicht gestartet werden
description: Schritte zur Fehlerbehebung für einen Apache Kafka-Broker-Prozess, der aufgrund eines vollen Datenträgers nicht gestartet werden kann.
ms.service: hdinsight
ms.topic: troubleshooting
author: Jacky-hdi
ms.author: linjzhu
ms.date: 10/11/2021
ms.openlocfilehash: 3e7d113da1c086c0a95916dd29c9149beb897862
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859626"
---
# <a name="scenario-brokers-are-unhealthy-or-cant-restart-due-to-disk-space-full-issue"></a>Szenario: Broker sind nicht funktionsfähig oder können nicht neu gestartet werden, weil die Festplatte voll ist

Dieser Artikel beschreibt Schritte zur Fehlerbehebung und mögliche Lösungen für Probleme bei der Verwendung von Apache Kafka in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

Die von Apache Kafka-Brokern in Azure HDInsight-Clustern verwendeten Datenfestplatten können voll werden. Wenn dies geschieht, kann der Apache Kafka-Broker-Prozess nicht gestartet werden und schlägt aufgrund des Fehlers "Festplatte voll" fehl. Wenn Sie kürzlich Konfigurationsänderungen vorgenommen haben, werden diese Änderungen nicht wirksam, da der Kafka-Broker-Prozess nicht gestartet wird.

## <a name="cause"></a>Ursache

Das Problem kann durch eines oder mehrere der folgenden Szenarien verursacht werden:

- Sie können die Anzahl der Festplatten oder die Festplattengröße nicht erhöhen, nachdem Sie den Kafka-Cluster erstellt haben.
- Normalerweise werden Festplattenwarnungen in der Apache Ambari UI konfiguriert. Wenn die Festplattenauslastung über 60 % steigt, werden Sie in einer Warnung darauf hingewiesen, dass Sie entweder die Anzahl der Protokolle im Kafka-Cluster verringern oder reduzieren müssen.
- Dieses Problem tritt nur auf, wenn Sie die Festplattenwarnungen ignorieren. Sie könnten den Cluster verkleinern, um mehr Platz für die Reaktion auf die Speicherplatzwarnungen zu schaffen.
- Nachrichten werden nicht sofort gelöscht, auch wenn die Aufbewahrungszeit abgelaufen ist. Nachrichten, die gelöscht werden sollen, werden zum Löschen markiert. Später löscht ein Bereinigungsprozess im Hintergrund die Nachrichten. Nur Nachrichten in passiven Segmenten werden gelöscht.

> [!IMPORTANT]
> Sie können eine Konfiguration verwenden, um die Leistung des Log Cleaners zu verbessern, aber  *seien Sie vorsichtig*, wenn Sie diese Verbesserung anwenden, da sie sich auf die Erzeugung oder den Verbrauch von Nachrichten auswirken kann.

## <a name="mitigation"></a>Minderung

Gehen Sie wie folgt vor, um das Problem zu beheben:

1. Überprüfen Sie die `server.properties` Dateien, um die Aufbewahrungszeit für jedes Thema zu finden. Manchmal ist die Protokollaufbewahrungsrichtlinie auf Themenebene festgelegt. Um die auf Themenebene konfigurierte Aufbewahrungszeit zu finden, führen Sie den folgenden Befehl aus:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper <zookeeper-list>
    ```

2. Wenn Sie diese Informationen haben, überprüfen Sie, welche großen Partitionen den maximalen Speicherplatz belegen.

    ```bash
    # Command to sort the directories based on size:
    du -hs * | sort -rh | head -5 
    ```

3. Sichern Sie die Dateien, die älter als die neue Aufbewahrungszeit sind.

4. Wenn etwas freier Speicherplatz verfügbar ist, können Sie die Broker neu starten, um die neue Aufbewahrungszeitkonfiguration zu verwenden. Der Neustart der Broker bereinigt die älteren Protokolle und gibt etwas Speicherplatz auf der Festplatte frei.

   > [!IMPORTANT]
   > Manchmal ist es nicht möglich, eine Sicherungskopie zu erstellen, da entweder die Festplatte des Betriebssystems voll sein könnte oder andere Kafka-Festplatten überlastet würden. In diesem Fall müssen Sie möglicherweise Dateien löschen, die älter als die Aufbewahrungszeit sind.

## <a name="resolution"></a>Lösung

Auch wenn Sie die Aufbewahrungszeit verringern können, ist die Konfiguration nicht skalierbar, wenn Sie dem Cluster weitere Themen hinzufügen möchten oder wenn die Last oder die Menge der eingelesenen Daten steigt.

Um diese Szenarien zu vermeiden, sollten Sie eine der folgenden Optionen verwenden:

- Bei zu großen Fächern sollten Sie die Anzahl der Fächer für die schwersten Themen erhöhen.

  > [!NOTE]
  > Durch das Erhöhen der Partitionen in einem vorhandenen Thema werden die Daten im Thema nicht reorganisiert. Sie müssen die Daten zunächst manuell von einem alten Thema mit geringer Partitionierung in ein neues Thema mit höherer Partitionierung kopieren. 

- Verkleinern Sie den Cluster und verteilen Sie alle Partitionen auf mehrere Festplatten.

- Erstellen Sie einen Cluster mit einer größeren SKU und mehr angeschlossenen Festplatten.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
