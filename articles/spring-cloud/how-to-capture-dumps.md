---
title: Manuelles Erfassen von Heapdump, Threaddump und JFR in Azure Spring Cloud
description: Erfahren Sie, wie Sie einen Heapdump oder einen Threaddump erfassen oder einen JFR manuell starten.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5f34dce5e177650b11858cabf770b7d63dc9b593
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000609"
---
# <a name="how-to-manually-capture-heap-dump-thread-dump-and-jfr-in-azure-spring-cloud"></a>Manuelles Erfassen von Heapdump, Threaddump und JFR in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ❌ C#

Eine effektive Problembehandlung, die für unsere Kunden entscheidend ist, um Probleme in ihrer Produktionsumgebung rechtzeitig zu beheben, stellt sicher, dass ihr Unternehmen immer online ist. In Azure Spring Cloud haben wir bereits heute Streaming von Anwendungsprotokollen und Abfragen, umfangreiche Metrikenausgabe und Warnungen, verteilte Ablaufverfolgung usw. bereitgestellt, um unsere Kunden in diesem Bereich zu unterstützen. Wenn Kunden jedoch Warnungen über hohe Latenz, JVM-Heapverluste oder hohe CPU-Auslastung erhalten, gibt es für sie keine Lösung für die letzte Meile. Daher haben wir das manuelle Generieren eines Heapdumps, das Generieren eines Threaddumps oder das Starten von JFR aktiviert.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Übung benötigen Sie Folgendes:

* Eine bereitgestellte Azure Spring Cloud-Dienstinstanz. Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](./quickstart.md).
* Mindestens eine bereits in Ihrer Dienstinstanz erstellte Anwendung.
* Mindestens einen [persistenten Speicher, der bereits an Ihre App gebunden ist](how-to-built-in-persistent-storage.md), um generierte Diagnosedateien zu speichern.

**Wenn Kunden den Pfad unter dem Einbindungspfad verwenden möchten, stellen Sie sicher, dass der Unterpfad bereits erstellt wurde.**

## <a name="generate-a-heap-dump"></a>Generieren eines Heapdumps
Generieren Sie einen Heapdump unserer App in Azure Spring Cloud.
```heap dump command
   az spring-cloud app deployment generate-heap-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="generate-a-thread-dump"></a>Generieren eines Threaddumps
Generieren Sie einen Threaddump unserer App in Azure Spring Cloud.
```thread dump command
   az spring-cloud app deployment generate-thread-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="start-jfr"></a>Starten von JFR
Starten Sie JFR für unsere App in Azure Spring Cloud.
```JFR command
   az spring-cloud app deployment start-JFR -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path> --duration <duration-of-JFR>
```
Der Standardwert für die Dauer ist 60 Sekunden.
## <a name="get-your-diagnostic-files"></a>Abrufen der Diagnosedateien
Wechseln Sie zum Zieldateipfad in Ihrem persistenten Speicher, und suchen Sie nach „dump/JFR“. Sie können sie auf Ihren lokalen Computer herunterladen. Der Name der generierten Datei lautet „{appInstance}\_heapdump\_{timeStamp}.hprof“ für Heapdump, „{appInstance}\_threaddump\_{timeStamp}.txt“ für Threaddump und „{appInstance}\_JFR\_{timeStamp}.jfr“ für JFR.
