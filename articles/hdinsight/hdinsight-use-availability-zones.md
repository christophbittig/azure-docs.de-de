---
title: Erstellen eines Azure HDInsight-Clusters, der Availability Zones verwendet
description: Erfahren Sie, wie Sie einen Azure HDInsight-Cluster erstellen, der Availability Zones verwendet.
ms.service: hdinsight
ms.topic: how-to
ms.custom: references_regions
ms.date: 09/01/2021
ms.openlocfilehash: 37d15255e469c369086954f08a1401a82641d838
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281814"
---
# <a name="create-an-hdinsight-cluster-that-uses-availability-zones-preview"></a>Erstellen eines HDInsight-Clusters, der Verfügbarkeitszonen verwendet (Vorschau)

Ein Azure HDInsight-Cluster besteht aus mehreren Knoten (Kopfknoten, Workerknoten, Gateway-Knoten und Zookeeper-Knoten). In einer Region, die Availability Zones unterstützt, hat der Benutzer standardmäßig keine Kontrolle darüber, welche Clusterknoten in welcher Verfügbarkeitszone bereitgestellt werden. 

Mit dieser neuen Verfügbarkeitszonenfunktion kann der Benutzer nun festlegen, welche Availability Zone alle Knoten des HDInsight-Clusters hosten soll. Die Clusterknoten sind physisch von einer anderen Verfügbarkeitszone getrennt und von Ausfällen in anderen Verfügbarkeitszonen in derselben Region isoliert. Dieses Bereitstellungsmodell bietet außerdem eine kostengünstige Netzwerkkonnektivität mit geringer Latenz innerhalb des Clusters. 

Die Replizierung dieses Bereitstellungsmodells in mehrere Verfügbarkeitszonen kann ein höheres Maß an Verfügbarkeit zum Schutz vor Hardwareausfällen bieten.

In diesem Artikel erfahren Sie, wie Sie einen HDInsight-Cluster innerhalb einer Availability Zone erstellen und wie Sie diese Funktion nutzen können, um eine höhere Verfügbarkeit zu erreichen. 

## <a name="before-you-begin"></a>Voraussetzungen
Die Funktion Availability Zone wird nur für Cluster unterstützt, die nach dem 15. Juni erstellt wurden. Die Einstellungen der Verfügbarkeitszone können nach dem Erstellen des Clusters nicht mehr aktualisiert werden. Außerdem können Sie einen bestehenden, nicht verfügbaren Zonencluster nicht aktualisieren, wenn Sie Verfügbarkeitszonen verwenden möchten.

## <a name="prerequisites-and-region-availability"></a>Voraussetzungen und regionale Verfügbarkeit
Voraussetzungen:

 - Cluster müssen unter einem benutzerdefinierten VNet erstellt werden. 
 - Sie müssen Ihre eigene SQL-DB für die Ambari-DB und den externen Metastore (wie den Hive-Metastore) mitbringen, damit Sie diese DBs in derselben Verfügbarkeitszone konfigurieren können. 

HDInsight-Cluster können derzeit mit Verfügbarkeitszonen in den folgenden Regionen erstellt werden:

 - Australien (Osten)
 - Brasilien Süd
 - Kanada, Mitte
 - USA (Mitte)
 - East US
 - USA (Ost) 2
 - Frankreich, Mitte
 - Deutschland, Westen-Mitte
 - Japan, Osten
 - Nordeuropa
 - Asien, Südosten
 - USA Süd Mitte
 - UK, Süden
 - US Government, Virginia
 - Europa, Westen
 - USA, Westen 2

## <a name="overview-of-availability-zones-for-hdinsight-clusters"></a>Übersicht der Verfügbarkeitszonen für HDInsight-Cluster

Verfügbarkeitszonen sind eindeutige physische Standorte innerhalb einer Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. In Azure enthält eine Region eine oder mehrere Availability Zones. Diese physische Trennung der Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen des Rechenzentrums. Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md).

Azure HDInsight-Cluster können so konfiguriert werden, dass sie innerhalb einer Availability Zone bereitgestellt werden. Alle Knoten in diesem HDInsight-Cluster, einschließlich der zwei Head-Knoten, drei Zookeeper-Knoten, zwei Gateway-Knoten und der Workerknoten, werden in der angegebenen Verfügbarkeitszone platziert.  Zum Beispiel gibt es drei Availability Zones in East US. Ein HDInsight-Cluster in Ost-USA kann mit allen Knoten in Availability Zone 1 erstellt werden. 

Die Verwendung von Availability Zones mit HDInsight-Clustern auf diese Weise kann sowohl Leistungs- als auch Kostenvorteile bieten: 

 - Bessere Leistung durch Netzanbindung mit geringer Latenz
 - Geringere Kosten: Die Datenübertragung innerhalb der gleichen Availability Zone ist kostenlos. Bei einer Datenübertragung über mehrere Availability Zones hinweg fallen zusätzliche Netzwerkkosten an. 

Wenn Ihre Anwendung Hochverfügbarkeit über mehrere Availability Zones hinweg erfordert, können Sie einen primären HDInsight-Cluster in einer Availability Zone erstellen und einen sekundären HDInsight-Cluster in einer anderen Availability Zone mit minimaler Größe erstellen, um Kosten zu sparen. Bei diesem Design ist dieser HDInsight-Cluster nicht betroffen, wenn eine der anderen Availability Zones ausfällt. Wenn diese Availability Zone ausfällt, müssen Kunden die sekundären Cluster in einer anderen Availability Zone auf den primären Cluster umstellen, die Arbeitslast an diesen neuen primären Cluster weiterleiten und die Clustergröße schnell erhöhen, um die Datenverarbeitung wieder aufzunehmen.   

## <a name="create-an-hdinsight-cluster-using-availability-zone"></a>Erstellen eines HDInsight-Clusters mit Verfügbarkeitszone
Sie können die Azure Resource Manager (ARM)-Vorlage verwenden, um einen HDInsight-Cluster in einer bestimmten Availability Zone zu starten. 

Im Abschnitt "Ressourcen" müssen Sie einen Abschnitt "Zonen" hinzufügen und angeben, in welcher Availability Zone dieser Cluster bereitgestellt werden soll. 

```json
   "resources": [
        {
            "type": "Microsoft.HDInsight/clusters",
            "apiVersion": "2018-06-01-preview",
            "name": "[parameters('cluster name')]",
            "location": "East US 2",
            "zones": [
                "1"
            ],
```
 
## <a name="verify-nodes-within-one-availability-zone-across-zones"></a>Überprüfen von Knoten innerhalb einer Availability Zone über Zonen hinweg
Wenn der HDInsight-Cluster fertig ist, können Sie den Standort überprüfen, um zu sehen, in welcher Verfügbarkeitszone er bereitgestellt wurde.

:::image type="content" source="./media/hdinsight-use-availability-zones/cluster-availability-zone-info.png" alt-text="Screenshot sthat Verfügbarkeitszoneninformationen in der Clusterübersicht" border="true":::

**Abfrage der API-Antwort**: 

```json
 [
        {
            "location": "East US 2",
            "zones": [
                "1"
            ],
```

## <a name="scale-up-the-cluster"></a>Skalieren des Clusters
Sie können einen HDInsight-Cluster mit mehr Workerknoten aufstocken. Die neu hinzugefügten Workerknoten werden in der gleichen Verfügbarkeitszone dieses Clusters platziert. 

**Einschränkungen:** 

 - Die Verfügbarkeitszonen-Funktion in HDInsight unterstützt keine Cluster, die verwalteter Datenträger benötigen (Kafka-Cluster und HBase-Cluster mit beschleunigter Schreibfunktion) 

## <a name="best-practices"></a>Bewährte Methoden

 - Regelmäßiges Sichern der Konfigurationen in Ambari DB. 
 - Implementieren Sie eine Logik zur einfachen Weiterleitung der Arbeitslast an den sekundären Cluster.

## <a name="when-az-goes-down-what-to-expect"></a>Wenn AZ ausfällt, was ist zu erwarten
 - Sie können sich nicht mit ssh auf diesen Cluster einloggen
 - Sie können diesen Cluster nicht löschen, vergrößern oder verkleinern
 - Sie können keine Aufträge einreichen oder den Auftragsverlauf einsehen
 - Sie können weiterhin eine Anforderung zur Erstellung eines neuen Clusters in einer anderen Region stellen
