---
title: Konzepte für Apache Spark-Pools
description: Hier erfahren Sie mehr über die Größen und Konfigurationen von Apache Spark-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/19/2021
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: c674813aee7702e9887e909e4a8baf7ace074a2c
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597645"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Konfigurationen von Apache Spark-Pools in Azure Synapse Analytics

Ein Spark-Pool stellt eine Gruppe von Metadaten dar, die die Anforderungen an Computeressourcen und die zugehörigen Verhaltensmerkmale definiert, wenn eine Spark-Instanz instanziiert wird. Zu diesen Merkmalen zählen unter anderem der Name, die Anzahl der Knoten, die Knotengröße, das Skalierungsverhalten und die Gültigkeitsdauer. Ein Spark-Pool selbst beansprucht keine Ressourcen. Beim Erstellen von Spark-Pools fallen keine Kosten an. Gebühren fallen nur an, wenn ein Spark-Auftrag für den Spark-Zielpool ausgeführt wird und die Spark-Instanz bei Bedarf instanziiert wird.

Informationen zum Erstellen eines Spark-Pools sowie alle verfügbaren Eigenschaften finden Sie unter [Erstellen eines Apache Spark-Pools](../quickstart-create-apache-spark-pool-portal.md).

## <a name="isolated-compute"></a>Isolated Compute

Die Option „Isolated Compute“ bietet zusätzliche Sicherheitsfeatures für Spark-Computeressourcen aus nicht vertrauenswürdigen Diensten, indem die physische Computeressource einem einzelnen Kunden zugeordnet wird.
Die Option „Isolated Compute“ eignet sich am besten für Workloads, die ein hohes Maß an Isolation von den Workloads anderer Kunden erfordern, beispielsweise um Compliance zu erzielen und gesetzliche Anforderungen zu erfüllen.  
Die Option „Isolate Compute“ ist nur mit der Knotengröße XXXLarge (80 vCPU/504 GB) und nur in den folgenden Regionen verfügbar.  Die Option „Isolate Compute“ kann nach der Poolerstellung aktiviert oder deaktiviert werden, dabei muss die Instanz allerdings ggf. neu gestartet werden.  Wenn Sie dieses Feature in Zukunft aktivieren möchten, stellen Sie sicher, dass Ihr Synapse-Arbeitsbereich in einer Region erstellt wird, die „Isolate Compute“ unterstützt.

* East US
* USA, Westen 2
* USA Süd Mitte
* US Gov Arizona
* US Government, Virginia

## <a name="nodes"></a>Nodes

Eine Instanz eines Apache Spark-Pools besteht aus einem Hauptknoten und zwei oder mehreren Workerknoten mit mindestens drei Knoten in einer Spark-Instanz.  Der Hauptknoten führt zusätzliche Verwaltungsdienste wie Livy, YARN Resource Manager, Zookeeper und den Spark-Treiber aus.  Auf allen Knoten werden Dienste wie Node Agent und YARN Node Manager ausgeführt. Auf allen Workerknoten wird der Spark Executor-Dienst ausgeführt.

## <a name="node-sizes"></a>Knotengrößen

Ein Spark-Pool kann mit Knotengrößen definiert werden, die von einem Serverknoten der Größe „Small“ mit 8 virtuellen Kernen und 64 GB Arbeitsspeicher bis hin zu einem Serverknoten der Größe „XXLarge“ mit 64 virtuellen Kernen und 432 GB Arbeitsspeicher pro Knoten reichen. Die Knotengrößen können nach der Poolerstellung geändert werden. Dabei muss die Instanz jedoch möglicherweise neu gestartet werden.

|Size | Virtueller Kern | Arbeitsspeicher|
|-----|------|-------|
|Klein|4|32 GB|
|Medium|8|64 GB|
|Groß|16|128 GB|
|Extra groß|32|256 GB|
|XXLarge|64|432 GB|
|XXX Large (Isolated Compute)|80|504 GB|

## <a name="autoscale"></a>Autoscale

Mit Apache Spark-Pools können Sie Computeressourcen basierend auf dem Aktivitätsumfang automatisch hoch- bzw. herunterskalieren.  Wenn das Feature für die Autoskalierung aktiviert ist, können Sie die minimale und die maximale Anzahl der zu skalierenden Knoten festlegen.
Wenn das Feature deaktiviert ist, bleibt die Anzahl der festgelegten Knoten unverändert.  Diese Einstellung kann nach der Poolerstellung geändert werden. Dabei muss die Instanz jedoch möglicherweise neu gestartet werden.

## <a name="automatic-pause"></a>Automatische Pause

Das Feature für automatische Pausen gibt Ressourcen nach einer festgelegten Inaktivitätsperiode frei, sodass die Gesamtkosten eines Apache Spark-Pools reduziert werden.  Die Anzahl der Minuten der Inaktivitätsperiode kann festgelegt werden, sobald das Feature aktiviert wurde.  Das Feature für automatische Pausen ist unabhängig vom Feature für die Autoskalierung. Ressourcen können angehalten werden, unabhängig davon, ob die Autoskalierung aktiviert oder deaktiviert ist.  Diese Einstellung kann nach der Poolerstellung geändert werden. Dabei muss die Instanz jedoch möglicherweise neu gestartet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark-Dokumentation](https://spark.apache.org/docs/2.4.5/)