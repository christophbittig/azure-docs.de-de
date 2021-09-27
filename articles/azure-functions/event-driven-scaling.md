---
title: Ereignisgesteuerte Skalierung in Azure Functions
description: Hier wird das Skalierungsverhalten von Funktions-Apps im Verbrauchs- und Premium-Plan erläutert.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: c67177570906e0122df6116f1a0a159d64f3486b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571309"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Ereignisgesteuerte Skalierung in Azure Functions

In den Verbrauchs- und Premium-Plänen skaliert Azure Functions CPU- und Arbeitsspeicherressourcen, indem zusätzliche Instanzen des Functions-Hosts hinzugefügt werden. Die Anzahl von Instanzen wird anhand der Anzahl der Ereignisse bestimmt, die eine Funktion auslösen. 

Jede Instanz des Functions-Hosts im Verbrauchsplan ist auf 1,5 GB Arbeitsspeicher und eine CPU beschränkt.  Eine Instanz des Hosts ist die gesamte Funktions-App, d. h., alle Funktionen innerhalb einer Funktions-App verwenden innerhalb einer Instanz dieselbe Ressource und werden gleichzeitig skaliert. Funktions-Apps, die denselben Verbrauchsplan nutzen, werden unabhängig voneinander skaliert.  Im Premium-Plan bestimmt die Plangröße den verfügbaren Arbeitsspeicher und die verfügbare CPU für alle Apps in diesem Plan für diese Instanz.  

Funktionscodedateien werden in Azure Files-Freigaben im Hauptspeicherkonto der Funktion gespeichert. Wenn Sie das Hauptspeicherkonto der Funktions-App löschen, werden die Funktionscodedateien gelöscht und können nicht wiederhergestellt werden.

## <a name="runtime-scaling"></a>Laufzeitskalierung

Azure Functions verwendet die Komponente *Skalierungscontroller*, um die Rate der Ereignisse zu überwachen und zu bestimmen, ob eine Auf- oder Abskalierung erforderlich ist. Der Skalierungscontroller verwendet Heuristik für jeden Triggertyp. Bei Verwendung eines Triggers für Azure Queue Storage beispielsweise basieren die Skalen auf der Länge der Warteschlange und dem Alter der ältesten Nachricht in der Warteschlange.

Die Skalierungseinheit für Azure Functions ist die Funktions-App. Bei einer horizontalen Hochskalierung der Funktions-App werden zusätzliche Ressourcen zugeordnet, um mehrere Instanzen des Azure Functions-Hosts auszuführen. Umgekehrt entfernt der Skalierungscontroller bei abnehmenden Computeanforderungen Instanzen des Functions-Hosts. Die Anzahl von Instanzen wird schließlich auf null abskaliert, wenn in einer Funktions-App keine Funktionen ausgeführt werden.

![Skalierungscontroller: Überwachen von Ereignissen und Erstellen von Instanzen](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Kaltstart

Wenn sich Ihre Funktions-App einige Minuten im Leerlauf befunden hat, skaliert die Plattform möglicherweise die Anzahl von Instanzen, auf denen Ihre App ausgeführt wird, auf 0. Bei der nächsten Anforderung kommt eine zusätzliche Latenz für die Skalierung von 0 auf 1 zum Tragen. Diese Latenz wird als _Kaltstart_ bezeichnet. Die Anzahl von Abhängigkeiten, die für Ihre Funktions-App erforderlich sind, kann sich auf die Dauer des Kaltstarts auswirken. Bei synchronen Vorgängen wie beispielsweise HTTP-Triggern, die eine Antwort zurückgeben müssen, ist ein Kaltstart ein größeres Problem. Wenn Kaltstarts Ihre Funktionen beeinträchtigen, sollten Sie die Ausführung in einem Premium-Plan oder einem Dedicated-Plan mit aktivierter **Always-On**-Einstellung in Erwägung ziehen.   

## <a name="understanding-scaling-behaviors"></a>Grundlegendes zum Verhalten von Skalierungen

Die Skalierung variiert ausgehend von verschiedenen Faktoren, und die Skalierung ist je nach dem ausgewählten Auslöser und der Sprache unterschiedlich. Es gibt einige Feinheiten des Skalierungsverhaltens, die zu beachten sind:

* **Maximale Anzahl von Instanzen:** Eine einzelne Funktions-App wird maximal auf 200 Instanzen erweitert. Eine einzelne Instanz kann mehrere Meldungen oder Anforderungen gleichzeitig verarbeiten, weshalb es keine Grenze für die Anzahl gleichzeitiger Ausführungen gibt.  Sie können [ein niedrigeres Maximum angeben](#limit-scale-out), um die Skalierung nach Bedarf zu drosseln.
* **Rate für neue Instanzen:** Bei HTTP-Triggern werden neue Instanzen höchstens einmal pro Sekunde zugeordnet. Bei Nicht-HTTP-Triggern werden neue Instanzen höchstens einmal alle 30 Sekunden zugeordnet. Die Skalierung ist schneller, wenn sie in einem [Premium-Plan](functions-premium-plan.md) ausgeführt wird.
* **Skalierungseffizienz:** Verwenden Sie für Service Bus-Trigger _Verwaltungsrechte_ für Ressourcen, um eine möglichst effiziente Skalierung zu erreichen. Mit _Lauschrechten_ ist die Skalierung unpräziser, da die Warteschlangenlänge nicht in Skalierungsentscheidungen einbezogen werden kann. Weitere Informationen zum Festlegen von Berechtigungen in Service Bus-Zugriffsrichtlinien finden Sie unter [SAS-Autorisierungsrichtlinien](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Informationen zu Event Hub-Triggern finden Sie im Referenzartikel im [Skalierungsleitfaden](functions-bindings-event-hubs-trigger.md#scaling). 

## <a name="limit-scale-out"></a>Begrenzen des horizontalen Hochskalierens

Möglicherweise möchten Sie die Anzahl von Instanzen einschränken, die von einer App für das Aufskalieren verwendet wird.  Dies ist am häufigsten der Fall, wenn der Durchsatz einer Downstreamkomponente wie einer Datenbank begrenzt ist.  Standardmäßig werden die Funktionen des Verbrauchsplans auf bis zu 200 Instanzen aufskaliert, und Premium-Planfunktionen werden auf bis zu 100 Instanzen aufskaliert.  Sie können einen niedrigeren Höchstwert für eine bestimmte App angeben, indem Sie den `functionAppScaleLimit`-Wert ändern.  Der `functionAppScaleLimit`-Wert kann auf `0` oder `null` festgelegt werden, wenn keine Einschränkungen erforderlich sind, oder auf einen gültigen Wert zwischen `1` und dem App-Maximum.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

```azurepowershell
$resource = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName <RESOURCE_GROUP> -Name <FUNCTION_APP-NAME>/config/web
$resource.Properties.functionAppScaleLimit = <SCALE_LIMIT>
$resource | Set-AzResource -Force
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Bewährte Methoden und Muster für skalierbare Apps

Es gibt viele Aspekte einer Funktions-App, die sich auf die Skalierung auswirken. Dazu zählen beispielsweise die Hostkonfiguration, der Runtimespeicherbedarf und die Ressourceneffizienz.  Weitere Informationen finden Sie im [Abschnitt zur Skalierbarkeit im Artikel zum Thema Leistung](performance-reliability.md#scalability-best-practices). Sie sollten auch das Verhalten von Verbindungen beim Skalieren Ihrer Funktions-App beachten. Weitere Informationen finden Sie unter [How to manage connections in Azure Functions](manage-connections.md) (Verwalten von Verbindungen in Azure Functions).

Weitere Informationen zum Skalieren in Python und Node.js finden Sie unter [Python-Entwicklerhandbuch für Azure Functions: Skalierung und Parallelität](functions-reference-python.md#scaling-and-performance) und [Node.js-Entwicklerhandbuch für Azure Functions: Skalierung und Parallelität](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Abrechnungsmodell

Die Abrechnung für die verschiedenen Pläne ist detailliert auf der [Preisseite für Azure Functions](https://azure.microsoft.com/pricing/details/functions/) beschrieben. Der Verbrauch wird auf Ebene der Funktions-App zusammengefasst, wobei nur die Zeit gezählt wird, für die der Funktionscode ausführt wurde. Folgende Einheiten werden für die Abrechnung verwendet:

* **Ressourcenverbrauch in Gigabytesekunden (GB-s)** – berechnet als Kombination aus Arbeitsspeichergröße und Ausführungsdauer für alle Funktionen in einer Funktions-App. 
* **Ausführungen** – werden bei jeder Ausführung einer Funktion als Antwort auf einen Ereignisauslöser gezählt.

Nützliche Fragen und Informationen zum Verständnis Ihrer Verbrauchsrechnung finden Sie [in den häufig gestellten Fragen zur Abrechnung](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Nächste Schritte

+ [Azure Functions-Hostingoptionen](functions-scale.md)

