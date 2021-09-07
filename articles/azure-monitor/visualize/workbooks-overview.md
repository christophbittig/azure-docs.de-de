---
title: Übersicht über Azure Monitor-Arbeitsmappen
description: Erfahren Sie, wie Arbeitsmappen einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals bieten.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c9a535e06278146f89f2c8f6a1844813c2417a1a
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706920"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor-Arbeitsmappen

Arbeitsmappen bieten einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals. Mit ihnen können Sie mehrere Datenquellen in Azure nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren.

Im Folgenden finden Sie eine Videoanleitung zum Erstellen von Arbeitsmappen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

> [!NOTE]
> Legacyarbeitsmappen und private Arbeitsmappen wurden entfernt. Verwenden Sie das [Tool zum Abrufen von Arbeitsmappen](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/LegacyAI/DeprecatedWorkbookRetrievalTool.md), um den Inhalt Ihrer alten Arbeitsmappe abzurufen.

## <a name="data-sources"></a>Datenquellen

Arbeitsmappen können Daten aus mehreren Quellen in Azure abfragen. Die Ersteller von Arbeitsmappen können diese Daten transformieren, um Einblicke in die Verfügbarkeit, Leistung, Nutzung und Gesamtintegrität der zugrunde liegenden Komponenten zu erhalten. Beispielsweise können Leistungsprotokolle von virtuellen Computern analysiert werden, um Fälle von hoher CPU-Auslastung oder geringem Arbeitsspeicher zu ermitteln, und die Ergebnisse können dann als Raster in einem interaktiven Bericht angezeigt werden.
  
Die eigentliche Stärke von Arbeitsmappen ist jedoch die Möglichkeit, Daten aus unterschiedlichen Quellen in einem einzigen Bericht zu kombinieren. Dies ermöglicht die Erstellung von zusammengesetzten Ressourcenansichten oder ressourcenübergreifenden Verknüpfungen für umfassendere Daten und Erkenntnisse, die andernfalls nicht möglich wären.

Arbeitsmappen sind derzeit mit den folgenden Datenquellen kompatibel:

* [Protokolle](../visualize/workbooks-data-sources.md#logs)
* [Metriken](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Warnungen (Vorschau)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Workloadintegrität](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualisierungen

Arbeitsmappen bieten eine Vielzahl von Funktionen zur Visualisierung Ihrer Daten. Ausführliche Beispiele der einzelnen Visualisierungstypen stehen über die folgenden Links bereit:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Diagramme](../visualize/workbooks-chart-visualizations.md)
* [Raster](../visualize/workbooks-grid-visualizations.md)
* [Kacheln](../visualize/workbooks-tile-visualizations.md)
* [Strukturen](../visualize/workbooks-tree-visualizations.md)
* [Graphs](../visualize/workbooks-graph-visualizations.md)
* [Gestapelte Säule](../visualize/workbooks-composite-bar.md)
* [Wabenförmig](workbooks-honey-comb.md)
* [Map](workbooks-map-visualizations.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Beispiele der Visualisierung von Arbeitsmappen." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Anheften von Visualisierungen

Die Schritte für Text, Abfrage und Metriken in einer Arbeitsmappe können mithilfe der Schaltfläche „Anheften“ an diese Elemente angeheftet werden, während sich die Arbeitsmappe im entsprechenden Modus befindet oder wenn der Arbeitsmappenautor Einstellungen für dieses Element aktiviert hat, damit das Symbol zum Anheften angezeigt wird.

Wählen Sie zum Aktivieren des Anheftmodus **Bearbeiten** aus, um in den Bearbeitungsmodus zu wechseln, und wählen Sie dann auf der oberen Leiste das blaue Stecknadelsymbol aus. Daraufhin wird auf der rechten Seite des Bildschirms ein einzelnes Stecknadelsymbol oberhalb des Felds *Bearbeiten* für den entsprechenden Arbeitsmappenteil angezeigt.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Screenshot der Anheftfunktion" border="false":::

> [!NOTE]
> Der Zustand der Arbeitsmappe wird zum Zeitpunkt des Anheftens gespeichert, und angeheftete Arbeitsmappen auf einem Dashboard werden beim Ändern der zugrunde liegenden Arbeitsmappe nicht aktualisiert. Um einen angehefteten Arbeitsmappenteil zu aktualisieren, müssen Sie diesen Teil löschen und erneut anheften.

## <a name="getting-started"></a>Erste Schritte

Zum Erkunden der Oberfläche für Arbeitsmappen navigieren Sie zunächst zum Azure Monitor-Dienst. Dazu können Sie **Monitor** in das Suchfeld im Azure-Portal eingeben.

Wählen Sie dann **Arbeitsmappen** aus.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Screenshot der Schaltfläche „Arbeitsmappen“ in einem roten Kasten." border="false":::

### <a name="gallery"></a>Galerie

Der Katalog erleichtert das Organisieren, Sortieren und Verwalten von Arbeitsmappen jedes Typs.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Screenshot des Katalogs auf der Registerkarte „Alle“" lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Registerkarten des Katalogs

Der Katalog enthält vier Registerkarten zum Organisieren von Arbeitsmappentypen.

| Registerkarte              | BESCHREIBUNG                                       |
|------------------|---------------------------------------------------|
| Alle | Hier werden die vier wichtigsten Elemente für jeden Typ angezeigt: Arbeitsmappen, öffentliche Vorlagen und eigene Vorlagen. Arbeitsmappen werden nach Änderungsdatum sortiert, sodass die acht zuletzt geänderten Arbeitsmappen angezeigt werden.|
| Arbeitsmappen | Hier wird die Liste aller verfügbaren Arbeitsmappen angezeigt, die Sie erstellt haben oder für Sie freigegeben wurden. |
| Öffentliche Vorlagen | Hier wird die Liste aller verfügbaren, sofort einsatzbereiten und funktionalen Arbeitsmappenvorlagen angezeigt, die von Microsoft veröffentlicht wurden. Diese sind nach Kategorien gruppiert. |
| Meine Vorlagen | Hier wird die Liste aller verfügbaren und bereitgestellten Arbeitsmappenvorlagen angezeigt, die Sie erstellt haben oder für Sie freigegeben wurden. Diese sind nach Kategorien gruppiert. |

#### <a name="features"></a>Features

* Auf jeder Registerkarte befindet sich ein Raster mit Informationen zu den Arbeitsmappen. Es enthält: Beschreibung, Datum der letzten Änderung, Tags, Abonnement, Ressourcengruppe, Region und Freigabestatus. Sie können die Arbeitsmappen auch nach diesen Informationen sortieren.
* Filtern Sie nach Ressourcengruppe, Abonnements, Arbeitsmappen-/Vorlagenname oder Vorlagenkategorie.
* Wählen Sie mehrere Arbeitsmappen zum Löschen oder Massenlöschen aus.
* Jede Arbeitsmappe verfügt über ein Kontextmenü (Auslassungspunkte/drei Punkte am Ende), bei dessen Auswahl eine Liste mit schnellen Aktionen geöffnet wird.
    * Anzeigen der Ressource: Rufen Sie die Registerkarte „Arbeitsmappenressource“ auf, um die Ressourcen-ID der Arbeitsmappe anzuzeigen, Tags hinzuzufügen, Sperren zu verwalten usw.
    * Löschen oder Umbenennen der Arbeitsmappe
    * Anheften der Arbeitsmappe an das Dashboard

### <a name="workbooks-versus-workbook-templates"></a>Arbeitsmappen im Vergleich zu Arbeitsmappenvorlagen

Es werden eine _Arbeitsmappe_ in Grün und eine Reihe von _Arbeitsmappenvorlagen_ in Violett angezeigt. Vorlagen dienen als zusammengestellte Berichte, die von mehreren Benutzern und Teams flexibel wiederverwendet werden können. Beim Öffnen einer Vorlage wird eine temporäre Arbeitsmappe mit dem Inhalt der Vorlage erstellt.

Sie können die Parameter der vorlagenbasierten Arbeitsmappe anpassen und Analysen durchführen, ohne befürchten zu müssen, dass die zukünftige Berichtsoberfläche für Kollegen verändert wird. Wenn Sie eine Vorlage öffnen, einige Anpassungen vornehmen und dann das Speichersymbol auswählen, speichern Sie die Vorlage als Arbeitsmappe, die dann in Grün angezeigt wird, während die ursprüngliche Vorlage unverändert bleibt.

Vorlagen unterscheiden sich auch von gespeicherten Arbeitsmappen. Beim Speichern einer Arbeitsmappe wird eine zugeordnete Azure Resource Manager-Ressource erstellt, während der beim Öffnen einer Vorlage erstellten temporären Arbeitsmappe keine eindeutige Ressource zugeordnet ist. Weitere Informationen zur Verwaltung der Zugriffssteuerung in Arbeitsmappen finden Sie im [Artikel zur Zugriffssteuerung für Arbeitsmappen](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Erkunden einer Arbeitsmappenvorlage

Wählen Sie **Analyse der Anwendungsfehler** aus, um eine der Standardvorlagen für Anwendungsarbeitsmappen anzuzeigen.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Screenshot der Vorlage zur Anwendungsfehleranalyse." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Wie bereits erwähnt, wird beim Öffnen der Vorlage eine temporäre Arbeitsmappe erstellt, mit der Sie interagieren können. Standardmäßig wird die Arbeitsmappe im Lesemodus geöffnet, in dem nur die Informationen für die gewünschte Analyse angezeigt werden, die vom ursprünglichen Vorlagenautor erstellt wurden.

Im Fall dieser speziellen Arbeitsmappe ist die Oberfläche interaktiv. Sie können das Abonnement, die Ziel-Apps und den Zeitbereich der Daten anpassen, die Sie anzeigen möchten. Nachdem Sie diese Auswahl getroffen haben, ist auch das Raster der HTTP-Anforderungen interaktiv. Dadurch werden bei Auswahl einer einzelnen Zeile die in den beiden Diagrammen am unteren Rand des Berichts dargestellten Daten geändert.

### <a name="editing-mode"></a>Bearbeitungsmodus

Um zu verstehen, wie diese Arbeitsmappenvorlage zusammengesetzt ist, müssen Sie in den Bearbeitungsmodus wechseln. Dazu wählen Sie **Bearbeiten** aus.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Screenshot der Schaltfläche „Bearbeiten“ in Arbeitsmappen" border="false" :::

Nachdem Sie in den Bearbeitungsmodus gewechselt haben, wird eine Reihe von Feldern des Typs **Bearbeiten** rechts neben den einzelnen Aspekten der Arbeitsmappe angezeigt.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Screenshot der Schaltfläche „Bearbeiten“." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Wenn Sie die Schaltfläche „Bearbeiten“ direkt unterhalb des Rasters mit Anforderungsdaten auswählen, sehen Sie, dass dieser Teil der Arbeitsmappe aus einer Kusto-Abfrage für Daten von einer Application Insights-Ressource besteht.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Screenshot der zugrunde liegenden Kusto-Abfrage." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Wenn Sie die anderen **Bearbeiten**-Schaltflächen auf der rechten Seite auswählen, wird eine Reihe von Kernkomponenten angezeigt, aus denen sich Arbeitsmappen zusammensetzen, wie markdownbasierte [Textfelder](../visualize/workbooks-text-visualizations.md), Benutzeroberflächenelemente für die [Parameterauswahl](../visualize/workbooks-parameters.md) und andere [Diagramm-/Visualisierungstypen](#visualizations).

Das Erkunden der vordefinierten Vorlagen im Bearbeitungsmodus, das anschließende Anpassen an Ihre Anforderungen und das Speichern Ihrer eigenen benutzerdefinierten Arbeitsmappe ist eine hervorragende Möglichkeit, um zu erfahren, welche Möglichkeiten Azure Monitor-Arbeitsmappen bieten.

## <a name="dashboard-time-ranges"></a>Zeitbereiche auf dem Dashboard

Bei angehefteten Abfrageteilen der Arbeitsmappe wird der Zeitbereich des Dashboards berücksichtigt, sofern das angeheftete Element für die Verwendung eines *Zeitbereichsparameters* konfiguriert ist. Der Zeitbereichswert des Dashboards wird als Wert für den Zeitbereichsparameter verwendet, und jede Änderung des Zeitbereichs des Dashboards bewirkt eine Aktualisierung des angehefteten Elements. Wenn ein angehefteter Teil den Zeitbereich des Dashboards verwendet, wird bei jeder Änderung des Zeitbereichs der Untertitel des angehefteten Teils aktualisiert, um den Zeitbereich des Dashboards anzuzeigen.

Außerdem werden angeheftete Arbeitsmappenteile, die einen Zeitbereichsparameter verwenden, automatisch mit einer Rate aktualisiert, die durch den Zeitbereich des Dashboards bestimmt wird. Der letzte Zeitpunkt, zu dem die Abfrage ausgeführt wurde, wird im Untertitel des angehefteten Teils angezeigt.

Wenn ein angehefteter Schritt einen explizit festgelegten Zeitbereich aufweist (keinen Zeitbereichsparameter verwendet), wird unabhängig von den Einstellungen des Dashboards immer dieser Zeitbereich für das Dashboard verwendet. Der Untertitel des angehefteten Teils zeigt dann nicht den Zeitbereich des Dashboards an, und die Abfrage wird auf dem Dashboard nicht automatisch aktualisiert. Der Untertitel zeigt den Zeitpunkt an, zu dem die Abfrage zuletzt ausgeführt wurde.

> [!NOTE]
> Abfragen, bei denen die Datenquelle *Zusammenführen* verwendet wird, werden beim Anheften an Dashboards derzeit nicht unterstützt.

## <a name="sharing-workbook-templates"></a>Freigeben von Arbeitsmappenvorlagen

Sobald Sie mit dem Erstellen Ihrer eigenen Arbeitsmappenvorlagen beginnen, können Sie diese für die breite Öffentlichkeit freigeben. Wenn Sie weitere Informationen wünschen und andere Vorlagen erkunden möchten, die nicht Teil der Azure Monitor-Standardkatalogansicht sind, besuchen Sie unser [GitHub-Repository](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Zum Durchsuchen vorhandener Arbeitsmappen besuchen Sie die [Arbeitsmappenbibliothek](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) auf GitHub.


## <a name="next-step"></a>Nächster Schritt

* [Erfahren Sie mehr](#visualizations) über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
* [Steuern](../visualize/workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.