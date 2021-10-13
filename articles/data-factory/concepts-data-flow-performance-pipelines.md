---
title: Optimieren der Pipelineleistung in Zuordnungsdatenflüssen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie mehr über das Optimieren der Datenflussausführung in Pipelines in Azure Data Factory- und Azure Synapse Analytics-Pipelines.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293557"
---
# <a name="using-data-flows-in-pipelines"></a>Verwenden von Datenflüssen in Pipelines 

Wenn Sie komplexe Pipelines mit mehreren Datenflüssen erstellen, kann der logische Datenfluss große Auswirkungen auf Zeit und Kosten haben. In diesem Abschnitt werden die Auswirkungen verschiedener Architekturstrategien behandelt.

## <a name="executing-data-flows-in-parallel"></a>Paralleles Ausführen von Datenflüssen

Wenn Sie mehrere Datenflüsse parallel ausführen, richtet der Dienst separate Spark-Cluster für jede Aktivität ein. Dadurch kann jeder Auftrag isoliert und parallel ausgeführt werden, führt aber dazu, dass mehrere Cluster gleichzeitig ausgeführt werden.

Bei paralleler Ausführung Ihrer Datenflüsse sollten Sie die Eigenschaft für die Gültigkeitsdauer der Azure IR nicht aktivieren, da hieraus mehrere nicht verwendete aktive Pools resultieren.

> [!TIP]
> Anstatt denselben Datenfluss mehrmals für jede Aktivität auszuführen, können Sie die Daten in einem Data Lake bereitstellen und Platzhalterpfade verwenden, um die Daten in einem einzelnen Datenfluss zu verarbeiten.

## <a name="execute-data-flows-sequentially"></a>Sequenzielles Ausführen von Datenflüssen

Wenn Sie Ihre Datenflussaktivitäten nacheinander ausführen, empfiehlt es sich, in der Azure IR-Konfiguration eine Gültigkeitsdauer festzulegen. Die Computeressourcen werden vom Dienst wiederverwendet, wodurch die Startzeit des Clusters verkürzt wird. Jede Aktivität wird weiterhin isoliert und erhält einen neuen Spark-Kontext für jede Ausführung. Aktivieren Sie das Kontrollkästchen **Schnelle Wiederverwendung** auf der Azure Integration Runtime-Instanz, um den Dienst anzuweisen, den vorhandenen Cluster erneut zu verwenden. So können Sie die Dauer zwischen sequenziellen Aktivitäten noch weiter reduzieren.

## <a name="overloading-a-single-data-flow"></a>Überladen eines einzelnen Datenflusses

Wenn Sie die gesamte Logik innerhalb eines einzelnen Datenflusses anordnen, führt der Dienst den gesamten Auftrag auf einer einzelnen Spark-Instanz aus. Dies mag zwar als eine Möglichkeit zur Kostenreduzierung erscheinen, doch werden unterschiedliche logische Datenflüsse kombiniert und das Überwachen und Debuggen kann schwierig sein. Wenn eine Komponente ausfällt, können auch alle anderen Teile des Auftrags nicht ausgeführt werden. Es wird empfohlen, Datenflüsse nach unabhängigen Flows der Geschäftslogik zu organisieren. Wenn der Datenfluss zu groß wird, werden Überwachung und Debugging durch Aufteilen in separate Komponenten erleichtert. Es gibt zwar kein festes Limit für die Anzahl von Transformationen in einem Datenfluss, doch wird der Auftrag bei zu vielen Transformationen sehr komplex.

## <a name="execute-sinks-in-parallel"></a>Paralleles Ausführen von Senken

Beim Standardverhalten von Datenflusssenken wird jede Senke sequenziell nacheinander ausgeführt, und der Datenfluss schlägt fehl, wenn ein Fehler in der Senke auftritt. Außerdem werden alle Senken standardmäßig der gleichen Gruppe zugeordnet, es sei denn, Sie bearbeiten die Datenflusseigenschaften und legen unterschiedliche Prioritäten für die Senken fest.

Datenflüsse ermöglichen es Ihnen, Senken über die Registerkarte für Datenflusseigenschaften im Benutzeroberflächendesigner in Gruppen zusammenfassen. Sie können sowohl die Ausführungsreihenfolge der Senken festlegen als auch Senken unter Verwendung derselben Gruppennummer gruppieren. Um die Verwaltung von Gruppen zu erleichtern, können Sie den Dienst anweisen, Senken aus der gleichen Gruppe parallel auszuführen.

In der Pipeline zum Ausführen der Datenflussaktivität befindet sich unter dem Abschnitt „Senkeneigenschaften“ eine Option, mit der das parallele Laden von Senken aktiviert werden kann. Wenn Sie die parallele Ausführung aktivieren, weisen Sie Datenflüsse an, gleichzeitig und nicht sequenziell in verbundene Senken zu schreiben. Um die Option für parallele Ausführung zu verwenden, müssen die Senken in einer Gruppe zusammengefasst und über eine neue Verzweigung oder bedingte Teilung mit demselben Datenstrom verbunden sein.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die Datenflussleistung](concepts-data-flow-performance.md)
- [Optimieren von Quellen](concepts-data-flow-performance-sources.md)
- [Optimieren von Senken](concepts-data-flow-performance-sinks.md)
- [Optimieren von Transformationen](concepts-data-flow-performance-transformations.md)

Lesen Sie die folgenden Artikel zu Datenflüssen in Bezug auf die Leistung:

- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)
- [Überwachen der Datenflussleistung](concepts-data-flow-monitoring.md)
- [Leistung der Integration Runtime](concepts-integration-runtime-performance.md)
