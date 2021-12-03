---
title: Handbuch für den Übergang vom Azure Monitor-Ansicht-Designer zu Arbeitsmappen
description: Übergang von Ansichten zu Arbeitsmappen in Azure Monitor.
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 1c371a155f36574f7a443506c0b9090b6b3bd544
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447073"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Handbuch für den Übergang vom Azure Monitor-Ansicht-Designer zu Arbeitsmappen
Der [Ansicht-Designer](view-designer.md) ist eine Funktion in Azure Monitor, mit der Sie verschiedene benutzerdefinierten Ansichten erstellen können, die Ihnen bei der Visualisierung von Daten in Ihrem Log Analytics-Arbeitsbereich mittels Diagrammen, Listen und Zeitachsen helfen. Sie wurden in Arbeitsmappen umgewandelt, die einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals bieten. Dieser Artikel unterstützt Sie beim Übergang vom Ansichts-Designer zu Arbeitsmappen. 


## <a name="workbooks-overview"></a>Übersicht über Arbeitsmappen
[Arbeitsmappen](../vm/vminsights-workbooks.md) kombinieren Text,  [Protokollabfragen](/azure/data-explorer/kusto/query/), Metriken und Parameter zu umfassenden interaktiven Berichten. Teammitglieder mit demselben Zugriff auf Azure-Ressourcen können auch Arbeitsmappen bearbeiten.

Arbeitsmappen sind beispielsweise für folgende Szenarien hilfreich:

-   Untersuchen der Nutzung Ihres virtuellen Computers, wenn Sie noch nicht wissen, welche Metriken für Sie von Interesse sind: CPU-Auslastung, Speicherplatz auf dem Datenträger, Arbeitsspeicher, Netzwerkabhängigkeiten, usw. Im Gegensatz zu anderen Tools für die Analyse der Nutzung können Sie mit Arbeitsmappen mehrere Arten von Visualisierungen und Analysen kombinieren, sodass Sie bei der Untersuchung deutlich weniger stark eingeschränkt sind.
-   Präsentieren von Leistungsindikatoren und anderen Protokollereignissen, um Ihrem Team die Leistung eines kürzlich bereitgestellten virtuellen Computers zu veranschaulichen
-   Weitergeben der Ergebnisse eines Größenänderungsexperiments für Ihren virtuellen Computer an andere Mitglieder Ihres Teams. Sie können die Ziele des Experiments in Textform erläutern und anschließend die einzelnen Nutzungsmetriken und Analyseabfragen für die Auswertung des Experiments sowie eindeutige Anmerkungen dazu präsentieren, ob eine Metrik oberhalb oder unterhalb des Zielwerts lag.
-   Melden der Auswirkungen eines Ausfalls auf die Nutzung Ihres virtuellen Computers durch eine Kombination aus Daten, einer Erläuterung in Textform und einer Auflistung der nächsten Schritte, mit denen sich Ausfälle in Zukunft vermeiden lassen.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Gründe für das Umwandeln von Ansicht-Designer-Dashboards in Arbeitsmappen

Mit dem Ansicht-Designer können unterschiedliche abfragebasierte Ansichten und Visualisierungen generiert werden. Bei vielen allgemeinen Anpassungen bestehen jedoch weiterhin Einschränkungen, etwa beim Formatieren der Raster- und Kachellayouts oder beim Auswählen alternativer Grafiken zur Darstellung Ihrer Daten. Der Ansicht-Designer ist auf insgesamt neun verschiedene Kacheln für die Darstellung Ihrer Daten beschränkt.

Workbooks ist eine Plattform, mit der das volle Potenzial Ihrer Daten ausgeschöpft werden kann. Arbeitsmappen bieten nicht nur alle Funktionen, sondern unterstützen auch höhere Flexibilität und zusätzliche Funktionalitäten durch Text, Metriken, Parameter und vieles mehr. Beispielsweise können Benutzer mit Arbeitsmappen dichte Raster konsolidieren und Suchleisten hinzufügen, um Daten leicht filtern und analysieren zu können. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vorteile der Verwendung von Arbeitsmappen gegenüber dem Ansicht-Designer

* Unterstützung von sowohl Protokollen als auch Metriken.
* Persönliche Aufrufe für die individuelle Zugriffssteuerung und gemeinsamer Aufruf von Arbeitsmappen.
* Benutzerdefinierte Layoutoptionen mit Registerkarten, Größenanpassung und Steuerelementen für die Skalierung.
* Unterstützung von Abfragen über mehrere Log Analytics-Arbeitsbereich, Application Insights-Anwendungen und Abonnements hinweg.
* Benutzerdefinierte Parameter zur dynamischen Aktualisierung von zugeordneten Diagrammen und Visualisierungen.
* Unterstützung für Vorlagenkatalog über öffentliches GitHub.

Obwohl dieses Handbuch einfache Schritte zum direkten Neuerstellen mehrerer der häufig verwendeten Ansicht-Designer-Ansichten bietet, können Benutzer weiterhin mithilfe von Arbeitsmappen ihre eigenen benutzerdefinierten Visualisierungen und Metriken erstellen und entwerfen. Der folgende Screenshot stammt aus der [Vorlage für die Arbeitsbereichsverwendung](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) und zeigt ein Beispiel dafür, was mit Arbeitsmappen erstellt werden kann:


![Beispiel für die Anwendung von Arbeitsmappen](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Beginnen mit der Verwendung von Arbeitsmappen
Öffnen Sie Arbeitsmappen über die Kachel „Arbeitsmappen“ in Ihrem Log Analytics-Arbeitsbereich.

![Arbeitsmappennavigation](media/view-designer-conversion-overview/workbooks-nav.png)

Nach der Auswahl wird ein Katalog mit allen gespeicherten Arbeitsmappen und Vorlagen für Ihren Arbeitsbereich angezeigt.

![Arbeitsmappenkatalog](media/view-designer-conversion-overview/workbooks-gallery.png)

Um eine neue Arbeitsmappe zu starten, können Sie die Vorlage **Leer** unter **Schnellstart** oder das Symbol **Neu** in der oberen Navigationsleiste auswählen. Um Vorlagen anzuzeigen oder zu gespeicherten Arbeitsmappen zurückzukehren, wählen Sie das Element aus dem Katalog aus, oder suchen Sie in der Suchleiste nach dem Namen.

Zum Speichern einer Arbeitsmappe müssen Sie den Bericht mit einem bestimmten Titel, Abonnement, einer bestimmten Ressourcengruppe und einem bestimmten Ort speichern.
Die Arbeitsmappe wird automatisch mit denselben Einstellungen wie der LA-Arbeitsbereich ausgefüllt, mit demselben Abonnement und derselben Ressourcengruppe, doch Benutzer können diese Berichtseinstellungen ändern. Bei Arbeitsmappen handelt es sich um gemeinsam genutzte Ressourcen, für die Schreibzugriff auf die übergeordnete Ressourcengruppe erforderlich ist, damit sie gespeichert werden können.

## <a name="next-steps"></a>Nächste Schritte

- [Umwandlungsoptionen](view-designer-conversion-options.md)
