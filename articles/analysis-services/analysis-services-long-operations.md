---
title: Bewährte Methoden für zeitintensive Vorgänge in Azure Analysis Services | Microsoft-Dokumentation
description: In diesem Artikel werden bewährte Methoden für zeitintensive Vorgänge beschrieben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: owend
ms.openlocfilehash: 65d1b6cbee1040846fec2d8e1dd8153eedf9eabc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580963"
---
# <a name="best-practices-for-long-running-operations"></a>Bewährte Methoden für zeitintensive Vorgänge

In Azure Analysis Services stellt ein *Knoten* einen virtuellen Hostcomputer dar, auf dem eine Serverressource ausgeführt wird. Einige Vorgänge, z. B. zeitintensive Abfragen, Aktualisierungsvorgänge und die Synchronisierung der horizontalen Skalierung von Abfragen, können fehlschlagen, wenn eine Serverressource auf einen anderen Knoten verschoben wird. Häufige Fehlermeldungen in diesem Szenario sind unter anderem:

- „Fehler beim Versuch, eine zeitintensive XMLA-Anforderung zu finden. Die Anforderung wurde möglicherweise durch ein Dienstupgrade oder einen Serverneustart unterbrochen.“
- „Der Auftrag mit der ID '\<guid\>für das Modell '\<database\>' wurde aufgrund eines Dienstfehlers (Inaktivität) mit der Meldung 'Die Aktualisierungsanforderung wird abgebrochen, weil sie ohne Aktualisierungen hängengeblieben ist. Dies ist ein internes Dienstproblem. Senden Sie den Auftrag oder die Datei erneut, oder öffnen Sie ein Ticket, um Hilfe zu erhalten, wenn dieses Problem wiederholt auftritt.“

Es gibt zahlreiche Gründe, warum zeitintensive Vorgänge unterbrochen werden können. Beispielsweise Updates in Azure wie: 
- Patches für Betriebssysteme 
- Sicherheitsupdates
- Azure Analysis Services-Dienstupdates
- Service Fabric-Updates. Service Fabric ist eine Plattformkomponente, die von einer Reihe von Microsoft Cloud Services verwendet wird, einschließlich Azure Analysis Services.

Neben den im Dienst auftretenden Updates gibt es eine natürliche Verlagerung von Diensten zwischen Knoten aufgrund des Lastenausgleichs. Knotenbewegungen gehören zum erwartungsgemäßen Verhalten eines Clouddiensts. Azure Analysis Services versucht, die Auswirkungen von Verlagerungen zwischen Knoten zu minimieren, doch es ist unmöglich, sie vollständig zu beseitigen. 

Zusätzlich zu Verlagerungen zwischen Knoten können noch andere Fehler auftreten. Beispielsweise kann ein Datenquellen-Datenbanksystem offline sein, oder die Netzwerkkonnektivität geht verloren. Wenn während einer Aktualisierung eine Partition 10 Millionen Zeilen hat, und in der 9-millionsten Zeile tritt ein Fehler auf, gibt es keine Möglichkeit, die Aktualisierung ab der Stelle des Fehlers neu zu starten. Der Dienst muss von Anfang an neu gestartet werden. 

## <a name="refresh-rest-api"></a>Aktualisieren mit der REST-API

Dienstunterbrechungen können für zeitintensive Vorgänge wie eine Datenaktualisierung eine Herausforderung darstellen. Azure Analysis Services enthält eine Rest-API, um negative Auswirkungen von Dienstunterbrechungen zu verringern. Weitere Informationen finden Sie unter [Asynchrones Aktualisieren mit der REST-API](analysis-services-async-refresh.md).
 
Neben der REST-API gibt es auch andere Ansätze, die Sie verwenden können, um potenzielle Probleme bei zeitintensiven Aktualisierungsvorgängen zu minimieren. Das Hauptziel besteht darin, zu vermeiden, dass der Aktualisierungsvorgang von Anfang an neu gestartet werden muss, und Aktualisierungen stattdessen in kleineren Batches auszuführen, für ein Commit in Phasen ausgeführt werden kann. 
 
Die REST-API gestattet einen solchen Neustart, ermöglicht aber keine vollständige Flexibilität beim Erstellen und Löschen von Partitionen. Wenn ein Szenario komplexe Datenverwaltungsvorgänge erfordert, sollte die Logik Ihrer Lösung eine Form der Batchverarbeitung enthalten. Wenn beispielsweise Transaktionen verwendet werden, um Daten in mehreren gesonderten Batches zu verarbeiten, ermöglicht dies, dass bei einem Fehler kein Neustart von Anfang an erforderlich ist, sondern dieser von einem Zwischenprüfpunkt aus erfolgen kann. 
 
## <a name="scale-out-query-replicas"></a>Horizontale Skalierung von Abfragereplikaten

Unabhängig davon, ob Sie eine REST- oder benutzerdefinierte Logik verwenden, können Clientanwendungsabfragen weiterhin inkonsistente oder Zwischenergebnisse zurückgeben, während Batches verarbeitet werden. Wenn es erforderlich ist, dass während der Verarbeitung konsistente Daten von Clientanwendungsabfragen zurückgegeben werden, und die Modelldaten sich in einem Zwischenzustand befinden, können Sie [horizontale Skalierung](analysis-services-scale-out.md) mit schreibgeschützten Abfragereplikaten verwenden.

Durch die Verwendung von schreibgeschützten Abfragereplikaten während der Durchführung von Aktualisierungen in Batches können Clientanwendungsbenutzer weiterhin die alte Momentaufnahme der Daten auf den schreibgeschützten Replikaten abfragen. Sobald die Aktualisierungen abgeschlossen sind, kann ein Synchronisierungsvorgang ausgeführt werden, um die schreibgeschützten Replikate auf den neuesten Stand zu bringen.


## <a name="next-steps"></a>Nächste Schritte

[Asynchrones Aktualisieren mit der REST-API](analysis-services-async-refresh.md)  
[Horizontales Hochskalieren von Azure Analysis Services](analysis-services-scale-out.md)  
[Hochverfügbarkeit für Analysis Services](analysis-services-bcdr.md)  
[Wiederholungsanleitung für Azure-Dienste](/azure/architecture/best-practices/retry-service-specific)