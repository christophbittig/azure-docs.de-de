---
title: 'Azure Monitor Application Insights für Java: Konfigurieren von JMX-Metriken'
description: In diesem Artikel erfahren Sie, wie Sie die Erfassung zusätzlicher JMX-Metriken für den Java-Agent von Azure Monitor Application Insights konfigurieren.
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 932ce7191aa64850dcf276f5906a038a0352ee80
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133878"
---
# <a name="configuring-jmx-metrics"></a>Konfigurieren von JMX-Metriken

Application Insights Java 3.x erfasst standardmäßig einige der JMX-Metriken, allerdings ist dies in vielen Fällen unzureichend. In dieser Dokumentation werden die JMX-Konfigurationsoptionen ausführlich beschrieben.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Wie werden zusätzliche JMX-Metriken erfasst?

Die Erfassung von JMX-Metriken kann durch Hinzufügen eines ```"jmxMetrics"```-Abschnitts zur Datei „applicationinsights.json“ konfiguriert werden. Sie können den Namen der Metrik auf die Weise festlegen, auf der sie im Azure-Portal in der Application Insights-Ressource angezeigt werden soll. Sie müssen den Objektnamen und das Attribut für alle Metriken definieren, die erfasst werden sollen.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Wie erfahre ich, welche Metriken konfiguriert werden können?

Diese Frage trifft den Nagel auf den Kopf: Sie müssen die Objektnamen und Attribute kennen, deren Eigenschaften sich für verschiedene Bibliotheken, Frameworks und Anwendungsserver unterscheiden sowie häufig unzureichend dokumentiert sind. Sie müssen die MBean-Struktur untersuchen, um die Objektnamen und Attribute zu erhalten. Ein verwaltetes Bean (MBean) ist ein verwaltetes Java-Objekt, das ein Gerät, eine Anwendung oder eine Ressource darstellen kann und eine Reihe von Attributen umfasst. 

Zum Anzeigen und Durchsuchen der verfügbaren Metriken wird die Verwendung von [Java Mission Control (JMC)](https://www.oracle.com/java/technologies/jdk-mission-control.html) empfohlen.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Wie navigiere in JMC zum Abrufen der richtigen Metriken?

Wenn Sie das Java Mission Control-Tool ausführen, haben Sie auf der linken Seite eine Auswahl von JVMs zur Verfügung, klicken Sie auf den entsprechenden Prozess unter der Registerkarte 'JVM Browser'. Warten Sie, bis JMC das Dashboard für den Prozess lädt, und wählen Sie unten den Reiter 'MBean Browser' (siehe unten). JMC muss sich im gleichen Ordner wie JVM befinden, und Ihr Prozess bzw. Ihre App muss ausgeführt werden.

![Screenshot: JMC MBean-Browser](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Wie erhalte ich die gewünschten Metriken und erforderlichen Attribute?

Im MBean-Browser wird die MBean-Struktur mit der Liste der Kategorien geöffnet, die erweitert werden können. Wenn Sie links eine Kategorie auswählen, wird rechts die Liste der Attribute geöffnet. Unten finden Sie ein Beispiel für eine Metrik, ihren Objektnamen und die Attribute. Die Attribute können wie im folgenden Beispiel geschachtelt sein.

![Screenshot: JMC MBean-Struktur](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Konfigurationsbeispiel

Im Folgenden werden einige Metriken anhand der Beispielauswahl im obigen Screenshot konfiguriert. Bei der ersten Konfiguration handelt es sich um ein Beispiel für eine geschachtelte Metrik namens `LastGcInfo`, die mehrere Eigenschaften umfasst. Das Ziel besteht darin, `GcThreadCount` zu erfassen.

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Welche Arten zu erfassender Metriken und Konfigurationsoptionen sind verfügbar?

Numerische und boolesche JMX-Metriken werden unterstützt. Andere Typen werden jedoch nicht unterstützt, diese werden daher ignoriert. 

Derzeit werden Platzhalterzeichen und aggregierte Attribute nicht unterstützt. Aus diesem Grund müssen alle Paare aus Objektnamen und Attributen separat konfiguriert werden. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Wo finde ich die JMX-Metriken in Application Insights?

Während Ihre Anwendung ausgeführt wird und die JMX-Metriken erfasst werden, können Sie diese anzeigen, indem Sie das Azure-Portal aufrufen und zu Ihrer Application Insights-Ressource navigieren. Klicken Sie wie unten gezeigt auf der Registerkarte „Metriken“ auf das Dropdownmenü, um die Metriken anzuzeigen.

![Screenshot: Metriken im Portal](media/java-ipa/jmx/jmx-portal.png)
