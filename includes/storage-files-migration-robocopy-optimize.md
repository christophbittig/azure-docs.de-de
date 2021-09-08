---
title: Datei einfügen
description: include file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 12a11fadaa7a98b9a5d2d9f81cf91dfa26680f56
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462190"
---
Die Geschwindigkeit und die Erfolgsrate einer bestimmten Robocopy-Ausführung hängt von mehreren Faktoren ab:

* IOPS im Quell- und Zielspeicher
* Verfügbare Netzwerkbandbreite zwischen Quelle und Ziel
* Schnelle Verarbeitung von Dateien und Ordnern in einem Namespace
* Anzahl von Änderungen zwischen Robocopy-Ausführungen


### <a name="iops-and-bandwidth-considerations"></a>IOPS- und Bandbreitenaspekte

In dieser Kategorie geht es um die Leistung des **Quellspeichers**, des **Zielspeichers** und des für die Verbindung verwendeten **Netzwerks**. Der maximal mögliche Durchsatz wird durch die langsamste dieser drei Komponenten bestimmt. Stellen Sie sicher, dass Ihre Netzwerkinfrastruktur so konfiguriert ist, dass optimale Übertragungsgeschwindigkeiten unterstützt werden und die bestmögliche Leistung erzielt wird.

> [!CAUTION]
> Schnellstmögliche Kopiervorgänge sind zwar häufig wünschenswert, aber Sie sollten auch die Auslastung Ihres lokalen Netzwerks und der NAS-Appliance in Bezug auf andere – häufig unternehmenskritische – Aufgaben berücksichtigen.

Unter Umständen sind schnellstmögliche Kopiervorgänge nicht wünschenswert, wenn das Risiko besteht, dass die verfügbaren Ressourcen von der Migration „monopolisiert“ werden.

* Überlegen Sie sich, welche Zeiten für die Durchführung von Migrationen in Ihrer Umgebung am besten geeignet sind: tagsüber, außerhalb der Geschäftszeiten oder an Wochenenden.
* Erwägen Sie auch, die QoS-Funktion (Quality of Service) für das Netzwerk auf einer Windows Server-Instanz zu nutzen, um die Robocopy-Geschwindigkeit zu drosseln.
* Vermeiden Sie für die Migrationstools unnötigen Arbeitsaufwand.

Mit Robocopy können Verzögerungen zwischen Paketen eingefügt werden. Hierzu wird der Switch `/IPG:n` angegeben, bei dem `n` für den Abstand zwischen Robocopy-Paketen in Millisekunden steht. Mit diesem Switch können Sie die Monopolisierung von Ressourcen sowohl auf Geräten mit E/A-Einschränkungen als auch auf überlasteten Netzwerkverknüpfungen vermeiden.

`/IPG:n` kann nicht genutzt werden, um eine präzise Netzwerk-Bandbreiteneinschränkung auf einen bestimmten MBit/s-Wert zu erreichen. Verwenden Sie stattdessen Netzwerk-QoS für Windows Server. Bei Robocopy wird für alle Netzwerkanforderungen ausschließlich das SMB-Protokoll verwendet. Die Verwendung von SMB ist der Grund dafür, warum der Netzwerkdurchsatz von Robocopy selbst nicht beeinflusst werden kann, es aber ggf. zu einer Verlangsamung bei der Nutzung kommt. 

Ein ähnlicher Ansatz gilt für den IOPS-Wert von Network Attached Storage (NAS). Die Clustergröße auf dem NAS-Volume, die Paketgrößen und einige andere Faktoren wirken sich auf den IOPS-Wert aus. Die Nutzung von Verzögerungen zwischen Paketen ist häufig die einfachste Möglichkeit zum Steuern der Last für NAS. Führen Sie Tests mit mehreren Werten durch, z. B. für ca. 20 Millisekunden (n=20) und dann für entsprechende Vielfache dieses Werts. Nach der Einführung einer Verzögerung können Sie auswerten, ob Ihre anderen Apps nun wie erwartet funktionieren. Mit dieser Optimierungsstrategie können Sie die optimale Robocopy-Geschwindigkeit für Ihre Umgebung ermitteln.

### <a name="processing-speed"></a>Verarbeitungsgeschwindigkeit

Robocopy durchläuft den Namespace, auf den jeweils verwiesen wird, und wertet jede Datei und jeden Ordner in Bezug auf den Kopiervorgang aus. Für jede Datei werden ein erstmaliger Kopiervorgang und dann weitere Kopiervorgänge ausgewertet. Ein Beispiel hierfür sind wiederholte Ausführungen der Robocopy-Option „/MIR“ für dieselben Quell- und Zielspeicherorte. Diese wiederholten Ausführungen sind hilfreich, um die Downtime für Benutzer und Apps gering zu halten und die allgemeine Erfolgsrate für die migrierten Dateien zu verbessern.

Häufig wird die Bandbreite als der am stärksten einschränkende Faktor bei der Migration angesehen – und dies kann auch tatsächlich der Fall sein. Die Möglichkeit zum Enumerieren eines Namespace kann aber dazu führen, dass sich die Gesamtkopierdauer für größere Namespaces mit kleineren Dateien noch weiter erhöht. Beachten Sie, dass das Kopieren von 1 TiB an kleinen Dateien erheblich länger dauert, als wenn eine Datenmenge von 1 TiB kopiert wird, die eine geringere Zahl von größeren Dateien umfasst. Bei dieser Aussage wird vorausgesetzt, dass alle anderen Variablen gleich bleiben.

Die Ursache für diesen Unterschied ist die Verarbeitungsleistung, die für das Durchlaufen eines Namespace erforderlich ist. Robocopy unterstützt Multithreadkopien über den Parameter `/MT:n`, wobei „n“ für die Anzahl der zu verwendenden Threads steht. Wenn Sie einen Computer speziell für Robocopy bereitstellen, sollten Sie also auf die Anzahl von Prozessorkernen und deren Beziehung zur jeweiligen Threadanzahl achten. Eine gängige Vorgehensweise ist die Nutzung von zwei Threads pro Kern. Die Anzahl von Kernen und Threads eines Computers stellt einen wichtigen Datenpunkt dar, der hilfreich beim Treffen der Entscheidung ist, welche Multithread-Werte (`/MT:n`) Sie angeben sollten. Berücksichtigen Sie auch, wie viele Robocopy-Aufträge Sie auf einem bestimmten Computer parallel ausführen möchten.

Bei einer höheren Anzahl von Threads verläuft der Kopiervorgang für die kleinen Dateien aus dem Beispiel mit 1 TiB erheblich schneller als bei einer geringeren Anzahl von Threads. Gleichzeitig kann es sein, dass die zusätzliche Investition in Ressourcen beim Beispiel mit 1 TiB an größeren Dateien nicht zu proportionalen Vorteilen führt. Bei einer höheren Anzahl von Threads wird versucht, eine größere Zahl von großen Dateien gleichzeitig über das Netzwerk zu kopieren. Durch diese zusätzliche Netzwerkaktivität erhöht sich die Wahrscheinlichkeit, dass es zu Einschränkungen in Bezug auf den Durchsatz oder den Speicher-IOPS-Wert kommt.

Beim ersten Kopieren in ein leeres Ziel mithilfe von RoboCopy oder bei einem differenziellen Vorgang mit zahlreichen geänderten Dateien ist Ihr Netzwerkdurchsatz wahrscheinlich ein limitierender Faktor. Beginnen Sie mit einer hohen Threadanzahl für eine erste Ausführung. Eine hohe Threadanzahl, die über die derzeit verfügbaren Threads auf dem Computer hinausgeht, hilft bei der Auslastung der verfügbaren Netzwerkbandbreite. Nachfolgende /MIR-Ausführungen werden progressiv durch die Verarbeitung von Elementen beeinflusst. Weniger Änderungen bei einem differenziellen Vorgang bedeuten weniger Datentransporte über das Netzwerk. Ihre Geschwindigkeit hängt nun weniger von der Übertragungsgeschwindigkeit über die Netzwerkverbindung und vielmehr von der Verarbeitungsgeschwindigkeit für Namespaceelemente ab. Stimmen Sie bei nachfolgenden Ausführungen den Threadanzahlwert auf die Prozessorkernanzahl und die Threadanzahl pro Kern ab. Berücksichtigen Sie, ob Kerne für andere Aufgaben reserviert werden müssen, die ein Produktionsserver möglicherweise hat.

> [!TIP]
> Faustregel: Die erste RoboCopy-Ausführung, bei der viele Daten über ein Netzwerk mit höherer Latenz verschoben werden, profitiert von einer Überbereitstellung bei der Anzahl der Threads (`/MT:n`). Bei nachfolgenden Ausführungen werden weniger Unterschiede kopiert, und es ist wahrscheinlicher, dass Sie vom eingeschränkten Netzwerkdurchsatz auf eingeschränkten Computeressourcen umschalten. Unter diesen Umständen ist es oft besser, die Anzahl der Threads für robocopy an die tatsächlich verfügbaren Threads auf dem Computer anzupassen. Eine Überbereitstellung in diesem Szenario kann zu mehr Kontextverschiebungen im Prozessor führen, was Ihren Kopiervorgang möglicherweise verlangsamt.

### <a name="avoid-unnecessary-work"></a>Vermeiden von unnötigem Arbeitsaufwand

Vermeiden Sie es, in Ihrem Namespace umfangreiche Änderungen vorzunehmen. Beispiele hierfür sind das Verschieben von Dateien zwischen Verzeichnissen und das Ändern von Eigenschaften im großen Stil oder von Berechtigungen (NTFS-ACLs). Insbesondere ACL-Änderungen können starke Auswirkungen haben, weil diese häufig zu kaskadierenden Änderungen von Dateien führen, die sich in den untergeordneten Ordnern der Hierarchie befinden. Dies kann die folgenden Konsequenzen haben:

* Längere Ausführungsdauer für Robocopy-Aufträge, weil jede Datei und jeder Ordner, die bzw. der von einer ACL-Änderung betroffen ist, aktualisiert werden muss
* Wiederverwendete Daten, die vorher verschoben wurden, müssen ggf. erneut kopiert werden Es müssen beispielsweise mehr Daten kopiert werden, wenn sich die Ordnerstrukturen ändern, nachdem Dateien zuvor bereits kopiert wurden. Bei einem Robocopy-Auftrag kann eine Namespaceänderung nicht „wiedergegeben“ werden. Die Dateien, die zuvor in die alte Ordnerstruktur übertragen wurden, müssen beim nächsten Auftrag bereinigt und in die neue Ordnerstruktur hochgeladen werden.

Ein weiterer wichtiger Aspekt ist die effektive Nutzung des Robocopy-Tools. Mit dem empfohlenen Robocopy-Skript erstellen und speichern Sie eine für Fehler bestimmte Protokolldatei. Das etwaige Auftreten von Kopierfehlern ist hierbei normal. Diese Fehler machen es häufig erforderlich, ein Kopiertool wie Robocopy mehrfach auszuführen. Ein Beispiel hierfür ist eine erstmalige Ausführung, z. B. aus NAS auf eine Data Box oder von einem Server auf eine Azure-Dateifreigabe. Anschließend werden mit dem Switch „/MIR“ zusätzlich eine oder mehrere weitere Ausführungen durchgeführt, um den Vorgang für nicht kopierte Dateien zu wiederholen.

Sie sollten darauf vorbereitet sein, Robocopy für einen bestimmten Namespacebereich mehrfach auszuführen. Aufeinanderfolgende Ausführungen können schneller abgeschlossen werden, weil weniger Daten kopiert werden müssen. Einschränkungen ergeben sich hierbei aber vermehrt aus der Geschwindigkeit bei der Verarbeitung des Namespace. Bei mehreren Ausführungen können Sie jeden Durchlauf beschleunigen, indem Sie festlegen, dass Robocopy nicht mit unnötig hohem Aufwand versuchen soll, bei einem Durchlauf alles zu kopieren. Die folgenden Robocopy-Switches können einen signifikanten Unterschied ausmachen:

* `/R:n`: „n“ steht hierbei dafür, wie oft Sie versuchen, eine nicht kopierte Datei erneut zu kopieren. 
* `/W:n`: Mit „n“ wird angegeben, wie viele Sekunden lang zwischen den Wiederholungen gewartet werden soll.

`/R:5 /W:5`: Eine gängige Einstellung, die Sie je nach Ihren Anforderungen anpassen können. Bei dieser Beispieleinstellung werden für eine nicht kopierte Datei fünf Wiederholungsversuche durchgeführt, und zwischen den Wiederholungen wird jeweils fünf Sekunden gewartet. Falls die Datei immer noch nicht kopiert werden kann, wird dies beim nächsten Robocopy-Auftrag erneut versucht. Mit diesem Ansatz ist der Kopiervorgang für Dateien, die aufgrund einer gegenwärtigen Nutzung oder von Timeoutproblemen nicht kopiert werden können, in vielen Fällen schließlich erfolgreich.
