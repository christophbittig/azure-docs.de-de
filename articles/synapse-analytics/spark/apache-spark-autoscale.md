---
title: Automatisches Skalieren von Apache Spark-Instanzen
description: Verwenden des Features für die automatische Skalierung von Azure Synapse zum automatischen Skalieren von Apache Spark-Instanzen
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: 7ca093294cb1782da5adeb02888696b38f57de4c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400108"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Automatisches Skalieren von Apache Spark-Pools in Azure Synapse Analytics

Mit der Funktion für automatische Skalierung in Apache Spark für Azure Synapse Analytics-Pools wird die Anzahl der Knoten in einer Clusterinstanz automatisch zentral hoch- oder herunterskaliert. Während der Erstellung eines neuen Apache Spark für Azure Synapse Analytics-Pools kann bei Auswahl der automatischen Skalierung eine minimale und maximale Anzahl von Knoten festgelegt werden. Die automatische Skalierung überwacht dann die Ressourcenanforderungen der Auslastung und skaliert die Anzahl von Knoten zentral hoch oder herunter. Für dieses Feature fallen keine zusätzlichen Gebühren an.

## <a name="metrics-monitoring"></a>Überwachen von Metriken

Die automatische Skalierung überwacht kontinuierlich die Spark-Instanz und sammelt die folgenden Metriken:

|Metrik|BESCHREIBUNG|
|---|---|
|CPU insgesamt für ausstehende|Die Gesamtanzahl von Kernen, die zum Starten der Ausführung aller ausstehenden Aufträge erforderlich sind.|
|Arbeitsspeicher insgesamt für ausstehende|Die Gesamtgröße des Arbeitsspeichers (in MB), die zum Starten der Ausführung aller ausstehenden Aufträge erforderlich ist.|
|Freie CPUs insgesamt|Die Summe aller nicht verwendeten Kerne auf den aktiven Knoten.|
|Freier Arbeitsspeicher insgesamt|Die Summe des nicht verwendeten Arbeitsspeichers (in MB) auf den aktiven Knoten.|
|Verwendeter Arbeitsspeicher pro Knoten|Die Auslastung eines Knotens. Ein Knoten, auf dem 10 GB Arbeitsspeicher verwendet werden, ist höher ausgelastet als ein Knoten, auf dem 2 GB verwendet werden.|

Die oben aufgeführten Metriken werden alle 30 Sekunden überprüft. Die Autoskalierung trifft auf Basis dieser Metriken Entscheidungen zum zentralen Hoch- und Herunterskalieren.

## <a name="load-based-scale-conditions"></a>Lastbasierte Skalierungsbedingungen

Wenn die folgenden Bedingungen erkannt werden, gibt die Autoskalierung eine Skalierungsanforderung aus:

|Zentrales Hochskalieren|Zentrales Herunterskalieren|
|---|---|
|„CPU insgesamt für ausstehende“ ist länger als eine Minute größer als die Anzahl der insgesamt freien CPUs.|„CPU insgesamt für ausstehende“ ist länger als 2 Minuten kleiner als die Anzahl der insgesamt freien CPUs.|
|„Arbeitsspeicher insgesamt für ausstehende“ ist länger als 1 Minute größer als der insgesamt freie Arbeitsspeicher.|„Arbeitsspeicher insgesamt für ausstehende“ ist länger als 2 Minuten kleiner als der insgesamt freie Arbeitsspeicher.|

Für das zentrale Hochskalieren berechnet der Azure Synapse-Autoskalierungsdienst, wie viele neue Knoten benötigt werden, um die aktuellen CPU- und Speicheranforderungen zu erfüllen, und gibt dann eine Anforderung für zentrales Hochskalieren aus, um die erforderliche Anzahl an Knoten hinzuzufügen.

Für das zentrale Herunterskalieren gibt die Autoskalierung basierend auf der Anzahl von Executors, Anwendungsmaster pro Knoten sowie den aktuellen CPU- und Arbeitsspeicheranforderungen eine Anforderung zum Entfernen einer bestimmten Anzahl von Knoten aus. Der Dienst erkennt auch, welche Knoten aufgrund der aktuellen Auftragsausführung für die Entfernung in Frage kommen. Der Vorgang des Herunterskalierens deaktiviert zunächst die Knoten und entfernt sie dann aus dem Cluster.

## <a name="get-started"></a>Erste Schritte

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>Erstellen eines serverlosen Apache Spark-Pools mit automatischer Skalierung

Um die Funktion für automatische Skalierung zu aktivieren, führen Sie als Teil des normalen Poolerstellungsvorgangs die folgenden Schritte aus:

1. Aktivieren Sie auf der Registerkarte **Grundlagen** das Kontrollkästchen **Automatische Skalierung aktivieren**.
1. Geben Sie die gewünschten Werte für die folgenden Eigenschaften ein:  

    * **Min**. Anzahl von Knoten
    * **Max**. Anzahl von Knoten

Die anfängliche Anzahl von Knoten ist der Minimalwert. Dieser Wert definiert die Anfangsgröße der Instanz bei der Erstellung. Die Mindestanzahl von Knoten darf nicht kleiner als drei sein.

Optional können Sie die dynamische Zuordnung von Executors in Szenarien aktivieren, in denen sich die Executoranforderungen in verschiedenen Phasen eines Spark-Auftrags stark unterscheiden oder die Menge der verarbeiteten Daten mit der Zeit schwankt. Durch Aktivieren der dynamischen Zuordnung von Executors kann die Kapazität nach Bedarf genutzt werden.

Beim Aktivieren der dynamischen Zuordnung von Executors während der Erstellung eines Spark-Pools kann die minimale und maximale Anzahl von Knoten in Abhängigkeit von den Grenzwerten der verfügbaren Knoten festgelegt werden. Diese Werte werden für jede neue Sitzung, die im Pool erstellt wird, als Standardwerte übernommen.

Apache Spark ermöglicht die Konfiguration der dynamischen Zuordnung von Executors über den folgenden Code:

```
    %%configure -f
    {
        "conf" : {
            "spark.dynamicAllocation.maxExecutors" : "6",
            "spark.dynamicAllocation.enable": "true",
            "spark.dynamicAllocation.minExecutors": "2"
     }
    }
```
Die durch den Code angegebenen Standardwerte setzen die über die Benutzeroberfläche festgelegten Werte außer Kraft.

Beim Aktivieren der dynamischen Zuordnung werden Executors basierend auf ihrer Auslastung hoch- oder herunterskaliert. Dadurch wird sichergestellt, dass die Executors entsprechend den Anforderungen des ausgeführten Auftrags bereitgestellt werden.

## <a name="best-practices"></a>Bewährte Methoden

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Berücksichtigen der Latenz von Vorgängen zum Hoch- bzw. Herunterskalieren

Es kann 1 bis 5 Minuten dauern, bis ein Skalierungsvorgang abgeschlossen ist.

### <a name="prepare-for-scaling-down"></a>Vorbereiten für das zentrale Herunterskalieren

Beim Herunterskalieren der Instanz werden die Knoten durch die automatische Skalierung in den Außerbetriebnahmezustand versetzt, sodass keine neuen Executors auf diesen Knoten gestartet werden können.

Die ausgeführten Aufträge werden weiterhin ausgeführt und abgeschlossen. Für die ausstehenden Aufträge wird auf die reguläre Einplanung mit weniger verfügbaren Knoten gewartet.

## <a name="next-steps"></a>Nächste Schritte

Schnellstart zum Einrichten eines neuen Spark-Pools [Erstellen eines Spark-Pools](../quickstart-create-apache-spark-pool-portal.md)
